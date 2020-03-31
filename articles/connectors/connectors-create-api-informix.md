---
title: Conecte-se ao banco de dados IBM Informix
description: Automatize tarefas e fluxos de trabalho que gerenciam recursos armazenados no IBM Informix usando aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757961"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Gerencie os recursos do banco de dados IBM Informix usando aplicativos de lógica do Azure

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [conector Informix,](/connectors/informix/)você pode criar tarefas e fluxos de trabalho automatizados que gerenciam recursos em um banco de dados IBM Informix. Este conector inclui um cliente Microsoft que se comunica com computadores de servidor Informix remotos em uma rede TCP/IP, incluindo bancos de dados baseados em nuvem, como o IBM Informix para Windows em execução na virtualização do Azure e bancos de dados on-premises quando você usa o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Você pode se conectar a essas plataformas e versões do Informix se elas estiverem configuradas para suportar conexões de clientes DE Arquitetura de Banco de Dados Relacionais Distribuídos (DRDA):

* IBM Informix 12.1
* IBM Informix 11.7

Este tópico mostra como usar o Conector em um aplicativo lógico para processar as operações do banco de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Para bancos de dados locais, [baixe e instale o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um computador local e, em seguida, crie um recurso de gateway de dados do [Azure no portal Azure](../logic-apps/logic-apps-gateway-connection.md).

* O aplicativo lógico onde você precisa ter acesso ao seu banco de dados Informix. Este conector fornece apenas ações, então seu aplicativo lógico já deve começar com um gatilho, por exemplo, o [gatilho Recorrência](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Adicione uma ação Informix

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativo lógico, se já não estiver aberto.

1. a etapa onde você deseja adicionar a ação Informix, selecione **Novo passo**.

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, insira `informix` como o filtro. Na lista de ações, selecione a ação desejada, por exemplo:

   ![Selecione a ação Informix para executar](./media/connectors-create-api-informix/select-informix-connector-action.png)

   O conector fornece essas ações, que executam as operações correspondentes do banco de dados:

   * Obter tabelas - Listar `CALL` tabelas de banco de dados usando uma declaração
   * Obter linhas - Leia todas as `SELECT *` linhas usando uma declaração
   * Obter linha - Leia uma `SELECT WHERE` linha usando uma declaração
   * Adicione uma linha `INSERT` usando uma declaração
   * Editar uma linha `UPDATE` usando uma declaração
   * Exclua uma linha `DELETE` usando uma declaração

1. Se você for solicitado a fornecer detalhes de conexão para o seu banco de dados Informix, siga as [etapas para criar a conexão](#create-connection)e, em seguida, continue com o próximo passo.

1. Forneça as informações para sua ação selecionada:

   | Ação | Descrição | Propriedades e descrições |
   |--------|-------------|-----------------------------|
   | **Obter tabelas** | Liste as tabelas do banco de dados executando uma declaração Informix CALL. | Nenhum |
   | **Obter linhas** | Busque todas as linhas na tabela especificada `SELECT *` executando uma declaração informix. | **Nome da tabela**: O nome para a tabela Informix que você deseja <p><p>Para adicionar outras propriedades a esta ação, selecione-as na lista **Adicionar novos parâmetros.** Para obter mais informações, consulte o [tópico de referência do conector](/connectors/informix/). |
   | **Obter linha** | Obter uma linha da tabela especificada executando `SELECT WHERE` uma declaração Informix. | - **Nome da tabela**: O nome para a tabela Informix que você deseja <br>- **ID de linha**: O ID exclusivo para a linha, por exemplo,`9999` |
   | **Inserir linha** | Adicione uma linha à tabela Informix especificada `INSERT` executando uma declaração Informix. | - **Nome da tabela**: O nome para a tabela Informix que você deseja <br>- **item**: A linha com os valores a serem adicionados |
   | **Atualizar linha** | Altere uma linha na tabela Informix especificada `UPDATE` executando uma declaração Informix. | - **Nome da tabela**: O nome para a tabela Informix que você deseja <br>- **ID de linha**: O ID exclusivo para a linha para atualizar, por exemplo,`9999` <br>- **Linha:** A linha com os valores atualizados, por exemplo,`102` |
   | **Excluir linha** | Remova uma linha da tabela Informix especificada `DELETE` executando uma declaração Informix. | - **Nome da tabela**: O nome para a tabela Informix que você deseja <br>- **ID de linha**: O ID exclusivo para a linha para excluir, por exemplo,`9999` |
   ||||

1. Salve seu aplicativo lógico. Agora, teste [seu aplicativo lógico](#test-logic-app) ou continue construindo seu aplicativo lógico.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Conecte-se ao Informix

1. Se o aplicativo de lógica se conectar a um banco de dados local, selecione **Conectar via gateway de dados on-premises**.

1. Forneça essas informações de conexão e selecione **Criar**.

   | Propriedade | Propriedade JSON | Obrigatório | Valor de exemplo | Descrição |
   |----------|---------------|----------|---------------|-------------|
   | Nome da conexão | `name` | Sim | `informix-demo-connection` | O nome a ser usado para a conexão com seu banco de dados Informix |
   | Servidor | `server` | Sim | - Nuvem:`informixdemo.cloudapp.net:9089` <br>- No local:`informixdemo:9089` | O endereço TCP/IP ou alias que está no formato IPv4 ou IPv6, seguido por um número de porta TCP/IP |
   | Banco de dados | `database` | Sim | `nwind` | O nome do banco de dados relacional DRDA (RDBNAM) ou o nome do banco de dados Informix (dbname). Informix aceita uma seqüência de 128 bytes. |
   | Autenticação | `authentication` | Somente no local | **Básico** ou **Windows** (kerberos) | O tipo de autenticação que é exigido pelo seu banco de dados Informix. Essa propriedade só aparece quando você seleciona **Conectar via gateway de dados on-premises**. |
   | Nome de Usuário | `username` | Não | <*banco de dados-nome de usuário*> | Um nome de usuário para o banco de dados |
   | Senha | `password` | Não | <*banco de dados-senha*> | Uma senha para o banco de dados |
   | Gateway | `gateway` | Somente no local | - <*assinatura do Azure*> <br>- <*azure-on-premises-data-gateway-resource*> | A assinatura do Azure e o nome do recurso Do Zure para o gateway de dados local que você criou no portal Azure. A propriedade e as subpropriedades **do Gateway** só são exibidas quando você **seleciona Conectar via gateway de dados on-premises**. |
   ||||||

   Por exemplo: 

   * **Banco de dados em nuvem**

     ![Informações de conexão de banco de dados na nuvem](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Banco de dados local**

     ![Informações de conexão de banco de dados no local](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Salve seu aplicativo lógico.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas Logic App Designer, selecione **Executar**. Depois que seu aplicativo lógico é executado, você pode visualizar as saídas dessa execução.

1. No menu do aplicativo lógico, selecione **Visão geral**. No painel de visão geral, em **Resumo** > **Executa histórico,** selecione a execução mais recente.

1. Em **Logic app run,** selecione **Run Details**.

1. Na lista de ações, selecione a ação com as saídas que deseja visualizar, por exemplo, **Get_tables**.

   Se a ação foi bem sucedida, sua propriedade **Status** será marcada como **bem sucedida**.

1. Para visualizar as entradas, em **Inputs Link,** selecione o link URL. Para visualizar as saídas, em **''''Vincular',** selecione o link URL.. Aqui estão alguns exemplos de saídas:

   * **Get_tables** mostra uma lista de tabelas:

     ![Saídas da ação "Obter tabelas"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** mostra uma lista de linhas:

     ![Saídas da ação "Obter linhas"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** mostra a linha especificada:

     ![Saídas da ação "Get row"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** mostra a nova linha:

     ![Saídas da ação "Inserir linha"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** mostra a linha atualizada:

     ![Saídas da ação "Atualizar linha"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** mostra a linha excluída:

     ![Saídas da ação "Excluir linha"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição swagger do conector, revise a [página de referência do conector](/connectors/informix/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](apis-list.md)
