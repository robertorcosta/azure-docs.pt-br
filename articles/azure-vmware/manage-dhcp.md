---
title: Como criar e gerenciar o DHCP
description: Este artigo explica como gerenciar o DHCP na solução VMware do Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 3fc3de228179925afdf1b7c1015c577fd9c4c924
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752165"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Como criar e gerenciar o DHCP na solução VMWare do Azure

O NSX-T fornece a capacidade de configurar o DHCP para sua nuvem privada. Se você planeja usar o NSX-T para hospedar o servidor DHCP, consulte [criar servidor DHCP](#create-dhcp-server). Caso contrário, se você tiver um servidor DHCP externo de terceiros em sua rede e quiser retransmitir solicitações para esse servidor DHCP, consulte [Criar serviço de retransmissão DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Criar servidor DHCP

Use as etapas a seguir para configurar um servidor DHCP no NSX-T.

No NSX Manager, navegue até a guia **rede** e selecione **DHCP** em **Gerenciamento de IP**. Selecione o botão **Adicionar servidor** . Em seguida, forneça o nome do servidor e o endereço IP do servidor. Quando terminar, selecione **salvar**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Adicionar servidor DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Conecte o servidor DHCP ao gateway de camada 1.

1. Selecione **gateways da camada 1**, selecione o gateway e selecione **Editar**

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Selecione o gateway a ser usado" border="true":::

1. Adicionar uma sub-rede selecionando **nenhum conjunto de alocação de IP**

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Adicionar uma sub-rede" border="true":::

1. Na próxima tela, selecione **servidor local DHCP** na lista suspensa **tipo** . Para **servidor DHCP**, selecione **DHCP padrão** e selecione **salvar**.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="selecionar opções para o servidor DHCP" border="true":::

1. Na janela **Gateway de camada 1** , selecione **salvar**. Na próxima tela, você verá **as alterações salvas**, selecione **fechar edição** para concluir.

### <a name="add-a-network-segment"></a>Adicionar um segmento de rede

Depois de criar o servidor DHCP, você precisará adicionar segmentos de rede a ele.

1. No NSX-T, selecione a guia **rede** e selecione **segmentos** em **conectividade**. Selecione **Adicionar segmento**. Nomeie o segmento e a conexão com o gateway de camada 1. Em seguida, selecione **definir sub-redes** para configurar uma nova sub-rede. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Adicionar um novo segmento de rede" border="true":::

1. Na janela **definir** sub-redes, selecione **Adicionar sub-rede**. Insira o endereço IP do gateway e o intervalo DHCP e selecione **Adicionar** e **aplicar**

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="Adicionar segmento de rede" border="true":::

1. Ao concluir, selecione **salvar** para concluir a adição de um segmento de rede.

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="segmentos concluídos" border="true":::

## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

1. Na janela NXT-T, selecione a guia **rede** e, em **Gerenciamento de IP**, selecione **DHCP**. Selecione **Adicionar servidor**. Escolha retransmissão DHCP para o **tipo de servidor** e insira o nome do servidor e o endereço IP do servidor de retransmissão. Selecione **Salvar** para salvar as alterações.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="criar servidor de retransmissão DHCP" border="true":::

1. Selecione **gateways de camada 1** em **conectividade**. Selecione as reticências verticais no gateway de camada 1 e escolha **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="editar gateway da camada 1" border="true":::

1. Selecione **nenhuma alocação de IP definida** para definir a alocação de endereço IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="editar alocação de endereço IP" border="true":::

1. Na caixa de diálogo, para **tipo**, selecione **servidor de retransmissão DHCP**. Na lista suspensa **retransmissão de DHCP** , selecione o servidor de retransmissão DHCP. Quando terminar, selecione **salvar**

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="definir gerenciamento de endereço IP" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>Especificar um IP de intervalo DHCP no segmento

> [!NOTE]
> Essa configuração é necessária para perceber a funcionalidade de retransmissão DHCP no segmento de cliente DHCP. 

1. Em **conectividade**, selecione **segmentos**. Selecione as reticências verticais e selecione **Editar**. Em vez disso, se você quisesse adicionar um novo segmento, você pode selecionar **Adicionar segmento** para criar um novo segmento.

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="editar uma sub-rede de rede" border="true":::

1. Adicione detalhes sobre o segmento. Selecione o valor em **sub-redes** ou defina sub- **redes** para adicionar ou modificar a sub-rede.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmentos de rede" border="true":::

1. Selecione as reticências verticais e escolha **Editar**. Se você precisar criar uma nova sub-rede, selecione **Adicionar sub-rede** para criar um gateway e configurar um intervalo DHCP. Forneça o intervalo do pool de IPS e selecione **aplicar**e, em seguida, selecione **salvar**

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="editar sub-redes" border="true":::

1. Agora, um pool de servidores DHCP é atribuído ao segmento.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Pool de servidores DHCP atribuído ao segmento" border="true":::
