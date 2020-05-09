---
title: 'Tutorial: Escalar uma nuvem privada'
description: Neste tutorial, você usa o portal do Azure para escalar uma nuvem privada da Versão prévia da AVS (Solução VMware no Azure).
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739721"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Tutorial: Escalar uma nuvem privada da Versão prévia da AVS (Solução VMware no Azure)

Para aproveitar ao máximo sua experiência com a nuvem privada da Versão prévia da AVS, escale os clusters e hosts de maneira a refletir aquilo de que você precisa para as cargas de trabalho planejadas. Como a AVS não dará suporte ao vCenter local durante a versão prévia, você precisará usar o que já criou por meio do portal do Azure.

Você pode escalar o número de clusters e de hosts em uma nuvem privada conforme necessário para a carga de trabalho do aplicativo. As limitações de desempenho e disponibilidade para serviços específicos precisam ser tratadas caso a caso em seu ambiente de nuvem da Versão prévia da AVS. Os limites de cluster e host em uma nuvem privada são informados no [artigo sobre o conceito de nuvem privada](concepts-private-clouds-clusters.md).

Neste tutorial, você usa o portal do Azure para:

> [!div class="checklist"]
> * Adicionar um cluster a uma nuvem privada existente
> * Adicionar hosts a um cluster existente

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma nuvem privada. Caso ainda não tenha criado uma nuvem privada, use o [tutorial de como criar uma nuvem privada](tutorial-create-private-cloud.md) para criar essa nuvem e configurar a rede para a nuvem privada do VMware no Azure para estabelecer a rede virtual necessária.

## <a name="add-a-new-cluster"></a>Adicionar um cluster

Na página de visão geral de uma nuvem privada existente, em **Gerenciar**, selecione **Escalar nuvem privada**. Em seguida, selecione **+ Adicionar um cluster**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="selecione adicionar um cluster" border="true":::

Na página **Adicionar cluster**, use o controle deslizante para selecionar o número de hosts. Clique em **Salvar**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Configurar um novo cluster de nuvem privada" border="true":::

A implantação do novo cluster será iniciada.

## <a name="scale-a-cluster"></a>Dimensionar um cluster 

Na página de visão geral de uma nuvem privada existente, selecione **Dimensionar nuvem privada** e selecione o ícone de lápis para editar o cluster.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selecione Escalar nuvem privada em visão geral" border="true":::

Na página **Editar cluster**, use o controle deslizante para selecionar o número de hosts. Clique em **Salvar**.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Configurar um novo cluster de nuvem privada" border="true":::

A adição de hosts ao cluster será iniciada.

## <a name="next-steps"></a>Próximas etapas

Se precisar de outra nuvem privada da AVS, [crie outra nuvem privada](tutorial-create-private-cloud.md) seguindo os mesmos pré-requisitos de rede, cluster e limites de host...

<!-- LINKS - external-->

<!-- LINKS - internal -->
