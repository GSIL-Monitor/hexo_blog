[TOC]

#### 包管理

- 列出所有包 conda list
- 更新包 conda upgrade package_name，更新所有包 conda upgrade --all
- 安装包 （如numpy） conda install numpy，可同时安装多个包：conda install numpy scipy pandas，或限定版本conda install numpy=1.9
- 卸载包 conda remove package_name

#### 环境管理

- 创建环境：在终端中使用 conda create -n env_name list of packages
- 指定python版本：conda create -n py3 python=3
- 进入环境：activate env_name
- 退出环境：deactivate
- 导出环境文件：conda env export > environment.yaml
- 使用环境文件创建环境
    - conda env create -f environment.yaml
    - pip freeze > requirements.txt
    - pip install -r requirements.txt
- 列出所有环境：conda env list
- 移除环境：conda env remove -n env_name

    ```
    ```

#### 常用命令

```
pip install 包名
pip list
conda env list
```



#### 常用包
- 集合处理：collections
    - 计数：collections.Counter(list)
    - 队列&堆栈：collections.deque()
    - 有序字典：collections.OrderedDict(dict)
- 随机数：random
- 模式匹配：re、glob、string
    - 分割（split）
    - 替换（sub\replace）
    - 查询（findall、search）
    - str.encode()
    - bytes.decode()
- 加密解析
    - hashlib（md5）
    - pyparsing
    - fuzzywuzzy（模糊字符串匹配）
    - ftfy（整理unicode文本）
    - unidecode（unicode转ASCII）
    - nameparser（人名）
    - phonenumbers（电话号码）
    - pypinyin（拼音）
    - flanker（邮件）
- 时间：datetime、time、calendar
    - time.localtime(timestamp) 时间戳 -> 时间数组
    - time.mktime(timeArray) 时间数组->时间戳
    - time.strptime(strTime, "%Y-%m-%d %H:%M:%S") 字符串->时间数组
    - time.strftime("%Y%m%d-%H:%M:%S",arrTime) 时间数组 -> 格式化时间
- 文件操作
    - os
    - csv
    - shutil
    - pandas
    - xlrd
    - tablib（XLS、CSV、JSON、YAML）
    - rows（CSV、HTML、XLS、TXT）
- 打印结果：logging、string、pprint
- 网络请求：requests、urllib、pycurl、socket
- 序列化对象：pickle、json
    - pickle.dump(obj, file, protocol=0)
    - pickle.load(file)
    - strs = json.dumps(obj, skipkeys=True, sort_keys=True)
    - dicts= json.loads(encode_obj)
- 压缩文件
    - zipfile（.zip）
    - gzip（.gz）
    - tarfile（.tar）
    - zlib
        - zlib.compress(orginal)   # 压缩字符串
        - zlib.decompress(compressed)  # 解压缩
        - zlib.adler32(orginal) & 0xffffffff   # 校验和
        - zlib.crc32(orginal) & 0xffffffff    # 校验和
- 数据库：sqlite3、sqlalchemy、pyneo4j、psycopg2
- 微信：itchat
- 爬虫
    - scrapy
    - phantomjs
    - pyspider
    - grab
    - treq（异步）
    - selenium（动态）
    - cola（分布式）
    - user-agents
- web框架：django
- NLP
    - pyltp
    - jieba
    - nltk
    - foolnltk
    - gensim
    - fasttext
- 分布式：pyspark
- 绘图：matplotlib
- 深度学习：tensorflow、caffe、keras、mxnet、theano、pytorch
- 网页解析：beautifulsoup4、lxml、html2text、xmltodict、pyquery
- 机器学习：sklearn
- 科学计算：scipy
    - interpolate 插值
    - linalg 线性代数
    - optimize 数值优化
    - stats 统计分布
    - cluster 聚类算法
    - constants 物理数学常数
    - fftpack 傅里叶变换
    - integrate 积分和常微分方程求解
    - odr 正交距离回归
    - signal 信号处理
    - ndimage n维图像包
    - sparse 稀疏矩阵
    - spatial 空间数据结构和算法
    - special 特殊方程
    - io 输入输出
    - weave C/C++ 积分
- 向量表示：numpy
    - 索引过滤
    - 一元运算
    - 三角函数（sin、arccos、tanh、cos）
    - 数值计算（exp、log、sqrt、floor、ceil、abs）
    - 修改形状（flatten、resize、reshape、ravel）
    - 数据类型（astype）
    - 数组属性+方法
    - 二元运算
    - 四则运算（+、-、*、/、//、%、**）
    - 拼接
    - 比较和逻辑运算
    - 矩阵运算
    - 生成数组
    - 特殊数组
    - 随机数组
    - 结构化数组
    - 文件读写
    - 内存映射


