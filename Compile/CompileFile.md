## install dependency
Test in Linux 18.04-LTS. 
``` linux
# [compiler]
sudo apt-get install build-essential
# [required]
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
# [optional]
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```

## download source code
```
cd $BASE_DIR
git clone https://github.com/opencv/opencv.git --depth 1
git clone https://github.com/opencv/opencv_contrib.git --depth 1
```

- `BASE_DIR` is the directory where the source code is stored
- `--depth == 1` only get the latest code

## prepare directory for build
```
cd $BASE_DIR/opencv
mkdir build
cd build
```

Run the cmake command to generate a MakeFile file.
```
cmake \
-DCMAKE_BUILD_TYPE=Release \
-DCMAKE_INSTALL_PREFIX=/usr/local \
-DPYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3 \
-DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
-DOPENCV_ENABLE_NONFREE=ON \ 
-DBUILD_opencv_python3=ON \
-DBUILD_opencv_python2=OFF \
-DPYTHON3_EXCUTABLE=/usr/bin/python3 \
-DPYTHON3_INCLUDE_DIR=/usr/include/python3.6 \
-DWITH_CUDA=ON \
-DOPENCV_GENERATE_PKGCONFIG=ON \
-DINSTALL_C_EXAMPLES=OFF \
-DINSTALL_PYTHON_EXAMPLES=OFF \
-DBUILD_EXAMPLES=OFF \
..
```

```
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local -DPYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3 -DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules -DOPENCV_ENABLE_NONFREE=ON -DBUILD_opencv_python3=ON -DBUILD_opencv_python2=OFF -DPYTHON3_EXCUTABLE=/usr/bin/python3 -DPYTHON3_INCLUDE_DIR=/usr/include/python3.6 -DWITH_CUDA=ON -DOPENCV_GENERATE_PKGCONFIG=ON -DINSTALL_C_EXAMPLES=OFF -DINSTALL_PYTHON_EXAMPLES=OFF -DBUILD_EXAMPLES=OFF ..
```

## compile and install
```
make -j 4
sudo make install
```

## update environment variables
```
nano ~/.bashrc
source ~/.bashrc
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
```

## confirm OpenCV version
```
$ pkg-config --modversion opencv4
```

## compile your own program
### compile the executable
Compile command:
```
g++ XXX.cpp -o XXX $(pkg-config --cflags --libs opencv4)
```

### compile the dynamic link library
Compile command:
```
g++ -shared -fPIC --std=c++11 -Wfatal-errors -O3 XXX.cpp -o XXX.so $(pkg-config --cflags --libs opencv4)
```

Makefile:
```
CC = g++

.PHONY: all
all : XXX.so

.PHONY: lib
lib : XXX.so

XXX.so : XXX.cpp
        $(CC) $^ -o $@  -shared -fPIC --std=c++11 -Wall -Wfatal-errors -O3 `pkg-config --cflags --libs opencv4`
        ldd $@

test : test.cpp
        $(CC) $^ -o $@  -fPIC --std=c++11 -Wfatal-errors `pkg-config --cflags --libs opencv4`
        ldd test  # 显示依赖库
        ./test

# export LD_LIBRARY_PATH=.  
.PHONY: clean
clean:
        rm -fv *.so *.o test
        echo done
```

