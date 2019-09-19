# useful_tools
code snippets from daily life.

1. 展示数据的分布图

```  python
import matplotlib.pyplot as plt
import seaborn as sns
sns.distplot(model_data_01.diff_2, kde=True) # kde控制是否显示核密度估计图
plt.show()
```

2. 数据持久化

```python
# 保存数据
with open('les_05.pickle', 'wb') as f:
    pickle.dump(les, f, -1)

# 恢复数据
with open(file_path, 'rb') as file:
    file_data = pickle.load(file)
```

3. 时间字符串转成`datetime`类型

```python
from datetime import datetime
date_dt = datetime.strptime('2018-11-01 00:00:00', '%Y-%m-%d %H:%M:%S')
```

4. 使用`tqdm`显示循环进度条

```python
from tqdm import tqdm
from time import sleep

for i in tqdm(range(10)):
    sleep(1)
```

5. manage files is using the "with" statement

```python
with open("hello.txt") as hello_file:
    for line in hello_file:
        print(line)
```

6. dataframe sample op / contruct dataset from DataFrame

```python
y_name="price"
# Shuffle the data
train_fraction=0.7
np.random.seed(seed)
# Split the data into train/test subsets.
x_train = data.sample(frac=train_fraction, random_state=seed)
x_test = data.drop(x_train.index)
# Extract the label from the features DataFrame.
y_train = x_train.pop(y_name)
y_test = x_test.pop(y_name)
```

7. tf_op / Create a slice Dataset from a pandas DataFrame and labels

```python
def make_dataset(batch_sz, x, y=None, shuffle=False, shuffle_buffer_size=1000):
    """Create a slice Dataset from a pandas DataFrame and labels"""

    def input_fn():
        if y is not None:
            dataset = tf.data.Dataset.from_tensor_slices((dict(x), y))
        else:
            dataset = tf.data.Dataset.from_tensor_slices(dict(x))
        if shuffle:
            dataset = dataset.shuffle(shuffle_buffer_size).batch(batch_sz).repeat()
        else:
            dataset = dataset.batch(batch_sz)
        return dataset.make_one_shot_iterator().get_next()

    return input_fn
```

8. DataFrame to train_x, train_y

```python
CSV_COLUMN_NAMES = ['SepalLength', 'SepalWidth',
                    'PetalLength', 'PetalWidth', 'Species']
train = pd.read_csv(train_path, names=CSV_COLUMN_NAMES, header=0)
train_x, train_y = train, train.pop(y_name)
```

9. Download data from URL use `tf.keras`

```python
TRAIN_URL = "http://download.tensorflow.org/data/iris_training.csv"
TEST_URL = "http://download.tensorflow.org/data/iris_test.csv"
def maybe_download():
    train_path = tf.keras.utils.get_file(TRAIN_URL.split('/')[-1], TRAIN_URL)
    test_path = tf.keras.utils.get_file(TEST_URL.split('/')[-1], TEST_URL)

    return train_path, test_path
```

10. 将本地代码包加入系统路径，避免本地代码找不到情况发生；

```python
import os
import sys

models_path = os.path.join(os.getcwd(), 'models')
sys.path.append(models_path)
```
11. 向`PYTHONPATH`中添加路径；

```python
import os
#export PYTHONPATH=${PYTHONPATH}:"$(pwd)/models"
#running from python you need to set the `os.environ` or the subprocess will not see the directory.

if "PYTHONPATH" in os.environ:
  os.environ['PYTHONPATH'] += os.pathsep +  models_path
else:
  os.environ['PYTHONPATH'] = models_path
```

12. Linux 中开启网易云音乐命令；

``` shell
nohup netease-cloud-music --no-sandbox %U &
```

13. python 中实现类似`switch`操作（通过字典dict方式解决）；

```python
priorities = {
        # t_3会因车辆优先级发生改变? 未解决
        '1': lambda w_: 0,
        '2': lambda w_: EPSILON_1,
        '3': lambda w_: EPSILON_1 + EPSILON_2,  # w_ - DELTA_1
        '4': lambda w_: max(w_ - helpers.convert_datetime_to_day(helpers.DATE_MATCH-car.date_collect), EPSILON_1),
        '5': lambda w_: max(w_ - helpers.convert_datetime_to_day(helpers.DATE_MATCH-car.date_collect), EPSILON_1) + EPSILON_2,
        '6': lambda w_: R,
        '7': lambda w_: R + EPSILON_1,
        '8': lambda w_: EPSILON_1 + EPSILON_2,  # w_ - DELTA_1
        '9': lambda w_: max(w_ - helpers.convert_datetime_to_day(helpers.DATE_MATCH-car.date_collect), EPSILON_1) + R,
        '10': lambda w_: max(w_ - helpers.convert_datetime_to_day(helpers.DATE_MATCH-car.date_collect), EPSILON_1) + R + EPSILON_2,
        '11': lambda w_: max(w_ - EPSILON_2, 0),
        '12': lambda w_: w_,
        '13': lambda w_: w_ + DELTA_2
    }
```

14. numpy 中的花式索引，选取某些索引之外的数据；

```python
orders_lists.append(list(orders_list[over_time]))
orders_lists.append(list(orders_list[~over_time]))
cost_matrix_list.append(cost_matrix[over_time, :])
cost_matrix_list.append(cost_matrix[~over_time, :])
```

15. 判断Series为空；