#### pytorch
> 1、数据的加载和保存

```
model = torch.load(path)
torch.save(model, path)
model.load_state_dict(torch.load(path))
torch.save(model.state_dict(), path)
```
> 2、Tensors与numpy互转

```
a = torch.ones(5)  ->  b = a.numpy()
a = torch.from_numpy(b)  <-  b = np.ones(5)
a.add_(1)  ->  np.add(b, 1, out=b)
```

> 3、常规模型、参数及方法

- Module
    - 基本属性：_backend、_parameters、_buffers、_backward_hooks、_forward_hooks、
    - _forward_pre_hooks、_modules、training（训练True / 验证 False）
    - 待重写方法：forward、extra_repr、_apply、__setstate__、train、eval
- Embedding
    - num_embeddings：向量个数
    - embedding_dim：向量维度
    - padding_idx：行偏移，默认为None
    - max_norm：归一化处理，默认为None
    - norm_type：范数类型，默认为2-范数
    - scale_grad_by_freq：是否缩放梯度，默认为False
    - sparse：是否输出稀疏张量，默认为False
    - from_pretrained(embeddings, freeze, sparse)[类方法]
    - RNNBase[Inputs: input, h_0; Outputs: output, h_n]
    - mode：'LSTM' 、'GRU'、 'RNN_TANH'、'RNN_RELU'
    - input_size：输入向量‘x’的特征维度
    - hidden_size：隐藏层‘h’的神经元个数
    - num_layers：神经网络的层数，默认为1
    - bias:是否使用偏置，默认为True
    - batch_first：是否批量输入输出，默认为False
    - dropout：在每个输出上引入一个“Dropout”层，默认为0
    - bidirectional：是否为双向RNN
    - _flat_weights、all_weights
    - check_forward_args(input, hidden, batch_sizes)
    - reset_parameters()
    - flatten_parameters()

#### ftplib
##### ftp登陆连接

```
from ftplib import FTP            #加载ftp模块
ftp=FTP()                         #设置变量
ftp.set_debuglevel(2)             #打开调试级别2，显示详细信息
ftp.connect("IP","port")          #连接的ftp sever和端口
ftp.login("user","password")      #连接的用户名，密码
print ftp.getwelcome()            #打印出欢迎信息
ftp.cmd("xxx/xxx")                #进入远程目录
bufsize=1024                      #设置的缓冲区大小
filename="filename.txt"           #需要下载的文件
file_handle=open(filename,"wb").write #以写模式在本地打开文件
ftp.retrbinaly("RETR filename.txt",file_handle,bufsize) #接收服务器上文件并写入本地文件
ftp.set_debuglevel(0)             #关闭调试模式
ftp.quit()                        #退出ftp
```
 
##### ftp相关命令操作

```
ftp.cwd(pathname)                 #设置FTP当前操作的路径
ftp.dir()                         #显示目录下所有目录信息
ftp.nlst()                        #获取目录下的文件
ftp.mkd(pathname)                 #新建远程目录
ftp.pwd()                         #返回当前所在位置
ftp.rmd(dirname)                  #删除远程目录
ftp.delete(filename)              #删除远程文件
ftp.rename(fromname, toname)#将fromname修改名称为toname。
ftp.storbinaly("STOR filename.txt",file_handel,bufsize)  #上传目标文件
ftp.retrbinary("RETR filename.txt",file_handel,bufsize)  #下载FTP文件
```


#### boto3
- 提供了两个级别的接口来访问AWS服务
    - High Level的Resource接口
    - Low Level的Client接口
```
res = boto3.resource('service name')	# 获取API对象，例如s3、ec2
client = boto3.client('service name')
```

#### os
```
def commandExecute(commandLine):
    run_sts = os.system(commandLine)
    if run_sts != 0:
       	print("执行失败:%s" % commandLine, 'ERROR')
        exit(1)
```


### 异常处理

```
安装包时报错：error: command 'clang++' failed with exit status 1
解决办法(有效)： 
 $ git clone https://github.com/HIT-SCIR/pyltp
 $ git submodule init
 $ git submodule update
 $ python setup.py install 
 # Mac系统出现版本问题，使用 MACOSX_DEPLOYMENT_TARGET=10.7 python setup.py install
```


```
FutureWarning: Conversion of the second argument of issubdtype from `float` to `np.floating` is deprecated. In future, it will be treated as `np.float64 == np.dtype(float).type`.
解决办法(有效)：pip install h5py==2.8.0rc1
```


