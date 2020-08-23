## GAN Terminology

Terms include:

%

Generator - Responsible for spawning instances from a distribution. Instances are called fake because they are generated.

Disriminator - Responsible for identifying the generated/fake instances from those in the real distribution.

## How GANs work

Invented in 2014 by Ian Goodfellow (https://papers.nips.cc/paper/5423-generative-adversarial-nets.pdf), Generative Adversarial Networks (GANs) comprise a generator model that generates instances as close to the distribution as possible, and a discriminator that tries to classify instances as generated or real. The objective is to train generator and discriminator against each other, in an adversarial manner, so that ultimately the 'fake' instances are so close to the underlying distribution that the discriminator can only guess.

Say that:

G(z) is the generator mapping latent space z to a generated instance

D(G(z)) is the estimated probability (scalar) that what G(z) output is fake

p_data is the probability distribution of real data

p_g is the probability distribution estimated by G

The generator G and discriminator D are playing a minimax game; D is trying to maximize the probably that it can classify real from fake - log(D(x)) - and G is trying to minimize the probability that D can classify correctly - log(1 - D(G(x)). 

The GAN loss function is therefore

mimimax valuefunction(G,D) = E[x ~p_data(x)](log(D(x))) + E[z ~p_z(z)](1 - D(G(z))
