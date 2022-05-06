

## 介绍
这是一个用来生成音频数据的脚本。从数据库随机取出原始数据，通过随机化处理，生成不同的音频数据。你可以通过配置文件`wav_config.yaml`进行参数的更改。
## `wav_config.yaml`具体说明
* `data_path`为音频数据生成的地址，`all_file_length`**单位'h'**,`one_file_length`**单位's'**,两者为数据的**长度属性**，数据具体点数与**采样率**有关。
* `speech`，`noise`，`white`，`rev`分别是人声，噪声，白噪，混响。`path`是其原始数据的**地址**。
* `speech`的`filter`、`gain`、`set_file_gap`
  * `gain`、`filter`、`set_file_gap`：都可以通过`enable`属性进行启用或关闭，分别给数据加**增益**、**滤波器**、**静默区间**
  * `gain`、`filter`的`limit_down`、`limit_up`：分别是**增益**（单位：dB）和滤波器**截止频率**（单位：hz）的最高值和最低值，增益和截止频率会在此区间内随机产生数值。
  * `gain`的`mute_period`<br>
    `filter`的`bypass_period`：分别是增益、滤波器**静默的周期**，即增益、滤波器的**失效周期**。
  * `set_file_gap`的`time`、`period`
    * `time`：加给数据的静默区间的**长度**，**单位's'**
    * `period`：静默区间的**生效周期**。
* `noise`、`white`的`align_size`、`filter`、`gain`
  * `align_size`：使数据长度对齐其`value`值，**单位's'**
  * `filter`：同`speech`。
  * `gain`：
    * `limit_down`、`limit_up`：信噪比的最低值、最高值。
    * 其余属性同`speech`。
* `rev`的`reverber_time`、`bypass_period`：
  * `reverber_time`：混响的时间，**单位'ms'**。
  * `bypass_period`：静默周期。
  * 
## 需要安装的包：
`scipy`,`numpy`,`random`,`math`,`librosa`,`re`,`os`,`json`,`soundfile`,`tqdm`,`yaml`

## 操作
* 滤波器的数量不限，需要添加时，需在`wav_config.yaml`中原滤波器下方添加**六个属性**：`type`，`enable`，`limit_down`,`limit_up`,`filter_order`,`bypass_period`,并对属性进行赋值，以此法添加滤波器。
#### 操作举例
以给`speech`添加一个低通滤波器和一个高通滤波器举例。<br>
未添加滤波器的原代码:
```ruby  
speech:
    path:...
    gain:...  #dB
    set_file_gap:...
    filter:
        - type: param
          enable: True
          limit: 0.5
          filter_order: 2
          bypass_period: 32
          
        - type: pre-emphasis
          enable: False
          filter_order:
          bypass_period:

        - type: highpass
          enable: True
          limit_down: 1800  #hz
          limit_up: 1900   #hz
          filter_order: 4
          bypass_period: 4
```  
添加低通滤波器后的代码
```ruby  
speech:
    path:...
    gain:...  #dB
    set_file_gap:..
    filter:
        - type: param
          enable: True
          limit: 0.5
          filter_order: 2
          bypass_period: 32
          
        - type: pre-emphasis
          enable: False
          filter_order:
          bypass_period:

        - type: highpass
          enable: True
          limit_down: 1800  #hz
          limit_up: 1900   #hz
          filter_order: 4
          bypass_period: 4
          
        - type: lowpass
          enable: True
          limit_down: 7000  #hz
          limit_up: 7000   #hz
          filter_order: 4
          bypass_period: 4
          
        - type: highpass
          enable: True
          limit_down: 100  #hz
          limit_up: 100   #hz
          filter_order: 4
          bypass_period: 4  
``` 


