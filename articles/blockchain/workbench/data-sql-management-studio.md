---
title: Consultar dados do Azure Blockchain Workbench usando SQL Server Management Studio
description: Saiba como se conectar ao Banco de Dados SQL do Azure Blockchain Workbench pelo SQL Server Management Studio.
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: f87d1880c90202fa26b0477e3b4dfbed5965bb82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326030"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Usando os dados do Azure Blockchain Workbench com o SQL Server Management Studio

Microsoft SQL Server Management Studio permite escrever e testar as consultas rapidamente no Banco de Dados SQL do Azure Blockchain Workbench. Esta seção contém uma explicação passo a passo de como se conectar ao banco de dados SQL do Azure Blockchain Workbench de dentro SQL Server Management Studio.

## <a name="prerequisites"></a>Pré-requisitos

* Baixar [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Conectando o SQL Server Management Studio aos dados no Azure Blockchain Workbench

1. Abra o SQL Server Management Studio e selecione **Conectar**.
2. Selecione **Mecanismo de Banco de Dados**.

    ![Mecanismo de banco de dados](./media/data-sql-management-studio/database-engine.png)

3. Na caixa de diálogo **Conectar-se ao Servidor**, insira o nome do servidor e suas credenciais de banco de dados.

    Se você estiver usando as credenciais criadas pelo processo de implantação do Azure Blockchain Workbench, o nome de usuário será **dbadmin**, e a senha será a fornecida durante a implantação.

    ![Inserir credenciais de SQL](./media/data-sql-management-studio/sql-creds.png)

   1. O SQL Server Management Studio exibe a lista de bancos de dados, exibições de bancos de dados e procedimentos armazenados no banco de dados do Azure Blockchain Workbench.

      ![Lista de banco de dados](./media/data-sql-management-studio/db-list.png)

5. Para exibir os dados associados a qualquer uma das exibições de banco de dados, você poderá gerar automaticamente uma instrução select usando as etapas a seguir.
6. Clique com o botão direito do mouse em qualquer uma das exibições de banco de dados no Pesquisador de Objetos.
7. Selecione **Exibição de Script como**.
8. Escolha **SELECIONAR para**.
9. Selecione **Janela do Editor de Nova Consulta**.
10. Uma nova consulta pode ser criada selecionando **Nova Consulta**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibições de banco de dados no Azure Blockchain Workbench](database-views.md)
