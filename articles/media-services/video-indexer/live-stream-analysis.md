---
title: Análise de transmissão ao vivo usando indexador de vídeo
titleSuffix: Azure Media Services
description: Este artigo mostra como realizar uma análise de transmissão ao vivo usando o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185991"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Análise de transmissão ao vivo com indexador de vídeo

O Azure Media Services Video Indexer é um serviço azure projetado para extrair insights profundos de arquivos de vídeo e áudio offline. Isso é para analisar um determinado arquivo de mídia já criado com antecedência. No entanto, para alguns casos de uso é importante obter os insights de mídia de uma transmissão ao vivo o mais rápido possível para desbloquear casos operacionais e outros de uso pressionados a tempo. Por exemplo, tais metadados ricos em uma transmissão ao vivo poderiam ser usados por produtores de conteúdo para automatizar a produção de TV.

Uma solução descrita neste artigo permite que os clientes usem o Video Indexer em resoluções quase em tempo real em feeds ao vivo. O atraso na indexação pode ser tão baixo quanto quatro minutos usando esta solução, dependendo dos pedaços de dados que estão sendo indexados, da resolução de entrada, do tipo de conteúdo e do cálculo alimentado para este processo.

![Os metadados do Indexador de vídeo na transmissão ao vivo](./media/live-stream-analysis/live-stream-analysis01.png)

*Figura 1 – Leitor de amostra exibindo os metadados do Indexador de vídeo na transmissão ao vivo*

A [solução de análise](https://aka.ms/livestreamanalysis) de fluxo em mãos, usa funções do Azure e dois Logic Apps para processar um programa ao vivo a partir de um canal ao vivo no Azure Media Services com o Video Indexer e exibe o resultado com o Azure Media Player mostrando o fluxo quase em tempo real.

Em alto nível, é composta por dois passos principais. O primeiro passo é executado a cada 60 segundos, e pega um subclipe dos últimos 60 segundos jogados, cria um ativo a partir dele e indexa-o via Video Indexer. Em seguida, o segundo passo é chamado uma vez que a indexação está completa. Os insights capturados são processados, enviados ao Azure Cosmos DB, e o subclipe indexado é excluído.

O sample player reproduz a transmissão ao vivo e recebe os insights do Azure Cosmos DB, usando uma função dedicada do Azure. Ele exibe os metadados e miniaturas em sincronia com o vídeo ao vivo.

![Os dois aplicativos lógicos processando a transmissão ao vivo a cada minuto na nuvem](./media/live-stream-analysis/live-stream-analysis02.png)

*Figura 2 – Os dois aplicativos lógicos que processam a transmissão ao vivo a cada minuto na nuvem.*

## <a name="step-by-step-guide"></a>Guia passo-a-passo 

O código completo e um guia passo-a-passo para implantar os resultados podem ser encontrados no [projeto GitHub para análise de mídia ao vivo com o Video Indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)
