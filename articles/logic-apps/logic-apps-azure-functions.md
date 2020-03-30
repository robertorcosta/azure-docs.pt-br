---
title: Adicionar e chamar funções do Azure a partir de aplicativos de lógica do Azure
description: Ligue e execute código personalizado em suas funções do Azure a partir de tarefas e fluxos de trabalho automatizados em Aplicativos de Lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284116"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Ligue para as funções do Azure a partir de aplicativos de lógica do Azure

Quando você deseja executar um código que executa um trabalho específico em seus aplicativos lógicos, você pode criar sua própria função usando [funções do Azure](../azure-functions/functions-overview.md). Este serviço ajuda você a criar funções Node.js, C#e F# para que você não precise construir um aplicativo ou infra-estrutura completo para executar o código. Você também pode [chamar aplicativos lógicos de dentro de funções do Azure](#call-logic-app). O Azure Functions fornece computação sem servidor na nuvem e é útil para executar tarefas, como estes exemplos:

* Estenda o comportamento do seu aplicativo lógico com funções em Node. js ou C#.
* Execute cálculos em seu fluxo de trabalho do aplicativo lógico.
* Aplicar a formatação avançada ou computação de campos em seus aplicativos lógicos.

Para executar trechos de código sem criar funções do Azure, aprenda a [adicionar e executar códigos inline](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> A integração entre aplicativos lógicos e funções do Azure atualmente não funciona com slots ativados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo de função Azure, que é um contêiner para funções do Azure, juntamente com sua função Azure. Se você não tiver um aplicativo de funções, [crie primeiro o aplicativo de função](../azure-functions/functions-create-first-azure-function.md). Você pode então criar sua função fora do seu aplicativo lógico no portal Azure, ou [de dentro do seu aplicativo lógico](#create-function-designer) no Logic App Designer.

* Ao trabalhar com aplicativos lógicos, os mesmos requisitos se aplicam a aplicativos e funções de função, sejam eles existentes ou novos:

  * Seu aplicativo de função e aplicativo lógico deve usar a mesma assinatura do Azure.

  * Os novos aplicativos de função devem usar o .NET ou o JavaScript como pilha de tempo de execução. Quando você adiciona uma nova função aos aplicativos de função existentes, você pode selecionar C# ou JavaScript.

  * Sua função usa o modelo **de gatilho HTTP.**

    Esse modelo de gatilho HTTP pode aceitar o conteúdo que tenha o tipo `application/json` do seu aplicativo lógico. Quando você adiciona uma função Azure ao seu aplicativo lógico, o Logic App Designer mostra funções personalizadas que são criadas a partir deste modelo dentro de sua assinatura do Azure.

  * Sua função não usa rotas personalizadas a menos que você tenha definido uma [definição OpenAPI](../azure-functions/functions-openapi-definition.md) (anteriormente conhecida como um [arquivo Swagger](https://swagger.io/)).

  * Se você tiver uma definição OpenAPI para sua função, o Logic Apps Designer lhe dá uma experiência mais rica quando seu trabalho com parâmetros de função. Antes de seu aplicativo lógico pode localizar e acessar funções que têm definições de OpenAPI, [configurar seu aplicativo de função seguindo estas etapas](#function-swagger).

* O aplicativo lógico no qual você deseja adicionar a função, incluindo uma [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico

  Antes de adicionar ações que executam funções, seu aplicativo lógico deve começar com um gatilho. Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Encontre funções que tenham descrições OpenAPI

Para uma experiência mais rica quando você trabalha com parâmetros de função no Logic Apps Designer, [gere uma definição OpenAPI](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como [arquivo Swagger](https://swagger.io/), para sua função. Para configurar seu aplicativo de função, para que seu aplicativo lógico possa encontrar e usar funções com descrições de Swagger, siga estas etapas:

1. Certifique-se de que seu aplicativo de função está em execução ativa.

1. Em seu aplicativo de função, configure [o CORS (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens sejam permitidas seguindo estas etapas:

   1. Na lista **Function Apps,** selecione seu aplicativo de função. No painel direito, selecione **Plataforma com recursos** > **cors**.

      ![Selecione seu aplicativo de função> "Recursos da plataforma"> "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Em **CORS,** adicione o**`*`** caractere curinga asterisco ( ) mas remova todas as outras origens da lista e selecione **Salvar**.

      ![Defina "CORS* como o caractere curinga "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Valores de propriedade de acesso dentro de solicitações HTTP

As funções do Webhook podem aceitar solicitações HTTP como entradas e passar essas solicitações para outras funções. Por exemplo, embora o Logic Apps possua [funções que convertem valores de DateTime](../logic-apps/workflow-definition-language-functions-reference.md), essa função JavaScript de amostra básica mostra como você pode acessar uma propriedade dentro de um objeto de solicitação transmitido à função e executar operações nesse valor de propriedade. Para acessar propriedades dentro de objetos, este exemplo usa o [operador ponto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors):

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Aqui está o que acontece dentro dessa função:

1. A função cria uma variável `data` e atribui o objeto `body` dentro do objeto `request` a essa variável. A função usa o operador ponto (.) para fazer referência a `body` dentro do objeto a `request` objeto:

   ```javascript
   var data = request.body;
   ```

1. A função agora pode acessar a propriedade `date` por meio da variável `data` e converter esse valor de propriedade do tipo DateTime para o tipo DateString chamando a função `ToDateString()`. A função também retorna o resultado através da propriedade `body` na resposta da função:

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que você criou sua função do Azure, siga as etapas de como [ adicionar funções a aplicativos lógicos ](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Criar funções dentro de aplicativos lógicos

Antes de criar uma função Azure a partir de dentro do seu aplicativo lógico usando o Logic App Designer, você deve primeiro ter um aplicativo de função Azure, que é um contêiner para suas funções. Se você não tiver um aplicativo de funções, crie primeiro o aplicativo de função. Veja [Crie sua primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md).

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Para criar e adicionar sua função, siga a etapa que se aplica ao seu cenário:

   * Na última etapa do fluxo de trabalho do seu aplicativo lógico, selecione **Nova etapa**.

   * Entre as etapas existentes no fluxo de trabalho do aplicativo lógico, mova o mouse sobre a seta, selecione o sinal de mais (+) e selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "funções azure" como seu filtro. Na lista de ações, selecione a **função Escolher um Azure,** por exemplo:

   ![Localizar "Funções do Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois que a lista de ações for aberta, selecione esta ação: **Criar nova função**

   ![Selecione seu aplicativo de funções](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. No editor de definição de função, defina sua função:

   1. Na caixa **Nome da função**, forneça um nome para sua função.

   1. Na caixa **Código,** adicione seu código ao modelo de função, incluindo a resposta e a carga que você deseja retornar ao seu aplicativo lógico depois que sua função terminar de ser em execução. Quando terminar, selecione **Criar**.

   Por exemplo: 

   ![Definir sua função](./media/logic-apps-azure-functions/add-code-function-definition.png)

   No código do modelo, * `context` * o objeto refere-se à mensagem que seu aplicativo lógico envia através do campo **Solicitar corpo** em uma etapa posterior. Para acessar as propriedades do objeto `context` de dentro de sua função, use a seguinte sintaxe:

   `context.body.<property-name>`

   Por exemplo, para fazer referência à propriedade `content` dentro do objeto `context`, use a seguinte sintaxe:

   `context.body.content`

   O código de modelo também inclui uma variável `input`, que armazena o valor do parâmetro `data` para que sua função possa executar operações nesse valor. Dentro de funções de JavaScript, a variável `data` também é um atalho para `context.body`.

   > [!NOTE]
   > A propriedade `body` aqui se aplica ao objeto `context` e não é igual ao token **Corpo** a partir de uma saída da ação, que você também pode passar para sua função.

1. Na caixa **Corpo da Solicitação**, forneça sua entrada de função, que deve ser formatada como um objeto JavaScript Object Notation (JSON).

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Quando você clica no campo **Corpo da Solicitação**, a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens de saídas de etapas anteriores. Este exemplo especifica que a carga útil `content` do contexto contém uma propriedade nomeada que tem o valor do token **From** a partir do gatilho de e-mail.

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente ao conteúdo do JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, é exibido um erro. Então, se este exemplo usou o token **Tempo recebido** em vez disso, você pode lançar o objeto de contexto como uma seqüência adicionando aspas duplas.

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método de uso, solicitar cabeçalhos ou parâmetros de consulta ou autenticação, abra a lista **Adicionar novos parâmetros** e selecione as opções desejadas. Para autenticação, suas opções diferem com base na função selecionada. Consulte [Ativar a autenticação para funções do Azure](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicionar funções existentes para aplicativos lógicos

Para chamar funções existentes do Azure de seus aplicativos lógicos, você pode adicionar funções do Azure como qualquer outra ação no Logic App Designer.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. a etapa onde deseja adicionar a função, selecione **Novo passo**.

1. Em **Escolha uma ação**, na caixa de pesquisa, digite "funções azure" como seu filtro. Na lista de ações, selecione a **ação Escolha uma função Azure.**

   ![Localizar "Funções do Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois que a lista de funções aparecer, selecione sua função.

   ![Selecione seu aplicativo de função e a função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para funções que possuem definições de API (descrições de Swagger) e são [configuradas para que seu aplicativo lógico possa encontrar e acessar essas funções,](#function-swagger)você pode selecionar **ações de Swagger**.

   ![Selecione seu aplicativo de função, "Ações de Swagger" e sua função Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Na caixa **Corpo da Solicitação**, forneça sua entrada de função, que deve ser formatada como um objeto JavaScript Object Notation (JSON).

   Essa entrada é o *objeto de contexto* ou a mensagem que seu aplicativo lógico envia para sua função. Quando você clica no campo **'''Corpo de solicitação',** a lista de conteúdo dinâmico é exibida para que você possa selecionar tokens para saídas de etapas anteriores. Este exemplo especifica que a carga útil `content` do contexto contém uma propriedade nomeada que tem o valor do token **From** a partir do gatilho de e-mail.

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Aqui, o objeto de contexto não é convertido como uma cadeia de caracteres, portanto, o conteúdo do objeto é adicionado diretamente ao conteúdo do JSON. No entanto, quando o objeto de contexto não é um token JSON que passa uma cadeia de caracteres, um objeto JSON ou uma matriz JSON, é exibido um erro. Portanto, se este exemplo tiver usado o token **Hora de Recebimento**, você poderá converter o objeto de contexto como uma cadeia de caracteres adicionando marcas de aspas duplas:

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Para especificar outros detalhes, como o método de uso, solicitar cabeçalhos, parâmetros de consulta ou autenticação, abra a lista **Adicionar novos parâmetros** e selecione as opções desejadas. Para autenticação, suas opções diferem com base na função selecionada. Consulte [Ativar a autenticação nas funções do Azure](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Aplicativos de lógica de chamada das funções do Azure

Quando você quiser acionar um aplicativo lógico de dentro de uma função do Azure, esse aplicativo lógico deverá ser iniciado com um gatilho que fornece um ponto de extremidade que pode ser chamado. Por exemplo, você pode iniciar o aplicativo lógico com o acionador **HTTP**, **Solicitação**, **Filas do Azure** ou **Grade de Eventos**. Dentro de sua função, envie uma solicitação HTTP POST para a URL do gatilho e inclua o conteúdo que você deseja que esse aplicativo lógico processe. Para obter mais informações, consulte [Chamar, acionar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Habilitar a autenticação para funções do Azure

Para autenticar o acesso aos recursos em outros inquilinos do Azure Active Directory (Azure AD) sem ter que fazer login e fornecer credenciais ou segredos, seu aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como Identidade de Serviço Gerenciado ou MSI). O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos. Saiba mais sobre [os serviços do Azure que suportam identidades gerenciadas para autenticação AD do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Se você configurar seu aplicativo lógico para usar a identidade atribuída ao sistema ou uma identidade atribuída ao usuário criada manualmente, as funções do Azure em seu aplicativo lógico também podem usar essa mesma identidade para autenticação. Para obter mais informações sobre o suporte à autenticação para funções do Azure em aplicativos lógicos, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Para configurar e usar a identidade gerenciada com sua função, siga estas etapas:

1. Habilite a identidade gerenciada em seu aplicativo lógico e configure o acesso dessa identidade ao recurso de destino. Consulte [o acesso autenticado aos recursos do Azure usando identidades gerenciadas em Aplicativos lógicos do Azure](../logic-apps/create-managed-service-identity.md).

1. Habilite a autenticação em seu aplicativo de função e função Do Azure seguindo estas etapas:

   * [Configure autenticação anônima em sua função](#set-authentication-function-app)
   * [Configure a autenticação Azure AD em seu aplicativo de função](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Configure autenticação anônima em sua função

Para usar a identidade gerenciada do seu aplicativo lógico em sua função Azure, você definiu o nível de autenticação da sua função como anônimo. Caso contrário, seu aplicativo lógico lança um erro "BadRequest".

1. No [portal Azure,](https://portal.azure.com)encontre e selecione seu aplicativo de função. Essas etapas usam "FabrikamFunctionApp" como exemplo de aplicativo de função.

1. No painel de aplicativo de função, selecione **recursos de Plataforma**. Em **Ferramentas de Desenvolvimento,** selecione **Ferramentas Avançadas (Kudu)**.

   ![Abra ferramentas avançadas para Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na barra de título do site do Kudu, no menu **Debug Console,** selecione **CMD**.

   ![No menu do console de depuração, selecione a opção "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Depois que a próxima página aparecer, na lista de pastas, selecione **o site** > **wwwroot** > *your-function*. Essas etapas usam "FabrikamAzureFunction" como exemplo de função.

   ![Selecione "site" > "wwwroot" > sua função](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Abra `function.json` o arquivo para edição.

   ![Clique em editar para o arquivo "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. No `bindings` objeto, verifique `authLevel` se a propriedade existe. Se a propriedade existir, defina o valor da propriedade para `anonymous`. Caso contrário, adicione essa propriedade e defina o valor.

   ![Adicionar propriedade "authLevel" e definir como "anônimo"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Quando terminar, salve suas configurações e continue até a próxima seção.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Configure a autenticação Azure AD para o seu aplicativo de função

Antes de iniciar esta tarefa, encontre e coloque esses valores de lado para uso posterior:

* O ID do objeto gerado para a identidade atribuída ao sistema que representa seu aplicativo lógico

  * Para gerar esse ID de objeto, [habilite a identidade atribuída ao sistema do seu aplicativo lógico.](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)

  * Caso contrário, para encontrar esse ID de objeto, abra seu aplicativo lógico no Logic App Designer. No menu do aplicativo lógico, em **Configurações,** selecione**Sistema de** **identidade** > atribuído .

* O ID do diretório para o seu inquilino no Azure Active Directory (Azure AD)

  Para obter o ID do diretório do [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) seu inquilino, você pode executar o comando Powershell. Ou, no portal Azure, siga estas etapas:

  1. No [portal Azure,](https://portal.azure.com)encontre e selecione seu aplicativo de função.

  1. Encontre e selecione seu inquilino Azure AD. Essas etapas usam "Fabrikam" como exemplo de inquilino.

  1. No menu do inquilino, em **Gerenciar, selecione** **Propriedades**.

  1. Copie o ID do diretório do seu inquilino, por exemplo, e salve esse ID para uso posterior.

     ![Encontrar e copiar o ID do diretório do inquilino do Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* O ID de recurso para o recurso de destino que você deseja acessar

  * Para encontrar esses IDs de recursos, revise os serviços do [Azure que suportam o Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Este ID de recurso deve corresponder exatamente ao valor que o Azure AD espera, incluindo quaisquer cortes necessários.

  Esse ID de recurso também é o mesmo valor que você usa posteriormente na propriedade **Audience** quando você [configura sua ação de função para usar a identidade atribuída ao sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Agora você está pronto para configurar a autenticação Azure AD para o seu aplicativo de função.

1. No [portal Azure,](https://portal.azure.com)encontre e selecione seu aplicativo de função.

1. No painel de aplicativo de função, selecione **recursos de Plataforma**. Em **Rede, selecione** **Autenticação/Autorização.**

   ![Exibir configurações de autenticação e autorização](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Alterar a **configuração de autenticação do serviço do aplicativo** para **On**. A partir da **Ação a ser tomada quando a solicitação não for autenticada,** selecione **Fazer login com o Diretório Ativo do Azure**. Em **Provedores de Autenticação,** selecione **O Diretório Ativo do Azure**.

   ![Ativar autenticação com Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. No painel Configurações do Diretório Ativo do **Azure,** siga estas etapas:

   1. Definir **o modo de gerenciamento** como **Avançado**.

   1. Na propriedade ID do **cliente,** digite o ID do objeto para a identidade atribuída ao sistema do seu aplicativo lógico.

   1. Na propriedade Url do **emissor,** digite a `https://sts.windows.net/` URL e apêndice o ID do diretório do inquilino do Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Na propriedade **Allowed Token Audiences,** digite o ID de recurso para o recurso de destino que você deseja acessar.

      Esse ID de recurso é o mesmo valor que você usa posteriormente na propriedade **Audience** quando você [configura sua ação de função para usar a identidade atribuída ao sistema](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   Neste ponto, sua versão se parece com este exemplo:

   ![Configurações de autenticação do Diretório Ativo do Azure](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Quando terminar, selecione **OK**.

1. Retorne ao Logic App Designer e siga os [passos para autenticar o acesso com a identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
