---
title: 'Gateway de VPN: modificar configurações de endereço IP do gateway: CLI do Azure'
description: Este artigo mostra o passo a passo da alteração dos prefixos de endereço IP do seu gateway de rede local usando a CLI do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864036"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificar as configurações de gateway de rede local usando a CLI do Azure

Às vezes, as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress mudam. Este artigo mostra como modificar as configurações de gateway de rede local. Também é possível modificar essas configurações usando um método diferente, selecionando uma opção diferente da lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos da CLI, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Modificar os prefixos de endereço IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Modificar o endereço IP do gateway

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Próximos passos

Você pode verificar a conexão de gateway. Confira [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).

