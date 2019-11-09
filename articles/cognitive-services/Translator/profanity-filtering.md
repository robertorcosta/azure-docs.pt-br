---
title: Filtragem de conteúdo ofensivo - API de tradução de texto
titleSuffix: Azure Cognitive Services
description: Use a filtragem de profanação para determinar o nível de profanação traduzido em seu texto no API de Tradução de Texto de serviços cognitivas do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836222"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adicionar conteúdo ofensivo filtragem com a API de tradução de texto

Normalmente o serviço de tradução retém a linguagem obscena presente no texto de origem da tradução. O grau de obscenidade e o contexto que torna as palavras impróprias diferem entre culturas. Como resultado, o grau de profanação na língua-alvo pode ser amplificado ou reduzido.

Se você quiser evitar ver palavrões na tradução, mesmo que a profanidade esteja presente no texto de origem, use a opção de filtragem de profanação disponível no método Translate (). Essa opção permite escolher se você deseja ver palavrões excluídos, marcados com as tags apropriadas ou não executar nenhuma ação.

O método Translate () usa o parâmetro “options”, que contém o novo elemento “ProfanityAction”. Os valores aceitos de ProfanityAction são "NoAction", "Marcada" e "Excluído".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: Origem - Japonês | Exemplo: Destino - Inglês|
| :---|:---|:---|:---|
| NoAction | Padrão. O mesmo que não configurar a opção. A linguagem obscena passa do texto de origem para o texto de destino. | 彼は変態です。 | Ele é um idiota. |
| Marked | Palavras impróprias cercadas por marcas XML \<profanação >... \</profanity >. | 彼は変態です。 | Ele é um \<profanity>jerk\</profanity >. |
| Deleted | Palavras impróprias são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Aplicar filtragem de linguagem obscena com a chamada à API de Tradução](reference/v3-0-translate.md)
