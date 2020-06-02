---
title: O que é o Azure Synapse Analytics?
description: Uma visão geral do Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 0f34b853728c91d9e3c6e32396c0f7eb427af101
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656216"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>O que é o Azure Synapse Analytics (versão prévia dos workspaces)?

[!INCLUDE [preview](includes/note-preview.md)]

Hoje, a análise empresarial exige uma operação em grande escala em qualquer tipo de dados, sejam eles brutos, refinados ou altamente coletados. No passado, a criação desses tipos de soluções de análise exigia que as empresas reunisse tecnologias de Big Data e data warehouse, como Spark e SQL. Depois, eles precisavam integrá-las a pipelines de dados avançados que funcionam entre dados em relational stores e data lakes.  

Soluções como essa são difíceis de serem criadas, configuradas, protegidas e mantidas, o que atrasa a extração rápida de insights inteligentes.

O **Azure Synapse** é um serviço de análise integrada que acelera o tempo de descoberta de insights de todos os dados em qualquer escala, entre data warehouses e sistemas de análise de Big Data. Ele reúne o melhor das tecnologias do **SQL** usadas em data warehouses empresariais, das tecnologias do **Spark** usadas na análise de Big Data e do **Pipelines** para orquestrar atividades e movimentação de dados.

O Azure Synapse é fornecido com uma experiência do **Studio** nativa da Web que fornece uma só experiência e modelo para gerenciamento, monitoramento, codificação e segurança.

O Azure Synapse fornece a maneira mais simples e rápida para uma empresa obter insights sobre qualquer tipo de dados em qualquer tamanho, usando a análise com a qual está mais familiarizada. Ele se integra profundamente ao **Power BI**, permitindo que os engenheiros de dados criem soluções de análise que funcionam de ponta a ponta para fornecer business intelligence.

Além disso, o Azure Synapse facilita a criação de modelos preditivos e a análise avançada com o aprendizado de máquina por meio do suporte interno para o **AzureML**.

## <a name="key-features--benefits"></a>Principais recursos e benefícios

### <a name="industry-leading-sql"></a>SQL líder do setor

* O **Synapse SQL** é um sistema de consulta distribuída que permite que as empresas implementem cenários de data warehouse e de virtualização de dados usando experiências padrão de T-SQL conhecidas para os engenheiros de dados. Ele também expande as funcionalidades do SQL para lidar com cenários de streaming e aprendizado de máquina.

* O Synapse SQL oferece modelos de recursos **sem servidor** e **provisionados**, oferecendo opções de consumo e cobrança de acordo com as suas necessidades. Para obter desempenho e custo previsíveis, provisione os pools para reservar a capacidade de processamento para os dados armazenados em tabelas SQL. Para cargas de trabalho não planejadas ou intermitentes, use o ponto de extremidade SQL sem servidor e sempre disponível.
* Use funcionalidades internas de **streaming** para enviar dados de fontes de dados na nuvem para tabelas SQL
* Integre a IA ao SQL usando modelos de **machine learning** para pontuar dados usando a função T-SQL PREDICT

### <a name="industry-standard-apache-spark"></a>Apache Spark padrão do setor

O **Apache Spark para Azure Synapse** integra-se total e perfeitamente ao Apache Spark, o mecanismo de Big Data de software livre mais popular usado para preparação de dados, engenharia de dados, ETL e aprendizado de máquina.

* Os modelos de ML com algoritmos do SparkML e integração do AzureML para Apache Spark 2.4 com suporte interno para o Linux Foundation Delta Lake.
* Modelo de recursos simplificado que libera você das preocupações com o gerenciamento de clusters.
* Inicialização rápida do Spark e dimensionamento automático agressivo.
* Suporte interno para .NET para Spark, permitindo que você reutilize sua experiência com C# e o código .NET existente em um aplicativo Spark.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interoperabilidade do SQL e do Apache Spark no Data Lake

O Azure Synapse remove as barreiras de tecnologia tradicionais entre o uso do SQL e do Spark juntos. Você pode fazer combinações e correspondências perfeitas de acordo com as suas necessidades e a sua experiência.

* Um sistema compartilhado de metadados compatível com Hive permite que as tabelas definidas nos arquivos do data lake sejam consumidas diretamente pelo Spark ou pelo Hive.
* O SQL e o Spark podem explorar e analisar diretamente arquivos Parquet, CSV, TSV e JSON armazenados no data lake.
* Carregamento e descarregamento escalonáveis e rápidos para dados transferidos entre bancos de dados SQL e Spark

### <a name="built-in-orchestration-via-pipelines"></a>Orquestração interna por meio de pipelines

O Azure Synapse vem integrado ao mesmo mecanismo de integração de dados e as experiências do Azure Data Factory, permitindo que você crie pipelines de dados avançados sem usar um mecanismo de orquestração separado.

* Mover dados entre o Azure Synapse e mais de 90 fontes de dados locais
* Orquestrar notebooks, pipelines, trabalhos do Spark, scripts SQL e procedimentos armazenados
* ETL sem código com atividades de fluxo de dados

### <a name="unified-management-monitoring-and-security"></a>Gerenciamento unificado, monitoramento e segurança

O Azure Synapse fornece um só modo para que as empresas gerenciem recursos de análise, monitorem o uso e a atividade e imponham a segurança.

* Atribuir usuários a uma função para simplificar o acesso aos recursos de análise
* Controle de acesso refinado sobre dados e código
* Um só painel para monitorar recursos, uso e usuários no SQL e no Spark

### <a name="synapse-studio"></a>Synapse Studio

O **Synapse Studio** é a experiência nativa da Web que reúne tudo para os engenheiros de dados, permitindo que eles realizem todas as tarefas necessárias para criar uma solução completa em um só lugar.

* Crie uma solução de análise de ponta a ponta em um só lugar: ingerir, explorar, preparar, orquestrar e visualizar
* Produtividade líder do setor para engenheiros de dados que trabalham com a codificação SQL ou Spark: criação, depuração e otimização de desempenho
* Integração aos processos CI/CD empresariais

## <a name="next-steps"></a>Próximas etapas

* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o Synapse Studio](quickstart-synapse-studio.md)
* [Criar um pool de SQL](quickstart-create-sql-pool-portal.md)
* [Usar o SQL sob demanda](quickstart-sql-on-demand.md)
* [Criar um Pool do Apache Spark](quickstart-create-apache-spark-pool-portal.md)
