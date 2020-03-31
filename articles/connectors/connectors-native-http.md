---
title: Chamar pontos finais do serviço usando HTTP ou HTTPS
description: Envie solicitações HTTP ou HTTPS de saída para pontos finais de serviço dos Aplicativos Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 8aefe851708c0b8d8780d03e4364e034e783bf4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297179"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Chamar pontos finais de serviço em HTTP ou HTTPS de Aplicativos Azure Logic

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o gatilho ou ação HTTP incorporado, você pode criar tarefas e fluxos de trabalho automatizados que enviam solicitações para pontos finais de serviço em HTTP ou HTTPS. Por exemplo, você pode monitorar o ponto final do serviço para o seu site verificando esse ponto final em um horário específico. Quando o evento especificado acontece nesse ponto final, como a queda do seu site, o evento aciona o fluxo de trabalho do seu aplicativo lógico e executa as ações nesse fluxo de trabalho. Se você quiser receber e responder às chamadas HTTPS de entrada, use o gatilho de solicitação incorporado [ou a ação Resposta](../connectors/connectors-native-reqres.md).

> [!NOTE]
> Com base na capacidade do ponto final de destino, o conector HTTP suporta as versões TLS (Transport Layer Security, segurança da camada de transporte) 1.0, 1.1 e 1.2. Logic Apps negocia com o ponto final sobre o uso da versão mais suportada possível. Assim, por exemplo, se o ponto final suporta 1.2, o conector usa 1.2 primeiro. Caso contrário, o conector usa a próxima versão mais alta suportada.

Para verificar ou *sondar* um ponto final em um cronograma recorrente, [adicione o gatilho HTTP](#http-trigger) como o primeiro passo no seu fluxo de trabalho. Cada vez que o gatilho verifica o ponto final, o gatilho liga ou envia uma *solicitação* para o ponto final. A resposta do ponto de extremidade determina se o fluxo de trabalho do aplicativo lógico é executado. O gatilho passa qualquer conteúdo da resposta do ponto final às ações do seu aplicativo lógico.

Para chamar um ponto final de qualquer outro lugar em seu fluxo de trabalho, [adicione a ação HTTP](#http-action). A resposta do ponto de extremidade determina como as ações restantes do fluxo de trabalho são executadas.

> [!IMPORTANT]
> Se um gatilho ou ação HTTP incluir esses cabeçalhos, o Logic Apps removerá esses cabeçalhos da mensagem de solicitação gerada sem mostrar qualquer aviso ou erro:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`com essas exceções: `Content-Disposition`, `Content-Encoding`e`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Embora o Logic Apps não o impeça de salvar aplicativos lógicos que usam um gatilho ou ação HTTP com esses cabeçalhos, o Logic Apps ignora esses cabeçalhos.

Este artigo mostra como adicionar um gatilho ou ação HTTP ao fluxo de trabalho do seu aplicativo lógico.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL para o ponto final de destino que você deseja chamar

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* O aplicativo lógico de onde você deseja chamar o ponto final do destino. Para começar com o gatilho HTTP, [crie um aplicativo de lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP, inicie seu aplicativo lógico com qualquer gatilho que você quiser. Este exemplo usa o gatilho HTTP como o primeiro passo.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Adicione um gatilho HTTP

Este gatilho incorporado faz uma chamada HTTP para a URL especificada para um ponto final e retorna uma resposta.

1. Faça login no [portal Azure](https://portal.azure.com). Abra seu aplicativo de lógica em branco no Logic App Designer.

1. Na caixa de pesquisa do designer, **selecione Built-in**. Na caixa de pesquisa, insira `http` como o filtro. Na lista **Triggers,** selecione o gatilho **HTTP.**

   ![Selecionar o gatilho HTTP](./media/connectors-native-http/select-http-trigger.png)

   Este exemplo renomeia o gatilho para "gatilho HTTP" para que a etapa tenha um nome mais descritivo. Além disso, o exemplo mais tarde adiciona uma ação HTTP, e ambos os nomes devem ser únicos.

1. Forneça os valores para os [parâmetros de gatilho HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) que você deseja incluir na chamada para o ponto final do destino. Configure a recorrência de quantas vezes você deseja que o gatilho verifique o ponto final do destino.

   ![Insira os parâmetros do gatilho HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Se você selecionar um tipo de autenticação diferente de **Nenhum,** as configurações de autenticação diferem com base na sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades gerenciadas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novos parâmetros** e selecione os parâmetros desejados.

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Adicione uma ação HTTP

Essa ação incorporada faz uma chamada HTTP para a URL especificada para um ponto final e retorna uma resposta.

1. Faça login no [portal Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

   Este exemplo usa o gatilho HTTP como o primeiro passo.

1. Na etapa em que deseja adicionar a ação HTTP, selecione **Novo passo**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `http` como o filtro. Na lista **Ações,** selecione a ação **HTTP.**

   ![Selecionar a ação HTTP](./media/connectors-native-http/select-http-action.png)

   Este exemplo renomeia a ação para "ação HTTP" para que a etapa tenha um nome mais descritivo.

1. Forneça os valores para os [parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) que você deseja incluir na chamada para o ponto final de destino.

   ![Inserir os parâmetros da ação HTTP](./media/connectors-native-http/http-action-parameters.png)

   Se você selecionar um tipo de autenticação diferente de **Nenhum,** as configurações de autenticação diferem com base na sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades gerenciadas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novos parâmetros** e selecione os parâmetros desejados.

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="content-with-multipartform-data-type"></a>Conteúdo com tipo de dados multiparte/formulário

Para lidar com `multipart/form-data` o conteúdo que tenha solicitação de tipo em `$content-type` HTTP, você pode adicionar um objeto JSON que inclui os atributos e `$multipart` atributos ao corpo da solicitação HTTP usando este formato.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Por exemplo, suponha que você tenha um aplicativo lógico que envie uma solicitação HTTP POST para `multipart/form-data` um arquivo Excel para um site usando a API desse site, que suporta o tipo. Veja como essa ação pode parecer:

![Dados de formulários multipartes](./media/connectors-native-http/http-action-multipart.png)

Aqui está o mesmo exemplo que mostra a definição JSON da ação HTTP na definição de fluxo de trabalho subjacente:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Referência de conector

Para obter mais informações sobre parâmetros de gatilho e ação, consulte estas seções:

* [Parâmetros do gatilho HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Detalhes de saída

Aqui estão mais informações sobre as saídas de um gatilho ou ação HTTP, que retorna essas informações:

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
