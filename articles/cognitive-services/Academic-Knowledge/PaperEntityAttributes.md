---
title: Atributos de entidade de documento ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Documento na API de Conhecimento Acadêmico.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123070"
---
# <a name="paper-entity"></a>Entidade Artigo

> [!NOTE]
> Os atributos abaixo são específicos da entidade de papel. (Ty = ' 0 ')

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
AA.AfId | ID da associação do autor | Int64 | É igual a
AA.AfN | Nome da associação do autor | String | Equals, StartsWith
AA.AuId | ID do autor | Int64 | É igual a
AA.AuN | Nome do autor normalizado | String | Equals, StartsWith
Suaviza. DAuN | Nome do autor original | String | nenhum
Suaviza. DAfN | Nome da afiliação original | String | nenhum
AA.S | Posição numérica na lista de autores | Int32 | É igual a
BT | Tipo de documento BibTex ("a": artigo de diário, "b": livro, "c": capítulo de livro, "p": papel de conferência) | String | nenhum
BV | Nome do local do BibTex | String | nenhum
C.CId | ID da série de conferências | Int64 | É igual a
C.CN | Nome da série de conferências | String | Equals, StartsWith
CC | Contagem de citações | Int32 | nenhum  
CitCon | Contextos de citação</br></br>Lista de IDs de papel referenciadas e o contexto correspondente no documento (por exemplo, [{123: ["Brown Foxes é conhecido por saltar como referenciado no papel 123", "os cachorros lentos são um informativo histórico, conforme mostrado no artigo 123"]}) | Personalizado | nenhum
D | Data de publicação no formato AAAA-MM-DD | data | Equals, isBetween
DN | Título do papel original | String | nenhum
DOI | Identificador de Objeto Digital | String | Equals, StartsWith
E | Metadados estendidos</br></br>**Importante**: esse atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA) | [Tensível](#extended) | nenhum
ECC | Contagem estimada de citações | Int32 | nenhum
F. DFN | Campo original do nome do estudo | String | nenhum
F.FId | ID do campo de estudo | Int64 | É igual a
F.FN | Campo normalizado do nome do estudo | String | Equals, StartsWith
FP | Primeira página de papel na publicação | String | É igual a
I | Problema de publicação | String | É igual a
IA | Resumo invertido | [InvertedAbstract](#invertedabstract) | nenhum
ID | ID do papel | Int64 | É igual a
J.JId | ID do periódico | Int64 | É igual a
J.JN | Nome do periódico | String | Equals, StartsWith
LP | Última página de papel na publicação | String | É igual a
PB | Publicador | String | nenhum
Pt | Tipo de publicação (0: desconhecido, 1: artigo do diário, 2: patente, 3: papel da conferência, 4: capítulo do livro, 5: livro, 6: entrada de referência de livro, 7: DataSet, 8: Repository | String | É igual a
RId | Lista de IDs de papel referenciados | Int64[] | É igual a
S | Lista de URLs de origem do documento, classificada por relevância | [Fonte](#source)[] | nenhum
Ti | Título normalizado | String | Equals, StartsWith
V | Volume da publicação | String | É igual a
VFN | Nome completo do local do diário ou da conferência | String | nenhum
VSN | Nome curto do local do diário ou da conferência | String | nenhum
W | Palavras exclusivas no título | String[] | É igual a
S | Ano publicado | Int32 | Equals, isBetween

## <a name="extended"></a>Tensível
> [!IMPORTANT]
> Este atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA)

> [!IMPORTANT]
> Suporte para solicitar atributos estendidos individuais usando o "E". o escopo, ou seja, "E. DN", está sendo preterido. Embora isso ainda tenha suporte técnico, solicitando atributos estendidos individuais usando o "E". o escopo fará com que o valor do atributo seja retornado em dois locais na resposta JSON, como parte do objeto "E" e como um atributo de nível superior.

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
BT | Tipo de documento BibTex ("a": artigo de diário, "b": livro, "c": capítulo de livro, "p": papel de conferência) | String | nenhum
BV | Nome do local do BibTex | String | nenhum
CC | Contextos de citação</br></br>Lista de IDs de papel referenciadas e o contexto correspondente no documento (por exemplo, [{123: ["Brown Foxes é conhecido por saltar como referenciado no papel 123", "os cachorros lentos são um informativo histórico, conforme mostrado no artigo 123"]}) | Personalizado | nenhum
DN | Título do papel original | String | nenhum
DOI | Identificador de Objeto Digital | String | nenhum
FP | Primeira página de papel na publicação | String | nenhum
I | Problema de publicação | String | nenhum
IA | Resumo invertido | [InvertedAbstract](#invertedabstract) | nenhum
LP | Última página de papel na publicação | String | nenhum
PB | Publicador | String | nenhum
S | Lista de URLs de origem do documento, classificada por relevância | [Fonte](#source)[] | nenhum
V | Volume da publicação | String | nenhum
VFN | Nome completo do local do diário ou da conferência | String | nenhum
VSN | Nome curto do local do diário ou da conferência | String | nenhum

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> Os atributos InvertedAbstract não podem ser solicitados diretamente como um atributo de retorno. Se você precisar de um atributo individual, deverá solicitar o atributo "IA" de nível superior, ou seja, para obter IA. Atributos de solicitação IndexLength = IA

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
IndexLength | Número de itens no índice (contagem de palavras do resumo) | Int32 | nenhum
InvertedIndex | Lista de palavras abstratas e sua posição correspondente no resumo original (por exemplo, [{"The": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}]) | Personalizado | nenhum

## <a name="source"></a>Fonte

> [!IMPORTANT]
> Os atributos de origem não podem ser solicitados diretamente como um atributo de retorno. Se você precisar de um atributo individual, deverá solicitar o atributo "S" de nível superior, ou seja, obter os atributos de solicitação S. U = S

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
Ty | Tipo de URL de origem (1: HTML, 2: texto, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | String | nenhum
U | URL de origem | String | nenhum
