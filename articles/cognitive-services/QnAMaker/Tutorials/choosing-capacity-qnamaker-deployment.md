---
title: Capacidade de recursos para implantação – QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de criar seu serviço QnA Maker, é preciso decidir qual camada dos serviços acima são adequadas para você.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793954"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Escolher a capacidade para sua implantação do QnA Maker

O serviço QnA Maker depende de três recursos do Azure:
1.  Serviço de Aplicativo (para o tempo de execução)
2.  Pesquisa Cognitiva do Azure (para armazenar e Pesquisar QnAs)
3.  App Insights (opcional, para armazenar telemetria e logs de chat)

Antes de criar seu serviço QnA Maker, é preciso decidir qual camada dos serviços acima são adequadas para você. 

Normalmente, há três parâmetros que você precisa considerar:

1. **A taxa de transferência de que você precisa do serviço**: selecione as [Plano de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/) para o serviço de aplicativo com base em suas necessidades. Você pode [escalar verticalmente](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou reduzir verticalmente o Aplicativo. Isso também deve influenciar sua seleção de SKU Pesquisa Cognitiva do Azure, veja mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Tamanho e o número de bases de dados de conhecimento**: escolha a [SKU de pesquisa do Azure](https://azure.microsoft.com/pricing/details/search/) apropriada para seu cenário. Você pode publicar as bases de dados de conhecimento N-1 em uma camada específica, onde N representa os índices máximos permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

    Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice é usado para todas as bases de dados de conhecimento para criação e teste. 

1. **Número de documentos como fontes**: a SKU gratuita do serviço de gerenciamento do QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs a três (cada uma com 1 MB de tamanho). O padrão de SKU não tem limites para o número de documentos que você pode gerenciar. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                        | Gerenciamento do QnA Maker | Serviço de Aplicativo | Pesquisa Cognitiva do Azure | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Camada Gratuita   | Camada Gratuita    | Publicar até 2 KB/s, tamanho de 50 MB  |
| Ambiente de Desenvolvimento/Teste   | SKU Standard         | Compartilhado      | Basic        | Publicar até 14 KBs, com tamanho de 2 GB    |
| Ambiente de Produção | SKU Standard         | Basic       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

Para fazer upgrade de sua pilha do QnA Maker, veja [Fazer upgrade do seu serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Fazer upgrade do seu serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
