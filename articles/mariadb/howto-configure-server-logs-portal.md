---
title: Acesse registros de consulta lenta - Portal Azure - Banco de dados Do Azure para MariaDB
description: Este artigo descreve como configurar e acessar os logs de consulta lenta no Banco de Dados Do Azure para MariaDB a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89bdd209315445519c35f3ef2c2f1ad2555106ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531389"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configure e acesse registros de consulta lenta do portal Azure

Você pode configurar, listar e baixar o [banco de dados Azure para logs](concepts-server-logs.md) de consulta lenta do MariaDB no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas deste artigo exigem que você tenha [o Banco de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso ao log de consultas lentas. 

1. Faça login no [portal Azure](https://portal.azure.com/).

2. Selecione o servidor do Banco de Dados do Azure para MariaDB.

3. Na seção **Monitoramento** na barra lateral, selecione **Registros do servidor**. 
   ![Captura de tela das opções de logs do Servidor](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **Clique aqui para ativar logs e configurar parâmetros de log**.

5. Altere os parâmetros que você precisa ajustar, incluindo transformar **slow_query_log** para **ON**. Todas as alterações feitas nessa sessão são realçadas em roxo. 

   Depois de alterar os parâmetros, selecione **Salvar**. Ou pode descartar suas mudanças.

   ![Captura de tela das opções de Parâmetros do Servidor](./media/howto-configure-server-logs-portal/3-save-discard.png)

Na página Parâmetros do **servidor,** você pode retornar à lista de registros fechando a página.

## <a name="view-list-and-download-logs"></a>Exibir a lista e baixar os logs
Depois que o registro começa, você pode exibir uma lista de registros de consulta lenta disponíveis e baixar arquivos de log individuais. 

1. Abra o portal do Azure.

2. Selecione o servidor do Banco de Dados do Azure para MariaDB.

3. Na seção **Monitoramento** na barra lateral, selecione **Registros do servidor**. A página mostra uma lista de seus arquivos de registro.

   ![Captura de tela da página de logs do Servidor, com lista de registros destacados](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomenclatura do log é **mysql-slow-< nome do seu servidor>-aaaammddhh.log**. A data e a hora usadas no nome do arquivo é a hora em que o registro foi emitido. Os arquivos de log são girados a cada 24 horas ou 7,5 GB, o que vier primeiro.

4. Se necessário, use a caixa de pesquisa para reduzir rapidamente a um registro específico, com base na data e hora. A pesquisa busca o nome do log.

5. Para baixar arquivos de log individuais, selecione o ícone de seta baixa ao lado de cada arquivo de log na linha de tabela.

   ![Captura de tela da página de logs do Servidor, com ícone de seta baixa destacado](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

1. Na seção **Monitoramento** na barra lateral, selecione **Configurações** > de diagnóstico**Adicionar configuração de diagnóstico**.

   ![Captura de tela das opções de configurações de diagnóstico](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especifique quais dados afundam para enviar os logs de consulta lenta (conta de armazenamento, hub de eventos ou espaço de trabalho do Log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de log.
![Captura de tela das opções de configuração de configurações de diagnóstico](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Depois de configurar os dissipadores de dados para canalizar os registros de consulta lenta para, **selecione Salvar**.
![Captura de tela das opções de configuração de configurações de diagnóstico, com save destacado](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Acesse os logs de consulta lenta explorando-os nos dissipadores de dados configurados. Pode levar até 10 minutos para que os registros apareçam.

## <a name="next-steps"></a>Próximas etapas
- Consulte [Acessar logs de consulta lenta em CLI](howto-configure-server-logs-cli.md) para saber como baixar registros de consulta lenta programáticamente.
- Saiba mais sobre [os logs de consulta lenta no](concepts-server-logs.md) Banco de Dados Do Azure para MariaDB.
- Para obter mais informações sobre as definições e o registro dos parâmetros, consulte a documentação do MariaDB nos [registros](https://mariadb.com/kb/en/library/slow-query-log-overview/).