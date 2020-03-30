---
title: Bibliotecas Apache Hive durante a criação de clusters - Azure HDInsight
description: Aprenda a adicionar bibliotecas Apache Hive (arquivos de jar) a um cluster HDInsight durante a criação de clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471016"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicionar bibliotecas Apache Hive personalizadas ao criar seu cluster HDInsight

Aprenda a pré-carregar bibliotecas [Apache Hive](https://hive.apache.org/) no HDInsight. Este documento contém informações sobre como usar uma Ação de script para pré-carregar bibliotecas durante a criação do cluster. Bibliotecas adicionadas usando as etapas deste documento estão disponíveis globalmente na Colmeia - não há necessidade de usar [o ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como ele funciona

Ao criar um cluster, você pode usar uma Ação de Script para modificar os nós de cluster à medida que eles são criados. O script neste documento aceita um único parâmetro, que é a localização das bibliotecas. Essa localização deve estar em uma conta de Armazenamento do Azure e as bibliotecas devem ser armazenadas como arquivos jar.

Durante a criação do cluster, o script enumera os arquivos, copia-os para o diretório `/usr/lib/customhivelibs/` nos nós de cabeçalho e de trabalho e adiciona-os à propriedade `hive.aux.jars.path` no arquivo `core-site.xml`. Em clusters baseados em Linux, ele também atualiza o arquivo `hive-env.sh` com o local dos arquivos.

O uso da ação de script neste artigo torna as bibliotecas disponíveis ao usar um cliente Hive para **WebHCat**e **HiveServer2**.

## <a name="the-script"></a>O script

**Local do script**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Requisitos

* Os scripts devem ser aplicados nos**Nós do cabeçalho** e nos **Nós de trabalho**.

* Os jars que você deseja instalar devem estar armazenados no Armazenamento de Blobs do Azure em um **único contêiner**.

* A conta de armazenamento contendo a biblioteca de arquivos jar **deve** ser vinculada ao cluster HDInsight durante a criação. Ele deve ser a conta de armazenamento padrão ou uma conta adicionada através __das Configurações da conta de armazenamento.__

* O caminho WASB para o contêiner deve ser especificado como um parâmetro para a Ação de Script. Por exemplo, se os jars estivessem armazenados em um contêiner denominado **libs** em uma conta de armazenamento denominada **mystorage**, o parâmetro seria `wasbs://libs@mystorage.blob.core.windows.net/`.

  > [!NOTE]  
  > Este documento supõe que você já criou uma conta de armazenamento, um contêiner de blobs e carregou os arquivos nele.
  >
  > Se você ainda não criou uma conta de armazenamento, faça isso usando o [portal do Azure](https://portal.azure.com). Lá, você pode usar um utilitário como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para criar um contêiner na conta e carregar arquivo nele.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

1. Comece a provisionar um cluster usando as etapas nos [clusters Provision HDInsight no Linux,](hdinsight-hadoop-provision-linux-clusters.md)mas não complete o provisionamento. Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com Ações de Script](hdinsight-hadoop-customize-cluster-linux.md). Para o portal Azure, na guia **Configuração + preços,** selecione a **ação + Adicionar script**.

1. Para **armazenamento,** se a conta de armazenamento que contém a biblioteca de arquivos de jar será diferente da conta usada para o cluster, complete **contas adicionais de armazenamento**.

1. Para **ações de script,** forneça as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Nome|Bibliotecas |
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Tipo(s) de nó|Chefe, Operário|
    |Parâmetros|Insira o endereço WASB para o contêiner e a conta de armazenamento que contém os jars. Por exemplo, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Para Apache Spark 2.1, use `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`este script de bash URI: .

1. Continue a provisionar o cluster como descrito em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Uma vez concluída a criação do cluster, você pode usar os frascos adicionados através deste script da Colmeia sem ter que usar a `ADD JAR` declaração.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com o Hive, consulte [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
