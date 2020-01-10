---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772968"
---
Esse erro pode ocorrer sempre que o serviço de Sincronização de Arquivos do Azure está acessível do servidor. Você pode solucionar esse erro trabalhando nas seguintes etapas:

1. Verifique se o serviço Windows `FileSyncSvc.exe` não está bloqueado pelo firewall.
2. Verifique se a porta 443 está aberta para conexões de saída para o serviço de Sincronização de Arquivos do Azure. Você pode fazer isso com o cmdlet `Test-NetConnection`. A URL para o espaço reservado `<azure-file-sync-endpoint>` abaixo pode ser encontrada no documento de [Configurações de proxy e firewall de Sincronização de Arquivos do Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verifique se a configuração de proxy está definida conforme o esperado. Isso pode ser feito com o cmdlet `Get-StorageSyncProxyConfiguration`. Mais informações sobre como definir a configuração de proxy para Sincronização de Arquivos do Azure podem ser encontradas em [Configurações de proxy e firewall da Sincronização de Arquivos do Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Use o cmdlet Test-StorageSyncNetworkConnectivity para verificar a conectividade de rede para os pontos de extremidade de serviço. Para saber mais, confira [testar a conectividade de rede para pontos de extremidade de serviço](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. Contate seu administrador de rede para obter mais assistência para a solução de problemas de conectividade de rede.
