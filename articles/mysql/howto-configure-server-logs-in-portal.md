---
title: Acesse registros de consulta lenta - Portal Azure - Banco de dados Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs lentos no Banco de Dados Azure para MySQL a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 59faf63312bd7cc657f8b96ca3110707ea997c02
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273590"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configure e acesse registros de consulta lenta do portal Azure

Você pode configurar, listar e baixar o [banco de dados Azure para logs de consulta lenta MySQL](concepts-server-logs.md) no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas deste artigo exigem que você tenha [o Banco de Dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta lenta do MySQL. 

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione seu servidor de Banco de Dados do Azure para MySQL.

3. Na seção **Monitoramento** na barra lateral, selecione **Registros do servidor**. 
   ![Captura de tela das opções de logs do Servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **Clique aqui para ativar logs e configurar parâmetros de log**.

5. Vire **slow_query_log** para **ON**.

6. Selecione onde fazer a saída dos logs para usar **log_output**. Para enviar logs para o armazenamento local e para os logs de diagnóstico do Monitor do Azure, selecione **Arquivo**. 

7. Altere todos os outros parâmetros necessários. 

8. Clique em **Salvar**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Captura de tela de parâmetros de log de consulta lenta e salvar.":::

Na página Parâmetros do **servidor,** você pode retornar à lista de registros fechando a página.

## <a name="view-list-and-download-logs"></a>Exibir a lista e baixar os logs
Depois que o registro começa, você pode exibir uma lista de registros de consulta lenta disponíveis e baixar arquivos de log individuais.

1. Abra o portal do Azure.

2. Selecione seu servidor de Banco de Dados do Azure para MySQL.

3. Na seção **Monitoramento** na barra lateral, selecione **Registros do servidor**. A página mostra uma lista de seus arquivos de registro.

   ![Captura de tela da página de logs do Servidor, com lista de registros destacados](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomenclatura do log é **mysql-slow-< nome do seu servidor>-aaaammddhh.log**. A data e a hora usadas no nome do arquivo é a hora em que o registro foi emitido. Os arquivos de log são girados a cada 24 horas ou 7,5 GB, o que vier primeiro. 

4. Se necessário, use a caixa de pesquisa para reduzir rapidamente a um registro específico, com base na data e hora. A pesquisa busca o nome do log.

5. Para baixar arquivos de log individuais, selecione o ícone de seta baixa ao lado de cada arquivo de log na linha de tabela.

   ![Captura de tela da página de logs do Servidor, com ícone de seta baixa destacado](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

1. Na seção **Monitoramento** na barra lateral, selecione **Configurações** > de diagnóstico**Adicionar configurações de diagnóstico**.

   ![Captura de tela das opções de configurações de diagnóstico](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especifique quais dados afundam para enviar os logs de consulta lenta (conta de armazenamento, hub de eventos ou espaço de trabalho do Log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de log.
![Captura de tela das opções de configuração de configurações de diagnóstico](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Depois de configurar os dissipadores de dados para canalizar os registros de consulta lenta para, **selecione Salvar**.
![Captura de tela das opções de configuração de configurações de diagnóstico, com save destacado](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Acesse os logs de consulta lenta explorando-os nos dissipadores de dados configurados. Pode levar até 10 minutos para que os registros apareçam.

## <a name="next-steps"></a>Próximas etapas
- Consulte [Acesso a consultas lentas Logs em CLI](howto-configure-server-logs-in-cli.md) para saber como baixar registros de consulta lenta programáticamente.
- Saiba mais sobre [logs de consulta lenta no](concepts-server-logs.md) Banco de Dados Azure para MySQL.
- Para obter mais informações sobre as definições de parâmetros e o registro do MySQL, consulte a documentação MySQL nos [registros](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).