---
title: Gerenciar espaços de trabalho do Log Analytics no Azure Monitor | Microsoft Docs
description: Você pode gerenciar o acesso aos dados armazenados em um espaço de trabalho do Log Analytics no Azure Monitor usando permissões de recurso, espaço de trabalho ou nível de tabela. Este artigo fornece detalhes sobre como concluir.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: magoedte
ms.openlocfilehash: 2f9c50053fca73aeee0ed9a286b4c286486bac86
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532335"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Gerenciar o acesso a dados de log e espaços de trabalho no Azure Monitor

Azure Monitor armazena dados de [log](data-platform-logs.md) em um espaço de trabalho log Analytics. Um espaço de trabalho é um contêiner que inclui informações de dados e de configuração. Para gerenciar o acesso aos dados de log, você executa várias tarefas administrativas relacionadas ao seu espaço de trabalho.

Este artigo explica como gerenciar o acesso aos logs e administrar os espaços de trabalho que os contêm, incluindo como conceder acesso a: 

* O espaço de trabalho usando permissões de espaço de trabalho.
* Usuários que precisam de acesso a dados de log de recursos específicos usando o RBAC (controle de acesso baseado em função) do Azure.
* Usuários que precisam de acesso a dados de log em uma tabela específica no espaço de trabalho usando o RBAC do Azure.

## <a name="configure-access-control-mode"></a>Configurar o modo de controle de acesso

Você pode exibir o modo de controle de acesso configurado em um espaço de trabalho do portal do Azure ou com Azure PowerShell.  Você pode alterar essa configuração usando um dos seguintes métodos com suporte:

* Portal do Azure

* Azure PowerShell

* Modelo de Azure Resource Manager

### <a name="from-the-azure-portal"></a>da portal do Azure

Você pode exibir o modo de controle de acesso do espaço de trabalho atual na página **visão geral** do espaço de trabalho no menu **log Analytics espaço de trabalho** .

![Exibir modo de controle de acesso do espaço de trabalho](media/manage-access/view-access-control-mode.png)

1. Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, selecione Log Analytics espaços de trabalho > seu espaço de trabalho.

Você pode alterar essa configuração na página **Propriedades** do espaço de trabalho. A alteração da configuração será desabilitada se você não tiver permissões para configurar o espaço de trabalho.

![Alterar modo de acesso do espaço de trabalho](media/manage-access/change-access-control-mode.png)

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

Um valor de `False` significa que o espaço de trabalho está configurado com o modo de acesso de contexto de espaço de trabalho.  Um valor de `True` significa que o espaço de trabalho está configurado com o modo de acesso de contexto de recurso.

> [!NOTE]
> Se um espaço de trabalho for retornado sem um valor booliano e estiver em branco, isso também corresponderá aos resultados de um valor `False`.
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

Use o script a seguir para definir o modo de controle de acesso para todos os espaços de trabalho na assinatura para a permissão de contexto de recurso:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="using-a-resource-manager-template"></a>Usando um modelo do Resource Manager

Para configurar o modo de acesso em um modelo de Azure Resource Manager, defina o sinalizador de recurso **enableLogAccessUsingOnlyResourcePermissions** no espaço de trabalho para um dos valores a seguir.

* **false**: defina o espaço de trabalho para permissões de contexto de espaço de trabalho. Essa será a configuração padrão se o sinalizador não estiver definido.
* **true**: defina o espaço de trabalho para permissões de contexto de recurso.

## <a name="manage-access-using-workspace-permissions"></a>Gerenciar o acesso usando permissões de espaço de trabalho

Cada espaço de trabalho pode ter várias contas associadas a ele, e cada conta pode ter acesso a vários espaços de trabalho. O acesso é gerenciado usando o [acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md).

As seguintes atividades também exigem permissões do Azure:

