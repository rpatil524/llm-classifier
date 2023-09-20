
# Laminify - Instantly classify data with [Lamini](lamini.ai) & Llama 2

Train a new classifier with just a prompt.

```bash
./train.sh --class "cat: CAT_DESCRIPTION" --class "dog: DOG_DESCRIPTION"
```

```bash
./classify.sh 'woof'
```

```python
{'data': 'woof',
 'prediction': 'dog',
 'probabilities': array([0.37996491, 0.62003509])}
```

For example, here is a cat/dog classifier trained using prompts.

Cat prompt:

```
Cats are generally more independent and aloof. Cats are also more territorial and may be more aggressive when defending their territory.
Cats are self-grooming animals, using their tongues to keep their coats clean and healthy. Cats use body language and vocalizations,
such as meowing and purring, to communicate.  An example cat is whiskers, who is a cat who lives in a house with a human.
Another example cat is furball, who likes to eat food and sleep.  A famous cat is garfield, who is a cat who likes to eat lasagna.
```

Dog prompt:
```
Dogs are social animals that live in groups, called packs, in the wild. They are also highly intelligent and trainable.
Dogs are also known for their loyalty and affection towards their owners. Dogs are also known for their ability to learn and
perform a variety of tasks, such as herding, hunting, and guarding. Dogs are also known for their ability to learn and
perform a variety of tasks, such as herding, hunting, and guarding.  An example dog is snoopy, who is the best friend of
charlie brown.  Another example dog is clifford, who is a big red dog.
```

```bash
./classify.sh --data "I like to sharpen my claws on the furniture." --data "I like to roll in the mud." --data "I like to run any play with a ball." --data "I like to sleep under the bed and purr." --data "My owner is charlie brown." --data "Meow, human! I'm famished! Where's my food?" --data "Purr-fect." --data "Hiss! Who dared to wake me from my nap? I'll have my revenge... later." --data "I'm so happy to see you! Can we go for a walk/play fetch/get treats now?" --data "I'm feeling a little ruff today, can you give me a belly rub to make me feel better?"
```

```python

{'data': 'I like to sharpen my claws on the furniture.',
 'prediction': 'cat',
 'probabilities': array([0.55363432, 0.44636568])}
{'data': 'I like to roll in the mud.',
 'prediction': 'dog',
 'probabilities': array([0.4563782, 0.5436218])}
{'data': 'I like to run any play with a ball.',
 'prediction': 'dog',
 'probabilities': array([0.44391914, 0.55608086])}
{'data': 'I like to sleep under the bed and purr.',
 'prediction': 'cat',
 'probabilities': array([0.51146226, 0.48853774])}
{'data': 'My owner is charlie brown.',
 'prediction': 'dog',
 'probabilities': array([0.40052991, 0.59947009])}
{'data': "Meow, human! I'm famished! Where's my food?",
 'prediction': 'cat',
 'probabilities': array([0.5172964, 0.4827036])}
{'data': 'Purr-fect.',
 'prediction': 'cat',
 'probabilities': array([0.50431873, 0.49568127])}
{'data': "Hiss! Who dared to wake me from my nap? I'll have my revenge... "
         'later.',
 'prediction': 'cat',
 'probabilities': array([0.50088163, 0.49911837])}
{'data': "I'm so happy to see you! Can we go for a walk/play fetch/get treats "
         'now?',
 'prediction': 'dog',
 'probabilities': array([0.42178513, 0.57821487])}
{'data': "I'm feeling a little ruff today, can you give me a belly rub to make "
         'me feel better?',
 'prediction': 'dog',
 'probabilities': array([0.46141002, 0.53858998])}
```

# Installation

Clone this repo, and run the `train.sh` or `classify.sh` command line tools.  

Requires docker: https://docs.docker.com/get-docker 

Setup your lamini keys (free): https://lamini-ai.github.io/auth/

`git clone git@github.com:lamini-ai/laminify.git`

`cd laminify`

Train a new classifier.

```
./train.sh --help

usage: train.py [-h] [--class CLASS [CLASS ...]] [--train TRAIN [TRAIN ...]] [--save SAVE] [-v]

options:
  -h, --help            show this help message and exit
  --class CLASS [CLASS ...]
                        The classes to use for classification, in the format 'class_name:prompt'.
  --train TRAIN [TRAIN ...]
                        The training data to use for classification, in the format 'class_name:data'.
  --save SAVE           The path to save the model to.
  -v, --verbose         Whether to print verbose output.

```

Classify your data.

```
./classify.sh --help

usage: classify.py [-h] [--data DATA [DATA ...]] [--load LOAD] [-v] [classify ...]

positional arguments:
  classify              The data to classify.

options:
  -h, --help            show this help message and exit
  --data DATA [DATA ...]
                        The training data to use for classification, any string.
  --load LOAD           The path to load the model from.
  -v, --verbose         Whether to print verbose output.

```

These command line scripts just call python inside of docker so you don't have to care about an environment.  

If you hate docker, you can also run this from python easily...


# Python Library

Install it
`pip install lamini`

Instantiate a classifier

```python
from lamini import LaminiClassifier

# Create a new classifier
classifier = LaminiClassifier()
```

Define classes using prompts

```python
classes = { "SOME_CLASS" : "SOME_PROMPT" }

classifier.prompt_train(classes)
```

Add some training examples (optional)

```python
data = ["example 1", "example 2"]
classifier.add_data_to_class("SOME_CLASS", data)

# Don't forget to train after adding data
classifier.train()
```

Classify your data

```python
# Classify the data
prediction = classifier.predict(data)

# Get the probabilities for each class
probabilities = classifier.predict_proba(data)
```

Save your model

```python
classifier.save("SOME_PATH")
```

Load your model
```python
classifier = LaminiClassifier.load(args["load"])
```

# FAQ

## How does it work?

Laminify converts your prompts into a pile of data, using the Llama 2 LLM. It then finetunes another LLM to distinguish between each pile of data.  

We use several specialized LLMs derived from Llama 2 to convert prompts into piles of training examples for each class.  The code for this is available
in the lamini python package if you want to look at it.  Working on open sourcing it when I'm not too distracted...

## Is this perfect?

No, this is a week night hackathon project, give us feedback and we will improve it.

## Why wouldn't I just use a normal classifier like BART, XGBoost, BERT, etc?

You don't need to label any data using Laminify.  Labeling data sucks.

No fiddling with hyperparameters. Fiddle with prompts instead.  Hopefully english is easier than attention_dropout_pcts.

## Why wouldn't I just use a LLM directly?

A classifier always outputs a valid class.  An LLM might answer the question "Is this talking about a cat" with "Well... that depends on ....".  Writing a parser sucks.

Added benefit: classifiers give you probabilities and can be calibrated: https://machinelearningmastery.com/threshold-moving-for-imbalanced-classification/

## Why does this FAQ sound so sarcastic?

Because it is 5am
