---
title: Visão geral da transformação fluxo de dados de mapeamento
description: Uma visão geral das diferentes transformações disponíveis no fluxo de dados de mapeamento
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 0442c701f39fd3e84361ad0201aaf8d1840d8851
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606279"
---
# <a name="mapping-data-flow-transformation-overview"></a>Visão geral da transformação fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Veja abaixo uma lista das transformações com suporte no fluxo de dados de mapeamento. Clique em cada transformação para aprender seus detalhes de configuração.

| Name | Categoria | Descrição |
| ---- | -------- | ----------- |
| [Tais](data-flow-aggregate.md) | Modificador de esquema | Defina diferentes tipos de agregações como SUM, MIN, MAX e COUNT agrupados por colunas computadas ou existentes. | 
| [Alterar linha](data-flow-alter-row.md) | Modificador de linha | Defina as políticas de inserção, exclusão, atualização e Upsert em linhas. |
| [Divisão condicional](data-flow-conditional-split.md) | Várias entradas/saídas | Encaminhe linhas de dados para fluxos diferentes com base em condições de correspondência. |
| [Coluna derivada](data-flow-derived-column.md) | Modificador de esquema | gere novas colunas ou modifique os campos existentes usando a linguagem de expressão de fluxo de dados. | 
| [Existe](data-flow-exists.md) | Várias entradas/saídas | Verifique se os dados existem em outra fonte ou fluxo. | 
| [Filter](data-flow-filter.md) | Modificador de linha | Filtrar uma linha com base em uma condição. |
| [Mesclar](data-flow-flatten.md) | Modificador de esquema |  Pegue valores de matriz dentro de estruturas hierárquicas, como JSON, e os registre em linhas individuais. |
| [Ingressar](data-flow-join.md) | Várias entradas/saídas |  Combine dados de duas origens ou fluxos. |
| [Pesquisa](data-flow-lookup.md) | Várias entradas/saídas | Dados de referência de outra fonte. |
| [Novo branch](data-flow-new-branch.md) | Várias entradas/saídas | Aplique vários conjuntos de operações e transformações no mesmo fluxo de dados. |
| [Dinâmico](data-flow-pivot.md) | Modificador de esquema | Uma agregação em que uma ou mais colunas de Agrupamento têm seus valores de linha distintos transformados em colunas individuais. |
| [Selecionar](data-flow-select.md) | Modificador de esquema | Colunas de alias e nomes de fluxo, e soltar ou reordenar colunas |
| [Coletor](data-flow-sink.md) | - | Um destino final para seus dados |
| [Classificar](data-flow-sort.md) | Modificador de linha | Classificar linhas de entrada no fluxo de dados atual |
| [Fonte](data-flow-source.md) | - | Uma fonte de dados para o fluxo de dados |
| [Chave substituta](data-flow-surrogate-key.md) | Modificador de esquema | Adicionar um valor de chave arbitrária de não comercial em incremento |
| [Unida](data-flow-union.md) | Várias entradas/saídas | Combinar vários fluxos de dados verticalmente |
| [Não Dinâmico](data-flow-unpivot.md) | Modificador de esquema | Dinamizar colunas em valores de linha |
| [Janela](data-flow-window.md) | Modificador de esquema |  Defina as agregações baseadas em janela de colunas em seus fluxos de dados. |
