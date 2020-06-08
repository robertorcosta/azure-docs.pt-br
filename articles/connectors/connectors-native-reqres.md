---
title: Receber e responder a chamadas usando HTTPS
description: Manipular solicitações HTTPS de entrada de serviços externos usando Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
tags: connectors
ms.openlocfilehash: 0dea516ea6b938b91fc4b9b833979bcecc285339
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714960"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Receber e responder a solicitações HTTPS de entrada nos Aplicativos Lógicos do Azure

Com [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e o gatilho de solicitação e a ação de resposta internos, você pode criar tarefas automatizadas e fluxos de trabalho que recebem e respondem a solicitações HTTPS de entrada. Por exemplo, você seu aplicativo lógico pode:

* Receber e responder a uma solicitação HTTPS para dados em um banco de dado local.
* Disparar um fluxo de trabalho quando ocorrer um evento de webhook externo.
* Receber e responder a uma chamada HTTPS de outro aplicativo lógico.

O gatilho de solicitação é compatível com a [Autenticação Aberta do Azure Active Directory](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth) para autorizar chamadas de entrada para seu aplicativo lógico. Para obter mais informações sobre como habilitar essa autenticação, consulte [Acesso seguro e dados em Aplicativos Lógicos do Azure – Habilitar a autenticação do Azure AD OAuth](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth).

> [!NOTE]
> O gatilho de solicitação tem suporte *somente* a TLS (segurança de camada de transporte) 1.2 para chamadas de entrada. As chamadas de saída têm suporte a TLS 1.0, 1.1, and 1.2. Para mais informações, consulte [Solucionar o problema do TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Se você obtiver erros de handshake de TLS, use o TLS 1.2. 
> Para chamadas de entrada, estes estão os conjuntos de criptografia com suporte:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Se ainda não estiver familiarizado com aplicativos lógicos, aprenda [como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Adicionar gatilho de solicitação

Esse gatilho interno cria um ponto de extremidade HTTPS que pode ser chamado manualmente e que pode receber *apenas* solicitações HTTPS de entrada. Quando esse evento acontece, o gatilho é acionado e executa o aplicativo lógico. Para obter mais informações sobre a definição JSON subjacente do gatilho e como chamar esse gatilho, consulte o [tipo de gatilho de solicitação](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTPS nos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-http-endpoint.md).

1. Entre no [portal do Azure](https://portal.azure.com). Criar um aplicativo lógico em branco.

1. Depois de abrir o Designer de Aplicativos Lógicos, na caixa de pesquisa, insira `http request` como o filtro. Na lista de gatilhos, selecione o gatilho **Quando uma solicitação HTTP é recebida**, que é a primeira etapa no fluxo de trabalho do aplicativo lógico.

   ![Selecionar gatilho de solicitação](./media/connectors-native-reqres/select-request-trigger.png)

   O gatilho de solicitação mostra essas propriedades:

   ![Gatilho de solicitação](./media/connectors-native-reqres/request-trigger.png)

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {none} | Sim | A URL do ponto de extremidade gerada depois que você salva o aplicativo lógico e usada para chamar seu aplicativo lógico |
   | **Esquema JSON do corpo da solicitação** | `schema` | Não | O esquema JSON que descreve as propriedades e os valores no corpo da solicitação de entrada |
   |||||

1. Na caixa de **Esquema JSON do Corpo da Solicitação**, opcionalmente, insira um esquema JSON que descreva o corpo na solicitação de entrada, por exemplo:

   ![Exemplo de esquema JSON](./media/connectors-native-reqres/provide-json-schema.png)

   O designer usa esse esquema para gerar tokens para as propriedades na solicitação. Dessa forma, seu aplicativo lógico pode analisar, consumir e passar dados da solicitação por meio do gatilho para seu fluxo de trabalho.

   Exemplo do esquema:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Quando você insere um esquema JSON, o designer mostra um lembrete para incluir o cabeçalho `Content-Type` em sua solicitação e definir esse valor de cabeçalho como `application/json`. Para saber mais, veja [Lidar com tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

   ![Lembrete para incluir o cabeçalho "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Veja a aparência desse cabeçalho no formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Para gerar um esquema JSON baseado no payload esperado (dados), você pode usar uma ferramenta como [JSONSchema.net](https://jsonschema.net), ou pode seguir estas etapas:

   1. No gatilho de Solicitação, selecione **Usar o conteúdo de amostra para gerar o esquema**.

      ![Gere o esquema do payload](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Insira o payload de exemplo e selecione **Concluído**.

      ![Gere o esquema do payload](./media/connectors-native-reqres/enter-payload.png)

      Segue o payload de exemplo:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Para especificar propriedades adicionais, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros que você deseja adicionar.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Método** | `method` | Não | O método que as solicitações de entrada devem usar para chamar seu aplicativo lógico |
   | **Caminho relativo** | `relativePath` | Não | O caminho relativo para o parâmetro que a URL do seu ponto de extremidade pode aceitar |
   |||||

   Este exemplo adiciona a propriedade **Método**:

   ![Adicionar parâmetro Método](./media/connectors-native-reqres/add-parameters.png)

   A propriedade **Método** aparece no gatilho para que você possa selecionar um método na lista.

   ![Selecione o método](./media/connectors-native-reqres/select-method.png)

1. Agora, adicione outra ação como a próxima etapa no fluxo de trabalho. No gatilho, selecione **Próxima etapa** para que você possa encontrar a ação que deseja adicionar.

   Por exemplo, você pode responder à solicitação [adicionando uma ação de resposta](#add-response), que pode ser usada para retornar uma resposta personalizada e é descrita posteriormente neste tópico.

   Seu aplicativo lógico mantém a solicitação de entrada aberta por [tempo limitado](../logic-apps/logic-apps-limits-and-config.md#request-limits). Supondo que o fluxo de trabalho do aplicativo lógico inclua uma ação de resposta, se o aplicativo lógico não retornar uma resposta durante esse tempo, seu aplicativo lógico retornará `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se seu aplicativo lógico não incluir uma ação de resposta, ele retornará imediatamente uma resposta `202 ACCEPTED` ao chamador.

1. Quando terminar, salve o aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   Esta etapa gera a URL a ser usada para enviar a solicitação que dispara o aplicativo lógico. Para copiar essa URL, selecione o ícone de cópia ao lado da URL.

   ![URL para usar como gatilho do seu aplicativo lógico](./media/connectors-native-reqres/generated-url.png)

1. Para disparar seu aplicativo lógico, envie um HTTP POST para a URL gerada.

   Por exemplo, você pode usar uma ferramenta como [Postman](https://www.getpostman.com/) para enviar o HTTP POST. Se você [habilitou Autenticação Aberta do Azure Active Directory](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth) para autorizar chamadas de entrada para o gatilho de Solicitação, chame o gatilho usando uma [URL de SAS (Assinatura de Acesso Compartilhado)](../logic-apps/logic-apps-securing-a-logic-app.md#sas) ou um token de autenticação, mas não é possível usar ambos. O token de autenticação deve especificar o tipo `Bearer` no cabeçalho de autorização. Para obter mais informações, consulte [Proteger o acesso e os dados em Aplicativos Lógicos do Azure - Acesso a gatilhos com base em solicitação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers).

Para obter mais informações sobre a definição JSON subjacente do gatilho e como chamar esse gatilho, consulte os tópicos [tipo de gatilho de solicitação](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTPS nos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-http-endpoint.md).

### <a name="trigger-outputs"></a>Saídas do gatilho

Veja mais informações sobre as saídas do gatilho de Solicitação:

| Nome da propriedade JSON | Tipo de dados | Descrição |
|--------------------|-----------|-------------|
| `headers` | Objeto | Um objeto JSON que descreve os cabeçalhos da solicitação |
| `body` | Objeto | Um objeto JSON que descreve o conteúdo do corpo da solicitação |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Adicionar uma ação de resposta

Você pode usar a ação de resposta para responder com um payload (dados) a uma solicitação HTTPS de entrada, mas somente em um aplicativo lógico que é disparado por uma solicitação HTTPS. Você pode adicionar a ação de resposta em qualquer ponto do fluxo de trabalho. Para obter mais informações sobre a definição de JSON subjacente para esse gatilho, consulte o [tipo de ação de Resposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Seu aplicativo lógico mantém a solicitação de entrada aberta por [tempo limitado](../logic-apps/logic-apps-limits-and-config.md#request-limits). Supondo que o fluxo de trabalho do aplicativo lógico inclua uma ação de resposta, se o aplicativo lógico não retornar uma resposta durante esse tempo, seu aplicativo lógico retornará `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se seu aplicativo lógico não incluir uma ação de resposta, ele retornará imediatamente uma resposta `202 ACCEPTED` ao chamador.

> [!IMPORTANT]
> Se uma ação de resposta incluir esses cabeçalhos, os Aplicativos Lógicos removerão esses cabeçalhos da mensagem de resposta gerada sem mostrar nenhum aviso ou erro:
>
> * `Allow`
> * `Content-*` com estas exceções: `Content-Disposition`, `Content-Encoding` e `Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Embora os Aplicativos Lógicos não impeçam que você salve os aplicativos lógicos que têm uma ação de resposta com esses cabeçalhos, os Aplicativos Lógicos ignoram esses cabeçalhos.

1. No Designer do Aplicativo Lógico, na etapa em que você deseja adicionar uma ação de resposta, selecione **Nova etapa**.

   Por exemplo, usando o gatilho de Solicitação anterior:

   ![Adicionar nova etapa](./media/connectors-native-reqres/add-response.png)

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o sinal mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira “resposta” como o filtro e selecione a ação **Resposta**.

   ![Selecionar a ação de resposta](./media/connectors-native-reqres/select-response-action.png)

   O gatilho de solicitação é recolhido neste exemplo para simplificar.

1. Adicione quaisquer valores necessários para a mensagem de resposta. 

   Em alguns campos, clicar dentro de suas caixas abre a lista de conteúdo dinâmico. Em seguida, você pode selecionar tokens que representam as saídas disponíveis de etapas anteriores no fluxo de trabalho. As propriedades do esquema especificado no exemplo anterior agora aparecem na lista de conteúdo dinâmico.

   Por exemplo, para a caixa **Cabeçalhos**, inclua `Content-Type` como o nome da chave e defina o valor da chave como `application/json`, conforme mencionado anteriormente neste tópico. Para a caixa **Corpo**, você pode selecionar a saída do corpo do gatilho na lista de conteúdo dinâmico.

   ![Detalhes da ação de resposta](./media/connectors-native-reqres/response-details.png)

   Para exibir os cabeçalhos no formato JSON, selecione **Alternar para exibição de texto**.

   ![Cabeçalhos - Mudar para a exibição de texto](./media/connectors-native-reqres/switch-to-text-view.png)

   Para obter mais informações sobre as propriedades que podem ser definidas na ação de resposta. 

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Código de status** | `statusCode` | Sim | O código de status a ser retornado na resposta |
   | **Cabeçalhos** | `headers` | Não | Um objeto JSON que descreve um ou mais cabeçalhos a serem incluídos na resposta |
   | **Corpo** | `body` | Não | O corpo da resposta |
   |||||

1. Para especificar propriedades adicionais, como um esquema JSON para o corpo da resposta, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros que você deseja adicionar.

1. Quando terminar, salve o aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer. 

## <a name="next-steps"></a>Próximas etapas

* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)
