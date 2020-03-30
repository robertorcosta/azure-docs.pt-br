---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272923"
---
| Tipo de dados | Características permitidas em expressões lambda com`any` | Características permitidas em expressões lambda com`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tudo, `search.ismatch` exceto e`search.ismatchscoring` | Idêntico |
| `Collection(Edm.String)` | Comparações `eq` com ou`search.in` <br/><br/> Combinando subexpressões com`or` | Comparações `ne` com ou`not search.in()` <br/><br/> Combinando subexpressões com`and` |
| `Collection(Edm.Boolean)` | Comparações `eq` com ou`ne` | Idêntico |
| `Collection(Edm.GeographyPoint)` | Usando `geo.distance` `lt` com ou`le` <br/><br/> `geo.intersects` <br/><br/> Combinando subexpressões com`or` | Usando `geo.distance` `gt` com ou`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combinando subexpressões com`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Comparações `eq` `ne`usando `lt` `gt`, `le`, , , , ou`ge` <br/><br/> Combinando comparações com outras subexpressões usando`or` <br/><br/> Combinando comparações exceto `ne` com outras subexpressões usando`and` <br/><br/> Expressões usando combinações de `and` e `or` em Forma Normal [Disjuntiva (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Comparações `eq` `ne`usando `lt` `gt`, `le`, , , , ou`ge` <br/><br/> Combinando comparações com outras subexpressões usando`and` <br/><br/> Combinando comparações exceto `eq` com outras subexpressões usando`or` <br/><br/> Expressões usando combinações de `and` e `or` em Forma Normal [Conjuntiva (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
