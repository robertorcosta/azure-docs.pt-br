---
title: Conectar ao SQL Server ou ao Banco de Dados SQL do Azure
description: Automatizar tarefas para bancos de dados SQL locais ou na nuvem usando Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: c32e17aaf83c233ad77bbbf607c30cc526253352
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402590"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizar fluxos de trabalho para SQL Server ou Banco de Dados SQL do Azure usando Aplicativos Lógicos do Azure

Este artigo mostra como você pode acessar dados no banco de dados SQL de dentro de um aplicativo lógico com o conector do SQL Server. Dessa forma, você pode automatizar tarefas, processos ou fluxos de trabalho que gerenciam dados e recursos SQL com a criação de aplicativos lógicos. O conector do SQL Server funciona tanto para o [SQL Server local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) quanto para o [Banco de Dados SQL do Azure baseado em nuvem](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Você pode criar Aplicativos Lógicos que são executados quando disparados por eventos no Banco de Dados SQL ou em outros sistemas, como Dynamics CRM Online. Os aplicativos lógicos também podem obter, inserir e excluir dados, além de executar consultas SQL e procedimentos armazenados. Por exemplo, você pode criar um aplicativo lógico que verifica automaticamente se há novos registros Dynamics CRM Online, adiciona itens ao Banco de Dados SQL para novos registros e envia alertas de email sobre os itens adicionados.

Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, limitações e problemas conhecidos, confira a [página de referência do conector do SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [banco de dados SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) ou um [Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md)

  As tabelas devem ter dados para que seu aplicativo lógico possa retornar resultados ao chamar operações. Se você criar um Banco de Dados SQL do Azure, poderá usar bancos de dados de exemplo, que estão incluídos.

* O nome do SQL Server, o nome do banco de dados, o nome de usuário e a senha. Você precisará dessas credenciais para que possa autorizar sua lógica para acessar o SQL Server.

  * Para o SQL Server, você pode encontrar esses detalhes na cadeia de conexão:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para o Banco de Dados SQL do Azure, você pode encontrar esses detalhes na cadeia de conexão ou no portal do Azure abaixo das propriedades do Banco de Dados SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* O [gateway de dados local](../logic-apps/logic-apps-gateway-install.md) instalado em um computador local e um [recurso do gateway de dados do Azure criado no portal do Azure](../logic-apps/logic-apps-gateway-connection.md) para estes cenários:

  * Os aplicativos lógicos não são executados em um [ISE (Ambiente de Serviço de Integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Os aplicativos lógicos *são* executados em um Ambiente de Serviço de Integração, mas você precisa usar a autenticação do Windows para a conexão do SQL Server. Para esse cenário, use a versão não ISE do conector do SQL Server junto com o gateway de dados, pois a versão do ISE não é compatível com a autenticação do Windows.

* O aplicativo lógico em que você precisa de acesso ao banco de dados SQL. Para iniciar seu aplicativo lógico com um gatilho SQL, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicionar um gatilho SQL

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo lógico.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. No designer, na caixa de pesquisa, insira "sql server" como filtro. Na lista de gatilhos, selecione o gatilho SQL desejado.

   Este exemplo usa o gatilho **Quando um item é criado**.

   ![Selecione o gatilho "Quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se for solicitada a criação da conexão, [crie sua conexão do SQL agora](#create-connection). Se a conexão existir, selecione um **Nome de tabela**.

   ![Selecione a tabela que você deseja](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Defina as propriedades **Intervalo** e **Frequência**, que especificam com que frequência o aplicativo lógico verifica a tabela.

   Este gatilho retorna apenas uma linha da tabela selecionada, nada mais. Para executar outras tarefas, adicione outras ações que executam as tarefas desejadas. Por exemplo, para ver os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e, em seguida, enviar alertas por email. Para saber mais sobre outras ações disponíveis para esse conector, confira a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicionar uma ação SQL

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico é iniciado com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md) e chama uma ação que obtém uma linha de um banco de dados SQL.

1. No portal do Azure ou no Visual Studio, abra o aplicativo lógico no Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. No gatilho ou na ação em que você deseja adicionar a ação SQL, selecione **Nova etapa**.

   ![Adicionar nova etapa ao aplicativo lógico](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Selecione o sinal mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira "sql server" como o filtro. Na lista de ações, selecione a ação SQL que desejar.

   Este exemplo usa a ação **Obter linha**, que obtém um registro único.

   ![Localizar e selecionar a ação SQL "Obter linha"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Esta ação retorna apenas uma linha da tabela selecionada, nada mais. Para ver os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e armazenar o arquivo em uma conta de armazenamento em nuvem. Para saber mais sobre outras ações disponíveis para esse conector, confira a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

1. Se for solicitada a criação da conexão, [crie sua conexão do SQL agora](#create-connection). Se a conexão existir, selecione um **nome de tabela** e insira a **ID da Linha** do registro desejado.

   ![Insira o nome da tabela e a ID da linha](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Lidar com os dados em massa

Às vezes, você tem que trabalhar com conjuntos de resultados tão grandes que o conector não retorna todos os resultados ao mesmo tempo ou você deseja um controle melhor sobre o tamanho e a estrutura dos conjuntos de resultados. Você pode lidar com esses grandes conjuntos de resultados das seguintes maneiras:

* Para ajudar você a gerenciar os resultados como conjuntos menores, ative a *paginação*. Para obter mais informações, confira [Obter dados em massa, registros e itens usando a paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Crie um procedimento armazenado que organize os resultados da maneira desejada.

  Ao obter ou inserir várias linhas, o aplicativo lógico pode iterar por essas linhas usando um [*loop until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro desses [limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando o aplicativo lógico precisa trabalhar com conjuntos de registros muito grandes, como milhares ou milhões de linhas, você deseja minimizar os custos resultantes de chamadas para o banco de dados.

  Para organizar os resultados da maneira que deseja, você pode criar um [*procedimento armazenado*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que é executado na instância SQL e usa a instrução **SELECT – ORDER BY**. Essa solução fornece a você mais controle sobre o tamanho e a estrutura de seus resultados. Seu aplicativo lógico chama o procedimento armazenado usando a ação **Executar procedimento armazenado** do conector do SQL Server.

  Para obter mais detalhes de solução, confira estes artigos:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginação de SQL para transferência de dados em massa com Aplicativos Lógicos)

  * [SELECT – Cláusula ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Manipular dados em massa dinâmicos

Às vezes, quando você faz uma chamada para um procedimento armazenado no conector do SQL Server, a saída retornada é dinâmica. Nesse cenário, siga estas etapas:

1. Abra o **Designer de Aplicativos Lógicos**.
1. Realize uma execução de teste de aplicativo lógico para conferir o formato de saída. Copie a saída de exemplo.
1. No designer, na ação em que você chama o procedimento armazenado, selecione **Nova etapa**.
1. Em **Escolha uma ação**, pesquise e selecione a ação [**Analisar JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).
1. Na ação **Analisar JSON**, selecione a opção **Usar carga de amostra para gerar esquema**.
1. Na janela **Insira ou cole uma amostra de conteúdo JSON**, cole a saída de exemplo e, em seguida, selecione **Concluído**.
1. Se você receber um erro informando que os Aplicativos Lógicos não podem gerar um esquema, verifique se a sintaxe da saída de exemplo está formatada corretamente. Se ainda não for possível gerar o esquema, insira-o manualmente na caixa **Esquema**.
1. Selecione **Salvar** na barra de ferramentas do designer.
1. Para acessar as propriedades de conteúdo JSON, use os tokens de dados que aparecem na lista de conteúdo dinâmico na ação [**Analisar JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre gatilhos, ações e limites desse conector, confira a [página de referência do conector](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [outros conectores para Aplicativos Lógicos do Azure](../connectors/apis-list.md)
