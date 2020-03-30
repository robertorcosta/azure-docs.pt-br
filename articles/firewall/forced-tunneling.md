---
title: Azure Firewall forçou o túnel
description: Você pode configurar túneis forçados para direcionar o tráfego vinculado à Internet para um firewall ou dispositivo virtual de rede adicional para posterior processamento.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597262"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure Firewall forçou o túnel (visualização)

Você pode configurar o Firewall Azure para direcionar todo o tráfego vinculado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, você pode ter um firewall de borda no local ou outro dispositivo virtual de rede (NVA) para processar o tráfego de rede antes de ser passado para a Internet.

> [!IMPORTANT]
> O túnel forçado do Azure Firewall está atualmente em visualização pública.
>
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Talvez alguns recursos não tenham suporte, tenham recursos restritos ou não estejam disponíveis em todos os locais do Azure. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Por padrão, o túnel forçado não é permitido no Azure Firewall para garantir que todas as suas dependências do Azure de saída sejam atendidas. As configurações de Rota Definida pelo Usuário (UDR) na *AzureFirewallSubnet* que possuem uma rota padrão que não vai diretamente para a Internet são desativadas.

## <a name="forced-tunneling-configuration"></a>Configuração de túnel forçado

Para suportar o túnel forçado, o tráfego de gerenciamento de serviços é separado do tráfego de clientes. Uma sub-rede dedicada adicional chamada *AzureFirewallManagementSubnet* (tamanho mínimo da sub-rede /26) é necessária com seu próprio endereço IP público associado. A única rota permitida nesta sub-rede é uma rota padrão para a Internet, e a propagação da rota BGP deve ser desativada.

Se você tiver uma rota padrão anunciada via BGP para forçar o tráfego para os locais, você deve criar a *AzureFirewallSubnet* e *a AzureFirewallManagementSubnet* antes de implantar seu firewall e ter um UDR com uma rota padrão para a Internet e **a propagação de rota de gateway de rede virtual** desativada.

Dentro dessa configuração, o *AzureFirewallSubnet* agora pode incluir rotas para qualquer firewall local ou NVA para processar o tráfego antes de ser passado para a Internet. Você também pode publicar essas rotas via BGP para *AzureFirewallSubnet* se a **propagação da rota de gateway de rede virtual** estiver ativada nesta sub-rede.

Por exemplo, você pode criar uma rota padrão na *AzureFirewallSubnet* com seu gateway VPN como o próximo salto para chegar ao seu dispositivo local. Ou você pode habilitar a **propagação da rota do gateway de rede virtual** para obter as rotas apropriadas para a rede local.

![Propagação de rota de gateway de rede virtual](media/forced-tunneling/route-propagation.png)

Uma vez configurado o Firewall Azure para suportar túneis forçados, você não pode desfazer a configuração. Se você remover todas as outras configurações DE NO firewall, a configuração ip de gerenciamento também será removida e o firewall será desalocado. O endereço IP público atribuído à configuração ip de gerenciamento não pode ser removido, mas você pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Implante e configure o Firewall Azure em uma rede híbrida usando o portal Azure](tutorial-hybrid-portal.md)