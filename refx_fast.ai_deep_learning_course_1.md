# fast.ai deep learning course 1

## week 1 image recognition

### launching AWS instances

- I've got a bunch of work done on this already
- setting up instances, easily managing their state, connecting to them and getting results back/forth
- https://github.com/matttrent/aws-deeplearn
- https://github.com/matttrent/tilde-bin
- https://github.com/matttrent/dotfiles

### fine-tuning

basic gist

- adapt an already-trained model weights/architecture for a different task
- remove last layer of the NN, replace with a layer that matches the output of the new task
- freeze all other layers of the NN, and retrain the weights of new last layer
- assumes the data of the new task is similar to that of the original architecture

can further finetune more layers of the network, than just the last

- as long as new task data matches old task data can keep existing CNN layers
- if finetuning more than last layer, best to first finetune the last layer only before finetuning more
- NOTE: why is this?

why this is powerful

- can leverage much larger datasets, provided they share some similarities with the data we're using for our task
- task does not need to match, as long as training/test inputs share similarities
- can use the generalization from a network only usable with a very large training set, and apply to problems with small amounts of data

### supporting material

- http://wiki.fast.ai/index.php/Lesson_1_Timeline


- http://wiki.fast.ai/index.php/Lesson_1
  - https://github.com/fastai/courses/blob/master/deeplearning1/nbs/lesson1.ipynb
- http://wiki.fast.ai/index.php/Lesson_1_Notes

## week 2 CNNs

### dataset orginization

1. organize dataset into `train` and `test` folders
2. create a `valid` folder and move 2000 random samples from the train folder, into it
3. make a separate `dataset-sample` 
   1. create a `train` folder and copy 1500 random samples to it from main `train` folder
   2. create a `valid` folder and copy 50 random samples to it from main `valid` folder

### my preferred project organization

code

- one module `fastai`
  - contains many shared helper functions
  - submodule per dataset/project to contain all common code for that project
- `notebooks`
  - hacking on small projects that aren't worth configuring the full script setup for
  - but ideally as infrastructure advances it becomes easier to spinup new things
- `scripts`
  - multiple training scripts + 1 generic test script, per project
  - one train script per model possible architecture, can be many
  - all hyperparameters are arguments to script
  - each script run logs all its info to a standardized directory format
  - test script reads output of previous training script, validates against test set, possible submits to kaggle

```
- fastai
    - fastai
        - kaggle.py
        - utils.py
        - dataset-project
            - etc.py...
- scripts
    - dataset-train-xxx.py
    - dataset-test.py
```

`~/data`

- one `dataset` sub-directory per dataset
- have a separate `dataset-sample` directory that mirrors the structure of the full dataset, but contains 1500 training images and 1000 validation

`~/runs`

- one `dataset` sub-directory per dataset
- one run folder per test or train script run
- formatted like `YYYYMMDDHHMM-scriptname-p1-p2-p3-p4` where p1-p4 are config values for that script
- contains yaml file of all configuration values for that script, so can be rerun
- contains saved models for that run, training log, and validation results

### stochastic gradient decent

