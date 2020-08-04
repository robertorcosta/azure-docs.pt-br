---
title: 'Início Rápido: Criar servidor – CLI do Azure – Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Guia de início rápido para criar um Banco de Dados do Azure para PostgreSQL – Servidor único usando a CLI (interface de linha de comando) do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030122"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Início Rápido: Criar um Banco de Dados do Azure para PostgreSQL – Servidor único usando a CLI do Azure

> [!TIP]
> Considere usar o comando mais simples da CLI do Azure [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) (atualmente em versão prévia). Experimente o [início rápido](./quickstart-create-server-up-azure-cli.md).

Este início rápido mostra como usar os comandos da [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) no [Azure Cloud Shell](https://shell.azure.com) para criar um servidor do Banco de Dados do Azure para PostgreSQL em cinco minutos.  Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará fazer logon em sua conta usando o comando [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Observe a propriedade **id**, que se refere à **ID da Assinatura** para sua conta do Azure. 

```azurecli-interactive
az login
```

Selecione a ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Anote o valor de **id** da saída **az login** para usar como valor para o argumento **subscription** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) e crie o seu servidor PostgreSQL dentro desse grupo de recursos. Você deve fornecer um nome exclusivo. O exemplo a seguir cria um grupo de recursos chamado `myresourcegroup` na localização `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie um [Banco de Dados do Azure para PostgreSQL](overview.md) usando o comando [az postgres server create](/cli/azure/postgres/server). Um servidor pode conter vários bancos de dados.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Estes são os detalhes dos argumentos acima: 

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Escolha um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter de 3 a 63 caracteres.
resource-group | myresourcegroup | Forneça o nome do grupo de recursos do Azure.
local | westus | O local do Azure para o servidor.
admin-user | myadmin | O nome de usuário para o logon de administrador. Não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.
admin-password | *senha de segurança* | A senha do usuário administrador. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos.
sku-name|GP_Gen5_2|Insira o nome do tipo de preço e a configuração de computação. Segue a convenção {tipo de preço} _{geração de computação}_ {vCores} em formato abreviado. Para obter mais informações, confira [Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- A versão padrão do PostgreSQL no seu servidor é 9.6. Veja todas as nossas versões com suporte [aqui](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).
>- Para ver todos os argumentos do comando **az postgres server create**, confira este [documento de referência](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)
>- O SSL é habilitado por padrão no seu servidor. Para obter mais informações sobre o SSL, confira [Configurar a conectividade SSL](./concepts-ssl-connection-security.md)

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor 
Por padrão, o servidor criado não é publicamente acessível nem protegido com regras de firewall. Você pode configurar a regra de firewall no servidor usando o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) para dar acesso ao seu ambiente local para se conectar ao servidor. 

O exemplo a seguir cria uma regra de firewall chamada `AllowMyIP`, que permite conexões de um endereço IP específico, 192.168.0.1. Substitua o endereço IP ou intervalo dos endereços IP que correspondem ao local do qual você vai se conectar.  Se não souber como procurar seu IP, acesse [https://whatismyipaddress.com/](https://whatismyipaddress.com/) para obter seu endereço IP.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Verifique se o firewall de sua rede permite a porta 5432 que é usada pelos servidores do Banco de Dados do Azure para PostgreSQL a fim de evitar problemas de conectividade. 

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **administratorLogin** e o **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Conectar-se ao Banco de Dados do Azure para servidor PostgreSQL usando o psql
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) é um cliente popular usado para se conectar a servidores PostgreSQL. Você pode se conectar ao seu servidor usando **psql** com o [Azure Cloud Shell](../cloud-shell/overview.md). Como alternativa, você poderá usar o psql em seu ambiente local se ele estiver disponível. Um banco de dados vazio, 'postgres', já foi criado com o novo servidor PostgreSQL que você pode usar para se conectar ao psql, conforme mostrado abaixo 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se você preferir usar um caminho de URL para se conectar ao Postgres, codifique a URL de entrada @ do nome de usuário com `%40`. Por exemplo, a cadeia de conexão para psql seria,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Limpar os recursos
Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los executando o seguinte comando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Se você quiser simplesmente excluir o servidor recém-criado, poderá executar o comando [az postgres server delete](/cli/azure/postgres/server).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)
> 
> [Implantar um aplicativo Web Django com PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Conectar-se com o aplicativo Node.js](./connect-nodejs.md)

