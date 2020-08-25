---
title: 'Tutorial: Escalar uma nuvem privada'
description: Neste tutorial, você usará o portal do Azure para escalar uma nuvem privada da Versão Prévia da Solução VMware no Azure.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: dddfbddd57f3ad6b541d11c360aeadea4383044a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750440"
---
# <a name="tutorial-scale-an-azure-vmware-solution-preview-private-cloud"></a>Tutorial: Escalar uma nuvem privada da Versão Prévia da Solução VMware no Azure

Para aproveitar ao máximo sua experiência com a nuvem privada da Versão Prévia da Solução VMware no Azure, escale os clusters e os hosts de maneira a refletir o que você precisa para as cargas de trabalho planejadas. Como a Solução VMware no Azure não é compatível com o vCenter local durante a versão prévia, será preciso usar o que você já criou no portal do Azure.

Você pode escalar o número de clusters e de hosts em uma nuvem privada conforme necessário para a carga de trabalho do aplicativo. As limitações de desempenho e disponibilidade para serviços específicos precisam ser resolvidas caso a caso em seu ambiente de nuvem da Versão Prévia da Solução VMware no Azure. Os limites de cluster e host em uma nuvem privada são informados no [artigo sobre o conceito de nuvem privada](concepts-private-clouds-clusters.md).

Neste tutorial, você usa o portal do Azure para:

> [!div class="checklist"]
> * Adicionar um cluster a uma nuvem privada existente
> * Adicionar hosts a um cluster existente

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma nuvem privada. Caso ainda não tenha criado uma nuvem privada, use o [tutorial de como criar uma nuvem privada](tutorial-create-private-cloud.md) para criar essa nuvem e configurar a rede para a nuvem privada do VMware no Azure para estabelecer a rede virtual necessária.

## <a name="add-a-new-cluster"></a>Adicionar um cluster

1. Na página de visão geral de uma nuvem privada existente, em **Gerenciar**, selecione **Escalar nuvem privada**. Em seguida, selecione **+ Adicionar um cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="selecione adicionar um cluster" border="true":::

1. Na página **Adicionar cluster**, use o controle deslizante para selecionar o número de hosts. Clique em **Salvar**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Na página Adicionar cluster, use o controle deslizante para selecionar o número de hosts. Clique em Salvar." border="true":::

   A implantação do novo cluster será iniciada.

## <a name="scale-a-cluster"></a>Dimensionar um cluster 

1. Na página de visão geral de uma nuvem privada existente, selecione **Dimensionar nuvem privada** e selecione o ícone de lápis para editar o cluster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selecione Escalar nuvem privada em visão geral" border="true":::

1. Na página **Editar cluster**, use o controle deslizante para selecionar o número de hosts. Clique em **Salvar**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Na página Editar Cluster, use o controle deslizante para selecionar o número de hosts. Clique em Salvar." border="true":::

   A adição de hosts ao cluster será iniciada.

## <a name="next-steps"></a>Próximas etapas

Se precisar de outra nuvem privada da Solução VMware no Azure, [crie outra nuvem privada](tutorial-create-private-cloud.md) seguindo os mesmos pré-requisitos de rede, cluster e limites de host.

<!-- LINKS - external-->

<!-- LINKS - internal -->
