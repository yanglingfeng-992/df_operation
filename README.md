# df_operation
some normal and useful skills about pandas.dataframe




----------------------（一）创建 ----------------------


【场景一】读取本地文件（csv、excel等）

import pandas as pd 

file_add = '/Users/yanglingfeng/Desktop/Python/insurance_order_data_demo.csv' 
df = pd.read_csv(file_add)  # ,skiprows=1 跳过第一行读取数据



【场景二】自定义df

import pandas as pd 
import numpy  as np 

df1 = pd.DataFrame({
     'sso_id':      ['659222','4051113','4052710',
                     '4052710','4052711','4778026'],
     'total_prem':  [256,120,150,95,65,1024],
     'ec_order_no': ['1711140795304501','1711240071974309',
                     '1805078210218834','1805078980084775',
                     '1712151887963096','1906151887965077'],
     'product_no':  ['AC_ABCX_0001','AC_ABCX_0002',
                     'AD_ADBX_0001','AL_ALCX_0007',
                     'AL_ALCX_0011','FX_FXLH_0003'],
     'is_long':     [1,0,0,0,1,1],
     'sale_channel':[101,102,101,193,201,101],
     'currentdate': ['2017-11-14','2017-11-24','2018-05-07',
                    '2018-05-07','2017-12-15','2019-06-15']   }) 

df2 = pd.DataFrame({'A': 1.,
                    'B': pd.Timestamp('2018-08-01'),
                    'C': pd.Series(1, index=list(range(4)), dtype='float32'),
                    'D': np.array([3] * 4, dtype='int32'),
                    'E': pd.Categorical(["test", "train", "test", "train"]),
                    'F': 'foo',
                    'G': ['apple','orange','banana','lemon']})



【场景三】以递增时间为索引的df 

import pandas as pd
import numpy  as np 

date_range = pd.date_range('2018-01-01','2018-01-10') # 等价于 date_range = pd.date_range('2018-01-01', periods=10)
df = pd.DataFrame(data=np.random.randint(low=1,high=50,size=40).reshape(10,4)
                 ,index=date_range
                 ,columns=['col1','col2','col3','col4'])



【场景四】读取数据库 

（一）MySQL 数据导入 形成 DataFrame

import pymysql 
import pandas as pd 

dbconn = pymysql.connect(host="192.168.**.***",user="******",passwd="******",db="edw") 
sql = "select * from myhexun"
df = pd.read_sql(sql, dbconn)    # 返回数据框
dbconn.close()


（二）PostgreSQL 数据导入 形成 DataFrame

import psycopg2
import pandas as pd 

def pg_query(sql):
    ## 连接到一个给定的数据库
    conn = psycopg2.connect(database="edw", 
                            user="******", 
                            password="******", 
                            host="192.168.**.***", 
                            port="5432")
    df = pd.read_sql(sql, con=conn)
    ## 关闭数据库连接
    conn.close()
    return df

sql = "select sso_id, real_name, phone_reg, company_name \
       from edwuser1.e_bxr_users limit 10 "

df = pg_query(sql)


(三) PostgreSQL 数据导入 形成 列表

import psycopg2

conn = psycopg2.connect(database="edw", 
                        user="******", 
                        password="******", 
                        host="192.168.**.***", 
                        port="5432")

sql = "select sso_id, real_name, phone_reg, company_name \
       from edwuser1.e_bxr_users limit 5 " 

cur = conn.cursor() # 建立游标
cur.execute(sql) 
rows_list = cur.fetchall() # list对象
print(rows_list) 
# [ (4857344, '王琳琳', '15937902912', '中荷人寿')
# , (4851249, '何鑫',  '15173942014', '泰康人寿')
# , (4845127, '唐心悦', '18109013148', '泰康人寿')
# , (4845131, '阿木木', '13750559191', '太平洋人寿')
# , (4929596, '随缘',  '13755577486', '泰康人寿')]
conn.close() # 关闭游标 




----------------------（二）查看 ----------------------

测试数据：
     sso_id  real_name              register_time   company_name  level_id      ec_order_no   currentdate             pay_time   total_prem  class_name 
