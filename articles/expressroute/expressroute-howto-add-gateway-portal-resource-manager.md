---
title: 'Azure ExpressRoute: adicionar um gateway a uma VNet: Portal'
description: Este artigo orienta você pela adição de um gateway de rede virtual a uma VNet do Resource Manager já criada para o ExpressRoute usando o portal do Azure.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 188d366dafce6ee79a084750b5f7d1fe4140432b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736366"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Configurar um gateway de rede virtual para ExpressRoute usando o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo explica as etapas para adicionar um gateway de rede virtual a uma VNet existente. Este artigo explica as etapas para adicionar, redimensionar e remover um gateway de VNet (rede virtual) para uma VNet existente. As etapas para essa configuração destinam-se especificamente a VNets criadas usando o modelo de implantação do Resource Manager que será usado em uma configuração do ExpressRoute. Para obter mais informações sobre gateways de rede virtual e definições de configuração do ExpressRoute, consulte [Sobre gateways de rede virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Antes de começar

As etapas para essa tarefa usam uma VNet com base nos valores na lista de referência de configuração a seguir. Usamos essa lista em nosso exemplo de etapas. É possível fazer uma cópia da lista para usá-la como referência, substituindo os valores pelos seus próprios.

**Lista de referência de configuração**

* Nome da Rede Virtual = “TestVNet”
* Espaço de endereço da Rede Virtual = 192.168.0.0/16
* Nome da sub-rede = "FrontEnd" 
    * Espaço de endereço da sub-rede = "192.168.1.0/24"
* Grupo de recursos = “TestRG”
* Local = "Leste dos EUA"
* Nome da Sub-rede do Gateway: “GatewaySubnet” Deve-se sempre nomear uma sub-rede do gateway como *GatewaySubnet*.
    * Espaço de endereço da Sub-Rede do Gateway = “192.168.200.0/26”
* Nome do gateway = "ERGW"
* Nome do IP público do gateway = "MyERGWVIP"
* Tipo de gateway = "ExpressRoute". Esse tipo é necessário para uma configuração de ExpressRoute.

Você pode exibir um [Vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) dessas etapas antes de iniciar sua configuração.

## <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No [portal](https://portal.azure.com), navegue até a rede virtual do Resource Manager para a qual você deseja criar um gateway de rede virtual.
2. Na seção **Configurações** da folha de sua rede virtual, clique em **Sub-redes** para expandir a folha Sub-redes.
3. Na folha **Sub-redes**, clique na **Sub-rede de +gateway** para abrir a folha **Adicionar sub-rede**. 
   
    ![Adicionar a sub-rede de gateway](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Adicionar a sub-rede de gateway")


4. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Esse valor é necessário para que o Azure reconheça a sub-rede como a sub-rede de gateway. Ajuste os valores preenchidos automaticamente de **Intervalo de endereços** para corresponder aos seus requisitos de configuração. É recomendável criar uma sub-rede do gateway com um /27 ou maior (/ 26, / 25, etc.). Em seguida, clique em **OK** para salvar os valores e criar a sub-rede de gateway.

    ![Adicionar a sub-rede](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Adicionar a sub-rede")

## <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal, no lado esquerdo, clique em **+** e digite "gateway de rede virtual" na pesquisa. Localize **Gateway de rede virtual** na pesquisa, retorne e clique na entrada. Na folha **Gateway de rede virtual**, clique em **Criar** na parte inferior. Isso abrirá a folha **Criar gateway de rede virtual**.
2. Na folha **Criar gateway de rede virtual**, preencha os valores do seu gateway de rede virtual.

    ![Campos da folha Criar gateway de rede virtual](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Campos da folha Criar gateway de rede virtual")
3. **Nome**: nomeie o seu gateway. Isso não é igual a nomear uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.
4. **Tipo de gateway**: selecione **ExpressRoute**.
5. **SKU**: selecione o SKU de gateway no menu suspenso.
6. **Local**: ajuste o campo **Local** para apontar para o local onde está sua rede virtual. Se o local não estiver apontando para a região onde reside sua rede virtual, a rede virtual não aparecerá na lista suspensa 'Escolher uma rede virtual'.
7. Escolha a rede virtual à qual você deseja adicionar este gateway. Clique em **rede virtual** para abrir a folha **escolher uma rede virtual** . Selecione a rede virtual. Se você não vir sua VNet, verifique se o campo **local** está apontando para a região em que sua rede virtual está localizada.
9. Escolha um endereço IP público. Clique em **Endereço IP público** para abrir a folha **Escolher endereço IP público**. Clique em **+Criar Novo** para abrir a folha **Criar endereço IP público**. Dê um nome para o seu endereço IP público. Essa folha cria um objeto de endereço IP público para o qual um endereço IP público será atribuído dinamicamente. Clique em **OK** para salvar as alterações a essa folha.
10. **Assinatura**: verifique se a assinatura correta foi selecionada.
11. **Grupo de recursos**: essa configuração é determinada pela Rede Virtual selecionada.
12. Não ajuste o **Local** depois de especificar as configurações acima.
13. Verifique as configurações. Se você quiser que seu gateway apareça no painel, poderá selecionar **Fixar no painel** na parte inferior da folha.
14. Clique em **Criar** para começar a criar o gateway. As configurações são validadas e o gateway é implantado. A criação de um gateway de rede virtual pode levar até 45 minutos para ser concluída.

## <a name="next-steps"></a>Próximas etapas
Depois de criar o gateway de VNet, é possível vincular sua VNet a um circuito do ExpressRoute. Consulte [Vincular uma Rede Virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
