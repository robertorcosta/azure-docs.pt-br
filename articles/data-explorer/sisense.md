---
title: Visualize dados do Azure Data Explorer usando o Sisense
description: Neste artigo, saiba como configurar o Azure Data Explorer como fonte de dados para o Sisense e visualizar os dados.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358177"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualize dados do Azure Data Explorer no Sisense

O Sisense é uma plataforma de inteligência de negócios de análise que permite criar aplicativos de análise que ofereçam experiências altamente interativas ao usuário. O software de inteligência de negócios e relatórios de dashboard permite que você acesse e combine dados em poucos cliques. Você pode se conectar a fontes de dados estruturadas e não estruturadas, juntar tabelas de várias fontes com scripts e codificação mínimos e criar painéis e relatórios interativos. Neste artigo, você aprenderá como configurar o Azure Data Explorer como fonte de dados para o Sisense e visualizar dados de um cluster de amostras.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte para concluir este artigo:

* [Baixe e instale o aplicativo Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Crie um cluster e um banco de dados que inclua os dados de amostra do StormEvents. Para obter mais informações, consulte [Quickstart: Crie um cluster e banco de dados do Azure Data Explorer](create-cluster-database-portal.md) e [insere dados de amostra no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Conecte-se aos painéis sisense usando o conector JDBC do Azure Data Explorer

1. Baixe e copie as versões mais recentes dos seguintes arquivos de jar para *.. \Sisense\DataConnectors\jdbcdrivers\adx* 

    * ativação-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-coleções4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-anotações-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Abra o aplicativo **Sisense.**
1. Selecione a guia **Dados** e selecione **+ElastiCube** para criar um novo modelo ElastiCube.
    
    ![Selecione ElastiCube](media/sisense/data-select-elasticube.png)

1. Em **Adicionar novo modelo ElastiCube,** nomeie o modelo ElastiCube e **Salve**.
   
    ![Adicionar novo modelo ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Selecione **+ Dados**.

    ![Selecione o botão de dados](media/sisense/select-data.png)

1. Na guia **Select Connector,** selecione **conector Genérico JDBC.**

    ![Escolha o conector JDBC](media/sisense/select-connector.png)

1. Na guia **Conectar,** complete os seguintes campos para o conector **JDBC genérico** e selecione **Next**.

    ![Configurações do conector JDBC](media/sisense/jdbc-connector.png)

    |Campo |Descrição |
    |---------|---------|
    |Cadeia de conexão     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Pasta JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nome da classe do motorista    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Nome do Usuário   |    Nome de usuário AAD     |
    |Senha     |   Senha de usuário AAD      |

1. Na guia **Selecionar dados,** **pesquise 'Selecionar banco de dados'** para selecionar o banco de dados relevante ao qual você tem permissões. Neste exemplo, selecione *teste1*.

    ![selecionar banco de dados](media/sisense/select-database.png)

1. No *painel de teste* (nome do banco de dados):
    1. Selecione o nome da tabela para visualizar a tabela e veja os nomes da coluna de tabela. Você pode remover colunas desnecessárias.
    1. Selecione a caixa de seleção da tabela relevante para selecionar essa tabela. 
    1. Selecione **Feito**.

    ![selecionar tabela](media/sisense/select-table-see-columns.png)    

1. Selecione **Build** para construir seu conjunto de dados. 

    * Na **janela Construir,** selecione **'Construir ''Construir'.**

      ![Construir janela](media/sisense/build-window.png)

    * Aguarde até que o processo de compilação esteja concluído e, em seguida, selecione **Build Succeeded**.

      ![Build bem-sucedido](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Crie painéis Sisense

1. Na guia **Analytics,** selecione **+**  >  **Novo Painel** para criar dashboards neste conjunto de dados.

    ![Novo painel](media/sisense/new-dashboard.png)

1. Escolha um painel e selecione **Criar**. 

    ![Criar painel](media/sisense/create-dashboard.png)

1. Em **Novo Widget,** selecione **+ Selecione Dados** para criar um novo widget. 

    ![Adicionar campos ao painel de eventos do StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Selecione **+ Adicione mais dados** para adicionar colunas adicionais ao seu gráfico. 

    ![Adicione mais dados ao gráfico](media/sisense/add-more-data.png)

1. Selecione **+ Widget** para criar outro widget. Arraste e solte widgets para reorganizar seu painel.

    ![Painel do Storm](media/sisense/final-dashboard.png)

Agora você pode explorar seus dados com análisevisual, construir dashboards adicionais e transformar dados em insights acionáveis para causar um impacto em seus negócios.

## <a name="next-steps"></a>Próximas etapas

* [Escrever consultas para O Azure Data Explorer](write-queries.md)

