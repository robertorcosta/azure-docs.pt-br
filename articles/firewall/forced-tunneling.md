---
title: Túnel forçado do Firewall do Azure
description: Você pode configurar o túnel forçado para rotear o tráfego vinculado à Internet para um firewall adicional ou solução de virtualização de rede para processamento adicional.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/01/2020
ms.author: victorh
ms.openlocfilehash: a467aa60b131e47e9251366369b3fae8dd95c004
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84267691"
---
# <a name="azure-firewall-forced-tunneling"></a>Túnel forçado do Firewall do Azure

Quando configura um novo Firewall do Azure, você pode rotear todo o tráfego vinculado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, você pode ter um firewall de borda local ou outra solução de virtualização de rede (NVA) para processar o tráfego de rede antes que ele seja passado para a Internet. No entanto, você não pode configurar um firewall existente para túnel forçado.

Por padrão, o túnel forçado não é permitido no Firewall do Azure para garantir que todas as suas dependências de saída do Azure sejam atendidas. As configurações de Rota Definida pelo Usuário (UDR) no *AzureFirewallSubnet* que têm uma rota padrão que não vai diretamente para a Internet estão desabilitadas.

## <a name="forced-tunneling-configuration"></a>Configuração de túnel forçado

Para dar suporte ao túnel forçado, o tráfego de Gerenciamento de Serviços é separado do tráfego do cliente. Uma sub-rede adicional dedicada denominada *AzureFirewallManagementSubnet* (tamanho mínimo de /26 da sub-rede) é necessária com seu próprio endereço IP público associado. A única rota permitida nessa sub-rede é uma rota padrão para a Internet, e a propagação de rotas BGP deve ser desabilitada.

Se tiver uma rota padrão anunciada via BGP para forçar o tráfego para o ambiente local, você deverá criar o *AzureFirewallSubnet* e o *AzureFirewallManagementSubnet* antes de implantar o firewall e ter uma UDR com uma rota padrão para a Internet, e a **propagação da rota do gateway de rede virtual** desabilitada.

Nessa configuração, o *AzureFirewallSubnet* agora pode incluir rotas para qualquer firewall local ou NVA para processar o tráfego antes que ele seja passado para a Internet. Você também pode publicar essas rotas via BGP no *AzureFirewallSubnet* se a **propagação de rota do gateway de rede virtual** estiver habilitada nessa sub-rede.

Por exemplo, você pode criar uma rota padrão no *AzureFirewallSubnet* com seu gateway de VPN como o próximo salto para acessar seu dispositivo local. Ou você pode habilitar a **propagação de rota do gateway de rede virtual** para obter as rotas apropriadas para a rede local.

![Propagação de rota do gateway de rede virtual](media/forced-tunneling/route-propagation.png)

Se você habilitar o túnel forçado, o tráfego de entrada na Internet será no modo SNAT a um dos endereços IP privados do firewall no AzureFirewallSubnet, ocultando a origem do seu firewall local.

Se a sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure realiza SNAT do tráfego para um dos endereços IP privados do firewall no AzureFirewallSubnet. No entanto, você pode configurar o Firewall do Azure para **não** SNAT de seu intervalo de endereços IP públicos. Para obter mais informações, confira [Intervalos de endereços IP privados do SNAT do Firewall do Azure](snat-private-range.md).

Depois de configurar o Firewall do Azure para dar suporte ao túnel forçado, não é possível desfazer a configuração. Se remover todas as outras configurações de IP em seu firewall, a configuração de IP de gerenciamento também será removida e o firewall será desalocado. O endereço IP público atribuído à configuração de IP de gerenciamento não pode ser removido, mas você pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure](tutorial-hybrid-portal.md)
