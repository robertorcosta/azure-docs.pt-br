---
title: Recursos compatíveis do Link do Azure Synapse (versão prévia) para o Azure Cosmos DB
description: Entender a lista atual de ações compatíveis com o Link do Azure Synapse para Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 2e0b1ee5584420ab38fda8897ef610794b09c29a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658846"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Recursos compatíveis do Link do Azure Synapse (versão prévia) para o Azure Cosmos DB

Este artigo descreve as funcionalidades atualmente compatíveis no Link do Azure Synapse para Azure Cosmos DB. 

## <a name="azure-synapse-support"></a>Suporte do Azure Synapse

Há dois tipos de contêineres no Azure Cosmos DB:
* Contêiner HTAP - um contêiner com o Link do Synapse habilitado. Esse contêiner tem repositório transacional e repositório analítico. 
* Contêiner OLTP - um contêiner com apenas o repositório transacional. O Link do Synapse não está habilitado. 

Você pode se conectar a um contêiner do Azure Cosmos DB sem habilitar o Link do Synapse. Nesse caso, você só poderá ler/gravar no repositório transacional. Segue uma lista dos recursos compatíveis no Link do Synapse para Azure Cosmos DB. 

| Categoria              | Descrição |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL sem servidor](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Suporte para tempo de execução** |Suporte para leitura ou gravação no tempo de execução do Azure Synapse| ✓ | [Fale conosco](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Suporte para API do Azure Cosmos DB** |Suporte para API como um Link do Synapse| SQL/MongoDB | SQL/MongoDB |
| **Objeto**  |Objetos, como uma tabela que pode ser criada, apontando diretamente para o contêiner do Azure Cosmos DB| Exibir, tabela | Visualizar |
| **Leitura**    |Ler dados em um contêiner do Azure Cosmos DB| OLTP/HTAP | HTAP  |
| **Gravar**   |Gravar dados do tempo de execução em um contêiner do Azure Cosmos DB| OLTP | n/d |

* Se você gravar dados em um contêiner do Azure Cosmos DB pelo Spark, esse processo ocorrerá por meio do repositório transacional do Azure Cosmos DB e afetará o desempenho transacional do Azure Cosmos DB consumindo Unidades de Solicitação.
* A integração do pool de SQL por meio de tabelas externas não tem suporte no momento.

## <a name="supported-code-generated-actions-for-spark"></a>Ações geradas por código compatíveis para Spark

| Gesto              | Descrição |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Carregar em DataFrame** |Carregar e ler dados em um DataFrame do Spark |X| ✓ |
| **Criar tabela do Spark** |Criar uma tabela apontando para um contêiner do Azure Cosmos DB|X| ✓ |
| **Gravar DataFrame no contêiner** |Gravar dados em um contêiner|✓| ✓ |
| **Carregar DataFrame de streaming do contêiner** |Transmitir dados usando o feed de alterações do Azure Cosmos DB|✓| ✓ |
| **Gravar DataFrame de streaming no contêiner** |Transmitir dados usando o feed de alterações do Azure Cosmos DB|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Ações geradas por código compatíveis para SQL sem servidor

| Gesto              | Descrição |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Selecionar 100 primeiros** |Visualizar os 100 primeiros itens de um contêiner|X| ✓ |
| **Criar exibição** |Criar uma exibição para ter acesso direto ao BI em um contêiner por meio do SQL do Synapse|X| ✓ |

## <a name="next-steps"></a>Próximas etapas

* Confira como [conectar-se ao Link do Azure Synapse para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Saiba como consultar o repositório analítico com o Spark](how-to-query-analytical-store-spark.md)