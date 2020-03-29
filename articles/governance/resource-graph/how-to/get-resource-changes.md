---
title: Obter alterações de recurso
description: Entenda como encontrar quando um recurso foi alterado, obtenha uma lista das propriedades que mudaram e avalie os diferenciais.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873022"
---
# <a name="get-resource-changes"></a>Obter alterações de recurso

Os recursos são alterados ao longo do uso diário, reconfiguração e até reimplantação.
A mudança pode vir de um indivíduo ou de um processo automatizado. A maioria das mudanças é pelo design, mas às vezes não é. Com os últimos 14 dias de histórico de mudanças, o Azure Resource Graph permite:

- Descubra quando foram detectadas alterações em uma propriedade do Azure Resource Manager
- Para cada alteração de recurso, veja detalhes de alteração de propriedade
- Veja uma comparação completa do recurso antes e depois da alteração detectada

A detecção de alterações e os detalhes são valiosos para os seguintes cenários de exemplo:

- Durante a gestão de incidentes para entender as mudanças _potencialmente_ relacionadas. Consulte os eventos de alteração durante uma janela específica de tempo e avalie os detalhes da alteração.
- Manter um banco de dados de gerenciamento de configuração, conhecido como CMDB, atualizado. Em vez de refrescar todos os recursos e seus conjuntos de propriedades completas em uma frequência programada, só obter o que mudou.
- Entender quais outras propriedades podem ter sido alteradas quando um recurso mudou o estado de conformidade. A avaliação dessas propriedades adicionais pode fornecer insights sobre outras propriedades que podem precisar ser gerenciadas por meio de uma definição de Política Do Azure.

Este artigo mostra como coletar essas informações através do SDK do Resource Graph. Para ver essas informações no portal Azure, consulte o histórico de [alterações](../../policy/how-to/determine-non-compliance.md#change-history-preview) da política do Azure ou o [histórico de alterações do](../../../azure-monitor/platform/activity-log-view.md#azure-portal)log de atividades do Azure .
Para obter detalhes sobre alterações em seus aplicativos desde a camada de infra-estrutura até a implantação do aplicativo, consulte [Use Application Change Analysis (preview)](../../../azure-monitor/app/change-analysis.md) no Azure Monitor.

> [!NOTE]
> Alterar detalhes no Gráfico de recursos são para propriedades do Gerenciador de recursos. Para rastrear alterações dentro de uma máquina virtual, consulte o [rastreamento de alterações](../../../automation/automation-change-tracking.md) da Azure Automation ou a configuração de convidado da Política Do Azure [para VMs](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> A história da mudança no Gráfico de Recursos do Azure está na Visualização Pública.

## <a name="find-detected-change-events-and-view-change-details"></a>Encontre eventos de alteração detectados e visualize detalhes de alteração

O primeiro passo para ver o que mudou em um recurso é encontrar os eventos de alteração relacionados a esse recurso dentro de uma janela de tempo. Cada evento de alteração também inclui detalhes sobre o que mudou no recurso. Esta etapa é feita através do **recursoChanges** REST endpoint.

O ponto final **do resourceChanges** aceita os seguintes parâmetros no órgão de solicitação:

- **resourceId** \[\]necessário : O recurso Do Zure para procurar alterações.
- **intervalo** \[\]necessário : Uma propriedade com datas _de início_ e _término_ para quando verificar um evento de alteração usando o **fuso horário zulu (Z)**.
- **fetchPropertyChanges** (opcional): Uma propriedade booleana que define se o objeto de resposta incluir alterações de propriedade.

Exemplo de corpo de solicitação:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Com o órgão de solicitação acima, o URI da API REST para **recursos Alterações** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Cada evento de alteração detectado para o **recursoId** tem as seguintes propriedades:

- **changeId** - Este valor é exclusivo para esse recurso. Embora a seqüência **changeId** possa às vezes conter outras propriedades, é apenas garantido ser único.
- **antesdoSnapshot** - Contém o **snapshotId** e o carimbo de **tempo** do instantâneo de recurso que foi tirado antes de uma alteração ser detectada.
- **afterSnapshot** - Contém o **snapshotId** e o carimbo de **tempo** do instantâneo de recurso que foi tirado após a desestatação de uma alteração.
- **changeType** - Descreve o tipo de alteração detectado para todo o registro de alteração entre o **beforeSnapshot** e **afterSnapshot**. Os valores são: _Criar,_ _Atualizar_e _Excluir_. A **matriz de propriedadesPropertyChanges** só é incluída quando **changeType** é _Update_.
- **propriedadeAlterações** - Esta matriz de propriedades detalha todas as propriedades de recurso que foram atualizadas entre o **beforeSnapshot** e o **afterSnapshot**:
  - **propertyName** - O nome da propriedade de recurso que foi alterada.
  - **changeCategory** - Descreve o que fez a mudança. Os valores são: _Sistema_ e _Usuário._
  - **changeType** - Descreve o tipo de alteração detectado para a propriedade de recursos individuais.
    Os valores são: _Inserir,_ _Atualizar,_ _Remover_.
  - **antesValor** - O valor da propriedade de recursos no **beforeSnapshot**. Não é exibido quando **changeType** é _Insert_.
  - **afterValue** - O valor da propriedade de recursos no **afterSnapshot**. Não é exibido quando **changeType** é _Remover_.

## <a name="compare-resource-changes"></a>Compare as alterações de recursos

Com o **changeId** do ponto final do **recursoChanges,** o ponto final **resourceChangeDetails** REST é então usado para obter os instantâneos antes e depois do recurso que foi alterado.

O ponto final **resourceChangeDetails** requer dois parâmetros no corpo de solicitação:

- **resourceId**: O recurso Do Azure para comparar alterações.
- **changeId**: O evento de alteração exclusivo para os **recursosId** coletados a partir de **resourceChanges**.

Exemplo de corpo de solicitação:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Com o órgão de solicitação acima, o URI da API REST para **recursosChangeDetails** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**antesSnapshot** e **afterSnapshot** cada um dá o momento em que o snapshot foi tirado e as propriedades naquele momento. A mudança aconteceu em algum momento entre esses instantâneos. Olhando para o exemplo acima, podemos ver que a propriedade que mudou foi **suportadaHttpsTrafficOnly**.

Para comparar os resultados, use a propriedade **alterações** em **recursosAlterações** ou avalie a parte de **conteúdo** de cada instantâneo em **resourceChangeDetails** para determinar a diferença. Se você comparar os instantâneos, o **carimbo de tempo** sempre aparece como uma diferença, apesar de ser esperado.

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso em [consultas iniciais](../samples/starter.md).
- Veja usos avançados em [consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](../concepts/explore-resources.md).