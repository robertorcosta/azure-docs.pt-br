---
title: Criptografia dupla de infraestrutura-portal do Azure-banco de dados do Azure para MySQL
description: Saiba como configurar e gerenciar a criptografia dupla de infraestrutura para o banco de dados do Azure para MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4623791e19ec83446b2978105286b71e6ff1eb1c
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034765"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Criptografia dupla de infraestrutura para o banco de dados do Azure para MySQL

Saiba como usar o como configurar e gerenciar a criptografia dupla de infraestrutura para seu banco de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Criar um servidor de banco de dados do Azure para MySQL com criptografia dupla de infraestrutura-Portal

Siga estas etapas para criar um servidor de banco de dados do Azure para MySQL com criptografia dupla de infraestrutura do portal do Azure:

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para MySQL**. Você também pode inserir **MySQL** na caixa de pesquisa para localizar o serviço.

   ![Opção do Banco de Dados do Azure para MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Forneça as informações básicas do servidor. Selecione **configurações adicionais** e habilite a caixa de seleção **criptografia dupla de infraestrutura** para definir o parâmetro.

    ![Seleções do banco de dados do Azure para MySQL](./media/howto-double-encryption/infrastructure-encryption-selected.png)

4. Selecione **revisão + criar** para provisionar o servidor.

    ![Resumo do banco de dados do Azure para MySQL](./media/howto-double-encryption/infrastructure-encryption-summary.png)

5. Depois que o servidor for criado, você poderá validar a criptografia dupla da infraestrutura verificando o status na folha do servidor de **criptografia de dados** .

    ![Validação do banco de dados do Azure para MySQL](./media/howto-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Criar um servidor de banco de dados do Azure para MySQL com a infraestrutura de criptografia dupla-CLI

Siga estas etapas para criar um servidor de banco de dados do Azure para MySQL com criptografia dupla de infraestrutura da CLI:

Este exemplo cria um grupo de recursos chamado `myresourcegroup` no `westus` local.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
O exemplo a seguir cria um servidor MySQL 5.7 no Oeste dos EUA chamado `mydemoserver` em seu grupo de recursos `myresourcegroup` com o logon de administrador de servidor `myadmin`. Esse é um servidor **Gen 4 de ** **Uso Geral** com **2 vCores**. Isso também habilitará a criptografia dupla de infraestrutura para o servidor criado. Substitua o `<server_admin_password>` com seu próprio valor.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Próximas etapas

 Para saber mais sobre criptografia de dados, confira [criptografia dupla de banco de dados do Azure para MySQL](concepts-Infrastructure-double-encryption.md).
