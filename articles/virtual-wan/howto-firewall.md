---
title: Instalação do Firewall do Azure em um hub de WAN Virtual
titleSuffix: Azure Virtual WAN
description: Etapas para configurar o Firewall do Azure em um hub de WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/21/2020
ms.author: cherylmc
ms.openlocfilehash: f20ed76a72eecce59a7b8795a42b033230a2f7e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753679"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Configuração do Firewall do Azure em um hub de WAN Virtual

Um **hub seguro** é um hub de WAN virtual do Azure com o Firewall do Azure. Este artigo orienta você pelas etapas para converter um hub de WAN Virtual em um hub seguro instalando o Firewall do Azure diretamente das páginas do portal de WAN Virtual do Azure.

## <a name="before-you-begin"></a>Antes de começar

As etapas neste artigo pressupõem que você já implantou uma WAN virtual com um ou mais hubs.

Para criar uma nova WAN virtual e um novo hub, siga as etapas nos artigos a seguir:

* [Criação de uma WAN virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criação de um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Exibição de hubs virtuais

A página **Visão geral** da sua WAN virtual mostra uma lista de hubs virtuais e hubs seguros. A figura a seguir mostra uma WAN virtual sem hubs seguros.

[ ![visão geral](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Conversão para hub seguro

1. Na página **Visão geral** da sua WAN virtual, selecione o hub que você quer converter em um hub seguro. Na página do hub virtual, você verá duas opções para implantar o Firewall do Azure nesse hub. Selecione uma das opções.

   [ ![segurança](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Depois de selecionar uma das opções, a página **Converter para hub seguro** será exibida. Selecione um hub para converter e, em seguida, selecione **Avançar: Firewall do Azure** na parte inferior da página.

   [ ![selecionar hub](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Depois de concluir o fluxo de trabalho, selecione **Confirmar**.

   [ ![confirmar](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Depois que o Hub tiver sido convertido em um hub seguro, você poderá vê-lo na página **Visão geral** da WAN virtual.

   [ ![exibir hub seguro](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Exibição dos recursos do hub

Na página **Visão geral** da WAN virtual, selecione o hub seguro. Na página do hub, você pode ver todos os recursos do hub virtual, incluindo o Firewall do Azure.

[ ![exibição dos recursos do hub](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Para exibir as configurações do Firewall do Azure no hub seguro, em **Segurança**, selecione **Configurações do hub virtual seguro**.
[ ![exibição das configurações do hub](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Definição de configurações adicionais

Para definir configurações adicionais do Firewall do Azure para o hub virtual, selecione o link para o **Gerenciador de Firewall do Azure**. Para obter informações sobre políticas de firewall, consulte o [Gerenciador de Firewall do Azure](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[ ![configurações adicionais](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Para retornar à página **Visão geral** do hub, navegue de volta clicando no caminho, conforme mostrado pela seta na figura a seguir.

[ ![voltar para visão geral](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
