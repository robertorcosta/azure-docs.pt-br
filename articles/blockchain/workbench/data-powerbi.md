---
title: Usar dados do Azure Blockchain Workbench no Microsoft Power BI
description: Saiba como carregar e exibir dados de Banco de Dados SQL do Azure Blockchain Workbench no Microsoft Power BI.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6e1f160c3563a280548c74ebe84f30bf08945c3f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324789"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Usando dados do Azure Blockchain Workbench com o Microsoft Power BI

O Microsoft Power BI permite gerar facilmente relatórios sofisticados de bancos de dados SQL usando o Power BI Desktop e, em seguida, publicá-los no [https://www.powerbi.com](https://www.powerbi.com).

Este artigo contém uma explicação passo a passo de como se conectar ao Banco de Dados SQL do Azure Blockchain Workbench no Power BI Desktop, criar um relatório e implantar o relatório em powerbi.com.

## <a name="prerequisites"></a>pré-requisitos

* Baixe o [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Conectando o Power BI aos dados no Azure Blockchain Workbench

1.  Abra o Power BI Desktop.
2.  Selecione **Obter Dados**.

    ![Obter dados](./media/data-powerbi/get-data.png)
3.  Selecione **SQL Server** na lista de tipos de fonte de dados.

4.  Forneça o nome do servidor e do banco de dados na caixa de diálogo. Especifique se você deseja importar os dados ou executar uma **DirectQuery**. Selecione **OK**.

    ![Selecionar SQL Server](./media/data-powerbi/select-sql.png)

5.  Forneça as credenciais de banco de dados para acessar o Azure Blockchain Workbench. Clique em **Banco de Dados** e insira suas credenciais.

    Se você estiver usando as credenciais criadas pelo processo de implantação do Azure Blockchain Workbench, o nome de usuário será **dbadmin**, e a senha será a fornecida durante a implantação.

    ![Configurações de Banco de Dados SQL](./media/data-powerbi/db-settings.png)

6.  Quando a conexão com o banco de dados estiver estabelecida, a caixa de diálogo **Navegador** mostrará as tabelas e as exibições disponíveis no banco de dados. As exibições são criadas para geração de relatórios, e todas elas recebem **vw** como prefixo.

    ![Navegador](./media/data-powerbi/navigator.png)

7.  Selecione as exibições que deseja incluir. Para fins de demonstração, incluímos **vwContractAction**, que fornece detalhes sobre todas as ações que foram executadas em um contrato.

    ![Selecionar exibições](./media/data-powerbi/select-views.png)

Agora, você pode criar e publicar relatórios, como faria normalmente com o Power BI.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibições de banco de dados no Azure Blockchain Workbench](database-views.md)