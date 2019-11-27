---
title: Solicitar limites – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo lista os limites de solicitação para a API de Tradução de Texto. Cobranças são incorridas com base na contagem de caracteres, não a frequência de solicitação com um limite de 5.000 caracteres por solicitação. Limites de caractere são assinatura com base com F0 limitado a 2 milhões de caracteres por hora.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: swmachan
ms.openlocfilehash: 3694c8cb34b2a050c9e18265c8cc0a0198456076
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533715"
---
# <a name="request-limits-for-translator-text"></a>Limites de solicitação para a Tradução de Texto

Este artigo fornece limites de limitação para a API de Tradução de Texto. Os serviços incluem tradução, transliteração, detecção de comprimento de frase, detecção de idioma e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de caracteres e de matriz por solicitação

Cada solicitação de conversão é limitada a 5.000 caracteres. Você é cobrado por personagem, não pelo número de solicitações. É recomendável enviar solicitações mais curtas.

A tabela a seguir lista os limites de elemento e de caracteres de matriz para cada operação do API de Tradução de Texto.

| Operação | Tamanho máximo do elemento de matriz |   Número máximo de elementos da matriz |  Tamanho máximo da solicitação (caracteres) |
|:----|:----|:----|:----|
| Translate | 5\.000 | 100   | 5\.000 |
| Transliterate | 5\.000 | 10    | 5\.000 |
| Detect | 10.000 | 100 |   50.000 |
| BreakSentence | 10.000    | 100 | 50.000 |
| Pesquisa no dicionário| 100 |  10  | 1\.000 |
| Exemplos de dicionário | 100 para texto e 100 para conversão (total de 200)| 10|   2\.000 |

## <a name="character-limits-per-hour"></a>Limites de caractere por hora

Seu limite de caractere por hora baseia-se em sua camada de assinatura de Tradução de Texto. 

A cota por hora deve ser consumida uniformemente durante a hora. Por exemplo, no limite da camada F0 de 2 milhões caracteres por hora, os caracteres devem ser consumidos não mais rápido do que aproximadamente 33.300 caracteres por minuto janela deslizante (2 milhões caracteres divididos por 60 minutos).

Se você atingir ou ultrapassar esses limites, ou enviar um grande número de uma parte da cota em um curto período de tempo, provavelmente receberá uma resposta de cota insuficiente. Não há limites em solicitações simultâneas.

| Camada | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões de caracteres por hora |
| S1 | 40 milhões de caracteres por hora |
| S2/C2 | 40 milhões de caracteres por hora |
| S3/C3 | 120 milhões de caracteres por hora |
| S4/C4 | 200 milhões de caracteres por hora |

Os limites para [assinaturas de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) são os mesmos da camada S1.

Esses limites são restritos aos modelos de tradução padrão da Microsoft. Os modelos de tradução personalizados que usam o tradutor personalizado são limitados a 1.800 caracteres por segundo.

## <a name="latency"></a>Latência

A API de Tradução de Texto tem uma latência máxima de 15 segundos usando modelos padrão e 120 segundos ao usar modelos personalizados. Normalmente, as respostas *de texto dentro de 100 caracteres* são retornadas em 150 milissegundos a 300 milissegundos. Os modelos de Tradutor personalizados têm características de latência semelhantes na taxa de solicitação sustentada e podem ter uma latência mais alta quando a taxa de solicitação é intermitente. Os tempos de resposta variam de acordo com o tamanho da solicitação e do par de idiomas. Se você não receber uma conversão ou uma [resposta de erro](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) dentro desse período, verifique seu código, sua conexão de rede e tente novamente. 

## <a name="sentence-length-limits"></a>Limites de duração de sentença

Ao usar a função [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), o comprimento da sentença é limitado a 275 caracteres. Existem exceções para esses idiomas:

| Idioma | Codificar | Limite de caracteres |
|----------|------|-----------------|
| Chinês | zh | 132 |
| Alemão | de | 290 |
| Italiano | it | 280 |
| Japonês | ja | 150 |
| Português | pt | 290 |
| Espanhol | es | 280 |
| Italiano | it | 280 |
| Tailandês | th | 258 |

> [!NOTE]
> Esse limite não se aplica a traduções.

## <a name="next-steps"></a>Próximos passos

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referência de API de Tradução de Texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
