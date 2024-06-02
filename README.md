# Generating a Complex Dataset From Compute

The goal of this project is to generate a complex, interesting dataset with a neural net, but *without relying on any dataset*. The end goal is to generate something as complex as human language from scratch using only raw, pure compute.

This possibility is supported by the strong ability of LLMs to compress images and audio while being trained only on text, beating domain-specific compressors like PNG [1]. By only looking at text, LLMs become general-purpose compressors. This raises the question: "Could there be a universal form of data, even more complex than human language, that could create a powerful, universal compressor?"

Here, the idea is to generate data with a decoder that maximizes an autoencoder's reconstruction error. By competing with the autoencoder, the decoder would generate increasingly complex data. Here are some rudimentary results on images:

<p align="center">
Evolution of the generated images through training iterations </br>
<img width=200 src="images/exp1/20.gif">
<img width=200 src="images/exp2/9_1.gif">
</p>

<p align="center">
Images sampled from different models after training </br>
<img width=200 src="images/extra/3.png">
<img width=200 src="images/extra/4.png">
<img width=200 src="images/exp1/2.png">
<img width=200 src="images/exp1/19.png">
<img width=200 src="images/extra/5.png">
<img width=200 src="images/extra/2.png">
<img width=200 src="images/extra/1.png">
<img width=200 src="images/exp2/4_1.png">
</p>

## Goal

Ideally, the goal is to generate a universal, intrinsically "complex" and "interesting" dataset on par with human language or real-world images/videos, using a neural net decoder trained without any prior data. This would involve a self-sufficient training procedure that maximizes the generated dataset's complexity. Then, we would use this infinite dataset to pretrain a kind of universal computation engine that can process any data that has been adapted for it with a lightweight stem/head, a bit like LLMs, but more general (see [2]).

A crucial hypothesis is that such a universal computation engine exists. More precisely, it means that there exists an abstract space shared by any complex datasets in which the patterns are similar for all datasets, such that any dataset translated into this abstract space can be further encoded by a single model without further training. This model would perform some sort of universal pattern recognition, maybe something akin to logical reasoning. If such a universal computation engine exists, there probably is an algorithm to build it.

## Complexity

Intuitively, complexity lies between uniformity and randomness, where the entropy is respectively minimal and maximal, two extremes where the complexity is at its lowest. Complexity might be maximal at some kind of critical points, like in some physical systems at phase transitions. Some of the most complex stuff we probably know are human language, the visual world, games, etc. What is truly complex in these data is the underlying world model behind them, which models complex interactions between different entities. More generally, we could consider a dataset as complex when we find it interesting to use for training a neural net, i.e. when it contains complex patterns to be detected and compressed.

Before being fed into a neural net, any dataset is represented by a bunch of numbers. For example, human language is just a sequence of numbers. Therefore, since any dataset is just numbers, the essence of information does not lie in each individual piece of data, but in the relation between the pieces of data. Language is complex because of the way each word interacts with other words. Fundamentally, what is interesting and complex are relations and interactions. What is important is the structure formed by the data points, their distribution; this is what really differentiates two datasets (and the dimensionality of the data points). We could expect that all complex datasets have a similar structure that characterizes them, somewhere between low and high entropy.

The problem is that we lack a practical metric for measuring the complexity of a dataset that we could use as a loss function for our dataset generator.

## Method

If we could find the right algorithm and scale it, it could be possible to generate a truly complex dataset on par with text and video on the internet. This dataset would be infinite and without any biases apart from the inductive biases. In fact, complex datasets can already be generated by current generative models like LLMs and image/video generative models when trained on appropriate data. The issue is how to train such models without prior data considering the huge space of possibilities.

The idea is to use another neural net in addition to the dataset generator to maximize the complexity in an implicit way. We do this with an autoencoder that compresses then reconstructs the data points by minimizing the reconstruction error (we can use masked modeling). The generated dataset is complex if the autoencoder struggles to reconstruct the data points while still being possible, since each data point is generated by a neural net conditioned on a random latent vector given as input. Thus, theoretically, the encoder part of the autoencoder can always compress the data points. To make the dataset complex, the generator maximizes the reconstruction error of the autoencoder. By iterative improvement, the generator and the autoencoder would jointly improve upon each other to achieve increasing complexity, a bit like AlphaZero for chess, where the model improves upon itself without human data.

