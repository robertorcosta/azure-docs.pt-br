---
title: 'Tutorial: Criar um segmento de rede do NSX-T na AVS (Solução VMware no Azure)'
description: Neste tutorial, você criou os segmentos de rede do NSX-T que são usados para VMs no vCenter
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 5654fbb6a063d4dfeb541c20407f9a09dff1509f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093235"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution-avs"></a>Tutorial: Criar um segmento de rede do NSX-T na AVS (Solução VMware no Azure)

Os segmentos de rede criados no NSX-T Manager são usados como redes para VMs (máquinas virtuais) no vCenter. As VMs criadas no vCenter são colocadas nos segmentos de rede criados no NSX-T e ficam visíveis no vCenter.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Navegar no NSX-T Manager para adicionar segmentos de rede
> * Adicionar um novo segmento de rede
> * Observe o novo segmento de rede no vCenter

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter uma nuvem privada da AVS com acesso às interfaces de gerenciamento do vCenter e do NSX-T Manager para concluir este tutorial. Confira o [Tutorial: Configurar a rede para a sua nuvem privada da VMware no Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Provisionar um segmento de rede no NSX-T

1. No vCenter da sua nuvem privada, selecione **Datacenter SDDC > Redes** e observe que ainda não existem redes.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="Selecionar Datacenter SDDC > Redes":::

1. No NSX-T Manager da sua nuvem privada, selecione **Rede**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="Selecionar Rede para navegar até a exibição Visão Geral da Rede do NSX-T Manager.":::

1. Selecione **Segmentos**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Selecionar Segmentos na página Visão Geral da Rede.":::

1. Na página de visão geral Segmentos do NSX-T, selecione **ADICIONAR SEGMENTO**. Três segmentos são criados como parte do provisionamento de nuvem privada e não podem ser usados para VMs.  Você precisará adicionar um novo segmento de rede para essa finalidade.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Selecionar Adicionar Segmento na página de visão geral Segmento de Rede.":::

1. Nomeie o segmento, escolha o Gateway Nível 1 pré-configurado (TNTxx-T1) como o **Gateway Conectado**, mantenha o **Tipo** como Flexível, escolha a sobreposição pré-configurada **Zona de Transporte** (TNTxx-OVERLAY-TZ) e selecione Definir Sub-redes. Todas as outras configurações desta seção, bem como as **PORTAS** e os **PERFIS DE SEGMENTO**, podem permanecer com a configuração padrão no estado em que se encontra.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Definir o Nome do Segmento, o Gateway Conectado e o Tipo e a Zona de Transporte e selecionar “Definir Sub-redes”.":::

1. Defina o endereço IP do gateway para o novo segmento e selecione **ADICIONAR**. O endereço IP usado precisa estar em um bloco de endereço RFC1918 não sobreposto, o que garante que você pode se conectar às VMs no novo segmento.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Especificar o endereço IP do gateway do segmento na notação CIDR e selecionar ADICIONAR.":::

1. Aplique o novo segmento de rede selecionando **APLICAR** e salve a configuração com **SALVAR**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Aplicar o novo segmento de rede à configuração do NSX-T com APLICAR.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Salvar o novo segmento de rede na configuração do NSX-T com SALVAR.":::

1. O segmento de rede foi criado, e você recusará a opção de continuar configurando o segmento selecionando **NÃO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Recusar a opção para fazer outras configurações no segmento de rede recém-criado selecionando NÃO.":::

1. Confirme se o novo segmento de rede está presente no NSX-T selecionando **Rede > Segmentos** e vendo se o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Confirmar se o novo segmento de rede está presente no NSX-T.":::

1. Confirme se o novo segmento de rede está presente no vCenter selecionando **Rede > Datacenter SDDC** e observe se o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Confirmar se o novo segmento de rede está presente no vCenter.":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou os segmentos de rede do NSX-T que são usados para VMs no vCenter. Agora você pode usar o [Tutorial: Criar uma biblioteca de conteúdo para implantar VMs na AVS (Solução VMware no Azure)](tutorial-deploy-vm-content-library.md) para criar uma biblioteca de conteúdo no vCenter e provisionar uma VM na rede que você criou neste tutorial.

<!-- LINKS - external-->

<!-- LINKS - internal -->
