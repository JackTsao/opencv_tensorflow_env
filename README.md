# OpenCV + TensorFlow 開發環境設定

## 1. Setup Ubuntu Mate 18.04 environment on VM
ignore

## 2. Install relative packages
	$sudo apt-get update
	$sudo apt-get upgrade
	$sudo apt-get install vim git cmake htop curl build-essential
	$sudo apt-get install pkg-config zip g++ zlib1g-dev unzip 
	$sudo apt-get install default-jdk autoconf automake libtool
	$sudo apt-get install python libgtk2.0-dev pkg-config
	$sudo apt-get install python-pip python-numpy swig python-dev
	$sudo apt-get install gcc-4.8 g++-4.8
	$pip install wheel
	$sudo apt-get install python3-pip python3-numpy swig python3-dev
	$pip3 install wheel
	$sudo apt-get install openjdk-8-jdk
	$pip install future
	$cd /usr/bin
	$sudo ln -s python3 python
### 2.1 Setup java to 1.8
	$sudo update-alternatives --config java
	$sudo update-alternatives --config javac
### 2.2 Setup JAVA_HOME
	$vim ~/.bashrc 
	//Add the following line at end
   	export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
### 2.3 Setup gcc/g++
	$sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 100
	$sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.8 100
	
### 2.4 Increase system swap size (optional, RAM <=2G)
Please refer to the following
[link](https://bogdancornianu.com/change-swap-size-in-ubuntu/).

## 3. Create workspace 
	$cd ~/
	$mkdir -p workspace

## 4. Download & compile Eigen 3.3.5
	$cd ~/workspace
	$wget -t 0 -c https://github.com/eigenteam/eigen-git-mirror/archive/3.3.5.zip
	$unzip 3.3.5.zip
	$cd eigen-git-mirror-3.3.5
	$mkdir build
	$cmake ..
	$make -j4
	$sudo make install

## 5. Download & compile Bazel 0.24.1
	$cd ~/workspace
	$wget https://github.com/bazelbuild/bazel/releases/download/0.24.1/bazel-0.24.1-dist.zip
	$unzip -d bazel ./bazel-0.24.1-dist.zip
	$cd bazel
	
	//on RPI must do some extra => modified compile.sh at line 124 (add -J-Xmx2g)
	$vim ./scripts/bootstrap/compile.sh
	...
	run "${JAVAC}" -classpath "${classpath}" -sourcepath "${sourcepath}" -d "${output}/classes" -source "$JAVA_VERSION" -target "$JAVA_VERSION" -encoding UTF-8 ${BAZEL_JAVAC_OPTS} "@${paramfile}" -J-Xmx1g
	...
	
	$env EXTRA_BAZEL_ARGS="--host_javabase=@local_jdk//:jdk" bash ./compile.sh
	$sudo cp ./output/bazel /usr/local/bin/
	$bazel version
	
## 6. Download & compile absl
	$cd ~/workspace
	$git clone https://github.com/abseil/abseil-cpp.git
	$cd abseil-cpp
	$git checkout 52e88ee56b72cf32bc66534d942c7398ce481331
	$mkdir -p build
	$cd build
	$cmake ..
	$make -j4
	$sudo make install

## 7. Download & compile protobuf
	$cd ~/workspace
	$git clone https://github.com/protocolbuffers/protobuf.git
	$cd protobuf
	$git checkout v3.3.0
	$./autogen.sh
	$./configure
	$make -j4
	$sudo make install
	
## 8. Download & compile TensorFlow
	$cd ~/workspace
	$git clone --recursive https://github.com/tensorflow/tensorflow.git
	$cd tensorflow
	$./configure
	//on VM
	$bazel build //tensorflow:libtensorflow.so
	$bazel build //tensorflow:libtensorflow_cc.so
	$bazel build //tensorflow:libtensorflow_framework.so
   
## 9. Download & compile OpenCV v3.4.1
	$cd ~/workspace
	$git clone https://github.com/opencv/opencv.git
	$git clone https://github.com/opencv/opencv_contrib.git
	$cd opencv
	$git checkout 3.4.1
	$cd ..
	$cd opencv_contrib
	$git checkout 3.4.1
	$cd ../opencv
	$mkdir -p build
	$cd build
	$cmake -DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules ..
	$make -j4
	$sudo make install

## Reference:
1. https://blog.csdn.net/MOU_IT/article/details/87976152
2. http://www.liuxiao.org/2018/08/ubuntu-tensorflow-c-%E4%BB%8E%E8%AE%AD%E7%BB%83%E5%88%B0%E9%A2%84%E6%B5%8B1%EF%BC%9A%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/
3. https://www.codelast.com/%E5%8E%9F%E5%88%9B-%E5%9C%A8%E6%A0%91%E8%8E%93%E6%B4%BE%E4%B8%8A%E7%94%A8tensorflow%E7%8E%A9%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0deep-learning/
4. https://gist.github.com/EKami/9869ae6347f68c592c5b5cd181a3b205
5. https://tuatini.me/building-tensorflow-as-a-standalone-project/
