---
title: Monitore as mudanças de delegação em seu inquilino de gestão
description: Saiba como monitorar a atividade da delegação desde os inquilinos do cliente até o seu inquilino gerenciador.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 82c41c392210e088c85af510b9698e0140f660e5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421898"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitore as mudanças de delegação em seu inquilino de gestão

Como provedor de serviços, você pode querer estar ciente quando assinaturas de clientes ou grupos de recursos são delegados ao seu inquilino através do [gerenciamento de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md)ou quando os recursos anteriormente delegados são removidos.

No inquilino de gestão, o [registro de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) acompanha a atividade da delegação no nível do inquilino. Esta atividade registrada inclui quaisquer delegações adicionadas ou removidas de todos os inquilinos do cliente.

Este tópico explica as permissões necessárias para monitorar a atividade da delegação para o seu inquilino (em todos os seus clientes) e as melhores práticas para fazê-lo. Ele também inclui um script de exemplo que mostra um método para consultar e relatar esses dados.

> [!IMPORTANT]
> Todas essas etapas devem ser executadas em seu inquilino gerenciador, e não em qualquer inquilino cliente.

## <a name="enable-access-to-tenant-level-data"></a>Habilite o acesso a dados de nível de inquilino

Para acessar os dados do Registro de Atividade saqueado, uma conta deve ser atribuída à função incorporada do Leitor de [Monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader) no escopo raiz (/). Essa atribuição deve ser executada por um usuário que tenha a função de Administrador Global com acesso adicional elevado.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevar o acesso a uma conta de administrador global

Para atribuir uma função no escopo raiz (/), você precisará ter a função de Administrador Global com acesso elevado. Esse acesso elevado só deve ser adicionado quando você precisar fazer a atribuição da função e, em seguida, removido quando terminar.

Para obter instruções detalhadas sobre como adicionar e remover a elevação, consulte [Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md).

Depois de elevar seu acesso, sua conta terá a função administradora de acesso do usuário no Azure no escopo raiz. Essa atribuição de função permite que você visualize todos os recursos e atribua acesso em qualquer grupo de assinatura ou gerenciamento no diretório, bem como faça atribuições de função no escopo raiz. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Crie uma nova conta principal de serviço para acessar dados em nível de inquilino

Depois de elevar seu acesso, você pode atribuir as permissões apropriadas a uma conta para que ela possa consultar dados de registro de atividade em nível de inquilino. Essa conta precisará ter a função de [monitoramento do Leitor](../../role-based-access-control/built-in-roles.md#monitoring-reader) incorporada atribuída no escopo raiz do seu inquilino gerenciador.

> [!IMPORTANT]
> A concessão de uma atribuição de função no escopo raiz significa que as mesmas permissões se aplicarão a todos os recursos do inquilino.

Como este é um amplo nível de acesso, recomendamos que você atribua essa função a uma conta principal do serviço, em vez de a um usuário individual ou a um grupo. Além disso, recomendamos as seguintes práticas recomendadas:

- [Crie uma nova conta principal de serviço](../../active-directory/develop/howto-create-service-principal-portal.md) para ser usada apenas para esta função, em vez de atribuir essa função a um diretor de serviço existente usado para outras automaçãos.
- Certifique-se de que este diretor de serviço não tenha acesso a nenhum recurso de cliente delegado.
- [Use um certificado para autenticar](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) e [armazená-lo com segurança no Azure Key Vault](../../key-vault/key-vault-best-practices.md).
- Limite os usuários que têm acesso a agir em nome do diretor do serviço.

Use um dos seguintes métodos para fazer as atribuições de escopo raiz.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Remover acesso elevado para a conta do Administrador Global

Depois de criar sua conta principal de serviço e atribuir a função Leitor de monitoramento no escopo raiz, certifique-se de [remover o acesso elevado](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) para a conta do Administrador Global, pois esse nível de acesso não será mais necessário.

## <a name="query-the-activity-log"></a>Consultar o registro de atividades

Depois de criar uma nova conta principal de serviço com o acesso do Monitorando Reader ao escopo raiz do seu inquilino gerenciador, você pode usá-lo para consultar e relatar a atividade da delegação em seu inquilino. 

[Este script Do Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) pode ser usado para consultar o último 1 dia de atividade e relata quaisquer delegações adicionadas ou removidas (ou tentativas que não foram bem sucedidas). Ele consulta os dados do [Registro de Atividade](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) do Inquilino e, em seguida, constrói os seguintes valores para relatar as delegações adicionadas ou removidas:

- **DelegatedResourceId**: O ID da assinatura delegada ou grupo de recursos
- **CustomerTenantId**: O ID do inquilino do cliente
- **CustomerSubscriptionId**: O ID de assinatura que foi delegado ou que contém o grupo de recursos que foi delegado
- **Status de Delegada do Cliente**: A alteração de status do recurso delegado (com sucesso ou falha)
- **EventTimeStamp**: A data e a hora em que a mudança de delegação foi registrada

Ao consultar esses dados, tenha em mente:

- Se vários grupos de recursos forem delegados em uma única implantação, as entradas separadas serão retornadas para cada grupo de recursos.
- Alterações feitas em uma delegação anterior (como a atualização da estrutura de permissão) serão registradas como uma delegação adicional.
- Como observado acima, uma conta deve ter a função de monitoramento do Leitor no escopo raiz (/) para acessar esses dados em nível de inquilino.
- Você pode usar esses dados em seus próprios fluxos de trabalho e relatórios. Por exemplo, você pode usar a [API http de coletor de dados (visualização pública)](../../azure-monitor/platform/data-collector-api.md) para registrar dados no Azure Monitor a partir de um cliente da API REST e, em seguida, usar grupos de [ação](../../azure-monitor/platform/action-groups.md) para criar notificações ou alertas.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Próximas etapas

- Aprenda a embarcar clientes para o [gerenciamento de recursos delegado do Azure.](../concepts/azure-delegated-resource-management.md)
- Saiba mais sobre [o Azure Monitor](../../azure-monitor/index.yml) e o registro de atividades do [Azure](../../azure-monitor/platform/platform-logs-overview.md).
