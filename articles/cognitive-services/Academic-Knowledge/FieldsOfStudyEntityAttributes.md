---
title: Atributos da entidade Campo de Estudo ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Campo de Estudo na API de Conhecimento Acadêmico.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146480"
---
# <a name="field-of-study-entity"></a>Entidade Campo de Estudo

> [!NOTE]
> Os atributos a seguir são específicos para o campo da entidade de estudo. (Ty = ' 6 ')

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
CC      |Contagem total de citações do campo de estudo    |Int32      |nenhum  
DFN     |Nome de exibição do campo de estudo            |String     |nenhum
ECC     |Campo da contagem total estimada de citações|Int32      |nenhum
FL      |Nível nos campos de hierarquia de estudo     |Int32      |Equals, isBetween
FN      |Nome normalizado do campo de estudo         |String     |É igual a
FC.FId  |ID do campo de estudo filho                |Int64      |É igual a
FC.FN   |Nome do campo de estudo filho              |String     |É igual a
FP.FId  |ID do campo de estudo pai               |Int64      |É igual a
FP.FN   |Nome do campo de estudo pai             |String     |É igual a
ID      |ID da Entidade                              |Int64      |É igual a
Computador    | Campo da contagem de publicação total de estudos | Int32 | nenhum
