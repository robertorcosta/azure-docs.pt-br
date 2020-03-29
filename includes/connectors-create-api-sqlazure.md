---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789182"
---
* Se você estiver usando o banco de dados do SQL do Microsoft Azure, siga as etapas em [Conectar-se ao Banco de Dados SQL do Azure](#connect-azure-sql-db).

* Se você estiver usando o SQL Server, siga as etapas em [Conectar ao SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Conectar-se ao Banco de Dados SQL do Azure

Quando o gatilho ou ação do SQL solicita informações de conexão, siga essas etapas, que funcionam tanto para gatilhos quanto para ações.

1. Para **nome de conexão,** crie um nome para sua conexão.

1. Em **Nome do servidor SQL,** selecione o servidor SQL do Azure. Quando a lista **SQL Database Name** for exibida, selecione seu banco de dados. Forneça o nome de usuário e a senha do servidor SQL do Azure.

   Você também pode encontrar essas informações no portal Azure suas propriedades de banco de dados SQL ou em sua seqüência de conexões:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Crie conexão com o Banco de Dados SQL do Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar sua conexão, continue com [adicionar um gatilho SQL](#add-sql-trigger) ou [adicionar uma ação SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Conecte-se ao SQL Server

Quando o gatilho ou ação do SQL solicita informações de conexão, siga essas etapas, que funcionam tanto para gatilhos quanto para ações. Para cenários que exijam que você instale o gateway de dados local em um computador local e [crie o recurso de gateway de dados do Azure,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection) [certifique-se](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) de concluir esses requisitos primeiro. Caso contrário, seu recurso gateway não aparecerá na lista de gateways quando você criar sua conexão.

Além disso, para usar a autenticação do Windows com o conector SQL Server em um [ambiente de serviço de integração (ISE),](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)use a versão não ISE do conector e o gateway de dados on-premises. A versão rotulada com ISE não suporta autenticação do Windows.

1. Para **nome de conexão,** crie um nome para sua conexão.

1. No gatilho ou ação, selecione **Conectar por meio do gateway de dados locais** para que sejam exibidas as opções do SQL Server.

1. Para **nome do servidor SQL** e nome do banco de dados **SQL,** forneça o endereço para o servidor SQL e o nome do seu banco de dados. Para **nome de usuário** e **senha,** forneça o nome de usuário e a senha do servidor.

   Você também pode encontrar essas informações em sua seqüência de conexões:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Criar conexão com o SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Se o servidor SQL usar o Windows ou a autenticação Básica, selecione o **Tipo de Autenticação**.

1. Em **Gateways,** selecione a assinatura do Azure associada ao gateway de dados criado anteriormente no local e selecione o nome para o gateway de dados no local.

   Se o seu gateway não aparecer na lista, verifique se você configurou [corretamente seu gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Criar conexão SQL Server concluída](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar sua conexão, continue com [Adicionar gatilho SQL](#add-sql-trigger) ou [Adicionar ação SQL](#add-sql-action).
