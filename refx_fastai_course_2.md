# refx fastai course 2

## week 8 object detection

### notebooks

- pascal.ipynb

### course 1 takeaways

- differentiable computing = setup differentiable problem, define loss function
- transfer learning / fine-tuning makes nearly almost everything easier, faster, accurate
- architecture design for specifics of dataset and objective
  - cnns for fixed-size ordered data, rnns for sequences, softmax for categories
- reducing overfitting
  - more data, data augmentation, generalizable architectures, regularization, reduce arch complexity
  - but start with something that's overfitting, because otherwise you know not enough params
- embeddings allow the use of categorical data

### building a box

- cpu matters for computer vision data augmentation
- make sure you have x8

### opportunities

- few DL practitioners know what you know
- experiment lots, especially in your area of expertise
- much of what you find will not be writte about before
- don't wait to be perfect before you start communicating

### what we'll study in part 2

- CNNS beyond classification
  - localization, enhancement, GANs
- NLP beyond classification
  - translation, seq2seq, attention, large vocabularies
- large data sets
  - large images, lots of data points, large outputs

### object detection

- steps
    1. classify the largest object in each image
    2. give the bounding box of the largest object in the image
    3. do both at the same time for the largest thing

### TODO

- work through the math of Adam
- understand the difference between a dataloader and dataset
- understand what `md.val_ds.denorm(to_np(x))[0]` is doing
- review code for bounding boxes, and 4-value error functions
- review code for full output

### tricks

```python
# define strings as upper case variables for tab-completion
IMAGES,ANNOTATIONS,CATEGORIES = ['images', 'annotations', 'categories']
trn_j[IMAGES][:5]

# default dict to default to empty list
trn_anno = collections.defaultdict(lambda:[])

# process your data and export a CSV, then use the standard from_csv loader
md = ImageClassifierData.from_csv(PATH, JPEGS, CSV, tfms=tfms)

# use pdb, s/n/c, u/d
pdb.set_trace()

# custom output activation of 4 values
head_reg4 = nn.Sequential(Flatten(), nn.Linear(25088,4))
learn = ConvLearner.pretrained(f_model, md, custom_head=head_reg4)

# using L1 loss instead of MSE
learn.crit = nn.L1Loss()
```

## week 9 object detection 2

### what you need to know from part 1

- how to view model inputs from a DataLoader
- how to view model outputs

```
x,y = nex(iter(md.val_dl))

ima = md.val_ds
...
```

## notebooks

- pascal.ipynb
- pascal-multi.ipynb

### library changes

```python
# how to load a regressor with continuous values
ImageClassifierData.from_csv(continuous=True)

# transforms take a TfmType enum to specify how to transform the y value
tfms = tfms_from_model(f_model, sz, crop_type=CropType.NO, tfm_y=TfmType.COORD)
```

### custom head

```python
head_reg4 = nn.Sequential(
    Flatten(),
    nn.ReLU(),
    nn.Dropout(0.5),
    nn.Linear(25088,256),
    nn.ReLU(),
    nn.BatchNorm1d(256),
    nn.Dropout(0.5),
    # output is 4 continuous variables of the bounding box, and 1-hot encoded categories
    nn.Linear(256,4+len(cats)),
)
models = ConvnetBuilder(f_model, 0, 0, 0, custom_head=head_reg4)
```

- normally ReLU then BatchNorm according to Jeremy
- occasionally does it differently depending on corresponding papers

### detection loss

```python
def detn_loss(input, target):
    bb_t,c_t = target
    bb_i,c_i = input[:, :4], input[:, 4:]
    bb_i = F.sigmoid(bb_i)*224
    # I looked at these quantities separately first then picked a multiplier
    #   to make them approximately equal
    return F.l1_loss(bb_i, bb_t) + F.cross_entropy(c_i, c_t)*20

# set on learner
learn.crit = detn_loss
```

- custom loss that combines L1 loss for regression and cross-entropy for classification
- can pass custom metrics for the learner to output

### multiclass classification

- outputs all the classes present in the image
- but doesn't output number of each class
- previous example from planet lesson covers this

### 2 approaches to multiple object outputs

- have a max of $N$ objects we want to detect
- YOLO: linear layer outputs a shape $N \times (4+C)$
- SSD: conv2d (stride=2) filter of that outputs a tensor of shape $4\times4\times(4+C)$

### anchor boxes and matching

- or default boxes or 
- subdivide the network to having into multiple detectors each with a receptive field
- matching problem to see which of the 16 boxes has the highest overlap with each object 
- jacard index = $IOU = area_{intersection} / area_{union}$
- compute jacard index for each possible pair of default box and ground truth object
- find max of overlap
- `map_to_ground_truth()` maps every anchor box to ground truth object according to SSD paper
- look at all the details, but eventually arrive at list of boxes that maximally overlap with ground truth objects
- error metric is L1 norm between bounding box differences & cross entropy of classes
- ignores the background boxes—they don't count against the error
- `actn_to_bb()` maps the anchor boxes to the range they're allowed to be modified



### classification

