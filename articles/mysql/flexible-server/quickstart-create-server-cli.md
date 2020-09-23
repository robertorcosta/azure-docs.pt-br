---
title: 'Início Rápido: Criar um servidor – CLI do Azure – Banco de Dados do Azure para MySQL – Servidor Flexível'
description: Este início rápido descreverá como usar a CLI do Azure para criar um Servidor Flexível do Banco de Dados do Azure para MySQL em um grupo de recursos do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc
ms.openlocfilehash: bae6e9f04eced02130ae628d5308a87a1baaa8fa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943565"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Início Rápido: Criar um Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure

Este início rápido mostrará como usar os comandos da [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) no [Azure Cloud Shell](https://shell.azure.com) para criar um Servidor Flexível do Banco de Dados do Azure para MySQL em cinco minutos. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT] 
> Atualmente, o Servidor Flexível do Banco de Dados do Azure para MySQL está em versão prévia pública

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O [Azure Cloud Shell](../../cloud-shell/overview.md) é um shell gratuito e interativo que poderá ser usado para executar as etapas deste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode abrir o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e selecione **Enter** para executá-lo.

Caso prefira instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará fazer logon em sua conta usando o comando [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Observe a propriedade **id**, que se refere à **ID da Assinatura** para sua conta do Azure.

```azurecli-interactive
az login
```

Selecione a assinatura específica em sua conta usando o comando [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Anote o valor de **id** da saída **az login** para usar como valor para o argumento **subscription** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Criar um servidor flexível

Crie um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) usando o comando `az group create` e depois crie um servidor flexível do MySQL dentro desse grupo de recursos. Você deve fornecer um nome exclusivo. O exemplo a seguir cria um grupo de recursos chamado `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie um servidor flexível usando o comando `az mysql flexible-server create`. Um servidor pode conter vários bancos de dados. O seguinte comando criará um servidor usando valores e padrões de serviço do [contexto local](https://docs.microsoft.com/cli/azure/local-context?view=azure-cli-latest) da CLI do Azure: 

```azurecli
az mysql flexible-server create
```

O servidor criado terá os atributos abaixo: 
- Nome do servidor gerado automaticamente, nome de usuário do administrador, senha de administrador, nome do grupo de recursos (caso ainda não tenha sido especificado no contexto local). Além disso, o servidor e o grupo de recursos estarão no mesmo local 
- Padrões de serviço para configurações de servidor restantes: nível de computação (Intermitente), tamanho/SKU da computação (B1MS), período de retenção de backup (7 dias) e versão do MySQL (5.7)
- O método de conectividade padrão será o acesso Privado (Integração VNET) com rede e sub-rede virtuais geradas automaticamente

> [!NOTE] 
> O método de conectividade não poderá ser alterado após a criação do servidor. Por exemplo, caso tenha selecionado o *acesso Privado (Integração VNET)* durante a criação, não será possível alterar para o *acesso Público (endereços IP permitidos)* posteriormente. Recomendamos criar um servidor com acesso Privado para que seja possível acessar seu servidor com segurança usando a Integração VNet. Saiba mais sobre o acesso Privado no [artigo sobre conceitos](./concepts-networking.md).

Caso queira alterar os padrões, consulte a documentação de referência da CLI do Azure <!--FIXME --> para obter uma lista completa de parâmetros configuráveis da CLI. 

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Se esse for o caso, você não poderá se conectar ao seu servidor enquanto o departamento de TI não abrir a porta 3306.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "name": "Standard_B1ms",
    "size": null,
    "tier": "Burstable"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Conectar-se usando um cliente de linha de comando do MySQL

Como o servidor flexível foi criado com *acesso privado (Integração VNet)* , será necessário se conectar ao servidor de um recurso na mesma VNet do servidor. Será possível criar uma máquina virtual e adicioná-la à rede virtual criada. 

Depois que a VM for criada, será possível usar o SSH no computador e instalar uma ferramenta de cliente popular, como a ferramenta de linha de comando **[mysql.exe](https://dev.mysql.com/downloads/)** .

Com o mysql.exe, conecte-se usando o comando abaixo. Substitua os valores por um nome do servidor e uma senha que sejam reais. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los ao fazer o seguinte comando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Caso queira apenas excluir o servidor recém-criado, será possível executar o comando `az mysql server delete`.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Criar um aplicativo Web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)