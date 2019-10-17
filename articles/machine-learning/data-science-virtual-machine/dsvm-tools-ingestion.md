---
title: Ferramentas de ingestão de dados
titleSuffix: Azure Data Science Virtual Machine
description: Saiba mais sobre as ferramentas e os utilitários de ingestão de dados pré-instalados no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 72a881a2ab508caa2274f3b9299157921cb8ceff
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330688"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de Máquina Virtual de Ciência de Dados

Como uma das primeiras etapas técnicas em um projeto de ciência de dados ou de ia, você deve identificar os conjuntos de dados a serem usados e colocá-los em seu ambiente de análise. O Máquina Virtual de Ciência de Dados (DSVM) fornece ferramentas e bibliotecas para trazer dados de diferentes fontes para o armazenamento de dados analíticos localmente no DSVM ou em uma plataforma de dados na nuvem ou no local.

Aqui estão algumas ferramentas de movimentação de dados que estão disponíveis no DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados do armazenamento de BLOBs do Azure para Azure Data Lake Store. Você também pode copiar dados entre contas do Azure Data Lake Store.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Importando vários BLOBs do armazenamento de BLOBs do Azure para o Azure Data Lake Store.      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `adlcopy` para obter ajuda.    |
| Links para exemplos      | [Como usar AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Ferramentas relacionadas no DSVM      | AzCopy, CLI do Azure     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gerenciamento para o Azure. Ele também contém verbos de comando para mover dados de plataformas de dados do Azure, como armazenamento de BLOBs do Azure e Azure Data Lake Store.     |
| Versões do DSVM com suporte      | Windows, Linux     |
| Usos típicos      | Importando e exportando dados de e para o armazenamento do Azure e Azure Data Lake Store.      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `az` para obter ajuda.    |
| Links para exemplos      | [Usar a CLI do Azure](https://docs.microsoft.com/cli/azure)     |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de e para arquivos locais, armazenamento de BLOBs do Azure, arquivos e tabelas.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Copiar arquivos para o armazenamento de BLOBs do Azure e copiar BLOBs entre contas.      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `azcopy` para obter ajuda.    |
| Links para exemplos      | [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas no DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados do Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados de várias fontes para Azure Cosmos DB, um banco de dado NoSQL na nuvem. Essas fontes incluem arquivos JSON, arquivos CSV, SQL, MongoDB, armazenamento de tabelas do Azure, Amazon DynamoDB e coleções de API do SQL Azure Cosmos DB.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Importando arquivos de uma VM para o CosmosDB, importando dados do armazenamento de tabelas do Azure para CosmosDB e importando dados de um banco de dado Microsoft SQL Server para o CosmosDB.     |
|  Como usar/executar?    |   Para usar a versão de linha de comando, abra um prompt de comando e digite `dt`. Para usar a ferramenta GUI, abra um prompt de comando e digite `dtui`.    |
| Links para exemplos      | [CosmosDB importar dados](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Interface gráfica do usuário para interagir com arquivos armazenados na nuvem do Azure. |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Importando e exportando dados do DSVM.    |
|  Como usar/executar?    | Pesquise por "Gerenciador de Armazenamento do Azure" no menu iniciar. |
| Links para exemplos      | [Gerenciador de Armazenamento do Azure](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta SQL Server para copiar dados entre o SQL Server e um arquivo de dados.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Importar um arquivo CSV para uma tabela SQL Server e exportar uma tabela SQL Server para um arquivo.      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `bcp` para obter ajuda.    |
| Links para exemplos      | [utilitário bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Ferramentas relacionadas no DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para montar um contêiner de armazenamento de BLOBs do Azure no sistema de arquivos do Linux.      |
| Versões do DSVM com suporte      | Linux      |
| Usos típicos      | Leitura e gravação em BLOBs em um contêiner.      |
|  Como usá-lo e executá-lo?    |   Execute _blobfuse_ em um terminal.    |
| Links para exemplos      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas no DSVM      | Azure CLI      |