- classifying a background category with a softmax is really difficult to ask
- binary cross entropy
- `BCE_loss` has an embedding of $N+1$ classes to include background
- but remove the background embedding
- not actually having a multiclass output
- opposed to the NN having to learn all the different ways of what background could be
- NN scans across all classes and checks whether they're present
- absence of any classes implies background, opposed to being its own class

### moar boxes

- a large variety of anchor boxes, at different scales, offsets and ratios
- we reject any anchor box + ground truth box combo that doesn't have a jacard index greater than 0.5
- so we need to generate many different bounding boxes to find a candidate anchor box that will match with the ground truth box

### focal loss



### TODO

- review custom head, with a bounding box regression
- review custom detection head with custom error metric
- review SSD paper, especially `map_to_ground_truth()`
- review `SSD_1_loss()`
- watch [Lecture 8 CS231N W2016](https://youtu.be/GxZrEKZfW2o) and [Lecture 11 CS231N S20177](https://youtu.be/nDPWywWRIRo?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)
- scalable object detection using deep neural networks (multibox)
- faster r-cnn: towards real-time object detection with region proposal networks
- you only look once: unified, real-time object detection
- ssd: single shot multibox detector
- focal loss for dense object detection (retina net)
- review cross-entropy loss


## week 10 language models

- [List of mathematical symbols](https://en.wikipedia.org/wiki/List_of_mathematical_symbols)
- [List of mathematical symbols by subject](https://en.wikipedia.org/wiki/List_of_mathematical_symbols_by_subject)

### nlp

- imbd.ipynb
- `pd.read_csv(chunksize=...)`
- variable size bptt around a mean
- AWD-LSTM = regularizing and optimizing LSTM language models
- big idea: if you can make your model a backbone plus a head, you can do transfer learning
- using accuracy over other losses
  - cross-entropy loss penalizes models based on their confidence

### major approaches

- gradual unfreezing, progressively unfreezing more and more layers over the course of training
- fine-tuning with cosine annealing, progressively increasing and decreasing the learning rate over time
- concat pooling, concatenating base activation, mean pool, and max pool
- discriminative fine-tuning, adjusting learning rates across the depth of the network 

### slanted learning rates

- leslie smith
- disciplined approach to neural network hyper-parameters
- `use_clr(X, Y)` 
  - where `X` is the ratio of the peak to the valley
  - where `Y` is the ratio of the uptick to the entire cycle length

### google's fire library

- turn a single function with shit-loads of parameters
- make command line interface to runs tons of variations

## week11 neural machine translation

- learning neutral translation, using a form of seq2seq translation
- starting with language model of chucking it through an RNN
- REMEMBER: concat pooling

$$
[H_t, \mathtt{maxpool}(H), \mathtt{meanpool}(H)]
$$

levels of RNN

- sequence to single value — sentiment classification
  - refactor a list of linear layers into a for loop
- sequence to similar length sequence — language model
  - including the output in each loop, appending your hidden state at each iteration of the loop
- sequence to different sequence — translation
  - one RNN feeds into another RNN

### using word vectors

- because we didn't have time to do language models NN that Jeremy extolled last week
- language models have more expressive power than single layer networks ala word vectors
- but haven't done the work for seq2seq translation, but probably some open research

### things of note

- pytorch variables have a `.data` attribute to access the wrapped tensor

### RNN seq2seq

- encoding
  - init hidden state
  - run through embedding, add dropout
  - running through GNU, and a final linear layer
- decoding
  - run the same effective thing, with a manual for loop
  - .squeeze to explain to pytorch we're operating a thing at time
  - run last token through an embedding — starting with beginning of stream token at first
  - run the embedding value and the hidden state output from the encoder into GRU
  - add dropout and run through linear layer
- using fastai
  - SingleModel is the simplest way to wrap a pytorch model into the expected learning rate groups
  - wrap in RNN_Learner

### go bidirectional

- very simple, run over the input both forwards and backwards
- add `bidirectional=True` to the encoder, harder to do in the decoder
- need to add `2x` to various places like number hidden, number layers to account for double the output

### teacher forcing

- early learn very difficult because it makes a poor initial guess, then feeds that guess into the next iteration, making poor guests not impact subsequent iterations
- idea: occasionally insert the correct word for the previous value, opposed to the network's last iteration
- gradually transition from forcing the correct word to taking the previous iteration over the course of training
- how do you implement that change over the course of training?
- inherent from `Stepper`, implement `step()` to have a custom version of the step

### attentional model

- expecting the entire sentence to be compressed into a single vector is asking a lot
- emit all the hidden states from the encoder, opposed to just the final
- need 1 or more of those hidden states, depending on what output you're translating
- add another neural net that spits out a weight for each input at each hidden output step
- nn in the middle — 1 hidden layer, with nonlinearity and softmax
- TODO: check wiki thread for attention model papers
- easiest way is to learn something is just stick the NN in the rest of your model
- decoder's hidden state is what tells us what to look for
- attentional net learns where 

### devise 

- train a model to output word vectors opposed to 1-hot encoded vectors
- map imagenet image to synset then to wordnet id
- ImageClassifierData.from_names_and_array — to load a list of images and a vector 
- convert to a regression problem that predicts the word vector for each image
- cosine similarity for how distant points are in high-dimensional spaces
- nmslib for approximate nearest neighbor search
- HOLY CRAP