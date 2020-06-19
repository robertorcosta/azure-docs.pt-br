---
title: Túnel forçado do Firewall do Azure
description: Você pode configurar o túnel forçado para rotear o tráfego vinculado à Internet para um firewall adicional ou solução de virtualização de rede para processamento adicional.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: cf0af93d95c5af56be6168bc8e4f79d3005e2ec2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649589"
---
# <a name="azure-firewall-forced-tunneling"></a>Túnel forçado do Firewall do Azure

Quando configura um novo Firewall do Azure, você pode rotear todo o tráfego vinculado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, você pode ter um firewall de borda local ou outra solução de virtualização de rede (NVA) para processar o tráfego de rede antes que ele seja passado para a Internet. No entanto, você não pode configurar um firewall existente para túnel forçado.

Por padrão, o túnel forçado não é permitido no Firewall do Azure para garantir que todas as suas dependências de saída do Azure sejam atendidas. As configurações de Rota Definida pelo Usuário (UDR) no *AzureFirewallSubnet* que têm uma rota padrão que não vai diretamente para a Internet estão desabilitadas.

## <a name="forced-tunneling-configuration"></a>Configuração de túnel forçado

Para dar suporte ao túnel forçado, o tráfego de Gerenciamento de Serviços é separado do tráfego do cliente. Uma sub-rede adicional dedicada denominada *AzureFirewallManagementSubnet* (tamanho mínimo de /26 da sub-rede) é necessária com seu próprio endereço IP público associado. A única rota permitida nessa sub-rede é uma rota padrão para a Internet, e a propagação de rotas BGP deve ser desabilitada.

Se tiver uma rota padrão anunciada via BGP para forçar o tráfego para o ambiente local, você deverá criar o *AzureFirewallSubnet* e o *AzureFirewallManagementSubnet* antes de implantar o firewall e ter uma UDR com uma rota padrão para a Internet, e a **propagação da rota do gateway de rede virtual** desabilitada.

Nessa configuração, o *AzureFirewallSubnet* pode então incluir rotas para qualquer firewall local ou NVA para processar o tráfego antes que ele seja passado para a Internet. Você também pode publicar essas rotas via BGP no *AzureFirewallSubnet* se a **propagação de rota do gateway de rede virtual** estiver habilitada nessa sub-rede.

Por exemplo, você pode criar uma rota padrão no *AzureFirewallSubnet* com seu gateway de VPN como o próximo salto para chegar ao dispositivo local. Ou você pode habilitar a **propagação de rota do gateway de rede virtual** para obter as rotas apropriadas para a rede local.

![Propagação de rota do gateway de rede virtual](media/forced-tunneling/route-propagation.png)

Depois de configurar o Firewall do Azure para dar suporte ao túnel forçado, não é possível desfazer a configuração. Se remover todas as outras configurações de IP em seu firewall, a configuração de IP de gerenciamento também será removida e o firewall será desalocado. O endereço IP público atribuído à configuração de IP de gerenciamento não pode ser removido, mas você pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure](tutorial-hybrid-portal.md)
