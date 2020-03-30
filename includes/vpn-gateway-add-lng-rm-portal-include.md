---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9c18a2c74d03a636a0865f3008eb421ab8d7412d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781422"
---
1. No portal, clique em **+Criar um recurso**.
2. Na caixa de pesquisa da página, insira **Gateway de rede local**, depois pressione **Enter** para pesquisar. Isso retornará uma lista de resultados. Clique em **Gateway de rede local**, depois clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   ![Crie o gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/local-network-gateway.png "Criar o gateway de rede local")

3. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

   - **Nome:** especifique um nome para seu objeto de gateway de rede local.
   - **Endereço IP:** este é o endereço IP público do dispositivo VPN ao qual você deseja que o Azure se conecte. Especifique um endereço IP público válido. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados no exemplo, mas precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
   - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. *Se deseja se conectar ao site local, use seus próprios valores aqui, e não os valores mostrados no exemplo*.
   - **Definir as configurações do BGP:** use apenas quando configurar o BGP. Caso contrário, não selecione essa opção.
   - **Assinatura:** verifique se a assinatura correta está sendo exibida.
   - **Grupo de Recursos:** selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
   - **Local:** selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

4. Quando terminar de especificar os valores, clique no botão **Criar** para criar o gateway.
