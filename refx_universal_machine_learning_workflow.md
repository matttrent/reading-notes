# The universal workflow of machine learning

What we present here is a universal blueprint you can use to attack and solve any machine learning problem, tying together the different concepts you learned about in this chapter: problem definition, evaluation, feature engineering, and fighting overfitting.

In summary, this is the universal workflow of machine learning:

1. Define the problem at hand and the data you will be training on; collect this data or annotate it with labels if need be.
2. Choose how you will measure success on your problem. Which metrics will you be monitoring on your validation data?
3. Determine your evaluation protocol: hold-out validation? K-fold validation? Which portion of the data should you use for validation?
4. Develop a first model that does better than a basic baseline: a model that has "statistical power".
5. Develop a model that overfits.
6. Regularize your model and tune its hyperparameters, based on performance on the validation data.
   A lot of machine learning research tends to focus only on the last step—but keep in mind the big picture.

## Define the problem and assemble a dataset

First, you must define the problem at hand:

- What will your input data will be? What will you be trying to predict? You can only learn to predict something if you have available training data, e.g. you can only learn to classify the sentiment of movie reviews if you have both movie reviews and sentiment annotations available. As such, data availability is usually the limiting factor at this stage (unless you have the means to pay people to collect data for you).
- What type of problem are you facing—is it binary classification? Multi-class classification? Scalar regression? Vector regression? Multi-class, multi-label classification? Something else, like clustering, generation or reinforcement learning? Identifying the problem type will guide your choice of model architecture, loss function, and so on.

You cannot move to the next stage until you know what your inputs and outputs are, and what data you will be using. Be aware of the hypotheses that you are making at this stage:

- You are hypothesizing that your outputs can be predicted given your inputs.
- You are hypothesizing that your available data is sufficiently informative to learn the relationship between inputs and outputs.

Until you have a working model, then these are merely hypotheses, waiting to be validated or invalidated. Not all problems can be solved; just because you have assembled examples of inputs X and targets Y doesn’t mean that X contains enough information to predict Y. For instance, if you are trying to predict the movements of a stock on the stock market given its recent price history, your are unlikely to succeed, since price history simply doesn’t contain much predictive information.

One class of unsolvable problems of which you should be specifically aware is non-stationary problems. Suppose that you are trying to build a recommendation engine for clothing, and that you are training it on one month of data, August, and that you want to start generating recommendations in the winter. One big issue is that the kind of clothes that people buy changes from season to season, i.e. clothes buying is a non-stationary phenomenon over the scale of a few months. What you are trying to model changes over time. In this case the right move would be to constantly retrain your model on data from the recent past, or gather data at a timescale where the problem is stationary. For a cyclical problem like clothes buying, a few years worth of data would suffice to capture seasonal variation—but then you should remember to make the time of the year an input of your model!

Keep it in mind: machine learning can only be used to memorize patterns which are present in your training data. You can only recognize what you have seen before. Using machine learning trained on past data to predict the future is making the assumption that the future will behave like the past. That is often not the case.

## Pick a measure of success

To control something, you need to be able to observe it. To achieve success, you must define what you mean by success—accuracy? Precision-Recall? Customer retention rate? Your metric for success will guide the choice of your loss function, i.e. the choice of what your model will optimize. It should directly align with your higher-level goals, such as the success of your business.

For balanced classification problems, where every class is equally likely, accuracy and ROC-AUC are common metrics. For class-imbalanced problems, one may use Precision-Recall. For ranking problems or multi-label classification, one may use Mean Average Precision. And it isn’t uncommon to have to define your own custom metric by which you will measure success. To get a sense of the diversity of machine learning success metrics and how they relate to different problem domains, it is helpful to browse data science competitions on Kaggle.com, as they showcase a wide range of different problems and evaluation metrics.

## Decide on an evaluation protocol

Once you know what you are aiming for, you must establish how you will measure your current progress. We have previously reviewed three common evaluation protocols:

- Maintaining a hold-out validation set; this is the way to go when you have plenty of data.
- Doing K-fold cross-validation; this is the way to go when you have too few samples for hold-out validation to be reliable.
- Doing iterated K-fold validation; this is for performing highly accurate model evaluation when little is available.

Just pick one of these; in most cases the first one will work well enough.

## Prepare your data

Once you know what you are training on, what you are optimizing for, and how to evaluate your approach, you are almost ready to start training models. But first, you should format your data in a way that can be fed into a machine learning model—here we will assume a deep neural network.

- As we saw previously, your data should be formatted as tensors.
- The values taken by these tensors should almost typically be scaled to small values, e.g. in the `[-1, 1]` range or `[0, 1]` range.
- If different features take values in different ranges (heterogenous data), then the data should be normalized.
- You may want to do some feature engineering, especially for small data problems.

Once your tensors of input data and target data are ready, you can start training models.

