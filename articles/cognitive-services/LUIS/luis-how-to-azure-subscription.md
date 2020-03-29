---
title: Como usar as chaves de autore-tempo - LUIS
titleSuffix: Azure Cognitive Services
description: Quando você usa pela primeira vez o Entendimento de Linguagem (LUIS), você não precisa criar uma chave de criação. Quando você pretende publicar o aplicativo, em seguida, use seu ponto final de tempo de execução, você precisa criar e atribuir a chave de tempo de execução para o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a5140da32eb6fce03131a42bfa90e71e64552431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219968"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Usando chaves de recursos de autoria e tempo de execução

A autoria e os recursos em tempo de execução fornecem autenticação para o seu aplicativo LUIS e ponto final de previsão.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Ao entrar no portal LUIS, você pode optar por continuar com:

* uma [chave de avaliação](#trial-key) gratuita - fornecendo autoria e algumas consultas de ponto final de previsão.
* um recurso de autoria do Azure [LUIS.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Faça login no portal LUIS e comece a escrever

1. Entre no [portal LUIS](https://www.luis.ai) e concorde com os termos de uso.
1. Inicie seu aplicativo LUIS escolhendo qual tipo de chave de autoria do LUIS você gostaria de usar: chave de avaliação gratuita ou nova chave de autoria do Azure LUIS. 

    ![Escolher um tipo de recurso de criação de Reconhecimento Vocal](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando terminar o processo de seleção de recursos, [crie um novo aplicativo](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Chave de teste

A chave de teste (inicial) está fornecida para você. Ele é usado como sua chave de autenticação para consultar o tempo de execução do ponto final de previsão, até 1000 consultas por mês. 

Ele é visível tanto na página **Configurações** do usuário quanto nas páginas **de recursos Gerenciar -> Azure** no portal LUIS. 

Quando estiver pronto para publicar seu ponto final de previsão, crie e atribua chaves de tempo de execução de autoria e previsão, para substituir a funcionalidade da chave inicial. 

## <a name="create-resources-in-the-azure-portal"></a>Criar recursos no portal Azure

1. Use [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) para abrir o portal Azure para criação de recursos.
1. Selecione **Ambos** para criar uma criação e uma chave de tempo de execução de ponto final de previsão. 
1. Digite as informações necessárias para criar o recurso e selecione **Criar** para concluir o processo.

    ![Criar o recurso de compreensão do idioma](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Nome|Finalidade|
    |--|--|
    |Nome do recurso| Um nome personalizado que você escolher, usado como parte da URL para suas consultas de ponto final de autoria e previsão.|
    |Nome da assinatura| a assinatura que será cobrada pelo recurso.|
    |Resource group| Um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento na mesma região.|
    |Localização de criação|A região associada ao seu modelo.|
    |Tipo de preço de criação|O nível de preços determina a transação máxima por segundo e mês.|
    |Localização do runtime|A região associada ao prazo de execução do ponto final da previsão publicada.|
    |Tipo de preço do runtime|O nível de preços determina a transação máxima por segundo e mês.|

    Uma vez criados os dois recursos, atribua os recursos no portal LUIS.

## <a name="create-resources-in-azure-cli"></a>Criar recursos no Azure CLI

Use o [Cli do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente. 

Recurso `kind`:

* Criação:`LUIS.Authoring`
* Previsão:`LUIS` 

1. Faça login na CLI do Azure:

    ```azurecli
    az login
    ```

    Isso abre um navegador para permitir que você selecione a conta correta e forneça autenticação.

1. Criar um **recurso de autoria luis**, de espécie `LUIS.Authoring` `my-resource-group` , `westus` nomeado `my-luis-authoring-resource` no grupo de recursos _existente_ nomeado para a região. 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Crie um recurso de ponto `LUIS`final `my-luis-prediction-resource` de **previsão LUIS,** de `westus` tipo, nomeado no grupo de recursos _existente_ nomeado `my-resource-group` para a região. Se você quiser um throughput maior `F0` do `S0`que o nível livre, mude para . Saiba mais sobre [níveis de preços e throughput](luis-boundaries.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Essas chaves **não** são usadas pelo portal LUIS até que sejam atribuídas no portal LUIS nos **recursos Gerenciar -> Azure**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Atribuir um recurso de autoria no portal LUIS para todos os aplicativos

Você pode atribuir um recurso de autoria para um único aplicativo ou para todos os aplicativos em LUIS. O procedimento a seguir atribui todos os aplicativos a um único recurso de autoria.

1. Faça login no [portal LUIS](https://www.luis.ai).
1. Na barra de navegação superior, à extrema direita, selecione sua conta de usuário e selecione **Configurações**.
1. Na página **Configurações** do usuário, selecione **Adicionar recurso de criação** e selecione um recurso de criação existente. Selecione **Salvar**. 

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a um aplicativo

Você pode atribuir um único recurso, autorou tempo de execução de ponto final de previsão, a um aplicativo com o seguinte procedimento.

1. Faça login no [portal LUIS](https://www.luis.ai)e selecione um aplicativo na lista de **aplicativos Meus.**
1. Navegue até a página **gerenciar -> recursos do Azure.**

    ![Selecione os recursos Gerenciar -> Azure no portal LUIS para atribuir um recurso ao aplicativo.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione a guia De previsão ou autorde recursos e selecione o **botão Adicionar recurso de previsão** ou Adicionar recurso de **autoria.** 
1. Selecione os campos no formulário para encontrar o recurso correto e selecione **Salvar**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de tempo de execução sem usar o portal LUIS

Para fins de automação, como um pipeline de CI/CD, você pode querer automatizar a atribuição de um recurso de tempo de execução LUIS para um aplicativo LUIS. Para fazer isso, é necessário executar as seguintes etapas:

1. Obter um token do Azure Resource Manager deste [site](https://resources.azure.com/api/token?plaintext=true). Esse token expira, então use-o imediatamente. A solicitação retorna um token do Azure Resource Manager.

    ![Solicite e receba o token do Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Use o token para solicitar os recursos de tempo de execução do LUIS através de assinaturas, a partir da [API get luis azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), a qual sua conta de usuário tem acesso. 

    Essa API POST requer as seguintes configurações:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e um espaço.| 
    |`Ocp-Apim-Subscription-Key`|Sua chave de criação.|

    Essa API retorna uma matriz de objetos JSON das suas assinaturas do LUIS, incluindo a ID da assinatura, o grupo de recursos e o nome do recurso, retornado como o nome da conta. Localize um item na matriz que é o recurso do LUIS a atribuir ao aplicativo LUIS. 

1. Atribua o token para o recurso do LUIS com a API [Atribuir contas do Azure do LUIS a um aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Essa API POST requer as seguintes configurações:

    |Type|Configuração|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|Sua chave de criação.|
    |Cabeçalho|`Content-type`|`application/json`|
    |Querystring|`appid`|A ID do aplicativo de LUIS. 
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando essa API for bem-sucedida, ela retornará um status 201 – criado. 

## <a name="unassign-resource"></a>Cancelar a atribuição do recurso

1. Faça login no [portal LUIS](https://www.luis.ai)e selecione um aplicativo na lista de **aplicativos Meus.**
1. Navegue até a página **gerenciar -> recursos do Azure.**
1. Selecione a guia De previsão ou autorde recursos e selecione o botão **Recurso Desatribuir** para o recurso. 

Quando você desatribuí um recurso, ele não é excluído do Azure. Ela é apenas desvinculada do LUIS. 

## <a name="reset-authoring-key"></a>Redefinir a chave de criador

**Para [criar recursos migrados](luis-migration-authoring.md) de aplicativos**: se sua chave de autoria estiver comprometida, recoloque a chave no portal Azure na página **Chaves** para esse recurso de criação. 

**Para aplicativos que ainda não migraram**: a chave é redefinida em todos os seus aplicativos no portal LUIS. Se você criar seus aplicativos através das APIs de autoria, você precisa alterar o valor da Chave de Assinatura Ocp-Apim para a nova chave.

## <a name="regenerate-azure-key"></a>Regenerar a chave Azure

Regenerar as chaves do Azure do portal Azure, na página **Chaves.**

## <a name="delete-account"></a>Exclui a conta

Consulte [Armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são excluídos quando você exclui sua conta.

## <a name="change-pricing-tier"></a>Alterar tipo de preço

1.  No [Azure](https://portal.azure.com), localize sua assinatura do LUIS. Selecione a assinatura do LUIS.
    ![Localize sua assinatura do LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **Tipo de preço** para ver os tipos de preços disponíveis. 
    ![Exibir os tipos de preço](./media/luis-usage-tiers/subscription.png)
1.  Selecione o tipo de preço e clique em **Selecionar** para salvar a alteração. 
    ![Altere sua camada de pagamento do LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a alteração de preços é concluída, uma janela pop-up verifica a nova camada de preços. 
    ![Verifique sua camada de pagamento do LUIS](./media/luis-usage-tiers/updated.png)
1. Lembre-se de [atribuir essa chave do ponto de extremidade](#assign-a-resource-to-an-app) na página **Publicar** e usá-la em todas as consultas de ponto de extremidade. 

## <a name="viewing-azure-resource-metrics"></a>Visualização de métricas de recursos do Azure

### <a name="viewing-azure-resource-summary-usage"></a>Exibindo o uso do resumo dos recursos do Azure
Você pode exibir informações de uso do LUIS no Azure. A página **Visão geral** mostra as informações de resumidas recentes, incluindo chamadas e erros. Se você fizer uma solicitação de ponto de extremidade do LUIS, acompanhe na **página Visão geral** e aguarde até cinco minutos para o uso ser exibido.

![Exibindo uso de resumo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalização de gráficos de uso de recursos do Azure
As métricas fornecem uma exibição mais detalhada dos dados.

![Métricas padrão](./media/luis-usage-tiers/metrics-default.png)

Você pode configurar os gráficos de métricas para o período de tempo e o tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta de limite total de transações
Se você quiser saber quando atingiu um determinado limite transação, por exemplo, 10.000 transações, crie um alerta. 

![Alertas padrão](./media/luis-usage-tiers/alert-default.png)

Adicionar um alerta de métrica para a métrica **total de chamadas** para um determinado período de tempo. Adicione endereços de email de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Você também poderá executar um aplicativo lógico quando o alerta for disparado. 

## <a name="next-steps"></a>Próximas etapas

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de autoria](luis-concept-keys.md#authoring-key) e os [contribuintes](luis-concept-keys.md#contributions-from-other-authors) sobre esse recurso.
* [Saiba como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md) 