0   4748179     刘鹏华     2018/06/19 17:32:16.308      太平洋人寿       0    1901152832941771   2019-01-15  2019/01/15 16:57:26       175        住院宝
1   4345024     章弋飏     2017/11/07 11:15:53.054        泰康人寿       3    1901021834130694   2019-01-02  2019/01/02 12:56:43       324      一年期重疾 
2   3805525     石增芹     2017/03/26 08:02:17.518        泰康人寿       2    1901101526338562   2019-01-10  2019/01/10 20:30:32       249        住院宝 
3   3842371     黄王平      2017/04/14 08:15:04.01        泰康人寿       2    1901132274143970   2019-01-13  2019/01/13 15:26:27       190        意外险 
4   3650007     罗树棉     2017/01/03 05:39:42.782        平安人寿       4    1901282656622529   2019-01-28                  NaN       140        意外险 
5   3812870     周锦丽      2017/03/29 09:41:27.63        泰康人寿       3    1901051983920326   2019-01-05  2019/01/05 19:42:41       249        住院宝
6   3331315     陈夏莲       2016/05/23 14:15:42.5        中国人寿       1    1901172062835595   2019-01-17  2019/01/17 13:32:07       249        住院宝 
7   4410387     王仁贵     2017/12/01 11:47:05.049        人保寿险       2    1901202549339279   2019-01-20  2019/01/20 13:28:32        65        意外险  
8   3241336     孙雪梅     2016/02/23 13:39:50.591       太平洋人寿      4    1901172363886492   2019-01-17  2019/01/17 09:38:48       190        意外险  
9   3144945     李晓璐     2015/12/16 21:22:22.005        泰康人寿       3    1901051087004244   2019-01-05  2019/01/05 14:57:46       249        住院宝 
10   972955     农尚国  2014/11/13 14:15:07.305075        中国人寿       2    1901172377608859   2019-01-17  2019/01/17 20:03:33       249        住院宝  
11  2018497     刘希东     2015/02/12 00:16:51.087        泰康人寿       3    1901202441693070   2019-01-20  2019/01/20 22:20:17       249        住院宝 
12  4645226     刘亚婷     2018/04/23 16:55:50.976        泰康人寿       2    1901212050815501   2019-01-21  2019/01/21 20:49:20       374        住院宝  
13  3396095   保险顾问     2016/07/26 12:50:31.083        友邦保险       4    1901142412883480   2019-01-14  2019/01/14 09:49:27       144        意外险  
14   669033     黄俊评         2013/05/13 08:22:14        其他公司       2    1901021508773539   2019-01-02  2019/01/02 15:50:05      1170        意外险 


数据信息：

df.index         # 查看索引
df.shape         # 形状
df.head()        # 查看头部数据，默认5条
df.head(10)      # 查看头部前10条数据
df.tail()        # 查看尾部数据，默认5条
df.tail(10)      # 查看尾部前10条数据
df.columns       # 查看字段名
df.columns[1]    # 查看第二列字段名(支持索引、切片)
df.dtypes        # 查看每一列的数据类型
df.describe()    # 描述
df.info()        # 信息
df.T             # 转置


----------------------（三）选择 ----------------------


# 单列/单行查看与赋值
df['real_name']            # 查看real_name列
df['real_name'] = '花花'    # 列赋值（覆盖）
df['new_col']   = '草草'    # 列赋值（新增）
df.real_name               # 查看real_name列 
df.real_name = '花花'       # 列赋值（覆盖）
df[df.columns[0:2]]        # 选择前2列
df[['sso_id','real_name']] # 选择指定列
df['sso_id':'real_name']   # 报错
df['sso_id','real_name']   # 报错
df[('sso_id','real_name')] # 报错 

# 返回类型
ser = df['real_name']     # type(ser)  pandas.core.series.Series
daf = df[['real_name']]   # type(daf)  pandas.core.frame.DataFrame

