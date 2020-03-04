---
title: Visualizar dados do Data Explorer do Azure usando o Sisense
description: Neste artigo, saiba como configurar o Data Explorer do Azure como uma fonte de dados para Sisense e visualizar os dados.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358177"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualizar dados do Azure Data Explorer no Sisense

Sisense é uma plataforma de inteligência de negócios de análise que permite que você crie aplicativos de análise que oferecem experiências de usuário altamente interativos. O business intelligence e o painel, relatório de software permite que você acesse e combinar dados em apenas alguns cliques. Você pode conectar-se aos dados estruturados e fontes de, unir tabelas de várias fontes com o mínimo de script e de codificação e criar relatórios e dashboards interativos da web. Neste artigo, você aprenderá a configurar o Data Explorer do Azure como uma fonte de dados para Sisense e visualize os dados de um cluster de exemplo.

## <a name="prerequisites"></a>Prerequisites

Você precisará do seguinte para concluir este artigo:

* [Baixe e instale o aplicativo de Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Crie um cluster e o banco de dados que inclui os dados de exemplo StormEvents. Para saber mais, confira [Início Rápido: Criar um banco de dados e cluster do Azure Data Explorer](create-cluster-database-portal.md) e [Ingerir dados de exemplo no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Conectar-se aos painéis de Sisense usando o conector do JDBC de Gerenciador de dados do Azure

1. Baixar e copiar as versões mais recentes dos seguintes arquivos jar para *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Abra **Sisense** aplicativo.
1. Selecione **dados** e selecione **+ ElastiCube** para criar um novo modelo de ElastiCube.
    
    ![Selecione ElastiCube](media/sisense/data-select-elasticube.png)

1. Na **adicionar novo modelo de ElastiCube**, chame o modelo ElastiCube e **salvar**.
   
    ![Adicionar novo modelo ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Selecione **+ dados**.

    ![Selecione o botão de dados](media/sisense/select-data.png)

1. Na **selecione o conector** , selecione **JDBC genérico** conector.

    ![Escolha o conector do JDBC](media/sisense/select-connector.png)

1. No **Connect** guia, conclua os seguintes campos para o **JDBC genérico** connector e selecione **próxima**.

    ![Configurações do conector do JDBC](media/sisense/jdbc-connector.png)

    |Campo |DESCRIÇÃO |
    |---------|---------|
    |Cadeia de conexão     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Pasta de JARs do JDBC  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nome da classe do driver    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Nome do Usuário   |    Nome de usuário do AAD     |
    |Senha     |   Senha de usuário do AAD      |

1. No **selecionar dados** guia, procure **Selecionar banco de dados** para selecionar o banco de dados relevante aos quais você tem permissões. Neste exemplo, selecione *test1*.

    ![selecionar banco de dados](media/sisense/select-database.png)

1. Na *testar* painel (nome do banco de dados):
    1. Selecione o nome da tabela para visualizar a tabela e ver os nomes de coluna de tabela. Você pode remover colunas desnecessárias.
    1. Marque a caixa de seleção da tabela relevante para selecionar a tabela. 
    1. Selecione **Concluído**.

    ![Selecionar tabela](media/sisense/select-table-see-columns.png)    

1. Selecione **Build** para compilar seu conjunto de dados. 

    * No **construir** janela, selecione **Build**.

      ![Janela de compilação](media/sisense/build-window.png)

    * Aguarde até que o processo de compilação é concluída e, em seguida, selecione **Build bem-sucedido**.

      ![Build concluído com êxito](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Criar painéis de Sisense

1. Na **Analytics** guia, selecione **+**  >  **novo painel** para criar painéis nesse conjunto de dados.

    ![Novo painel](media/sisense/new-dashboard.png)

1. Escolher um dashboard e selecione **criar**. 

    ![Criar painel](media/sisense/create-dashboard.png)

1. Sob **novo Widget**, selecione **+ selecionar dados** para criar um novo widget. 

    ![Adicionar campos ao painel de StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Selecione **+ adicionar mais dados** adicionar colunas extras ao seu grafo. 

    ![Adicionar mais dados ao gráfico](media/sisense/add-more-data.png)

1. Selecione **+ Widget** para criar outro widget. Widgets de arrastar e soltar para reorganizar seu painel.

    ![Painel do Storm](media/sisense/final-dashboard.png)

Agora você pode explorar seus dados com análise visual, crie painéis adicionais e transformar dados em informações acionáveis para causar impacto nos negócios.

## <a name="next-steps"></a>Próximas etapas

* [Gravar consultas para Azure Data Explorer](write-queries.md)

