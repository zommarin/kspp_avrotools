kspp_avrotools
=========

A fork of avro projects avrogencpp that adds the schema to the generated class.
This is required by kspp avro serdes to automatically register avro classes to schema registry
 

Platforms: Windows / Linux / Mac


## Ubuntu 16 x64:

Install build tools
```
sudo apt-get install -y automake autogen shtool libtool git wget cmake unzip build-essential libboost-all-dev g++ python-dev autotools-dev libicu-dev zlib1g-dev openssl libssl-dev libbz2-dev libsnappy-dev libgoogle-glog-dev libgflags-dev libjansson-dev libcurl4-openssl-dev liblzma-dev pkg-config
```

Install a late avro
```
git clone https://github.com/apache/avro.git
cd avro
git checkout release-1.8.2
cd lang/c++/
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j8
[sudo] make install
cd ../../../..
```

Installation
```
git clone https://github.com/bitbouncer/kspp_avrotools.git
cd kspp_avrotools
mkdir build
cd build
cmake ..
make
[sudo] make install
```

## MacOS X

Install build tools (using Homebrew)
```
# Install Xcode
xcode-select --install
brew install cmake
brew install boost
brew install boost-python
```

Install a late avro
```
git clone https://github.com/apache/avro.git
cd avro
git checkout release-1.8.2
cd lang/c++/
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j8
[sudo] make install
cd ../../../..
```

Installation
```
git clone https://github.com/bitbouncer/kspp_avrotools.git
cd kspp_avrotools
mkdir build
cd build
cmake ..
make
[sudo] make install
```

## Windows x64:

Install build tools
```
- CMake (https://cmake.org/)
- Visual Studio 14 (https://www.visualstudio.com/downloads/)
- nasm (https://sourceforge.net/projects/nasm/)
- perl (http://www.activestate.com/activeperl)
```
Build
```
wget --no-check-certificate http://downloads.sourceforge.net/project/boost/boost/1.62.0/boost_1_62_0.zip
unzip boost_1_62_0.zip
rename boost_1_62_0 boost
rm -f boost_1_62_0.zip

git clone https://github.com/madler/zlib.git
git clone https://github.com/lz4/lz4.git
git clone https://github.com/apache/avro.git
git clone https://github.com/bitbouncer/kspp.git

set VISUALSTUDIO_VERSION_MAJOR=14
"C:\Program Files (x86)\Microsoft Visual Studio %VISUALSTUDIO_VERSION_MAJOR%.0\VC\vcvarsall.bat" amd64

cd zlib
rm -rf build
mkdir build & cd build
cmake -G "Visual Studio 14 Win64" ..
msbuild zlib.sln
msbuild zlib.sln /p:Configuration=Release
copy /y zconf.h ..
cd ../..

cd boost
call bootstrap.bat
.\b2.exe -j8 -toolset=msvc-%VisualStudioVersion% variant=release,debug link=static threading=multi runtime-link=shared address-model=64 architecture=x86 --stagedir=stage\lib\x64 stage -s ZLIB_SOURCE=%CD%\..\zlib headers log_setup log date_time timer thread system program_options filesystem regex chrono iostreams
cd ..

cd avro
git checkout release-1.8.2
cd lang/c++/
rm -rf build
mkdir build & cd build
cmake -G "Visual Studio 14 Win64" -DBOOST_ROOT=../../../boost -DBOOST_LIBRARYDIR=..\..\..\boost\stage\lib\x64\lib -DBoost_USE_STATIC_LIBS=TRUE ..
#-DBoost_DEBUG=ON
msbuild /maxcpucount:8 avrocpp_s.vcxproj
msbuild /maxcpucount:8 avrocpp_s.vcxproj /p:Configuration=Release
cd ..
rm -rf include
mkdir include
mkdir include\avro
mkdir include\avro\buffer
copy /y api\*.hh include\avro
copy /y api\buffer\*.hh include\avro\buffer
cd ../../..


cd kspp_avrotools
call rebuild_windows_vs14.bat
cd ..
```
 

