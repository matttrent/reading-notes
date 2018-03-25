# refx fastai course 2

## week 8 object detection

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

