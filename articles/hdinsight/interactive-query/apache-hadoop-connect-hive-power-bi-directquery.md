---
title: Veja dados da Colmeia de Consulta Interativa com power bi no Azure HDInsight
description: Usar o Microsoft Power BI para visualizar dados da consulta interativa do Hive do Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367985"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualize dados do Apache Hive de consulta interativa com o Microsoft Power BI usando consulta direta no HDInsight

Este artigo descreve como conectar o Microsoft Power BI a clusters da consulta interativa do Azure HDInsight e visualizar os dados do Apache Hive usando a consulta direta. O exemplo fornecido carrega os `hivesampletable` dados de uma tabela Colmeia para power BI. A `hivesampletable` tabela Hive contém alguns dados de uso do telefone celular. Em seguida, você cria gráficos com os dados de uso em um mapa mundial:

![Relatório de mapa de Power BI do HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Você pode aproveitar o [Driver ODBC do Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) para fazer a importação por meio do conector ODBC genérico no Power BI Desktop. No entanto, não é recomendável para cargas de trabalho de BI considerando a natureza não interativo do mecanismo de consulta do Hive. [Os conectores do HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) e do conector do [HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) são opções melhores para seu desempenho.

## <a name="prerequisites"></a>Pré-requisitos
Antes de prosseguir com este artigo, você deve ter os seguintes itens:

* **Cluster HDInsight**. O cluster pode ser um cluster HDInsight com o Apache Hive ou um cluster recém-lançado de Consulta Interativa. Para a criação de clusters, consulte [Criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Você pode baixar uma cópia do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A `hivesampletable` tabela Hive vem com todos os clusters HDInsight.

1. Inicie o Power BI Desktop.

2. Na barra de menus, navegue até **Home** > **Get Data** > **More...**.

    ![HDInsight Power BI Obter mais dados](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Na janela **Obter dados,** digite **hdinsight** na caixa de pesquisa.  

4. A partir dos resultados da pesquisa, selecione **hdInsight Interactive Query**e selecione **Conectar**.  Se você não ver **o HDInsight Interactive Query,** você precisa atualizar seu Power BI Desktop para a versão mais recente.

5. Selecione **Continuar** para fechar a conexão a uma caixa de diálogo **de serviço de terceiros.**

6. Na janela **hdInsight Interactive Query,** digite as seguintes informações e selecione **OK**:

    |Propriedade | Valor |
    |---|---|
    |Servidor |Digite o nome do cluster, por exemplo *myiqcluster.azurehdinsight.net*.|
    |Banco de dados |Digite **o padrão** deste artigo.|
    |Modo de Conectividade de Dados |Selecione **DirectQuery** para este artigo.|

    ![Consulta interativa do HDInsight, conexão com o DirectQuery do Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Digite as credenciais HTTP e selecione **Conectar**. O nome de usuário padrão é **admin**.

8. Na janela **Do Navegador** no painel esquerdo, **selecione hivesampletale**.

9. Selecione **Carregar** na janela principal.

    ![Consulta interativa do HDInsight, hivesampletable do Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizar dados em um mapa

Continue do último procedimento.

1. No painel Visualizações, selecione **Mapa**, o ícone do globo. Um mapa genérico aparece em seguida na janela principal.

    ![O Power BI do HDInsight personaliza o relatório](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. No painel Campos, selecione **país** e **devicemake**. Um mapa-múndi com os pontos de dados aparece na janela principal após alguns momentos.

3. Expanda o mapa.

## <a name="next-steps"></a>Próximas etapas
Este artigo, você aprendeu como visualizar dados do HDInsight usando o Microsoft Power BI.  Para obter mais informações sobre visualização de dados, consulte os seguintes artigos:

* [Visualize os dados da Colméia Apache com o Microsoft Power BI usando o ODBC no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Use o Apache Zeppelin para executar consultas da Colmeia Apache no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando o Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Faça upload de dados para hdinsight](./../hdinsight-upload-data.md).
