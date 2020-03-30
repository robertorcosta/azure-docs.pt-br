---
title: Configure o firewall do banco de dados azure Blockchain Workbench
description: Saiba como configurar o firewall de banco de dados Azure Blockchain Workbench Preview para permitir que clientes e aplicativos externos se conectem.
ms.date: 09/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e6739b7ead9299f020465d3c50ed01826334ca76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325993"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Configurar o firewall do banco de dados do Azure Blockchain Workbench

Este artigo mostra como configurar uma regra de firewall usando o portal do Azure. As regras de firewall permitem que clientes externos ou aplicativos se conectem ao banco de dados do Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Conecte-se ao banco de dados do Blockchain Workbench

Para se conectar ao banco de dados em que você deseja configurar uma regra:

1. Faça login no portal Dozure com uma conta que tenha permissões **do proprietário** para os recursos da Bancada de Trabalho blockchain do Azure.
2. No painel de navegação esquerdo, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos para sua implantação do Blockchain Workbench.
4. Selecione **Tipo** para classificar a lista de recursos e, em seguida, escolha o **SQL Server**.
5. O exemplo de lista de recursos na captura de tela a seguir mostra dois bancos de dados: *master* e *lsgn-sdk*. Configure a regra de firewall em *lsgn-sdk*.

![Listar recursos do Blockchain Workbench](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Criar uma regra de firewall de banco de dados

Criar uma regra de firewall:

1. Escolha o link para o banco de dados "lsgn-sdk".
2. Na barra de menus, selecione **Definir firewall do servidor**.

   ![Definir firewall do servidor](./media/database-firewall/configure-server-firewall.png)

3. Para criar uma regra para a sua organização:

   * Insira um **NOME DE REGRA**
   * Insira um endereço IP para o **IP INICIAL** do intervalo de endereços
   * Insira um endereço IP para o **IP FINAL** do intervalo de endereços

   ![Criar uma regra de firewall](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Se quiser adicionar apenas o endereço IP do seu computador, escolha **+ Adicionar IP do cliente**.
        
1. Para salvar sua configuração de firewall, selecione **Salvar**.
2. Teste o intervalo de endereços IP configurado para o banco de dados conectando-se por um aplicativo ou uma ferramenta de teste. Por exemplo, SQL Server Management Studio.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibições de banco de dados no Azure Blockchain Workbench](database-views.md)