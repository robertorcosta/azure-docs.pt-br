---
author: baanders
description: incluir arquivo para limites de gêmeos digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 64b3bf87df7d0b10f8a69e8303010f64b3e68f79
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507556"
---
### <a name="functional-limits"></a>Limites funcionais

A tabela a seguir lista os limites funcionais do Azure digital gêmeos na visualização atual.

| Área | Funcionalidade | Limite padrão | Ajustá? |
| --- | --- | --- | --- |
| Recurso do Azure | Número de instâncias de gêmeos digitais do Azure em uma região, por assinatura | 10 | Sim |
| Gêmeos Digitais | Número de gêmeos em uma instância de gêmeos digital do Azure | 200.000 | Sim |
| Gêmeos Digitais | Número de relações de entrada para um único "n" | 5\.000 | Não |
| Gêmeos Digitais | Número de relações de saída de um único n º | 5\.000 | Não |
| Roteamento | Número de pontos de extremidade para uma única instância de gêmeos digital do Azure | 6 | Não |
| Roteamento | Número de rotas para uma única instância de gêmeos digital do Azure | 6 | Sim |
| Modelos | Número de modelos em uma única instância de gêmeos digital do Azure | 10.000 | Sim |
| Modelos | Número de modelos que podem ser carregados em uma única chamada à API | 250 | Não |
| Modelos | Número de itens retornados em uma única página | 100 | Não |
| Consulta | Número de itens retornados em uma única página | 100 | Não |
| Consulta | Número de `AND`  /  `OR` expressões em uma consulta | 50 | Sim |
| Consulta | Número de itens de matriz em uma `IN`  /  `NOT IN` cláusula | 50 | Sim |
| Consulta | Número de caracteres em uma consulta | 8,000 | Sim |
| Consulta | Número de `JOINS` em uma consulta | 1 | Sim |

### <a name="rate-limits"></a>Limites de taxa

Esta tabela reflete os limites de taxa de APIs diferentes.

| API | Funcionalidade | Limite padrão | Ajustá? |
| --- | --- | --- | --- |
| API de modelos | Número de solicitações por segundo | 100 | Sim |
| API gêmeos digital | Número de solicitações por segundo | 1,000 | Sim |
| API de Consulta | Número de solicitações por segundo | 500 | Sim |
| API de Consulta | Unidades de consulta por segundo | 4.000 | Sim |
| API de rotas de eventos | Número de solicitações por segundo | 100 | Sim |

### <a name="other-limits"></a>Outros limites

Os limites em tipos de dados e campos em documentos de DTDL para modelos de gêmeos digitais do Azure podem ser encontrados em sua documentação de especificações no GitHub: [*DTDL (digital gêmeos Definition Language)-versão 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Detalhes de latência de consulta e outras diretrizes sobre como escrever consultas durante a visualização podem ser encontrados em [*instruções: consultar o grafo de entrelaçamento*](../articles/digital-twins/how-to-query-graph.md).