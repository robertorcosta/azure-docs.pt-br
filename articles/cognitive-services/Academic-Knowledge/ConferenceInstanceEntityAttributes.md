---
title: Atributos de entidade de Instância de conferência ‒ API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os atributos que podem ser utilizados com a entidade Instância de Conferência na API de Conhecimento Acadêmico.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146570"
---
# <a name="conference-instance-entity"></a>Entidade Instância de Conferência

> [!NOTE]
> Os atributos a seguir são específicos para a entidade de instância de conferência. (Ty = ' 4 ')

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
CC      |Contagem total de citações da instância de conferência           |Int32      |nenhum  
CD.D    |Data de um evento de instância de conferência    |data       |Equals, isBetween
CD.T    |Título de um evento de instância de conferência   |data       |Equals, isBetween
CIARD   |Resumo da data de conclusão do registro da instância de conferência  |data       |Equals, isBetween
CIED    |Data de término da instância de conferência    |data       |Equals, isBetween
CIFVD   |Data de conclusão da versão final da instância de conferência  |data       |Equals, isBetween
CIL     |Local da instância de conferência    |String     |Equals, StartsWith
CIN     |Nome normalizado da instância de conferência |String        |É igual a
CINDD   |Data de notificação da instância de conferência   |data       |Equals, isBetween
CISD    |Data de início da instância de conferência  |data       |Equals, isBetween
CISDD   |Data de conclusão do envio da instância de conferência     |data       |Equals, isBetween
DCN     |Nome de exibição da instância de conferência  |String      |nenhum
E | Metadados estendidos</br></br>**Importante**: esse atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA) | [Tensível](#extended) | nenhum
ECC     |Contagem total estimada de citações da instância de conferência |Int32      |nenhum
FN | Nome completo da instância de conferência | String | nenhum
ID      |ID da Entidade                              |Int64      |É igual a
Computador | Contagem de publicação total da instância de conferência | Int32 | nenhum
PCS.CN  |Nome da série de conferência pai da instância |String  |É igual a
PCS.CId |ID da série de conferências pai da instância |Int64     |É igual a

## <a name="extended"></a>Tensível

> [!IMPORTANT]
> Este atributo foi preterido e só tem suporte para aplicativos herdados. Solicitar esse atributo individualmente (ou seja, Attributes = ID, ti, E) fará com que todos os atributos de metadados estendidos sejam retornados em uma *cadeia de caracteres JSON serializada*</br></br>Todos os atributos contidos nos metadados estendidos agora estão disponíveis como um atributo de nível superior e podem ser solicitados como tal (ou seja, Attributes = ID, ti, DOI, IA)

> [!IMPORTANT]
> Suporte para solicitar atributos estendidos individuais usando o "E". o escopo, ou seja, "E. DN", está sendo preterido. Embora isso ainda tenha suporte técnico, solicitando atributos estendidos individuais usando o "E". o escopo fará com que o valor do atributo seja retornado em dois locais na resposta JSON, como parte do objeto "E" e como um atributo de nível superior.

NOME | DESCRIÇÃO | Digite | Operações
--- | --- | --- | ---
FN | Nome completo da instância de conferência | String | nenhum
Computador | Contagem de publicação total da instância de conferência | Int32 | nenhum