# 按位置
df.iloc[0]                # 查看第一行（series）
df.iloc[0:1]              # 查看第一行（DataFrame）左闭右开，返回第一行
df.iloc[:, 1:3]           # 返回所有行、索引值为1到2位的列数据（即第2，3列）
df.iloc[1:3,[1,2]]        # 查看第2-3行第2-3列

# 按索引标签值
df.loc[0]                 # 按行标签值取一行（series）或 df.loc[0,:] 建议此种写法
df.loc[0:1]               # 左闭右闭（返回第一、第二行）
df.loc[:,['real_name','level_id']]     # 返回所有行、real_name和level_id列

# 按元素坐标
df.iat[0,0]             # 按坐标访问元素内容，第1行第1列对应的元素
df.iat[1,1]             # 按坐标访问元素内容，第2行第2列对应的元素
df.iat[1,'level_id']    # 报错
df.at[1,'level_id']     # 选取level_id列第一行

#关于iloc,loc的区别：
#    loc获取索引中具有特定标签的行或列，即具体的「索引值」，索引值必须同类型，不可混合
#    iloc获取索引中特定位置的行（或列），即「行数或列数」



----------------------（四）排序 ----------------------

# 索引排序
df.sort_index(axis=0,ascending=False)             # axis=0 按行索引排序，降序
df.sort_index(axis=1,ascending=True)              # axis=1 按列字段名（字母、字符串顺序）排序，升序
# 值排序
df.sort_values('currentdate',ascending=False)     # 按currentdate的值倒叙排序
df.sort_values(['sso_id','total_prem'], ascending=[False,True])  # 按sso_id降序，total_prem升序


----------------------（五）过滤 ----------------------

# where
df1 = df['is_long'] == 1
df2 = df['total_prem'] > 100 
df2 = df.query('total_prem > 100')  # 另一种写法
# and 
df3 = (df['is_long'] == 0) & (df['total_prem'] > 200) 
# or 
df4 = (df['is_long'] == 0) | (df['total_prem'] > 200) 
# in 
df5 = df['level_id'].isin([2,4])
# not in 
df6 = -df['level_id'].isin([2,4])
# like 
df7 = df.company_name.str.contains('平安',na=False)  # company_name包含平安，空值剔除；若数据中包含空值而不加na=False参数，会报错
# not like 
df8 = df[df7==0]
# is null  
df9 = df[df['company_name'].isnull().values]    # 例如：找出company_name为NaN值的记录-2条，建议多加个values
df['company_name'].isnull().sum()               # 返回 2 注意使用sum()
df['company_name'].isnull().count()             # 返回 100 总记录数
# is not null 
df10 = df[df['company_name'].notnull().values]  # 例如：找出company_name为NaN值的记录-2条，建议多加个values
df['company_name'].notnull().sum()              # 返回 98 注意使用sum()
df['company_name'].notnull().count()            # 返回 100 总记录数
# between 
df[df['total_prem'].between(200,500)]  



----------------------（六）去重 ----------------------

series.unique()方法
df['level_id'].unique()           # array([1, 2, 4, 3, 0])
df['level_id'].unique().shape[0]  # 5 
dataframe.drop_duplicates()方法
# subset，为选定的列做distinct，默认为所有列；
# keep，值选项{'first', 'last', False}，保留重复元素中的第一个、最后一个，或全部删除；
# inplace ，默认为False，返回一个新的dataframe；若为True，则返回去重后的原dataframe

示例:
df2 = pd.DataFrame({'sso_id':['111','222','111','111'] 
                   ,'level_id':[1, 2, 1, 3]
                   ,'money':[100, 500, 100, 100]})

#   sso_id  level_id  money
# 0    111         1    100
# 1    222         2    500
# 2    111         1    100
# 3    111         3    100


【一】 实现：select distinct * from table

方法1：查找重复值，设为False
df2[df2.duplicated()==False]
  sso_id  level_id  money
# 0    111         1    100
# 1    222         2    500
# 3    111         3    100

方法2: 剔除重复值
df2.drop_duplicates(keep='first') # keep默认first
#   sso_id  level_id  money
# 0    111         1    100
# 1    222         2    500
# 3    111         3    100


