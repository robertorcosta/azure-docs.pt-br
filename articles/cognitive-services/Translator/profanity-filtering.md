---
title: Filtragem de profanação-Tradutor
titleSuffix: Azure Cognitive Services
description: Use a filtragem de profanação para determinar o nível de profanação traduzido em seu texto no Tradutor de serviços cognitivas do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 864f6a6d92306c40713f66b526c8a8df1683d3c4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586789"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Adicionar filtragem de profanação com o tradutor

Normalmente o serviço de tradução retém a linguagem obscena presente no texto de origem da tradução. O grau de obscenidade e o contexto que torna as palavras impróprias diferem entre culturas. Como resultado, o grau de profanação na língua-alvo pode ser amplificado ou reduzido.

Se você quiser evitar ver palavrões na tradução, mesmo que a profanidade esteja presente no texto de origem, use a opção de filtragem de profanação disponível no método Translate (). Essa opção permite escolher se você deseja ver palavrões excluídos, marcados com as tags apropriadas ou não executar nenhuma ação.

O método Translate () usa o parâmetro “options”, que contém o novo elemento “ProfanityAction”. Os valores aceitos de ProfanityAction são "NoAction", "Marcada" e "Excluído".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: Origem - Japonês | Exemplo: Destino - Inglês|
| :---|:---|:---|:---|
| NoAction | Padrão. O mesmo que não configurar a opção. A linguagem obscena passa do texto de origem para o texto de destino. | 彼は変態です。 | Ele é um idiota. |
| Marked | Palavras impróprias cercadas por marcas XML \<profanação >... \</profanity >. | 彼は変態です。 | Ele é um \<profanity>jerk\</profanity >. |
| Excluído | Palavras impróprias são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Aplicar a filtragem de profanação à sua chamada de Tradutor](reference/v3-0-translate.md)
