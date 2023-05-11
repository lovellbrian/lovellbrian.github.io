# I Finally have my Local GPU Working on Windows

I finally have my local GPU working on Windows.  I have been trying to get this working for a while.  I have a NVIDIA Quadro P620  2GB card.  I have been trying to get it working with PyTorch and fastai  I have been using pip and Python 3.7.  I have been successful using the following links:

- [Pytorch Setup](https://pytorch.org/get-started/locally/)
- [CUDA 11.8 Toolkit](https://developer.nvidia.com/cuda-11-8-0-download-archive)
= [cuDNN 8.2.4 for CUDA 11.8](https://developer.nvidia.com/rdp/cudnn-archive)
- [fastai](https://docs.fast.ai/#Installing)
- [fastai Windows](https://forums.fast.ai/t/platform-windows/39)

The final install command was: 
`pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu117`

Then I was missing some common libraries in Python, so I ran the following comment in the [course22](https://github.com/fastai/course22) folder.

Now my notebooks run in VS Code on my local GPU. 

`pip install -r .devcontainer/requirements.txt`

![](/images/GPU.gif "Here is my GPU at 100%") 

100% GPU utilisation.  I am so happy.  

Happy GitHub GPUing, 

Brian

![](/images/Lovell_portrait_small.jpg "Brian Lovell")
