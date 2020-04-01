---
title: Coletar dados em seus modelos de produção
titleSuffix: Azure Machine Learning
description: Saiba como coletar dados do modelo de entrada do Azure Machine Learning no armazenamento Azure Blob.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 44acc81df9eb6dc6a6af28b5b0f4730aa93adffc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475434"
---
# <a name="collect-data-for-models-in-production"></a>Coletar dados para modelos em produção

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> O Azure Machine Learning Monitoring SDK será aposentado em breve. O SDK ainda é apropriado para desenvolvedores que atualmente usam o SDK para monitorar a deriva de dados nos modelos. Mas para novos clientes, recomendamos o uso do monitoramento simplificado [de dados com o Application Insights.](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)

Este artigo mostra como coletar dados do modelo de entrada do Azure Machine Learning. Ele também mostra como implantar os dados de entrada em um cluster Azure Kubernetes Service (AKS) e armazenar os dados de saída no armazenamento Azure Blob.

Uma vez ativada a coleta, os dados coletados ajudam:

* [Monitore os desvios de dados à](how-to-monitor-data-drift.md) medida que os dados de produção entram no seu modelo.

* Tome melhores decisões sobre quando retreinar ou otimizar seu modelo.

* Retreinar seu modelo com os dados coletados.

## <a name="what-is-collected-and-where-it-goes"></a>O que é coletado e para onde vai

Os dados a seguir podem ser coletados:

* Modele dados de entrada de serviços web implantados em um cluster AKS. Áudio de voz, imagens e vídeo *não* são coletados.
  
* Predições de modelo usando dados de entrada de produção.

>[!NOTE]
> Preaggregação e pré-cálculos sobre esses dados não fazem atualmente parte do serviço de coleta.

A saída é salva no armazenamento Blob. Como os dados são adicionados ao armazenamento Blob, você pode escolher sua ferramenta favorita para executar a análise.

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Nas versões do Azure Machine Learning SDK para Python anteriormente `designation` à versão `identifier`0.1.0a16, o argumento é nomeado . Se você desenvolveu seu código com uma versão anterior, você precisa atualizá-lo de acordo.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.

- Um espaço de trabalho AzureMachine Learning, um diretório local que contém seus scripts, e o Azure Machine Learning SDK for Python devem ser instalados. Para saber como instalá-los, [consulte Como configurar um ambiente de desenvolvimento.](how-to-configure-environment.md)

- Você precisa de um modelo de aprendizado de máquina treinado para ser implantado no AKS. Se você não tem um modelo, consulte o tutorial do [modelo de classificação de imagem Train.](tutorial-train-models-with-aml.md)

- Você precisa de um cluster AKS. Para obter informações sobre como criar um e implantar nele, consulte [Como implantar e onde](how-to-deploy-and-where.md).

