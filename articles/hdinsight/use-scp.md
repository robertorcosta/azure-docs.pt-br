---
title: Usar o SCP com Apache Hadoop no Azure HDInsight
description: Este documento fornece informações sobre como se conectar ao HDInsight usando os comandos SSH e SCP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 559746a817442602c76ba91f12c195be1d7f3cc8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82188422"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Usar o SCP com Apache Hadoop no Azure HDInsight

Este artigo fornece informações sobre como transferir arquivos com segurança com o cluster HDInsight.

## <a name="copy-files"></a>Copiar arquivos

O utilitário `scp` pode ser usado para copiar arquivos de e para os nós individuais no cluster. Por exemplo, o comando a seguir copia o diretório `test.txt` do sistema local para o nó principal primário:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Como nenhum caminho é especificado após `:`, o arquivo é colocado no diretório base `sshuser`.

O exemplo a seguir copia o arquivo `test.txt` do diretório base `sshuser` no nó principal primário para o sistema local:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` só pode acessar o sistema de arquivos de nós individuais dentro do cluster. Ele não pode ser usado para acessar dados no armazenamento compatível com HDFS para o cluster.

Use `scp` quando precisar carregar um recurso para usar em uma sessão SSH. Por exemplo, carregue um script Python, em seguida, execute o script em uma sessão SSH.

Para obter informações sobre como carregar diretamente os dados no armazenamento compatível com o HDFS, consulte os seguintes documentos:

* [HDInsight usando o armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight usando o Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar o SSH com o HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
