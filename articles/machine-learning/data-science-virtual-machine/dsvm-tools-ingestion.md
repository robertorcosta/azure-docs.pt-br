---
title: Ferramentas de ingestão de dados
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e utilitários de ingestão de dados pré-instalados na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270050"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de Máquina Virtual de Ciência de Dados

Como uma das primeiras etapas técnicas de um projeto de ciência de dados ou IA, você deve identificar os conjuntos de dados a serem usados e trazê-los para o seu ambiente de análise. A Máquina Virtual de Data Science (DSVM) fornece ferramentas e bibliotecas para levar dados de diferentes fontes para armazenamento de dados analíticos localmente no DSVM, ou em uma plataforma de dados na nuvem ou no local.

Aqui estão algumas ferramentas de movimentação de dados que estão disponíveis no DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados do armazenamento Azure Blob no Azure Data Lake Store. Você também pode copiar dados entre contas do Azure Data Lake Store.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importando vários blobs do armazenamento Azure Blob para o Azure Data Lake Store.      |
|  Como usar/executar?    |   Abra um prompt `adlcopy` de comando e digite para obter ajuda.    |
| Links para exemplos      | [Como usar AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Ferramentas relacionadas no DSVM      | AzCopy     |

## <a name="azure-cli"></a>CLI do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gerenciamento para o Azure. Ele também contém verbos de comando para mover dados de plataformas de dados do Azure, como o armazenamento Azure Blob e o Azure Data Lake Store.     |
| Versões DSVM suportadas      | Windows, Linux     |
| Usos típicos      | Importação e exportação de dados para e de armazenamento Azure e Azure Data Lake Store.      |
|  Como usar/executar?    |   Abra um prompt `az` de comando e digite para obter ajuda.    |
| Links para exemplos      | [Usando a CLI do Azure](https://docs.microsoft.com/cli/azure)     |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados para e de arquivos locais, armazenamento do Azure Blob, arquivos e tabelas.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Copiando arquivos para o armazenamento do Azure Blob e copiando blobs entre contas.      |
|  Como usar/executar?    |   Abra um prompt `azcopy` de comando e digite para obter ajuda.    |
| Links para exemplos      | [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas no DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados do Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados de várias fontes para o Azure Cosmos DB, um banco de dados NoSQL na nuvem. Essas fontes incluem arquivos JSON, arquivos CSV, SQL, MongoDB, armazenamento de tabela azure, Amazon DynamoDB e coleções de API Azure Cosmos DB SQL.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importação de arquivos de um VM para o CosmosDB, importação de dados do armazenamento da tabela Azure para o CosmosDB e importação de dados de um banco de dados do Microsoft SQL Server para o CosmosDB.     |
|  Como usar/executar?    |   Para usar a versão de linha de `dt`comando, abra um prompt de comando e digite . Para usar a ferramenta GUI, abra `dtui`um prompt de comando e digite .    |
| Links para exemplos      | [Dados de importação do CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Interface gráfica do usuário para interagir com arquivos armazenados na nuvem do Azure. |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importação e exportação de dados do DSVM.    |
|  Como usar/executar?    | Procure por "Azure Storage Explorer" no menu Iniciar. |
| Links para exemplos      | [Explorador de armazenamento azure](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta SQL Server para copiar dados entre o SQL Server e um arquivo de dados.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importar um arquivo CSV para uma tabela do SQL Server e exportar uma tabela SQL Server para um arquivo.      |
|  Como usar/executar?    |   Abra um prompt `bcp` de comando e digite para obter ajuda.    |
| Links para exemplos      | [utilitário bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Ferramentas relacionadas no DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para montar um contêiner de armazenamento Azure Blob no sistema de arquivos Linux.      |
| Versões DSVM suportadas      | Linux      |
| Usos típicos      | Lendo e escrevendo para bolhas em um recipiente.      |
|  Como usá-lo e executá-lo?    |   Execute _blobfuse_ em um terminal.    |
| Links para exemplos      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas no DSVM      | CLI do Azure      |
