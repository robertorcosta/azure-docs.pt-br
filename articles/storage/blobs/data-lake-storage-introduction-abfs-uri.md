---
title: Usar o Azure Data Lake armazenamento Gen2 URI
description: Usar o Azure Data Lake armazenamento Gen2 URI
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437123"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Usar o Azure Data Lake armazenamento Gen2 URI

O driver do [Hadoop Filesystem](https://www.aosabook.org/en/hdfs.html) que é compatível com o Azure Data Lake Storage Gen2 é conhecido por seu identificador de esquema `abfs` (sistema de arquivos de Blob do Azure). Consistente com outros drivers de Hadoop Filesystem, o driver ABFS utiliza um formato URI para arquivos de endereço e diretórios dentro de uma conta com capacidade Gen2 de armazenamento do Data Lake.

## <a name="uri-syntax"></a>Sintaxe URI

A sintaxe URI para o Data Lake armazenamento Gen2 depende se ou não sua conta de armazenamento é configurada para ter dados Lake armazenamento Gen2 como o sistema de arquivos padrão.

Se a conta de capaz de Data Lake armazenamento Gen2 que deseja endereço **não** é definida como o sistema de arquivos padrão durante a criação da conta, é a abreviação de sintaxe do URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador do esquema**: O `abfs` protocolo é usado como o identificador do esquema. Você tem a opção de conectar-se com ou sem uma TLS (segurança de camada de transporte), anteriormente conhecida como protocolo SSL (SSL), conexão. Use `abfss` para conectar-se a uma conexão TLS.

2. **Sistema de arquivos**: O local do pai que contém os arquivos e pastas. Isso é o mesmo que contêineres no serviço de Blobs de armazenamento do Azure.

3. **Nome da conta**: O nome dado à sua conta de armazenamento durante a criação.

4. **Caminhos**: uma barra invertida delimitada (`/`) representação da estrutura de diretório.

5. **Nome do arquivo**: o nome do arquivo individual. Esse parâmetro é opcional se você está abordando um diretório.

Porém, se a conta que deseja endereço não for definida como o sistema de arquivos padrão durante a criação da conta, é a abreviação de sintaxe do URI:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Caminho**: uma barra invertida delimitada (`/`) representação da estrutura de diretório.

2. **Nome do arquivo**: O nome do arquivo individual.


## <a name="next-steps"></a>Próximas etapas

- [Usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
