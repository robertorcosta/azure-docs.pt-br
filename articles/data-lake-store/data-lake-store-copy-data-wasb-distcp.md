---
title: Copiar dados de e para wasb no Azure Data Lake Storage Gen1 usando DistCp
description: Use a ferramenta DistCp para copiar dados para e de blobs de armazenamento azure para Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638826"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Use o DistCp para copiar dados entre blobs de armazenamento Azure e Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Como usar DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Como usar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se você tiver um cluster HDInsight com acesso ao Azure Data Lake Storage Gen1, você pode usar ferramentas do ecossistema Hadoop como o DistCp para copiar dados de e para um armazenamento em cluster HDInsight (WASB) em uma conta Data Lake Storage Gen1. Este artigo mostra como usar a ferramenta DistCp.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure.** Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções de como criar uma, confira [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar o DistCp de um cluster HDInsight do Linux

Um cluster HDInsight vem com a ferramenta DistCp, que pode ser usada para copiar dados de diferentes fontes em um cluster HDInsight. Se você configurou o cluster HDInsight para usar o Data Lake Storage Gen1 como armazenamento adicional, você pode usar o DistCp fora da caixa para copiar dados de e para uma conta Data Lake Storage Gen1. Nesta seção, vemos como usar a ferramenta DistCp.

1. Da sua área de trabalho, use o SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Execute os comandos do prompt de SSH.

1. Verifique se você pode acessar as bolhas de armazenamento do Azure (WASB). Execute o comando a seguir:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   A saída fornece uma lista de conteúdos na bolha de armazenamento.

1. Da mesma forma, verifique se você pode acessar a conta de do Data Lake Storage Gen1 do cluster. Execute o comando a seguir:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    A saída fornece uma lista de arquivos e pastas na conta Data Lake Storage Gen1.

1. Use DistCp para copiar dados do WASB para uma conta Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    O comando copia o conteúdo da pasta **/example/data/gutenberg/** do WASB em **/myfolder** na conta do Data Lake Storage Gen1.

1. Da mesma forma, use DistCp para copiar dados de uma conta Data Lake Storage Gen1 para WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    O comando copia o conteúdo de **/myfolder** na conta Data Lake Storage Gen1 para **/exemplo/data/gutenberg/folder** no WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao usar o DistCp

Como a menor granularidade da ferramenta DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação ao Data Lake Storage Gen1. Você pode controlar o número de cópias simultâneas definindo o número de mapeadores ('m') parâmetro na linha de comando. Esse parâmetro especifica o número máximo de mapeadores que são usados para copiar dados. O valor padrão é 20.

Exemplo:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Como determinar o número de mapeadores a serem usados

Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: Determinar a memória total de YARN** ‑ A primeira etapa é determinar a memória YARN disponível para o cluster em que você executa o trabalho DistCp. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue até O Fio e visualize a guia **Configs** para ver a memória YARN. Para obter a memória YARN total, multiplique a memória YARN por nó pelo número de nós que você tem no cluster.

* **Etapa 2: Calcular o número de mapeadores** ‑ O valor de **m** é igual ao quociente de memória total de YARN dividida pelo tamanho do contêiner YARN. As informações sobre o tamanho do contêiner YARN também estão disponíveis no portal Ambari. Navegue até O Fio e visualize a guia **Configs.** O tamanho do recipiente YARN é exibido nesta janela. A equação para chegar ao número de mapeadores **(m)** é:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Exemplo:

Vamos supor que você tem quatro nódulos D14v2s no cluster e você deseja transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contêm volumes diversos de dados e os tamanhos dos arquivos em cada pasta também são diferentes.

* Total de memória YARN – No portal do Ambari você determina que a memória YARN é 96 GB para um nó D14. Portanto, o total de memória YARN para um cluster de quatro nós é:

   `YARN memory = 4 * 96GB = 384GB`

* Número de mapeadores ‑ Do portal do Ambari a você determina que o tamanho do contêiner YARN é 3072 para um nó de cluster D14. Então, o número de mapeadores é:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Se outros aplicativos estiverem usando memória, você pode optar por usar apenas uma parte da memória YARN do seu cluster para DistCp.

### <a name="copying-large-datasets"></a>Copiando grandes conjuntos de dados

Quando o tamanho do conjunto de dados a ser movido for grande (por exemplo, > 1 TB) ou se você tiver muitas pastas diferentes, considere usar vários trabalhos distCp. Provavelmente não há ganho de desempenho, mas ele espalha os trabalhos de modo que, se qualquer trabalho falhar, você só precisa reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* O DistCp tenta criar mapeadores de tamanho semelhantes para otimizar o desempenho. Aumentar o número de mapeadores nem sempre melhora o desempenho.

* O DistCp é limitado a apenas um mapeador por arquivo. Portanto, você não deve ter mais mapeadores do que você tem arquivos. Como o DistCp pode atribuir apenas um mapeador a um arquivo, isso limita a quantidade de simultuá- lo como moeda que pode ser usada para copiar arquivos grandes.

* Se você tiver um pequeno número de arquivos grandes, divida-os em pedaços de arquivo de 256 MB para lhe dar mais potencial de concorrência.

* Se você estiver copiando de uma conta de armazenamento Azure Blob, seu trabalho de cópia pode ser estrangulado no lado de armazenamento Blob. Isso pode degradar o desempenho do seu trabalho de cópia. Para saber mais sobre os limites do armazenamento Azure Blob, consulte os limites de armazenamento do Azure nos limites de assinatura e serviço do [Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Confira também

* [Copiar dados de blobs de armazenamento do Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
