---
title: Conecte-se ao SQL Server ou ao Banco de Dados SQL do Azure
description: Automatize tarefas para bancos de dados SQL em locais ou na nuvem usando aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789181"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatize fluxos de trabalho para o SQL Server ou O Banco de Dados SQL do Azure usando aplicativos de lógica do Azure

Este artigo mostra como você pode acessar dados no banco de dados SQL de dentro de um aplicativo lógico com o conector do SQL Server. Dessa forma, você pode automatizar tarefas, processos ou fluxos de trabalho que gerenciam seus dados e recursos SQL criando aplicativos lógicos. O conector SQL Server funciona tanto para [o SQL Server no local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) quanto para o Banco de Dados [SQL baseado em nuvem](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Você pode criar Aplicativos Lógicos que são executados quando disparados por eventos no Banco de Dados SQL ou em outros sistemas, como Dynamics CRM Online. Seus aplicativos de lógica também podem obter, inserir e excluir dados, juntamente com a execução de consultas SQL e procedimentos armazenados. Por exemplo, você pode criar um aplicativo lógico que verifica automaticamente novos registros no Dynamics CRM Online, adiciona itens ao seu banco de dados SQL para quaisquer novos registros e, em seguida, envia alertas de e-mail sobre os itens adicionados.

Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, limitações e problemas conhecidos, consulte a [página de referência do conector SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [banco de dados SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) ou banco de dados [SQL do Azure](../sql-database/sql-database-get-started-portal.md)

  As tabelas devem ter dados para que seu aplicativo lógico possa retornar resultados ao chamar operações. Se você criar um Banco de Dados SQL do Azure, poderá usar bancos de dados de exemplo, que estão incluídos.

* O nome do SQL Server, o nome do banco de dados, o nome de usuário e a senha. Você precisará dessas credenciais para que possa autorizar sua lógica para acessar o SQL Server.

  * Para o SQL Server, você pode encontrar esses detalhes na cadeia de conexão:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para o Banco de Dados SQL do Azure, você pode encontrar esses detalhes na cadeia de conexão ou no portal do Azure abaixo das propriedades do Banco de Dados SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* O [gateway de dados local](../logic-apps/logic-apps-gateway-install.md) instalado em um computador local e um recurso de gateway de dados do [Azure criado no portal Azure](../logic-apps/logic-apps-gateway-connection.md) para esses cenários:

  * Seus aplicativos lógicos não são executados em um [ambiente de serviço de integração (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * Seus aplicativos *lógicos são* executados em um ambiente de serviço de integração, mas você tem que usar a autenticação do Windows para sua conexão SQL Server. Para este cenário, use a versão não ISE do conector SQL Server, juntamente com o gateway de dados, porque a versão ISE não suporta autenticação do Windows.

* O aplicativo lógico em que você precisa de acesso ao banco de dados SQL. Para iniciar seu aplicativo lógico com um gatilho SQL, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicione um gatilho SQL

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo Logic Apps cria uma instância lógica do aplicativo e começa a executar o fluxo de trabalho do seu aplicativo lógico.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. No designer, na caixa de pesquisa, digite "sql server" como seu filtro. Na lista de gatilhos, selecione o gatilho SQL desejado.

   Este exemplo usa o **gatilho Quando um item é criado.**

   ![Selecione o gatilho "Quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se você for solicitado a criar uma conexão, [crie sua conexão SQL agora](#create-connection). Se sua conexão existir, selecione um **nome de tabela**.

   ![Selecione a tabela que você deseja](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Defina as propriedades **Intervalo** e **Frequência**, que especificam com que frequência o aplicativo lógico verifica a tabela.

   Este gatilho retorna apenas uma linha da tabela selecionada, nada mais. Para executar outras tarefas, adicione outras ações que executem as tarefas desejadas. Por exemplo, para visualizar os dados nesta linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e, em seguida, enviar alertas de e-mail. Para saber mais sobre outras ações disponíveis para este conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Quando terminar, na barra de ferramentas do designer, selecione **Salvar**.

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicione uma ação SQL

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico é iniciado com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md) e chama uma ação que obtém uma linha de um banco de dados SQL.

1. No portal do Azure ou no Visual Studio, abra o aplicativo lógico no Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. No gatilho ou ação em que você deseja adicionar a ação SQL, selecione **Nova etapa**.

   ![Adicione um novo passo ao seu aplicativo lógico](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Em **Escolha uma ação**, na caixa de pesquisa, digite "sql server" como seu filtro. Na lista de ações, selecione a ação SQL desejada.

   Este exemplo usa a ação **Get row,** que obtém um único registro.

   ![Encontre e selecione a ação SQL "Get row"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Esta ação retorna apenas uma linha da tabela selecionada, nada mais. Para exibir os dados nesta linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e armazenam esse arquivo em uma conta de armazenamento na nuvem. Para saber mais sobre outras ações disponíveis para este conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Se você for solicitado a criar uma conexão, [crie sua conexão SQL agora](#create-connection). Se sua conexão existir, selecione um **nome de tabela**e digite o **ID de linha** para o registro que deseja.

   ![Insira o nome da tabela e a ID da linha](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Quando terminar, na barra de ferramentas do designer, selecione **Salvar**.

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Lidar com dados em massa

Às vezes, você tem que trabalhar com conjuntos de resultados tão grandes que o conector não retorne todos os resultados ao mesmo tempo, ou você quer um melhor controle sobre o tamanho e estrutura para seus conjuntos de resultados. Aqui estão algumas maneiras que você pode lidar com conjuntos de resultados tão grandes:

* Para ajudá-lo a gerenciar resultados como conjuntos menores, ative *a paginação*. Para obter mais informações, consulte [Obter dados em massa, registros e itens usando paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Crie um procedimento armazenado que organize os resultados da maneira que você quiser.

  Ao obter ou inserir várias linhas, seu aplicativo lógico pode iterar através dessas linhas usando um [*loop até*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro desses [limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando seu aplicativo lógico tem que trabalhar com conjuntos de registros tão grandes, por exemplo, milhares ou milhões de linhas, que você deseja minimizar os custos resultantes de chamadas para o banco de dados.

  Para organizar os resultados da maneira que você deseja, você pode criar um [*procedimento armazenado*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que é executado na sua instância SQL e usa a declaração SELECT - **ORDER BY.** Essa solução fornece a você mais controle sobre o tamanho e a estrutura de seus resultados. Seu aplicativo lógico chama o procedimento armazenado usando a ação **Executar procedimento armazenado** do conector do SQL Server.

  Para obter mais detalhes sobre a solução, consulte estes artigos:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginação de SQL para transferência de dados em massa com Aplicativos Lógicos)

  * [SELECT – Cláusula ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, ações e limites deste conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Próximas etapas

* Conheça outros [conectores para aplicativos azure logic](../connectors/apis-list.md)
