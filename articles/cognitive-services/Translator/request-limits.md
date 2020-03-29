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
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498940"
---
# <a name="request-limits-for-translator-text"></a>Limites de solicitação para a Tradução de Texto

Este artigo fornece limites de limitação para a API de Tradução de Texto. Os serviços incluem tradução, transliteração, detecção de comprimento de frase, detecção de idioma e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de caracteres e matrizpor solicitação

Cada solicitação de tradução é limitada a 5.000 caracteres, em todos os idiomas-alvo para os seus idiomas. Por exemplo, enviar uma solicitação de tradução de 1.500 caracteres para traduzir para 3 idiomas diferentes resulta em um tamanho de solicitação de 1.500x3 = 4.500 caracteres, o que satisfaz o limite de solicitação. Você é cobrado por personagem, não pelo número de solicitações. Recomenda-se enviar pedidos mais curtos.

A tabela a seguir lista os limites de matriz e de caracteres para cada operação da API de texto tradutor.

| Operação | Tamanho máximo do elemento array |   Número máximo de elementos de matriz |  Tamanho máximo de solicitação (caracteres) |
|:----|:----|:----|:----|
| Translate | 5.000 | 100   | 5.000 |
| Transliterate | 5.000 | 10    | 5.000 |
| Detect | 10.000 | 100 |   50.000 |
| BreakSentence | 10.000    | 100 | 50.000 |
| Pesquisa no dicionário| 100 |  10  | 1,000 |
| Exemplos de dicionário | 100 para texto e 100 para tradução (200 no total)| 10|   2.000 |

## <a name="character-limits-per-hour"></a>Limites de caractere por hora

Seu limite de caractere por hora baseia-se em sua camada de assinatura de Tradução de Texto. 

A cota horária deve ser consumida uniformemente ao longo da hora. Por exemplo, no limite de nível F0 de 2 milhões de caracteres por hora, os personagens não devem ser consumidos mais rápido do que cerca de 33.300 caracteres por minuto janela deslizante (2 milhões de caracteres divididos por 60 minutos).

Se você atingir ou ultrapassar esses limites, ou enviar uma parcela muito grande da cota em um curto período de tempo, você provavelmente receberá uma resposta fora da cota. Não há limites para pedidos simultâneos.

| Camada | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões de caracteres por hora |
| S1 | 40 milhões de caracteres por hora |
| S2 / C2 | 40 milhões de caracteres por hora |
| S3 / C3 | 120 milhões de caracteres por hora |
| S4 / C4 | 200 milhões de caracteres por hora |

Os limites para [assinaturas de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) são os mesmos do nível S1.

Esses limites estão restritos aos modelos de tradução padrão da Microsoft. Os modelos de tradução personalizados que usam o Custom Translator são limitados a 1.800 caracteres por segundo.

## <a name="latency"></a>Latency

A API de texto tradutor tem uma latência máxima de 15 segundos usando modelos padrão e 120 segundos ao usar modelos personalizados. Normalmente, as respostas *para texto dentro de 100 caracteres* são retornadas em 150 milissegundos a 300 milissegundos. Os modelos de tradutor personalizado têm características de latência semelhantes na taxa de solicitação sustentada e podem ter uma latência mais alta quando sua taxa de solicitação é intermitente. Os tempos de resposta variam de acordo com o tamanho da solicitação e do par de idiomas. Se você não receber uma tradução ou uma [resposta de erro](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) dentro desse prazo, verifique seu código, sua conexão de rede e tente novamente. 

## <a name="sentence-length-limits"></a>Limites de duração de sentença

Ao usar a função [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), o comprimento da sentença é limitado a 275 caracteres. Existem exceções para esses idiomas:

| Idioma | Código | Limite de caracteres |
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

## <a name="next-steps"></a>Próximas etapas

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referência de API de Tradução de Texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