【二】实现：select distinct sso_id, level_id from table 

df2.drop_duplicates(subset=['sso_id','level_id'],keep='first')[['sso_id','level_id']]
#   sso_id  level_id
# 0    111         1
# 1    222         2
# 3    111         3

【三】实现：select distinct sso_id from table 

df2['sso_id'].unique()
# array(['111', '222'], dtype=object)



----------------------（七）分组、聚合 ----------------------

# groupby 一般会配合聚合函数;
# 从实现上看，groupby返回的是一个DataFrameGroupBy结构，这个结构必须调用聚合函数（如sum）之后，才会得到结构为Series的数据结果

type(df.groupby('level_id'))     
# <pandas.core.groupby.generic.DataFrameGroupBy object at 0x7fddc59686d0>

d1 = df.groupby('sso_id').count()
d2 = df.groupby('sso_id').size()


【一】计数： count(), count(distinct), sum()

1. 分组计数
df1 = df.groupby('level_id').size()                               # select level_id, count(1) pv from t group by 1
df2 = df.groupby('level_id').agg({'sso_id':pd.Series.nunique})    # select level_id, count(distinct sso_id) uv from t group by 1
df3 = df.groupby('level_id')['total_prem'].sum()                  # select level_id, sum(total_prem) from t group by 1 
df4 = df.groupby(['level_id','sso_id'])['total_prem'].sum()       # select level_id, sso_id, sum(total_prem) from t group by 1,2 

2. 不分组计数
df1 = df.shape[0]                           # select count(*) from t
df2 = df.dropna().shape[0]                  # select count(*) from t  # 空值剔除（某记录其中一个字段为空则该记录删除）
df3 = df.agg({'sso_id':pd.Series.nunique})  # select count(distinct sso_id) from t 
df4 = df.sso_id.nunique().shape[0]          # select count(distinct sso_id) from t 
df5 = df.level_id.nunique()                 # select distinct level_id from t 


【二】其他聚合：sum()、max()、min()、mean()

func = lambda x: x.unique().shape[0]
func.__name__ = 'countd'
aggregation = df.agg({'total_prem': ['sum','max','min','mean'], 
                          'sso_id': ['count',func] })
print(aggregation)



--------------------（八）标签 case when ----------------------

【方法一】：np.select() 

df = pd.DataFrame({'name':['成龙','柠檬','土豆'],'age':[18,28,52]})
    
case_when = [(df['age'].lt(20)),
             (df['age'].gt(20) & df['age'].lt(50)),
             (df['age'].gt(50))] 

then = ['yonng','mature','old']

df['elderly'] = np.select(case_when,then)
#     name  age  elderly
# 0   成龙   18    yonng
# 1   柠檬   28   mature
# 2   土豆   52      old



【方法二】：分类法，数据的categoricals类型

df = pd.DataFrame({'id':[1,2,3,4,5], 'row_grade':['A','A','B','C','A']})
df['grade'] = df['row_grade'].astype('category') 
df['grade'].cat.categories = ["very good", "good", "very bad"]

print(df)
print(df.info())

#    id  row_grade     grade
# 0   1         A  very good
# 1   2         A  very good
# 2   3         B       good
# 3   4         C   very bad
# 4   5         A  very good

#  #   Column     Non-Null Count  Dtype   
# ---  ------     --------------  -----   
#  0   id         5 non-null      int64   
#  1   row_grade  5 non-null      object  
#  2   grade      5 non-null      category



----------------------（九）关联、合并 ----------------------

【注意】 pandas可以实现 a left join b on a.key1=b.key1（and a.key2=b.key2）
        pandas无法实现 a left join b on 其他关联条件

df1 = pd.DataFrame({'sso_id':[111,222,333,444,555]
                   ,'level_id':[0,1,2,3,4]
                   ,'total_prem':[0,235,1500,4050,16600]})

df2 = pd.DataFrame({'sso_id':[111,222,333,444,555]
                   ,'level_id':[5,1,2,3,4]
                   ,'company_name':['泰康','平安','太平洋','新华','太平']})

