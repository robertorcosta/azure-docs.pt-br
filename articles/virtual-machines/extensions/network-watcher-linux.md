---
title: Extensão de máquina virtual do Azure Network Watcher Agent para Linux
description: Implante o Agente do Observador de Rede do Azure na máquina virtual Linux usando uma extensão da máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 712ec177996cd54d7bd6d184fea306009b58b083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531015"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux

## <a name="overview"></a>Visão geral

[Observador de Rede do Azure](/azure/network-watcher/) é um serviço de monitoramento de desempenho, diagnóstico e análise de rede que permite o monitoramento de redes do Azure. A extensão de máquina virtual (VM) do Agente do Observador de Rede é um requisito para alguns dos recursos do Observador de Rede em VMs do Azure para capturar o tráfego de rede sob demanda e outras funcionalidades avançadas.

Este artigo detalha as plataformas e as opções de implantação com suporte para a extensão da VM do Agente do Observador de Rede para Linux. A instalação do agente não interrompe a VM ou exige uma reinicialização dela. É possível implantar a extensão em máquinas virtuais que você implanta. Se a máquina virtual for implantada por um serviço do Azure, verifique a documentação do serviço para determinar se ele permite ou não a instalação de extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do Agente do Observador de Rede pode ser configurada para as seguintes distribuições do Linux:

| Distribuição | Versão |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 e 8 |
| Red Hat | 6 e 7 |
| Oracle Linux | 6.8+ e 7 |
| SUSE Linux Enterprise Server | 11 e 12 |
| OpenSUSE Leap | 42.3+ |
| CentOS | 6.5+ e 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Conectividade com a Internet

Algumas das funcionalidades do Agente do Observador de Rede exigem que a VM esteja conectada à Internet. Sem a capacidade de estabelecer conexões de saída, alguns dos recursos do Agente do Observador de Rede podem apresentar problemas ou se tornar indisponíveis. Para obter mais informações sobre a funcionalidade do Observador de Rede que requer o agente, consulte a [documentação do Observador de Rede](/azure/network-watcher/).

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do Agente do Observador de Rede. A extensão não exige ou oferecem suporte a todas as configurações fornecidas pelo usuário. A extensão depende de sua configuração padrão.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Implantação de modelo

Você pode implantar extensões de VM do Azure com um modelo do Azure Resource Manager. Para implantar a extensão do Agente do Observador de Rede, use o esquema json anterior em seu modelo.

## <a name="azure-classic-cli-deployment"></a>Implantação da CLI clássica do Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O exemplo a seguir implanta a extensão de VM de Agente do Observador de Rede para uma VM existente implantada por meio do modelo de implantação clássico:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

O exemplo a seguir implanta a extensão de VM de Agente do Observador de Rede para uma VM existente implantada por meio do Gerenciador de Recursos:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Solução de problemas e suporte

### <a name="troubleshooting"></a>Solução de problemas

Você pode recuperar dados sobre o estado das implantações de extensão usando o portal do Azure ou a CLI do Azure.

O exemplo a seguir mostra o estado de implantação da extensão NetworkWatcherAgentLinux para uma VM implantada por meio do Resource Manager, usando a CLI do Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Suporte

Se você precisar de mais ajuda em qualquer ponto deste artigo, você pode consultar a [documentação](/azure/network-watcher/)do Observador de Rede ou entrar em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para saber mais sobre como usar o Suporte do Azure, consulte as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
