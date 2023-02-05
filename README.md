# NL2SQL项目运行说明
## 环境需求
linux GPU服务器，CUDA>=10.2  
安装过docker和nvidia-docker
## 运行步骤
1. 从百度网盘下载docker镜像压缩包`nl2sql_test.tar`和测试集文件样例`test.json`。  
```
https://pan.baidu.com/s/1DzMEAfUzjqQZVs5FzjpPUA?pwd=z8ig
```
2. 将`nl2sql_test.tar`和`test.json`上传至本地服务器。
3. 进入`nl2sql_test.tar`所在目录，在终端执行以下命令。 
```docker
#导出镜像
docker load --input nl2sql_test.tar
```  
```docker
#创建容器，容器名nl2sql_container，对应镜像名nl2sql，版本1.0
docker run -it --gpus all --name nl2sql_container nl2sql:1.0
```  
```docker
#退出但不关闭容器
ctrl+p+q
```  
```docker
#将test.json拷贝到容器中，其中xxx为test.json所在的本地目录
docker cp xxx/test.json nl2sql_container:/home/nl2sql/pymodel/input
```  
```docker
#进入容器并执行命令
docker exec -it nl2sql_container /bin/bash
```  
以下命令在容器内部执行：  
```python
#添加环境变量
export PATH=/root/anaconda3/bin:$PATH
```  
```python
#激活python环境
source activate nl2sql_base
```  
```python
#进入项目文件夹
cd home/nl2sql/pymodel
```  
```python
#运行项目
python model.py
```
## 运行结果
如果程序运行成功，控制台输出测试集预测结果，示例如下：  
```python
qid000001	select dcline_basic.name , commonsubstation_basic.top_voltage_type , dcline_statistic_power.average from dcline_basic join commonsubstation_basic on commonsubstation_basic.id = dcline_basic.id join dcline_statistic_power on commonsubstation_basic.id = dcline_statistic_power.id where dcline_basic.voltage_type like '%320%'

qid000002	select count ( * ) from powergrid_basic where powergrid_basic.level = '国家级'

qid000003	select busbar_basic.name , busbar_basic.operate_date from busbar_basic where busbar_basic.model = 'ldre-φ250/230'

qid000004	select count ( * ) from powertransformer_basic where powertransformer_basic.structural_style = '壳式'

qid000005	select hvdcsys_basic.name from hvdcsys_basic join powergrid_basic on powergrid_basic.id = hvdcsys_basic.id where powergrid_basic.name = '华北电网'

……

qid000212	select acline_basic.name from acline_basic join commonsubstation_basic on acline_basic.id = commonsubstation_basic.id where commonsubstation_basic.plant_station_type like '%火电%' and acline_basic.length > 300 and acline_basic.operate_date like '%2018%'

``` 
