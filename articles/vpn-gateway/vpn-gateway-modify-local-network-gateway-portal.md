---
title: 'Gateway de VPN: modificar configurações de endereço IP do gateway: portal do Azure'
description: Este artigo mostra o passo a passo da alteração dos prefixos de endereço IP do seu gateway de rede local usando o portal do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983169"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as configurações de gateway de rede local usando o portal do Azure

Às vezes, as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress mudam. Este artigo mostra como modificar as configurações de gateway de rede local. Também é possível modificar essas configurações usando um método diferente, selecionando uma opção diferente da lista a seguir:

Antes de excluir a conexão, convém baixar a configuração dos seus dispositivos de conexão para obter a PSK definida. Dessa forma, não é necessário redefini-la do outro lado.

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuração do gateway de rede local

A captura de tela abaixo mostra a página de **configuração** de um recurso de gateway de rede local usando o ponto de extremidade de endereço IP público:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Configurar o gateway de rede local-endereço IP":::

Esta é a mesma página de configuração com um ponto de extremidade FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Configurar gateway de rede local-FQDN":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Modificar o endereço IP do gateway

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração.

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Na caixa **Endereço IP**, modifique o endereço IP.
3. Clique em **Salvar** para salvar as configurações.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Modificar o FQDN do gateway

Se o dispositivo VPN ao qual você deseja se conectar tiver alterado seu FQDN (nome de domínio totalmente qualificado), você precisará modificar o gateway de rede local para refletir essa alteração.

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Na caixa **FQDN** , modifique o nome de domínio.
3. Clique em **Salvar** para salvar as configurações.

> ! ANOTAÇÕES Você não pode alterar um gateway de rede local entre o ponto de extremidade FQDN e o ponto de extremidade de endereço IP. Você deve excluir todas as conexões associadas a esse gateway de rede local, criar uma nova com o novo ponto de extremidade (endereço IP ou FQDN) e recriar as conexões.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar os prefixos de endereço IP

### <a name="to-add-additional-address-prefixes"></a>Para adicionar prefixos de endereço adicional:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Adicione o espaço de endereço IP na caixa *Adicionar intervalo de endereços adicional*.
3. Para salvar suas configurações, clique em **Salvar** .

### <a name="to-remove-address-prefixes"></a>Para remover os prefixo de endereço:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Clique em **'...'** na linha que contém o prefixo que você deseja remover.
3. Clique em **Remover**.
4. Para salvar suas configurações, clique em **Salvar** .

## <a name="modify-bgp-settings"></a><a name="bgp"></a>Modificar configurações de BGP

### <a name="to-add-or-update-bgp-settings"></a>Para adicionar ou atualizar as configurações de BGP:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Selecione **"definir configurações de BGP"** para exibir ou atualizar as configurações de BGP para esse gateway de rede local
3. Adicionar ou atualizar o número do sistema autônomo ou o endereço IP do par de BGP nos campos correspondentes
4. Para salvar suas configurações, clique em **Salvar** .

### <a name="to-remove-bgp-settings"></a>Para remover as configurações de BGP:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Cancele a seleção de **"definir configurações de BGP"** para remover o endereço IP do par de BGP e do protocolo BGP existente
3. Para salvar suas configurações, clique em **Salvar** .

## <a name="next-steps"></a>Próximas etapas

Você pode verificar a conexão de gateway. Confira [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).
