---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ce9fa29a29559a1eaaff6173737159f11aa83d8
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71268886"
---
1. No portal, no lado esquerdo, clique em **+ criar um recurso** e digite "gateway de rede virtual" na pesquisa. Localize **Gateway de rede virtual** na pesquisa, retorne e clique na entrada. Na página **Gateway de rede virtual** , clique em **criar**. Isso abre a página **criar gateway de rede virtual** .

   ![Criar campos de página de gateway de rede virtual](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Criar campos de página de gateway de rede virtual")

   ![Criar campos de página de gateway de rede virtual](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Criar campos de página de gateway de rede virtual")
2. Na página **criar gateway de rede virtual** , preencha os valores de seu gateway de rede virtual.

   **Detalhes do projeto**

   - **Assinatura**: selecione a assinatura que você deseja usar na lista suspensa.
   - **Grupo de recursos**: essa configuração é preenchida automática quando você seleciona sua rede virtual nesta página.

   **Detalhes da instância**

   - **Nome**: nomeie o seu gateway. Nomear seu gateway não é o mesmo que nomear uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.
   - **Região**: selecione a região na qual você deseja criar esse recurso. A região do gateway deve ser a mesma que a rede virtual.
   - **Tipo de gateway**: selecione **VPN**. Gateways VPN usam o tipo de gateway de rede virtual do tipo **VPN**. 
   - **Tipo de VPN**: selecione o Tipo de VPN especificado para sua configuração. A maioria das configurações requer um tipo de VPN baseado em rota.
   - **SKU**: selecione o SKU de gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado. Para saber mais sobre os SKUs de gateway, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Rede virtual**: escolha a rede virtual à qual você deseja adicionar esse gateway.

      **Intervalo de endereços de sub-rede de gateway**: esse campo será exibido somente se a rede virtual selecionada não tiver uma sub-rede de gateway. Preencha o intervalo se você ainda não tiver uma sub-rede de gateway. Se possível, torne o intervalo/27 ou maior (/26,/25 etc.)

   **Endereço IP público**: essa configuração especifica o objeto de endereço IP público que é associado ao gateway de VPN. O endereço IP público é atribuído dinamicamente a esse objeto quando o gateway de VPN é criado. A única vez em que o endereço IP público é alterado é quando o gateway é excluído e recriado. Ele não é alterado em redimensionamento, redefinição ou outra manutenção/atualização interna do seu gateway de VPN.

     - **Endereço IP público**: deixe **criar novo** selecionado.
     - **Nome do endereço IP público**: na caixa de texto, digite um nome para sua instância de endereço IP público.
     - **Atribuição**: o gateway de VPN dá suporte apenas ao dinâmico.

   **Modo ativo-ativo**: selecione somente **habilitar modo ativo-ativo** se você estiver criando uma configuração de gateway ativo-ativo. Caso contrário, deixe essa configuração desmarcada.

   Deixe **Configurar BGP ASN** desmarcado, a menos que sua configuração exija especificamente essa configuração. Se você exigir essa configuração, o ASN padrão será 65515, embora isso possa ser alterado.

3. Clique em **examinar + criar** para executar a validação. Depois que a validação for aprovada, clique em **criar** para implantar o gateway de VPN. Um gateway pode levar até 45 minutos para criar e implantar totalmente. Você pode ver o status da implantação na página Visão geral do seu gateway.

Depois de criar o gateway você pode exibir, observando a Rede Virtual no portal, o endereço IP que foi atribuído a esse gateway. O gateway aparece como um dispositivo conectado.