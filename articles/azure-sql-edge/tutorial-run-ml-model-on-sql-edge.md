---
title: Implantar o modelo de ML no SQL do Azure no Edge usando ONNX
description: Na última parte deste tutorial do SQL do Azure no Edge de três partes para prever impurezas do minério de ferro, você executará os modelos de aprendizado de máquina do ONNX no SQL no Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: abd1bc9b53c303d094d74683da0d6e3fad4d715f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887467"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Implantar o modelo de ML no SQL do Azure no Edge usando ONNX 

Na última parte deste tutorial de três partes para prever as impurezas do minério de ferro no SQL do Azure no Edge, você irá:

1. Usar o Azure Data Studio para se conectar ao Banco de Dados SQL na instância do SQL do Azure no Edge.
2. Prever as impurezas do minério de ferro com o ONNX no SQL do Azure no Edge.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Conectar-se ao Banco de Dados SQL na instância do SQL do Azure no Edge

1. Abra o Azure Data Studio.

2. Na guia de **boas-vindas**, inicie uma nova conexão com os seguintes dados:

   |_Campo_|_Valor_|
   |-------|-------|
   |Tipo de conexão| Microsoft SQL Server|
   |Servidor|Endereço IP público mencionado na VM que foi criado para esta demonstração.|
   |Nome de Usuário|sa|
   |Senha|A senha forte que foi usada ao criar a instância do SQL do Azure no Edge.|
   |Banco de dados|Padrão|
   |Grupo de servidor|Padrão|
   |Nome (opcional)|Forneça um nome opcional.|

3. Clique em **Conectar**

4. Na seção **Arquivo**, abra um novo notebook ou use o atalho de teclado Alt + Windows + N. 

5. Defina o kernel como Python 3.

## <a name="predict-iron-ore-impurities-with-onnx"></a>Prever as impurezas do minério de ferro com o ONNX

Insira o código Python a seguir no notebook do Azure Data Studio e execute-o.

1. Primeiramente, instale e importe os pacotes necessários.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. Defina o workspace AutoML do Azure e a configuração de experimento AutoML para o experimento de regressão.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. Importe o conjunto de dados para um quadro do panda. Para este modelo de treinamento, use o conjunto de dados de treinamento [Quality Prediction in a Mining Process](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) (Previsão da qualidade em um processo de mineração) da Kaggle. Baixe o arquivo de dados e salve-o localmente no seu computador de desenvolvimento. Você usará esses dados para prever a quantidade de impurezas no concentrado de minério.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. Analise os dados para identificar qualquer assimetria. Durante esse processo, examine a distribuição e as informações de distorção de cada coluna no quadro de dados.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. Verifique e corrija o nível de assimetria nos dados.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. Verifique a correlação dos outros recursos com o recurso de previsão. Se a correlação não for alta, remova esses recursos.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. Inicie o experimento do Azure ML para localizar e treinar o melhor algoritmo. Neste caso, você está testando com todos os algoritmos de regressão, com uma métrica primária de erro quadrático médio normalizado (NRMSE). Para obter mais informações, confira [Métrica primária dos experimentos do Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric). O código a seguir iniciará uma execução local do experimento do ML.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. Carregue o modelo no banco de dados do SQL do Azure no Edge para pontuação local.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. Por fim, use o modelo do SQL do Azure no Edge para realizar previsões usando o modelo treinado.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. Usando Python, crie um gráfico do percentual previsto de silica em relação às informações de ferro (iron feed), datetime e silica (silica feed).

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar os modelos do ONNX no SQL do Azure no Edge, confira [Machine learning e IA com ONNX no SQL no Edge](onnx-overview.md).
