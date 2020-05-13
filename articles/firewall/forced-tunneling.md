---
title: Túnel forçado do firewall do Azure
description: Você pode configurar o túnel forçado para rotear o tráfego vinculado à Internet para um firewall adicional ou solução de virtualização de rede para processamento adicional.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 463bccb29d59f06e7381d7d7123946029223a93a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199727"
---
# <a name="azure-firewall-forced-tunneling"></a>Túnel forçado do firewall do Azure

Você pode configurar o Firewall do Azure para rotear todo o tráfego vinculado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, você pode ter um firewall de borda local ou outra solução de virtualização de rede (NVA) para processar o tráfego de rede antes que ele seja passado para a Internet.

Por padrão, o túnel forçado não é permitido no firewall do Azure para garantir que todas as suas dependências de saída do Azure sejam atendidas. As configurações de UDR (rota definida pelo usuário) no *AzureFirewallSubnet* que têm uma rota padrão que não vai diretamente para a Internet estão desabilitadas.

## <a name="forced-tunneling-configuration"></a>Configuração de túnel forçado

Para dar suporte ao túnel forçado, o tráfego de gerenciamento de serviços é separado do tráfego do cliente. Uma sub-rede adicional dedicada denominada *AzureFirewallManagementSubnet* (tamanho mínimo da sub-rede/26) é necessária com seu próprio endereço IP público associado. A única rota permitida nessa sub-rede é uma rota padrão para a Internet, e a propagação de rota BGP deve ser desabilitada.

Se você tiver uma rota padrão anunciada via BGP para forçar o tráfego para o local, deverá criar o *AzureFirewallSubnet* e o *AzureFirewallManagementSubnet* antes de implantar o firewall e ter um UDR com uma rota padrão para a Internet e a **propagação de rota do gateway de rede virtual** desabilitada.

Nessa configuração, o *AzureFirewallSubnet* agora pode incluir rotas para qualquer firewall local ou NVA para processar o tráfego antes que ele seja passado para a Internet. Você também pode publicar essas rotas via BGP para *AzureFirewallSubnet* se a **propagação de rota de gateway de rede virtual** estiver habilitada nesta sub-rede.

Por exemplo, você pode criar uma rota padrão no *AzureFirewallSubnet* com seu gateway de VPN como o próximo salto para chegar ao dispositivo local. Ou você pode habilitar a **propagação de rota de gateway de rede virtual** para obter as rotas apropriadas para a rede local.

![Propagação de rota do gateway de rede virtual](media/forced-tunneling/route-propagation.png)

Depois de configurar o Firewall do Azure para dar suporte ao túnel forçado, não é possível desfazer a configuração. Se você remover todas as outras configurações de IP em seu firewall, a configuração de IP de gerenciamento também será removida e o firewall será desalocado. O endereço IP público atribuído à configuração de IP de gerenciamento não pode ser removido, mas você pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure](tutorial-hybrid-portal.md)
