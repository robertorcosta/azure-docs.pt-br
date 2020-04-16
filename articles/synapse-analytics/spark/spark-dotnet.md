---
title: Use .NET para Apache Spark com Azure Synapse Analytics
description: Aprenda a usar o .NET e o Apache Spark para fazer processamento em lote, streaming em tempo real, aprendizado de máquina e escrever consultas ad-hoc em notebooks Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430507"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Use .NET para Apache Spark com Azure Synapse Analytics

[.NET para Apache Spark](https://dot.net/spark) é gratuito, de código aberto e suporte multiplataforma .NET para Spark. .NET para Apache Spark fornece vinculações .NET para Spark que permitem acessar as APIs spark através de C# e F#. Com .NET para Apache Spark, você tem a capacidade de escrever e executar funções definidas pelo usuário para o Spark usando .NET. As APIs .NET para Spark permitem que você acesse todos os aspectos do Spark que ajudam a analisar seus dados, incluindo Spark SQL e Structured Streaming.

Você pode analisar dados com o .NET para Apache Spark através de definições de trabalho em lote spark ou com notebooks interativos do Azure Synapse Analytics. Neste artigo, você aprende a usar .NET para Apache Spark com Azure Synapse usando ambas as técnicas. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Enviar trabalhos em lote usando a definição de trabalho spark

Visite o tutorial para aprender como usar o Azure Synapse Analytics para [criar definições de trabalho Apache Spark para pools sinapse Spark](apache-spark-job-definitions.md). Se você não empacotou seu aplicativo para enviar ao Azure Synapse, complete as seguintes etapas.

1. Execute os seguintes comandos para publicar seu aplicativo. Certifique-se de substituir *o mySparkApp* pelo caminho para o seu aplicativo.

   **No Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **No Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET para Apache Spark em notebooks Azure Synapse Analytics

Ao criar um novo notebook, você escolhe um kernel de idioma que deseja expressar sua lógica de negócios. Há suporte ao kernel para vários idiomas, incluindo C#.

Para usar o .NET para Apache Spark no seu notebook Azure Synapse Analytics, selecione **.NET Spark (C#)** como seu kernel e conecte o notebook a um pool Spark existente.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET para Apache Spark em notebooks Azure Synapse Analytics 

Notebooks são uma ótima opção para prototipagem do seu .NET para gasodutos e cenários Apache Spark. Você pode começar a trabalhar com, entender, filtrar, exibir e visualizar seus dados de forma rápida e eficiente. Engenheiros de dados, cientistas de dados, analistas de negócios e engenheiros de aprendizado de máquina são capazes de colaborar com um documento compartilhado e interativo. Você vê resultados imediatos da exploração de dados e pode visualizar seus dados no mesmo notebook.

### <a name="how-to-use-notebooks"></a>Como usar notebooks

Quando você cria um novo notebook, você escolhe um kernel de idioma que deseja expressar sua lógica de negócios. Há suporte ao kernel para vários idiomas, incluindo C#. 

Para usar o .NET para Apache Spark no seu notebook Azure Synapse Analytics, selecione **.NET Spark (C#)** como seu kernel e conecte o notebook a um pool Spark existente. 

O notebook .NET Spark é baseado nas experiências interativas .NET e fornece experiências interativas c# `spark` com a capacidade de usar .NET para Spark fora da caixa com a variável de sessão Spark já predefinida.

### <a name="sparknet-c-kernel-features"></a>Spark.NET recursos do kernel C#

Os seguintes recursos estão disponíveis quando você usa .NET para Apache Spark no notebook Azure Synapse Analytics:

* HTML declarativo: Gere saída de suas células usando sintaxe HTML, como cabeçalhos, listas com balas e até mesmo exibindo imagens.
* Instruções C# simples (como atribuições, impressão para console, exceções de lançamento, e assim por diante).
* Blocos de código C# de várias linhas (como se as instruções, loops foreach, definições de classe e assim por diante).
* Acesso à biblioteca C# padrão (como System, LINQ, Enumerables, etc.).
* Suporte para [recursos de idioma C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* 'faísca' como uma variável pré-definida para lhe dar acesso à sua sessão Apache Spark.
* Suporte para definir [funções definidas pelo usuário .NET que podem ser executadas dentro do Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Suporte para visualizar a saída de seus trabalhos spark usando diferentes gráficos (como linha, barra ou histograma) `XPlot.Plotly` e layouts (como single, overlaid, e assim por diante) usando a biblioteca.
* Capacidade de incluir pacotes NuGet no seu notebook C#.

## <a name="next-steps"></a>Próximas etapas

* [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)