## Develop a model that does better than a baseline

Your goal at this stage is to achieve "statistical power", i.e. develop a small model that is capable of beating a dumb baseline. In our MNIST digits classification example, anything that gets an accuracy higher than 0.1 can be said to have statistical power; in our IMDB example it would be anything with an accuracy higher than 0.5.

Note that it is not always possible to achieve statistical power. If you cannot beat a random baseline after trying multiple reasonable architectures, it may be that the answer to the question you are asking isn’t actually present in the input data. Remember that you are making two hypotheses:

- You are hypothesizing that your outputs can be predicted given your inputs.
- You are hypothesizing that your available data is sufficiently informative to learn the relationship between inputs and outputs.

It may well be that these hypotheses are false, in which case you would have to go back to the drawing board.

Assuming that things go well—there are three keys choices you need to make in order to build your first working model:

- Choice of the last-layer activation. This establishes useful constraints on the network’s output: for instance in our IMDB classification example we used sigmoid in the last layer, in the regression example we didn’t use any last-layer activation, etc.
- Choice of loss function. It should match the type of problem you are trying to solve: for instance in our IMDB classification example we used `binary_crossentropy`, in the regression example we used `mse`, etc.
- Choice of optimization configuration: what optimizer will you use? What will its learning rate be? In most cases it is safe to go with rmsprop and its default learning rate.

Regarding the choice of a loss function: note that it isn’t always possible to directly optimize for the metric that measures success on a problem. Sometimes there is no easy way to turn a metric into a loss function; loss functions, after all, need to be computable given only a mini-batch of data (ideally, a loss function should be computable for as few as a single data point) and need to be differentiable (otherwise you cannot use backpropagation to train your network). For instance, the widely used classification metric ROC-AUC (Receiver Operating Characteristic Area Under the Curve) cannot be directly optimized. Hence in classification tasks it is common to optimize for a proxy metric of ROC-AUC, such as crossentropy. In general, one can hope that the lower the crossentropy gets, the higher the ROC-AUC will be.

Here is a table to help you pick a last-layer activation and a loss function for a few common problem types:

| Problem type                             | Last-layer activation | Loss function                  |
| ---------------------------------------- | --------------------- | ------------------------------ |
| Binary classification                    | sigmoid               | `binary_crossentropy`          |
| Multi-class, single-label classification | softmax               | `categorical_crossentropy`     |
| Multi-class, multi-label classification  | sigmoid               | `binary_crossentropy`          |
| Regression to arbitrary values           | None                  | `mse`                          |
| Regression to values between 0 and 1     | sigmoid               | `mse` or `binary_crossentropy` |

## Scale up: develop a model that overfits

Once you have obtained a model that has statistical power, the question becomes: is your model powerful enough? Does it have enough layers and parameters to properly model the problem at hand? For instance, a network with a single hidden layer with 2 units would have statistical power on MNIST, but would not be sufficient to solve the problem well. Remember that the universal tension in machine learning is between optimization and generalization; the ideal model is one that stands right at the border between under-fitting and over-fitting; between under-capacity and over-capacity. To figure out where this border lies, first you must cross it.

To figure out how big a model you will need, you must develop a model that overfits. This is fairly easy:

- Add layers.
- Make your layers bigger. 
- Train for more epochs.

Always monitor the training loss and validation loss, as well as the training and validation values for any metrics you care about. When you see that the performance of the model on the validation data starts degrading, you have achieved overfitting.

The next stage is to start regularizing and tuning your model, in order to get as close as possible to the ideal model, that is neither underfitting nor overfitting.

## Regularize your model and tune your hyperparameters

This is the part that will take you the most time: you will repeatedly modify your model, train it, evaluate on your validation data (not your test data at this point), modify it again... until your model is as good as it can get.
These are some of things you should be trying:

- Add dropout.
- Try different architectures, add or remove layers.
- Add L1 / L2 regularization.
- Try different hyperparameters (such as the number of units per layer, the learning rate of the optimizer) to find the optimal configuration.
- Optionally iterate on feature engineering: add new features, remove features that do not seem to be informative.

Be mindful of the following: every time you are using feedback from your validation process in order to tune your model, you are leaking information about your validation process into your model. Repeated just a few times, this is innocuous, but done systematically over many iterations will eventually cause your model to overfit to the validation process (even though no model is directly trained on any of the validation data). This makes your evaluation process less reliable, so keep it in mind.

Once you have developed a seemingly good enough model configuration, you can train your final production model on all data available (training and validation) and evaluate it one last time on the test set. If it turns out that the performance on the test set is significantly worse than the performance measured on the validation data, this could mean either that your validation procedure wasn’t that reliable after all, or alternatively it could mean that have started overfitting to the validation data while tuning the parameters of the model. In this case you may want to switch to a more reliable evaluation protocol (e.g. iterated K-fold validation).