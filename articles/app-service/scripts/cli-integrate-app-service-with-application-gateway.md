---
title: Exemplo de script da CLI do Azure – integrar o Serviço de Aplicativo com o Gateway de Aplicativo | Microsoft Docs
description: Exemplo de script da CLI do Azure – integrar o Serviço de Aplicativo com o Gateway de Aplicativo
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3ff268ca77fc855853b6506141a25c761341b451
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495498"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrar o Serviço de Aplicativo com o Gateway de Aplicativo usando a CLI

Este script de exemplo cria um aplicativo Web do Serviço de Aplicativo do Azure, uma Rede Virtual do Azure e um Gateway de Aplicativo. Em seguida, ele restringe o tráfego para o aplicativo Web de modo que só tenha origem na sub-rede do Gateway de Aplicativo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, você precisará da CLI do Azure versão 2.0.74 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, um aplicativo do Serviço de Aplicativo, o Cosmos DB e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [`az group create`](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az network vnet create`](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) | Cria uma rede virtual. |
| [`az network public-ip create`](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) | Cria um endereço IP público. |
| [`az network public-ip show`](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) | Mostrar detalhes de um endereço IP público. |
| [`az appservice plan create`](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Cria um Plano do Serviço de Aplicativo. |
| [`az webapp create`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Cria um aplicativo Web do Serviço de Aplicativo. |
| [`az webapp show`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-show) | Mostrar detalhes de um aplicativo Web do Serviço de Aplicativo. |
| [`az webapp config access-restriction add`](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest#az-webapp-config-access-restriction-add) | Adiciona uma restrição de acesso ao aplicativo Web do Serviço de Aplicativo. |
| [`az network application-gateway create`](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-create) | Cria um Gateway de Aplicativo. |
| [`az network application-gateway http-settings update`](https://docs.microsoft.com/cli/azure/network/application-gateway/http-settings?view=azure-cli-latest#az-network-application-gateway-http-settings-update) | Atualiza as configurações de HTTP do Gateway de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../samples-cli.md).
