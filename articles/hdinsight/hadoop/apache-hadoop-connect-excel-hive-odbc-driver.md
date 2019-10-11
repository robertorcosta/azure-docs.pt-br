---
title: Conecte o Excel ao Apache Hadoop com o driver ODBC do Hive - Azure HDInsight
description: Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em clusters HDInsight no Microsoft Excel.
keywords: hadoop excel,hive excel,hive odbc
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 7451eaf56a2466bbb02fa879008b4a9787f6c2f5
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264626"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conecte o Excel ao Apache Hadoop no HDInsight do Azure com o driver ODBC do Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução de Big Data da Microsoft integra componentes do Microsoft Business Intelligence (BI) com os clusters do Apache Hadoop que foram implantados no Azure HDInsight. Um exemplo dessa integração é a capacidade de conectar o Excel ao data warehouse do Hive de um cluster Hadoop no HDInsight usando o driver ODBC do Microsoft Hive.

Também é possível conectar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters Hadoop (não HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar Power Query, consulte [conectar o Excel ao HDInsight com o Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes itens:

* Cluster Hadoop do HDInsight. Para criar um, confira [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho com o Office 2010 Professional Plus ou posterior ou o Excel 2010 ou posterior.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive

Baixe e instale a versão [driver ODBC do Microsoft Hive](https://go.microsoft.com/fwlink/?LinkID=286698) que corresponde à versão do aplicativo em que você usará o driver ODBC.  Para este artigo, o driver é usado para o Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados ODBC do Apache Hive

As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1. No Windows, navegue até Iniciar > Ferramentas Administrativas do Windows > Fontes de Dados ODBC (32 bits)/(64 bits).  Isso abre a janela **Administrador de Fonte de Dados ODBC**.

    ![Administrador de fonte de dados ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configurar um DSN usando o Administrador de Fonte de Dados ODBC")

1. Na guia **DSN de Usuário**, selecione **Adicionar** para abrir a janela **Criar Nova Fonte de Dados**.

1. Selecione **Driver ODBC do Microsoft Hive** e selecione **Concluir** para abrir a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.

1. Digite ou selecione os valores a seguir:

   | Propriedade | DESCRIÇÃO |
   | --- | --- |
   |  Nome da fonte de dados |Forneça um nome para a sua fonte de dados |
   |  Hosts |Digite `HDInsightClusterName.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Porta |Use **443**. (Essa porta foi alterada de 563 para 443.) |
   |  Banco de Dados |Use **padrão**. |
   |  Mecanismo |Selecione **Serviço do Microsoft Azure HDInsight** |
   |  Nome do Usuário |Insira o nome de usuário HTTP do cluster HDInsight. O nome de usuário padrão é **admin**. |
   |  Senha |Insira a senha do usuário do cluster HDInsight. Marque a caixa de seleção **Salvar Senha (Criptografada)** .|

1. Opcional: Selecione **Opções Avançadas...**  

   | Parâmetro | DESCRIÇÃO |
   | --- | --- |
   |  Use Consulta Nativa |Quando selecionado, o driver ODBC NÃO tenta converter TSQL em HiveQL. Você deve usá-lo somente se você for 100% certeza de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
   |  Linhas buscadas por bloco |Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, Comprimento da coluna binária e Escala da coluna decimal |Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles farão com que informações incorretas sejam retornadas devido à perda de precisão e/ou truncamento. |

    ![Opções avançadas de configuração de DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Advanced DSN configuration options")

1. Selecione **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, o resultado do teste mostrará **SUCESSO!** .  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.  

1. Selecione **OK** para fechar a janela **Administrador de Fonte de Dados ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importar dados do HDInsight para o Excel

As etapas a seguir descrevem a maneira de importar dados de uma tabela Hive em uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou na seção anterior.

1. Abra uma pasta de trabalho nova ou existente no Excel.

2. Na guia **Dados**, navegue até **Obter Dados** > **De Outras Fontes** > **Do ODBC** para abrir a janela **Do ODBC**.

    ![Abrir o assistente de conexão de dados do Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "abrir assistente de conexão de dados do Excel")

3. Na lista suspensa, selecione o nome da fonte de dados que você criou na última seção e, em seguida, selecione **OK**.

4. Para o primeiro uso, uma caixa de diálogo do **driver ODBC** será aberta. Selecione **Windows** no menu à esquerda. Em seguida, selecione **conectar** para abrir a janela **navegador** .

5. De **Navegador**, navegue até **HIVE** > **padrão** > **hivesampletable** e selecione **Carregar**. Leva alguns minutos para que os dados sejam importados para o Excel.

    ![Hdinsight Excel Hive ODBC Navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDINSIGHT Excel Hive ODBC Navigator")

## <a name="next-steps"></a>Próximas etapas

Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um serviço do HDInsight. Para obter mais informações, consulte:

* [Visualizar dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar consultas do Apache Hive no HDInsight do Azure](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conecte o Excel ao Apache Hadoop usando o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregue os dados no HDInsight](./../hdinsight-upload-data.md).