|Ação |Permissões do Azure necessárias |Observações |
|-------|-------------------------|------|
| Adicionando e removendo soluções de monitoramento | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Essas permissões precisam ser concedidas no nível do grupo de recursos ou da assinatura. |
| Alterando o tipo de preço | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Exibindo dados nos blocos de solução de *backup* e *site Recovery* | Administrador/coadministrador | Acessa recursos implantados usando o modelo de implantação clássico |
| Criando um espaço de trabalho no portal do Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Exibir propriedades básicas do espaço de trabalho e inserir a folha do espaço de trabalho no portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Consultar logs usando qualquer interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Acessar todos os tipos de log usando consultas | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Acessar uma tabela de log específica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Ler as chaves do espaço de trabalho para permitir o envio de logs para este espaço de trabalho | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gerenciar o acesso usando as permissões do Azure

Para conceder acesso ao espaço de trabalho do Log Analytics usando as permissões do Azure, siga as etapas em [usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md). Por exemplo, funções personalizadas, consulte [funções personalizadas de exemplo](#custom-role-examples)

O Azure tem duas funções de usuário internas para Log Analytics espaços de trabalho:

* Leitor de Log Analytics
* Colaborador de Log Analytics

Os membros da função *leitor de log Analytics* podem:

* Exibir e Pesquisar todos os dados de monitoramento
* Exibir configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure.

A função leitor de Log Analytics inclui as seguintes ações do Azure:

| Tipo    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de exibir todos os recursos e a configuração de recursos do Azure. Inclui a exibição: <br> Status da extensão da máquina virtual <br> Configuração do diagnóstico do Azure em recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, ele permite permissões totalmente irrestritas para ler as configurações do espaço de trabalho e executar a consulta nos dados. Veja as opções mais granulares acima. |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Preterido, sem necessidade de atribuí-los aos usuários. |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Preterido, sem necessidade de atribuí-los aos usuários. |
| Ação | `Microsoft.Support/*` | Capacidade de abrir casos de suporte |
|Não ação | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave do espaço de trabalho necessária para usar a API de coleta de dados e para instalar agentes. Isso impede que o usuário adicione novos recursos ao espaço de trabalho |

Os membros da função *colaborador de log Analytics* podem:

* Inclui todos os privilégios da *função leitor de log Analytics*, permitindo que o usuário Leia todos os dados de monitoramento
* Criar e configurar contas de automação
* Adicionar e remover soluções de gerenciamento

    > [!NOTE]
    > Para executar as duas últimas ações com êxito, essa permissão precisa ser concedida no nível do grupo de recursos ou da assinatura.

* Ler chaves de conta de armazenamento
* Configurar a coleta de logs do armazenamento do Azure
* Editar configurações de monitoramento para recursos do Azure, incluindo
  * Adicionando a extensão de VM às VMs
  * Configurando o diagnóstico do Azure em todos os recursos do Azure

> [!NOTE]
> Você pode usar a capacidade de adicionar uma extensão de máquina virtual a uma máquina virtual para obter controle total sobre uma máquina virtual.

A função colaborador de Log Analytics inclui as seguintes ações do Azure:

| Permissão | Descrição |
| ---------- | ----------- |
| `*/read`     | Capacidade de exibir todos os recursos e a configuração de recursos. Inclui a exibição: <br> Status da extensão da máquina virtual <br> Configuração do diagnóstico do Azure em recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, ele permite permissões totalmente irrestritas para ler a configuração do espaço de trabalho e executar a consulta nos dados. Veja as opções mais granulares acima. |
| `Microsoft.Automation/automationAccounts/*` | Capacidade de criar e configurar contas de automação do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover extensões de máquina virtual, incluindo a extensão de Microsoft Monitoring Agent e a extensão de Agente do OMS para Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Exiba a chave da conta de armazenamento. Necessário para configurar Log Analytics para ler logs de contas de armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover regras de alerta |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover configurações de diagnóstico nos recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicionar, atualizar e remover a configuração de Log Analytics espaços de trabalho. Para editar as configurações avançadas do espaço de trabalho, o usuário precisa `Microsoft.OperationalInsights/workspaces/write`. |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gerenciamento |
| `Microsoft.Resources/deployments/*` | Criar e excluir implantações. Necessário para adicionar e remover soluções, espaços de trabalho e contas de automação |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Criar e excluir implantações. Necessário para adicionar e remover soluções, espaços de trabalho e contas de automação |

Para adicionar e remover usuários de uma função de usuário, é necessário ter `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write` permissão.

Use essas funções para conceder aos usuários acesso em escopos diferentes:

* Assinatura – acesso a todos os espaços de trabalho na assinatura
* Grupo de recursos-acesso a todos os espaços de trabalho no grupo de recursos
* Recurso-acesso somente ao espaço de trabalho especificado

É recomendável executar atribuições no nível de recurso (espaço de trabalho) para garantir o controle de acesso preciso. Use [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recurso

Quando os usuários consultam logs de um espaço de trabalho usando acesso de contexto de recurso, eles terão as seguintes permissões no recurso:

| Permissão | Descrição |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de exibir todos os dados de log para o recurso.  |
| `Microsoft.Insights/diagnosticSettings/write` | Capacidade de definir a configuração de diagnóstico para permitir a configuração de logs para este recurso. |

a permissão `/read` geralmente é concedida de uma função que inclui as permissões _\*/ler ou_ _\*_ , como as funções de [leitor](../../role-based-access-control/built-in-roles.md#reader) e [colaborador](../../role-based-access-control/built-in-roles.md#contributor) internas. Funções personalizadas que incluem ações específicas ou funções internas dedicadas podem não incluir essa permissão.

Consulte [definindo o controle de acesso por tabela](#table-level-rbac) abaixo se você quiser criar um controle de acesso diferente para tabelas diferentes.

## <a name="custom-role-examples"></a>Exemplos de função personalizada

1. Para conceder a um usuário acesso aos dados de log de seus recursos, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda permissões `*/read` ou `Microsoft.Insights/logs/*/read` aos usuários para seus recursos. Se eles já tiverem sido atribuídos à função [leitor de log Analytics](../../role-based-access-control/built-in-roles.md#reader) no espaço de trabalho, será suficiente.

2. Para conceder a um usuário acesso aos dados de log de seus recursos e configurar seus recursos para enviar logs para o espaço de trabalho, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda aos usuários as seguintes permissões no espaço de trabalho: `Microsoft.OperationalInsights/workspaces/read` e `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Com essas permissões, os usuários não podem executar nenhuma consulta no nível do espaço de trabalho. Eles só podem enumerar o espaço de trabalho e usá-lo como um destino para configurações de diagnóstico ou configuração do agente.

    * Conceda aos usuários as seguintes permissões para seus recursos: `Microsoft.Insights/logs/*/read` e `Microsoft.Insights/diagnosticSettings/write`. Se eles já tiverem sido atribuídos à função de [colaborador de log Analytics](../../role-based-access-control/built-in-roles.md#contributor) , atribuída a função de leitor ou permissões de `*/read` concedidas a esse recurso, isso será suficiente.

3. Para conceder a um usuário acesso aos dados de log de seus recursos sem a capacidade de ler eventos de segurança e enviar dados, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda aos usuários as seguintes permissões para seus recursos: `Microsoft.Insights/logs/*/read`.

    * Adicione a seguinte não ação para impedir que os usuários leiam o tipo SecurityEvent: `Microsoft.Insights/logs/SecurityEvent/read`. A não ação deve estar na mesma função personalizada que a ação que fornece a permissão de leitura (`Microsoft.Insights/logs/*/read`). Se o usuário inerente à ação de leitura de outra função atribuída a esse recurso ou à assinatura ou ao grupo de recursos, ele poderá ler todos os tipos de log. Isso também é verdadeiro se eles herdam `*/read`, que existem por exemplo, com a função de leitor ou colaborador.

4. Para conceder a um usuário acesso a dados de log de seus recursos e ler todos os dados de logon do Azure AD e ler Gerenciamento de Atualizações de log da solução no espaço de trabalho, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda aos usuários as seguintes permissões no espaço de trabalho: 

        * `Microsoft.OperationalInsights/workspaces/read` – necessário para que o uso possa enumerar o espaço de trabalho e abrir a folha do espaço de trabalho no portal do Azure
        * `Microsoft.OperationalInsights/workspaces/query/read` – obrigatório para cada usuário que pode executar consultas
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – para poder ler os logs de entrada do Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – para poder ler Gerenciamento de Atualizações logs de solução
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – para poder ler Gerenciamento de Atualizações logs de solução
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – para poder ler os logs de gerenciamento de atualizações
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – necessário para poder usar a solução Gerenciamento de Atualizações
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – necessário para poder usar a solução Gerenciamento de Atualizações

    * Conceda aos usuários as seguintes permissões para seus recursos: `*/read`, atribuído à função de leitor ou `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>RBAC de nível de tabela

O **RBAC em nível de tabela** permite que você defina um controle mais granular para dados em um espaço de trabalho log Analytics além das outras permissões. Esse controle permite que você defina tipos de dados específicos que são acessíveis somente para um conjunto específico de usuários.

Você implementa o controle de acesso de tabela com as [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) para conceder ou negar acesso a [tabelas](../log-query/logs-structure.md) específicas no espaço de trabalho. Essas funções são aplicadas a espaços de trabalho com o contexto do espaço de trabalho ou os [modos de controle de acesso](design-logs-deployment.md#access-control-mode) de contexto de recurso, independentemente do modo de [acesso](design-logs-deployment.md#access-mode)do usuário.

Crie uma [função personalizada](../../role-based-access-control/custom-roles.md) com as ações a seguir para definir o acesso ao controle de acesso à tabela.

* Para conceder acesso a uma tabela, inclua-a na seção **ações** da definição de função.
* Para negar o acesso a uma tabela, inclua-a na seção não- **ações** da definição de função.
* Use * para especificar todas as tabelas.

Por exemplo, para criar uma função com acesso às tabelas _Heartbeat_ e _AzureActivity_ , crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para criar uma função com acesso somente a _SecurityBaseline_ e nenhuma outra tabela, crie uma função personalizada usando as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Logs personalizados

 Os logs personalizados são criados a partir de fontes de dados como logs personalizados e API do coletor de dados HTTP. A maneira mais fácil de identificar o tipo de log é verificando as tabelas listadas em [logs personalizados no esquema de log](../log-query/get-started-portal.md#understand-the-schema).

 No momento, não é possível conceder ou negar acesso a logs personalizados individuais, mas você pode conceder ou negar acesso a todos os logs personalizados. Para criar uma função com acesso a todos os logs personalizados, crie uma função personalizada usando as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considere

* Se um usuário receber permissão de leitura global com as funções leitor padrão ou colaborador que incluem a ação _\*/leitura_ , ele substituirá o controle de acesso por tabela e dará acesso a todos os dados de log.
* Se um usuário receber acesso por tabela, mas nenhuma outra permissão, ele poderá acessar dados de log da API, mas não do portal do Azure. Para fornecer acesso do portal do Azure, use o leitor de Log Analytics como sua função base.
* Os administradores da assinatura terão acesso a todos os tipos de dados, independentemente de quaisquer outras configurações de permissão.
* Os proprietários do espaço de trabalho são tratados como qualquer outro usuário para o controle de acesso por tabela.
* É recomendável atribuir funções a grupos de segurança em vez de usuários individuais para reduzir o número de atribuições. Isso também ajudará você a usar as ferramentas de gerenciamento de grupo existentes para configurar e verificar o acesso.

## <a name="next-steps"></a>Próximos passos

* Consulte [visão geral do agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para coletar dados de computadores em seu datacenter ou em outro ambiente de nuvem.

* Consulte [coletar dados sobre máquinas virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar a coleta de dados de VMs do Azure.
