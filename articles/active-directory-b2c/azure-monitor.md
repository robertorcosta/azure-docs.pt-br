---
title: Monitorar Azure AD B2C com Azure Monitor
titleSuffix: Azure AD B2C
description: Saiba como registrar Azure AD B2C eventos com Azure Monitor usando o gerenciamento de recursos delegado.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 3106e5a640ed66828558078e6986979ad7195450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386208"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitorar Azure AD B2C com Azure Monitor

Use Azure Monitor para rotear logs de entrada e [auditoria](view-audit-logs.md) de Azure Active Directory B2C (Azure ad B2C) para diferentes soluções de monitoramento. Você pode manter os logs para uso de longo prazo ou integrá-los a ferramentas de SIEM (gerenciamento de eventos e informações de segurança) de terceiros para obter informações sobre o seu ambiente.

Você pode rotear eventos de log para:

* Uma [conta de armazenamento](../storage/blobs/storage-blobs-introduction.md)do Azure.
* Um [espaço de trabalho log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (para analisar dados, criar painéis e alertar sobre eventos específicos).
* Um [Hub de eventos](../event-hubs/event-hubs-about.md) do Azure (e integre com suas instâncias de lógica Splunk e do resumo).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você implanta um modelo de Azure Resource Manager usando o módulo Azure PowerShell.

* [Azure PowerShell módulo](https://docs.microsoft.com/powershell/azure/install-az-ps) versão 6.13.1 ou superior

Você também pode usar o [Azure cloud Shell](https://shell.azure.com), que inclui a versão mais recente do módulo Azure PowerShell.

## <a name="delegated-resource-management"></a>Gerenciamento de recursos delegado

Azure AD B2C aproveita [Azure Active Directory monitoramento](../active-directory/reports-monitoring/overview-monitoring.md). Para habilitar *as configurações de diagnóstico* no Azure Active Directory em seu locatário do Azure ad B2C, use o [Gerenciamento de recursos delegado](../lighthouse/concepts/azure-delegated-resource-management.md).

Você autoriza um usuário ou grupo em seu diretório de Azure AD B2C (o **provedor de serviços**) a configurar a instância de Azure monitor dentro do locatário que contém sua assinatura do Azure (o **cliente**). Para criar a autorização, você implanta um modelo de [Azure Resource Manager](../azure-resource-manager/index.yml) no seu locatário do Azure AD que contém a assinatura. As seções a seguir orientam você pelo processo.

## <a name="create-or-choose-resource-group"></a>Criar ou escolher grupo de recursos

Esse é o grupo de recursos que contém a conta de armazenamento do Azure de destino, o Hub de eventos ou o espaço de trabalho Log Analytics para receber dados de Azure Monitor. Especifique o nome do grupo de recursos ao implantar o modelo de Azure Resource Manager.

[Crie um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ou escolha um existente no locatário Azure Active Directory (Azure AD) que contém sua assinatura do Azure, *não* o diretório que contém o locatário Azure ad B2C.

Este exemplo usa um grupo de recursos chamado *Azure-ad-B2C-monitor* na região de *EUA Central* .

## <a name="delegate-resource-management"></a>Delegar gerenciamento de recursos

Em seguida, reúna as seguintes informações:

**ID de diretório** do seu diretório de Azure ad B2C (também conhecido como a ID do locatário).

1. Entre no [portal do Azure](https://portal.azure.com/) como um usuário com a função de *administrador de usuário* (ou superior).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. Selecione **Azure Active Directory**, selecione **Propriedades**.
1. Registre a **ID do diretório**.

**ID de objeto** do grupo de Azure ad B2C ou usuário que você deseja conceder permissão de *colaborador* para o grupo de recursos que você criou anteriormente no diretório que contém sua assinatura.

Para facilitar o gerenciamento, é recomendável usar *grupos* de usuários do Azure ad para cada função, permitindo que você adicione ou remova usuários individuais do grupo em vez de atribuir permissões diretamente a esse usuário. Neste tutorial, você adiciona um usuário.

1. Com **Azure Active Directory** ainda selecionado no portal do Azure, selecione **usuários**e, em seguida, selecione um usuário.
1. Registre a ID de **objeto**do usuário.

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo do Azure Resource Manager

Para carregar seu locatário do Azure AD (o **cliente**), crie um [modelo de Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) para sua oferta com as informações a seguir. Os `mspOfferName` `mspOfferDescription` valores e são visíveis quando você exibe detalhes da oferta na [página provedores de serviço](../lighthouse/how-to/view-manage-service-providers.md) do portal do Azure.

| Campo   | Definição |
|---------|------------|
| `mspOfferName`                     | Um nome que descreve essa definição. Por exemplo, *Azure ad B2C serviços gerenciados*. Esse valor é exibido para o cliente como o título da oferta. |
| `mspOfferDescription`              | Uma breve descrição da sua oferta. Por exemplo, *habilita Azure monitor em Azure ad B2C*.|
| `rgName`                           | O nome do grupo de recursos que você cria anteriormente no seu locatário do Azure AD. Por exemplo, *Azure-ad-B2C-monitor*. |
| `managedByTenantId`                | A **ID de diretório** do seu locatário de Azure ad B2C (também conhecido como a ID de locatário). |
| `authorizations.value.principalId` | A **ID de objeto** do grupo ou usuário B2C que terá acesso aos recursos nesta assinatura do Azure. Para este guia, especifique a ID de objeto do usuário que você registrou anteriormente. |

Baixe o modelo de Azure Resource Manager e os arquivos de parâmetro:

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Em seguida, atualize o arquivo de parâmetros com os valores que você registrou anteriormente. O trecho JSON a seguir mostra um exemplo de um arquivo de parâmetros de Azure Resource Manager template. Para `authorizations.value.roleDefinitionId` , use o valor da [função interna](../role-based-access-control/built-in-roles.md) para a *função colaborador*, `b24988ac-6180-42a0-ab88-20f7382dd24c` .

```json
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

Depois de atualizar o arquivo de parâmetros, implante o modelo de Azure Resource Manager no locatário do Azure como uma implantação em nível de assinatura. Como essa é uma implantação em nível de assinatura, ela não pode ser iniciada no portal do Azure. Você pode implantar usando o módulo Azure PowerShell ou o CLI do Azure. O método Azure PowerShell é mostrado abaixo.

Entre no diretório que contém sua assinatura usando [Connect-AzAccount](/powershell/azure/authenticate-azureps). Use o `-tenant` sinalizador para forçar a autenticação para o diretório correto.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Use o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) para listar as assinaturas que a conta atual pode acessar no locatário do Azure AD. Registre a ID da assinatura que você deseja projetar em seu locatário Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Em seguida, alterne para a assinatura que você deseja projetar no locatário Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Por fim, implante o modelo de Azure Resource Manager e os arquivos de parâmetro que você baixou e atualizou anteriormente. Substitua os `Location` `TemplateFile` valores, e `TemplateParameterFile` adequadamente.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

A implantação bem-sucedida do modelo produz uma saída semelhante à seguinte (saída truncada para fins de brevidade):

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

Depois de implantar o modelo, pode levar alguns minutos para que a projeção de recursos seja concluída. Talvez seja necessário aguardar alguns minutos (normalmente não mais do que cinco) antes de passar para a próxima seção para selecionar a assinatura.

## <a name="select-your-subscription"></a>Selecionar sua assinatura

Depois de implantar o modelo e ter aguardado alguns minutos para que a projeção de recursos seja concluída, associe sua assinatura ao seu diretório Azure AD B2C com as etapas a seguir.

1. **Saia** do portal do Azure se você estiver conectado no momento. Isso e a etapa a seguir são feitas para atualizar suas credenciais na sessão do Portal.
1. Entre no [portal do Azure](https://portal.azure.com) com sua conta administrativa do Azure ad B2C.
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal.
1. Selecione o diretório que contém a assinatura.

    ![Mudar diretório](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Verifique se você selecionou o diretório e a assinatura corretos. Neste exemplo, todos os diretórios e assinaturas são selecionados.

    ![Todos os diretórios selecionados no diretório & filtro de assinatura](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico

As configurações de diagnóstico definem onde os logs e as métricas de um recurso devem ser enviados. Os possíveis destinos são:

- [Conta de armazenamento do Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- Soluções de [hubs de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md) .
- [Espaço de Trabalho do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Se você ainda não fez isso, crie uma instância do tipo de destino escolhido no grupo de recursos especificado no [modelo de Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Criar configurações de diagnóstico

Você está pronto para [criar configurações de diagnóstico](../active-directory/reports-monitoring/overview-monitoring.md) no portal do Azure.

Para definir configurações de monitoramento para Azure AD B2C logs de atividades:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. Selecionar **Azure Active Directory**
1. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
1. Se houver configurações existentes no recurso, você verá uma lista de configurações já configuradas. Selecione **Adicionar configuração de diagnóstico** para adicionar uma nova configuração ou **Editar** configuração para editar uma existente. Cada configuração não pode ter mais de um de cada um dos tipos de destino.

    ![Painel de configurações de diagnóstico no portal do Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Dê um nome à sua configuração se ela ainda não tiver uma.
1. Marque a caixa para cada destino para enviar os logs. Selecione **Configurar** para especificar suas configurações, conforme descrito na tabela a seguir.

    | Configuração | Descrição |
    |:---|:---|
    | Arquivar em uma conta de armazenamento | Nome da conta de armazenamento. |
    | Transmitir para um hub de eventos | O namespace em que o Hub de eventos é criado (se esta for a primeira vez que os logs de streaming) ou transmitido (se já houver recursos que estão transmitindo essa categoria de log para esse namespace).
    | Enviar para o Log Analytics | Nome do espaço de trabalho. |

1. Selecione **AuditLogs** e **SignInLogs**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como adicionar e definir configurações de diagnóstico no Azure Monitor, consulte [tutorial: coletar e analisar logs de recursos de um recurso do Azure](../azure-monitor/insights/monitor-azure-resource.md).

Para obter informações sobre streaming de logs do Azure AD para um hub de eventos, consulte [tutorial: transmitir Azure Active Directory logs para um hub de eventos do Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
