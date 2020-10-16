---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78331207"
---
1. No menu do [portal do Azure](https://portal.azure.com), selecione **Criar um recurso**. 

   ![Criar um recurso no portal do Azure](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. No campo **Pesquisar no Marketplace**, digite "Gateway de Rede Virtual". Localize o item **Gateway de rede virtual** no retorno da pesquisa e selecione a entrada correspondente. Na página **Gateway de rede virtual**, selecione **Criar**. Isso abre a página **Criar gateway de rede virtual**.
3. Na guia **Noções básicas**, preencha os valores do gateway de rede virtual.

   ![Campos da página Criar gateway de rede virtual](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Campos da página Criar gateway de rede virtual")

   ![Campos da página Criar gateway de rede virtual](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Campos da página Criar gateway de rede virtual")

   **Detalhes do projeto**

   - **Assinatura**: Selecione na lista suspensa a assinatura que deseja usar.
   - **Grupo de Recursos**: Essa configuração é preenchida automaticamente quando você seleciona a rede virtual nesta página.

   **Detalhes da instância**

   - **Nome**: nomeie o seu gateway. Dar um nome ao gateway não é igual a dar um nome a uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.
   - **Região**: Selecione a região na qual deseja criar este recurso. A região do gateway deve ser a mesma que da rede virtual.
   - **Tipo de gateway**: selecione **VPN**. Gateways VPN usam o tipo de gateway de rede virtual do tipo **VPN**.
   - **Tipo de VPN**: selecione o Tipo de VPN especificado para sua configuração. A maioria das configurações exige um tipo de VPN baseado em rota.
   - **SKU**: selecione o SKU de gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado. Para saber mais sobre os SKUs de gateway, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   - **Geração**: para informações sobre a Geração do Gateway de VPN, confira [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Rede virtual**: na lista suspensa, selecione a rede virtual à qual você deseja adicionar este gateway.
   - **Intervalo de endereços da Sub-rede do Gateway:** Esse campo só aparecerá se a VNet não tiver uma sub-rede de gateway. Se possível, faça o intervalo /27 ou maior (/26, /25 etc.). Não recomendamos criar um intervalo menor que /28. Se você já tiver uma sub-rede de gateway, poderá ver os detalhes do GatewaySubnet navegando até a rede virtual. Clique em **sub-redes** para ver o intervalo. Se quiser alterar o intervalo, você poderá excluir e recriar o GatewaySubnet.

   **Endereço IP público**: essa configuração especifica o objeto de endereço IP público associado ao gateway da VPN. O endereço IP público é atribuído dinamicamente a esse objeto quando o gateway de VPN é criado. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

     - **Endereço IP público**: Deixe **criar novo** selecionado.
     - **Nome do endereço IP público**: Na caixa de texto, digite um nome para a instância do endereço IP público.
     - **Atribuição**: o gateway de VPN é compatível somente com o Dinâmico.

   **Modo ativo-ativo**: Selecione apenas **Ativar o modo ativo-ativo** se estiver criando uma configuração de gateway ativa-ativa. Caso contrário, deixe essa configuração não selecionado.

   Deixe **Configurar BGN ASN** desmarcado, a menos que sua configuração exija especificamente essa configuração. Se você precisar dessa configuração, o ASN padrão será 65515, embora isso possa ser alterado.
4. Selecione **Examinar + criar** para executar a validação. Depois que a validação for aprovada, selecione **Criar** para implantar o Gateway de VPN. Um gateway pode levar até 45 minutos para ser criado e implantado por completo. Você poderá ver o status de implantação na página de Visão Geral do gateway.

Depois de criar o gateway você pode exibir, observando a Rede Virtual no portal, o endereço IP que foi atribuído a esse gateway. O gateway aparecerá como um dispositivo conectado.
