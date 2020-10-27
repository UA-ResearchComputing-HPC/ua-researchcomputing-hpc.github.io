# Singularity - CentOS7, Theano0.9, Python3.4, Cuda7.5, cuDNN5.1

## About 
This example will result in a CentOS 7-based container that can successfully run Theano with GPU support. Building this container requires root privileges on the build host and execution requires the presence of an NVIDIA GPU. Theano is a bit nastier than Tensorflow from a setup perspective as it requires compilation of an C library to function, so the definition file is mildly more complex than the Tensorflow example.

## Definition File
The first step is to create a definition file. This file is used by Singularity to install all the packages you'll need, run any additional setup steps you need interior to the container, and set up the runscript that makes running code via the container more convenient.

You'll see above that this definition file requires the location of the cudnn library tarball. You'll need to download that directly from NVIDIA and modify the path accordingly: https://developer.nvidia.com/cudnn

```
BootStrap: yum
OSVersion: 7
MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum wget
%setup
   # commands to be executed on host outside container during bootstrap
   cp cudnn-7.5-linux-x64-v5.1.tgz $SINGULARITY_ROOTFS/cudnn.tgz
%test
   # commands to be executed within container at close of bootstrap process
   exec /usr/bin/python3.4 --version
%environment
   export CUDA_HOME=/usr/local/cuda
   CUDA_LIB=$CUDA_HOME/lib64
   CUDA_INCLUDE=$CUDA_HOME/include
   CUDA_BIN=$CUDA_HOME/bin
   export LD_LIBRARY_PATH=$CUDA_LIB:/usr/local/lib
   export PATH=$CUDA_BIN:$PATH
   #export THEANO_FLAGS=device=cuda,dnn.include_path=$CUDA_INCLUDE,dnn.library_path=$CUDA_LIB
   # BEST IDEA IS TO SET THEANO OPTIONS IN THEANORC
%runscript
   # commands to be executed when the container runs
   echo "LD_LIBRARY_PATH: $LD_LIBRARY_PATH"
   echo "PATH: $PATH"
   echo "THEANO_FLAGS: $THEANO_FLAGS"
   echo "Arguments received: $*"
   exec /usr/bin/python3.4 "$@"
%post
   # commands to be executed inside container during bootstrap
   yum -y install epel-release
   yum -y install https://centos7.iuscommunity.org/ius-release.rpm
   yum -y install http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/cuda-repo-rhel7-7.5-18.x86_64.rpm
   yum clean all && yum makecache
   yum -y groupinstall "Development Tools"
   yum -y install wget vim-enhanced python34u python34u-pip python34u-devel libgomp cuda-runtime-7-5 check cmake3 cuda-misc-headers-7-5 cuda-cudart-dev-7-5
   ln -s /usr/local/cuda-7.5 /usr/local/cuda
   # install cudnn
   tar -xf /cudnn.tgz -C /usr/local
   rm /cudnn.tgz
   echo "/usr/local/cuda/lib64" > /etc/ld.so.conf.d/cuda.conf
   ldconfig
   pip3.4 install --upgrade pip
   pip3.4 install Theano==0.9 numpy==1.11.0 scipy=0.17.1 nose
   # install libgpuarray and pygpu by hand because these people are the worst
   git clone https://github.com/Theano/libgpuarray.git /libgpuarray
   mkdir /libgpuarray/Build
   cd /libgpuarray/Build && cmake3 .. -DCMAKE_BUILD_TYPE=Release && make && make install
   cd /libgpuarray && python3.4 setup.py build && python3.4 setup.py install
   rm -rf /libgpuarray
   # in-container bind points for shared filesystems
   mkdir -pl /xdisk /cm/shared
   ```
   
## Build
Next, you'll need to initialize an image for Singularity to operate on. For this particular use-case, we're going to make a 4GB image file.
```
$ singularity create --size 4096 theano09_cp34_cuda75_cudnn51.img
Initializing Singularity image subsystem
Opening image file: theano09_cp34_cuda75_cudnn51.img
Creating 4096MiB image
Binding image to loop
Creating file system within image
done
Image is done: theano09_cp34_cuda75_cudnn51.img
```
