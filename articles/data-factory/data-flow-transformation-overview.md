---
title: Visão geral da transformação fluxo de dados de mapeamento
description: Uma visão geral das diferentes transformações disponíveis no fluxo de dados de mapeamento
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086930"
---
# <a name="mapping-data-flow-transformation-overview"></a>Visão geral da transformação fluxo de dados de mapeamento

Veja abaixo uma lista das transformações com suporte no fluxo de dados de mapeamento. Clique em cada transformação para aprender seus detalhes de configuração.

| Nome | Categoria | Descrição |
| ---- | -------- | ----------- |
| [Aggregate](data-flow-aggregate.md) | Modificador de esquema | Defina diferentes tipos de agregações como SUM, MIN, MAX e COUNT agrupados por colunas computadas ou existentes. | 
| [Alterar linha](data-flow-alter-row.md) | Modificador de linha | Defina as políticas de inserção, exclusão, atualização e Upsert em linhas. |
| [Divisão condicional](data-flow-conditional-split.md) | Várias entradas/saídas | Encaminhe linhas de dados para fluxos diferentes com base em condições de correspondência. |
| [Coluna derivada](data-flow-derived-column.md) | Modificador de esquema | gere novas colunas ou modifique os campos existentes usando a linguagem de expressão de fluxo de dados. | 
| [Exists](data-flow-exists.md) | Várias entradas/saídas | Verifique se os dados existem em outra fonte ou fluxo. | 
| [Filter](data-flow-filter.md) | Modificador de linha | Filtrar uma linha com base em uma condição. |
| [Dimensiona](data-flow-flatten.md) | Modificador de esquema |  Pegue valores de matriz dentro de estruturas hierárquicas, como JSON, e os registre em linhas individuais. |
| [Join](data-flow-join.md) | Várias entradas/saídas |  Combine dados de duas origens ou fluxos. |
| [Pesquisar](data-flow-lookup.md) | Várias entradas/saídas | Dados de referência de outra fonte. |
| [Nova ramificação](data-flow-new-branch.md) | Várias entradas/saídas | Aplique vários conjuntos de operações e transformações no mesmo fluxo de dados. |
| [Dinâmico](data-flow-pivot.md) | Modificador de esquema | Uma agregação em que uma ou mais colunas de Agrupamento têm seus valores de linha distintos transformados em colunas individuais. |
| [Selecionar](data-flow-select.md) | Modificador de esquema | Colunas de alias e nomes de fluxo, e soltar ou reordenar colunas |
| [Coletor](data-flow-sink.md) | - | Um destino final para seus dados |
| [Sort](data-flow-sort.md) | Modificador de linha | Classificar linhas de entrada no fluxo de dados atual |
| [Origem](data-flow-source.md) | - | Uma fonte de dados para o fluxo de dados |
| [Chave substituta](data-flow-surrogate-key.md) | Modificador de esquema | Adicionar um valor de chave arbitrária de não comercial em incremento |
| [Union](data-flow-union.md) | Várias entradas/saídas | Combinar vários fluxos de dados verticalmente |
| [Não Dinâmico](data-flow-unpivot.md) | Modificador de esquema | Dinamizar colunas em valores de linha |
| [Window](data-flow-window.md) | Modificador de esquema |  Defina as agregações baseadas em janela de colunas em seus fluxos de dados. |
