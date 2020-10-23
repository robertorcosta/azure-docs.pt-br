---
title: Como criar e gerenciar o DHCP
description: Este artigo explica como gerenciar o DHCP na solução VMware do Azure.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461049"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Como criar e gerenciar o DHCP na solução VMware do Azure

O NSX-T fornece a capacidade de configurar o DHCP para sua nuvem privada. Se você usar o NSX-T para hospedar o servidor DHCP, consulte [criar servidor DHCP](#create-dhcp-server). Caso contrário, se você tiver um servidor DHCP externo de terceiros em sua rede, consulte [Criar serviço de retransmissão de DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Criar servidor DHCP

Use as etapas a seguir para configurar um servidor DHCP no NSX-T.

1. No NSX Manager, navegue até a guia **rede** e selecione **DHCP**. 
1. Selecione **Adicionar servidor** e forneça o nome do servidor e o endereço IP. 
1. Selecione **Salvar**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Adicionar servidor DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Conecte o servidor DHCP ao gateway de camada 1.

1. Selecione **gateways da camada 1**, o gateway da lista e, em seguida, selecione **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Selecione **nenhuma alocação de IP definida** para adicionar uma sub-rede.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Selecione **servidor local DHCP** para o **tipo**. 
1. Selecione **DHCP padrão** para o **servidor DHCP** e, em seguida, selecione **salvar**.


1. Na janela **Gateway de camada 1** , selecione **salvar**. 
1. Selecione **fechar edição** para concluir.

### <a name="add-a-network-segment"></a>Adicionar um segmento de rede

Depois de criar o servidor DHCP, você precisará adicionar segmentos de rede a ele.

1. No NSX-T, selecione a guia **rede** e selecione **segmentos** em **conectividade**. 
1. Selecione **Adicionar segmento** e nomeie o segmento e a conexão com o gateway de camada 1. 
1. Selecione **definir sub-redes** para configurar uma nova sub-rede. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Na janela **definir** sub-redes, selecione **Adicionar sub-rede**. 
1. Insira o endereço IP do gateway e o intervalo DHCP e selecione **Adicionar** e **aplicar**

1. Selecione **salvar** para adicionar o novo segmento de rede.

## <a name="create-dhcp-relay-service"></a>Criar serviço de retransmissão DHCP

1. Selecione a guia **rede** e, em **Gerenciamento de IP**, selecione **DHCP**. 
1. Selecione **Adicionar servidor**. 
1. Selecione retransmissão DHCP para o **tipo de servidor** e insira o nome do servidor e o endereço IP do servidor de retransmissão. 
1. Selecione **Salvar**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Selecione **gateways de camada 1** em **conectividade**. 
1. Selecione as reticências verticais no gateway de camada 1 e selecione **Editar**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Selecione **nenhuma alocação de IP definida** para definir a alocação de endereço IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Selecione **servidor de retransmissão DHCP** para **tipo**.
1. Selecione o servidor de retransmissão DHCP para **retransmissão DHCP**. 
1. Selecione **Salvar**.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Especificar um IP de intervalo DHCP em um segmento

> [!NOTE]
> Essa configuração é necessária para perceber a funcionalidade de retransmissão DHCP no segmento de cliente DHCP. 

1. Em **conectividade**, selecione **segmentos**. 
1. Selecione as reticências verticais e selecione **Editar**. 

   >[!TIP]
   >Se você quiser adicionar um novo segmento, selecione **Adicionar segmento**.

1. Adicione detalhes sobre o segmento. 
1. Selecione o valor em **definir sub-redes** para adicionar ou modificar a sub-rede.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Adicionar servidor DHCP" border="true":::

1. Selecione as reticências verticais e escolha **Editar**. Se você precisar criar uma nova sub-rede, selecione **Adicionar sub-rede** para criar um gateway e configurar um intervalo DHCP. 
1. Forneça o intervalo do pool de IPS e selecione **aplicar**e, em seguida, selecione **salvar**

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Adicionar servidor DHCP" border="true":::

   Um pool de servidores DHCP é atribuído ao segmento.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Adicionar servidor DHCP" border="true":::