- cs231n [Optimization: Stochastic Gradient Descent](https://cs231n.github.io/optimization-1/)
- cs231n [Backpropagation, Intuitions](https://cs231n.github.io/optimization-2/)
- want the highest learning rate you can get away with
- want to reduce learning rate after a few epochs
- in practice, deep learning does not have local minima.  with 100-million parameters, it's effectively impossible to find a point that no dimension can't be improved up

### neural network architecture

- cs231n [Neural Networks Part 1: Setting up the Architecture](https://cs231n.github.io/neural-networks-1/)

dense layers

- every neuro has a weight for every entry in the previous layer, plus a shared bias term
- so, each neuron computes a dot-product between the weights and the previous layer's neurons + bias
- effectively, matrix-multiplication, when considering all neurons in a layer

activation layers

- a dense NN is represented as a series of matrix multiplications
- however, that doesn't contain much predictive power
- series of matrix multiplications can still be represented by a single linear transform
- so include various non-linear activation functions to transform the output of each dense layer to prevent it from collapsing to a single linear transform
- for most layers, ReLU is standard
- for the last layer, the activation layer is dependent on the score function
  - in the case of classification, softmax

convolution layers & max pooling

- a max pooling layer reduces the dimensionality of images (resolution) by reducing the number of pixels in the image
- it does so by replacing an entire NxN area of the input image with the maximum pixel value in that area.
- doing 7x7 max pooling, on a 28x28 image, results in a 4x4 image

###  linear models in keras

- training linear model via SGD

```python
# init data
x = random((30,2))
y = np.dot(x, [2., 3.]) + 1.

# create and compile single layer linear model
lm = Sequential([ 
  Dense(1, input_shape=(2,))
])
lm.compile(optimizer=SGD(lr=0.1), loss='mse')

# fit data
lm.fit(x, y, nb_epoch=5, batch_size=1)

# some low number since mse between y and y' will be small
lm.evaluate(x, y, verbose=0)
```

### storing inputs and outputs of models

```python
import bcolz
def save_array(fname, arr):
  	c = bcolz.carray(arr, rootdir=fname, mode='w')
    c.flush()

def load_array(fname): 
  	return bcolz.open(fname)[:]
  
# get the data and save to arrays
val_data = get_data(path+'valid')
trn_data = get_data(path+'train')

save_array(model_path+'train_data.bc', trn_data)
save_array(model_path+'valid_data.bc', val_data)

# load train/valid data from bcolz if you want
trn_data = load_array(model_path+'train_data.bc')
val_data = load_array(model_path+'valid_data.bc')

from vgg16 import Vgg16
vgg = Vgg16()
model = vgg.model

trn_features = model.predict(trn_data, batch_size=batch_size)
val_features = model.predict(val_data, batch_size=batch_size)

save_array(model_path+'train_lastlayer_features.bc', trn_features)
save_array(model_path+'valid_lastlayer_features.bc', val_features)
```

### training a linear model on the feature outputs from imagenet model

- not even fine-tuning
- training a linear model on the imagenet class predictions from VGG

```python
# keras returns class ids, so we one-hot encode them
def onehot(x): 
  	return np.array(OneHotEncoder().fit_transform(x.reshape(-1,1)).todense())

# Use batch size of 1 since we're just doing preprocessing on the CPU
val_batches = get_batches(path+'valid', shuffle=False, batch_size=1)
batches = get_batches(path+'train', shuffle=False, batch_size=1)

# load the features that will be our training data
trn_features = load_array(model_path+'train_lastlayer_features.bc')
val_features = load_array(model_path+'valid_lastlayer_features.bc')

# get one-hot encoded labels for keras
val_classes = val_batches.classes
trn_classes = batches.classes
val_labels = onehot(val_classes)
trn_labels = onehot(trn_classes)

# define and compile model
lm = Sequential([
  	Dense(2, activation='softmax', input_shape=(1000,))
])
lm.compile(
  optimizer=RMSprop(lr=0.1), 
  loss='categorical_crossentropy', 
  metrics=['accuracy']
)

# train
lm.fit(trn_features, trn_labels, nb_epoch=3, batch_size=batch_size, 
       validation_data=(val_features, val_labels))
```

### visualization of results

It's often a good idea to analyze how well your model is doing through visualization. Fortunately, since we're dealing with images this is exceptionally easy. We'd like to look at the following examples:

1. A few correct labels at random
2. A few incorrect labels at random
3. The most correct labels of each class
4. The most incorrect labels of each class
5. The most uncertain labels (those with probabilities near 0.5)

### kaggle submission

- be sure to pay attention to the specifics of the how kaggle evaluates your submission
- different contests can have completely different evaluation metrics, make sure you understand the nuance of them.
- e.g. log loss rewards predictions that are confident and correct (p=.9999,label=1), but it punishes predictions that are confident and wrong far more (p=.0001,label=1). 
- In Jeremy's case, he got 97% accuracy on his validation set, so conservatively chose a 90% confidence and he rounded down his edge predictions, and swap all ones with .95 and all zeros with .05.

### supporting material

- http://wiki.fast.ai/index.php/Lesson_2_Timeline
- http://wiki.fast.ai/index.php/Lesson_2
  - https://github.com/fastai/courses/blob/master/deeplearning1/nbs/lesson2.ipynb
  - https://github.com/fastai/courses/blob/master/deeplearning1/nbs/dogs_cats_redux.ipynb
- http://wiki.fast.ai/index.php/Lesson_2_Notes



## week 3 over-fitting

### neural network architecture

- cs231n [Convolutional Neural Networks: Architectures, Convolution / Pooling Layers](https://cs231n.github.io/convolutional-networks/)
- cs231n [Understanding and Visualizing Convolutional Neural Networks](https://cs231n.github.io/understanding-cnn/)
- https://colah.github.io/posts/2014-07-Understanding-Convolutions/

convolutional layers

- **TODO** complete this explanation

![conv_layer_illustration](refx_fast.ai_deep_learning_course_1/conv_layer_illustration.png)

![conv_layer_rules](refx_fast.ai_deep_learning_course_1/conv_layer_rules.png)

softmax activation

- defined as $e^{x_i} / \sum e^{x_i}$
- final layer output is probabilities, should sum to 1
- final layer output is one-hot encoded, so 1 should be large and rest small
- softmax accomplishes both of that
- in general, want to construct activation layers that make it as convenient as possible for the network accomplish the thing we want

### underfitting and overfitting

underfitting

- using a model that's not complex or powerful enough for your task (not enough parameters)
- if our training error is much lower than our validation error then we're underfitting

overfitting

- a model with too many parameters, trained for too long, that has learned what your specific training data is instead of the  
- training set has a much higher accuracy than your validation set
- perhaps the single most important concept in machine learning

application to class examples

- example models are underfitting, because of dropout (in this case set to 0.5)
  - in this case because our cats & dogs task is not as complex as the imagenet task
- the dropout layers throw away half the inputs, so it's very difficult to learn the specific examples to overfit
- can train big complex models for long periods of time without overfitting

This is the order that we recommend using for reducing overfitting (more details about each in a moment):

1. Add more data
2. Use data augmentation
3. Use architectures that generalize well
4. Add regularization (dropout, L1-, L2- regularization)
5. Reduce architecture complexity.

### data augmentation

- never a reason not to do data augmentation
- only a question of what and how much
- use `keras` generator

### batch normalization

- something else you should always do
- in machine learning you always want to normalize your inputs
  - data to have mean=0 & std=1, so weights have normalized gradients
- Adding batchnorm to a model can result in **10x or more improvements in training speed**
- Because normalization greatly reduces the ability of a small number of outlying inputs to over-influence the training, it also tends to **reduce overfitting**.
- In addition to normalizing weights, batchnorm takes two additional steps:
  1. Add two more trainable parameters to each layer - one to multiply all activations to set an arbitrary standard deviation, and one to add to all activations to set an arbitary mean
  2. Incorporate both the normalization, and the learnt multiply/add parameters, into the gradient calculations during backprop.
- This ensures that the weights don't tend to push very high or very low (since the normalization is included in the gradient calculations, so the updates are aware of the normalization). 
- But it also ensures that if a layer does need to change the overall mean or standard deviation in order to match the output scale, it can do so.

### model manipulation

**TODO**

1. take VGG model
2. separate into convolutional and fully connected (+ dropout) layers
3. create new convolution-only model, precompute output and save
4. create new full-connected model with dropout=0
5. transfer weights from old dropout model
6. finetune new fc model

### end to end process notes

- [x] linear model
- [x] single hidden dense later
- [x] Basic VGG-style CNN
- [x] data augmentation
- [x] data augmentation + batchnorm
- [ ] data augmentation + batchnorm + dropout

notes

- in all cases, train for 1 epoch at default LR, then 0.1 LR, then 0.01 LR, etc...
- all experiments except last can be performed on sample data
- dropout regularization must be tuned to the full dataset
- see https://github.com/fastai/courses/blob/master/deeplearning1/nbs/mnist.ipynb

### supporting material

- http://wiki.fast.ai/index.php/Lesson_3_Video_Timeline
- http://wiki.fast.ai/index.php/Lesson_3
  - https://github.com/fastai/courses/blob/master/deeplearning1/nbs/lesson3.ipynb
  - https://colah.github.io/posts/2015-08-Backprop/
  - http://wiki.fast.ai/index.php/Gradient_Descent#Stochastic_Gradient_Descent
  - cs231n [Neural Networks Part 2: Setting up the Data and the Loss](https://cs231n.github.io/neural-networks-2/)
    - NOTE: why is it called log-loss some places and binary/categorical entropy others?
  - cs231n [Neural Networks Part 3: Learning and Evaluation](https://cs231n.github.io/neural-networks-3/)
- http://wiki.fast.ai/index.php/Lesson_3_Notes



## week 4 collaborative filtering and embeddings

### supporting material

- http://wiki.fast.ai/index.php/Lesson_4_Timeline
- http://wiki.fast.ai/index.php/Lesson_4
  - https://github.com/fastai/courses/blob/master/deeplearning1/nbs/statefarm-sample.ipynb
  - https://github.com/fastai/courses/blob/master/deeplearning1/nbs/statefarm.ipynb
  - http://ruder.io/optimizing-gradient-descent/
- http://wiki.fast.ai/index.php/Lesson_4_Notes