# CS585-Puppet-Gan
CS585 course project - puppet gan

Group member: Deyan Hao, Kaijun Wang, Yirong Zhang

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
- learning rates
- loss weights: reconstruction, disentanglement, cycle,attribute cycle b3, attribute cycle a

The default parameters from the model are:

**Learning rate**:
- "real generator" : 2e-4,
- "real discriminator" : 5e-5,
- "synthetic generator" : 2e-4,
- "synthetic discriminator" : 5e-5

**Losses Weights**: 
- "reconstruction" : 10,
- "disentanglement" : 10,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 

we get the result:
![default](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/default.png)

We first explore the losses weight with default learning rate. 

Test 1:

**Losses Weights**: 
- "reconstruction" : 20,
- "disentanglement" : 10,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 3

![1](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/20%2010%2010%205%203.png)

Test 2:

**Losses Weights**: 
- "reconstruction" : 20,
- "disentanglement" : 20,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 3

- ![2](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/20%2020%2010%205%203.png)

Test 3:

**Losses Weights**: 
- "reconstruction" : 40,
- "disentanglement" : 20,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 3

![3](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/40%2020%2010%205%203.png)

With the discovered best losses weight, we then tested different learning rate.

Test 1:

**Learning rate**:
- "real generator" : 2e-4,
- "real discriminator" : 5e-6,
- "synthetic generator" : 2e-4,
- "synthetic discriminator" : 5e-6

**Losses Weight**:
- "reconstruction" : 40,
- "disentanglement" : 20,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 3

![changed_learning_rate1](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/r1.png "Changed leahring rate1")

Test 2:

**Learning rate**:
- "real generator" : 1e-4,
- "real discriminator" : 2.5e-5,
- "synthetic generator" : 1e-4,
- "synthetic discriminator" : 2.5e-5

**Losses Weight**:
- "reconstruction" : 40,
- "disentanglement" : 20,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 3

![changed_learning_rate2](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/r2.png "Changed leahring rate2")


---
## Results

With the limited set of real image, we found this parameter to produce the best result. Although the background is not precisely preserved, this parameter retains actions of the figure fairly well. 

**Learning rate**:
- "real generator" : 2e-4,
- "real discriminator" : 3.5e-5,
- "synthetic generator" : 2e-4,
- "synthetic discriminator" : 3.5e-5

**Losses Weight**:
- "reconstruction" : 40,
- "disentanglement" : 20,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 3


![changed_learning_rate3](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/r3.png "Changed leahring rate3")



With the secceessful production of smaller real image, we apply the same parameter to a larger training data set, with different actions in the real images, in another word, our training dataset gets larger.

**Learning rate**:
- "real generator" : 2e-4,
- "real discriminator" : 4e-5,
- "synthetic generator" : 2e-4,
- "synthetic discriminator" : 4e-5

**Losses Weight**:
- "reconstruction" : 40,
- "disentanglement" : 20,
- "cycle" : 10,
- "attribute cycle b3" : 5,
- “attribute cycle a" : 3

As shown, the background and some of the action are perserved nicely, while some actions are not as precise as the result from the smaller data set. Some actions are not retained well enough probably because the people is relatively small in real scene and as the result actions get smaller some information get lost.

![4](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/4.gif)

![5](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/15.gif)

![6](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/11.gif)

---
## Evaluation
We use provided pose estimation script to generate 17 keypoints of posture in a image. This is implemented by tensorflow posenet. Then we selected 13 keypoints from them to simplify the posture estimation. We use a standard called Object Keypoint Similarity (OKS) to evaluate the similarity between generated pose and ground truth. The formula is shown below:

![oks_formula](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/oks_formula.png)

- di — the euclidian distance between ground truth keypoint and predicted keypoint;
- s — scale: the square root of the object segment area;
- k — per-keypoint constant that controls fall off;

We first calulated OKS score of all given synthetic triplet and got an average score of 0.9469. We used it as a standard to determine how good of our model is.

![oks_1](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/oks_1.png)

Then we tested on model trained with only 90 real images from jump dataset. We got an average score of 0.6495 and the best score of a pair of GT and generated pose reached 0.8044.

![oks_2](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/oks_2.png)

At last we tested on model trained with all 1000 real images. We only got an average OKS score of 0.4975 and best score of 0.6119.

![oks_3](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/oks_3.png)![oks_4](https://github.com/KizzySama/CS585-Puppet-Gan/blob/master/imgs/oks_4.png)

|  | Average OKS | Best OKS |
| :-----| ----: | ----: |
| Synthetic images | 0.9469 | - |
| Testset on first model | 0.6495 | 0.8044 |
| Testset on second model | 0.4975 | 0.6119 |

---
## Conclusion
GAN is quite sensitive to hyper parameters, even a small change makes a big difference in the final output. So we have to trained the model from the very start for every set of parameters which makes fine-tuning time-consuming and low efficient.

We only used 90 real images from jump dataset and 10,000 synthetic triplets at the beginning which costed around 9 hours per model. Then we used 1000 real images from the whole dataset which costed almost 20 hours to train.

Model trained with the small dataset generates images with bad reconstruction of realistic body and background but quite robust pose information. 
Model trained with large dataset generates clear body structure and background with the same texture as real images. However, posture information is lost in some of predictions (especially  postures with a horizontal body) which leads to a low score in OKS evaluation.

---
## Reference
- [1] [SynAction dataset](https://arxiv.org/pdf/1812.01037.pdf)
- [2] [Unofficial implementation of Puppet GAN by Giorgos Karantonis](https://github.com/GiorgosKarantonis/PuppetGAN)
- [3] [Pose estimation script](https://gist.github.com/MInner/ff536d865404d878708822f0c3b922f9)
