---
title: Visão geral do armazenamento do Azure no HDInsight
description: Visão geral do Armazenamento Azure no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 43d948a2a98407bacc212ddc6e065c67a105f332
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873375"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Visão geral do armazenamento do Azure no HDInsight

O Armazenamento do Azure é uma solução robusta de armazenamento de uso geral que se integra perfeitamente ao HDInsight. O HDInsight pode usar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão para o cluster. Ao usar uma interface do HDFS, o conjunto completo de componentes no HDInsight pode operar diretamente sobre os dados estruturados ou não estruturados armazenados como blobs.

Recomendamos o uso de recipientes de armazenamento separados para o armazenamento de cluster padrão e os dados de negócios. A separação é para isolar os logs do HDInsight e arquivos temporários de seus próprios dados de negócios. Também recomendamos a exclusão do recipiente blob padrão, que contém registros de aplicativos e sistemas, após cada uso para reduzir o custo de armazenamento. Certifique-se de recuperar os logs antes de excluir o contêiner.

Se você optar por proteger sua conta de armazenamento com os **Firewalls e** as restrições de redes virtuais em **redes selecionadas,** certifique-se de ativar a exceção **Permitir serviços confiáveis da Microsoft...**. A exceção é para que o HDInsight possa acessar sua conta de armazenamento.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama a seguir fornece uma exibição abstrata da arquitetura do HDInsight do Armazenamento do Azure:

!['Arquitetura de armazenamento HDInsight'](./media/overview-azure-storage/storage-architecture.png "Arquitetura de armazenamento HDInsight")

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Através do HDInsight, você também pode acessar dados no Azure Storage. A sintaxe é mostrada a seguir:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Considere os seguintes princípios ao usar uma conta de Armazenamento do Azure com clusters do HDInsight:

* **Contêineres nas contas de armazenamento que estão conectadas a um cluster:** como o nome e a chave da conta são associados ao cluster durante a criação, você tem acesso completo aos blobs nesses contêineres.

* **Contêineres públicos ou blobs públicos em contas de armazenamento que não estão conectados a um cluster:** Você tem permissão apenas para ler as bolhas nos recipientes.
  
  > [!NOTE]  
  > Os contêineres públicos permitem obter uma lista de todos os blobs disponíveis nesse contêiner e os metadados do contêiner. Um blob público somente permite acessar os blobs se você souber a URL exata. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Contêineres privados em contas de armazenamento que não estão conectados a um cluster:** Você não pode acessar as bolhas nos contêineres a menos que você defina a conta de armazenamento quando você enviar os trabalhos do WebHCat.

As contas de armazenamento definidas no processo de criação e suas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. Por padrão, o HDInsight usa as contas de armazenamento definidas no arquivo core-site.xml. Você pode modificar essa configuração usando o [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos do WebHCat, incluindo a Colmeia Apache. E MapReduce, o streaming Apache Hadoop e o Apache Pig, carregam uma descrição de contas de armazenamento e metadados. (Este aspecto é atualmente verdadeiro para pig com contas de armazenamento, mas não para metadados.) Para obter mais informações, [consulte Usando um cluster HDInsight com contas de armazenamento e metalojas alternativas.](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)

Os blobs podem ser usados para dados estruturados e não estruturados. Os contêineres de blobs armazenam dados como pares de chave/valor e não possuem nenhuma hierarquia de diretório. No entanto, o nome da chave pode incluir o caractere de barra ( / ) para parecer que o arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser `input/log1.txt`. Não existe nenhum diretório `input`, mas por causa do caractere de barra no nome da chave, a chave se parece com um caminho de arquivo.

## <a name="benefits-of-azure-storage"></a>Benefícios do Armazenamento do Azure

Os clusters de cálculo e os recursos de armazenamento que não são colocados acarretam custos de desempenho. Esses custos são reduzidos dependendo da maneira que os clusters de cálculo são criados próximos aos recursos de conta de armazenamento na região do Azure. Nessa região, os nós de computação podem acessar com eficiência os dados na rede de alta velocidade dentro do Armazenamento do Azure.

Quando armazena os dados no armazenamento do Azure em vez de no HDFS, você obtém vários benefícios:

* **Compartilhamento e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Por outro lado, os dados no Armazenamento do Azure podem ser acessados por meio das APIs do HDFS ou por meio das APIs REST do Armazenamento de Blobs. Em razão dessa organização, um conjunto maior de aplicativos (incluindo outros clusters do HDInsight) e ferramentas pode ser usado para produzir e consumir os dados.

* **Arquivamento de dados:** Quando os dados são armazenados no Azure Storage, os clusters HDInsight usados para computação podem ser excluídos com segurança sem perder os dados do usuário.

* **Custo de armazenamento de dados:** Armazenar dados no DFS a longo prazo é mais caro do que armazenar os dados no Azure Storage. Porque o custo de um cluster de computação é maior do que o custo do Armazenamento Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de cálculo, você estará economizando também em custos de carregamento de dados.

* **Escala horizontal elástica:** embora o HDFS forneça um sistema de arquivos escalado horizontalmente, a escala é determinada pelo número de nós que você cria para o seu cluster. Alterar a escala pode ser mais complicado do que os recursos de dimensionamento elástico que você recebe automaticamente no Azure Storage.

* **Geo-replicação:** Seu Armazenamento Azure pode ser replicado geografamente. Embora a replicação geográfica forneça recuperação geográfica e redundância de dados, um failover para a localização com replicação geográfica afetará seriamente o desempenho e poderá gerar custos adicionais. Portanto, escolha a replicação geográfica com cuidado e somente se o valor dos dados justificar o custo adicional.

Determinados trabalhos e pacotes do MapReduce podem criar resultados intermediários que talvez você não queira realmente armazenar no contêiner de Armazenamento do Azure. Nesse caso, é possível ainda optar por armazenar os dados no HDFS local. O HDInsight usa o DFS para vários desses resultados intermediários em trabalhos do Hive e outros processos.

> [!NOTE]  
> A maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) ainda funciona conforme o esperado no Armazenamento do Azure. Apenas os comandos específicos à implementação nativa do HDFS (que é conhecida como DFS), como `fschk` e `dfsadmin`, apresentam um comportamento diferente no Armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Visão geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)