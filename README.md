# 源码优化(Python2.7 >>> Python3.10)

## 源码参数配置

对Github中```https://github.com/Aayush-Ankit/puma-simulator```的仿真器和```https://github.com/illinois-impact/puma-compiler```的编译器的源码进行参数配置

根据```https://github.com/Aayush-Ankit/puma-simulator```的```how_to_run.md```进行复现

### 1. ./puma-compiler/src/common.h参数配置

```common.h```中line 16```N_CONSTANT_MVMUS_PER_CORE```要与```./puma-simulator/include/config.py```中```num_matrix```参数保持一致

```common.h```中line 17```N_TRAINING_MVMUS_PER_CORE```参数改为0

### 2. ./puma-simulator/include/config.py参数配置

```config.py```中line 115```num_tile_compute```参数要与模型路径```./puma-simulator/test/testasm/<model_name>/```下的tile数保持一致，这里修改为70


## 源码修改部分

Linux系统与Python版本

| Requirement | Version                    |
| ----------- | -------------------------- |
| Ubuntu      | 22.04.3 LTS                |
| Python      | 3.10.14                    |

### 1. ./puma-simulator/src/tile_modules.py: line 38

原始代码
```
temp_rb_list = [''] * cfg.receive_buffer_width
```

运行后报错：TypeError: can't multiply sequence by non-int of type 'float'

print```cfg.receive_buffer_width```的值为16.0，因此猜测Python3.X版本不支持浮点数与List相乘

将源码改为
```
temp_rb_list = [''] * int(cfg.receive_buffer_width)
```
报错消除

### 2. ./puma-simulator/src/dnn_wt_p.py: line 22

原始代码
```
for l in range(cfg.phy2log_ratio):
```

运行后报错：TypeError: 'float' object cannot be interpreted as an integer

print```cfg.phy2log_ratio```的值为8.0，因此猜测Python3.X版本不支持浮点数作为```range()```函数的参数

将源码改为
```
for l in range(int(cfg.phy2log_ratio)):
```
报错消除
