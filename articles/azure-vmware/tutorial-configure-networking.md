---
title: Tutorial – Configurar a rede para sua nuvem privada do VMware no Azure
description: Saiba como a criar e configurar a rede necessária para implantar sua nuvem privada no Azure
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: ff071e0d6eaf1552634433a76e4eade530c603b6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750492"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configurar a rede para a sua nuvem privada da VMware no Azure

Uma nuvem privada da Solução VMware no Azure requer uma Rede Virtual do Azure. Como a Solução VMware no Azure não dá suporte para o vCenter local durante a versão prévia, são necessárias etapas adicionais para a integração ao ambiente local. Também é necessário configurar um circuito do ExpressRoute e um gateway de rede virtual e isso será abordado neste tutorial.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Conectar o circuito do ExpressRoute ao gateway
> * Localizar as URLs do vCenter e do NSX Manager

## <a name="prerequisites"></a>Pré-requisitos 
Para criar uma rede virtual, verifique se você criou uma [Nuvem privada da Solução VMware no Azure](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Entre no [portal do Azure](https://portal.azure.com).

1. Navegue até o grupo de recursos criado no [tutorial de criação de uma nuvem privada](tutorial-create-private-cloud.md) e selecione **+ Adicionar** para definir um novo recurso. 

1. Na caixa de texto **Pesquisar no Marketplace**, digite **Rede Virtual**. Localize o recurso Rede Virtual e selecione-o.

1. Na página **Rede Virtual**, selecione **Criar** para configurar sua rede virtual para sua nuvem privada.

1. Na página **Criar Rede Virtual**, insira os detalhes da sua rede virtual.

1. Na guia **Básico**, insira um nome para a rede virtual, escolha a região apropriada e selecione **Avançar: Endereços IP**.

1. Na guia **Endereços IP**, em **Espaço de endereço IPv4**, insira o espaço de endereço criado no tutorial anterior.

   > [!IMPORTANT]
   > Use um espaço de endereço que **não** se sobreponha ao espaço de endereço usado quando você criou a nuvem privada no tutorial anterior.

1. Selecione **+ Adicionar sub-rede** e, na página **Adicionar sub-rede**, dê à sub-rede um nome e um intervalo de endereços apropriado. Quando concluir, selecione **Adicionar**.

1. Selecione **Examinar + criar**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Selecione Examinar + criar." border="true":::

1. Verifique as informações e selecione **Criar**. Quando a implantação for concluída, você verá a rede virtual no grupo de recursos.

## <a name="create-a-virtual-network-gateway"></a>Criar um gateway de rede virtual

Agora que você criou uma rede virtual, você criará um gateway de rede virtual.

1. Em seu grupo de recursos, selecione **+ Adicionar** para adicionar um novo recurso.

1. Na caixa de texto **Pesquisar no Marketplace**, digite **Gateway de rede virtual**. Localize o recurso Rede Virtual e selecione-o.

1. Na página **Gateway de rede virtual**, selecione **Criar**.

1. Na guia Básico da página **Criar gateway de rede virtual**, forneça valores para os campos e selecione **Examinar + criar**. 

   | Campo | Valor |
   | --- | --- |
   | **Assinatura** | Este valor já está preenchido com a Assinatura à qual o grupo de recursos pertence. |
   | **Grupo de recursos** | Este valor já está preenchido para o grupo de recursos atual. Ele deve ser o grupo de recursos criado em um teste anterior. |
   | **Nome** | Insira um nome exclusivo para o gateway de rede virtual. |
   | **Região** | Selecione a localização geográfica do gateway de rede virtual. |
   | **Tipo de gateway** | Selecione **ExpressRoute**. |
   | **SKU** | Deixe o valor padrão: **padrão**. |
   | **Rede virtual** | Selecione a rede virtual criada anteriormente. Se não vir a rede virtual, verifique se a região do gateway corresponde à região da rede virtual. |
   | **Intervalo de endereços da sub-rede do gateway** | Esse valor é preenchido quando você seleciona a rede virtual. Não altere o valor padrão. |
   | **Endereço IP público** | Selecione **Criar novo**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Na guia Básico da página Criar gateway de rede virtual, forneça valores para os campos e selecione Examinar + criar." border="true":::

1. Verifique se os detalhes estão corretos e selecione **Criar** para iniciar a implantação do gateway de rede virtual. 
1. Quando a implantação for concluída, passe para a próxima seção para conectar sua conexão do ExpressRoute ao gateway de rede virtual que contém a nuvem privada da Solução VMware no Azure.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Conectar o ExpressRoute ao gateway de rede virtual

Agora que você implantou um gateway de rede virtual, você adicionará uma conexão entre ele e sua nuvem privada da Solução VMware no Azure.

1. Navegue até a nuvem privada criada no tutorial anterior, selecione **Conectividade** em **Gerenciar** e selecione a guia **ExpressRoute**.

1. Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma. Para fazer isso, selecione **+ Solicitar uma chave de autorização**.

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma. Para fazer isso, selecione + Solicitar uma chave de autorização." border="true":::

1. Navegue até o Gateway de Rede Virtual criado na etapa anterior e, em **Configurações**, selecione **Conexões**. Na página **Conexões**, selecione **+ Adicionar**.

1. Na página **Adicionar conexão**, forneça valores para os campos e selecione **OK**. 

   | Campo | Valor |
   | --- | --- |
   | **Nome**  | Insira um nome para a conexão.  |
   | **Tipo de conexão**  | Selecione **ExpressRoute**.  |
   | **Resgatar autorização**  | Esta caixa deve estar selecionada.  |
   | **Gateway de rede virtual** | O gateway de Rede Virtual criado anteriormente.  |
   | **Chave de autorização**  | Copie e cole a chave de autorização da guia ExpressRoute do seu Grupo de recursos. |
   | **URI de circuito de par**  | Copie e cole a ID do ExpressRoute da guia ExpressRoute do seu Grupo de recursos.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Na página Adicionar conexão, forneça valores para os campos e selecione OK." border="true":::

A conexão entre o circuito do ExpressRoute e sua Rede Virtual é criada.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localizar as URLs do vCenter e do NSX Manager

Para entrar no vCenter e no NSX Manager, você precisará das URLs do cliente Web do vCenter e do site do NSX-T Manager. 

Navegue até sua nuvem privada da Solução VMware no Azure, em **Gerenciar**, selecione **Identidade**, aqui você encontrará as informações necessárias.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Navegue até sua nuvem privada da Solução VMware no Azure, em Gerenciar, selecione Identidade, aqui você encontrará as informações necessárias." border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de rede virtual
> * Conectar o circuito do ExpressRoute ao gateway
> * Localizar as URLs do vCenter e do NSX Manager

Continue para o próximo tutorial para aprender a criar uma caixa de salto que é usada para se conectar ao seu ambiente, para que você possa gerenciar a nuvem privada localmente.

> [!div class="nextstepaction"]
> [Acessar a Nuvem Privada](tutorial-access-private-cloud.md)
