# df_operation

some normal and useful skills about pandas.dataframe

# -----------------------(创建)------------------------- # 

# (一) 自定义
import pandas as pd 

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
                    '2018-05-07','2017-12-15','2019-06-15']}) 


# (二) 读取本地文件

file_add = '/Desktop/insurance_data_demo.csv'
df = pd.read_csv(file_add
                ,skiprows=1) # 跳过第一行读取

# (三) 以递增时间为索引的df 

import pandas as pd
import numpy  as np 

date_range = pd.date_range('2018-01-01','2018-01-10') # 等价于 date_range = pd.date_range('2018-01-01', periods=10)
df = pd.DataFrame(data=np.random.randint(low=1,high=50,size=40).reshape(10,4)
                 ,index=date_range
                 ,columns=['col1','col2','col3','col4'])

# (四) 读取数据库

# (1) MySQL 数据导入 形成 DataFrame

import pymysql 
import pandas as pd 

dbconn = pymysql.connect(host="192.168.**.***",user="******",passwd="******",db="edw") 
sql = "select * from table_name"
df = pd.read_sql(sql, dbconn)    # 返回数据框
dbconn.close()


# (2) PostgreSQL 数据导入 形成 DataFrame

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


# (3) PostgreSQL 数据导入 形成 列表

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

conn.close() # 关闭游标 




