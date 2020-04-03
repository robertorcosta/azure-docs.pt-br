---
title: Gateway mergulho profundo e práticas recomendadas para colmeia apache no Azure HDInsight
description: Saiba como navegar pelas práticas recomendadas para executar consultas da Colmeia no gateway Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586971"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Gateway mergulho profundo e práticas recomendadas para colmeia apache no Azure HDInsight

O gateway Azure HDInsight (Gateway) é o frontend HTTPS para clusters HDInsight. O Gateway é responsável por: autenticação, resolução do host, descoberta de serviços e outros recursos úteis necessários para um sistema distribuído moderno. Os recursos fornecidos pelo Gateway resultam em algumas despesas gerais para as quais este documento descreverá as práticas recomendadas para navegar. Técnicas de solução de problemas do Gateway também são discutidas.

## <a name="the-hdinsight-gateway"></a>O gateway HDInsight

O gateway HDInsight é a única parte de um cluster HDInsight que é acessível publicamente pela internet. O serviço Gateway pode ser acessado sem passar `clustername-int.azurehdinsight.net` pela internet pública usando o ponto final do gateway interno. O ponto final do gateway interno permite que as conexões sejam estabelecidas no serviço de gateway sem sair da rede virtual do cluster. O Gateway lida com todas as solicitações enviadas para o cluster e encaminha essas solicitações para os componentes corretos e hosts de cluster.

O diagrama a seguir fornece uma ilustração aproximada de como o Gateway fornece uma abstração na frente de todas as diferentes possibilidades de resolução do host dentro do HDInsight.

![Diagrama de resolução do host](./media/gateway-best-practices/host-resolution-diagram.png "Diagrama de resolução do host")

## <a name="motivation"></a>Motivação

A motivação para colocar um gateway na frente dos clusters HDInsight é fornecer uma interface para a descoberta de serviços e autenticação do usuário. Os mecanismos de autenticação fornecidos pelo gateway são especialmente relevantes para clusters habilitados para ESP.

Para a descoberta de serviços, a vantagem do gateway é que cada componente dentro `clustername.azurehdinsight.net/hive2`do cluster pode `host:port` ser acessado como ponto final diferente sob o site do Gateway ( ), em oposição a uma infinidade de pares.

Para autenticação, o Gateway permite que `username:password` os usuários se autentiquem usando um par de credenciais. Para clusters habilitados para ESP, essa credencial seria o nome de usuário e senha do usuário. A autenticação em clusters HDInsight através do Gateway não exige que o cliente adquira um bilhete kerberos. Uma vez que `username:password` o Gateway aceita credenciais e adquire o bilhete Kerberos do usuário em nome do usuário, conexões seguras podem ser feitas ao Gateway de qualquer host cliente, incluindo clientes unidos a diferentes domínios AA-DDS do que o cluster (ESP).

## <a name="best-practices"></a>Práticas recomendadas

O Gateway é um único serviço (carga equilibrada em dois hosts) responsável pela solicitação de encaminhamento e autenticação. O Gateway pode se tornar um gargalo de throughput para consultas de Colmeia que excedam um determinado tamanho. A degradação do desempenho da consulta pode ser observada quando consultas **SELECT** muito grandes são executadas no Gateway via ODBC ou JDBC. "Muito grande" significa consultas que compõem mais de 5 GB de dados em linhas ou colunas. Esta consulta pode incluir uma longa lista de linhas e, ou uma ampla contagem de colunas.

A degradação de desempenho do Gateway em torno de consultas de grande porte é porque os dados devem ser transferidos do armazenamento de dados subjacente (ADLS Gen2) para: o HDInsight Hive Server, o Gateway e, finalmente, através dos drivers JDBC ou ODBC para o host cliente.

O diagrama a seguir ilustra as etapas envolvidas em uma consulta SELECT.

![Diagrama de resultados](./media/gateway-best-practices/result-retrieval-diagram.png "Diagrama de resultados")

Apache Hive é uma abstração relacional em cima de um sistema de arquivos compatível com HDFS. Esta abstração significa instruções **SELECT** na Colmeia correspondem às operações **READ** no sistema de arquivos. As operações **READ** são traduzidas no esquema apropriado antes de relatadoao usuário. A latência desse processo aumenta com o tamanho dos dados e o total de saltos necessários para chegar ao usuário final.

