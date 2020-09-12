---
title: Como usar as chaves de criação e tempo de execução-LUIS
description: Ao usar o Reconhecimento vocal (LUIS) pela primeira vez, você não precisa criar uma chave de criação. Quando você pretende publicar o aplicativo, use o ponto de extremidade do tempo de execução, você precisa criar e atribuir a chave de tempo de execução ao aplicativo.
services: cognitive-services
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 99f73399c410641be352111302b1d4999d1ebc1b
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565898"
---
# <a name="create-luis-resources"></a>Criar recursos do LUIS

Os recursos de tempo de execução de previsão de criação e consulta fornecem autenticação para o aplicativo LUIS e o ponto de extremidade de previsão.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Recursos do LUIS

O LUIS permite três tipos de recursos do Azure e um recurso não Azure:

|Recurso|Finalidade|Serviço cognitiva `kind`|Serviço cognitiva `type`|
|--|--|--|--|
|Recurso de criação|Permite criar, gerenciar, treinar, testar e publicar seus aplicativos. [Crie um recurso de criação do Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-luis-resources-in-azure-portal) se você pretende criar Luis aplicativos programtically ou no portal do Luis. Primeiro, você precisa [migrar sua conta do Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring#what-is-migration) para poder vincular seus recursos do Authroring do Azure ao seu aplicativo. Você pode controlar as permissões para o recurso de criação atribuindo pessoas à [função colaborador](#contributions-from-other-authors). <br><br> Há um disponíveis de camada para o recurso de criação de LUIS:<br> * **F0 criação de recurso** que fornece a você 1 milhão de transações de criação gratuitas e 1000 solicitações de ponto de extremidade de previsão gratuitas mensais. |`LUIS.Authoring`|`Cognitive Services`|
|Recurso de previsão| Depois de publicar o aplicativo LUIS, use o recurso/chave de previsão para consultar solicitações de ponto de extremidade de previsão. Crie um recurso de previsão LUIS antes que seu aplicativo cliente solicite previsões além das 1.000 solicitações fornecidas pela criação ou pelo recurso de início. <br><br> Há duas camadas avialble para o recurso de previsão:<br> * O **recurso de previsão F0** que fornece a você 10.000 solicitações de ponto de extremidade gratuitas de previsão mensais<br> * **Recurso de previsão S0** , que é a camada paga. [Saiba mais sobre os detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Recurso de avaliação/inicialização|Permite criar, gerenciar, treinar, testar e publicar seus aplicativos. Isso é criado por padrão se você escolher a opção de recurso de início ao se inscrever na primeira vez o TP LUIS. No entanto, a chave inicial será eventualmente preterida e todos os usuários do LUIS precisarão [migrar suas contas](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring#what-is-migration) e vincular seus aplicativos Luis a um recurso de criação. Esse recurso não lhe dá permissões para controle de acesso baseado em função, como o recurso de criação. <br><br> Assim como o recurso de criação, o recurso de início fornece a você 1 milhão de transações de criação gratuitas e 1000 de solicitações de ponto de previsão gratuitas.|-|Não é um recurso do Azure|
|[Chave de recurso de vários serviços cognitivas do serviço](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Solicitações de ponto de extremidade de previsão de consulta compartilhadas com LUIS e outros serviços cognitivas com suporte.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> Há dois tipos de recursos F0 (camada gratuita) que o LUIS fornece. Uma para criar transações e outra para transações de previsão. Se você estiver ficando sem cota livre para transações de previsão, certifique-se de que você está na verdade usando o recurso de previsão F0 que fornece a você as 10.000 transações gratuitas mensais e não o recurso de criação que oferece a você 1000 transações de previsão mensalmente.

Quando o processo de criação de recursos do Azure for concluído, [atribua o recurso](#assign-a-resource-to-an-app) ao aplicativo no portal do Luis.

É importante criar aplicativos LUIS em [regiões](luis-reference-regions.md#publishing-regions) em que você deseja publicar e consultar.

## <a name="resource-ownership"></a>Propriedade do recurso

Um recurso do Azure, como um LUIS, pertence à assinatura que contém o recurso.

Para transferir a propriedade de um recurso, você pode:
* Transferir a [Propriedade](../../cost-management-billing/manage/billing-subscription-transfer.md) de sua assinatura
* Exporte o aplicativo LUIS como um arquivo e, em seguida, importe o aplicativo em uma assinatura diferente. A exportação está disponível na página **meus aplicativos** no portal do Luis.


## <a name="resource-limits"></a>Limites de recursos

### <a name="authoring-key-creation-limits"></a>Limites de criação de chave de criação

Você pode criar até 10 chaves de criação por região por assinatura.

Consulte [limites de chave](luis-limits.md#key-limits) e [regiões do Azure](luis-reference-regions.md).

Regiões de publicação são diferentes de regiões de criação. Certifique-se de criar um aplicativo na região de criação correspondente à região de publicação que você deseja que seu aplicativo cliente esteja localizado.

### <a name="key-usage-limit-errors"></a>Erros de limite de uso de chave

Os limites de uso são baseados no tipo de preço.

Se exceder sua cota de transações por segundo (TPS), você receberá um erro HTTP 429. Se exceder sua cota de transações por mês (TSM), você receberá um erro HTTP 403.


### <a name="reset-authoring-key"></a>Redefinir a chave de criador

Para a criação de aplicativos [migrados](luis-migration-authoring.md) : se a sua chave de criação estiver comprometida, redefina a chave no portal do Azure na página **chaves** desse recurso de criação.

Para aplicativos que ainda não foram migrados: a chave é redefinida em todos os seus aplicativos no portal do LUIS. Se você criar seus aplicativos por meio de APIs de criação, precisará alterar o valor de OCP-APIM-Subscription-Key para a nova chave.

### <a name="regenerate-azure-key"></a>Regenerar chave do Azure

Gere novamente as chaves do Azure no portal do Azure, na página **chaves** .


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Propriedade, acesso e segurança do aplicativo

Um aplicativo é definido por seus recursos do Azure, que é determinado pela assinatura do proprietário.

Você pode mover seu aplicativo LUIS. Use os seguintes recursos de documentação no portal do Azure ou CLI do Azure:

* [Mover o aplicativo entre os recursos de criação do LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Mover recurso para novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Mover recurso na mesma assinatura ou em assinaturas](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Contribuições de outros autores

Para [criar aplicativos migrados de recursos](luis-migration-authoring.md) : os _colaboradores_ são gerenciados no portal do Azure para o recurso de criação, usando a página de **controle de acesso (iam)** . Saiba [como adicionar um usuário](luis-how-to-collaborate.md), usando o endereço de email da colaboração e a função _colaborador_ .

Para aplicativos que ainda não foram migrados: todos os _colaboradores_ são gerenciados no portal do Luis a partir da página **gerenciar > colaboradores** .

### <a name="query-prediction-access-for-private-and-public-apps"></a>Acesso de previsão de consulta para aplicativos públicos e privados

Para um aplicativo **privado** , o acesso ao tempo de execução de previsão de consulta está disponível para proprietários e colaboradores. Para um aplicativo **público** , o acesso ao tempo de execução está disponível para todos que têm seu próprio [serviço cognitiva](../cognitive-services-apis-create-account.md) do Azure ou recurso de tempo de execução [Luis](#create-resources-in-the-azure-portal) e tem a ID do aplicativo público.

Atualmente, não há um catálogo de aplicativos públicos.

### <a name="authoring-permissions-and-access"></a>Permissões de criação e acesso
O acesso ao aplicativo no portal do [Luis](luis-reference-regions.md#luis-website) ou nas [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087) é controlado pelo recurso de criação do Azure.

O proprietário e todos os colaboradores têm acesso para criar o aplicativo.

|O acesso de criação inclui|Observações|
|--|--|
|Adicionar ou remover chaves de ponto de extremidade||
|Versão de exportação||
|Exportar logs de ponto de extremidade||
|Versão de importação||
|Tornar um aplicativo público|Quando um aplicativo for público, qualquer pessoa com uma chave de criação ou de ponto de extremidade poderá consultá-lo.|
|Modificar modelo|
|Publicar|
|Examinar declarações de ponto de extremidade para [aprendizado ativo](luis-how-to-review-endpoint-utterances.md)|
|Treinar|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Acesso ao tempo de execução de ponto de extremidade de previsão

O acesso para consultar o ponto de extremidade de previsão é controlado por uma configuração na página de **informações do aplicativo** na seção **gerenciar** .

|[Ponto de extremidade privado](#runtime-security-for-private-apps)|[Ponto de extremidade público](#runtime-security-for-public-apps)|
|:--|:--|
|Disponível para o proprietário e os colaboradores|Disponível para o proprietário, colaboradores e qualquer outra pessoa que conheça a ID do aplicativo|

Você pode controlar quem vê sua chave de tempo de execução LUIS chamando-a em um ambiente de servidor para servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já estará segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar a API do servidor (como uma [função](https://azure.microsoft.com/services/functions/) do Azure) com acesso controlado (como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do lado do servidor for chamada e autenticada e a autorização for verificada, passe a chamada para LUIS. Embora essa estratégia não impeça ataques man-in-the-Middle, ela ofusca sua chave e a URL do ponto de extremidade dos usuários, permite que você acompanhe o acesso e permite que você adicione o log de resposta do ponto de extremidade (como [Application insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Segurança de tempo de execução para aplicativos privados

O tempo de execução de um aplicativo privado só está disponível para o seguinte:

|Chave e usuário|Explicação|
|--|--|
|Chave de criação do proprietário| Até 1000 ocorrências de ponto de extremidade|
|Colaboradores/chaves de criação de colaborador| Até 1000 ocorrências de ponto de extremidade|
|Qualquer chave atribuída a LUIS por um autor ou colaboração/colaborador|Com base na camada de uso da chave|

### <a name="runtime-security-for-public-apps"></a>Segurança de tempo de execução para aplicativos públicos

Depois que um aplicativo for configurado como público, _qualquer_ chave de criação LUIS válida ou chave do ponto de extremidade LUIS poderá consultar seu aplicativo, desde que a chave não tenha usado toda a cota de ponto de extremidade.

Um usuário que não seja um proprietário ou colaborador, só poderá acessar o tempo de execução de um aplicativo público se a ID do aplicativo for fornecida. O LUIS não tem um _mercado_ público ou outra maneira de pesquisar um aplicativo público.

Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso do LUIS baseada em região possa acessar o aplicativo em qualquer região associada com a chave de recurso.


### <a name="securing-the-query-prediction-endpoint"></a>Protegendo o ponto de extremidade de previsão de consulta

Você pode controlar quem pode ver sua chave de ponto de extremidade do LUIS de tempo de execução de previsão chamando-a em um ambiente de servidor para servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já estará segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar a API do servidor (como uma [função](https://azure.microsoft.com/services/functions/) do Azure) com acesso controlado (como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do servidor for chamada e a autenticação e a autorização forem verificadas, passe a chamada para o LUIS. Embora essa estratégia não impeça ataques man-in-the-Middle, ele ofusca seu ponto de extremidade de seus usuários, permite que você acompanhe o acesso e permite que você adicione o log de resposta do ponto de extremidade (como [Application insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Entre no portal do LUIS e comece a criar

1. Entre no [portal do Luis](https://www.luis.ai) e concorde com os termos de uso.
1. Inicie seu aplicativo LUIS escolhendo sua chave de criação do LUIS do Azure.

   ![Escolher um tipo de recurso de criação de Reconhecimento Vocal](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando você terminar com o processo de seleção de recursos, [crie um novo aplicativo](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Criar recursos no CLI do Azure

Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente.

Recurso `kind` :

* Criação `LUIS.Authoring`
* Previsão `LUIS`

1. Entre no CLI do Azure:

    ```azurecli
    az login
    ```

    Isso abre um navegador para permitir que você selecione a conta correta e forneça a autenticação.

1. Crie um **recurso de criação de Luis**, de tipo `LUIS.Authoring` , nomeado `my-luis-authoring-resource` no grupo de recursos _existente_ chamado `my-resource-group` para a `westus` região.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Crie um **recurso de ponto de extremidade de previsão Luis**, de tipo `LUIS` , nomeado `my-luis-prediction-resource` no grupo de recursos _existente_ denominado `my-resource-group` para a `westus` região. Se você quiser uma taxa de transferência mais alta do que a camada gratuita, altere `F0` para `S0` . Saiba mais sobre os [tipos de preço e a taxa de transferência](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Essas chaves **não** são usadas pelo portal do Luis até que sejam atribuídas no portal do Luis nos **recursos do Azure gerenciar->**.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Atribuir recurso no portal do LUIS

Você pode atribuir um recurso de criação para um único aplicativo ou para todos os aplicativos no LUIS. O procedimento a seguir atribui todos os aplicativos a um único recurso de criação.

1. Entre no portal do [LUIS](https://www.luis.ai).
1. Na barra de navegação superior, à extrema direita, selecione sua conta de usuário e, em seguida, selecione **configurações**.
1. Na página **configurações do usuário** , selecione **Adicionar recurso de criação** e, em seguida, selecione um recurso de criação existente. Clique em **Salvar**.

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a um aplicativo

Observe que, se você não tiver uma assinatura do Azure, não poderá atribuir ou criar um novo recurso. Você precisará primeiro ir e criar uma [avaliação gratuita do Azure](https://azure.microsoft.com/en-us/free/) e retornar ao Luis para criar um novo recurso no Portal.

Você pode atribuir ou criar uma criação ou um recurso de previsão para um aplicativo com o seguinte procedimento:

1. Entre no portal do [Luis](https://www.luis.ai)e selecione um aplicativo na lista **meus aplicativos**
1. Navegue até a página **gerenciar > recursos do Azure**

    ![Selecione Gerenciar-> recursos do Azure no portal do LUIS para atribuir um recurso ao aplicativo.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione a guia recurso de previsão ou criação e, em seguida, selecione o botão **Adicionar recurso de previsão** ou **Adicionar recurso de criação**
1. Selecione os campos no formulário para localizar o recurso correto e, em seguida, selecione **salvar**
1. Se você não tiver um recurso existente, poderá criar um selecionando "criar um novo recurso LUIS?" na parte inferior da janela


### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de tempo de execução de previsão de consulta sem usar o portal do LUIS

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

1. Atribua o token ao recurso LUIS com [atribuir uma Luis contas do Azure a uma](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API de aplicativo.

    Essa API POST requer as seguintes configurações:

    |Type|Setting|Valor|
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


## <a name="delete-account"></a>Excluir conta

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
* Migrar para o novo [recurso de criação](luis-migration-authoring.md)
