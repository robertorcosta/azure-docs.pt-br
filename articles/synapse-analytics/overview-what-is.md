---
title: O que é o Azure Synapse Analytics?
description: Uma visão geral do Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/27/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 316a78f4725aa3c33a78b00c6de3b8419aea717e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600200"
---
# <a name="what-is-azure-synapse-analytics"></a>O que é o Azure Synapse Analytics?

O **Azure Synapse** é um serviço de análise empresarial que acelera o tempo de descoberta de insights entre data warehouses e sistemas de Big Data. O Azure Synapse reúne o melhor das tecnologias de **SQL** usadas em data warehousing corporativo, tecnologias **Spark** usadas para Big Data, **Pipelines** para integração de dados e ETL/ELT e integração profunda com outros serviços do Azure, como **Power BI**, **CosmosDB** e **AzureML**.


## <a name="industry-leading-sql"></a>SQL líder do setor

O **SQL do Synapse** é um sistema de consulta distribuída para o T-SQL que permite cenários de data warehouse e virtualização de dados e estende o T-SQL para lidar com cenários de streaming e machine learning.

* O SQL do Synapse fornece modelos de recursos **sem servidor** e **dedicados**. Para obter desempenho e custo previsíveis, crie pools de SQL dedicados para reservar a capacidade de processamento para os dados armazenados em tabelas SQL. Para cargas de trabalho não planejadas ou intermitentes, use o ponto de extremidade SQL sem servidor e sempre disponível.
* Use funcionalidades internas de **streaming** para enviar dados de fontes de dados na nuvem para tabelas SQL
* Integrar a IA ao SQL usando modelos de **machine learning** para pontuar dados empregando a [função T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Apache Spark padrão do setor

O **Apache Spark para Azure Synapse** integra-se total e perfeitamente ao Apache Spark, o mecanismo de Big Data de software livre mais popular usado para preparação de dados, engenharia de dados, ETL e aprendizado de máquina.

* Os modelos de ML com algoritmos do SparkML e integração do AzureML para Apache Spark 2.4 com suporte interno para o Linux Foundation Delta Lake.
* Modelo de recursos simplificado que libera você das preocupações com o gerenciamento de clusters.
* Inicialização rápida do Spark e dimensionamento automático agressivo.
* Suporte interno para .NET para Spark, permitindo que você reutilize sua experiência com C# e o código .NET existente em um aplicativo Spark.

## <a name="working-with-your-data-lake"></a>Como trabalhar com o Data Lake

O Azure Synapse remove as barreiras de tecnologia tradicionais entre o uso do SQL e do Spark juntos. Você pode fazer combinações e correspondências perfeitas de acordo com as suas necessidades e a sua experiência.

* Um sistema compartilhado de metadados compatível com Hive permite que as tabelas definidas nos arquivos do data lake sejam consumidas diretamente pelo Spark ou pelo Hive.
* O SQL e o Spark podem explorar e analisar diretamente arquivos Parquet, CSV, TSV e JSON armazenados no data lake.
* Carregamento e descarregamento escalonáveis e rápidos para dados transferidos entre bancos de dados SQL e Spark

## <a name="built-in-data-integration-via-pipelines"></a>Integração de dados interna por meio de pipelines

O Azure Synapse contém o mesmo mecanismo de Integração de Dados e as mesmas experiências do Azure Data Factory, permitindo que você crie pipelines de ETL avançados em escala sem sair do Azure Synapse Analytics.

* Ingira dados de mais de 90 fontes de dados
* ETL sem código com atividades de fluxo de dados
* Orquestre notebooks, trabalhos do Spark, procedimentos armazenados, scripts SQL, entre outros

## <a name="unified-management-monitoring-and-security"></a>Gerenciamento unificado, monitoramento e segurança

O Azure Synapse fornece um só modo para que as empresas gerenciem recursos de análise, monitorem o uso e a atividade e imponham a segurança.

* Atribuir usuários a uma função para simplificar o acesso aos recursos de análise
* Controle de acesso refinado sobre dados e código
* Um só painel para monitorar recursos, uso e usuários no SQL e no Spark

## <a name="unified-experience"></a>Experiência unificada

O **Synapse Studio** é a experiência do usuário que reúne tudo para engenheiros de dados. Ele permite que eles realizem todas as tarefas necessárias para criar uma solução de análise completa.

* Principais tarefas de engenheiro de dados em um único lugar: ingestão, exploração, preparação, orquestração, visualização
* Produtividade líder do setor que trabalham com a codificação SQL ou Spark: criação, depuração e otimização de desempenho
* Fazer integração com os processos CI/CD empresariais

## <a name="engage-with-the-synapse-engineering-team"></a>Envolver-se com a equipe de engenharia do Azure Synapse

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Faça perguntas sobre desenvolvimento.
- [Página de perguntas e respostas da Microsoft](/answers/topics/azure-synapse-analytics.html): Faça perguntas técnicas.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)