Comportamentos semelhantes podem ocorrer ao executar instruções **CREATE** ou **INSERT** de dados grandes, pois esses comandos corresponderão às operações **WRITE** no sistema de arquivos subjacente. Considere escrever dados, como orc bruto, para o sistema de arquivos/datalake em vez de carregá-los usando **INSERT** ou **LOAD**.

Em clusters habilitados para Enterprise Security Pack, políticas apache ranger suficientemente complexas podem causar uma desaceleração no tempo de compilação de consultas, o que pode levar a um tempo de intervalo de gateway. Se um tempo de intervalo de gateway for notado em um cluster ESP, considere reduzir ou combinar o número de políticas de ranger.

## <a name="troubleshooting-techniques"></a>Técnicas de solução de problemas

Existem vários locais para mitigar e entender questões de desempenho atendidas como parte do comportamento acima. Use a seguinte lista de verificação ao experimentar a degradação do desempenho da consulta no gateway HDInsight:

* Use a cláusula **LIMITE** ao executar grandes consultas **SELECT.** A cláusula **LIMITE** reduzirá o total de linhas relatadas ao host cliente. A cláusula **LIMITE** afeta apenas a geração de resultados e não altera o plano de consulta. Para aplicar a cláusula **LIMITE** ao plano `hive.limit.optimize.enable`de consulta, use a configuração . **Limit** pode ser combinado com um deslocamento usando o formulário de argumento **LIMITE x,y**.

* Nomeie suas colunas de interesse ao executar consultas **SELECT** em vez de usar **SELECT \* **. Selecionar menos colunas diminuirá a quantidade de dados lidos.

* Tente executar a consulta de interesse através de Beeline Apache. Se a recuperação de resultados via Apache Beeline levar um longo período de tempo, espere atrasos ao recuperar os mesmos resultados através de ferramentas externas.

* Teste uma consulta básica da Colmeia para garantir que uma conexão com o HDInsight Gateway possa ser estabelecida. Tente executar uma consulta básica de duas ou mais ferramentas externas para se certificar de que nenhuma ferramenta individual está tendo problemas.

* Revise quaisquer alertas Apache Ambari para garantir que os serviços HDInsight sejam saudáveis. Os Alertas Ambari podem ser integrados ao Azure Monitor para relatórios e análises.

* Se você estiver usando uma Metastore de Colmeia externa, verifique se o DTU do Azure SQL DB para o Metastore da Colmeia não atingiu seu limite. Se o DTU estiver se aproximando do seu limite, você precisará aumentar o tamanho do banco de dados.

* Certifique-se de que quaisquer ferramentas de terceiros, como PBI ou Tableau, estejam usando paginação para visualizar tabelas ou bancos de dados. Consulte seus parceiros de suporte para obter essas ferramentas para assistência na paginação. A principal ferramenta utilizada para paginação `fetchSize` é o parâmetro JDBC. Um pequeno tamanho de busca pode resultar em desempenho de gateway degradado, mas um tamanho de busca muito grande pode resultar em um tempo limite de gateway. A sintonia do tamanho da busca deve ser feita em uma base de carga de trabalho.

* Se o seu pipeline de dados envolve a leitura de uma grande quantidade de dados do armazenamento subjacente do cluster HDInsight, considere usar uma ferramenta que interfacediretamente com o Azure Storage, como o Azure Data Factory

* Considere usar o Apache Hive LLAP ao executar cargas de trabalho interativas, pois o LLAP pode fornecer uma experiência mais suave para o retorno rápido dos resultados da consulta

* Considere aumentar o número de threads disponíveis para `hive.server2.thrift.max.worker.threads`o serviço Hive Metastore usando . Esta configuração é especialmente relevante quando um alto número de usuários simultâneos estão enviando consultas para o cluster

* Reduza o número de repetições usadas para alcançar o Gateway a partir de quaisquer ferramentas externas. Se várias tentativas forem usadas, considere seguir uma política de retentativa exponencial

* Considere ativar a colmeia de `hive.exec.compress.output` compressão usando as configurações e `hive.exec.compress.intermediate`.

## <a name="next-steps"></a>Próximas etapas

* [Beeline Apache no HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Etapas de solução de problemas do hdinsight gateway](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Redes Virtuais para HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight com Rota Expressa](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)