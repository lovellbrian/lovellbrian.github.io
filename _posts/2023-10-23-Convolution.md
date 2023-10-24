# Convolution is just Matrix Multiplication

Hat tip to Penny at her [blog](https://penny-xu.github.io/blog/convolution-is-matrixmultiplication) for much of this article including the images. 

I was trying to understand how convolution is performed in GPU hardware and Penny gave me some good insights which inspired my own interpretation. 

The computation pattern in training a convolutional network is very similar to matrix multiplication: it is both compute intensive and highly parallel.

convolution == 2d dot product == unrolled 1d dot product == matrix multiplication

Penny says: "Thinking about it - convolution is just a 2d dot product".  This is a pretty useful analogy. However, 2D convolution and matrix products look quite different at first glance. One helpful tip is to note that all matrices and images are ultimately stored as flattened blocks of memory in our computing devices. 

## Example 3D Tensor Convolution

![Alt text](/images/conv.jpg)

Above we have a simple illustration of a 3D convolution of a 3x3x3 colour image convolved with a 3x2x2 kernel to yield a 2x2 activation layer.

That is, we have a convolution layer that takes the input as 3 feature maps (input channel C = 3) of size 3 x 3. Let's have the layer reduce the output to 1 feature map (output channel C' = 1) of size 2 x 2.

```console

    Input: C x H x W = (3 x 3 x 3)
    Kernel: C x K x K = (3 x 2 x 2)
    Output: C' H' x W' = (H - K + 1) x (W - K + 1) = (2 x 2)

```
So let's see how we can express this 3D convolution as a Matrix Multiplication. 

First notice how the both the input and the kernel is 3D. Well in order to use matrix multiplication, we would need to have a 2D input and a 2D kernel so we can treat both like a matrix. This leads to the flattening of the kernel and duplication of the inputs described below.

Reducing the kernel - each channel of the kernel will be linearized and concatenated shown in grey.

Reducing the input - the input elements needs to be rearranged and duplicated. Since the results of the convolutions are summed across input features, the input features can be concatenated into one large matrix. The height of the unrolled input equals to the number of kernel elements, 12 in our case. The width of the unrolled input equals to the number of output elements, which is 4 in our case.

![Alt text](/images/demo-9af80b0afedfed875b486a194dbd3211.gif)

## Why do we REALLY care...

With deep learning networks increasing in size, increasing in computational complexity, as well as the increasing demand for realtime computation (self-driving cars, real time image processing etc), these convolution layers and its operations become the bottleneck in these fast system applications. So in order to optimize these convolution layers, would you rather optimize convolution or matrix multiplication? If you pick the latter, then yeah I agree, since matrix multiplication is something high schoolers understand... It is much easier to tweak a matrix multiplication function in order to say optimize for speed or power through understanding the software implementation as well as how is it applied to hardware. In addition to optimizing the software such as linear algebra libraries, recent areas of research are done on specialized hardware, such as implementation of hardware accelerators to just do matrix multiplication. Indeed GPUs are specialized hardware which can do matrix multiplication in a very fast, parallelizable way.

## Size Matters

One issue with the above construction is that the image size is increased from 27 elements to 36 elements while the kernel remains only 12 elements. The matrix appears as follows and there is a fair amount of redundancy in the image data.  As images tend to be large and kernels are small, it would make more sense to have redundacy in the kernel representation instead of the image representation.

![Alt text](/images/image-25.png)

Here is another explanation from [Baeldung](https://www.baeldung.com/cs/convolution-matrix-multiplication) which does it this way. Note that this is 2D convolution and not 3D.

Let's say A (nxn) and K (mxm) are 3x3 and 2x2: 

![Alt text](/images/image-26.png)

The corresponding convolution matrices are:

![Alt text](/images/image-27.png)

The result of this convolution is:

![Alt text](/images/image-28.png)


Here the image has the same number of elements but the kernel has increased from 4 elements to 36, which doesn't look so good. In fact, if the image width is n=256 pixels, then M must have nxn=65,536 columns and (n-m)x(n-m)=64,516 rows. This is quite daunting. However note that `M` is a block circulant matrix. Each row is simply the same set of 4 elements shifted by either one or two columns in the example. This means we can replace the large `M` matric by a simple vector with 4 elements representing the flattened kernel. Now it is simply a matter of caculating the dot product of the kernel vector with the image vector using pointers to shift the alignment of the kernel elements against the image vector.  In this manner, neither the image nor the kernel is expanded and the convolution as a matrix multiply reduces to set of simple vector dot products. 

Enjoy!

I hope you now have a good idea of how convolutions can be expressed as matrices. 

Brian

![Lovell Portrait](/images/Lovell_portrait_small.jpg "Brian Lovell")

<!-- Put Javascript here! -->

<script src="/assets/scripts/copyCode.js" async> </script>