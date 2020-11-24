---
title: Roteiro – Azure Synapse Analytics (versão prévia de workspaces)
description: Guia de referência que orienta o usuário pelo Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a592b1b160edef1ed1f41478187d989d087e9617
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844975"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Folha de referências do Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

A folha de referências do Azure Synapse Analytics explicará os conceitos básicos do serviço e os comandos importantes. Este artigo é útil para os novos aprendizes e para aqueles que desejam conhecer os destaques dos tópicos essenciais do Azure Synapse.

## <a name="basics"></a>Noções básicas

Um **workspace do Azure Synapse** é um limite de colaboração protegível para fazer análises empresariais baseadas em nuvem no Azure. Um workspace é implantado em uma região específica e tem uma conta do ADLS Gen2 e um sistema de arquivos associados (para armazenar dados temporários). Um workspace está localizado em um grupo de recursos.

Um workspace permite que você execute análises com o SQL e o Apache Spark. Os recursos disponíveis para análises de SQL e do Spark são organizados em **pools** de SQL e do Spark. 

Um workspace pode conter qualquer número de **Serviço vinculado**, essencialmente cadeias de conexão que definem as informações de conexão necessárias para que o workspace se conecte a recursos externos.

## <a name="synapse-sql-terminology"></a>Terminologia do SQL do Synapse

O **SQL do Synapse** é a capacidade de fazer análises baseadas em T-SQL no workspace do Azure Synapse. O SQL do Synapse tem dois modelos de consumo: dedicado e sem servidor.  Para o modelo dedicado, use **pools de SQL dedicados**. Um workspace pode ter qualquer quantidade desses pools. Para usar o modelo sem servidor, use os **pools de SQL sem servidor**. Todo workspace tem um desses pools.

* **Solicitação SQL** – uma operação como uma consulta executada por meio do pool de SQL dedicado ou do pool de SQL sem servidor.
* **Script SQL** – conjunto de comandos SQL salvos em um arquivo. Um script SQL pode conter uma ou mais instruções SQL. Ele pode ser usado para executar solicitações do SQL por meio do pool de SQL dedicado ou do pool de SQL sem servidor.

## <a name="apache-spark-for-synapse-terminology"></a>Terminologia do Apache Spark para Synapse

Para usar a análise do Spark, crie e use **pools do Apache Spark sem servidor** no seu workspace do Azure Synapse.


* **Apache Spark para Synapse** – tempo de execução do Spark usado em um pool do Spark sem servidor. A versão atual compatível é Spark 2.4 com Python 3.6.1, Scala 2.11.12, suporte do .NET para Apache Spark 0.5 e Delta Lake 0.3.  
* **Pool do Apache Spark** – os recursos provisionados do Spark de 0 a N com os bancos de dados correspondentes podem ser implantados em um workspace. Um Pool do Spark pode ser colocado em pausa automaticamente, retomado e escalado.  
* **Aplicativo Spark** – consiste em um processo de driver e em um conjunto de processos de executor. Um aplicativo Spark é executado em um Pool do Spark sem servidor.            
* **Sessão do Spark** – ponto de entrada unificado de um aplicativo Spark. Ele fornece um modo de interagir com várias funcionalidades do Spark e com um número menor de constructos. Para executar um notebook, uma sessão precisará ser criada. Uma sessão pode ser configurada para ser executada em um número específico de executores de um tamanho específico. A configuração padrão de uma sessão de notebook é a execução em dois executores de tamanho médio.
* **Notebook** – interface interativa e reativa de Engenharia e Ciência de Dados que dá suporte ao Scala, ao PySpark, ao C# e ao SparkSQL.
* **Definição de trabalho do Spark** – interface usada para enviar um trabalho do Spark com o JAR do assembly contendo o código e as dependências.

## <a name="pipelines-terminology"></a>Terminologia do Pipelines
* **Integração de dados** – fornece a capacidade de ingerir dados entre várias fontes e orquestrar atividades em execução em um workspace ou fora dele.
* **Fluxo de Dados** – fornece uma experiência totalmente visual sem nenhuma codificação necessária para fazer a transformação de Big Data. Toda a otimização e a execução são processadas sem servidor.
* **Pipeline** – agrupamento lógico de atividades que executam uma tarefa juntas.
* **Atividade** – define as ações a serem executadas nos dados, como copiar dados, executar um Notebook ou um script SQL.
* **Gatilho** – executa um pipeline. Pode ser executado manual ou automaticamente (agenda, janela em cascata ou baseada em evento)
* **Conjunto de dados de integração** – exibição nomeada de dados que apenas apontam para os dados ou referenciam os dados a serem usados em uma atividade como entrada e saída. Pertence a um serviço vinculado.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)

