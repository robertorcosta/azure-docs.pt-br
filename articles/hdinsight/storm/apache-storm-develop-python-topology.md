---
title: Tempestade Apache com componentes Python - Azure HDInsight
description: Saiba como criar uma topologia apache storm que usa componentes Python no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460017"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias do Apache Storm usando o Python no HDInsight

Saiba como criar uma topologia [Apache Storm](https://storm.apache.org/) que usa componentes de Python. O Apache Storm dá suporte a várias linguagens, permitindo até a combinação de componentes de várias linguagens em uma topologia. A estrutura de [Fluxo](https://storm.apache.org/releases/current/flux.html) (introduzida com o Storm 0.10.0) permite que você crie facilmente soluções que usam componentes do Python.

> [!IMPORTANT]  
> As informações neste documento foram testadas usando o Storm no HDInsight 3.6.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Storm no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** como **Tipo de cluster**.

* Um ambiente local de desenvolvimento de tempestades (Opcional). Um ambiente local do Storm só será necessário se você quiser executar a topologia localmente. Para obter mais informações, consulte [Configurar um ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2.7 ou superior](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.

## <a name="storm-multi-language-support"></a>Suporte a várias linguagens no Storm

O Apache Storm foi projetado para trabalhar com componentes escritos usando qualquer linguagem de programação. Os componentes devem entender como trabalhar com a definição do Thrift para Storm. Para o Python, é fornecido um módulo como parte do projeto do Apache Storm que permite interagir facilmente com o Storm. Você pode encontrar [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)este módulo em .

O Storm é um processo Java que é executado na máquina virtual Java (JVM). Componentes escritos em outras linguagens são executados como subprocessos. O Storm comunica-se com esses subprocessos usando mensagens JSON enviadas por stdin/stdout. Mais detalhes sobre a comunicação entre os componentes podem ser encontrados na documentação sobre [Protocolo de várias linguagens](https://storm.apache.org/releases/current/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python com a estrutura do Flux

A estrutura do Flux permite que você defina topologias Storm separadamente dos componentes. A estrutura do Flux usa YAML para definir a topologia Storm. O texto a seguir é um exemplo de um componente do Python é referenciado no documento YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

A classe `FluxShellSpout` é usada para iniciar o script `sentencespout.py` que implementa o spout.

O Flux espera que os scripts de Python estejam no diretório `/resources`, dentro do arquivo jar que contém a topologia. Assim, este exemplo armazena os scripts do Python no diretório `/multilang/resources`. O `pom.xml` inclui esse arquivo usando o XML a seguir:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Como mencionado anteriormente, `storm.py` há um arquivo que implementa a definição de Thrift para Tempestade. A estrutura do Flux inclui o `storm.py` automaticamente quando o projeto é compilado, portanto, você não precisa se preocupar em incluí-lo.

## <a name="build-the-project"></a>Compilar o projeto

1. Baixe o [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)projeto de .

1. Abra um prompt de comando e `hdinsight-python-storm-wordcount-master`navegue até a raiz do projeto: . Insira o seguinte comando:

    ```cmd
    mvn clean compile package
    ```

    Esse comando cria um arquivo `target/WordCount-1.0-SNAPSHOT.jar` que contém a topologia compilada.

## <a name="run-the-storm-topology-on-hdinsight"></a>Executar a topologia Storm no HDInsight

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para copiar o arquivo para o `WordCount-1.0-SNAPSHOT.jar` seu cluster Storm no HDInsight. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Depois que o arquivo for carregado, conecte-se ao cluster usando SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Na sessão de SSH, use o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Após ser iniciada, uma topologia do Storm é executada até ser interrompida.

1. Use a UI da tempestade para ver a topologia no cluster. A interface do usuário do Storm está localizada em `https://CLUSTERNAME.azurehdinsight.net/stormui`. Substitua `CLUSTERNAME` pelo nome do cluster.

1. Pare a topologia da tempestade. Use o seguinte comando para parar a topologia no cluster:

    ```bash
    storm kill wordcount
    ```

    Alternativamente, você pode usar a UI tempestade. Em **ações de Topologia** para a topologia, selecione **Kill**.

## <a name="run-the-topology-locally"></a>Executar a topologia localmente

Para executar a topologia localmente, use o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Esse comando requer um ambiente de desenvolvimento local do Storm. Para obter mais informações, consulte [Configurar um ambiente de desenvolvimento](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

Uma vez que a topologia é iniciada, ela emite informações para o console local semelhantes ao texto a seguir:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Para interromper a topologia, use __Ctrl+C__.

## <a name="next-steps"></a>Próximas etapas

Veja os documentos a seguir para outras maneiras de usar python com HDInsight: [Como usar As Funções Definidas pelo Usuário Python (UDF) em Apache Pig e Apache Hive](../hadoop/python-udf-hdinsight.md).
