---
title: Instalar e usar o O giraph no Azure HDInsight
description: Saiba como instalar o O giraph em clusters HDInsight usando ações de script. Você pode usar O giraph para fazer o processamento de grafo no Apache Hadoop na nuvem do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/26/2019
ms.openlocfilehash: 1f6fd88ec492f26f6819dff099ec8fe53364ba0b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552246"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar o Apache Giraph nos clusters Hadoop do HDInsight e usar o Giraph para processar grafos em grande escala

Saiba como instalar o Apache Giraph em um cluster HDInsight. O recurso de ação de script do HDInsight permite que você personalize o cluster executando um script de bash. Scripts podem ser usados para personalizar os clusters durante e após a criação do cluster.

## <a name="what-is-giraph"></a>O que é O giraph

O [Apache Giraph](https://giraph.apache.org/) permite executar processamento de grafos usando o Hadoop e pode ser usado com o Azure HDInsight. Grafos moldam as relações entre objetos. Por exemplo, as conexões entre roteadores em uma rede grande como a Internet ou relações entre pessoas em redes sociais. O processamento de grafos permite que você faça a análise das relações entre objetos em um grafo, como:

* Identificar possíveis amigos com base em suas relações atuais.

* Identificar a menor rota entre dois computadores em uma rede.

* Calcular a ordem de classificação de página da Web.

> [!WARNING]  
> Há suporte total a componentes fornecidos com o cluster do HDInsight – o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados, como o Giraph, recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O Suporte da Microsoft pode ser capaz de resolver o problema. Caso contrário, você deve consultar comunidades de software livre em que é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos do Apache têm sites de projeto em [https://apache.org](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).

## <a name="what-the-script-does"></a>O que o script faz

O script executa as ações a seguir:

* Instala o O giraph para `/usr/hdp/current/giraph`.

* Copia o arquivo de `giraph-examples.jar` para o armazenamento padrão (WASB) para o cluster: `/example/jars/giraph-examples.jar`.

## <a name="install-giraph-using-script-actions"></a>Instalar o O giraph usando ações de script

Um script de exemplo para instalar o O giraph em um cluster HDInsight está disponível em `https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`

Esta seção fornece instruções sobre como usar o exemplo de script durante a criação do cluster usando o Portal do Azure.

> [!NOTE]  
> As ações de script podem ser aplicadas por meio dos seguintes métodos:
> * Azure PowerShell
> * A CLI do Azure
> * O SDK .NET do HDInsight
> * Modelos do Azure Resource Manager
> 
> Também é possível aplicar ações de script a clusters que já estão em execução. Para saber mais, veja [Personalizar clusters HDInsight com as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Comece a criar um cluster usando as etapas em [Criar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md), mas não conclua a criação. Você precisará usar a **experiência de criação clássica** e **personalizada (tamanho, configurações, aplicativos)** .

1. Na seção **tamanho do cluster** , verifique se o **número de nós de trabalho** é pelo menos 2, para este exemplo.

1. Na seção **ações de script** , forneça as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Nome|Instalar o Giraph|
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`|
    |Tipo(s) de nó|de Cabeçalho|
    |Parâmetros|Deixar em branco|

    Para obter mais informações, consulte [usar uma ação de script durante a criação do cluster](./hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation).

1. Continue a criação do cluster conforme descrito em [Criar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="how-do-i-use-giraph-in-hdinsight"></a>Como fazer usar o O giraph no HDInsight?

Quando o cluster tiver sido criado, use as etapas a seguir para executar o exemplo SimpleShortestPathsComputation incluído com o Giraph. Este exemplo usa a implementação do [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) básico para encontrar o caminho mais curto entre objetos em um grafo.

1. Use o [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Use o comando a seguir para criar um novo arquivo chamado **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Use o seguinte texto como o conteúdo deste arquivo:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Esses dados descrevem uma relação entre objetos em um grafo direcionado, usando o formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Cada linha representa uma relação entre um objeto `source_id` e um ou mais objetos `dest_id`. O `edge_value` pode ser considerado a força ou a distância da conexão entre `source_id` e `dest\_id`.

    Desenhados e utilizando o valor (ou peso) como distância entre os objetos, os dados acima podem se parecer com o diagrama a seguir:

    ![tiny_graph.txt Desenhado como círculos com linhas de distância variável entre](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Para salvar o arquivo, use **Ctrl + X**, em seguida, **Y** e, finalmente, **Enter** para aceitar o nome de arquivo.

4. Use o seguinte para armazenar os dados no armazenamento primário para o cluster HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Execute o exemplo SimpleShortestPathsComputation usando o comando a seguir:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    > [!IMPORTANT]
    > O valor passado para `-w` deve ser menor ou igual ao número real de nós de trabalho.

    Os parâmetros usados com este comando são descritos na tabela a seguir:

   | Parâmetro | O que ele faz |
   | --- | --- |
   | vidro |O arquivo jar que contém os exemplos. |
   | org. Apache. o giraph. GiraphRunner |A classe usada para iniciar os exemplos. |
   | org. Apache. o giraph. examples. SimpleShortestPathsComputation |O exemplo usado. Nesse exemplo, ele calcula o caminho mais curto entre a ID 1 e todas as outras IDs no grafo. |
   | -CA mapred. Job. Tracker |O nó de cabeçalho do cluster. |
   | -vif |O formato de entrada a ser usado para os dados de entrada. |
   | -VIP |O arquivo de dados de entrada. |
   | -vof |O formato de saída. Nesse exemplo, a ID e o valor como texto sem formatação. |
   | -Op |O local de saída. |
   | -w 2 |O número de trabalhos a usar. Neste exemplo, 2. |

    Para obter mais informações sobre esses e outros parâmetros usados com exemplos do Giraph, consulte o [Guia de início rápido do Giraph](https://giraph.apache.org/quick_start.html).

6. Depois que o trabalho for concluído, os resultados serão armazenados no diretório **/example/output/shortestpaths** . Os nomes de arquivo de saída começam com **Part-m-** e terminam com um número que indica o primeiro, o segundo, e assim por diante, arquivo. Use o comando a seguir para exibir a saída:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A saída é semelhante ao texto a seguir:

    ```output
    0    1.0
    4    5.0
    2    2.0
    1    0.0
    3    1.0
    ```

    O exemplo SimpleShortestPathComputation está codificado para começar com a ID do objeto 1 e localizar o caminho mais curto para os outros objetos. A saída está no formato de `destination_id` e `distance`. `distance` é o valor (ou peso) das bordas percorridas entre a ID do objeto 1 e a ID de destino.

    Visualizando esses dados, você pode verificar os resultados percorrendo os caminhos mais curtos entre a ID 1 e todos os outros objetos. Observe que o caminho mais curto entre a ID 1 e a ID 4 é 5. Esse valor é a distância total entre a ID 1 e 3 e, em seguida, a ID 3 e 4.

    ![Desenho dos objetos como círculos com os percursos mais curtos entre](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Próximos passos

[Instalar e usar matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md).
