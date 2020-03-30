---
title: Monitore e colete dados de pontos finais de serviçoweb de Aprendizado de Máquina
titleSuffix: Azure Machine Learning
description: Monitore os serviços web implantados com o Azure Machine Learning usando o Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136186"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitore e colete dados de pontos finais de serviçoweb ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende como coletar dados e monitorar modelos implantados em pontos finais de serviços web no Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI) ativando o Azure Application Insights via 
* [Azure Machine Learning Python SDK](#python)
* [Estúdio de Machine Learning do Azure](#studio) emhttps://ml.azure.com

Além de coletar os dados e a resposta de saída de um ponto final, você pode monitorar:

* As taxas, tempos de resposta e taxas de falha de solicitação
* Taxas de dependência, tempos de resposta e taxas de falha
* Exceções

[Saiba mais sobre o Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Pré-requisitos

* Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje

* Um workspace do Azure Machine Learning, um diretório local que contém seus scripts e o SDK de Aprendizado de Máquina do Azure para Python instalado. Para saber como obter esses pré-requisitos, consulte [Como configurar um ambiente de desenvolvimento](how-to-configure-environment.md)

* Um modelo de aprendizado de máquina treinado para ser implantado para o serviço de Kubernetes do Azure (AKS) ou Instância de Contêiner do Azure (ACI). Se você não tiver um, consulte o tutorial do [modelo de classificação de imagem train](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadados de serviços web e dados de resposta

>[!Important]
> O Azure Application Insights só registra cargas de até 64kb. Se esse limite for atingido, apenas as saídas mais recentes do modelo serão registradas. 

Os metadados e a resposta ao serviço - correspondentes aos metadados do serviço web e às previsões do `"model_data_collection"`modelo - são registrados nos rastreamentos do Azure Application Insights a mensagem. Você pode consultar o Azure Application Insights diretamente para acessar esses dados ou configurar uma [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) para uma conta de armazenamento para maior retenção ou processamento adicional. Os dados do modelo podem então ser usados no Azure Machine Learning para configurar rotulagem, retreinamento, explicabilidade, análise de dados ou outro uso. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Use python SDK para configurar 

### <a name="update-a-deployed-service"></a>Atualizar um serviço implantado

1. Identificar o serviço no seu workspace. O valor `ws` para é o nome do seu espaço de trabalho

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Atualize seu serviço e habilite o Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rastreamentos de log personalizados em seu serviço

Se você deseja registrar rastreamentos personalizados, siga o processo de implantação padrão para o AKS ou ACI no documento [Como e onde implantar](how-to-deploy-and-where.md). Em seguida, execute as seguintes etapas:

1. Atualize o arquivo de pontuação adicionando declarações de impressão
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Atualize a configuração do serviço
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Criar uma imagem e implantá-lo no [AKS ou ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Desabilitar o controle em Python

Para desativar o Azure Application Insights, use o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Use o estúdio Azure Machine Learning para configurar

Você também pode habilitar o Azure Application Insights do estúdio Azure Machine Learning quando estiver pronto para implantar seu modelo com essas etapas.

1. Faça login no seu espaço de trabalho emhttps://ml.azure.com/
1. Vá para **Modelos** e selecione qual modelo você deseja implantar
1. Selecione **+Implantar**
1. Preencha o formulário **de modelo Implantar**
1. Expanda o **menu Avançado**

    ![Implantar formulário](./media/how-to-enable-app-insights/deploy-form.png)
1. Selecione **ativar diagnósticos de aplicativos e coleta de dados**

    ![Habilite insights de aplicativos](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Avaliar dados
Os dados do seu serviço são armazenados na conta do Azure Application Insights, dentro do mesmo grupo de recursos do Azure Machine Learning.
Para exibi-lo:

1. Vá para o seu espaço de trabalho de Aprendizado de Máquina do Azure no [portal Azure](https://ms.portal.azure.com/) e clique no link Insights do aplicativo

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selecione a **guia Visão geral** para ver um conjunto básico de métricas para o seu serviço

   [![Visão geral](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Para analisar os metadados e a resposta da solicitação de serviço web, selecione a tabela **de solicitações** na seção **Logs (Analytics)** e selecione **Executar** para exibir solicitações

   [![Modelar dados](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Para analisar seus traços personalizados, selecione **Analytics**
4. Na seção de esquema, selecione **Traces**. Em seguida, selecione **executar** para executar sua consulta. Os dados devem aparecer em um formato de tabela e devem mapear para suas chamadas personalizadas em seu arquivo de pontuação

   [![Traços personalizados](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Para saber mais sobre como usar o Azure Application Insights, consulte [O que é o Application Insights?](../azure-monitor/app/app-insights-overview.md)

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportar dados para processamento adicional e retenção mais longa

>[!Important]
> O Azure Application Insights só suporta exportações para o armazenamento blob. Os limites adicionais desse recurso de exportação estão listados na [telemetria exportação do App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Você pode usar a [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do Azure Application Insights para enviar mensagens para uma conta de armazenamento suportada, onde uma retenção mais longa pode ser definida. As `"model_data_collection"` mensagens são armazenadas no formato JSON e podem ser facilmente analisados para extrair dados do modelo. 

A Fábrica de Dados Do Azure, a Azure ML Pipelines ou outras ferramentas de processamento de dados podem ser usadas para transformar os dados conforme necessário. Quando você transformou os dados, você pode registrá-los no espaço de trabalho do Azure Machine Learning como um conjunto de dados. Para isso, consulte [Como criar e registrar conjuntos de dados](how-to-create-register-datasets.md).

   [![Exportação contínua](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Caderno de exemplo

O notebook [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) demonstra conceitos neste artigo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* Veja [como implantar um modelo em um cluster de serviços Do Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) ou como implantar um modelo no [Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) para implantar seus modelos em pontos finais de serviço web e habilitar o Azure Application Insights para aproveitar a coleta de dados e o monitoramento de ponto final
* Consulte [MLOps: Gerencie, implante e monitore modelos com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) para saber mais sobre como aproveitar os dados coletados dos modelos em produção. Esses dados podem ajudar a melhorar continuamente seu processo de aprendizado de máquina
