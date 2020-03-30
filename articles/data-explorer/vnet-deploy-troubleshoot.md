---
title: Solucionar problemas de acesso, ingestão e operação do cluster Azure Data Explorer em sua rede virtual
description: Solucionando problemas de conectividade, ingestão, criação de clusters e operação do cluster Azure Data Explorer em sua rede virtual
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241654"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Solucionar problemas de acesso, ingestão e operação do cluster Azure Data Explorer em sua rede virtual

Nesta seção, você aprende como solucionar problemas de conectividade, operacional e criação de clusters para um cluster implantado em sua [Rede Virtual](/azure/virtual-network/virtual-networks-overview).

## <a name="access-issues"></a>Problemas de acesso

Se você tiver um problema ao acessar cluster usando o ponto final público (cluster.region.kusto.windows.net) ou privado (private-cluster.region.kusto.windows.net) e suspeitar que está relacionado à configuração de rede virtual, execute as seguintes etapas para solucionar problemas o problema.

### <a name="check-tcp-connectivity"></a>Verifique a conectividade TCP

O primeiro passo inclui verificar a conectividade TCP usando o Sistema Operacional Windows ou Linux.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Baixe [TCping](https://www.elifulkerson.com/projects/tcping.php) para a máquina conectada ao cluster.
   2. Pingando o destino da máquina de origem usando o seguinte comando:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Instale *o netcat* na máquina conectando-se ao cluster

    ```bash
    $ apt-get install netcat
     ```

   2. Pingando o destino da máquina de origem usando o seguinte comando:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Se o teste não for bem sucedido, prossiga com as seguintes etapas. Se o teste for bem sucedido, o problema não será devido a um problema de conectividade TCP. Vá para [problemas operacionais](#cluster-creation-and-operations-issues) para solucionar ainda mais.

### <a name="check-the-network-security-group-nsg"></a>Verifique o Grupo de Segurança de Rede (NSG)

   Verifique se o [Grupo de Segurança de Rede](/azure/virtual-network/security-overview) (NSG) conectado à sub-rede do cluster tem uma regra de entrada que permite o acesso do IP da máquina cliente para a porta 443.

### <a name="check-route-table"></a>Confira a tabela da rota

   Se a sub-rede do cluster tiver configuração de túnel de força para firewall (sub-rede com uma [tabela de rota](/azure/virtual-network/virtual-networks-udr-overview) que contém a rota padrão '0.0.0.0/0'), certifique-se de que o endereço IP da máquina tenha uma rota com o próximo tipo de [hop](/azure/virtual-network/virtual-networks-udr-overview) para VirtualNetwork/Internet. Esta rota é necessária para evitar problemas de rota assimétrica.

## <a name="ingestion-issues"></a>Questões de ingestão

Se você está enfrentando problemas de ingestão e suspeita que está relacionado à configuração da rede virtual, execute as etapas a seguir.

### <a name="check-ingestion-health"></a>Verificar a saúde da ingestão

Verifique se as [métricas de ingestão de cluster](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicam um estado saudável.

### <a name="check-security-rules-on-data-source-resources"></a>Verifique as regras de segurança sobre os recursos de origem de dados

Se as métricas indicarem que nenhum evento foi processado a partir da fonte de dados (métrica processada de*eventos* para hubs de eventos/IoT), certifique-se de que os recursos de origem de dados (Event Hub ou Storage) permitam o acesso da sub-rede do cluster nas regras de firewall ou pontos finais de serviço.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Verifique as regras de segurança configuradas na sub-rede do cluster

Certifique-se de que as regras de sub-rede do cluster estão configuradas corretamente. Além disso, teste a conectividade da rede para todos os pontos finais dependentes. 

## <a name="cluster-creation-and-operations-issues"></a>Problemas de criação e operações de cluster

Se você está enfrentando problemas de criação ou operação de cluster e suspeita que está relacionado à configuração da rede virtual, siga essas etapas para solucionar o problema.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnosticar a rede virtual com a API REST

O [ARMClient](https://chocolatey.org/packages/ARMClient) é usado para chamar a API REST usando o PowerShell. 

1. Fazer logon com o ARMClient

   ```powerShell
   armclient login
   ```

1. Invoque a operação de diagnóstico

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Confira a resposta

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Aguarde a conclusão da operação

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Aguarde até que a propriedade *de status* seja *concluída,* então o campo *propriedades* deve mostrar:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Se a propriedade *Findings* mostrar um resultado vazio, significa que todos os testes de rede passaram e nenhuma conexão será quebrada. Se o erro a seguir for mostrado, *a dependência de saída '{dependencyName}:{port}' pode não estar satisfeita (Saída)* o cluster não poderá alcançar os pontos finais de serviço dependentes. Proceda com as seguintes etapas.

### <a name="check-network-security-group-nsg"></a>Verifique o Grupo de Segurança da Rede (NSG)

Certifique-se de que o [Grupo de Segurança de Rede](/azure/virtual-network/security-overview) esteja configurado corretamente de acordo com as instruções em [Dependências para implantação do VNet](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Confira a tabela da rota

Se a sub-rede do cluster tiver o túnel de força configurado para firewall (sub-rede com uma [tabela de rota](/azure/virtual-network/virtual-networks-udr-overview) que contém a rota padrão '0.0.0.0/0') certifique-se de que os [endereços IP](vnet-deployment.md#azure-data-explorer-management-ip-addresses)de gerenciamento ) e os [endereços IP de monitoramento de saúde](vnet-deployment.md#health-monitoring-addresses) tenham uma rota com o próximo tipo [de](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*e [prefixo de endereço de origem](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) para *'management-ip/32'* e *'health-monitoring-ip/32'*. Esta rota é necessária para evitar problemas de rota assimétrica.

### <a name="check-firewall-rules"></a>Verifique as regras do firewall

Se você forçar o tráfego de saída de sub-rede do túnel para um firewall, certifique-se de que todas as dependências FQDN (por exemplo, *.blob.core.windows.net)* sejam permitidas na configuração de firewall, conforme descrito na [proteção do tráfego de saída com firewall](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).