#    sso_id  level_id  total_prem
# 0     111         0           0
# 1     222         1         235
# 2     333         2        1500
# 3     444         3        4050
# 4     555         4       16600

#    sso_id  level_id  company_name
# 0     111         5          泰康
# 1     222         1          平安
# 2     333         2          太平洋
# 3     444         3          新华
# 4     555         4          太平


merge()    ---> (相当于join， 左右拼接)
concat()   ---> (相当于union，上下拼接)


### left join （单个on条件）

df1.merge(df2,how='left',on='sso_id') # 或 pd.merge(df1, df2, how='left', on='sso_id')

#    sso_id  level_id_x  total_prem  level_id_y company_name
# 0     111           0           0           5          泰康
# 1     222           1         235           1          平安
# 2     333           2        1500           2          太平洋
# 3     444           3        4050           3          新华
# 4     555           4       16600           4          太平


### left join （多个on条件）

df1.merge(df2,how='left', on=['sso_id','level_id']) # 或 pd.merge(df1, df2, how='left', on=['sso_id','level_id'])
#    sso_id  level_id  total_prem company_name
# 0     111         0           0          NaN
# 1     222         1         235          平安
# 2     333         2        1500          太平洋
# 3     444         3        4050          新华
# 4     555         4       16600          太平


### union all 
pd.concat([df1, df2]) 
#    sso_id  level_id  total_prem  company_name
# 0     111       0.0         0.0          NaN
# 1     222       1.0       235.0          NaN
# 2     333       2.0      1500.0          NaN
# 3     444       3.0      4050.0          NaN
# 4     555       4.0     16600.0          NaN
# 0     111       NaN         NaN          泰康
# 1     222       NaN         NaN          平安
# 2     333       NaN         NaN          太平洋
# 3     444       NaN         NaN          新华
# 4     555       NaN         NaN          太平

### 左右拼接
pd.concat([df1, df2], axis=1) 
#    sso_id  level_id  total_prem  sso_id company_name
# 0     111         0           0     111          泰康
# 1     222         1         235     222          平安
# 2     333         2        1500     333         太平洋
# 3     444         3        4050     444          新华
# 4     555         4       16600     555          太平


### 多个df关联
import pandas as pd
from functools import reduce

dfs = [df0, df1, df2, dfN]
df_final = reduce(lambda left,right: pd.merge(left,right,on='name'), dfs)





--------------------（十）缺失值处理 ----------------------


df = pd.DataFrame({'sso_id':['4778026','4051113','4052715',np.NaN,'56213544'],
                   'total_prem':[100,95,30,226,568],
                   'ec_order_no':[np.NaN,'1805078210218834','1805078980084775','1712151887963096','1906151887965077'],
                   'is_long':[1,0,0,0,1]})
# 舍弃
df.dropna()                                  # 默认how='any',删除所有包含NaN的记录
df.dropna(subset=['sso_id','ec_order_no'])   # 通过subset参数删除sso_id和ec_order_no列含空值的记录
# 填充
df.fillna(value='x')                         # 以x填充所有的空值
#        sso_id  total_prem       ec_order_no  is_long
#   0   4778026         100                 x        1
#   1   4051113          95  1805078210218834        0
#   2   4052715          30  1805078980084775        0
#   3         x         226  1712151887963096        0
#   4  56213544         568  1906151887965077        1

df.fillna({'sso_id':'xxx'})                 # 通过字典填充不同的常数：
#        sso_id  total_prem       ec_order_no  is_long
#   0   4778026         100               NaN        1
#   1   4051113          95  1805078210218834        0
#   2   4052715          30  1805078980084775        0
#   3       xxx         226  1712151887963096        0
#   4  56213544         568  1906151887965077        1

df.fillna(df.mean()['total_prem'])           # 以total_prem的均值填充所有的空值，sum(),max()等也可
#        sso_id  total_prem       ec_order_no  is_long
#   0   4778026         100             203.8        1
#   1   4051113          95  1805078210218834        0
#   2   4052715          30  1805078980084775        0
#   3     203.8         226  1712151887963096        0
#   4  56213544         568  1906151887965077        1

