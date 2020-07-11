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