- [Configure seu ambiente](how-to-configure-environment.md) e instale o [SDK de monitoramento de aprendizado de máquina do Azure](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Habilitar coleta de dados

Você pode habilitar a coleta de dados independentemente do modelo que você implanta através do Azure Machine Learning ou outras ferramentas.

Para habilitar a coleta de dados, você precisa:

1. Abra o arquivo de pontuação.

1. Adicione o [seguinte código](https://aka.ms/aml-monitoring-sdk) na parte superior do arquivo:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declare suas variáveis de coleta de dados na sua função `init`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *Correlacionaé* um parâmetro opcional. Você não precisa usá-lo se o seu modelo não precisar. O uso de *CorrelationId* ajuda a mapear com mais facilidade outros dados, como *LoanNumber* ou *CustomerId*.
    
    O *parâmetro Identificador* é usado mais tarde para construir a estrutura da pasta em sua bolha. Você pode usá-los para diferenciar dados brutos de dados processados.

1. Adicione as seguintes linhas de código à função `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. A coleta de dados *não* é definida automaticamente como **verdadeira** quando você implanta um serviço no AKS. Atualize seu arquivo de configuração, como no exemplo a seguir:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Você também pode habilitar o Application Insights para monitoramento de serviços alterando essa configuração:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Para criar uma nova imagem e implantar o modelo de aprendizado de máquina, veja [Como implantar e onde](how-to-deploy-and-where.md).

Se você já tiver um serviço com as dependências instaladas no arquivo de ambiente e no arquivo de pontuação, habilite a coleta de dados seguindo estas etapas:

1. Vá para [Azure Machine Learning](https://ml.azure.com).

1. Abra seu workspace.

1. Selecionar **implantações** > Selecionar**edição****de serviço** > .

   ![Editar o serviço](././media/how-to-enable-data-collection/EditService.PNG)

1. Em **Configurações Avançadas,** **selecione Habilitar diagnósticos de aplicativos e coleta de dados**.

1. Selecione **Atualizar** para aplicar as alterações.

## <a name="disable-data-collection"></a>Desativar coleta de dados

Você pode parar de coletar dados a qualquer momento. Use o código Python ou o Azure Machine Learning para desativar a coleta de dados.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Opção 1 - Desativar a coleta de dados no Azure Machine Learning

1. Faça login no [Azure Machine Learning](https://ml.azure.com).

1. Abra seu workspace.

1. Selecionar **implantações** > Selecionar**edição****de serviço** > .

   [![Selecione a opção Editar](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. Em **Configurações Avançadas,** **habilitar os diagnósticos e a coleta de dados do Application Insights.**

1. Selecione **Atualizar** para aplicar a alteração.

Você também pode acessar essas configurações em seu espaço de trabalho no [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Opção 2 - Usar python para desativar coleta de dados

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Validar e analisar seus dados

Você pode escolher uma ferramenta de sua preferência para analisar os dados coletados no seu armazenamento Blob.

### <a name="quickly-access-your-blob-data"></a>Acesse rapidamente seus dados de blob

1. Faça login no [Azure Machine Learning](https://ml.azure.com).

1. Abra seu workspace.

1. Selecione **Armazenamento**.

    [![Selecione a opção Armazenamento](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída da bolha com esta sintaxe:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analisar dados do modelo usando o Power BI

1. Baixe e abra [o Power BI Desktop](https://www.powerbi.com).

1. Selecione **Obter dados** e selecione [**Armazenamento Azure Blob**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Configuração de bolha bi de potência](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adicione o nome da sua conta de armazenamento e insira sua chave de armazenamento. Você pode encontrar essas informações selecionando**teclas de acesso** de **configurações** > em sua bolha.

1. Selecione o contêiner **de dados do modelo** e selecione **Editar**.

    [![Navegador BI de Potência](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consulta, clique na coluna **Nome** e adicione sua conta de armazenamento.

1. Digite seu caminho de modelo no filtro. Se você quiser olhar apenas em arquivos de um ano ou mês específico, basta expandir o caminho do filtro. Por exemplo, para olhar apenas os dados de março, use este caminho de filtro:

   /modeldata/\<subscriptionid\<>/\<resourcegroupname>/\<workspacename>/ webservicename>/\<modelname>/\<modelversion>/\<design>/ano\<>/3

1. Filtre os dados que são relevantes para você com base nos valores **de Name.** Se você armazenou previsões e entradas, você precisa criar uma consulta para cada um.

1. Selecione as setas duplas para baixo ao lado do título da coluna **Conteúdo** para combinar os arquivos.

    [![Conteúdo bi de energia](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecione **OK**. Os dados são pré-carregados.

    [![Power BI Combine arquivos](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecione **Fechar e Aplicar**.

1. Se você adicionou entradas e previsões, suas tabelas serão automaticamente ordenadas por valores **RequestId.**

1. Comece a criar relatórios personalizados em seus dados de modelo.

### <a name="analyze-model-data-using-azure-databricks"></a>Analisar dados do modelo usando o Azure Databricks

1. Crie [um espaço de trabalho do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Vá para seu workspace do Databricks.

1. No seu espaço de trabalho Databricks, selecione **'Carregar dados '**.

    [![Selecionando a opção Databricks Upload Data](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecione **Criar nova tabela** e selecionar **Outras fontes de** > dados**Azure Blob Storage** > **Criar tabela no Notebook**.

    [![Criação de tabela databricks](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize a localização de seus dados. Veja um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Configuração de databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga as etapas do modelo para visualizar e analisar seus dados.
