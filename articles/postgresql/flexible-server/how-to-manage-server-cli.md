---
title: Gerenciar servidor-CLI do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Saiba como gerenciar um servidor de banco de dados do Azure para PostgreSQL-flexível do CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8e24dd6cb8a1fa90f1a6caf9117ab3c344c00b12
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913867"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Gerenciar um banco de dados do Azure para PostgreSQL – servidor flexível usando o CLI do Azure

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo mostra como gerenciar seu servidor flexível implantado no Azure. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="prerequisites"></a>Pré-requisitos
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará fazer logon em sua conta usando o comando [az login](/cli/azure/reference-index#az-login). Observe a propriedade **id** , que se refere à **ID da Assinatura** para sua conta do Azure.

```azurecli-interactive
az login
```

Selecione a assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Anote o valor de **id** da saída **az login** para usar como valor para o argumento **subscription** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se você ainda não criou um servidor flexível, crie um para começar a usar este guia de instruções.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Você pode escalar verticalmente sua camada de computação, vCores e armazenamento facilmente usando o comando a seguir. Você pode ver toda a operação do servidor que pode executar [AZ postgres Flexible-Server Server Overview](https://docs.microsoft.com/cli/azure/postgres/flexible-server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Estes são os detalhes dos argumentos acima:

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Insira um nome exclusivo para o servidor. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter de 3 a 63 caracteres.
resource-group | myresourcegroup | Forneça o nome do grupo de recursos do Azure.
sku-name|Standard_D4ds_v3|Insira o nome da camada e do tamanho da computação. Segue a Convenção Standard_ {tamanho da VM} em formato abreviado. Confira os [tipos de preço](../concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | A capacidade de armazenamento do servidor (a unidade é megabytes). Mínimo de 5120 e aumentos em incrementos de 1024.

> [!IMPORTANT]
> O armazenamento não pode ser reduzido verticalmente. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gerenciar bancos de dados PostgreSQL em um servidor

Há vários aplicativos que você pode usar para conectar o servidor Banco de Dados do Azure para PostgreSQL. Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para se conectar a um servidor PostgreSQL do Azure. Usaremos agora o utilitário de linha de comando psql para nos conectarmos ao servidor PostgreSQL do Azure.

1. Execute o comando psql a seguir para se conectar a um Banco de Dados do Azure para servidor PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** no seu servidor PostgreSQL **mydemoserver.postgres.database.azure.com** usando as credenciais de acesso. Insira o `<server_admin_password>` que você escolheu quando uma senha foi solicitada a você.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Depois que você se conectar, o utilitário psql exibirá um prompt do postgres no qual você digitará os comandos do sql. Na saída de conexão inicial, um aviso pode ser exibido, pois o psql que você está usando pode ter uma versão diferente da versão Banco de Dados do Azure para o servidor PostgreSQL.

   Exemplo de saída psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Se o firewall não está configurado para permitir o endereço IP do seu cliente, ocorre o seguinte erro:
   >
   > "psql: FATAL: nenhuma entrada pg_hba.conf para o host `<IP address>`, usuário "myadmin", banco de dados "postgres", SSL em FATAL: Uma conexão SSL é necessária. Especifique as opções de SSL e tente novamente.
   >
   > Confirme se o que IP do seu cliente é permitido na etapa de regras de firewall acima.

2. Crie um banco de dados em branco chamado "postgresdb" no prompt digitando o seguinte comando:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. No prompt, execute o seguinte comando para mudar as conexões para o banco de dados **postgresdb** recém-criado:

    ```bash
    \c postgresdb
    ```

4. Digite `\q` e selecione a tecla Enter para sair do psql.

Você está conectado ao Banco de Dados do Azure para servidor PostgreSQL via psql e criou um banco de dados do usuário em branco.

## <a name="reset-admin-password"></a>Redefinir senha de administrador
Você pode alterar a senha da função de administrador com este comando
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Verifique se a senha tem no mínimo 8 caracteres e no máximo 128 caracteres.
> A senha deve conter caracteres de três das seguintes categorias: letras maiúsculas em inglês, letras minúsculas, números e caracteres não alfanuméricos.

## <a name="delete-a-server"></a>Excluir um servidor

Se você quiser apenas excluir o servidor flexível PostgreSQL, poderá executar o comando [AZ postgres exflexíveis-Server Delete](https://docs.microsoft.com/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

- [Entender os conceitos de backup e restauração](concepts-backup-restore.md)
- [Ajustar e monitorar o servidor](concepts-monitoring.md)