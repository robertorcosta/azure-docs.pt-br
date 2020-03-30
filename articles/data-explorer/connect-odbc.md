---
title: Conecte-se ao Azure Data Explorer com o ODBC
description: Neste artigo, você aprende como configurar uma conexão ODBC (Open Database Connectivity, conectividade de banco de dados aberto) ao Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034027"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Conecte-se ao Azure Data Explorer com o ODBC

Open Database Connectivity[(ODBC)](/sql/odbc/reference/odbc-overview)é uma interface de programação de aplicativos (API) amplamente aceita para acesso ao banco de dados. Use o ODBC para se conectar ao Azure Data Explorer a partir de aplicativos que não possuem um conector dedicado.

Nos bastidores, os aplicativos chamam funções na interface ODBC, que são implementadas em módulos específicos de banco de dados chamados *drivers*. O Azure Data Explorer suporta um subconjunto do protocolo de comunicação SQL Server[(MS-TDS),](/azure/kusto/api/tds/)para que ele possa usar o driver ODBC para SQL Server.

Usando o vídeo a seguir, você pode aprender a criar uma conexão ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Alternativamente, você pode [configurar a fonte de dados ODBC](#configure-the-odbc-data-source) conforme detalhado abaixo. 

No artigo, você aprende a usar o driver SQL Server ODBC, para que você possa se conectar ao Azure Data Explorer a partir de qualquer aplicativo que suporte o ODBC. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte:

* [Driver Microsoft ODBC para SQL Server versão 17.2.0.1 ou posterior](/sql/connect/odbc/download-odbc-driver-for-sql-server) para o seu sistema operacional.

## <a name="configure-the-odbc-data-source"></a>Configure a fonte de dados ODBC

Siga essas etapas para configurar uma fonte de dados ODBC usando o driver ODBC para SQL Server.

1. No Windows, procure *fontes de dados oDBC*e abra o aplicativo desktop ODBC Data Sources.

1. Selecione **Adicionar**.

    ![Adicionar fonte de dados](media/connect-odbc/add-data-source.png)

1. Selecione **o driver ODBC 17 para o SQL Server** e depois **termine**.

    ![Selecionar driver](media/connect-odbc/select-driver.png)

1. Digite um nome e uma descrição para a conexão e o cluster ao que deseja se conectar e, em seguida, selecione **Next**. A URL do cluster deve estar no formulário * \<ClusterName\>.\< Região\>(kusto.windows.net.*

    ![Selecionar Servidor](media/connect-odbc/select-server.png)

1. Selecione **O diretório ativo integrado** e depois o **next**.

    ![Integrado ao Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Selecione o banco de dados com os dados da amostra e **depois o Next**.

    ![Alterar banco de dados padrão](media/connect-odbc/change-default-database.png)

1. Na próxima tela, deixe todas as opções como padrão e selecione **Terminar**.

1. Selecione **A fonte de dados do teste**.

    ![Fonte de dados de teste](media/connect-odbc/test-data-source.png)

1. Verifique se o teste foi bem sucedido e selecione **OK**. Se o teste não tiver sucesso, verifique os valores especificados em etapas anteriores e certifique-se de ter permissões suficientes para se conectar ao cluster.

    ![Teste bem sucedido](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Próximas etapas

* [Conecte-se ao Azure Data Explorer do Tableau](tableau.md)