---
title: Receber e responder a chamadas usando HTTPS
description: Lidar com solicitações HTTPS de entrada de serviços externos usando aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 1885d7f8713b3801ce0c9846b7a8509b3864032a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656293"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Receba e responda às solicitações HTTPS de entrada no Azure Logic Apps

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o gatilho de solicitação incorporado ou ação de resposta, você pode criar tarefas e fluxos de trabalho automatizados que recebem e respondem às solicitações HTTPS recebidas. Por exemplo, você pode ter seu aplicativo lógico:

* Receba e responda a uma solicitação HTTPS de dados em um banco de dados local.
* Acione um fluxo de trabalho quando um evento de webhook externo acontecer.
* Receba e responda a uma chamada HTTPS de outro aplicativo lógico.

> [!NOTE]
> O gatilho Solicitação suporta *apenas* o TLS (Transport Layer Security, segurança de camada de transporte) 1.2 para chamadas recebidas. As chamadas de saída continuam a suportar o TLS 1.0, 1.1 e 1.2. Para obter mais informações, consulte [Resolvendo o problema do TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Se você vir erros de aperto de mão TLS, certifique-se de usar o TLS 1.2. Para chamadas recebidas, aqui estão as suítes de cifra suportadas:
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

* Uma assinatura do Azure. Se você não tiver uma assinatura, você pode [se inscrever em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Se você é novo em aplicativos lógicos, [aprenda a criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Adicionar gatilho de solicitação

Este gatilho incorporado cria um ponto final HTTPS manualmente callable que pode receber *apenas* solicitações HTTPS recebidas. Quando esse evento acontece, o gatilho é acionado e executa o aplicativo lógico. Para obter mais informações sobre a definição JSON subjacente do gatilho e como chamar esse gatilho, consulte o [tipo de gatilho de solicitação](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [fluxos de trabalho de chamada, gatilho ou ninho com pontos finais HTTP no Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Entre no [portal do Azure](https://portal.azure.com). Criar um aplicativo lógico em branco.

1. Depois que o Logic App Designer for aberto, na caixa de pesquisa, digite "http request" como seu filtro. Na lista de gatilhos, selecione o **gatilho Quando uma solicitação HTTP for recebida,** que é o primeiro passo no fluxo de trabalho do aplicativo lógico.

   ![Seleleta Ativar solicitação](./media/connectors-native-reqres/select-request-trigger.png)

   O gatilho Solicitar mostra essas propriedades:

   ![Gatilho de solicitação](./media/connectors-native-reqres/request-trigger.png)

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **URL HTTP POST** | {none} | Sim | A URL de ponto final gerada após salvar o aplicativo lógico e é usada para chamar seu aplicativo lógico |
   | **Solicitar corpo JSON Schema** | `schema` | Não | O esquema JSON que descreve as propriedades e valores no órgão de solicitação de entrada |
   |||||

1. Na caixa **Esquema JSON, insira** opcionalmente um esquema JSON que descreve o corpo na solicitação recebida, por exemplo:

   ![Exemplo esquema JSON](./media/connectors-native-reqres/provide-json-schema.png)

   O designer usa esse esquema para gerar tokens para as propriedades na solicitação. Dessa forma, seu aplicativo lógico pode analisar, consumir e repassar dados da solicitação através do gatilho para o seu fluxo de trabalho.

   Aqui está o esquema de amostra:

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

   Quando você insere um esquema JSON, o `Content-Type` designer mostra um lembrete para `application/json`incluir o cabeçalho em sua solicitação e definir esse valor de cabeçalho para . Para obter mais informações, consulte [Lidar com os tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

   ![Lembrete para incluir cabeçalho "Tipo de conteúdo"](./media/connectors-native-reqres/include-content-type.png)

   Aqui está como este cabeçalho se parece no formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Para gerar um esquema JSON baseado na carga útil esperada (dados), você pode usar uma ferramenta como [JSONSchema.net](https://jsonschema.net), ou você pode seguir estas etapas:

   1. No gatilho de Solicitação, selecione **Usar o conteúdo de amostra para gerar o esquema**.

      ![Gerar esquema a partir da carga útil](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Digite a carga de amostra e selecione **Feito**.

      ![Gerar esquema a partir da carga útil](./media/connectors-native-reqres/enter-payload.png)

      Aqui está a carga de amostra:

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

1. Para especificar propriedades adicionais, abra a lista **Adicionar novos parâmetros** e selecione os parâmetros que deseja adicionar.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Método** | `method` | Não | O método que a solicitação de entrada deve usar para chamar o aplicativo lógico |
   | **Caminho relativo** | `relativePath` | Não | O caminho relativo para o parâmetro que a URL de ponto final do aplicativo lógico pode aceitar |
   |||||

   Este exemplo adiciona a propriedade **Método:**

   ![Adicionar parâmetro de método](./media/connectors-native-reqres/add-parameters.png)

   A propriedade **Método** aparece no gatilho para que você possa selecionar um método da lista.

   ![Seleleta método](./media/connectors-native-reqres/select-method.png)

1. Agora, adicione outra ação como o próximo passo em seu fluxo de trabalho. Sob o gatilho, selecione **Próximo passo** para que você possa encontrar a ação que deseja adicionar.

   Por exemplo, você pode responder à solicitação [adicionando uma ação de resposta](#add-response), que você pode usar para retornar uma resposta personalizada e é descrita mais tarde neste tópico.

   Seu aplicativo lógico mantém a solicitação de entrada aberta apenas por um minuto. Supondo que seu fluxo de trabalho de aplicativo lógico inclua uma ação de resposta, se `504 GATEWAY TIMEOUT` o aplicativo lógico não retornar uma resposta depois que esse tempo passar, seu aplicativo lógico retorna a ao chamador. Caso contrário, se o seu aplicativo lógico não incluir uma ação `202 ACCEPTED` de resposta, seu aplicativo de lógica retorna imediatamente uma resposta ao chamador.

1. Quando terminar, salve o aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer. 

   Essa etapa gera a URL a ser usada para o envio da solicitação que aciona o aplicativo lógico. Para copiar essa URL, selecione o ícone de cópia ao lado da URL.

   ![URL para usar o acionamento do seu aplicativo lógico](./media/connectors-native-reqres/generated-url.png)

1. Para acionar seu aplicativo lógico, envie um POST HTTP para a URL gerada. Por exemplo, você pode usar uma ferramenta como [o Carteiro](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Saídas do gatilho

Aqui estão mais informações sobre as saídas do gatilho Solicitação:

| Nome da propriedade JSON | Tipo de dados | Descrição |
|--------------------|-----------|-------------|
| `headers` | Objeto | Um objeto JSON que descreve os cabeçalhos da solicitação |
| `body` | Objeto | Um objeto JSON que descreve o conteúdo do corpo a partir da solicitação |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Adicionar uma ação de resposta

Você pode usar a ação Resposta para responder com uma carga (dados) a uma solicitação HTTPS recebida, mas apenas em um aplicativo lógico que é acionado por uma solicitação HTTPS. Você pode adicionar a ação Resposta a qualquer momento do seu fluxo de trabalho. Para obter mais informações sobre a definição JSON subjacente para este gatilho, consulte o [tipo de ação Resposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Seu aplicativo lógico mantém a solicitação de entrada aberta apenas por um minuto. Supondo que seu fluxo de trabalho de aplicativo lógico inclua uma ação de resposta, se `504 GATEWAY TIMEOUT` o aplicativo lógico não retornar uma resposta depois que esse tempo passar, seu aplicativo lógico retorna a ao chamador. Caso contrário, se o seu aplicativo lógico não incluir uma ação `202 ACCEPTED` de resposta, seu aplicativo de lógica retorna imediatamente uma resposta ao chamador.

> [!IMPORTANT]
> Se uma ação de resposta incluir esses cabeçalhos, o Logic Apps removerá esses cabeçalhos da mensagem de resposta gerada sem mostrar qualquer aviso ou erro:
>
> * `Allow`
> * `Content-*`com essas exceções: `Content-Disposition`, `Content-Encoding`e`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Embora o Logic Apps não o impeça de salvar aplicativos lógicos que tenham uma ação de resposta com esses cabeçalhos, o Logic Apps ignora esses cabeçalhos.

1. No Logic App Designer, sob o passo em que você deseja adicionar uma ação resposta, selecione **Nova etapa**.

   Por exemplo, usando o gatilho Solicitar anteriormente:

   ![Adicionar novo passo](./media/connectors-native-reqres/add-response.png)

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Em **Escolha uma ação**, na caixa de pesquisa, digite "resposta" como seu filtro e selecione a ação **Resposta.**

   ![Selecione a ação Resposta](./media/connectors-native-reqres/select-response-action.png)

   O gatilho Solicitar é colapsado neste exemplo para a simplicidade.

1. Adicione todos os valores necessários para a mensagem de resposta. 

   Em alguns campos, clicar dentro de suas caixas abre a lista de conteúdo dinâmico. Em seguida, você pode selecionar tokens que representam saídas disponíveis de etapas anteriores no fluxo de trabalho. As propriedades do esquema especificado no exemplo anterior agora aparecem na lista de conteúdo dinâmico.

   Por exemplo, para a caixa `Content-Type` **Cabeçalhos,** inclua como `application/json` nome-chave e defina o valor-chave como mencionado anteriormente neste tópico. Para a caixa **Corpo,** você pode selecionar a saída do corpo do gatilho da lista de conteúdo dinâmico.

   ![Detalhes da ação de resposta](./media/connectors-native-reqres/response-details.png)

   Para exibir os cabeçalhos no formato JSON, selecione **'Alterar para exibição de texto ''''''''''''''**

   ![Cabeçalhos - Mude para exibição de texto](./media/connectors-native-reqres/switch-to-text-view.png)

   Aqui estão mais informações sobre as propriedades que você pode definir na ação Resposta. 

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Código de status** | `statusCode` | Sim | O código de status para retornar na resposta |
   | **Cabeçalhos** | `headers` | Não | Um objeto JSON que descreve um ou mais cabeçalhos para incluir na resposta |
   | **Corpo** | `body` | Não | O corpo da resposta |
   |||||

1. Para especificar propriedades adicionais, como um esquema JSON para o corpo de resposta, abra a lista **Adicionar novos parâmetros** e selecione os parâmetros que deseja adicionar.

1. Quando terminar, salve o aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer. 

## <a name="next-steps"></a>Próximas etapas

* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)
