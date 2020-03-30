---
title: Conectar o Excel ao Apache Hadoop com o Power Query - Azure HDInsight
description: Saiba como aproveitar os componentes de business intelligence e usar o Power Query para Excel para acessar dados armazenados no Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435796"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Conectar o Excel ao Apache Hadoop com o Power Query

Um dos principais recursos da solução de big data da Microsoft é a integração dos componentes de BI (business intelligence) da Microsoft com clusters Apache Hadoop no Azure HDInsight. Um dos principais exemplos é a capacidade de conectar o Excel com a conta de Armazenamento do Azure, que contém os dados associados ao seu cluster Hadoop usando o suplemento Microsoft Power Query para Excel. Este artigo explica como configurar e usar o Power Query para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho que está executando o Windows 10, 7, O Windows Server 2008 R2 ou um sistema operacional posterior.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 autônomo ou Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Instale a consulta de energia do Microsoft

O Power Query pode importar dados que foram retornados ou que foram gerados por um trabalho Hadoop em execução em um cluster HDInsight.

No Excel 2016, o Power Query foi integrado na faixa de opções Dados na seção Obter e Transformar. Para versões mais antigas do Excel, baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) e instale-o.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel

O suplemento do Power Query para Excel facilita a importação de dados de seu cluster HDInsight para o Excel onde ferramentas de BI, como o PowerPivot e o Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

1. Lançar excel.

1. Crie uma nova pasta de trabalho em branco.

1. Execute as etapas a seguir com base na versão do Excel:

   * Excel 2016

     * Selecione > **dados** > **obtenham dados** > do**Azure** > **do Azure HDInsight (HDFS).**

       ![Hdi. PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Selecione **consulta de energia do** > **Azure** > do Microsoft**Azure HDInsight**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Nota:** Se você não ver o menu **Consulta de energia,** vá para**Add-ins****opções** > de **arquivos** > e selecione **Add-ins COM** na caixa **de gerenciamento** suspenso na parte inferior da página. Selecione o botão **Ir...** e verifique se a caixa do suplemento Power Query para Excel está marcada.

       **Nota:** A consulta de energia também permite importar dados do HDFS selecionando **De outras fontes**.

1. Na caixa de diálogo **Azure HDInsight (HDFS),** no nome da conta ou na caixa de texto **url,** digite o nome da conta de armazenamento Azure Blob associada ao seu cluster. Em seguida, selecione **OK**. Essa conta pode ser a conta de armazenamento padrão ou uma conta de armazenamento vinculada.  O formato é `https://StorageAccountName.blob.core.windows.net/`.

1. Em **For Account Key**, digite a chave para a conta de armazenamento Blob e, em seguida, selecione **Conectar**. (Você precisa inserir as informações da conta somente na primeira vez que acessar este repositório.)

1. No painel **Navegador** à esquerda do Editor de Consulta, clique duas vezes no nome do contêiner de armazenamento Blob associado ao seu cluster. Por padrão, o nome do contêiner é igual ao nome do cluster.

1. Localize **hiveSampleData.txt** na coluna **Nome** (o caminho da pasta é **.. /hive/warehouse/hivesampletable/**), e, em seguida, selecione **Binário** à esquerda de HiveSampleData.txt. HiveSampleData.txt acompanha todo o cluster. Se desejar, você pode usar seu próprio arquivo.

    ![Dados de importação de energia do HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Se desejar, você pode renomear os nomes das colunas. Quando estiver pronto, selecione **'Fechar &' Carregar**.  Os dados foram carregados em sua pasta de trabalho:

    ![Tabela importada de consulta de energia Do HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar o Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, você pode recuperar dados do HDInsight no banco de dados SQL do Azure. Também é possível carregar dados no HDInsight. Para saber mais, leia os seguintes artigos:

* [Visualize os dados da Colmeia Apache com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados da Colmeia de Consulta Interativa com power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar consultas da Colmeia Apache no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregue os dados no HDInsight](./../hdinsight-upload-data.md).
