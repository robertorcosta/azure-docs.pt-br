---
title: Obter dados de conformidade da política
description: Efeitos e avaliações do Azure Policy determinam a conformidade. Saiba como obter os detalhes de conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: ff50619d7b3d5bc803e8ee8d9e4cbf4389a4191f
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978096"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obter dados de conformidade de recursos do Azure

Os maiores benefícios do Azure Policy são o insight e os controles que ele fornece sobre os recursos em uma assinatura ou [grupo de gerenciamento](../../management-groups/overview.md) de assinaturas. Este controle pode ser desempenhado de várias maneiras diferentes, como impedindo que os recursos sejam criados no local errado, imposição do uso comum e consistente de marcas ou auditoria de recursos existentes para configurações apropriadas. Em todos os casos, os dados são gerados pelo Azure Policy para permitir que você compreenda o estado de conformidade do seu ambiente.

Há várias maneiras de acessar as informações de conformidade geradas por sua política e iniciativas de atribuições:

- Usando o [Portal do Azure](#portal)
- Usando script de [linha de comando](#command-line)

Antes de examinar os métodos de relatório de conformidade, vamos ver quando as informações de conformidade são atualizadas, além da frequência e dos eventos que disparam um ciclo de avaliação.

> [!WARNING]
> Se o estado de conformidade estiver sendo relatado como **não registrado**, verifique se o provedor de recursos **Microsoft. PolicyInsights** está registrado e se o usuário tem as permissões RBAC (controle de acesso baseado em função) apropriadas, conforme descrito em [RBAC in Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Gatilhos de avaliação

Os resultados de um ciclo de avaliação concluído estão disponíveis no Provedor de Recursos `Microsoft.PolicyInsights` por meio das operações `PolicyStates` e `PolicyEvents`. Para obter mais informações sobre as operações da API REST do Azure Policy insights, consulte [Azure Policy insights](/rest/api/policy-insights/).

As avaliações de políticas atribuídas e iniciativas ocorrem como resultado de diversos eventos:

- Uma política ou iniciativa foi recentemente atribuída a um escopo. Demora cerca de 30 minutos para que a atribuição seja aplicada ao escopo definido. Após a aplicação, o ciclo de avaliação começa para recursos dentro desse escopo na política ou iniciativa recentemente atribuída e, dependendo dos efeitos usados pela política ou iniciativa, os recursos serão marcados como em conformidade ou sem conformidade. Uma política ou iniciativa grande avaliada em um grande escopo de recursos pode levar tempo. Como tal, não há nenhuma expectativa predefinida de quando o ciclo de avaliação estará concluído. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no Portal e nos SDKs.

- Uma política ou iniciativa já atribuída a um escopo foi atualizada. O ciclo de avaliação e o tempo desse cenário são iguais aos de uma nova atribuição a um escopo.

- Um recurso é implantado em um escopo com uma atribuição por meio do Gerenciador de Recursos, REST, CLI do Azure ou do Azure PowerShell. Nesse cenário, o evento de efeito (anexar, auditar, negar, implantar) e as informações de status compatíveis com o recurso individual ficam disponíveis no portal e nos SDKs por volta de 15 minutos depois. Este evento não causa uma avaliação de outros recursos.

- Ciclo de avaliação de conformidade padrão. Uma vez a cada 24 horas, as atribuições são automaticamente reavaliadas. Uma política ou iniciativa de muitos recursos pode demorar, portanto, não há uma expectativa predefinida de conclusão do ciclo de avaliação. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no Portal e nos SDKs.

- O provedor de recursos [Configuração de Convidado](../concepts/guest-configuration.md) é atualizado com detalhes de conformidade por um recurso gerenciado.

- Exame sob demanda

### <a name="on-demand-evaluation-scan"></a>Exame de avaliação sob demanda

Um exame de avaliação de uma assinatura ou de um grupo de recursos pode ser iniciado com uma chamada para a API REST. Essa verificação é um processo assíncrono. Dessa forma, o ponto de extremidade REST para iniciar o exame não espera até que o exame seja concluído para responder. Em vez disso, ele fornece um URI para consultar o status da avaliação solicitada.

Em cada URI da API REST, há variáveis usadas que precisam ser substituídas com seus próprios valores:

- `{YourRG}`: substitua pelo nome do grupo de recursos
- `{subscriptionId}`: substitua por sua ID da assinatura

O exame dá suporte à avaliação de recursos em uma assinatura ou em um grupo de recursos. Inicie uma verificação por escopo com um comando **POST** da API REST usando as seguintes estruturas de URI:

- Assinatura

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Grupo de recursos

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

A chamada retorna um status **202 Aceito**. Uma propriedade **Location** com o seguinte formato é incluída no cabeçalho da resposta:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` é estaticamente gerado para o escopo solicitado. Se um escopo já estiver executando um exame sob demanda, um novo exame não será iniciado. Em vez disso, a nova solicitação receberá o mesmo URI de `{ResourceContainerGUID}` **localização** para o status. Um comando **GET** da API REST para o URI de **localização** retorna um **202 Aceito** enquanto a avaliação está em andamento. Quando o exame de avaliação for concluído, ela retornará um status **200 OK**. O corpo de um exame completo é uma resposta JSON com o status:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Como funciona a conformidade

Em uma atribuição, é um recurso **incompatível** se ele não segue as regras de política ou iniciativa.
A tabela a seguir mostra como os diferentes efeitos da política funcionam com a avaliação da condição para o estado de conformidade resultante:

| Estado do recurso | Efeito | Avaliação de política | Estado de conformidade |
| --- | --- | --- | --- |
| Existe | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Sem conformidade |
| Existe | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Compatível |
| Novo | Auditoria, AuditIfNotExist\* | True | Sem conformidade |
| Novo | Auditoria, AuditIfNotExist\* | False | Compatível |

\* Os efeitos de Acrescentar, DeployIfNotExist e AuditIfNotExist exigem que a instrução IF seja TRUE.
Os efeitos também exigem que a condição de existência seja FALSE para não estar em conformidade. Quando TRUE, a condição IF dispara a avaliação da condição de existência para os recursos relacionados.

Por exemplo, suponha que você tenha um grupo de recursos – ContsoRG, com algumas contas de armazenamento (realçadas em vermelho) que são expostas para redes públicas.

![Contas de armazenamento expostas a redes públicas](../media/getting-compliance-data/resource-group01.png)

Neste exemplo, você precisa estar atento aos riscos de segurança. Agora que você criou uma atribuição de política, ela é avaliada em relação a todas as contas de armazenamento no grupo de recursos ContosoRG. Ele audita as três contas de armazenamento não compatíveis, consequentemente alterando seus estados para **Não compatíveis.**

![Auditoria de contas de armazenamento sem conformidade](../media/getting-compliance-data/resource-group03.png)

Além de **Compatível** e **Não compatível**, as políticas e os recursos têm três outros estados:

- **Conflitante**: duas ou mais políticas existem com regras conflitantes. Por exemplo, duas políticas que acrescentam a mesma tag com valores diferentes.
- **Não foi iniciado**: O ciclo de avaliação não foi iniciado para a política ou o recurso.
- **Não registrado**: o Provedor de Recursos do Azure Policy não foi registrado ou a conta conectada não tem permissão para ler dados de conformidade.

Azure Policy usa os campos de **tipo** e **nome** na definição para determinar se um recurso é uma correspondência. Quando o recurso é correspondido, é considerado aplicável e com um status **Compatível** ou **Não compatível**. Se o **tipo** ou o **nome** for a única propriedade na definição, então todos os recursos serão considerados aplicáveis e serão avaliados.

A porcentagem de conformidade é determinada pela divisão de **recursos** Compatíveis pelo _total de recursos_.
_O total de recursos_ é definido como a soma dos recursos **Em conformidade**, **Não-compatível** e **Conflito**. Os números gerais de conformidade são a soma de recursos distintos que são **Compatíveis** divididos pela soma de todos os recursos distintos. Na imagem abaixo, existem 20 recursos distintos que são aplicáveis e apenas um é **Não compatível**. A conformidade geral do recurso é 95 (19 de 20).

![Exemplo de conformidade de política da página conformidade](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

O Portal do Azure apresenta uma experiência gráfica de visualização e compreensão do estado de conformidade em seu ambiente. Na página **Política**, a opção **Visão Geral** fornece detalhes dos escopos disponíveis sobre a conformidade das políticas e iniciativas. Junto com o estado de conformidade e com a contagem por atribuição, ela contém um gráfico mostrando a conformidade nos últimos sete dias. A página **Conformidade** contém muitas dessas mesmas informações (exceto o gráfico), mas fornece opções adicionais de filtragem e classificação.

![Exemplo de página de conformidade Azure Policy](../media/getting-compliance-data/compliance-page.png)

Como uma política ou iniciativa pode ser atribuída a escopos diferentes, a tabela inclui o escopo de cada atribuição e o tipo de definição que foi atribuído. O número de recursos não compatíveis e políticas não compatíveis para cada atribuição também são fornecidos. Clicar em uma política ou iniciativa na tabela fornece uma análise mais profunda sobre a conformidade dessa atribuição específica.

![Exemplo de Azure Policy página de detalhes de conformidade](../media/getting-compliance-data/compliance-details.png)

A lista de recursos na guia **Conformidade de recursos** mostra o status de avaliação de recursos existentes para a atribuição atual. O padrão da guia é **Não compatível**, mas pode ser filtrado.
Os eventos (acrescentar, auditar, negar, implantar) disparados pela solicitação para criar um recurso são mostrados na guia **Eventos**.

![Exemplo de eventos de conformidade de Azure Policy](../media/getting-compliance-data/compliance-events.png)

Clique com o botão direito na linha do evento para obter mais detalhes e selecione **Mostrar logs de atividade**. A página de registro de atividade é aberta e pré-filtrada na pesquisa, mostrando detalhes da atribuição e dos eventos. O log de atividades fornece um contexto adicional e informações sobre esses eventos.

![Exemplo de log de atividades de conformidade Azure Policy](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Entender a não conformidade

<a name="change-history-preview"></a>

Quando um recurso é determinado como **não compatível**, há muitas razões possíveis. Para determinar o motivo pelo qual um recurso **não está em conformidade** ou para localizar a alteração responsável, consulte [determinar a não conformidade](./determine-non-compliance.md).

## <a name="command-line"></a>Linha de comando

As mesmas informações disponíveis no portal podem ser recuperadas com a API REST (incluindo com [ARMClient](https://github.com/projectkudu/ARMClient)) ou o Azure PowerShell. Para obter detalhes completos sobre a API REST, consulte a referência do [Azure Policy insights](/rest/api/policy-insights/) . As páginas de referência da API REST têm um botão verde "Experimente" em cada operação, permitindo que você experimente diretamente no navegador.

Para usar os exemplos a seguir no Azure PowerShell, construa um token de autenticação com este exemplo de código. Em seguida, substitua o $restUri pela cadeia de caracteres nos exemplos para recuperar um objeto JSON que pode ser analisado.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Resumir resultados

Com a API REST, o resumo pode ser executado por contêiner, por definição ou por atribuição. Aqui está um exemplo de resumo no nível da assinatura usando o resumo do Azure Policy Insight [para a assinatura](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A saída resume a assinatura. No exemplo abaixo, a conformidade resumida está sob **value.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. Essa solicitação fornece mais detalhes, incluindo cada atribuição que compõem os números sem conformidade e as informações de definição de cada atribuição. Cada objeto de política na hierarquia fornece um **queryResultsUri** que pode ser usado para obter detalhes adicionais nesse nível.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Pesquisar recursos

No exemplo acima, **value.policyAssignments.policyDefinitions.results.queryResultsUri** fornece um URI de exemplo para obter todos os recursos sem conformidade de uma definição de política específica. Observando o valor **$filter**, IsCompliant é igual (eq) a false, PolicyAssignmentId é especificado para a definição de política e, em seguida, o próprio PolicyDefinitionId. O motivo para incluir o PolicyAssignmentId no filtro é porque PolicyDefinitionId pode existir em várias atribuições ou iniciativas de política com diferentes escopos. Ao especificar o PolicyAssignmentId e o PolicyDefinitionId, podemos ser explícitos com relação aos resultados que procuramos. Anteriormente, para PolicyStates usamos o **mais recente**, que define automaticamente uma janela de tempo **de** e **até** das últimas 24 horas.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

A resposta de exemplo abaixo foi reduzida para um único recurso não compatível para ter brevidade. A resposta detalhada tem várias partes de dados sobre o recurso, a política ou a iniciativa e a atribuição. Observe que você também pode ver quais parâmetros de atribuição foram passados para a definição de política.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Exibir eventos

Quando um recurso é criado ou atualizado, um resultado da avaliação da política é gerado. Os resultados são chamados _Eventos de Política_. Use o URI a seguir para exibir eventos recentes de política associados à assinatura.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Seus resultados devem se parecer com o exemplo a seguir:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Para obter mais informações sobre como consultar eventos de política, consulte o artigo de referência de [eventos de Azure Policy](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

O módulo Azure PowerShell para Azure Policy está disponível no Galeria do PowerShell como [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Usando o PowerShellGet, você pode instalar o módulo usando `Install-Module -Name Az.PolicyInsights` (verifique se você tem a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) instalada):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

O módulo tem os seguintes cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exemplo: obter o estado de resumo da política superior atribuída com o maior número de recursos sem conformidade.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: obter o registro de estado para o recurso avaliada mais recentemente (o padrão é pelo carimbo de data/hora em ordem decrescente).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obter os detalhes para todos os recursos de rede virtual sem conformidade.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obter eventos relacionados aos recursos de rede virtual sem conformidade que ocorreram após uma data específica.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

O campo **PrincipalOid** pode ser usado para obter um usuário específico com o cmdlet do Azure PowerShell `Get-AzADUser`. Substitua **{principalOid}** pela resposta obtida do exemplo anterior.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Logs do Azure Monitor

Se você tiver um [espaço de trabalho log Analytics](../../../log-analytics/log-analytics-overview.md) com `AzureActivity` da [solução análise do log de atividades](../../../azure-monitor/platform/activity-log-collect.md) vinculada à sua assinatura, também poderá exibir resultados de não conformidade do ciclo de avaliação usando consultas simples de Kusto e a tabela `AzureActivity`. Com os detalhes nos logs do Azure Monitor, os alertas poderão ser configurados para inspecionar a não conformidade.


![Azure Policy conformidade usando logs de Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Próximas etapas

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).