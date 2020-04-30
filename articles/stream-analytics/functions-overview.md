---
title: Funções definidas pelo usuário no Azure Stream Analytics
description: Este artigo é uma visão geral das funções definidas pelo usuário no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 45e766c624ee96f7faa06fb07d00349e620a4c0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133479"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo usuário no Azure Stream Analytics

A linguagem de consulta do tipo SQL no Azure Stream Analytics facilita a implementação da lógica de análise em tempo real em dados de streaming. Stream Analytics fornece flexibilidade adicional por meio de funções personalizadas que são invocadas em sua consulta. O exemplo de código a seguir é um `sampleFunction` UDF chamado que aceita um parâmetro, cada registro de entrada que o trabalho recebe e o resultado é gravado na `sampleResult`saída como.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Tipos de funções

O Azure Stream Analytics dá suporte aos quatro tipos de função a seguir: 

* Funções definidas pelo usuário de JavaScript 
* Agregações definidas pelo usuário do JavaScript 
* Funções definidas pelo usuário do C# (usando o Visual Studio) 
* Azure Machine Learning 

Você pode usar essas funções para cenários como pontuação em tempo real usando modelos de aprendizado de máquina, manipulações de cadeia de caracteres, cálculos matemáticos complexos, codificação e decodificação de dados. 

## <a name="limitations"></a>Limitações

As funções definidas pelo usuário são sem estado e o valor de retorno só pode ser um valor escalar. Você não pode chamar pontos de extremidade REST externos dessas funções definidas pelo usuário, pois isso provavelmente afetará o desempenho do seu trabalho. 

Azure Stream Analytics não mantém um registro de todas as invocações de funções e retornou resultados. Para garantir a capacidade de repetição – por exemplo, executar novamente seu trabalho de carimbo de data/hora antigo produz os mesmos resultados novamente – não use `Date.GetData()` funções `Math.random()`como ou, pois essas funções não retornam o mesmo resultado para cada invocação.  

## <a name="resource-logs"></a>Logs de recursos

Os erros de tempo de execução são considerados fatais e são exibidos por meio de logs de atividade e de recursos. É recomendável que sua função manipule todas as exceções e erros e retorne um resultado válido para sua consulta. Isso impedirá que seu trabalho vá para um [estado de falha](job-states.md).  


## <a name="next-steps"></a>Próximas etapas

* [Funções definidas pelo usuário do JavaScript no Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics agregações definidas pelo usuário do JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Desenvolver .NET Standard funções definidas pelo usuário para trabalhos de Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integrar Azure Stream Analytics com Azure Machine Learning](machine-learning-udf.md)

