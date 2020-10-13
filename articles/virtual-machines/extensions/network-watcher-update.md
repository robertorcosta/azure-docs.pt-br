---
title: Atualizar a extensão do observador de rede para a versão mais recente
description: Saiba como atualizar a extensão do observador de rede para a versão mais recente
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: fd3fff2d438bbf804e35f04db0cfae15eea5e782
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973332"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Como atualizar a extensão do observador de rede para a versão mais recente 

## <a name="overview"></a>Visão geral

O [Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitoramento de desempenho, diagnóstico e análise de rede que permite o monitoramento de redes do Azure. A extensão de máquina virtual do Agente do Observador de Rede é um requisito para capturar o tráfego de rede sob demanda e outras funcionalidades avançadas em máquinas virtuais do Azure. A extensão do observador de rede é usada por recursos como monitor de conexão, monitor de conexão (versão prévia), solução de problemas de conexão e captura de pacote.   

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha a extensão do observador de rede instalada em sua máquina virtual e forneça instruções para atualizá-la para a versão mais recente. 

## <a name="latest-version"></a>Última versão
A versão mais recente da extensão do observador de rede é atualmente `1.4.1654.1` .

## <a name="updating-your-extension"></a>Atualizando sua extensão 

### <a name="check-your-extension-version"></a>Verifique sua versão de extensão  

**Usando o portal do Azure**

1. Vá para a folha ' extensões ' de sua VM no portal do Azure.   
2. Clique na extensão ' AzureNetworkWatcher ' para ver o painel de detalhes.  
3. Localize o número de versão no campo ' versão '.  

**Usando CLI do Azure** Execute o comando abaixo em um prompt de CLI do Azure.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Localize a extensão AzureNetworkWatcher na saída e identifique o número de versão do campo "TypeHandlerVersion" na saída.  


**Usando o PowerShell** Execute os seguintes comandos em um prompt do PowerShell:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Localize a extensão AzureNetworkWatcher na saída e identifique o número de versão do campo "TypeHandlerVersion" na saída.   


### <a name="update-your-extension"></a>Atualizar sua extensão

No caso, sua versão é menor que `1.4.1654.1` (a versão mais recente atual), atualize sua extensão usando qualquer uma das opções a seguir. 

**Opção 1: usar o PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Opção 2: usar CLI do Azure**  

Forçar atualização 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Se isso não funcionar. Remova e instale a extensão novamente, usando as etapas abaixo. Isso adicionará automaticamente a versão mais recente. 

Removendo a extensão 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Instalando a extensão novamente

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Opção 3: reinicialize suas VMs**

Se você tiver a atualização automática definida como true para a extensão NetworkWatcher. Reinicializar sua VM instale a extensão mais recente.


## <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá consultar a documentação da extensão do observador de rede ([Linux](./network-watcher-linux.md), [Windows](./network-watcher-windows.md)) ou entre em contato com os especialistas do Azure nos [fóruns do Azure e Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).