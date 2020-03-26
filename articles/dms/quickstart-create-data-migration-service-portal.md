---
title: 'Início Rápido: Criar uma instância usando o portal do Azure'
titleSuffix: Azure Database Migration Service
description: Use o portal do Azure para criar uma instância do Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/08/2020
ms.openlocfilehash: efbf7d9db91b8c23151ca4337bd4d58f814a7096
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78254972"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Início Rápido: Crie uma instância do Serviço de Migração de Banco de Dados do Azure usando o Portal do Azure

Nesse Início Rápido, use o portal do Azure para criar uma instância do Serviço de Migração de Banco de Dados do Azure.  Depois de criar o serviço, pode usá-lo para migrar dados do SQL Server no local para o Banco de Dados SQL do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Abra seu navegador da Web, navegue até o [portal do Microsoft Azure](https://portal.azure.com/) e insira suas credenciais para entrar no portal.

A exibição padrão é o painel de serviço.

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

Registre o provedor de recursos Microsoft.DataMigration antes de criar sua primeira instância do Serviço de Migração de Banco de Dados.

1. No portal do Azure, selecione **Todos os serviços**e, em seguida, selecione **Assinaturas**.

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Crie uma instância do serviço

1. Selecione +**Criar um recurso** para criar uma instância do Serviço de Migração de Banco de Dados do Azure.

2. Pesquise "migração" no marketplace, selecione **Serviço de Migração de Banco de Dados do Azure** e, em seguida, na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

3. Na tela **Criar Serviço de Migração**:

    - Escolha um **Nome de Serviço** que seja fácil de lembrar e exclusivo para identificar sua instância do Serviço de Migração de Banco de Dados do Azure.
    - Selecione a **Assinatura** do Azure na qual quer criar a instância.
    - Selecione um **Grupo de Recursos** existente ou crie um novo.
    - Escolha o **Local** mais próximo ao seu servidor de origem ou de destino.
    - Selecione uma **Rede virtual** existente ou crie uma.

        A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao banco de dados de origem e ao ambiente de destino.

        Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/vnet).

    - Selecione Básico: 1 vCore para o **Tipo de preço**.

        ![Criar serviço de migração](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Selecione **Criar**.

    Após alguns instantes, sua instância do Serviço de Migração de Banco de Dados do Azure é criada e estará pronta para uso. O Serviço de Migração de Banco de Dados do Azure exibe conforme mostrado na imagem seguinte:

    ![Serviço de migração criado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode limpar todos os recursos criados nesse início rápido excluindo o [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md). Para excluir o grupo de recursos, navegue até a instância do Serviço de Migração de Banco de Dados do Azure que você criou. Selecione o nome do **Grupo de recursos** e selecione **Excluir grupo de recursos**. Essa ação exclui todos os ativos no grupo de recursos, bem como o grupo em si.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar SQL Server local para o Banco de Dados SQL do Azure](tutorial-sql-server-to-azure-sql.md)