df.fillna(method='bfill')                    # 以同列的下一个值填充
#        sso_id  total_prem       ec_order_no  is_long
#   0   4778026         100  1805078210218834        1
#   1   4051113          95  1805078210218834        0
#   2   4052715          30  1805078980084775        0
#   3  56213544         226  1712151887963096        0
#   4  56213544         568  1906151887965077        1

df.fillna(method='ffill')                   # 以同列的上一个值填充
#        sso_id  total_prem       ec_order_no  is_long
#   0   4778026         100               NaN        1
#   1   4051113          95  1805078210218834        0
#   2   4052715          30  1805078980084775        0
#   3   4052715         226  1712151887963096        0
#   4  56213544         568  1906151887965077        1

df.fillna(method='bfill',axis=1)           # 以同行的右一个值填充
df.fillna(method='ffill',axis=1)           # 以同行的左一个值填充

# 替换 replace() 
df.replace(np.nan, 0)

# 通过sklearn的数据预处理方法对数据进行处理
from sklearn.preprocessing import Imputer 

nan_model = Imputer(missing_values="NaN",strategy="mean",axis=0) 
# 这是一个模型，模型算法是以整个df均值填充缺失值（median,most_frequent,可以以中位数、众数替代）
nan_result = nan_model.fit_transform(df)  # 应用模型规则
print(nan_result)



--------------------（十一）增、删 ----------------------

【一】增加、追加 （append）

df1 = pd.DataFrame({'sso_id':[111,222,333],'gender':[1,0,1]})
df2 = pd.DataFrame({'sso_id':[444,555,666],'gender':[0,0,1]})

DF = df1.append(df2, ignore_index=True) 

   sso_id  gender
0     111       1
1     222       0
2     333       1
3     444       0
4     555       0
5     666       1

【二】删除 （drop）
data = {'证券名称': ['格力电器', '视觉中国', '成都银行', '中国联通', '格力电器', '视觉中国', '成都银行', '中国联通'],
        '摘要':    ['证券买入', '证券买入', '证券买入', '证券买入', '证券卖出', '证券卖出', '证券卖出', '证券卖出'],
        '成交数量': [500, 1000, 1500, 2000, 500, 500, 1000, 1500],
        '成交金额': [-5000, -10000, -15000, -20000, 5500, 5500, 11000, 15000] }
 
df = pd.DataFrame(data)

#    证券名称    摘要  成交数量  成交金额
# 0  格力电器  证券买入   500    -5000
# 1  视觉中国  证券买入  1000   -10000
# 2  成都银行  证券买入  1500   -15000
# 3  中国联通  证券买入  2000   -20000
# 4  格力电器  证券卖出   500     5500
# 5  视觉中国  证券卖出   500     5500
# 6  成都银行  证券卖出  1000    11000
# 7  中国联通  证券卖出  1500    15000

df.drop(index=[0,7])           # 删除第1和第8行
df.drop(index=range(len(df)))  # 清空数据
df.drop(columns=['成交数量'] )  # 删除列


--------------------（十二）数据透视表 ----------------------

df = pd.DataFrame({'col1':['one','one','two','three']*3,
                   'col2':['A','B','C']*4,
                   'col3':['foo','foo','foo','bar','bar','bar']*2,
                   'col4': np.random.randint(0,10,12),
                   'col5': np.random.randint(11,30,12)})

#      col1 col2 col3  col4  col5
# 0     one    A  foo     7    22
# 1     one    B  foo     9    22
# 2     two    C  foo     5    25
# 3   three    A  bar     4    15
# 4     one    B  bar     3    20
# 5     one    C  bar     6    11
# 6     two    A  foo     6    20
# 7   three    B  foo     4    15
# 8     one    C  foo     4    29
# 9     one    A  bar     0    28
# 10    two    B  bar     0    17
# 11  three    C  bar     1    19

df_new = df.pivot_table(values=['col4','col5'],index=['col1','col2'],columns='col3')

               # col4      col5
