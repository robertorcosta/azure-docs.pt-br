---
title: Gerar recomendações usando o Apache Mahout no Azure HDInsight
description: Saiba como usar a biblioteca de aprendizado de máquina do Apache Mahout para gerar recomendações de vídeos com o HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: 33110e9f1d45fcd11e5f4cad1b589ab929a9472d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767629"
---
# <a name="generate-movie-recommendations-using-apache-mahout-with-apache-hadoop-in-hdinsight-ssh"></a>Gerar recomendações de filmes usando Apache Mahout com Apache Hadoop no HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca de aprendizado de máquina do [Apache Mahout](https://mahout.apache.org) com o Azure HDInsight para gerar recomendações de vídeos.

O Mahout é uma biblioteca de [machine learning](https://en.wikipedia.org/wiki/Machine_learning) para o Apache Hadoop. O Mahout contém algoritmos para processamento de dados, como filtragem, classificação e clustering. Neste artigo, você utiliza um mecanismo de recomendação para gerar recomendações de filmes baseadas nos vídeos que seus amigos assistiram.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="apache-mahout-versioning"></a>Controle de versão do Apache Mahout

Para obter mais informações sobre a versão do Mahout no cluster HDInsight, confira [Versões do HDInsight e componentes do Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="understanding-recommendations"></a>Entendendo as recomendações

Uma das funções oferecidas pelo Mahout é um mecanismo de recomendação. Esse mecanismo aceita dados no formato de `userID`, `itemId` e `prefValue` (o usuário escolhe o item de sua preferência). O Mahout, então, pode realizar análises de coocorrência, para determinar que *usuários que têm preferência por um item também têm preferência por esses outros itens*. O Mahout determinará, então, usuários com preferências de item similares, que podem ser utilizadas para fazer recomendações.

O fluxo de trabalho a seguir é um exemplo simplificado que usa dados de filmes:

* **Co-ocorrência**: Joe, Alice e Bob todos gostaram *de Star Wars*, O Império *Contra-Ataca*, e *O Retorno de Jedi*. O Mahout determina que usuários que gostam de qualquer um desses filmes também gostam dos outros dois.

* **Co-ocorrência**: Bob e Alice também gostaram de *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*. O Mahout determina que usuários que gostam de qualquer um dos três filmes citados anteriormente também gostam destes últimos três filmes.

* **Recomendação de similaridade**: Como Joe gostou dos três primeiros filmes, Mahout olha para filmes que outros com preferências semelhantes gostaram, mas Joe não assistiu (gostei/avaliado). Nesse caso, o Mahout recomendaria *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*.

### <a name="understanding-the-data"></a>Compreendendo os dados

Convenientemente, a [GroupLens Research](https://grouplens.org/datasets/movielens/) oferece dados de classificação para filmes em um formato compatível com o Mahout. Esses dados estão disponíveis no armazenamento padrão do cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois arquivos, `moviedb.txt` e `user-ratings.txt`. O arquivo `user-ratings.txt` é usado durante a análise. O `moviedb.txt` é usado para fornecer informações de texto fáceis e simples ao exibir os resultados.

Os dados `user-ratings.txt` contidos têm `userID` `movieID`uma `userRating`estrutura `timestamp`de , e , o que indica o quão altamente cada usuário classificou um filme. Aqui está um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Executar a análise

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Use o seguinte comando para executar o trabalho de recomendação:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> O trabalho pode levar vários minutos para ser concluído e pode executar vários trabalhos do MapReduce.

## <a name="view-the-output"></a>Exibir a saída

1. Quando o trabalho for concluído, use o seguinte comando para exibir a saída gerada:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    A saída é exibida da seguinte maneira:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    A primeira coluna é a `userID`. Os valores contidos em '[' and ']' são `movieId`:`recommendationScore`.

2. Você pode usar a saída juntamente com o moviedb.txt para fornecer mais informações sobre as recomendações. Primeiro, copie os arquivos localmente usando os seguintes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Esse comando copiará os dados de saída em um arquivo chamado **recommendations.txt** no diretório atual, juntamente com os arquivos de dados de filme.

3. Use o comando a seguir para criar um script Python que pesquise nomes de filmes para os dados na saída de recomendações:

    ```bash
    nano show_recommendations.py
    ```

    Quando o editor for aberto, use o texto a seguir como conteúdo do arquivo:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Pressione **Ctrl-X**, **Y** e finalmente **Enter** para salvar os dados.

4. Execute o script Python. O comando a seguir pressupõe que você esteja no diretório em que todos os arquivos foram baixados:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Esse comando examina as recomendações geradas para o usuário ID 4.

   * O arquivo **user-ratings.txt** é usado para recuperar filmes que foram classificados.

   * O arquivo **moviedb.txt** é usado para recuperar os nomes dos filmes.

   * As **recomendações.txt** são usadas para recuperar as recomendações do filme para este usuário.

     A saída desse comando deve ser semelhante a este texto:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Excluir dados temporários

Os trabalhos mahout não removem dados temporários que são criados durante o processamento do trabalho. O parâmetro `--tempDir` é especificado no trabalho de exemplo para isolar os arquivos temporários em um caminho específico para fácil exclusão. Para remover os arquivos temporários, use o seguinte comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Se você quiser executar o comando novamente, você também deve excluir o diretório de saída. Use o seguinte para excluir esse diretório:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o Mahout, descubra outras formas de trabalhar com dados no HDInsight:

* [Apache Hive com HDInsight](hdinsight-use-hive.md)
* [MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
