---
title: Funções definidas pelo usuário no Azure Stream Analytics
description: Este artigo é uma visão geral das funções definidas pelo usuário no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115580"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo usuário no Azure Stream Analytics

A linguagem de consulta semelhante ao SQL no Azure Stream Analytics facilita a implementação da lógica de análise em tempo real sobre dados de streaming. O Stream Analytics oferece flexibilidade adicional através de funções personalizadas que são invocadas em sua consulta. O exemplo de código a `sampleFunction` seguir é um UDF chamado que aceita um parâmetro, cada `sampleResult`registro de entrada que o trabalho recebe, e o resultado é escrito na saída como .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Tipos de funções

O Azure Stream Analytics suporta os quatro tipos de função a seguir: 

* Funções definidas pelo usuário de JavaScript 
* Agregações definidas pelo usuário do JavaScript 
* C# funções definidas pelo usuário (usando o Visual Studio) 
* Azure Machine Learning 

Você pode usar essas funções para cenários como pontuação em tempo real usando modelos de aprendizado de máquina, manipulações de cordas, cálculos matemáticos complexos, codificação e decodificação de dados. 

## <a name="limitations"></a>Limitações

As funções definidas pelo usuário são apátridas, e o valor de retorno só pode ser um valor escalar. Não é possível chamar os pontos finais externos do REST a partir dessas funções definidas pelo usuário, pois isso provavelmente afetará o desempenho do seu trabalho. 

O Azure Stream Analytics não mantém um registro de todas as invocações de funções e resultados retornados. Para garantir a repetibilidade - por exemplo, refazer o seu trabalho a partir de carimbo `Date.GetData()` `Math.random()`de data-hora mais antigo produz os mesmos resultados novamente - não use funções como ou , como essas funções não retornam o mesmo resultado para cada invocação.  

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Quaisquer erros de tempo de execução são considerados fatais e são fornecidos através de registros de atividade e diagnóstico. Recomenda-se que sua função acuse todas as exceções e erros e retorne um resultado válido à sua consulta. Isso impedirá que seu trabalho vá para um [estado falido.](job-states.md)  


## <a name="next-steps"></a>Próximas etapas

* [Funções definidas pelo usuário do JavaScript no Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Agregados definidos pelo usuário JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Desenvolver funções padrão do usuário .NET para trabalhos do Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integre o Azure Stream Analytics com o Azure Machine Learning](machine-learning-udf.md)