# col3        bar  foo   bar   foo
# col1  col2                      
# one    A     0.0  7.0  28.0  22.0
#        B     3.0  9.0  20.0  22.0
#        C     6.0  4.0  11.0  29.0
# three  A     4.0  NaN  15.0   NaN
#        B     NaN  4.0   NaN  15.0
#        C     1.0  NaN  19.0   NaN
# two    A     NaN  6.0   NaN  20.0
#        B     0.0  NaN  17.0   NaN
#        C     NaN  5.0   NaN  25.0



--------------------（十三）绘图 ----------------------

df1 = pd.DataFrame(np.random.randn(31,1),index=pd.date_range('2018-08-01',periods=31),columns = ['A'])
df2 = pd.DataFrame(np.random.randn(31,4),index=pd.date_range('2018-08-01',periods=31),columns = ['A','B','C','D'])

plot = df1.plot()
plot = df2.plot()



----------------（十四）字段、索引重命名 -----------------

df = pd.DataFrame({'sso_id':[111,222,333,444,555]
                  ,'level_id':[0,1,2,3,4]
                  ,'gender':[1,1,0,0,1]})


【索引重命名】

df.index = pd.date_range(start='2020-11-01',periods=5)
#             sso_id  level_id  gender
# 2020-11-01     111         0       1
# 2020-11-02     222         1       1
# 2020-11-03     333         2       0
# 2020-11-04     444         3       0
# 2020-11-05     555         4       1


【字段重命名】

df.columns[0] = 'uid'  # 把ssoid重命名为uid 

# 【注意】会报错 
#  DataFrame不支持修列中的某一个名字进行修改，只能对所有的columns修改，先将columns这个index对象改成列表操作完后再重新赋值

new_col = list(df.columns)
new_col[0] = 'uid' 
df.columns = new_col

#    uid  level_id  gender
# 0  111         0       1
# 1  222         1       1
# 2  333         2       0
# 3  444         3       0
# 4  555         4       1


----------------（十五）匹配 vlookup -----------------


frame = pd.DataFrame({'item':['ball','mug','pen','pencil','ashtray'],
                      'color':['white','red','green','black','yellow']})

#       item   color
# 0     ball   white
# 1      mug     red
# 2      pen   green
# 3   pencil   black
# 4  ashtray  yellow

# 假设有一份价格清单（字典）
price = {'ball':5.56,
         'mug':4.20,
         'bottle':1.30,
         'scissors':3.41,
         'pen':1.30,
         'pencil':0.56,
         'ashtray':2.75,
         'book':3.33}

frame['price'] = frame['item'].map(price)  # vlookup功能

#       item   color  price
# 0     ball   white   5.56
# 1      mug     red   4.20
# 2      pen   green   1.30
# 3   pencil   black   0.56
# 4  ashtray  yellow   2.75




----------------（十六）正则匹配 -----------------

【匹配】

df = pd.DataFrame({'sso_id':[111,222,333,444,555]
                  ,'level_id':[0,1,2,3,4]
                  ,'gender':[1,1,0,0,1]
                  ,'goods_id':['405','355','501','tkbx','fdrs']})

df[df.goods_id.str.contains(r'[0-9]{3}')] # 匹配三位数数值字符串的记录



【截取】

df = pd.DataFrame({'sso_id':[833673248,9427622524,1199246236],
                   'to_url':['https://super.xrkbx.com/c/goods/417?clzType=2',
                             'https://super.xrkbx.com/b/goods/501?clzType=2',
                             'https://super.xrkbx.com/b/goods/355?clzType=2']})

df['goods_id'] = df['to_url'].str.extract(r'([0-9]{3})',expand=False) # 从url中提取goods_id




----------------（十七）日期处理 -----------------


1. 时间戳转化：年、年月、年月日
方法一：
data['x_year'] = data['created_at'].apply(lambda x: x.strftime('%Y'))       # 2020-03-26 18:09:47.630 --> 2020
data['x_month'] = data['created_at'].apply(lambda x: x.strftime('%Y-%m'))   # 2020-03-26 18:09:47.630 --> 2020-03
data['x_day'] = data['created_at'].apply(lambda x: x.strftime('%Y-%m-%d'))  # 2020-03-26 18:09:47.630 --> 2020-03-26

