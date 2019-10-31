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
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 1d16668e2c0f52c0824016c977251e64c800c54d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161734"
---
# <a name="paper-entity"></a>Entidade Artigo

<sub>* Os atributos abaixo são específicos para a entidade de papel. (Ty = ' 0 ')</sub>

name | Descrição | Type | Operations
--- | --- | --- | ---
AA.AfId | ID da associação do autor | Int64 | Igual a
AA.AfN | Nome da associação do autor | string | Equals, StartsWith
AA.AuId | ID do autor | Int64 | Igual a
AA.AuN | Nome do autor normalizado | string | Equals, StartsWith
Suaviza. DAuN | Nome do autor original | string | Nenhum
Suaviza. DAfN | Nome da afiliação original | string | Nenhum
AA.S | Posição numérica na lista de autores | Int32 | Igual a
CC | Contagem de citações | Int32 | Nenhum  
C.CId | ID da série de conferências | Int64 | Igual a
C.CN | Nome da série de conferências | string | Equals, StartsWith
D | Data de publicação no formato AAAA-MM-DD | Data | Equals, isBetween
E | Metadados estendidos (veja a tabela abaixo) | string | N/D  
ECC | Contagem estimada de citações | Int32 | Nenhum
F. DFN | Campo original do nome do estudo | string | Nenhum
F.FId | ID do campo de estudo | Int64 | Igual a
F.FN | Campo normalizado do nome do estudo | string | Equals, StartsWith
ID | ID do papel | Int64 | Igual a
J.JId | ID do periódico | Int64 | Igual a
J.JN | Nome do periódico | string | Equals, StartsWith
Pt | Tipo de publicação (0: desconhecido, 1: artigo do diário, 2: patente, 3: papel da conferência, 4: capítulo do livro, 5: livro, 6: entrada de referência de livro, 7: DataSet, 8: Repository | string | Igual a
RId | Lista de IDs de papel referenciados | Int64[] | Igual a
Ti | Título normalizado | string | Equals, StartsWith
W | Palavras exclusivas no título | String[] | Igual a
S | Ano publicado | Int32 | Equals, isBetween

## <a name="extended-metadata-attributes"></a>Atributos de metadados estendidos ##

name | Descrição               
--- | ---
BT | Tipo de documento BibTex ("a": artigo de diário, "b": livro, "c": capítulo de livro, "p": papel de conferência)
BV | Nome do local do BibTex
CC | Contextos de citação – lista de IDs de papel referenciadas e o contexto correspondente no documento (por exemplo, [{123: ["Brown Foxes é conhecido por saltar como referenciado no papel 123", "as cachorros lentas são um informativo histórico, conforme mostrado no artigo 123"]})
DN | Título do papel original
DOI | Identificador de Objeto Digital
FP | Primeira página de papel na publicação
I | Problema de publicação
IA | Resumo invertido
IA.IndexLength | Número de itens no índice (contagem de palavras do resumo)
IA.InvertedIndex | Lista de palavras abstratas e sua posição correspondente no resumo original (por exemplo, [{"The": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}])
LP | Última página de papel na publicação
PB | Publicador
P | Fontes – lista das fontes da Web do artigo, ordenadas por classificação estática
S.Ty | Tipo de fonte (1:HTML, 2:Texto, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U | URL de origem
V | Volume da publicação
VFN | Nome completo do local do diário ou da conferência
VSN | Nome curto do local do diário ou da conferência
