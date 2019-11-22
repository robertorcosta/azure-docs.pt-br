---
title: Gerenciar logs de fluxo NSG-CLI do Azure
titleSuffix: Azure Network Watcher
description: Esta página explica como gerenciar logs de fluxo de grupo de segurança de rede no Observador de Rede do Azure com CLI do Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 950b014d7e08eeeeed40ba7b294e53e1c200474b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278009"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Configurar logs de fluxo de grupo de segurança de rede com a CLI do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Os logs de fluxo do Grupo de Segurança da Rede são um recurso do Observador de Rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança da Rede. Esses logs de fluxo são escritos no formato json e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, informações com cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

Para executar as etapas deste artigo, será necessário [instalar a interface de linha de comando do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registrar o provedor Insights

Para o registro em log de fluxo funcionar com sucesso, o provedor **Microsoft.Insights** deve ser registrado. Se você não tiver certeza se o provedor **Microsoft.Insights** está registrado, execute o script a seguir.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Habilitar os logs do Fluxo de Grupo de Segurança de Rede

O comando para habilitar os logs de fluxo é mostrado no exemplo a seguir:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

A conta de armazenamento que você especificar não pode ter regras de rede configuradas para ela que restrinjam o acesso à rede somente aos serviços da Microsoft ou redes virtuais específicas. A conta de armazenamento pode ser a mesma ou uma assinatura diferente do Azure do que o NSG que você habilitar o log de fluxo. Se você usar assinaturas diferentes, ambas devem estar associadas ao mesmo locatário do Microsoft Azure Active Directory. A conta que você usa para cada assinatura deve ter as [permissões necessárias](required-rbac-permissions.md). 

Se a conta de armazenamento estiver em um outro grupo de recursos ou assinatura que não seja o grupo de segurança de rede, especifique a ID completa da conta de armazenamento, em vez de seu nome. Por exemplo, se a conta de armazenamento estiver em um grupo de recursos denominado *RG-Storage*, em vez de especificar *storageAccountName* no comando anterior, você especificará */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Desabilitar os logs do Fluxo de Grupo de Segurança de Rede

Use o exemplo a seguir para desabilitar logs de fluxo:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Baixar um log de fluxo

O local de armazenamento de um log de fluxo é definido no momento da criação. Uma ferramenta conveniente para acessar esses logs de fluxo salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixado aqui: https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de log de fluxo serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

> [!IMPORTANT]
> Atualmente, há um problema em que os [logs de fluxo do NSG (grupo de segurança de rede)](network-watcher-nsg-flow-logging-overview.md) para o Observador de Rede não são automaticamente excluídos do Armazenamento de Blobs com base nas configurações da política de retenção. Se você tiver uma política de retenção diferente de zero, recomendamos que você exclua periodicamente os blobs de armazenamento que ultrapassaram os respectivos períodos de retenção para evitar incorrer em alguma cobrança. Para obter mais informações sobre como excluir o blob de armazenamento de log de fluxo do NSG, confira [Excluir blobs de armazenamento de log de fluxo do NSG](network-watcher-delete-nsg-flow-log-blobs.md).

## <a name="next-steps"></a>Próximas Etapas

Saiba como [Visualizar seus logs de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualizar seus logs de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
