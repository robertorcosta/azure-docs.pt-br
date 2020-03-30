---
title: Configurar parâmetros de servidor - Azure CLI - Banco de dados Azure para MySQL
description: Este artigo descreve como configurar os parâmetros de serviço no Banco de Dados do Azure para MySQL usando o utilitário da linha de comando da CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f3027909d1c4684e2ef5d1b6e967cb11f570fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062424"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Personalize os parâmetros do servidor usando o Azure CLI
É possível listar, exibir e atualizar os parâmetros de configuração de um servidor de Banco de Dados do Azure para MySQL usando o utilitário da linha de comando da CLI do Azure. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Um banco de dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Utilitário de linha de comando do [Azure CLI](/cli/azure/install-azure-cli) ou use o Azure Cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Listar os parâmetros de configuração de servidor para o Banco de Dados do Azure para MySQL
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list).

É possível listar os parâmetros de configuração do servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do MySQL em [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando [az mysql server configuration show.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show)

Este exemplo mostra detalhes do parâmetro de configuração de servidor **slow\_query\_log** para o servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor do parâmetro de configuração do servidor
Você também pode modificar o valor de determinados parâmetros de configuração, que atualiza o valor da configuração subjacente para o mecanismo do servidor MySQL. Para atualizar o valor de configuração execute o comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set). 

Para atualizar o parâmetro de configuração de servidor **slow\_query\_log** do servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Se você quiser redefinir o valor de um parâmetro de configuração, omita o parâmetro opcional `--value` e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Esse código redefine a configuração **log de\_consultas\_lentas** para o valor padrão **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser preenchidas, chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL.

> [!NOTE]
> Se estiver executando o comando `az_load_timezone` do Workbench do MySQL, talvez seja necessário desativar primeiro o modo de atualização segura usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Você deve reiniciar o servidor para garantir que as tabelas de fuso horário estejam preenchidas corretamente. Para reiniciar o servidor, use o [portal Azure](howto-restart-server-portal.md) ou [CLI](howto-restart-server-cli.md).

Para exibir os valores de fuso horário disponíveis, execute o comando a seguir:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurar o fuso horário de nível global

O fuso horário de nível global pode ser configurado usando o comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set).

O comando a seguir atualiza o parâmetro de configuração do servidor de **fuso\_horário** do servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup** para **EUA/Pacífico**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Configurar o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser configurado, executando o comando `SET time_zone` a partir de uma ferramenta como a linha de comando do MySQL ou Workbench do MySQL. O exemplo abaixo configura o fuso horário para **EUA/Pacífico**.  

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MySQL para [Funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Próximas etapas

- Como configurar [parâmetros de servidor no Portal do Azure](howto-server-parameters.md)