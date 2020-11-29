---
title: Terminologia – Azure Synapse Analytics (versão prévia dos workspaces)
description: Guia de referência que orienta o usuário pelo Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c58ee46a608ccdcbb01a082ee278d9e0f8a07f6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030672"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologia do Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Este documento descreve os conceitos básicos do Azure Synapse Analytics.

## <a name="basics"></a>Noções básicas

Um **workspace do Azure Synapse** é um limite de colaboração protegível para fazer análises empresariais baseadas em nuvem no Azure. Um workspace é implantado em uma região específica e tem uma conta do ADLS Gen2 e um sistema de arquivos associados (para armazenar dados temporários). Um workspace está localizado em um grupo de recursos.

Um workspace permite que você execute análises com o SQL e o Apache Spark. Os recursos disponíveis para análises de SQL e do Spark são organizados em **pools** de SQL e do Spark. 

## <a name="linked-services"></a>Serviços vinculados

Um workspace pode conter qualquer número de **Serviço vinculado**, essencialmente cadeias de conexão que definem as informações de conexão necessárias para que o workspace se conecte a recursos externos.

## <a name="synapse-sql"></a>SQL do Synapse

O **SQL do Synapse** é a capacidade de fazer análises baseadas em T-SQL no workspace do Azure Synapse. O SQL do Synapse tem dois modelos de consumo: dedicado e sem servidor.  Para o modelo dedicado, use **pools de SQL dedicados**. Um workspace pode ter qualquer quantidade desses pools. Para usar o modelo sem servidor, use os **pools de SQL sem servidor**. Todo workspace tem um desses pools.

* **Solicitação SQL** – uma operação como uma consulta executada por meio do pool de SQL dedicado ou do pool de SQL sem servidor.
* **Script SQL** – conjunto de comandos SQL salvos em um arquivo. Um script SQL pode conter uma ou mais instruções SQL. Ele pode ser usado para executar solicitações do SQL por meio do pool de SQL dedicado ou do pool de SQL sem servidor.

## <a name="apache-spark-for-synapse"></a>Apache Spark para o Synapse

Para usar a análise do Spark, crie e use **pools do Apache Spark sem servidor** no seu workspace do Azure Synapse. Quando você começa a usar um Pool do Spark, os workspaces criam uma **sessão do Spark** para processar os recursos associados à sessão. 

Há duas maneiras de usar o Spark no Azure Synapse:
* **Notebooks do Spark** para realizar ciência e engenharia de dados. Use o Scala, o PySpark, o C# e o Spark SQL
* **Definições de trabalho do Spark** para executar trabalhos em lote do Spark usando arquivos JAR.

Suporte à versão:
* Spark 2.4
* Python 3.6.1
* Scala 2.11.12
* .NET para Apache Spark 1.0
* Delta Lake 0.3.  

## <a name="pipelines"></a>Pipelines

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