One problem with this approach is the lack of diversity in the resulting dataset. To solve this, we can train a population of generators and autoencoders which could interact with each other.

An important question is where the complexity of this method stops. Can it reach "superhuman" performance by going beyond human language, like AlphaZero for chess? Or can it even increase the complexity indefinitely, a bit like the correlation length at the critical point in some physical systems?

If it works, it would mean that a neural net can build a coherent and complex world model from scratch without seeing our own world or human-crafted games. It would prove that neural nets can have their own internal world model, and even more than that, generate one from scratch. To prove it, we could use a trained model as a backbone for downstream tasks, like a universal computation engine, and see how it performs.

## Experiments

In this repository, the goal is just to explore the possibility of generating "complex" or "interesting" images with neural nets without any training data. The training dynamics of the proposed method are complex, sensitive, and not fully understood, but we could still generate some interesting images.

Each experiment has its own script - exp1.py, exp2.py - containing the code to train the model and generate images.

The generators (= decoders) consist of a stack of transposed convolutions, batch normalizations, ReLUs, and average poolings, while the encoders consist of a stack of convolutions, batch normalizations, and leaky ReLUs. The average pooling layers in the decoders are particularly important as they act as strong regularizers. Without them, we do not obtain interesting images.

### Experiment 1

The model is a decoder followed by an autoencoder. The idea is to create a "complex" dataset for the autoencoder. The decoder generates images from random latent vectors, and the autoencoder compresses and reconstructs the generated images, minimizing the reconstruction error. Here, there is no masked modeling objective, but it would be an interesting future direction. For the autoencoder's task to be complex, the decoder maximizes the autoencoder's reconstruction error. This way, the decoder cannot generate something simple like uniform images, which would be too simple to autoencode, but it still must generate images that can be autoencoded, since it is conditioned on the input latent vector. The model is thus constrained to generate "complex" images. The level of complexity varies with the latent vectors' dimensions.

<p align="center">
Evolution of the generated images through training iterations </br>
<img width=150 src="images/exp1/20.gif">
</p>

<p align="center">
Images sampled from different models after training </br>
<img width=150 src="images/exp1/2.png">
<img width=150 src="images/exp1/3.png">
<img width=150 src="images/exp1/6.png">
<img width=150 src="images/exp1/11.png">
<img width=150 src="images/exp1/12.png">
<img width=150 src="images/exp1/13.png">
<img width=150 src="images/exp1/14.png">
<img width=150 src="images/exp1/15.png">
<img width=150 src="images/exp1/16.png">
<img width=150 src="images/exp1/19.png">
</p>

<p align="center">
Image sampled before training for reference </br>
<img width=150 src="images/exp1/18.png">
</p>

### Experiment 2

This is a variant of experiment 1 where we use 2 decoders and 2 autoencoders instead of just one. The 2 decoders generate images from random latent vectors and each autoencoder compresses and reconstructs the images generated from both decoders.

<p align="center">
Evolution of the generated images through training iterations </br>
<img width=150 src="images/exp2/9_1.gif">
</p>

<p align="center">
Images sampled from different models after training </br>
<img width=150 src="images/exp2/1_2.png">
<img width=150 src="images/exp2/2_1.png">
<img width=150 src="images/exp2/3_1.png">
<img width=150 src="images/exp2/4_1.png">
<img width=150 src="images/exp2/5_1.png">
</p>

<p align="center">
Image sampled before training for reference </br>
<img width=150 src="images/exp2/6_1.png">
</p>

## References

[1] Grégoire Delétang, Anian Ruoss, Paul-Ambroise Duquenne, Elliot Catt, Tim Genewein, Christopher Mattern, Jordi Grau-Moya, Li Kevin Wenliang, Matthew Aitchison, Laurent Orseau, Marcus Hutter, and Joel Veness. Language Modeling Is Compression. arXiv preprint arXiv:2309.10668, 2023.

[2] Kevin Lu, Aditya Grover, Pieter Abbeel, and Igor Mordatch. Pretrained Transformers as Universal Computation Engines. arXiv preprint arXiv:2103.05247, 2021.