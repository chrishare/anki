## ANN Layers

Artificial Neural Network (ANN) layers include:

%

Input - The first layer, that takes input from sensors/images/etc
Hidden - Layers from the second onwards (between Input and Output)
Output - The final layer that outputs information in the form of a prediction (e.g. probability), a categorical classification, regression output (e.g. house cost should be 100000), and so on

Note - Usually every neuron in a layer is connected to every neuron in the next (Fully Connected), but there are exceptions. Logically, you often don't want each neuron to matter to all neurons inthe next layer, but this is mitigated by many weights being 0 (making the network sparse) - because doing large matrice multiples on GPUs is faster than doing many small matrix multiplies.

## Datasets

Different datasets are used for different purposes. What are they?

%

Train - The dataset used for training (learning). The algorithm can look at this dataset freely and uses it to learn.
Validation - The dataset used for validating hyper parameter choices. For instance, you choose a hyper parameter for the number of layers, and reserve a validation training set to compare the different choices. The final (best) algorithm is biased on the validation set.
Test - A final dataset that is only used at the very end to validate a chosen, tuned, model. Training should not be performed on this dataset at any time.

The datasets need to be chosen randomly, IID (independently and identically distributed).

Ratios between these 3 sets vary, depending on how much data you have. Something like 80, 10, 10 is good. There are also techniques that allow random splits to be used between train and validation, such as Cross Validation.

## Supervised Learning Problem Types

There are different problem types that ML can address. Can you name some of them?

%

Regression - Maps inputs into a continuous output numeric value; e.g. Given size of house, number of rooms, lattitude and longitude, output the estimated house price as 350000. https://d2l.ai/chapter_introduction/index.html#regression 

Classificiation - Map inputs into one of many classes usually with a probability estimate for each; e.g. Given rgb pixel values (an image), output the confidence that the image contains a dog or a cat. https://d2l.ai/chapter_introduction/index.html#classification

Multi-class classification or Tagging - Like classification, but classes are not mutually-exclusive. e.g. Given rgb pixel values (an image), output the confidence that the image contains a dog, that it contains a cat, that it contains a horse and so on. Another example - tagging an article with "Machine Learning", "Computer Science" and "Ethics" - these aren't mutually exclusive. https://d2l.ai/chapter_introduction/index.html#tagging

Search and Rank - The problem of ranking a set of input samples according to some desired condition(s). For instance, ranking C B D E A as A B C D E, ranking articles according to relevance (a la Google). https://d2l.ai/chapter_introduction/index.html#search-and-ranking

Recommender Systems - Similar to Search and Rank, but usually related to an individual user's personal preferences. An example would be Amazon recommending companion products to what you have already purchased in the past. https://d2l.ai/chapter_introduction/index.html#recommender-systems

Sequence Learning - Rather than looking as a fixed number of inputs, look at a dynamic-length sequence. The classic example is in NLP (Natural Language Processing), but other examples are video frame process - earlier frames give context (motion, etc) - and time series data, like medical events and stock market values over time. https://d2l.ai/chapter_introduction/index.html#sequence-learning

### What is Unsupervised Learning?

What is unsupervised learning? What does supervision really mean?

%

Supervised learning usually refers to learning for data that is explicitly labelled with the signal that is trying to be learned, e.g. Images are labelled as cats or dogs. It isn't always practical to gather large amounts of data with labels, since that might require lots of human effort.

In these situations, unsupervised learning approaches can be used. There are very new techniques arising in this field, as well as some more mature techniques - clustering (partition the space into clusters), subspace learning/principal component analysis, representation learning (learning a simple(r) set of features that represent all samples), causality/relationship learning, and GANs (generative adversarial networks).

Sometimes, a combination of unsupervised (e.g. pre-training) and supervised fine-tuning learning makes sense - where there's a large dataset but a small subset might be labelled.

More recently, self-supervised has been a term used to describe the same thing but make it clear that there is signal in the input - it's just the training process / model that has to find the signal, as opposed to produce signal where there is none.
