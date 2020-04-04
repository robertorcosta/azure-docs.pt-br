---
title: Aguarde e responda aos eventos
description: Automatize fluxos de trabalho que acionam, pausam e retomam com base em eventos em um ponto final de serviço usando aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656292"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Crie e execute fluxos de trabalho automatizados baseados em eventos usando webhooks HTTP em Aplicativos de Lógica do Azure

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector HTTP Webhook incorporado, você pode automatizar fluxos de trabalho que esperam e executam com base em eventos específicos que acontecem em um ponto final HTTP ou HTTPS através da construção de aplicativos lógicos. Por exemplo, você pode criar um aplicativo lógico que monitora um ponto final de serviço esperando por um evento específico antes de acionar o fluxo de trabalho e executar as ações especificadas, em vez de verificar ou *verificar* regularmente esse ponto final.

Aqui estão alguns exemplos de fluxos de trabalho baseados em eventos:

* Aguarde que um item chegue de um [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) antes de acionar um aplicativo lógico executado.
* Aguarde uma aprovação antes de continuar um fluxo de trabalho.

## <a name="how-do-webhooks-work"></a>Como funcionam os webhooks?

Um gatilho http webhook é baseado em eventos, que não depende de verificação ou pesquisa regular para novos itens. Quando você salva um aplicativo lógico que começa com um gatilho de webhook, ou quando você altera seu aplicativo lógico de desativado para ativado, o gatilho webhook *assina* um serviço ou ponto final específico registrando uma *URL de retorno de chamada* com esse serviço ou ponto final. O gatilho então aguarda que o serviço ou ponto final ligue para a URL, que começa a executar o aplicativo lógico. Semelhante ao [gatilho Solicitação,](connectors-native-reqres.md)o aplicativo lógico é acionado imediatamente quando o evento especificado acontece. O gatilho *cancela* a assinatura do serviço ou ponto final se você remover o gatilho e salvar seu aplicativo lógico, ou quando você alterar seu aplicativo lógico de ativado para desativado.

Uma ação http webhook também é baseada em eventos e *assina* um serviço ou ponto final específico registrando uma *URL de retorno* de chamada com esse serviço ou ponto final. A ação webhook pausa o fluxo de trabalho do aplicativo lógico e espera até que o serviço ou ponto final chame a URL antes que o aplicativo lógico seja retomado. O aplicativo de lógica de ação *não é subscrito* a partir do serviço ou ponto final nesses casos:

* Quando a ação webhook termina com sucesso
* Se a execução do aplicativo lógico for cancelada enquanto aguarda uma resposta
* Antes que o aplicativo lógico averie

Por exemplo, a ação de [**e-mail**](connectors-create-api-office365-outlook.md) de aprovação do Connector Send do Office 365 Outlook é um exemplo de ação de webhook que segue esse padrão. Você pode estender esse padrão para qualquer serviço usando a ação webhook.

> [!NOTE]
> O Logic Apps impõe o TLS (Transport Layer Security, segurança de camada de transporte) 1.2 ao receber a chamada de volta ao gatilho ou ação do webhook HTTP. Se você vir erros de aperto de mão TLS, certifique-se de usar o TLS 1.2. Para chamadas recebidas, aqui estão as suítes de cifra suportadas:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Para saber mais, consulte esses tópicos:

