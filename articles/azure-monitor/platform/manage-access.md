---
title: Gerenciar workspaces do Log Analytics no Azure Monitor | Microsoft Docs
description: Você pode gerenciar o acesso aos dados armazenados em um espaço de trabalho do Log Analytics no Azure Monitor usando recursos, espaço de trabalho ou permissões de nível de tabela. Este artigo detalha como completar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 1e559309b8e8d9768ca2f79dabfb01ec6086a961
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348714"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Gerencie o acesso a dados de registro e espaços de trabalho no Azure Monitor

O Azure Monitor armazena dados [de registro](data-platform-logs.md) em um espaço de trabalho do Log Analytics. Um espaço de trabalho é um contêiner que inclui informações de dados e configuração. Para gerenciar o acesso aos dados de log, você executa várias tarefas administrativas relacionadas ao seu espaço de trabalho.

Este artigo explica como gerenciar o acesso aos logs e administrar os espaços de trabalho que os contêm, incluindo como conceder acesso a: 

* O espaço de trabalho usando permissões de espaço de trabalho.
* Usuários que precisam acessar dados de log de recursos específicos usando o RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure).
* Usuários que precisam acessar dados de registro em uma tabela específica no espaço de trabalho usando o Azure RBAC.

## <a name="configure-access-control-mode"></a>Configurar o modo de controle de acesso

Você pode visualizar o [modo de controle](design-logs-deployment.md) de acesso configurado em um espaço de trabalho a partir do portal Azure ou com o Azure PowerShell.  Você pode alterar essa configuração usando um dos seguintes métodos suportados:

* Portal do Azure

* Azure PowerShell

* Modelo do Azure Resource Manager

### <a name="from-the-azure-portal"></a>No portal do Azure

Você pode visualizar o modo de controle de acesso do espaço de trabalho atual na página **Visão geral** para o espaço de trabalho no menu do espaço de trabalho **Log Analytics.**

![Exibir modo de controle de acesso ao espaço de trabalho](media/manage-access/view-access-control-mode.png)

1. Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal Azure, selecione espaços de trabalho do Log Analytics > seu espaço de trabalho.

Você pode alterar essa configuração a partir da página **Propriedades** do espaço de trabalho. A alteração da configuração será desativada se você não tiver permissões para configurar o espaço de trabalho.

