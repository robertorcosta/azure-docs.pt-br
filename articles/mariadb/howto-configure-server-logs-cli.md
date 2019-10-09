---
title: Acessar logs do servidor no Banco de Dados do Azure para MariaDB usando a CLI do Azure
description: Este artigo descreve como acessar logs do servidor no Banco de Dados do Azure para MariaDB usando o utilitário de linha de comando da CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ffc724ef5133ee25643a966d2b6d8448a4c3a920
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023605"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e acessar logs de servidor usando a CLI do Azure
É possível baixar os logs do servidor do Banco de Dados do Azure para MariaDB usando a CLI do Azure, o utilitário de linha de comando do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Banco de dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- A [CLI do Azure](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no navegador

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Configurar o registro em log do Banco de Dados do Azure para MariaDB
É possível configurar o servidor para acessar os logs de consulta lenta do MariaDB, executando as seguintes etapas:
1. Ative o registro em log definindo o parâmetro **slow\_query\_log** como ON.
2. Ajuste outros parâmetros como **long\_query\_time** e **log\_slow\_admin\_statements**.

Para saber como definir o valor desses parâmetros por meio da CLI do Azure, consulte [Como configurar parâmetros do servidor](howto-configure-server-parameters-cli.md).

Por exemplo, o comando da CLI a seguir ativa o log de consultas lentas, define o tempo de consulta longo para 10 segundos e desativa o registro em log da instrução de admin lenta. Por fim, ele lista as opções de configuração para sua análise.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Listar logs para o servidor do Banco de Dados do Azure para MariaDB
Para listar os arquivos de log de consulta lenta disponíveis para seu servidor, execute o comando [AZ MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Você pode listar os arquivos de log para o servidor **mydemoserver.mariadb.database.azure.com** no grupo de recursos **myresourcegroup**. Em seguida, direcione-os para um arquivo de texto chamado **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar logs do servidor
Com o comando [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download), você pode baixar arquivos de log individuais para o servidor.

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.mariadb.database.azure.com** no grupo de recursos **myresourcegroup** para o ambiente local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [logs de consulta lentos no banco de dados do Azure para MariaDB](concepts-server-logs.md).