方法二：
data['y_year'] = data['created_at'].dt.to_period('Y')      # 2020-03-26 18:09:47.630 --> 2020
data['y_month'] = data['created_at'].dt.to_period('M')     # 2020-03-26 18:09:47.630 --> 2020-03
data['y_day'] = data['created_at'].dt.to_period('D')       # 2020-03-26 18:09:47.630 --> 2020-03-26


2. 时间间隔天数：
data['order_diff'] = data['first_order_time'] - data['created_at']  # timedelta类型

返回类似sql里的 age()返回  19 days 10:29:42.454000
导出为excel时，order_diff的值为19，而不是 19 days 10:29:42.454000

data['order_diff_days'] = data['order_diff'].dt.days  此时返回19 
或者
data['order_diff_days'] = data['order_diff'].apply(lambda x: x.days)  此时返回19 


3. 时间间隔秒数：
data['order_diff_seconds'] = data['order_diff'].apply(lambda x:x.seconds) + data['diff_days']*86400  返回间隔秒数


4. 日期加减1天
df2 = df[ df['zhuce_date'] <= pd.to_datetime('2020-09-08')-pd.Timedelta(days=1) ]  # 注册日期小于等于'2020-09-08'的前1天



----------------（十八）分组排序 row_number()over() -----------------


df['rnk_desc'] = df.groupby(['sso_id'])['created_at'].rank(ascending=False)

封装成函数：
def row_number(df, groupby_col=[], orderby_col='', ascending=True):
    '''
    :param df: 需要处理的数据集；pandas.DataFrame
    :param groupby_col: 需要分组的列；list
    :param orderby_col: 需要分组后，进行排序的列；columns_names
    :param ascending: 排序方式，默认升序
    :return: pandas.Series
    '''
    return df.groupby(groupby_col)[orderby_col].rank(ascending=False,method='dense')
 
df['row_numbers'] = row_number(df, groupby_col=['k1'], orderby_col='data', ascending=False)
print(df)


----------------（十九）数据导入导出 -----------------

pd.read_sql()
pd.read_csv()
pd.read_excel()

df.to_excel(result_path, sheet_name='数据')


----------------（二十）标志转换 -----------------

标志转换 Pandas get_dummies() 方法 

df = pd.DataFrame({'id':[3566841,6541227,3512441],
                   'sex':['male','female','female'],
                   'level':['high','low','middle'],
                   'score':['1','2','3']})
id_data = df[['id']]
raw_convert_data = df.iloc[:, 1:]

df_new3 = pd.get_dummies(raw_convert_data) 
print(df_new3)
#   sex_female  sex_male  level_high  level_low  level_middle  score_1  score_2  score_3 
#            0         1           1          0             0        1        0        0
#            1         0           0          1             0        0        1        0
#            1         0           0          0             1        0        0        1

df_all2 = pd.concat((id_data, pd.DataFrame(df_new3)), axis=1) 
print(df_all2) 
#        id  sex_female  sex_male  level_high  level_low  level_middle  score_1  score_2  score_3 
#   3566841           0         1           1          0             0        1        0        0
#   6541227           1         0           0          1             0        0        1        0
#   3512441           1         0           0          0             1        0        0        1



----------------【其他归纳】 -----------------

1. 实现字符串拼接 || 

如：
print(df1)
#   sso_id company_name
# 0    111           tk
# 1    222           pa
# 2    333           zg
print(df2) 
#   sso_id  level_id
# 0    111         4
# 1    222         4
# 2    333         5

df1['new_sso_id'] = df1['sso_id'].map(str) + df2['sso_id'].map(str) 
print(df1)
#   sso_id company_name new_sso_id
# 0    111           tk     111111
# 1    222           pa     222222
# 2    333           zg     333333



2. df在Spyder中字段显示不全的问题（以...缩略字段）

pd.set_option('display.max_columns',20) #给最大列设置为20列




