---
title: Crie modelos de aplicativos lógicos para implantação
description: Saiba como criar modelos do Azure Resource Manager para automatizar a implantação em aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972643"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Crie modelos do Azure Resource Manager para automatizar a implantação de aplicativos de lógica do Azure

Para ajudá-lo a automatizar a criação e a implantação do seu aplicativo lógico, este artigo descreve as maneiras pelas quais você pode criar um [modelo do Azure Resource Manager](../azure-resource-manager/management/overview.md) para o seu aplicativo lógico. Para obter uma visão geral sobre a estrutura e a sintaxe de um modelo que inclua a definição do fluxo de trabalho e outros recursos necessários para a implantação, consulte [Visão geral: Automatize a implantação de aplicativos lógicos com modelos do Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md).

O Azure Logic Apps fornece um [modelo de azure Resource Manager pré-construído](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) que você pode reutilizar, não apenas para criar aplicativos lógicos, mas também para definir os recursos e parâmetros a serem usados para implantação. Você pode usar esse modelo para seus próprios cenários de negócios ou personalize o modelo para atender às suas necessidades.

> [!IMPORTANT]
> Certifique-se de que as conexões em seu modelo usem o mesmo grupo de recursos e localização do Azure que seu aplicativo lógico.

Para obter mais informações sobre os modelos do Azure Resource Manager, consulte esses tópicos:

* [Estrutura e sintaxe do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Criar modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/template-syntax.md)
* [Desenvolva modelos do Azure Resource Manager para consistência de nuvem](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Crie modelos com o Visual Studio

Para a maneira mais fácil de criar modelos de aplicativos lógicos parametrizados válidos que estão em sua maioria prontos para implantação, use o Visual Studio (edição comunitária gratuita ou superior) e o Azure Logic Apps Tools for Visual Studio. Você pode então [criar seu aplicativo lógico no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou encontrar e baixar um aplicativo lógico existente do portal [Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Ao baixar seu aplicativo lógico, você recebe um modelo que inclui as definições para o seu aplicativo lógico e outros recursos, como conexões. O modelo também *parametriza*, ou define parâmetros para os valores usados para implantar seu aplicativo lógico e outros recursos. Você pode fornecer os valores para esses parâmetros em um arquivo de parâmetros separado. Dessa forma, você pode alterar mais facilmente esses valores com base em suas necessidades de implantação. Para saber mais, consulte esses tópicos:

* [Criar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gerenciar aplicativos lógicos no Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Crie modelos com o Azure PowerShell

Você pode criar modelos do Gerenciador de recursos usando o Azure PowerShell com o [módulo LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Este módulo de código aberto avalia primeiro seu aplicativo lógico e quaisquer conexões que o aplicativo lógico usa. O módulo então gera recursos de modelo com os parâmetros necessários para implantação.

Por exemplo, suponha que você tenha um aplicativo lógico que recebe uma mensagem de uma fila de ônibus de serviço do Azure e faz upload de dados para um banco de dados SQL do Azure. O módulo preserva toda a lógica de orquestração e parametriza as seqüências de conexão SQL e Service Bus para que você possa fornecer e alterar esses valores com base nas suas necessidades de implantação.

Essas amostras mostram como criar e implantar aplicativos lógicos usando modelos do Azure Resource Manager, Pipelines Azure no Azure DevOps e PowerShell do Azure:

* [Exemplo: Conecte-se às filas de ônibus de serviço do Azure a partir de aplicativos azure logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Conecte-se às contas de armazenamento do Azure a partir de aplicativos azure logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Amostra: Configure uma ação de aplicativo de função para aplicativos azure logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Conecte-se a uma conta de integração do Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Instalar módulos do PowerShell

1. Se você ainda não tiver, [instale o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. Para obter a maneira mais fácil de instalar o módulo LogicAppTemplate da [Galeria PowerShell,](https://www.powershellgallery.com/packages/LogicAppTemplate)execute este comando:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Para atualizar para a versão mais recente, execute este comando:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Ou, para instalar manualmente, siga as etapas do GitHub for [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Instale o cliente do Azure Resource Manager

Para que o módulo LogicAppTemplate trabalhe com qualquer token de acesso ao inquilino do Azure e por assinatura, instale a [ferramenta cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient), que é uma ferramenta simples de linha de comando que chama a API do Azure Resource Manager.

Quando você `Get-LogicAppTemplate` executa o comando com esta ferramenta, o comando primeiro recebe um token de acesso através da ferramenta ARMClient, canaliza o token para o script PowerShell e cria o modelo como um arquivo JSON. Para obter mais informações sobre a ferramenta, consulte este [artigo sobre a ferramenta cliente Do Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Gerar modelo com PowerShell

Para gerar seu modelo depois de instalar o módulo LogicAppTemplate e [o Azure CLI,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)execute este comando PowerShell:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Para seguir a recomendação de tubulação em um token da ferramenta cliente `$SubscriptionId` do [Azure Resource Manager,](https://github.com/projectkudu/ARMClient)execute este comando em vez disso, onde está o seu ID de assinatura do Azure:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Após a extração, você pode criar um arquivo de parâmetros a partir do seu modelo executando este comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Para extração com referências do Azure Key Vault (somente estática), execute este comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parâmetros | Obrigatório | Descrição |
|------------|----------|-------------|
| TemplateFile | Sim | O caminho do arquivo para o seu arquivo de modelo |
| KeyVault | Não | Um enum que descreve como lidar com possíveis valores do cofre de chaves. O padrão é `None`. |
||||

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar modelos de aplicativo lógico](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
