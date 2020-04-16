---
title: Monitora Azure AD B2C com Monitor Azure
titleSuffix: Azure AD B2C
description: Saiba como registrar eventos Azure AD B2C com o Azure Monitor usando o gerenciamento de recursos delegado.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 99e04c95156e40eed8c2b9aa88a2bee6f39e90c9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392885"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitora Azure AD B2C com Monitor Azure

Use o Azure Monitor para encaminhar logs de login e [auditoria](view-audit-logs.md) do Azure Active Directory B2C (Azure AD B2C) para diferentes soluções de monitoramento. Você pode reter os logs para uso a longo prazo ou integrar-se com ferramentas de gerenciamento de dados de segurança de terceiros e gerenciamento de eventos (SIEM) para obter insights sobre o seu ambiente.

Você pode encaminhar eventos de registro para:

* Uma conta de [armazenamento](../storage/blobs/storage-blobs-introduction.md)Azure.
* Um hub [de eventos](../event-hubs/event-hubs-about.md) Azure (e integre-se com suas instâncias Splunk e Sumo Logic).
* Um [espaço de trabalho do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (para analisar dados, criar dashboards e alertar sobre eventos específicos).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você implanta um modelo do Azure Resource Manager usando o módulo Azure PowerShell.

* [Módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) versão 6.13.1 ou superior

Você também pode usar o [Azure Cloud Shell](https://shell.azure.com), que inclui a versão mais recente do módulo Azure PowerShell.

## <a name="delegated-resource-management"></a>Gestão de recursos delegada

O Azure AD B2C aproveita o [monitoramento do Azure Active Directory](../active-directory/reports-monitoring/overview-monitoring.md). Para habilitar *as configurações de diagnóstico* no Azure Active Directory dentro do seu inquilino Azure AD B2C, você usa o gerenciamento de recursos [delegado](../lighthouse/concepts/azure-delegated-resource-management.md).

Você autoriza um usuário ou grupo em seu diretório Azure AD B2C (o **Provedor de Serviços)** a configurar a instância do Monitor Do Azure dentro do inquilino que contém sua assinatura do Azure (o **Cliente**). Para criar a autorização, você implanta um modelo [do Azure Resource Manager](../azure-resource-manager/index.yml) no seu inquilino Azure AD contendo a assinatura. As seções a seguir o acompanham no processo.

## <a name="create-or-choose-resource-group"></a>Criar ou escolher grupo de recursos

Este é o grupo de recursos que contém a conta de armazenamento azure de destino, o hub de eventos ou o espaço de trabalho do Log Analytics para receber dados do Azure Monitor. Você especifica o nome do grupo de recursos ao implantar o modelo do Azure Resource Manager.

[Crie um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ou escolha um já existente no inquilino do Azure Active Directory (Azure AD) que contenha sua assinatura do Azure, *não* o diretório que contém o inquilino Azure AD B2C.

Este exemplo usa um grupo de recursos chamado *azure-ad-b2c-monitor* na região *central dos EUA.*

## <a name="delegate-resource-management"></a>Delegar gerenciamento de recursos

Em seguida, reúna as seguintes informações:

**ID do diretório** do seu diretório Azure AD B2C (também conhecido como ID do inquilino).

1. Faça login no [portal Azure](https://portal.azure.com/) como usuário com a função *de administrador* do Usuário (ou superior).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o inquilino Ad B2C do Azure.
1. Selecione **O diretório ativo do Azure,** selecione **Propriedades**.
1. Grave o **ID do Diretório**.

**ID** de objeto do grupo Azure AD B2C ou usuário que você deseja dar ao *Contribuinte* permissão para o grupo de recursos que você criou anteriormente no diretório que contém sua assinatura.

Para facilitar o gerenciamento, recomendamos o uso de *grupos* de usuários Azure AD para cada função, permitindo que você adicione ou remova usuários individuais ao grupo em vez de atribuir permissões diretamente a esse usuário. Neste passo a passo, você adiciona um usuário.

1. Com **o Azure Active Directory** ainda selecionado no portal Azure, selecione **Usuários**e selecione um usuário.
1. Registre o **ID**do objeto do usuário .

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo do Azure Resource Manager

Para embarcar no seu inquilino Azure AD (o **Cliente),** crie um [modelo de Gerenciador de Recursos do Azure](../lighthouse/how-to/onboard-customer.md) para sua oferta com as seguintes informações. Os `mspOfferName` `mspOfferDescription` valores e valores são visíveis quando você visualiza detalhes da oferta na [página provedores](../lighthouse/how-to/view-manage-service-providers.md) de serviços do portal Azure.

| Campo   | Definição |
|---------|------------|
| `mspOfferName`                     | Um nome que descreve essa definição. Por exemplo, *Azure AD B2C Managed Services*. Esse valor é exibido para o cliente como o título da oferta. |
| `mspOfferDescription`              | Uma breve descrição de sua oferta. Por exemplo, *habilita o Monitor Azure no Azure AD B2C*.|
| `rgName`                           | O nome do grupo de recursos que você cria anteriormente no seu inquilino Azure AD. Por exemplo, *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | O **ID do diretório** do seu inquilino Azure AD B2C (também conhecido como ID do inquilino). |
| `authorizations.value.principalId` | O **ID do Objeto** do grupo B2C ou usuário que terá acesso aos recursos nesta assinatura do Azure. Para este passo a passo, especifique o ID do objeto do usuário que você gravou anteriormente. |

Baixe o modelo e os arquivos de parâmetros do Azure Resource Manager:

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Em seguida, atualize o arquivo parâmetros com os valores que você gravou anteriormente. O trecho JSON a seguir mostra um exemplo de um arquivo de parâmetros do Azure Resource Manager. Para `authorizations.value.roleDefinitionId`, use o valor [da função](../role-based-access-control/built-in-roles.md) `b24988ac-6180-42a0-ab88-20f7382dd24c`incorporada para a função *contribuinte*, .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Implantar os modelos do Azure Resource Manager

Depois de atualizar seu arquivo de parâmetros, implante o modelo do Azure Resource Manager no inquilino do Azure como uma implantação em nível de assinatura. Como essa é uma implantação em nível de assinatura, ela não pode ser iniciada no portal do Azure. Você pode implantar usando o módulo Azure PowerShell ou o Azure CLI. O método Azure PowerShell é mostrado abaixo.

Faça login no diretório que contém sua assinatura usando [o Connect-AzAccount](/powershell/azure/authenticate-azureps). Use `-tenant` a bandeira para forçar a autenticação ao diretório correto.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Use o [cmdlet Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) para listar as assinaturas que a conta corrente pode acessar sob o inquilino Azure AD. Grave o ID da assinatura que deseja projetar no seu inquilino Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Em seguida, mude para a assinatura que deseja projetar para o inquilino Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Finalmente, implante o modelo de design de recursos do Azure e os arquivos de parâmetros que você baixou e atualizou anteriormente. Substitua `Location` `TemplateFile`o `TemplateParameterFile` , e os valores em conformidade.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

A implantação bem-sucedida do modelo produz uma saída semelhante à seguinte (saída truncada para brevidade):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

Depois de implantar o modelo, pode levar alguns minutos para a projeção de recursos ser concluída. Você pode precisar esperar alguns minutos (normalmente não mais do que cinco) antes de passar para a próxima seção para selecionar a assinatura.

## <a name="select-your-subscription"></a>Selecione sua assinatura

Depois de implantar o modelo e esperar alguns minutos para que a projeção de recursos seja concluída, associe sua assinatura ao diretório Azure AD B2C com as seguintes etapas.

1. **Saia** do portal Azure se você estiver atualmente conectado. Este e o seguinte passo são feitos para atualizar suas credenciais na sessão do portal.
1. Faça login no [portal Azure](https://portal.azure.com) com sua conta administrativa Azure AD B2C.
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal.
1. Selecione o diretório que contém sua assinatura.

    ![Mudar diretório](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Verifique se você selecionou o diretório e a assinatura corretos. Neste exemplo, todos os diretórios e assinaturas são selecionados.

    ![Todos os diretórios selecionados no filtro de assinatura de & de diretório](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico

As configurações de diagnóstico definem para onde devem ser enviados registros e métricas para um recurso. Os possíveis destinos são:

- [Conta de armazenamento do Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Hubs de](../azure-monitor/platform/resource-logs-stream-event-hubs.md) eventos soluções.
- [Espaço de trabalho do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Se você ainda não tiver, crie uma instância do tipo de destino escolhido no grupo de recursos especificado no [modelo Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Criar configurações de diagnóstico

Você está pronto para [criar configurações de diagnóstico](../active-directory/reports-monitoring/overview-monitoring.md) no portal Azure.

Para configurar as configurações de monitoramento para logs de atividade saqueadores do Azure AD B2C:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o inquilino Ad B2C do Azure.
1. Selecione **o Diretório Ativo do Azure**
1. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
1. Se houver configurações existentes no recurso, você verá uma lista de configurações já configuradas. Selecione **Adicionar configuração de diagnóstico** para adicionar uma nova configuração ou **Editar** a configuração para editar uma configuração existente. Cada configuração não pode ter mais do que um de cada um dos tipos de destino..

    ![Painel de configurações de diagnóstico no portal Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Dê um nome se ele ainda não tiver um.
1. Verifique a caixa de cada destino para enviar os registros. Selecione **Configurar** para especificar suas configurações conforme descrito na tabela a seguir.

    | Configuração | Descrição |
    |:---|:---|
    | Arquivar em uma conta de armazenamento | Nome da conta de armazenamento. |
    | Transmitir para um hub de eventos | O namespace onde o hub de eventos é criado (se esta é a primeira vez que os logs de streaming) ou transmitidos para (se já houver recursos que estão transmitindo essa categoria de log para este namespace).
    | Enviar para o Log Analytics | Nome do espaço de trabalho. |

1. Selecione **Loglogs de auditoria** e **logonlogs**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como adicionar e configurar configurações de diagnóstico no Azure Monitor, consulte [Tutorial: Coletar e analisar registros de recursos de um recurso do Azure](../azure-monitor/insights/monitor-azure-resource.md).

Para obter informações sobre o streaming de logs do Azure AD em um hub de eventos, consulte [Tutorial: Stream Azure Active Directory logs em um hub de eventos Do Zure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
