---
title: Recursos compatíveis do Link do Azure Synapse (versão prévia) para o Azure Cosmos DB
description: Entender a lista atual de ações compatíveis com o Link do Azure Synapse para Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 336409b8b6f804b224b87d5fb11fded0654b8619
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895524"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Recursos compatíveis do Link do Azure Synapse (versão prévia) para o Azure Cosmos DB

Este artigo descreve as funcionalidades atualmente compatíveis no Link do Azure Synapse para Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Suporte do Azure Synapse

Há dois tipos de contêineres no Azure Cosmos DB:
* Contêiner HTAP - um contêiner com o Link do Synapse habilitado. Esse contêiner tem repositório transacional e repositório analítico. 
* Contêiner OLTP-um contêiner com o link Synaspe não habilitado. Esse contêiner tem apenas armazenamento transacional e nenhum repositório analítico.

> [!IMPORTANT]
> O link Synapse do Azure para Azure Cosmos DB tem suporte atualmente em espaços de trabalho do Synapse que não têm a rede virtual gerenciada habilitada. 

Você pode se conectar a um contêiner do Azure Cosmos DB sem habilitar o Link do Synapse. Nesse caso, você só poderá ler/gravar no repositório transacional. O que vem a seguir é a lista dos recursos com suporte no momento no link do Synapse para Azure Cosmos DB. 

| Categoria              | Descrição |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL sem servidor](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Suporte para tempo de execução** |Tempo de execução do Azure Synapse com suporte para acessar Azure Cosmos DB| ✓ | [Fale conosco](mailto:cosmosdbsynapselink@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB) |
| **Suporte para API do Azure Cosmos DB** | Tipo de API Azure Cosmos DB com suporte | SQL/MongoDB | SQL/MongoDB |
| **Objeto**  |Objetos, como uma tabela que pode ser criada, apontando diretamente para o contêiner do Azure Cosmos DB| Dataframe, exibição, tabela | Visualizar |
| **Leitura**    | Tipo de contêiner de Azure Cosmos DB que pode ser lido | OLTP/HTAP | HTAP  |
| **Gravar**   | O tempo de execução do Azure Synapse pode ser usado para gravar dados em um contêiner de Azure Cosmos DB | Sim | Não |

* Se você gravar dados em um contêiner de Azure Cosmos DB do Spark, isso ocorrerá por meio do armazenamento transacional de Azure Cosmos DB e afetará o desempenho de cargas de trabalho transacionais em Azure Cosmos DB e consumirá unidades de solicitação.
* Não há suporte para a integração do pool SQL Synapse por meio de tabelas externas no momento.

## <a name="supported-code-generated-actions-for-spark"></a>Ações geradas por código compatíveis para Spark

| Gesto              | Descrição |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Carregar em DataFrame** |Carregar e ler dados em um DataFrame do Spark |✓| ✓ |
| **Criar tabela do Spark** |Criar uma tabela apontando para um contêiner do Azure Cosmos DB|✓| ✓ |
| **Gravar DataFrame no contêiner** |Gravar dados em um contêiner|✓| ✓ |
| **Carregar DataFrame de streaming do contêiner** |Transmitir dados usando o feed de alterações do Azure Cosmos DB|✓| ✓ |
| **Gravar DataFrame de streaming no contêiner** |Transmitir dados usando o feed de alterações do Azure Cosmos DB|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Ações geradas por código compatíveis para SQL sem servidor

| Gesto              | Descrição |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Explorar dados** |Explorar dados de um contêiner com sintaxe T-SQL familiar e inferência de esquema automática|X| ✓ |
| **Criar exibições e criar relatórios de BI** |Crie um modo de exibição SQL para ter acesso direto a um contêiner para BI até Synapse SQL Server |X| ✓ |
| **Unir fontes de dados distintas junto com Cosmos DB dados** | Armazenar os resultados da consulta lendo dados de contêineres Cosmos DB junto com os dados no armazenamento de BLOBs do Azure ou Azure Data Lake Storage usando CETAS |X| ✓ |

## <a name="next-steps"></a>Próximas etapas

* Confira como [conectar-se ao Link do Azure Synapse para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Saiba como consultar o repositório analítico com o Spark](how-to-query-analytical-store-spark.md)
