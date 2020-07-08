---
title: Contas de armazenamento de propriedade do cliente para ingestão de logs
description: Use sua conta de armazenamento para a ingestão de dados de log em um workspace do Log Analytics no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 0c9982fd4aa6459cdcbd715077f08092075a9776
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84610059"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Contas de armazenamento de propriedade do cliente para ingestão de logs no Azure Monitor

O Azure Monitor usa contas de armazenamento no processo de ingestão de alguns tipos de dados, como [logs personalizados](data-sources-custom-logs.md) e alguns [logs do Azure](azure-storage-iis-table.md). Durante o processo de ingestão, os logs são enviados primeiro a uma conta de armazenamento e, posteriormente, ingeridos no Log Analytics ou Application Insights. Se você quiser controlar seus dados durante a ingestão, poderá usar suas contas de armazenamento em vez do armazenamento gerenciado pelo serviço. Usar sua própria conta de armazenamento lhe dá controle sobre o acesso, o conteúdo, a criptografia e a retenção dos logs durante a ingestão. Nós nos referimos a isso como Traga seu armazenamento ou BYOS. 

Um cenário que requer BYOS é o isolamento de rede por meio de Links Privados. Ao usar uma VNet, o isolamento de rede é geralmente um requisito, e o acesso à Internet pública é limitado. Nesses casos, o acesso ao armazenamento do serviço Azure Monitor para ingestão de logs é completamente bloqueado ou considerado uma prática inadequada. Em vez disso, os logs devem ser ingeridos por meio de uma conta de armazenamento de propriedade do cliente dentro da VNet ou facilmente acessados a partir dela.

Outro cenário é a criptografia de logs com CMK (chaves gerenciadas pelo cliente). Os clientes podem criptografar dados registrados usando CMK nos clusters que armazenam os logs. A mesma chave também pode ser usada para criptografar logs durante o processo de ingestão.

## <a name="data-types-supported"></a>Tipos de dados compatíveis

Os tipos de dados que são ingeridos de uma conta de armazenamento incluem os apresentados a seguir. Confira [Coletar dados da extensão de diagnóstico do Azure para logs do Azure Monitor](azure-storage-iis-table.md) para obter mais informações sobre a ingestão desses tipos de dados.

| Type | Informações da tabela |
|:-----|:------------------|
| Logs IIS | Blob: wad-iis-logfiles|
|Logs de eventos do Windows | Tabela: WADWindowsEventLogsTable |
| syslog | Tabela: LinuxsyslogVer2v0 |
| Logs do ETW do Windows | Tabela: WADETWEventTable|
| Service fabric | Tabela: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Logs personalizados | n/d |
| Arquivos de despejo do Watson da Central de Segurança do Azure | n/d|  

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento 
A conta de armazenamento deve atender aos seguintes requisitos:

- Ser acessível aos recursos em sua VNet que gravam logs no armazenamento.
- Estar na mesma região que o workspace ao qual está vinculada.
- Permitir explicitamente que o Log Analytics leia os logs da conta de armazenamento selecionando *Permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento*.

## <a name="process-to-configure-customer-owned-storage"></a>Processo para configurar o armazenamento de propriedade do cliente
O processo básico do uso de sua conta de armazenamento para ingestão é o seguinte:

1. Crie uma conta de armazenamento ou selecione uma conta existente.
2. Vincule a conta de armazenamento a um workspace do Log Analytics.
3. Gerencie o armazenamento examinando sua carga e retenção para garantir que ele esteja funcionando conforme o esperado.

O único método disponível para criar e remover links é por meio da API REST. Detalhes sobre a solicitação de API específica necessária para cada processo são fornecidos nas seções a seguir.

