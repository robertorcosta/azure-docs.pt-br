---
title: Como usar as chaves de criação e tempo de execução-LUIS
description: Ao usar o Reconhecimento vocal (LUIS) pela primeira vez, você não precisa criar uma chave de criação. Quando você pretende publicar o aplicativo, use o ponto de extremidade do tempo de execução, você precisa criar e atribuir a chave de tempo de execução ao aplicativo.
services: cognitive-services
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: d9235b6ef1c7cddbfbbd36f8382439d781af6d5f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101018"
---
# <a name="create-luis-resources"></a>Criar recursos do LUIS

Os recursos de criação e tempo de execução fornecem autenticação para seu aplicativo LUIS e ponto de extremidade de previsão.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Ao entrar no portal do LUIS, você pode optar por continuar com:

* uma [chave de avaliação](#trial-key) gratuita-fornecendo criação e algumas consultas de ponto de extremidade de previsão.
* um recurso de [criação de Luis](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) do Azure.

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Entre no portal do LUIS e comece a criar

1. Entre no [portal do Luis](https://www.luis.ai) e concorde com os termos de uso.
1. Inicie seu aplicativo LUIS escolhendo o tipo de chave de criação do LUIS que você gostaria de usar: chave de avaliação gratuita ou nova chave de criação do Azure LUIS.

    ![Escolher um tipo de recurso de criação de Reconhecimento Vocal](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando você terminar com o processo de seleção de recursos, [crie um novo aplicativo](luis-how-to-start-new-app.md#create-new-app-in-luis).

## <a name="trial-key"></a>Chave de avaliação

A chave de avaliação (inicial) é fornecida para você. Ele é usado como sua chave de autenticação para consultar o tempo de execução de ponto de extremidade de previsão, até 1000 consultas por mês.

Ele é visível na página de **configurações do usuário** e nas páginas de **recursos do Azure Manage->** no portal do Luis.

Quando você estiver pronto para publicar seu ponto de extremidade de previsão, [crie](#create-luis-resources) e [atribua](#assign-a-resource-to-an-app) chaves de criação e de tempo de execução de previsão para substituir a funcionalidade de chave inicial.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Criar recursos no CLI do Azure

Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente.

Recurso `kind`:

* Criação`LUIS.Authoring`
* Previsão`LUIS`

1. Entre no CLI do Azure:

    ```azurecli
    az login
    ```

    Isso abre um navegador para permitir que você selecione a conta correta e forneça a autenticação.

1. Crie um **recurso de criação de Luis**, de `LUIS.Authoring`tipo, `my-luis-authoring-resource` nomeado no grupo de recursos _existente_ chamado `my-resource-group` para `westus` a região.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Crie um **recurso de ponto de extremidade de previsão Luis**, `LUIS`de `my-luis-prediction-resource` tipo, nomeado no grupo de `my-resource-group` recursos _existente_ denominado para a `westus` região. Se você quiser uma taxa de transferência mais alta do que a `F0` camada `S0`gratuita, altere para. Saiba mais sobre os [tipos de preço e a taxa de transferência](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Essas chaves **não** são usadas pelo portal do Luis até que sejam atribuídas no portal do Luis nos **recursos do Azure gerenciar->**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Atribuir um recurso de criação no portal do LUIS para todos os aplicativos

Você pode atribuir um recurso de criação para um único aplicativo ou para todos os aplicativos no LUIS. O procedimento a seguir atribui todos os aplicativos a um único recurso de criação.

1. Entre no portal do [Luis](https://www.luis.ai).
1. Na barra de navegação superior, à extrema direita, selecione sua conta de usuário e, em seguida, selecione **configurações**.
1. Na página **configurações do usuário** , selecione **Adicionar recurso de criação** e, em seguida, selecione um recurso de criação existente. Clique em **Salvar**.

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a um aplicativo

Você pode atribuir um único recurso, criação ou tempo de execução de ponto de extremidade de previsão a um aplicativo com o procedimento a seguir.

1. Entre no portal do [Luis](https://www.luis.ai)e selecione um aplicativo na lista **meus aplicativos** .
1. Navegue até a página **gerenciar > recursos do Azure** .

    ![Selecione Gerenciar-> recursos do Azure no portal do LUIS para atribuir um recurso ao aplicativo.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione a guia Previsão ou recurso de criação e, em seguida, selecione o botão **Adicionar recurso de previsão** ou **Adicionar recurso de criação** .
1. Selecione os campos no formulário para localizar o recurso correto e, em seguida, selecione **salvar**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de tempo de execução sem usar o portal do LUIS

Para fins de automação, como um pipeline de CI/CD, talvez você queira automatizar a atribuição de um recurso de tempo de execução LUIS para um aplicativo LUIS. Para fazer isso, é necessário executar as seguintes etapas:

1. Obter um token do Azure Resource Manager deste [site](https://resources.azure.com/api/token?plaintext=true). Esse token expira, então use-o imediatamente. A solicitação retorna um token do Azure Resource Manager.

    ![Solicite e receba o token do Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Use o token para solicitar os recursos de tempo de execução LUIS entre assinaturas, da [API Get Luis Azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), à qual sua conta de usuário tem acesso.

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

1. Entre no portal do [Luis](https://www.luis.ai)e selecione um aplicativo na lista **meus aplicativos** .
1. Navegue até a página **gerenciar > recursos do Azure** .
1. Selecione a guia recurso de previsão ou criação e, em seguida, selecione o botão **desatribuir recurso** para o recurso.

Quando você cancelar a atribuição de um recurso, ele não será excluído do Azure. Ela é apenas desvinculada do LUIS.

## <a name="reset-authoring-key"></a>Redefinir a chave de criador

**Para a criação de aplicativos [migrados](luis-migration-authoring.md) **: se a sua chave de criação estiver comprometida, redefina a chave no portal do Azure na página **chaves** desse recurso de criação.

**Para aplicativos que ainda não foram migrados**: a chave é redefinida em todos os seus aplicativos no portal do Luis. Se você criar seus aplicativos por meio de APIs de criação, precisará alterar o valor de OCP-APIM-Subscription-Key para a nova chave.

## <a name="regenerate-azure-key"></a>Regenerar chave do Azure

Gere novamente as chaves do Azure no portal do Azure, na página **chaves** .

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

## <a name="viewing-azure-resource-metrics"></a>Exibindo métricas de recursos do Azure

### <a name="viewing-azure-resource-summary-usage"></a>Exibindo o uso do Resumo de recursos do Azure
Você pode exibir informações de uso do LUIS no Azure. A página **Visão geral** mostra as informações de resumidas recentes, incluindo chamadas e erros. Se você fizer uma solicitação de ponto de extremidade do LUIS, acompanhe na **página Visão geral** e aguarde até cinco minutos para o uso ser exibido.

![Exibindo uso de resumo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizando gráficos de uso de recursos do Azure
As métricas fornecem uma exibição mais detalhada dos dados.

![Métricas padrão](./media/luis-usage-tiers/metrics-default.png)

Você pode configurar os gráficos de métricas para o período de tempo e o tipo de métrica.

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta de limite total de transações
Se você quiser saber quando atingiu um determinado limite transação, por exemplo, 10.000 transações, crie um alerta.

![Alertas padrão](./media/luis-usage-tiers/alert-default.png)

Adicionar um alerta de métrica para a métrica **total de chamadas** para um determinado período de tempo. Adicione endereços de email de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Você também poderá executar um aplicativo lógico quando o alerta for disparado.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de criação](luis-concept-keys.md#authoring-key) e os [colaboradores](luis-concept-keys.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de criação](luis-migration-authoring.md)
