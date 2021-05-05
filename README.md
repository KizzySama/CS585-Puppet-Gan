# CS585-Puppet-Gan
CS585 course project - puppet gan

---
## Contents
1. [Introduction](#introduction)
2. [Model & Methods](#methods)
3. [Result](#results)
4. [Evaluation](#evaluation)

---
## Introduction
Puppet Gan is a machine learning model that can manipulate an individual attribute of objects in a real image by learning from sythetic images. Paticularly, in this project, attribute refers to pose of people in real image. The model is trained to transform the poses of the people in real pictures by learning from sythetic 3D modeling pictures. All real images come from [Weizmann AsSTS classification dataset](http://www.wisdom.weizmann.ac.il/~vision/SpaceTimeActions.html), which contains a variety of postures such as walking, running, jumping, etc.

---
## Methods
### Data Preparation
Given the dataset provided is in video format, we have to firstly extract images from videos to fit the input of model. We use video capturing to obtain images in PNG format. Then we resize the image based on the size of provided sythetic pictures.
After a few rounds of training, we found that the result is not satisfying due to the proportion of people in the entire picture is quite different from that in sythetic picture. Thus we use provided background and frame differencing between the current frame and background to decide the position of largest contour(the person). Then we crop image and resize to 256*256.
As for validation, we split provided triplets images and combline them into a row of images of length 10. Images in every row is picked following a certain rule(same background, same character, same postures).

![same background](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/same%20background.png)![same character](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/same%20character.png)![same pose](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/same%20pose.png)

### Model
The model contains two domain-agnostic encoders:
- E_attr for attribute of interest(AoI)
- E_rest for all other attributes

And two domain-specific decoders:
- G_a for real domain
- G_b for synthetic domain

![model](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/model.png "model")

The loss is a weighted sum of L1-penalties for violating the following constraint:
- **Reconstruction constraint** : ensures that encoder decoder pairs learn representations of real and synthetic domains.
- **Disentanglement constraint** ensures correct disentanglement of synthetic images by the shared encoder and the decoder for the synthetic domain.
- **Cycle constraint** ensures semantic consistency in visual correspondences learned by unsupervised image-to-image translation models.
- The pair of **attribute cycle constraints** prevents shared encoders and the real decoder from converging to a degenerate solution.

### Parameter exploring
We change the set of following parameters to fine-tune the model and found out which set of parameters optimize results of the task.
- learning rate
- loss weights: reconstruction, disentanglement, cycle,attribute cycle b3, attribute cycle a



- Learning rate:
"real generator" : 2e-4,
"real discriminator" : 5e-6,
"synthetic generator" : 2e-4,
"synthetic discriminator" : 5e-6

- Losses Weight:
"reconstruction" : 40,
"disentanglement" : 20,
"cycle" : 10,
"attribute cycle b3" : 5,
“attribute cycle a" : 3

![changed_learning_rate1](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/r1.png "Changed leahring rate1")

- Learning rate:
"real generator" : 1e-4,
"real discriminator" : 2.5e-5,
"synthetic generator" : 1e-4,
"synthetic discriminator" : 2.5e-6

- Losses Weight:
"reconstruction" : 40,
"disentanglement" : 20,
"cycle" : 10,
"attribute cycle b3" : 5,
“attribute cycle a" : 3

![changed_learning_rate2](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/r2.png "Changed leahring rate2")


---
## Results
Here is our best result:

- Learning rate:
"real generator" : 2e-4,
"real discriminator" : 3.5e-5,
"synthetic generator" : 2e-4,
"synthetic discriminator" : 3.5e-5

- Losses Weight:
"reconstruction" : 40,
"disentanglement" : 20,
"cycle" : 10,
"attribute cycle b3" : 5,
“attribute cycle a" : 3


![changed_learning_rate3](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/r3.png "Changed leahring rate3")







---
## Evaluation

---
## Reference
- [1] [SynAction dataset](https://arxiv.org/pdf/1812.01037.pdf)
- [2] [Unofficial implementation of Puppet GAN by Giorgos Karantonis](https://github.com/GiorgosKarantonis/PuppetGAN)
- [3] [Pose estimation script](https://gist.github.com/MInner/ff536d865404d878708822f0c3b922f9)
