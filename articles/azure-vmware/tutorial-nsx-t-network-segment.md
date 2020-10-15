---
title: Tutorial – Criar um segmento de rede do NSX-T na Solução VMware no Azure
description: Saiba como criar os segmentos de rede do NSX-T que são usados para VMs no vCenter
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 780cac15efc043b9ae44b77af1234adca3fec5a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254526"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Tutorial: Criar um segmento de rede do NSX-T na Solução VMware no Azure

Os segmentos de rede criados no NSX-T Manager são usados como redes para VMs (máquinas virtuais) no vCenter. As VMs criadas no vCenter são colocadas nos segmentos de rede criados no NSX-T e ficam visíveis no vCenter.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Navegar no NSX-T Manager para adicionar segmentos de rede
> * Adicionar um novo segmento de rede
> * Observe o novo segmento de rede no vCenter

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter uma nuvem privada da Solução VMware no Azure com acesso às interfaces de gerenciamento do vCenter e do NSX-T Manager para concluir este tutorial. Para obter mais informações, confira o tutorial [Configurar a rede para sua nuvem privada do VMware no Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Provisionar um segmento de rede no NSX-T

1. No vCenter da sua nuvem privada, selecione **Datacenter SDDC > Redes** e observe que ainda não existem redes.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. No NSX-T Manager da sua nuvem privada, selecione **Rede**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::.

1. Selecione **Segmentos**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. Na página de visão geral Segmentos do NSX-T, selecione **ADICIONAR SEGMENTO**. Três segmentos são criados como parte do provisionamento de nuvem privada e não podem ser usados para VMs.  Você precisará adicionar um novo segmento de rede para essa finalidade.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. Nomeie o segmento, escolha o Gateway Nível 1 pré-configurado (TNTxx-T1) como o **Gateway Conectado**, mantenha o **Tipo** como Flexível, escolha a sobreposição pré-configurada **Zona de Transporte** (TNTxx-OVERLAY-TZ) e selecione Definir Sub-redes. Todas as outras configurações desta seção, bem como as **PORTAS** e os **PERFIS DE SEGMENTO**, podem permanecer com a configuração padrão no estado em que se encontra.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. Defina o endereço IP do gateway para o novo segmento e selecione **ADICIONAR**. O endereço IP usado precisa estar em um bloco de endereço RFC1918 não sobreposto, o que garante que você pode se conectar às VMs no novo segmento.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. Aplique o novo segmento de rede selecionando **APLICAR** e salve a configuração com **SALVAR**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. O segmento de rede foi criado, e você recusará a opção de continuar configurando o segmento selecionando **NÃO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. Confirme se o novo segmento de rede está presente no NSX-T selecionando **Rede > Segmentos** e vendo se o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

1. Confirme se o novo segmento de rede está presente no vCenter selecionando **Rede > Datacenter SDDC** e observe se o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="No vCenter da sua nuvem privada, selecione Datacenter SDDC > Redes e observe que ainda não existem redes.":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou os segmentos de rede do NSX-T que são usados para VMs no vCenter. Agora, você pode [criar uma biblioteca de conteúdo para implantar VMs na Solução VMware no Azure](deploy-vm-content-library.md) e provisionar uma VM na rede que você criou neste tutorial.

Caso contrário, prossiga para o próximo tutorial para saber como criar um emparelhamento de Alcance Global do ExpressRoute com uma nuvem privada em uma Solução VMware no Azure.

> [!div class="nextstepaction"]
> [Emparelhar ambientes locais com uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
