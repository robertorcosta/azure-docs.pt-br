---
title: Usar o .NET para Apache Spark com o Azure Synapse Analytics
description: Saiba como usar o .NET e Apache Spark para fazer processamento em lotes, streaming em tempo real, aprendizado de máquina e gravar consultas ad hoc nos notebooks do Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430507"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Usar o .NET para Apache Spark com o Azure Synapse Analytics

O [.net para Apache Spark](https://dot.net/spark) é um suporte .net gratuito, de software livre e de plataforma cruzada para o Spark. O .NET para Apache Spark fornece associações .NET para o Spark, que permitem que você acesse as APIs do Spark por meio de C# e F #. Com o .NET para Apache Spark, você tem a capacidade de escrever e executar funções definidas pelo usuário para o Spark usando o .NET. As APIs do .NET para Spark permitem que você acesse todos os aspectos do Spark que o ajudarão a analisar seus dados, incluindo o Spark SQL e o streaming estruturado.

Você pode analisar dados com o .NET para Apache Spark por meio de definições de trabalho do lote Spark ou com blocos de anotações interativos do Azure Synapse Analytics. Neste artigo, você aprenderá a usar o .NET para Apache Spark com o Azure Synapse usando as duas técnicas. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Enviar trabalhos em lotes usando a definição de trabalho do Spark

Visite o tutorial para aprender a usar o Azure Synapse Analytics para [criar Apache Spark definições de trabalho para pools do Synapse Spark](apache-spark-job-definitions.md). Se você não tiver empacotado seu aplicativo para enviar ao Azure Synapse, conclua as etapas a seguir.

1. Execute os comandos a seguir para publicar seu aplicativo. Certifique-se de substituir *mySparkApp* pelo caminho para seu aplicativo.

   **No Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **No Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET para Apache Spark nos blocos de anotações do Azure Synapse Analytics

Ao criar um novo bloco de anotações, escolha um kernel de idioma que você deseja expressar sua lógica de negócios. Há suporte de kernel para várias linguagens, incluindo C#.

Para usar o .NET para Apache Spark no seu notebook do Azure Synapse Analytics, selecione **.net Spark (C#)** como seu kernel e anexe o bloco de anotações a um pool do Spark existente.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET para Apache Spark nos blocos de anotações do Azure Synapse Analytics 

Os notebooks são uma ótima opção para o protótipo de seu .NET para Apache Spark pipelines e cenários. Você pode começar a trabalhar com, entender, filtrar, exibir e visualizar seus dados com rapidez e eficiência. Engenheiros de dados, cientistas de dados, analistas de negócios e engenheiros de aprendizado de máquina são capazes de colaborar em um documento interativo e compartilhado. Você vê resultados imediatos da exploração de dados e pode visualizar seus dados no mesmo bloco de anotações.

### <a name="how-to-use-notebooks"></a>Como usar notebooks

Ao criar um novo bloco de anotações, você escolhe um kernel de idioma que deseja expressar sua lógica de negócios. Há suporte de kernel para várias linguagens, incluindo C#. 

Para usar o .NET para Apache Spark no seu notebook do Azure Synapse Analytics, selecione **.net Spark (C#)** como seu kernel e anexe o bloco de anotações a um pool do Spark existente. 

O notebook .NET Spark se baseia nas experiências interativas do .NET e fornece experiências C# interativas com a capacidade de usar o .NET para o Spark pronto para uso `spark` com a variável de sessão do Spark já predefinida.

### <a name="sparknet-c-kernel-features"></a>Recursos de kernel do Spark.NET C#

Os recursos a seguir estão disponíveis quando você usa o .NET para Apache Spark no bloco de anotações do Azure Synapse Analytics:

* HTML declarativo: gere a saída de suas células usando a sintaxe HTML, como cabeçalhos, listas com marcadores e até mesmo exibindo imagens.
* Instruções C# simples (como atribuições, impressão no console, lançamento de exceções e assim por diante).
* Blocos de código C# de várias linhas (como instruções IF, loops foreach, definições de classe e assim por diante).
* Acesso à biblioteca C# padrão (como sistema, LINQ, Enumerfiles e assim por diante).
* Suporte para [recursos de linguagem C# 8,0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* "Spark" como uma variável predefinida para dar acesso à sua sessão de Apache Spark.
* Suporte para definir [funções definidas pelo usuário do .NET que podem ser executadas em Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Suporte para visualizar a saída de seus trabalhos do Spark usando diferentes gráficos (como linha, barra ou histograma) e layouts (como single, sobreposto e assim por diante) usando a `XPlot.Plotly` biblioteca.
* Capacidade de incluir pacotes NuGet em seu notebook C#.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do .NET para Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [Interativo do .NET](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)