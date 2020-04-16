---
title: O que é delta lake
description: Visão geral do Delta Lake e como ele funciona como parte do Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429194"
---
# <a name="what-is-delta-lake"></a>O que é o Delta Lake?

O Azure Synapse Analytics é compatível com o Linux Foundation Delta Lake. Delta Lake é uma camada de armazenamento de código aberto que traz transações ACID (atomicity, consistência, isolamento e durabilidade) para apache spark e cargas de trabalho de big data.

## <a name="key-features"></a>Principais recursos

| Recurso | Descrição |
| --- | --- |
| **Transações acidiais** | Os lagos de dados são tipicamente preenchidos através de múltiplos processos e pipelines, alguns dos quais estão escrevendo dados simultaneamente com leituras. Antes do Delta Lake e da adição de transações, os engenheiros de dados tiveram que passar por um processo manual propenso a erros para garantir a integridade dos dados. Delta Lake traz transações acidiais familiares para lagos de dados. Proporciona serlizabilidade, o nível mais forte de isolamento. Saiba mais em [Diving into Delta Lake: Desempacotando o registro de transações](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Manuseio de metadados escaláveis** | Em big data, até mesmo os metadados em si podem ser "big data". Delta Lake trata metadados como dados, aproveitando o poder de processamento distribuído da Spark para lidar com todos os seus metadados. Como resultado, delta lake pode lidar com tabelas em escala de petabyte com bilhões de partições e arquivos à vontade. |
| **Viagem no Tempo (versão de dados)** | A capacidade de "desfazer" uma alteração ou voltar para uma versão anterior é uma das principais características das transações. O Delta Lake fornece instantâneos de dados que permitem reverter para versões anteriores de dados para auditorias, reversões ou para reproduzir experimentos. Saiba mais na [introdução da Delta Lake Time Travel para lagos de dados em grande escala.](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html) |
| **Formato Aberto** | Apache Parquet é o formato de linha de base do Delta Lake, permitindo que você aproveite os eficientes esquemas de compressão e codificação que são nativos do formato. |
| **Fonte e pia unificadas de lote e streaming** | Uma mesa em Delta Lake é uma mesa de lote, bem como uma fonte de streaming e pia. A ingestão de dados de streaming, o refil histórico em lote e as consultas interativas funcionam apenas fora da caixa. |
| **Aplicação de esquemas** | A aplicação do esquema ajuda a garantir que os tipos de dados estejam corretos e as colunas necessárias estejam presentes, evitando que dados ruins causem inconsistência de dados. Para obter mais informações, consulte [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Evolução do Esquema** | Delta Lake permite que você faça alterações em um esquema de tabela que pode ser aplicado automaticamente, sem ter que escrever DDL de migração. Para obter mais informações, consulte [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Histórico de auditoria** | O registro de transações delta lake registra detalhes sobre cada alteração feita em dados fornecendo uma trilha de auditoria completa das alterações. |
| **Atualizações e Exclusões** | Delta Lake suporta APIs Scala / Java / Python e SQL para uma variedade de funcionalidades. O suporte para mesclar, atualizar e excluir operações ajuda você a atender aos requisitos de conformidade. Para obter mais informações, consulte [Anunciando a versão delta lake 0.4.0](https://delta.io/news/delta-lake-0-4-0-released/) e [upserts e exclusões simples e confiáveis em Tabelas delta lake usando APIs Python](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), que inclui trechos de código para mesclar, atualizar e excluir comandos DML. |
| **100% compatível com a API apache spark** | Os desenvolvedores podem usar o Delta Lake com seus pipelines de dados existentes com alteração mínima, pois é totalmente compatível com as implementações existentes do Spark. |

Para obter documentação completa, consulte a [página de documentação](https://docs.delta.io/latest/delta-intro.html) do Lago Delta

Para obter mais informações, consulte [Delta Lake Project](https://lfprojects.org).

## <a name="next-steps"></a>Próximas etapas

- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