![Alterar o modo de acesso ao espaço de trabalho](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Usando o PowerShell

Use o seguinte comando para examinar o modo de controle de acesso para todos os espaços de trabalho na assinatura:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

O resultado deve ser assim:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Um valor `False` de meios que o espaço de trabalho é configurado com o modo de acesso ao contexto do espaço de trabalho.  Um valor `True` de meios que o espaço de trabalho é configurado com o modo de acesso ao contexto de recursos.

> [!NOTE]
> Se um espaço de trabalho é devolvido sem um valor booleano e está em branco, isso também corresponde aos resultados de um `False` valor.
>

Use o script a seguir para definir o modo de controle de acesso para um espaço de trabalho específico para a permissão de contexto de recurso:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Use o seguinte script para definir o modo de controle de acesso para todos os espaços de trabalho na assinatura da permissão de contexto de recurso:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Usando um modelo de gerenciador de recursos

Para configurar o modo de acesso em um modelo do Azure Resource Manager, defina o recurso **enableLogAccessUsingOnlyResourcePermissions** no espaço de trabalho como um dos seguintes valores.

* **falso**: Defina o espaço de trabalho para permissões de contexto de espaço de trabalho. Esta é a configuração padrão se o sinalizador não estiver definido.
* **verdadeiro**: Defina o espaço de trabalho para permissões de contexto de recursos.

## <a name="manage-access-using-workspace-permissions"></a>Gerencie o acesso usando permissões de espaço de trabalho

Cada workspace pode ter várias contas associadas e cada conta pode ter acesso a vários workspaces. O acesso é gerenciado usando [o acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md).

As atividades a seguir também exigem permissões do Azure:

|Ação |Permissões do Azure necessárias |Observações |
|-------|-------------------------|------|
| Adicionando e removendo soluções de monitoramento | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Essas permissões precisam ser concedidas no nível de assinatura ou no grupo de recursos. |
| Alterar o tipo de preço | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Exibir dados nos blocos de solução *Backup* e *Site Recovery* | Administrador/coadministrador | Acessa recursos implantados usando o modelo de implantação clássico |
| Criar um workspace no portal do Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Veja as propriedades básicas do espaço de trabalho e insira a lâmina do espaço de trabalho no portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Registros de consulta usando qualquer interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Acesse todos os tipos de log usando consultas | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Acesse uma tabela de log específica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Leia as chaves do espaço de trabalho para permitir o envio de logs para este espaço de trabalho | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gerenciar acesso usando permissões do Azure

Para conceder acesso ao espaço de trabalho do Log Analytics usando permissões do Azure, execute as etapas em [Usar atribuições de função para gerenciar o acesso aos recursos de sua assinatura do Azure](../../role-based-access-control/role-assignments-portal.md). Por exemplo, funções personalizadas, veja [Exemplo de funções personalizadas](#custom-role-examples)

O Azure tem duas funções de usuário predefinidas para workspaces do Log Analytics:

* Leitor do Log Analytics
* Colaborador do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:

* Exibir e pesquisar todos os dados de monitoramento
* Exiba configurações de monitoramento, incluindo exibir a configuração do diagnóstico do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| Type    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de exibir todos os recursos do Azure e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, permite permissões completas e irrestritas para ler as configurações do espaço de trabalho e executar a consulta sobre os dados. Veja mais opções granulares acima. |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Preterido, não há necessidade de atribuí-los aos usuários. |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Preterido, não há necessidade de atribuí-los aos usuários. |
| Ação | `Microsoft.Support/*` | Capacidade de abrir casos de suporte |
|Nenhuma Ação | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave do workspace necessária para usar a API da coleta de dados e instalar os agentes. Isso impede que o usuário adicione os novos recursos para o workspace |

Os membros da função *Colaborador do Log Analytics* podem:

* Inclui todos os privilégios da *função Log Analytics Reader,* permitindo que o usuário leia todos os dados de monitoramento
* Crie e configure contas de automação
* Adicionar e remover soluções de gerenciamento

    > [!NOTE]
    > Para executar com êxito essas duas ações, essa permissão deve ser concedida no nível do grupo de recursos ou da assinatura.

* Leia as chaves da conta de armazenamento
* Configure a coleta de logs do Azure Storage
* Editar configurações de monitoramento dos recursos do Azure, incluindo
  * Adicionar a extensão VM às VMs
  * Configurar o diagnóstico do Azure em todos os recursos do Azure

> [!NOTE]
> Você pode usar a capacidade de adicionar uma extensão da máquina virtual a uma máquina virtual para ter controle total sobre uma máquina virtual.

A função de Colaborador do Log Analytics inclui as seguintes ações do Azure:

| Permissão | Descrição |
| ---------- | ----------- |
| `*/read`     | Capacidade de exibir todos os recursos e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, permite permissões completas e irrestritas para ler a configuração do espaço de trabalho e executar a consulta sobre os dados. Veja mais opções granulares acima. |
| `Microsoft.Automation/automationAccounts/*` | Capacidade de criar e configurar Contas de automação do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover as extensões da máquina virtual, incluindo a extensão do Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Exiba a chave da conta de armazenamento. Necessário para configurar o Log Analytics para ler os logs das contas de armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover as regras de alerta |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as configurações de diagnóstico nos recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicione, atualize e remova a configuração para espaços de trabalho do Log Analytics. Para editar configurações avançadas `Microsoft.OperationalInsights/workspaces/write`do espaço de trabalho, as necessidades do usuário . |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gerenciamento |
| `Microsoft.Resources/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |

Para adicionar e remover usuários de uma função de usuário, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Use essas funções para conceder acesso aos usuários em escopos diferentes:

* Assinatura – acesso a todos os workspaces na assinatura
* Grupo de Recursos - acesso a todo workspace no grupo de recursos
* Recurso - acesso somente ao workspace especificado

Recomendamos a realização de atribuições no nível de recursos (espaço de trabalho) para garantir um controle de acesso preciso. Use as [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recursos

Quando os usuários consultarem logs de um espaço de trabalho usando acesso ao contexto de recursos, eles terão as seguintes permissões no recurso:

| Permissão | Descrição |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de visualizar todos os dados de log para o recurso.  |
| `Microsoft.Insights/diagnosticSettings/write` | Capacidade de configurar a configuração de diagnósticos para permitir a configuração de logs para este recurso. |

`/read`a permissão geralmente é concedida a partir de uma função que [Contributor](../../role-based-access-control/built-in-roles.md#contributor) inclui _ \*/read ou_ _\*_ permissões, como as funções [de Leitor](../../role-based-access-control/built-in-roles.md#reader) e Contribuinte incorporado. Funções personalizadas que incluem ações específicas ou funções incorporadas dedicadas podem não incluir essa permissão.

Consulte [Definindo o controle de acesso por tabela](#table-level-rbac) abaixo se você quiser criar um controle de acesso diferente para diferentes tabelas.

## <a name="custom-role-examples"></a>Exemplos de função personalizadas

1. Para conceder a um usuário acesso a dados de log de seus recursos, execute o seguinte:

    * Configure o modo de controle de acesso ao espaço de trabalho para **usar permissões de espaço de trabalho ou recursos**

    * Conceda `*/read` `Microsoft.Insights/logs/*/read` aos usuários ou permissões aos seus recursos. Se eles já tiverem a função [de Leitor de Análise de Log](../../role-based-access-control/built-in-roles.md#reader) no espaço de trabalho, basta.

2. Para conceder a um usuário acesso a dados de log de seus recursos e configurar seus recursos para enviar logs para o espaço de trabalho, execute o seguinte:

    * Configure o modo de controle de acesso ao espaço de trabalho para **usar permissões de espaço de trabalho ou recursos**

    * Conceda aos usuários as seguintes `Microsoft.OperationalInsights/workspaces/read` `Microsoft.OperationalInsights/workspaces/sharedKeys/action`permissões no espaço de trabalho: e . Com essas permissões, os usuários não podem realizar quaisquer consultas de nível de espaço de trabalho. Eles só podem enumerar o espaço de trabalho e usá-lo como destino para configurações de diagnóstico ou configuração do agente.

    * Conceda aos usuários as seguintes permissões aos seus recursos: `Microsoft.Insights/logs/*/read` e `Microsoft.Insights/diagnosticSettings/write`. Se eles já tiverem a função [de Contribuinte do Log Analytics,](../../role-based-access-control/built-in-roles.md#contributor) atribuído à função Reader ou permissões concedidas `*/read` neste recurso, basta.

3. Para conceder a um usuário acesso a dados de log de seus recursos sem ser capaz de ler eventos de segurança e enviar dados, execute o seguinte:

    * Configure o modo de controle de acesso ao espaço de trabalho para **usar permissões de espaço de trabalho ou recursos**

    * Conceda aos usuários as seguintes permissões aos seus recursos: `Microsoft.Insights/logs/*/read`.

    * Adicione o seguinte NonAction para impedir que `Microsoft.Insights/logs/SecurityEvent/read`os usuários leiam o tipo SecurityEvent: . A NonAction deve estar na mesma função personalizada que`Microsoft.Insights/logs/*/read`a ação que fornece a permissão de leitura (). Se o usuário inerente à ação de leitura de outra função atribuída a esse recurso ou ao grupo de assinatura ou recurso, ele poderá ler todos os tipos de log. Isso também é verdade `*/read`se eles herdarem , que existem, por exemplo, com a função de Leitor ou Contribuinte.

4. Para conceder a um usuário acesso a dados de log de seus recursos e ler todos os dados de login do Azure AD e ler dados de registro de solução de gerenciamento de atualização do espaço de trabalho, execute o seguinte:

    * Configure o modo de controle de acesso ao espaço de trabalho para **usar permissões de espaço de trabalho ou recursos**

    * Conceda aos usuários as seguintes permissões no espaço de trabalho: 

        * `Microsoft.OperationalInsights/workspaces/read`– necessário para que o uso possa enumerar o espaço de trabalho e abrir a lâmina do espaço de trabalho no portal Azure
        * `Microsoft.OperationalInsights/workspaces/query/read`– necessário para cada usuário que possa executar consultas
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– para poder ler os registros de login do Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– para poder ler os registros de soluções do Update Management
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– para poder ler os registros de soluções do Update Management
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– para poder ler os registros de gerenciamento de atualização
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– é necessário ser capaz de usar a solução Update Management
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– é necessário ser capaz de usar a solução Update Management

    * Conceda aos usuários as seguintes permissões aos seus `*/read` `Microsoft.Insights/logs/*/read`recursos: , atribuídas à função Leitor, ou . 

## <a name="table-level-rbac"></a>Nível de tabela RBAC

**O RBAC de nível de tabela** permite definir um controle mais granular aos dados em um espaço de trabalho do Log Analytics, além das outras permissões. Este controle permite definir tipos de dados específicos que são acessíveis apenas a um conjunto específico de usuários.

Você implementa o controle de acesso à tabela com [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) para conceder acesso a [tabelas](../log-query/logs-structure.md) específicas no espaço de trabalho. Essas funções são aplicadas a espaços de trabalho com [modos](design-logs-deployment.md#access-control-mode) de controle de acesso de contexto de espaço de trabalho ou de contexto de recursos, independentemente do modo de [acesso](design-logs-deployment.md#access-mode)do usuário .

Crie uma [função personalizada](../../role-based-access-control/custom-roles.md) com as seguintes ações para definir o acesso ao controle de acesso à tabela.

* Para conceder acesso a uma tabela, inclua-a na seção **Ações** da definição de função. Para subtrair o acesso das **Ações permitidas,** inclua-o na seção **NotActions.**
* Use o Microsoft.OperationalInsights/workspaces/query/* para especificar todas as tabelas.

Por exemplo, para criar uma função com acesso às _tabelas Heartbeat_ e _AzureActivity,_ crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para criar uma função com acesso apenas à tabela _SecurityBaseline,_ crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Logs personalizados

 Os logs personalizados são criados a partir de fontes de dados, como logs personalizados e API http de coletor de dados. A maneira mais fácil de identificar o tipo de registro é verificando as [tabelas listadas](../log-query/get-started-portal.md#understand-the-schema)em Registros Personalizados no esquema de log .

 No momento, você não pode conceder acesso a registros personalizados individuais, mas pode conceder acesso a todos os registros personalizados. Para criar uma função com acesso a todos os logs personalizados, crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>Considerações

* Se um usuário receber permissão global de leitura com _ \*_ as funções padrão de Leitor ou Contribuinte que incluam a ação /read, ele substituirá o controle de acesso por tabela e dará acesso a todos os dados de log.
* Se um usuário tiver acesso por tabela, mas nenhuma outra permissão, ele poderá acessar dados de log da API, mas não do portal Azure. Para fornecer acesso a partir do portal Azure, use o Log Analytics Reader como sua função base.
* Os administradores da assinatura terão acesso a todos os tipos de dados, independentemente de quaisquer outras configurações de permissão.
* Os proprietários de espaços de trabalho são tratados como qualquer outro usuário para controle de acesso por tabela.
* Recomendamos atribuir funções a grupos de segurança em vez de usuários individuais para reduzir o número de atribuições. Isso também ajudará você a usar as ferramentas de gerenciamento de grupo existentes para configurar e verificar o acesso.

## <a name="next-steps"></a>Próximas etapas

* Consulte a [visão geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para reunir dados de computadores no datacenter ou outro ambiente de nuvem.

* Consulte [Coletar dados sobre máquinas virtuais Do Zure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar a coleta de dados das VMs do Azure.
