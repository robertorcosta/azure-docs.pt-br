---
title: Conectar-se ao Dynamics 365
description: Criar e gerenciar registros com as APIs REST do Dynamics 365 (online) e os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789879"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Gerenciar registros do Dynamics 365 com os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Dynamics 365, é possível criar tarefas e fluxos de trabalho automatizados com base nos recursos no Dynamics 365. Os fluxos de trabalho podem criar registros, atualizar itens, retornar registros e muito mais na sua conta do Dynamics 365. É possível incluir ações nos seus aplicativos lógicos que obtêm respostas do Dynamics 365 e disponibilizam a saída para outras ações. Por exemplo, quando um item é atualizado no Dynamics 365, você pode enviar um email usando o Office 365.

Este tópico mostra como criar um aplicativo lógico que cria uma tarefa no Dynamics 365 sempre que um novo registro de cliente potencial é criado no Dynamics 365.
Se ainda não estiver familiarizado com aplicativos lógicos, leia [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta do Dynamics 365](https://dynamics.microsoft.com)

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta do Dynamics 365. Para iniciar seu aplicativo lógico com um gatilho do Dynamics 365, você precisará de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Adicionar gatilho do Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Primeiro, adicione um gatilho do Dynamics 365 que é acionado quando um novo registro de cliente potencial é exibido no Dynamics 365.

1. No [Portal do Azure](https://portal.azure.com), abra seu aplicativo lógico em branco no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na caixa de pesquisa, insira “Dynamics 365” como filtro. Neste exemplo, na lista de gatilhos, selecione este gatilho: **Quando um registro é criado**

   ![Selecionar gatilho](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Se for solicitado que você entre no Dynamics 365, faça isso agora.

1. Forneça os seguintes detalhes do gatilho:

   | Propriedade | obrigatórios | Descrição |
   |----------|----------|-------------|
   | **Nome da organização** | SIM | O nome da instância do Dynamics 365 da sua organização a ser monitorada, por exemplo, “Contoso” |
   | **Nome da entidade** | SIM | O nome da entidade a ser monitorada, por exemplo, “Clientes potenciais” | 
   | **Frequência** | SIM | A unidade de tempo a ser usada com intervalos durante a verificação de atualizações relacionadas ao gatilho |
   | **Intervalo** | SIM | O número de segundos, minutos, horas, dias, semanas ou meses que você deseja que passem antes da próxima verificação |
   ||| 

   ![Detalhes do gatilho](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Adicionar ação do Dynamics 365

Agora, adicione a ação do Dynamics 365 que cria um registro de tarefa para o novo registro de cliente potencial.

1. No gatilho, escolha **Nova etapa**.

1. Na caixa de pesquisa, insira “Dynamics 365” como filtro. Na lista de ações, selecione esta ação: **Criar um novo registro**

   ![Ação selecionar](./media/connectors-create-api-crmonline/select-action.png)

1. Forneça esses detalhes da ação:

   | Propriedade | obrigatórios | Descrição |
   |----------|----------|-------------|
   | **Nome da organização** | SIM | A instância do Dynamics 365 na qual você deseja criar o registro, que não precisa ser a mesma instância em seu gatilho, mas é “Contoso” neste exemplo |
   | **Nome da entidade** | SIM | A entidade na qual você deseja criar o registro, por exemplo, “Tarefas” |
   | | |

   ![Detalhes da ação](./media/connectors-create-api-crmonline/action-details.png)

1. Quando a caixa **Assunto** é exibida na ação, clique dentro dessa caixa **Assunto** para que a lista de conteúdo dinâmica seja exibida. Nesta lista, selecione os valores de campo a serem incluídos no registro da tarefa associado ao novo registro de cliente potencial:

   | Campo | Descrição |
   |-------|-------------|
   | **Sobrenome** | O sobrenome do cliente potencial, como o contato principal no registro |
   | **Tópico** | O nome descritivo para o cliente potencial no registro |
   | | |

   ![Detalhes do registro da tarefa](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na barra de ferramentas do designer, escolha **Salvar** para seu aplicativo lógico. 

1. Para iniciar manualmente o aplicativo lógico, escolha **Executar** na barra de ferramentas do designer.

   ![Executar o aplicativo lógico](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Agora, crie um registro de cliente potencial no Dynamics 365 para que você possa acionar o fluxo de trabalho do aplicativo lógico.

## <a name="add-filter-or-query"></a>Adicionar um filtro ou consulta

Para especificar como filtrar dados em uma ação do Dynamics 365, escolha **Mostrar opções avançadas** nessa ação. Em seguida, adicione um filtro ou ordene pela consulta.
Por exemplo, é possível usar uma consulta de filtro para obter apenas contas ativas e ordenar os registros pelo nome da conta. Para executar essa tarefa, siga estas etapas:

1. Em **Filtrar consulta**, insira esta consulta de filtro OData: `statuscode eq 1`

2. Em **Ordenar por**, quando a lista de conteúdo dinâmica for exibida, selecione **Nome da conta**. 

   ![Especificar o filtro e ordem](./media/connectors-create-api-crmonline/advanced-options.png)

Para obter mais informações, veja essas opções de consulta do sistema de API Web de Participação do Cliente do Dynamics 365:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Práticas recomendadas para opções avançadas

Quando você especifica um valor para um campo em um gatilho ou ação, o tipo de dados do valor precisa corresponder ao tipo de campo, seja o valor inserido manualmente ou selecionado da lista de conteúdo dinâmica.

Esta tabela descreve alguns dos tipos de campo e os tipos de dados exigidos para seus valores.

| Tipo de campo | Tipo de dados exigido | Descrição | 
|------------|--------------------|-------------|
| Campos de texto | Linha única de texto | Esses campos exigem uma única linha de texto ou conteúdo dinâmico que tenha o tipo do texto. <p><p>*Campos de exemplo*: **Descrição** e **Categoria** | 
| Campos de número inteiro | Número inteiro | Alguns campos exigem conteúdo inteiro ou dinâmico que tenha um tipo inteiro. <p><p>*Campos de exemplo*: **Percentual concluído** e **Duração** | 
| Campos de data | Data e hora | Alguns campos exigem a data inserida no formato mm/dd/aaaa ou conteúdo dinâmico que tenha o tipo de data. <p><p>*Campos de exemplo*: **Criado em**, **Data de início**, **Início real**, **Término real** e **Data de conclusão** | 
| Campos que exigem uma ID de registro e um tipo de pesquisa | Chave primária | Alguns campos que fazem referência a outro registro de entidade exigem a ID do registro e um tipo de pesquisa. | 
||||

Expandindo esses tipos de campo, vemos aqui os campos de exemplo em gatilhos e ações do Dynamics 365 que exigem uma ID de registro e o tipo de pesquisa. Esse requisito significa que valores selecionados na lista dinâmica não funcionarão.

| Campo | Descrição |
|-------|-------------|
| **Proprietário** | É preciso ser uma ID de usuário ou ID de registro de equipe válida. |
| **Tipo de proprietário** | Deve ser `systemusers` ou `teams`. |
| **Relação** | Precisa ser uma ID de registro válida, como uma ID de conta ou de registro de contato. |
| **Tipo de relação** | Deve ser um tipo de pesquisa, como `accounts` ou `contacts`. |
| **Cliente** | Precisa ser uma ID de registro válida, como uma ID de conta ou de registro de contato. |
| **Tipo de cliente** | Deve ser o tipo de pesquisa, como `accounts` ou `contacts`. |
|||

Neste exemplo, a ação denominada **Criar um novo registro** cria um novo registro de tarefa:

![Criar registro de tarefas com IDs de registro e os tipos de pesquisa](./media/connectors-create-api-crmonline/create-record-advanced.png)

Essa ação atribui o registro da tarefa a uma ID de usuário ou ID de registro de equipe específica, com base na ID do registro no campo **Proprietário** e o tipo de pesquisa no campo **Tipo de proprietário**:

![ID de registro de proprietário e tipo de pesquisa](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Essa ação também adiciona um registro de conta que está associado à ID de registro adicionada no campo **Relação** e ao tipo de pesquisa no campo **Tipo de relação** campo:

![Com relação à ID de registro e ao tipo de pesquisa](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Localizar ID de registro

Para localizar uma ID de registro, siga estas etapas:

1. No Dynamics 365, abra um registro, como um registro de conta.

2. Na barra de ferramentas de ações, escolha uma destas etapas:

   * Escolha **Pop Out**. ![destacar registro](./media/connectors-create-api-crmonline/popout-record.png) 
   * Escolha **LINK POR EMAIL** para que você possa copiar a URL completa no seu programa de email padrão.

   A ID do registro é exibida na URL entre os caracteres de codificação `%7b` e `%7d`:

   ![Localizar ID de registro](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Solucionar problemas de execuções com falha

Para localizar e examine as etapas com falha no seu aplicativo lógico, exiba o histórico de execuções, status, entradas, saídas e assim por diante do aplicativo lógico.

1. No Portal do Azure, no menu principal do aplicativo lógico, selecione **Visão geral**. Na seção **Histórico de execuções**, que mostra todos os status de execução do seu aplicativo lógico, selecione uma execução com falha para ver mais informações.

   ![Status de execução do aplicativo lógico](./media/connectors-create-api-crmonline/run-history.png)

1. Expanda uma etapa com falha para poder exibir mais.

   ![Expandir etapa com falha](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Examine os detalhes da etapa, como as entradas e saídas, que podem ajudar a encontrar a causa por trás da falha.

   ![Etapa com falha – entradas e saídas](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Para obter mais informações sobre como solucionar problemas de aplicativos lógicos, consulte [Diagnosticando falhas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
