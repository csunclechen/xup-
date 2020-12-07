#### 软硬配置

操作系统: ubuntu 18.04

python版本: 3.6.9

服务器A和B分别装有U280加速卡 每块卡都通过qsfp网线跟交换机相连

服务器A和服务器B都装有vitis和xrt的环境

#### 在两台服务器上都安装python-dask和pynq的环境(必须是pynq2.5.4版本)

```bash
sudo apt install python3-pip
python3 -m pip install dask[complete]
python3 -m pip install pynq==2.5.4
```

在主节点服务器A上安装jupyter notebook的环境

```bash
python3 -m pip install jupyter
```

在两台服务器A和B上都打开pynq-server

```bash
echo 'PATH="$HOME/.local/bin/:$PATH"' >>~/.bashrc
pynq-server start
```

#### 配置dask分布式集群

将服务器A作为dask主节点 服务器A和服务器B作为dask工作节点

服务器A：

```bash
dask-scheduler
```

![image-20201207194131855](https://github.com/csunclechen/xup_test/raw/main/png/image-20201207194131855.png)

记住scheduler的地址为tcp://192.168.1.7:8786

服务器A：

```bash
dask-worker tcp://192.168.1.7:8786 --name worker_A
```

服务器B：

```bash
dask-worker tcp://192.168.1.7:8786 --name worker_B
```

分布式集群配置完成，以后只需要操作主节点——服务器A

#### 在服务器A上下载并编译好benchmark kernel生成xclbin

```bash
git clone https://github.com/Xilinx/xup_vitis_network_example.git --recursive
```

#### 开始测试

```bash
jupyter notebook
```

打开[xup_vitis_network_example](https://github.com/Xilinx/xup_vitis_network_example)/**Notebooks**/vnx-benchmark-throughput-switch.ipynb

![image-20201207195341145](https://github.com/csunclechen/xup_test/raw/main/png/image-20201207195341145.png)

将Client的地址改成服务器A的dask scheduler的地址tcp://192.168.1.7:8786

![image-20201207195433275](https://github.com/csunclechen/xup_test/raw/main/png/image-20201207195433275.png)

将xclbin文件的位置和名称改成前面编译好的xclbin的位置和名称

#### 按照vnx-benchmark-throughput-switch中的注释开始跑cells，每一步的输出应该与注释的正确结果一致。

#### 测试结果

![image-20201207195948156](https://github.com/csunclechen/xup_test/raw/main/png/image-20201207195948156.png)

![image-20201207195959166](https://github.com/csunclechen/xup_test/raw/main/png/image-20201207195959166.png)