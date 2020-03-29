---
title: Gerenciar logs - Portal Azure - Banco de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar e acessar os logs de servidor (.log files) no Azure Database for PostgreSQL - Single Server from the Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763677"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configure e acesse o Banco de Dados Azure para PostgreSQL - Logs de servidor único do portal Azure

Você pode configurar, listar e baixar o [banco de dados Do Azure para logs PostgreSQL](concepts-server-logs.md) no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas deste artigo exigem que você tenha [o Banco de Dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta e de erros. 

1. Faça login no [portal Azure](https://portal.azure.com/).

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Na seção **Monitoramento** na barra lateral, selecione **Registros do servidor**. 

   ![Captura de tela das opções de logs do Servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **Clique aqui para ativar logs e configurar parâmetros de log**.

5. Altere os parâmetros que você precisa ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo.

   Depois de alterar os parâmetros, selecione **Salvar**. Ou pode descartar suas mudanças. 

   ![Captura de tela das opções de Parâmetros do Servidor](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na página Parâmetros do **servidor,** você pode retornar à lista de registros fechando a página.

## <a name="view-list-and-download-logs"></a>Exibir a lista e baixar os logs
Depois que o registro começar, você pode visualizar uma lista de logs disponíveis e baixar arquivos de log individuais. 

1. Abra o portal do Azure.

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Na seção **Monitoramento** na barra lateral, selecione **Registros do servidor**. A página mostra uma lista de seus arquivos de registro.

   ![Captura de tela da página de logs do Servidor, com lista de registros destacados](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomenclatura do log é **postgresql-yyyy-mm-dd_hh0000.log**. A data e a hora usadas no nome do arquivo é a hora em que o registro foi emitido. Os arquivos de registro giram a cada hora ou 100 MB, o que vier primeiro.

4. Se necessário, use a caixa de pesquisa para reduzir rapidamente a um registro específico, com base na data e hora. A pesquisa busca o nome do log.

   ![Captura de tela da página de logs do Servidor, com caixa de pesquisa e resultados destacados](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Para baixar arquivos de log individuais, selecione o ícone de seta baixa ao lado de cada arquivo de log na linha de tabela.

   ![Captura de tela da página de logs do Servidor, com ícone de seta baixa destacado](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Próximas etapas
- Consulte [os logs do servidor Access na CLI](howto-configure-server-logs-using-cli.md) para saber como baixar logs de forma programática.
- Saiba mais sobre [logs de servidor](concepts-server-logs.md) no Banco de Dados Azure para PostgreSQL. 
- Para obter mais informações sobre as definições de parâmetros e o registro de PostgreSQL, consulte a documentação PostgreSQL sobre [relatórios de erro e registro](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