* [Parâmetros do gatilho HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks e assinaturas](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Crie APIs personalizadas que suportam um webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL de um ponto final ou API já implantado que suporta o padrão de assinatura e cancelamento de assinatura do webhook para [gatilhos de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou [ações de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-actions) conforme apropriado

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico onde você deseja esperar por eventos específicos no ponto final do destino. Para começar com o gatilho HTTP Webhook, [crie um aplicativo de lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP Webhook, inicie seu aplicativo lógico com qualquer gatilho que você quiser. Este exemplo usa o gatilho HTTP como o primeiro passo.

## <a name="add-an-http-webhook-trigger"></a>Adicione um gatilho HTTP Webhook

Este gatilho incorporado chama o ponto final de assinatura no serviço de destino e registra uma URL de retorno de chamada com o serviço de destino. Seu aplicativo lógico então espera que o `HTTP POST` serviço de destino envie uma solicitação para a URL de retorno de chamada. Quando esse evento acontece, o gatilho é acionado e passa qualquer dado na solicitação junto ao fluxo de trabalho.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo de lógica em branco no Logic App Designer.

1. Na caixa de pesquisa do `http webhook` designer, digite como seu filtro. Na lista **Triggers,** selecione o gatilho **HTTP Webhook.**

   ![Selecione o gatilho HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Este exemplo renomeia `HTTP Webhook trigger` o gatilho para que o passo tenha um nome mais descritivo. Além disso, o exemplo mais tarde adiciona uma ação HTTP Webhook, e ambos os nomes devem ser únicos.

1. Forneça os valores para os parâmetros de [gatilho HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que você deseja usar para as chamadas de subscrição e cancelamento de assinatura.

   Neste exemplo, o gatilho inclui os métodos, URIs e órgãos de mensagem para usar ao executar as operações de subscrição e cancelamento de assinatura.

   ![Digite parâmetros de gatilho HTTP Webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Assinatura - Método** | Sim | O método a ser usado ao se inscrever no ponto final do destino |
   | **Inscreva-se - URI** | Sim | A URL a ser usada para assinar o ponto final do destino |
   | **Inscreva-se - Corpo** | Não | Qualquer órgão de mensagem a ser inserido na solicitação de inscrição. Este exemplo inclui a URL de retorno de chamada que identifica exclusivamente `@listCallbackUrl()` o assinante, que é o seu aplicativo lógico, usando a expressão para recuperar a URL de retorno de chamada do seu aplicativo lógico. |
   | **Cancelar a inscrição - Método** | Não | O método a ser usado ao não assinar a partir do ponto final do alvo |
   | **Cancelar a inscrição - URI** | Não | A URL a ser usada para não assinar a partir do ponto final do destino |
   | **Cancelar inscrição - Corpo** | Não | Um órgão de mensagem opcional para incluir na solicitação de cancelamento de inscrição <p><p>**Nota:** Esta propriedade não `listCallbackUrl()` suporta o uso da função. No entanto, o gatilho inclui e `x-ms-client-tracking-id` envia `x-ms-workflow-operation-name`automaticamente os cabeçalhos, e , que o serviço de destino pode usar para identificar exclusivamente o assinante. |
   ||||

1. Para adicionar outras propriedades de gatilho, abra a lista **Adicionar novos parâmetros.**

   ![Adicione mais propriedades de gatilho](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Por exemplo, se você precisar usar a autenticação, você pode adicionar as propriedades **Assinar - Autenticação** e **Cancelar - Autenticação.** Para obter mais informações sobre os tipos de autenticação disponíveis para http webhook, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando terminar, pronto, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   Salvar seu aplicativo lógico chama o ponto final de assinatura no serviço de destino e registra a URL de retorno de chamada. Seu aplicativo lógico então espera que o `HTTP POST` serviço de destino envie uma solicitação para a URL de retorno de chamada. Quando esse evento acontece, o gatilho é acionado e passa qualquer dado na solicitação junto ao fluxo de trabalho. Se essa operação for concluída com sucesso, o gatilho cancelará a inscrição a partir do ponto final e seu aplicativo lógico continuará o fluxo de trabalho restante.

## <a name="add-an-http-webhook-action"></a>Adicione uma ação HTTP Webhook

Essa ação incorporada chama o ponto final de assinatura no serviço de destino e registra uma URL de retorno de chamada com o serviço de destino. Seu aplicativo lógico então pausa e espera que `HTTP POST` o serviço de destino envie uma solicitação para a URL de retorno de chamada. Quando esse evento acontece, a ação passa todos os dados da solicitação ao longo do fluxo de trabalho. Se a operação for concluída com sucesso, a ação cancelará a inscrição a partir do ponto final e seu aplicativo lógico continuará executando o fluxo de trabalho restante.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

   Este exemplo usa o gatilho HTTP Webhook como o primeiro passo.

1. Na etapa em que deseja adicionar a ação HTTP Webhook, selecione **Novo passo**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa do `http webhook` designer, digite como seu filtro. Na lista **Ações,** selecione a ação **HTTP Webhook.**

   ![Selecione a ação HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Este exemplo renomeia a ação para "ação HTTP Webhook" para que a etapa tenha um nome mais descritivo.

1. Forneça os valores para os parâmetros de ação HTTP Webhook, que são semelhantes aos parâmetros de [gatilho HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger), que você deseja usar para as chamadas de assinatura e cancelamento de assinatura.

   Neste exemplo, a ação inclui os métodos, URIs e órgãos de mensagem para usar ao realizar as operações de subscrição e cancelamento de assinatura.

   ![Digite os parâmetros de ação HTTP Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Assinatura - Método** | Sim | O método a ser usado ao se inscrever no ponto final do destino |
   | **Inscreva-se - URI** | Sim | A URL a ser usada para assinar o ponto final do destino |
   | **Inscreva-se - Corpo** | Não | Qualquer órgão de mensagem a ser inserido na solicitação de inscrição. Este exemplo inclui a URL de retorno de chamada que identifica exclusivamente `@listCallbackUrl()` o assinante, que é o seu aplicativo lógico, usando a expressão para recuperar a URL de retorno de chamada do seu aplicativo lógico. |
   | **Cancelar a inscrição - Método** | Não | O método a ser usado ao não assinar a partir do ponto final do alvo |
   | **Cancelar a inscrição - URI** | Não | A URL a ser usada para não assinar a partir do ponto final do destino |
   | **Cancelar inscrição - Corpo** | Não | Um órgão de mensagem opcional para incluir na solicitação de cancelamento de inscrição <p><p>**Nota:** Esta propriedade não `listCallbackUrl()` suporta o uso da função. No entanto, a ação inclui e `x-ms-client-tracking-id` envia `x-ms-workflow-operation-name`automaticamente os cabeçalhos, e , que o serviço de destino pode usar para identificar exclusivamente o assinante. |
   ||||

1. Para adicionar outras propriedades de ação, abra a lista **Adicionar novos parâmetros.**

   ![Adicione mais propriedades de ação](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Por exemplo, se você precisar usar a autenticação, você pode adicionar as propriedades **Assinar - Autenticação** e **Cancelar - Autenticação.** Para obter mais informações sobre os tipos de autenticação disponíveis para http webhook, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   Agora, quando essa ação é executada, seu aplicativo lógico chama o ponto final de assinatura no serviço de destino e registra a URL de retorno de chamada. O aplicativo lógico então pausa o fluxo de trabalho e `HTTP POST` aguarda que o serviço de destino envie uma solicitação para a URL de retorno de chamada. Quando esse evento acontece, a ação passa todos os dados da solicitação ao longo do fluxo de trabalho. Se a operação for concluída com sucesso, a ação cancelará a inscrição a partir do ponto final e seu aplicativo lógico continuará executando o fluxo de trabalho restante.

## <a name="connector-reference"></a>Referência de conector

Para obter mais informações sobre parâmetros de gatilho e ação, que são semelhantes entre si, consulte [os parâmetros HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Detalhes de saída

Aqui estão mais informações sobre as saídas de um gatilho ou ação http webhook, que retorna essas informações:

| Nome da propriedade | Type | Descrição |
|---------------|------|-------------|
| headers | objeto | Os cabeçalhos da solicitação |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo a partir da solicitação |
| código de status | INT | O código de status da solicitação |
|||

| Código de status | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceita |
| 400 | Solicitação incorreta |
| 401 | Não Autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