```python
a = pd.Series([])
a.empty
```

16. 忽略代码中的告警信息；

```python
import warnings
warnings.filterwarnings('ignore')
```

17. 指定显卡运行；

```python
CUDA_VISIBLE_DEVICES=1 python run.py
```

18. python 并发编程;

```python
# 顺序执行
start_time = time.time()
for item in number_list:
    print(evaluate_item(item))
print("Sequential execution in " + str(time.time() - start_time), "seconds")
# 线程池执行
start_time_1 = time.time()
with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    futures = [executor.submit(evaluate_item, item) for item in number_list]
    for future in concurrent.futures.as_completed(futures):
        print(future.result())
print ("Thread pool execution in " + str(time.time() - start_time_1), "seconds")
# 进程池
start_time_2 = time.time()
with concurrent.futures.ProcessPoolExecutor(max_workers=5) as executor:
    futures = [executor.submit(evaluate_item, item) for item in number_list]
    for future in concurrent.futures.as_completed(futures):
        print(future.result())
print ("Process pool execution in " + str(time.time() - start_time_2), "seconds")
```

19. 同步最新的`tensorflow/mdoels`;

``` shell
# 添加远端地址
git remote add upstream git@github.com:tensorflow/models.git
# 获取最新代码
git fetch upstream
# 合并最新代码
git merge upstream/master
# 提交
git push
```

20. Python文件前缀

```python
# coding=utf-8
import os
import sys

sys.path.append(os.path.abspath(os.path.join(os.path.dirname("__file__"), os.path.pardir, os.path.pardir)))
```

21. TensorFlow等深度学习框架，指定显卡运行

```python
import os
os.environ["CUDA_VISIBLE_DEVICES"] = "2"
```

22. 远程服务器Jupiter Notebook

```shell
ssh user_name@remote_ip -N -L localhost:local_port:localhost:remote_port
```

23. numpy去除1的维度

```python
np.squeeze() 
```

24. Pandas DataFrame多条件筛选

```python
raw_data[(raw_data.call_record_no == tmp_phone_call_no) & (raw_data.channel_id == 0)]
```

25. Numpy 多个ndarray合并

```python
np.append(ndarray1, ndarray2)
```

26. Jupyter notebook中pandas dataframe显示不全

```python
#显示所有列
pd.set_option('display.max_columns', None)
#显示所有行
pd.set_option('display.max_rows', None)
#设置value的显示长度为100，默认为50
pd.set_option('max_colwidth',100)
```

27. tar分包与合并

```shell
# 分包
tar czf - test.pdf | split -b 500m - test.tar.gz
# 合并
cat test.tar.gz* > test.tar.gz
```

28. conda源修改（清华源）

```shell
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

29. pip修改aliyun源

```shell
vim ~/.pip/pip.conf
# 追加
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```

30. Centos7 添加新用户

```shell
sudo useradd username
# 将新增用户添加进指定组
sudo usermod -a -G groupname username
# 强制用户在下次登录时修改密码
sudo passwd -e username
```

31. 将多维列表转为一维列表

```python
from itertools import chain
a = [[1, 2], [3]]
b = list(chain.from_iterable(a))  # [1, 2, 3]
```

32. keras中Lambda进行去除维度1

```python
x = Lambda(lambda x: x[:, 0])(x)  # shape (3, 1, 2) -> shape (3, 2)
```

33. 服务器开放/删除端口：

```shell
/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT  # 开放
/sbin/iptables -D INPUT 1  # 删除
/sbin/iptables -L -n  # 查看
```

34. Flask框架外网访问：

```python
app.run(host="0.0.0.0")
```

35. cURL服务器post交互：

```shell
curl -X POST -F "image=@dog.jpg" http://localhost:5000/predict
```

36. centos7安装操作Apache2

```
yum install httpd  # 安装
systemctl enable httpd.service  # 服务器开机自启
systemctl start httpd.service  # 手动启动
systemctl restart httpd.service  # 手动重启
systemctl stop httpd.service  # 手动停止
```

37. NLP任务中，char2indices/indices2char字典构建

```python
char2indices = dict((c, i) for i, c in enumerate(chars))
indices2char = dict((i, c) for i, c in enumerate(chars))
```

38. 使用numpy进行shuffle操作

```python
import numpy as np
indices = np.arange(len(y))
np.random.shuffle(indices)
x = x[indices]
y = y[indices]
```

39. Keras 中将y进行one-hot操作：

```python
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)
```

40. 实时看log文件：

```shell
tail -f bert.log
```

41. 查看某一目录下的存储使用详情：

```shell
sudo du -h -d 1 /home
```

42. conda在指定目录创建虚拟环境，并且所创建的虚拟环境不会出现在`conda info -e`中：

```shell
conda create -p ENV python=3.6 
# To activate this environment, use:
conda activate path_to_env
```

43. 使用conda安装指定版本的cudnn：

```shell
conda install cudnn=7.3 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/linux-64/
```

44. Python3读取json文件，bytes数据转dict

```python
JSON_PATH = 'data/intent_detect_01.json'
with open(JSON_PATH, 'r') as f:
    data = json.load(f)

origin_json = eval(str(data, encoding="utf-8"))
```

45. 遍历DataFrame中的每一行

```python
for _, tmp_row in makeup_labels.iterrows():
    print(tmp_row.AspectTerms)
    break
```

