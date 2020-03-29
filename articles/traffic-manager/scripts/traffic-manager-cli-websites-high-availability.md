---
title: Tráfego de rota para HA de aplicativos - Azure CLI - Gerenciador de tráfego
description: Exemplo de script da CLI do Azure - Rotear o tráfego para alta disponibilidade de aplicativos
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: e4912e0e285d41bf2de4cf439788a943251cfae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934788"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Rotear o tráfego para alta disponibilidade de aplicativos usando a CLI do Azure

Este script cria um grupo de recursos, dois planos de serviço de aplicativo, dois aplicativos web, um perfil do Gerenciador de tráfego e dois pontos de extremidade de Gerenciador de tráfego. O Gerenciador de Tráfego direciona o tráfego para o aplicativo em uma região como a região primária, e para a região secundária quando o aplicativo na região primária não estiver disponível. Antes de executar o script, você deve alterar os valores MyWebApp, MyWebAppL1 e MyWebAppL2 para valores exclusivos no Azure. Depois de executar o script, você pode acessar o aplicativo na região primária com a URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo do Serviço de Aplicativo e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, o aplicativo Web, o perfil do Gerenciador de tráfego e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Cria um Plano do Serviço de Aplicativo. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [az webapp web create](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Cria um aplicativo web do Azure no plano do Serviço de Aplicativo. |
| [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Cria um perfil de Gerenciador de Tráfego do Azure. |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Adiciona um endpoint a um perfil do Gerenciador de tráfego do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure, consulte [a documentação da Cli do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação de Rede do Azure](../cli-samples.md).
