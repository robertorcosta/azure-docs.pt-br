---
title: Tutorial – Configurar a rede para sua nuvem privada do VMware no Azure
description: Saiba como a criar e configurar a rede necessária para implantar sua nuvem privada no Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6cac420fb77526746dbdbfef5a88b071c007d555
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148099"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configurar a rede para a sua nuvem privada da VMware no Azure

Uma nuvem privada da AVS (Solução VMware no Azure) requer uma rede virtual. Como a AVS não tem suporte para o vCenter local durante a versão prévia, são necessárias etapas adicionais para a integração ao ambiente local. Também é necessário configurar um circuito do ExpressRoute e um Gateway de Rede Virtual e isso será abordado neste tutorial.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um Gateway de Rede Virtual
> * Conectar o circuito do ExpressRoute ao gateway
> * Localizar as URLs do vCenter e do NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Navegue até o grupo de recursos criado no [tutorial anterior](tutorial-create-private-cloud.md) e selecione **+ Adicionar** para definir um novo recurso.

Na caixa de texto **Pesquisar no Marketplace**, digite **Rede Virtual**. Localize o recurso Rede Virtual e selecione-o.

Na página Rede Virtual, selecione **Criar** para configurar a rede virtual para sua nuvem privada.

Na página **Criar Rede Virtual**, insira os detalhes relevantes para a rede virtual; uma descrição das propriedades é mostrada na seguinte tabela:

> [!IMPORTANT]
> Use um espaço de endereço que **não** se sobreponha ao espaço de endereço usado quando você criou a nuvem privada no tutorial anterior.

Na guia **Básico**, insira um nome para a rede virtual, escolha a região apropriada e selecione **Avançar: Endereços IP**

Na guia **Endereços IP**, em **Espaço de endereço IPv4**, insira o espaço de endereço criado no tutorial anterior.

Selecione **+ Adicionar sub-rede** e, na página **Adicionar sub-rede**, dê à sub-rede um nome e um intervalo de endereços apropriado. Quando concluir, selecione **Adicionar**.

Selecione **Examinar + Criar**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="criar uma rede virtual" border="true":::

Verifique as informações e selecione **Criar**. Quando a implantação for concluída, você verá a rede virtual no grupo de recursos.

## <a name="create-a-virtual-network-gateway"></a>Criar um Gateway de Rede Virtual

Você criou uma rede virtual na seção anterior, agora criará um Gateway de Rede Virtual.

Em seu grupo de recursos, selecione **+ Adicionar** para adicionar um novo recurso.

Na caixa de texto **Pesquisar no Marketplace**, digite **Gateway de rede virtual**. Localize o recurso Rede Virtual e selecione-o.

Na página **Gateway de rede virtual**, selecione **Criar**.

Na guia Básico da página **Criar gateway de rede virtual**, forneça valores para os campos. Descrições dos campos são mostradas na seguinte tabela:

| Campo | Valor |
| --- | --- |
| **Assinatura** | Este valor já está preenchido com a Assinatura à qual o grupo de recursos pertence. |
| **Grupo de recursos** | Este valor já está preenchido para o grupo de recursos atual. Ele deve ser o grupo de recursos criado em um teste anterior. |
| **Nome** | Insira um nome exclusivo para o gateway de rede virtual. |
| **Região** | Selecione a localização geográfica do gateway de rede virtual. |
| **Tipo de gateway** | Selecione **ExpressRoute**. |
| **Tipo de VPN** | Selecione **Baseado em rota**. |
| **SKU** | Deixe o valor padrão: **padrão**. |
| **Rede virtual** | Selecione a rede virtual criada anteriormente. Se não vir a rede virtual, verifique se a região do gateway corresponde à região da rede virtual. |
| **Intervalo de endereços da sub-rede do gateway** | Esse valor é preenchido quando você seleciona a rede virtual. Não altere o valor padrão. |
| **Endereço IP público** | Selecione **Criar novo**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="criar um gateway" border="true":::

Selecione **Examinar + criar**, na página seguinte, verifique se os detalhes estão corretos e selecione **Criar** para iniciar a implantação do gateway de rede virtual. Quando a implantação for concluída, vá para a próxima seção deste tutorial para conectar sua conexão do ExpressRoute à rede virtual que contém a nuvem privada.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Conectar ExpressRoute ao Gateway de Rede Virtual

Esta seção descreve como adicionar uma conexão entre sua nuvem privada da AVS e o gateway de rede virtual que você criou.

Navegue até a nuvem privada criada no tutorial anterior, selecione **Conectividade** em **Gerenciar** e selecione a guia **ExpressRoute**.

Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma. Para fazer isso, selecione **+ Solicitar uma chave de autorização**

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="solicitar uma chave de autorização" border="true":::

Navegue até o Gateway de Rede Virtual criado na etapa anterior e, em **Configurações**, selecione **Conexões**. Na página **Conexões**, selecione **+ Adicionar**.

Na página **Adicionar conexão**, forneça valores para os campos. Descrições dos campos são mostradas na seguinte tabela:

| Campo | Valor |
| --- | --- |
| **Nome**  | Insira um nome para a conexão.  |
| **Tipo de conexão**  | Selecione **ExpressRoute**.  |
| **Resgatar autorização**  | Esta caixa deve estar selecionada.  |
| **Gateway de rede virtual** | O gateway de rede virtual criado anteriormente  |
| **Chave de autorização**  | Copie e cole a chave de autorização da guia ExpressRoute do seu Grupo de recursos. |
| **URI de circuito de par**  | Copie e cole a ID do ExpressRoute da guia ExpressRoute do seu Grupo de recursos.  |

Selecione **OK**. Isso cria a conexão entre o circuito do ExpressRoute e a rede virtual.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="adicionar uma conexão" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localizar as URLs do vCenter e do NSX Manager

Para entrar no vCenter e no NSX Manager, você precisará das URLs do cliente Web do vCenter e do site do NSX-T Manager. Para localizar as URLs:

Navegue até sua nuvem privada da AVS, em **Gerenciar**, selecione **Identidade**, aqui você encontrará as informações necessárias.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="localizar as URLs do vCenter" border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um Gateway de Rede Virtual
> * Conectar o circuito do ExpressRoute ao gateway
> * Localizar as URLs do vCenter e do NSX Manager

Continue para o próximo tutorial para aprender a criar uma caixa de salto que é usada para se conectar ao seu ambiente, para que você possa gerenciar a nuvem privada localmente.

> [!div class="nextstepaction"]
> [Acessar a Nuvem Privada](tutorial-access-private-cloud.md)
