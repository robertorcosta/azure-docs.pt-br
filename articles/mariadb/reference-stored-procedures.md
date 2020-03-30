---
title: Procedimentos armazenados de gestão - Banco de Dados Azure para MariaDB
description: Saiba quais procedimentos armazenados no Banco de Dados Do Azure para O MariaDB são úteis para ajudá-lo a configurar a replicação de dados, definir o fuso horário e matar consultas.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529983"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Banco de dados Azure para procedimentos armazenados de gestão do MariaDB

Os procedimentos armazenados estão disponíveis no Banco de Dados Azure para servidores MariaDB para ajudar a gerenciar seu servidor MariaDB. Isso inclui o gerenciamento das conexões, consultas do servidor e a configuração de replicação de dados.  

## <a name="data-in-replication-stored-procedures"></a>Procedimentos armazenados de replicação de dados

A Replicação de Dados permite sincronizar dados de um servidor do MariaDB executado localmente em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem no serviço do Banco de Dados do Azure para MariaDB.

Os procedimentos armazenados a seguir são usados para configurar ou remover a Replicação de Dados entre um mestre e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de entrada**|**Parâmetros de saída**|**Observação de uso**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Para transferir dados com o modo SSL, passe o contexto do certificado CA para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, passe uma cadeia de caracteres vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Inicia a replicação.|
|*mysql.az_replication _stop*|N/D|N/D|Para a replicação.|
|*mysql.az_replication _remove_master*|N/D|N/D|Remove o relacionamento de replicação entre o mestre e a réplica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora um erro de replicação.|

Para configurar a replicação de dados entre um mestre e uma réplica no Banco de Dados do Azure para MariaDB, consulte [como configurar a replicação de dados](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

Os procedimentos armazenados a seguir estão disponíveis no Banco de Dados Azure para que o MariaDB gerencie seu servidor.

|**Nome do procedimento armazenado**|**Parâmetros de entrada**|**Parâmetros de saída**|**Observação de uso**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) ao comando. Encerrará a conexão associada à processlist_id fornecida após o término de qualquer declaração que a conexão esteja executando.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) ao comando. Terminará a declaração que a conexão está executando no momento. Deixa a conexão viva.|
|*mysql.az_load_timezone*|N/D|N/D|Carrega tabelas de fuso horário para permitir que o `time_zone` parâmetro seja definido como valores nomeados (ex. "EUA/Pacífico").|

## <a name="next-steps"></a>Próximas etapas
- Saiba como configurar a [replicação de dados](howto-data-in-replication.md)
- Saiba como usar as tabelas de [fuso horário](howto-server-parameters.md#working-with-the-time-zone-parameter)