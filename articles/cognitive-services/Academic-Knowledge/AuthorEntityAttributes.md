---
title: Atributos de entidade de autor ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Autor na API de Conhecimento Acadêmico.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146500"
---
# <a name="author-entity"></a>Entidade Autor

> [!NOTE]
> Os atributos a seguir são específicos para a entidade de autor. (Ty = ' 1 ')

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
ID      | ID da Entidade                             |Int64      |É igual a
AuN     | Nome normalizado do autor                    |String     |É igual a
CC      | Contagem total de citações do autor           |Int32      |nenhum  
DAuN    | Nome de exibição do autor                   |String     |nenhum
E | Metadados estendidos</br></br>**Importante**: esse atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA) | [Tensível](#extended) | nenhum
ECC     | Contagem total estimada de citações do autor |Int32      |nenhum
LKA.AfId | ID da entidade da última afiliação conhecida encontrada para o autor | Int64 | nenhum
LKA. AfN | Nome normalizado da última afiliação conhecida encontrada para o autor | String | nenhum
Computador | Contagem total de publicações do autor | Int32 | nenhum

## <a name="extended"></a>Tensível

> [!IMPORTANT]
> Este atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA)

> [!IMPORTANT]
> Suporte para solicitar atributos estendidos individuais usando o "E". o escopo, ou seja, "E. DN", está sendo preterido. Embora isso ainda tenha suporte técnico, solicitando atributos estendidos individuais usando o "E". o escopo fará com que o valor do atributo seja retornado em dois locais na resposta JSON, como parte do objeto "E" e como um atributo de nível superior.

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
LKA.AfId | ID da entidade da última afiliação conhecida encontrada para o autor | Int64 | nenhum
LKA. AfN | Nome normalizado da última afiliação conhecida encontrada para o autor | String | nenhum
Computador | Contagem total de publicações do autor | Int32 | nenhum
