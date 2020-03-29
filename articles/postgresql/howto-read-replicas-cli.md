---
title: Gerenciar réplicas de leitura - Azure CLI, REST API - Banco de dados Azure para PostgreSQL - Servidor Único
description: Saiba como gerenciar réplicas de leitura no Banco de Dados Azure para PostgreSQL - Servidor Único da Azure CLI e API REST
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774797"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Crie e gerencie réplicas de leitura da Azure CLI, API REST

Neste artigo, você aprende como criar e gerenciar réplicas de leitura no Banco de Dados Azure para PostgreSQL usando a API Azure CLI e REST. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

## <a name="azure-cli"></a>CLI do Azure
Você pode criar e gerenciar réplicas de leitura usando o Azure CLI.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Um [servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-up-azure-cli.md) que será o servidor mestre.


### <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de uso geral ou otimizada para memória.

O parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando este parâmetro estático é alterado, é necessária uma reinicialização do servidor para que a alteração entre em vigor.

1. Configurado `azure.replication_support` para réplica.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Se você receber o erro "Valor inválido dado" ao tentar definir azure.replication_support da CLI do Azure CLI, é provável que seu servidor já tenha REPLICA definido por padrão. Um bug está impedindo que essa configuração seja refletida corretamente em servidores mais novos onde o REPLICA é o padrão interno. <br><br>
> Você pode pular as etapas mestres de preparação e ir para criar a réplica. <br><br>
> Se você quiser confirmar se seu servidor está nesta categoria, visite a página de replicação do servidor no portal Azure. "Desativar a replicação" será acinzentado e "Adicionar réplica" estará ativo na barra de ferramentas.

2. Reinicie o servidor para aplicar a alteração.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

O [comando az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) requer os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group | myresourcegroup |  O grupo de recursos onde o servidor de réplica será criado.  |
| name | mydemoserver-replica | O nome do novo servidor de réplica criado. |
| source-server | mydemoserver | O nome ou id de recurso do servidor mestre existente para replicar. |

No exemplo CLI abaixo, a réplica é criada na mesma região que o mestre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Para criar uma réplica de `--location` leitura de região cruzada, use o parâmetro. O exemplo da CLI abaixo cria a réplica no Oeste dos EUA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo de conceitos de réplica de leitura](concepts-read-replicas.md). 

Se você não definir `azure.replication_support` o parâmetro para **REPLICAR** em um servidor mestre de propósito geral ou de memória otimizada para memória e reiniciar o servidor, você receberá um erro. Complete esses dois passos antes de criar uma réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para um novo valor, atualize a configuração da réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Réplicas de lista
Você pode visualizar a lista de réplicas de um servidor mestre usando o comando [az postgres server replica list.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode parar a replicação entre um servidor mestre e uma réplica de leitura usando o comando [az postgres server replica stop.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

Depois de interromper a replicação para um servidor mestre e uma réplica de leitura, isso não poderá ser desfeito. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Exclua um servidor mestre ou réplica
Para excluir um servidor mestre ou réplica, use o comando [az postgres server delete.](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)

Ao excluir um servidor mestre, a replicação para todas as réplicas de leitura será interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
Você pode criar e gerenciar réplicas de leitura usando a [API Azure REST](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de uso geral ou otimizada para memória.

O parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando este parâmetro estático é alterado, é necessária uma reinicialização do servidor para que a alteração entre em vigor.

1. Configurado `azure.replication_support` para réplica.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Reinicie o servidor](/rest/api/postgresql/servers/restart) para aplicar a alteração.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Você pode criar uma réplica de leitura usando a [API criar](/rest/api/postgresql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo de conceitos de réplica de leitura](concepts-read-replicas.md). 

Se você não definir `azure.replication_support` o parâmetro para **REPLICAR** em um servidor mestre de propósito geral ou de memória otimizada para memória e reiniciar o servidor, você receberá um erro. Complete esses dois passos antes de criar uma réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.


> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para um novo valor, atualize a configuração da réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Réplicas de lista
Você pode visualizar a lista de réplicas de um servidor mestre usando a [API da lista de réplicas](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode parar a replicação entre um servidor mestre e uma réplica de leitura usando a [API de atualização](/rest/api/postgresql/servers/update).

Depois de interromper a replicação para um servidor mestre e uma réplica de leitura, isso não poderá ser desfeito. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Exclua um servidor mestre ou réplica
Para excluir um servidor mestre ou réplica, você usa a [API excluir](/rest/api/postgresql/servers/delete):

Ao excluir um servidor mestre, a replicação para todas as réplicas de leitura será interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
