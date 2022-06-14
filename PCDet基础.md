# PCDet基础

## 0.简介

`OpenPCDet` is a clear, simple, self-contained open source project for LiDAR-based 3D object detection.

希望可以安装成功，然后跑通一个框架，跑通之后，用下面开源的办法转成onnx，实现pointpillars的部署。

https://github.com/SmallMunich/nutonomy_pointpillars

## 1.安装

### Requirements

All the codes are tested in the following environment:

+ Linux (tested on Ubuntu 14.04/16.04)
+ Python 3.6+
+ PyTorch 1.1 or higher (tested on PyTorch 1.1, 1,3, 1,5)
+ CUDA 9.0 or higher (PyTorch 1.3+ needs CUDA 9.2+)
+ [`spconv v1.0 (commit 8da6f96)`](https://github.com/traveller59/spconv/tree/8da6f967fb9a054d8870c3515b1b44eca2103634) or [`spconv v1.2`](https://github.com/traveller59/spconv)

**安装`spconv v1.2`**

1. Use `git clone xxx.git --recursive` to clone this repo.
2. Install boost headers to your system include path, you can use either `sudo apt-get install libboostall-dev` or download compressed files from boost official website and copy headers to include path.
3. Download cmake >= 3.13.2, then add cmake executables to PATH.
4. Ensure you have install pytorch 1.0 in your environment, run `python setup.py bdist_wheel` (don't use `python setup.py install`).（==pytorch>=1.3==)
5. Run `cd ./dist`, use pip to install generated whl file.

### Install `pcdet v0.3`

NOTE: Please re-install `pcdet v0.3` by running `python setup.py develop` even if you have already installed previous version.

a. Clone this repository.

```
git clone https://github.com/open-mmlab/OpenPCDet.git
```

b. Install the dependent libraries as follows:

+ Install the dependent python libraries:

```
pip install -r requirements.txt 
```

+ Install the SparseConv library, we use the implementation from

   

  `[spconv]`

  .

  + If you use PyTorch 1.1, then make sure you install the `spconv v1.0` with ([commit 8da6f96](https://github.com/traveller59/spconv/tree/8da6f967fb9a054d8870c3515b1b44eca2103634)) instead of the latest one.
  + If you use PyTorch 1.3+, then you need to install the `spconv v1.2`. As mentioned by the author of [`spconv`](https://github.com/traveller59/spconv), you need to use their docker if you use PyTorch 1.4+.

c. Install this `pcdet` library by running the following command:

```
python setup.py develop
```