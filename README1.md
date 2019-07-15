# 决策树分类工具

-------

## 目的

本项目利用决策树模型进行自动分类模型的构建。特点是速度快、准确度高（过拟合)迭代升级方便。目前分析下来是用户体验最好的模型选择。

## 依赖

+ numpy
+ pandas
+ sklearn
+ collections
+ jieba
+ pickle

## 安装&使用

` python setup.py install`

```python
 from xenDTClassifier import XenDTModel
 m = XenDTModel( data_file_path='data',model_save_path='model')
# data_file_path 为建模训练数据放置位置，model_save_path 为模型保存位置
 s = m.DTCreateAPI(js1) #创建模型
 s = m.DTClassifierAPI(js) # 预测分类
```



## API功能

POST json

+ **/decisiontree/create**  根据训练数据建立决策树模型

  + 请求json样例 {"uniqueId":"test","saveTag":0,"featureSelect":1}  uniqueId数据json文件的文件名，saveTag是否保存模型文件，默认不保存。CFA如使用一般需要显式通知保存。featureSelect是否适用特征筛选功能，使用该功能会稍微降低建模速度但提高模型鲁棒性，默认使用。
  + 数据json文件样例。test.json  内容和后台对账单查询api输出保持一致

  {"searchBankStatementList":[{"accountMaskNo":"10001","accountingDate":"2017-03-01","accountingDateId":20170301,"attachments":0,"balanceAmount":808.44,"bank":"CASH","bankComments":"","bankStatementId":51310523,"cashType":"","crAmount":0,"currency":"CNY","customerAccountMaskNo":"","customerId":0,"customerName":"","customerReference":"","downPaymentFlag":0,"drAmount":1073,"internalFlag":0,"leId":0,"leName":"焙滋","transAmount":1073,"transFlag":"P","transTypeId":2787,"transTypeName":"其它","trxDate":"2017-03-01 00:00:00","userMemo":"现金解行 记-0002 "},{"accountMaskNo":"1001207409206999161","accountingDate":"2017-0301","accountingDateId":20170301,"attachments":0,"balanceAmount":121507.49,"bank":"ICBC","bankComments":"","bankStatementId":51293664,"cashType":"","crAmount":1402.58,"currency":"CNY","customerAccountMaskNo":"","customerId":0,"customerName":"","customerReference":"","downPaymentFlag":0,"drAmount":0,"internalFlag":0,"leId":0,"leName":"上海博滋餐饮有限司","transAmount":1402.58,"transFlag":"R","transTypeId":2791,"transTypeName":"其它","trxDate":"2017-03-01 00:00:00","userMemo":"收到2.28微信 "}]}

  + 返回样例 {'error':0,'msg':'model saved','dotString':''}  dotString 中字符串为决策树可视化信息，使用graphviz标准解析可复现。建立好的模型会保存在服务器端供后续预测调用。

+ **/decisiontree/predict** 利用已有模型预测对账单条目类别

  + 请求json样例 {"uniqueId":"test","searchBankStatementList":"*对账单标准输出*"}  uniqueId为企业模型的名称，searchBankStatementList要求预测类别的对账单条目组
  + 返回数据样例 {'mappings': [{'groupName': '2787:其它', 'groupId': '2787', 'bankStatementId': 51310523}, {'groupName': '2791:其它', 'groupId': '2791', 'bankStatementId': 51293664}], 'error':0, 'msg':'OK'}

## API性能说明

建模API 

| 数据量  | 总时间 | 分词耗时 | 模型大小 |
| :-----: | :----: | :------: | :------: |
|  800条  |  1.8s  |   0.5s   |   23kb   |
| 52000条 |  35s   |   18s    |  540kb   |



预测API

| 数据量 | 总时间 |
| :----: | :----: |
|  800   | 0.08s  |
| 52000  |  0.2s  |



## 测试

python3.5+ 环境下

```python
 import xenDTClassifier
 xenDTClassifier.test() #应该有正确输出
```

