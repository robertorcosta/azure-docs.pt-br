---
title: Implantar modelos de aplicativo lógico
description: Saiba como implantar modelos do Azure Resource Manager criados para aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 432e22879ce0eba89f04a1084e2d4a93a487dd45
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086429"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Implantar modelos do Azure Resource Manager para aplicativos de lógica do Azure

Depois de criar um modelo do Azure Resource Manager para o seu aplicativo lógico, você pode implantar seu modelo destamaneira:

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [PowerShell do Azure](#powershell)
* [CLI do Azure](#cli)
* [APIs REST do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implantar através do portal Azure

Para implantar automaticamente um modelo de aplicativo lógico no Azure, você pode escolher o seguinte botão **Implantar para o Azure,** que o inscreve no portal Azure e solicita informações sobre seu aplicativo lógico. Em seguida, você pode fazer quaisquer alterações necessárias no modelo ou parâmetros do aplicativo lógico.

[![Implantar no Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Por exemplo, você é solicitado para obter as seguintes informações depois de entrar no portal Azure:

* Nome da assinatura do Azure
* Grupo de recursos que você deseja usar
* Local do aplicativo lógico
* O nome do seu aplicativo lógico
* Um URI de teste
* Aceitação dos termos e das condições especificados

Para saber mais, consulte esses tópicos:

* [Visão geral: Automatize a implantação de aplicativos lógicos com modelos do Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Implantar recursos com modelos do Azure Resource Manager e do portal Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Implantação com o Visual Studio

Para implantar um modelo de aplicativo lógico a partir de um projeto do Azure Resource Group que você criou usando o Visual Studio, siga essas [etapas para implantar manualmente seu aplicativo lógico](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) no Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implantar com o Azure PowerShell

Para implantar em um grupo de *recursos específico do Azure,* use o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para saber mais, consulte esses tópicos:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para implantar em um grupo de *recursos específico do Azure,* use o seguinte comando:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para saber mais, consulte esses tópicos:

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implantar com DevOps Azure

Para implantar modelos de aplicativos lógicos e gerenciar ambientes, as equipes geralmente usam uma ferramenta como [o Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) no [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). O Azure Pipelines fornece uma [tarefa de implantação do Grupo de Recursos do Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que você pode adicionar a qualquer pipeline de compilação ou liberação. Para autorização para implantar e gerar o pipeline de liberação, você também precisa de um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md)do Azure Active Directory (AD). Saiba mais sobre [o uso de diretores de serviços com a Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Para obter mais informações sobre integração contínua e implantação contínua (CI/CD) para modelos do Azure Resource Manager com o Azure Pipelines, consulte esses tópicos e amostras:

* [Integre modelos de gerenciador de recursos com pipelines do Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Tutorial: Integração contínua dos modelos do Azure Resource Manager com o Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Exemplo: Conecte-se às filas de ônibus de serviço do Azure a partir de aplicativos azure logic e implante com pipelines Azure no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Conecte-se às contas de armazenamento do Azure a partir de aplicativos azure logic e implante com pipelines Azure no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Configure uma ação de aplicativo de função para aplicativos azure logic e implante com pipelines Azure no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Conecte-se a uma conta de integração do Azure Logic Apps e implante com o Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Amostra: Orquestrar pipelines azure usando aplicativos azure logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Aqui estão as etapas gerais de alto nível para usar o Azure Pipelines:

1. Em Azure Pipelines, crie um oleoduto vazio.

1. Escolha os recursos necessários para o pipeline, como o modelo do aplicativo lógico e os arquivos de parâmetros de modelo, que você gera manualmente ou como parte do processo de compilação.

1. Para o seu trabalho de agente, encontre e adicione a tarefa de implantação do **grupo de recursos do Azure.**

   ![Adicionar a tarefa "Azure Resource Group Deployment"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configure-se com um [diretor de serviço](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Adicione referências aos arquivos de parâmetros de modelo e parâmetros do aplicativo lógico.

1. Continue a compilar as etapas no processo de versão para quaisquer outros ambientes, testes automatizados ou aprovadores, conforme necessário.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizar Conexões OAuth

Após a implantação, seu aplicativo lógico funciona de ponta a ponta com parâmetros válidos. No entanto, você ainda deve autorizar quaisquer conexões OAuth para gerar tokens de acesso válidos para [autenticar suas credenciais](../active-directory/develop/authentication-scenarios.md). Aqui estão maneiras de autorizar conexões OAuth:

* Para implantações automatizadas, você pode usar um script que fornece consentimento para cada conexão OAuth. Aqui está um exemplo de script no GitHub no projeto [LogicAppConnectionAuth.](https://github.com/logicappsio/LogicAppConnectionAuth)

* Para autorizar manualmente as conexões OAuth, abra seu aplicativo lógico no Logic App Designer, seja no portal Azure ou no Visual Studio. No designer, autorize quaisquer conexões necessárias.

Se você usar um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory (Azure AD) para autorizar conexões, saiba como [especificar os principais parâmetros do serviço no modelo do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar aplicativos lógicos](../logic-apps/monitor-logic-apps.md)
