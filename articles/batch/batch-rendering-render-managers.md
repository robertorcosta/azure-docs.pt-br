---
title: Suporte para gerenciador de reinicialização - Lote do Azure
description: Usando a integração do gerenciador de renderização azure Batch. Saiba mais sobre suporte embutido ou complementos para gestores de renderização populares.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449692"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Usar o Lote do Azure com gerenciadores de farm de renderização

Se você estiver usando um farm de renderização local existente, é altamente provável que um gerenciador de renderização controle a capacidade do farm de renderização e renderize trabalhos.

O Azure fornece complementos ou suporte interno a gerenciadores de renderização populares. Em seguida, será possível adicionar e remover VMs do Azure, incluindo VMs com o licenciamento de aplicativo de pagamento por uso e VMs de baixa prioridade.

Há suporte aos seguintes gerenciadores de renderização:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

O Azure Render Hub simplifica a criação e a gestão das fazendas de renderização do Azure.  O Render Hub tem suporte nativo para PipelineFx Qube e Deadline 10.  Para obter mais informações e instruções detalhadas, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Usar Azure com PipelineFX Qube

O Azure Render Hub suporta gestores de renderização populares, incluindo o Deadline.  Para obter instruções sobre como implantar e usar o Render Hub, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

Scripts e instruções para permitir que as VMs do pool azure batch sejam usadas como trabalhadores do Qube também estão disponíveis [no repositório GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Usar Azure com Royal Render

O Royal Render tem integração do Azure e Lote do Azure interna, permitindo que você estenda um farm de renderização com VMs baseadas no Azure. Para um resumo, consulte [os arquivos de ajuda](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Para um exemplo de um cliente Royal Render usando a integração do Azure, consulte a [história do cliente Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Usar Azure com Thinkbox Deadline

O Azure Render Hub suporta gestores de renderização populares, incluindo o Deadline.  Para obter instruções sobre como implantar e usar o Render Hub, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Próximas etapas

Experimente a integração do Lote do Azure para o gerenciador de renderização, usando o plug-in apropriado e as instruções no GitHub, onde aplicável.
