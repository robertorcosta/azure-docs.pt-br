---
title: Excluir ofertas do Marketplace | Azure Marketplace
description: Excluir ofertas nos Marketplaces do Azure e do AppSource usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pabutler
ms.openlocfilehash: 6a75fbb0c4b9c364342a406a8076128346943101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826773"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Excluir SKUs ou ofertas do Azure Marketplace e do AppSource

Por motivos diversos, você pode optar por retirar a oferta do seu Microsoft Marketplace, o que pode ocorrer de duas formas:

- A *remoção de oferta* garante que novos clientes não possam adquirir ou implantar sua oferta, mas não tem impacto sobre clientes existentes, aos quais você precisa dar suporte de acordo com seu contrato de licença e leis pertinentes. 
- O *encerramento de oferta* é o processo de encerrar o contrato de licenciamento e/ou serviço entre você e seus clientes existentes. 

Diretrizes e políticas relacionadas à remoção e ao encerramento da oferta são regidas por [Microsoft Marketplace contrato de editor](https://go.microsoft.com/fwlink/?LinkID=699560) e as [políticas de participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (seção [oferecendo suspensão e remoção](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Este artigo aborda os diferentes cenários de exclusão compatíveis e as etapas necessárias para executar cada um deles.  

> [!NOTE]
> Você pode excluir uma oferta que não tenha sido publicada, bastando selecionar o botão **Excluir** na barra de ferramentas da guia **Editor**.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Excluir um SKU publicado do Azure Marketplace

É possível excluir um SKU publicado do Azure Marketplace usando as seguintes etapas:

1.  Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).
2.  Na página **Todas as ofertas**, selecione a oferta.  Sua oferta deve ser exibida na guia **Editor**.
3.  Na barra de ferramentas esquerda, selecione a guia **SKUs**. 
4.  Selecione o SKU que você deseja excluir e, em seguida, clique no botão **Excluir**.
5.  [Republique](./cpp-publish-offer.md) a oferta do Azure Marketplace.

Depois que a oferta modificada for publicada no Azure Marketplace, o SKU selecionado não será mais listado no Azure Marketplace e no portal do Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Reverter para uma versão anterior da SKU

Você pode excluir a versão atual de um SKU ativo do Azure Marketplace seguindo as etapas abaixo. Quando o processo for concluído, o SKU será revertido para a versão anterior.

1. Entre no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Na página **Todas as ofertas**, selecione a oferta.  Sua oferta deve ser exibida na guia **Editor**.
3. Na barra de ferramentas esquerda, selecione a guia **SKUs**. 
4. Exclua a versão mais recente do ativo da solução associada da lista de versões de disco.  Dependendo do tipo de oferta, esse campo pode ser **Versão de Disco**, **Versões de Pacote** ou outro ativo semelhante. 
5. [Republique](./cpp-publish-offer.md) a oferta do Azure Marketplace.

Depois que a oferta modificada for publicada no theAzure Marketplace, a versão atual do SKU listado não ficará mais listada. no Azure Marketplace e no portal do Azure.  O SKU é revertido para sua versão anterior.


## <a name="delete-a-live-offer"></a>Excluir uma oferta ativa

Há vários aspectos procedimentais, empresariais e legais para remover uma oferta online. Siga as etapas a seguir para obter diretrizes da equipe de suporte para remover uma oferta ativa do Azure Marketplace:

1.  Crie um tíquete de suporte usando a página [Criar um incidente](https://go.microsoft.com/fwlink/?linkid=844975) ou clicando em **Suporte** no canto superior direito do [Portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Selecione o tipo de oferta específico na lista **Tipo de problema** e selecione **Remover uma oferta publicada** na lista **Categoria**.

3.  Envie a solicitação.

A equipe de suporte orienta você pelo processo de exclusão da oferta.

> [!NOTE]
> A exclusão de uma oferta (ou SKU) não afeta as respectivas compras atuais. Essas compras continuarão funcionando como antes. No entanto, SKUs ou ofertas excluídos não estarão disponíveis para nenhuma compra futura.


## <a name="next-steps"></a>Próximas etapas

Depois de se familiarizar com as operações básicas usadas para gerenciar ofertas, você está pronto para criar uma instância de uma [oferta do marketplace](../cpp-marketplace-offers.md) da Microsoft.
