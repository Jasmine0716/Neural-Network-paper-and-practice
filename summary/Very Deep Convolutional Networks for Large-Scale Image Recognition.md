## Very Deep Convolutional Networks for Large-Scale Image Recognition

###### Three aspects for the improvement of the training result

* smaller receptive window size + smaller stride of the first convolutional layer
* training and testing the networks densely over the whole image and over multiple scales
* increasing depth &larr; feasible due to the use of very small convolution filters in all layers
* * * 



###### Architecture & Strategy

* before training
  * preprocess: subtracting the mean RGB value, computed on the training set from each pixel
  * input: a fixed size of 224&times;224
  * initialization
    * pre-initialization of certain layers: 
      * begin with training smaller conv net(A), then training deeper nets, initialised the the first four conv layers and the three fc layers with parameters in A
    * random initialization: 
      * weights: from a normal distribution with the zero mean and 10<sup>-2</sup> variance
      * bias: zeros
  
  * data
    * Image Source:
      * original image &rarr; isotropically rescale the image and make the smallest side is S &rarr; crop &rarr; final size: 224&times;224
      * S
        * training:
          * fixed scales: 256/384(to speed up the training of S=384 network, it was initalised with the weights pre-trained with S=256)
          * multi-scale: training image is individually rescaled by randomly sampling S from [S<sub>min</sub>/256, S<sub>max</sub>/512]  &rarr; this strategy lead to a better result
        * testing:
          * original image &rarr; isotropically rescale the image and make the smallest side is Q
          * Q is not necessarily equal to S
          * using several values of Q for each S lead to improved performance
  * Augment: 
    * random horizontal flipping
    * random RGB color shift
  
  * The network is applied over the whole image, there is no need to sample multiple crops at test time



* training
  * multiple GPUs
  * activation: All hidden layers are equipped with the ReLU
  * normalization: none used LRN
  * parameters
    * batch size: 256
    * momentum: 0.9
    * weight decay-L<sub>2</sub> penalty multiplier: 5&times;10<sup>-4</sup>  <font color='red'>??</font>
    * dropout: the first two FC layers, dropout ratio-0.5
    * learning rate: initial:10<sup>-2</sup>   decrease by 10 when validation set stop improving 
  * the class score map is spatially averaged
  * general structure:
    * ![VGG](https://upload-images.jianshu.io/upload_images/3352522-1a60b820389a1ae5.png?imageMogr2/auto-orient/strip|imageView2/2/w/830/format/webp)



###### Deeper Understanding of the Strategies

* the advantages of a stack of two 3&times;3 conv layers over one 5&times;5
  * decrease the number of parameters
  * make the decision function more discriminative
* the incorporation of 1&times;1 conv layers
  * a way to increase the nonlinearity of the decision function without affecting the receptive field of the conv layers
* Applying a ConvNet to a crop VS to the whole image
  * crop: padded with zeros
  * whole image: padded with neighbouring parts of an image, more context is captured