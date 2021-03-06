---
title: SQL Database Edge Preview'da ONNX ile öngörüleri dağıtma ve yapma
description: Bir modeli nasıl eğitecek, ONNX'e dönüştürecek, Azure SQL Veritabanı Kenarı Önizlemesine dağıtacak ve yüklenen ONNX modelini kullanarak verilerde yerel PREDICT'i çalıştırmayı öğrenin.
keywords: sql veritabanı kenarını dağıtmak
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: aff9346595d3b8985d3558658af32d05f88c0554
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365457"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-in-sql-database-edge-preview"></a>SQL Database Edge Preview'da bir ONNX modeliyle öngörüleri dağıtma ve yapma

Bu hızlı başlangıçta, bir modeli nasıl eğiteceğinizi, ONNX'e dönüştürmeyi, Azure SQL Veritabanı Kenarı Önizlemesine dağıtmayı ve yüklenen ONNX modelini kullanarak verilerde yerel PREDICT'i çalıştırmayı öğreneceksiniz. Daha fazla bilgi için [SQL Database Edge Preview'da ONNX ile Machine learning ve AI'ye](onnx-overview.md)bakın.

Bu quickstart **scikit-öğrenmek** dayanmaktadır ve [Boston Konut veri seti](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)kullanır.

## <a name="before-you-begin"></a>Başlamadan önce

* Bir Azure SQL Veritabanı Kenarı modülü dağıtmadıysanız, [Azure portalını kullanarak SQL Veritabanı Kenarı Önizlemesini dağıtma](deploy-portal.md)adımlarını izleyin.

* [Azure Veri Stüdyosu'nu](https://docs.microsoft.com/sql/azure-data-studio/download)yükleyin.

* Azure Veri Stüdyosu'nu açın ve bu hızlı başlangıç için gereken paketleri yüklemek için aşağıdaki adımları izleyin:

    1. Python 3 Çekirdeğine bağlı [Yeni Not Defteri'ni](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks) açın. 
    1. **Paketleri Yönet'e** tıklayın ve **Yeni Ekle**altında , **scikit-learn**için arama yapın ve scikit-learn paketini yükleyin. 
    1. Ayrıca, **setuptools**, **numpy**, **onnxmltools**, **onnxruntime**, **skl2onnx**, **pyodbc**, ve **sqlalchemy** paketleri yükleyin.
    
* Aşağıdaki her komut dosyası bölümü için, Azure Veri Stüdyosu not defterindeki bir hücreye girin ve hücreyi çalıştırın.

## <a name="train-a-pipeline"></a>Bir boru hattı eğitin

Bir evin ortanca değerini tahmin etmek için özellikleri kullanmak için veri kümesini bölün.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])

# x contains all predictors (features)
x = df.drop(['MEDV'], axis = 1)

# y is what we are trying to predict - the median value
y = df.iloc[:,-1]

# Split the data frame into features and target
x_train = df.drop(['MEDV'], axis = 1)
y_train = df.iloc[:,-1]

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Çıktı**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

LinearRegression modelini eğitmek için bir boru hattı oluşturun. Diğer regresyon modellerini de kullanabilirsiniz.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Modelin doğruluğunu kontrol edin ve ardından R2 puanını ve ortalama kare hatasını hesaplayın.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Çıktı**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Modeli ONNX'e dönüştürün

Veri türlerini desteklenen SQL veri türlerine dönüştürün. Bu dönüştürme, diğer veri çerçeveleri için de gerekli olacaktır.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Kullanarak, `skl2onnx`LinearRegression modelini ONNX biçimine dönüştürün ve yerel olarak kaydedin.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>ONNX modelini test edin

Modeli ONNX biçimine dönüştürdükten sonra, performansta çok az ve hiç bozulma göstermek için modeli puan.

> [!NOTE]
> ONNX Runtime, küçük tutarsızlıkların mümkün olması için çiftler yerine float kullanır.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Çıktı**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>ONNX modelini ekleme

Modeli Azure SQL Veritabanı Kenarı'nda, `models` veritabanındaki `onnx`bir tabloda saklayın. Bağlantı dizesi, **sunucu adresini,** **kullanıcı adını**ve **parolayı**belirtin.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Verileri yükleme

Verileri Azure SQL Veritabanı Kenarı'na yükleyin.

İlk olarak, Boston konut veri kümesinin alt kümelerini depolamak için iki tablo, **özellik** ve **hedef**oluşturun.

* **Özellikler,** hedefi, ortanca değeri tahmin etmek için kullanılan tüm verileri içerir. 
* **Hedef,** veri kümesindeki her kayıt için ortanca değeri içerir. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Son `sqlalchemy` olarak, tablolara `x_train` `y_train` `features` ve pandalara veri çerçeveleri `target`eklemek için kullanın ve sırasıyla. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

Artık veritabanındaki verileri görüntüleyebilirsiniz.

## <a name="run-predict-using-the-onnx-model"></a>ONNX modelini kullanarak PREDICT'i çalıştırın

Azure SQL Database Edge'deki modelle, yüklenen ONNX modelini kullanarak verilerde yerel PREDICT'i çalıştırın.

> [!NOTE]
> Kalan hücreyi çalıştırmak için not defteri çekirdeğini SQL olarak değiştirin.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [onnx].[dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input) WITH (variable1 FLOAT) AS p
```

## <a name="next-steps"></a>Sonraki Adımlar

* [SQL Database Edge'de ONNX ile Makine Öğrenimi ve Yapay AI](onnx-overview.md)
