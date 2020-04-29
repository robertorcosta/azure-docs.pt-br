---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9648f4c16e8d266bbdd504d4a7599b67a5c846b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73522209"
---
1. No menu [portal do Azure](https://portal.azure.com) , selecione **criar um recurso**. 

   ![Crie um recurso no portal do Azure](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. No campo **Pesquisar no Marketplace** , digite **Gateway de rede local**e pressione **Enter** para pesquisar. Isso retornará uma lista de resultados. Clique em **Gateway de rede local**, depois clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   ![Criar o gateway de rede local](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Criar o gateway de rede local")

3. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

   - **Nome:** especifique um nome para seu objeto de gateway de rede local.
   - **Endereço IP:** este é o endereço IP público do dispositivo VPN ao qual você deseja que o Azure se conecte. Especifique um endereço IP público válido. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados no exemplo, mas precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
   - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. *Se deseja se conectar ao site local, use seus próprios valores aqui, e não os valores mostrados no exemplo*.
   - **Definir as configurações do BGP:** use apenas quando configurar o BGP. Caso contrário, não selecione essa opção.
   - **Assinatura:** verifique se a assinatura correta está sendo exibida.
   - **Grupo de Recursos:** selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
   - **Local:** O local é o mesmo que a **região** em outras configurações. Selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

4. Ao terminar de especificar os valores, clique no botão **Criar**, na parte inferior da página, para criar o gateway de rede local.
