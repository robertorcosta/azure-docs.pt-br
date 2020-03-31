---
title: implantação e consumo
titleSuffix: ML Studio (classic) - Azure
description: Você pode usar o Azure Machine Learning Studio (clássico) para implantar fluxos de trabalho e modelos de aprendizado de máquina como serviços web. Esses serviços Web podem ser usados para chamar os modelos de aprendizado de máquina de aplicativos pela Internet para fazer previsões em tempo real ou no modo de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204385"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (clássico) Web Services: Implantação e consumo

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Você pode usar o Azure Machine Learning Studio (clássico) para implantar fluxos de trabalho e modelos de aprendizado de máquina como serviços web. Esses serviços Web podem ser usados para chamar os modelos de aprendizado de máquina de aplicativos pela Internet para fazer previsões em tempo real ou no modo de lote. Como os serviços Web são RESTful, você pode chamá-los por meio de várias linguagens de programação e plataformas, como .NET e Java, e de aplicativos, como o Excel.

As próximas seções fornecem links para passo a passos, código e documentação para ajudá-lo a se familiarizar.

## <a name="deploy-a-web-service"></a>Implantar um serviço Web

### <a name="with-azure-machine-learning-studio-classic"></a>Com Azure Machine Learning Studio (clássico)

O portal Studio (clássico) e o portal Microsoft Azure Machine Learning Web Services ajudam você a implantar e gerenciar um serviço web sem escrever código.

Os seguintes links fornecem informações gerais sobre como implantar um novo serviço Web:

* Para obter uma visão geral de como implantar um novo serviço Web baseado no Azure Resource Manager, consulte [Implantar um novo serviço Web](deploy-a-machine-learning-web-service.md).
* Para ver um passo a passo de como implantar um serviço Web, consulte [Implantar um serviço Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Para um passo a passo completo sobre como criar e implantar um serviço web, comece com [o Tutorial 1: Preveja o risco de crédito](tutorial-part1-credit-risk.md).
* Para obter exemplos específicos que implantam um serviço Web, consulte:

  * [Tutorial 3: Implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)
  * [Como implantar um serviço Web em várias regiões](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com as APIs do provedor de recursos dos serviços Web (APIs do Azure Resource Manager)

O provedor de recursos azure Machine Learning Studio (clássico) para serviços web permite a implantação e o gerenciamento de serviços web usando chamadas de API REST. Para saber mais, confira a referência [Serviço Web do Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell

O provedor de recursos azure Machine Learning Studio (clássico) para serviços web permite a implantação e o gerenciamento de serviços web usando cmdlets PowerShell.

Para usar os cmdlets, você deve primeiro fazer login na sua conta do Azure dentro do ambiente PowerShell usando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Se não souber como chamar comandos do PowerShell baseados no Resource Manager, consulte [Usando o Azure PowerShell com o Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Para exportar seu experimento preditivo, use este [código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o arquivo .exe com base no código, você pode digitar:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executar o aplicativo cria um modelo JSON do serviço Web. Para usar o modelo para implantar um serviço Web, você deve adicionar as seguintes informações:

* Nome e chave da conta de armazenamento

    É possível obter o nome e a chave da conta de armazenamento no [portal do Azure](https://portal.azure.com/).
* ID do plano de compromisso

    Você pode obter a ID do plano no portal [Serviços Web do Azure Machine Learning](https://services.azureml.net) fazendo logon e clicando no nome de um plano.

Adicione-a ao modelo JSON como filho do nó *Properties* no mesmo nível do nó *MachineLearningWorkspace*.

Aqui está um exemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte os seguintes artigos e o código de exemplo para obter mais detalhes:

* [Azure Machine Learning Studio (clássico) Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) referência em MSDN
* [Passo a passo](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) de exemplo no GitHub

## <a name="consume-the-web-services"></a>Consumir os serviços Web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Na interface do usuário dos Serviços Web do Azure Machine Learning (Teste)

Você pode testar o serviço Web no portal dos Serviços Web do Azure Machine Learning. Isso inclui o teste das interfaces do RRS (serviço de Solicitação-Resposta) e do BES (serviço de Execução em Lotes).

* [Implantar um novo serviço Web](deploy-a-machine-learning-web-service.md)
* [Implantar um serviço Web de Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>No Excel

Você pode baixar um modelo do Excel que consome o serviço Web:

* [Consumindo um Serviço Web do Azure Machine Learning por meio do Excel](consuming-from-excel.md)
* [Complemento do Excel para serviços web de aprendizado de máquina do Azure](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Em um cliente baseado em REST

Os Serviços Web do Azure Machine Learning são APIs RESTful. Você pode consumir essas APIs de várias plataformas, como .NET, Python, R, Java, etc. A página **Consumir** para o seu serviço web no [portal Microsoft Azure Machine Learning Web Services](https://services.azureml.net) tem um código de exemplo que pode ajudá-lo a começar. Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).
