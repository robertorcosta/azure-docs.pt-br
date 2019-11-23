---
title: Coletar dados em seus modelos de produção
titleSuffix: Azure Machine Learning
description: Aprenda a coletar dados do modelo de entrada do Aprendizado no Azure Machine em um armazenamento de Blobs do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406462"
---
# <a name="collect-data-for-models-in-production"></a>Coletar dados para modelos em produção
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> This SDK is retiring soon. This SDK is still appropriate for developers monitoring data drift in models but most developers should use the simplified [data monitoring with Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

In this article, you can learn how to collect input model data from Azure Machine Learning you've deployed into Azure Kubernetes Cluster (AKS) into an Azure Blob storage. 

Depois de ativado, esses dados coletados ajudam você a:
* [Monitor data drifts](how-to-monitor-data-drift.md) as production data enters your model

* Tome melhores decisões sobre quando treinar ou otimizar seu modelo

* Treine novamente seu modelo com os dados coletados

## <a name="what-is-collected-and-where-does-it-go"></a>O que é coletado e para onde vai?

Os dados a seguir podem ser coletados:
* Dados de **entrada** do modelo  de serviços da Web implantados no Azure Kubernetes Cluster (AKS) (voz, imagens e vídeo **não** coletadas) 
  
* Model predictions using production input data

> [!Note]
> Pré-agregação ou pré-cálculos sobre esses dados não fazem parte do serviço no momento.   

A saída é salva em um Blob do Azure. Como os dados são adicionados a um Blob do Azure, você pode escolher sua ferramenta favorita para executar a análise. 

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> In versions of the SDK prior to `0.1.0a16` the `designation` argument was named `identifier`. If your code was developed with an earlier version, you will need to update accordingly.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)

- Um workspace do Azure Machine Learning, um diretório local contendo seus scripts e o SDK de Aprendizado de Máquina do Azure para Python instalado. Learn how to get these prerequisites using the [How to configure a development environment](how-to-configure-environment.md) document

- Um modelo de aprendizado de máquina treinado para ser implantado para o serviço de Kubernetes do Azure (AKS). If you don't have one, see the [train image classification model](tutorial-train-models-with-aml.md) tutorial

- Um cluster do Serviço de Kubernetes do Azure. For information on how to create and deploy to one, see the [How to deploy and where](how-to-deploy-and-where.md) document

- [Set up your environment](how-to-configure-environment.md) and install the [Monitoring SDK](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Habilitar coleta de dados
Data collection can be enabled regardless of the model being deployed through Azure Machine Learning or other tools. 

Para habilitá-lo, você precisa:

1. Open the scoring file

1. Adicione o [seguinte código](https://aka.ms/aml-monitoring-sdk) na parte superior do arquivo:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Declare suas variáveis de coleta de dados na sua função `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional, você não precisa configurá-lo se seu modelo não exigir isso. Ter uma correlação em vigor ajuda você a mapear com mais facilidade outros dados. (Exemplos incluem: LoanNumber, CustomerId, etc.)
    
    *Identifier* is later used for building the folder structure in your Blob, it can be used to divide “raw” data versus “processed”

3.  Adicione as seguintes linhas de código à função `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. A coleta de dados é **não** automaticamente definida como **verdadeiro** quando você implanta um serviço no AKS, portanto, você deve atualizar seu arquivo de configuração, como: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    O AppInsights para monitoramento de serviço também pode ser ativado alterando essa configuração:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. To create a new image and deploy the service, see the [How to deploy and where](how-to-deploy-and-where.md) document


Se você já tiver um serviço com as dependências instaladas no **arquivo de ambiente** e no **arquivo de pontuação**, ative a coleta de dados:

1. Go to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace

1. Go to **Deployments** -> **Select service** -> **Edit**

   ![Editar serviço](media/how-to-enable-data-collection/EditService.PNG)

1. In **Advanced Settings**, select **Enable Model data collection**

    [![Rápida verificação de dados](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In this window, you can also choose to "Enable Appinsights diagnostics" to track the health of your service

1. Select **Update** to apply the change


## <a name="disable-data-collection"></a>Desativar coleta de dados
Você pode parar de coletar dados a qualquer momento. Use Python code or Azure Machine Learning studio to disable data collection.

+ Option 1 - Disable in Azure Machine Learning studio: 
  1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

  1. Open your workspace

  1. Go to **Deployments** -> **Select service** -> **Edit**

     [![Opções de edição](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **Advanced Settings**, deselect **Enable Model data collection**

     [![Desmarque a opção de coleta de dados](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Select **Update** to apply the change

  You can also access these settings in your workspace in [Azure Machine Learning studio](https://ml.azure.com).

+ Opção 2 - Use o Python para desabilitar a coleta de dados:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Validar seus dados e analisá-los
Você pode escolher qualquer ferramenta de sua preferência para analisar os dados coletados para o Blob do Azure.

Para acessar rapidamente os dados do blob:

1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace
1. Click on **Storage**

    [![Armazenamento](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída no blob com esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analisando dados do modelo por meio do Power BI

1. Download and Open [Power BI Desktop](https://www.powerbi.com)

1. Select **Get Data** and click on [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![Instalação de Blob do PBI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Adicione o nome da sua conta de armazenamento e insira sua chave de armazenamento. You can find this information in your blob's **Settings** >> Access keys

1. Select the container **modeldata** and click on **Edit**

    [![Navegador PBI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consultas, clique na coluna "Nome" e adicione sua conta de armazenamento 1. Caminho do modelo para o filtro. Nota: se você quiser apenas examinar arquivos de um ano ou mês específico, basta expandir o caminho do filtro. For example, just look into March data: /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/designation>/year>/3

1. Filtre os dados relevantes para você com base no **Nome**. If you stored **predictions** and **inputs**, you'll need to create a query for each

1. Click on the double arrow aside the **Content** column to combine the files

    [![Conteúdo do PBI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Click OK and the data will preload

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. You can now click **Close and Apply**

1.  If you added inputs and predictions, your tables will automatically correlate by **RequestId**

1. Start building your custom reports on your model data


### <a name="analyzing-model-data-using-databricks"></a>Analisando dados do modelo usando Databricks

1. Create a [Databricks workspace](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Go to your Databricks workspace

1. In your databricks workspace, select **Upload Data**

    [![Carregamento de banco de dados](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Create New Table and select **Other Data Sources** -> Azure Blob Storage -> Create Table in Notebook

    [![Tabela de banco de dados](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize o local dos seus dados. Veja um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Follow the steps on the template in order to view and analyze your data

## <a name="example-notebook"></a>Caderno de exemplo

O notebook [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) demonstra os conceitos neste artigo.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