## <a name="api-request-values"></a>Valores da solicitação de API

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson – Use esse valor para os arquivos de despejo do Azure Watson da Central de Segurança do Azure.
- CustomLogs – Use esse valor para os seguintes tipos de dados:
  - Logs personalizados
  - Logs IIS
  - Eventos (Windows)
  - Syslog (Linux)
  - Logs do ETW
  - Eventos do Service Fabric
  - Dados de avaliação  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Esse valor usa a seguinte estrutura:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```



## <a name="get-current-links"></a>Obter links atuais

### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Obter contas de armazenamento vinculadas para um tipo de fonte de dados específico

#### <a name="api-request"></a>Solicitação de API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Resposta 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

### <a name="get-all-linked-storage-accounts"></a>Obter todas as contas de armazenamento vinculadas

#### <a name="api-request"></a>Solicitação de API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Resposta

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```

## <a name="create-or-modify-a-link"></a>Criar ou modificar um link

Depois que você vincular uma conta de armazenamento a um workspace, o Log Analytics começará a usá-la em vez da conta de armazenamento de propriedade do serviço. Você pode registrar uma lista de contas de armazenamento ao mesmo tempo e usar a mesma conta de armazenamento para vários workspaces.

Caso seu workspace gerencie recursos de VNet e recursos de fora de uma VNet, você deve garantir ele não esteja rejeitando o tráfego proveniente da Internet. Seu armazenamento deve ter as mesmas configurações que seu workspace e ser disponibilizado para recursos fora de sua VNet. 

### <a name="api-request"></a>Solicitação de API

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Carga útil

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Resposta

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Desvincular uma conta de armazenamento
Se você decidir parar de usar uma conta de armazenamento para ingestão ou substituir o workspace usado, desvincule o armazenamento do workspace.

Desvincular todas as contas de armazenamento de um workspace significa que a ingestão tentará utilizar contas de armazenamento gerenciadas pelo serviço. Caso os agentes sejam executados em uma VNet com acesso limitado à Internet, espera-se que a ingestão falhe. O workspace deve ter uma conta de armazenamento vinculada que possa ser alcançada por meio dos seus recursos monitorados.

Antes de excluir uma conta de armazenamento, verifique se todos os dados nela contidos foram inseridos no workspace. Como precaução, mantenha sua conta de armazenamento disponível depois de vincular um armazenamento alternativo. Exclua-a somente depois que todo o seu conteúdo tiver sido ingerido, e você poderá ver que novos dados são gravados na conta de armazenamento recém-conectada.


### <a name="api-request"></a>Solicitação de API
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Substituir uma conta de armazenamento

Para substituir uma conta de armazenamento usada para ingestão, primeiro crie um link para uma nova conta de armazenamento. Os agentes de registro em log receberão a configuração atualizada e começarão a enviar dados para o novo armazenamento também.

Em seguida, desvincule a antiga conta de armazenamento para que os agentes parem de gravar na conta removida. O processo de ingestão continua lendo os dados dessa conta até que eles sejam todos ingeridos. Não exclua a conta de armazenamento até ver que todos os logs foram ingeridos.

A configuração do agente será atualizada após alguns minutos e mudará para o novo armazenamento.

## <a name="manage-storage-account"></a>Gerenciar conta de armazenamento

### <a name="load"></a>Carregar

As contas de armazenamento podem lidar com determinada carga de solicitações de leitura e gravação antes de iniciarem as solicitações de limitação. A limitação afeta o tempo necessário para a ingestão de logs e pode resultar na perda de dados. Se o armazenamento estiver sobrecarregado, registre contas de armazenamento adicionais e distribua a carga entre elas. 

### <a name="related-charges"></a>Cobranças relacionadas

As contas de armazenamento são cobradas pelo volume de dados armazenados, pelos tipos de armazenamento e pelo tipo de redundância. Para obter detalhes, confira [Preços de blobs de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/) e [Preços do Armazenamento de Tabelas](https://azure.microsoft.com/pricing/details/storage/tables/).

Se a conta de armazenamento registrada do seu workspace estiver em outra região, você será cobrado pela saída de acordo com estes [Detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como configurar um link privado, consulte [usar o link privado do Azure para conectar redes com segurança a Azure monitor](private-link-security.md)
