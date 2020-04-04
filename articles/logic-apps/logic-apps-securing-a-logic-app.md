---
title: Proteger o acesso e os dados
description: Acesso seguro a entradas, saídas, gatilhos baseados em solicitações, histórico de execução, tarefas de gerenciamento e acesso a outros recursos no Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657170"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Acesso seguro e dados em Aplicativos lógicos do Azure

Para controlar o acesso e proteger dados no Azure Logic Apps, você pode configurar a segurança nessas áreas:

* [Acesso a gatilhos baseados em solicitação](#secure-triggers)
* [Acesso às operações lógicas de aplicativos](#secure-operations)
* [Acesso para executar entradas e saídas do histórico](#secure-run-history)
* [Acesso a entradas de parâmetros](#secure-action-parameters)
* [Acesso a serviços e sistemas chamados de aplicativos lógicos](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Acesso a gatilhos baseados em solicitação

Se o seu aplicativo lógico usar um gatilho baseado em solicitação, que recebe chamadas ou solicitações recebidas, como o gatilho [Solicitação](../connectors/connectors-native-reqres.md) ou [Webhook,](../connectors/connectors-native-webhook.md) você pode limitar o acesso para que apenas clientes autorizados possam ligar para o seu aplicativo lógico. Todas as solicitações recebidas por um aplicativo lógico são criptografadas e protegidas com o TLS (Transport Layer Security, segurança de camada de transporte), anteriormente conhecido como Secure Sockets Layer (SSL), protocolo.

Aqui estão opções que podem ajudá-lo a garantir o acesso a este tipo de gatilho:

* [Gerar assinaturas de acesso compartilhado](#sas)
* [Restringir endereços IP de entrada](#restrict-inbound-ip-addresses)
* [Adicionar OAuth do Diretório Ativo do Azure ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar assinaturas de acesso compartilhado (SAS)

Cada ponto final de solicitação em um aplicativo lógico tem uma [Assinatura de Acesso Compartilhado (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) na URL do ponto final, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL `sp`contém `sv`o `sig` parâmetro de consulta e consulta, conforme descrito nesta tabela:

| Parâmetro de consulta | Descrição |
|-----------------|-------------|
| `sp` | Especifica permissões para os métodos HTTP permitidos usarem. |
| `sv` | Especifica a versão SAS a ser usada para gerar a assinatura. |
| `sig` | Especifica a assinatura a ser usada para autenticar o acesso ao gatilho. Essa assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos e propriedades da URL. Nunca exposta ou publicada, essa chave é mantida criptografada e armazenada com o aplicativo lógico. Seu aplicativo lógico autoriza somente gatilhos que contenham uma assinatura válida criada com a chave secreta. |
|||

Para obter mais informações sobre como garantir acesso ao SAS, consulte estas seções neste tópico:

* [Regenerar chaves de acesso](#access-keys)
* [Criar URLs de retorno de chamada prestes a expirar](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Para gerar uma nova chave de acesso de segurança a qualquer momento, use a API ou o portal Azure REST. Todas as URLs geradas anteriormente que usam a chave antiga são invalidadas e não têm mais autorização para acionar o aplicativo lógico. Os URLs que você recupera após a regeneração são assinados com a nova chave de acesso.

1. No [portal Azure](https://portal.azure.com), abra o aplicativo lógico que tem a chave que você deseja regenerar.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Chaves de Acesso**.

1. Selecione a chave que deseja regenerar e termine o processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Criar URLs de retorno de chamada prestes a expirar

Se você compartilhar a URL de ponto final para um gatilho baseado em solicitação com outras partes, você poderá gerar URLs de retorno de chamada que usam chaves específicas e têm datas de validade. Dessa forma, você pode rolar chaves perfeitamente ou restringir o acesso ao acionamento do seu aplicativo lógico com base em um plantão específico. Para especificar uma data de validade de uma URL, use a [API Logic Apps REST,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)por exemplo:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua `NotAfter`a propriedade usando uma seqüência de data JSON. Essa propriedade retorna uma URL de retorno de chamada válida somente até a data e hora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Quando você gera ou lista URLs de retorno de chamada para um gatilho baseado em solicitação, você pode especificar a chave a ser usada para assinar a URL. Para gerar uma URL assinada por uma chave específica, use a [API Logic Apps REST](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `KeyType` como `Primary` ou `Secondary`. Esta propriedade retorna uma URL assinada pela chave de segurança especificada.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Restringir endereços IP de entrada

Juntamente com o SAS (SAS), você pode querer limitar especificamente os clientes que podem ligar para o seu aplicativo lógico. Por exemplo, se você gerenciar seu ponto final de solicitação usando o Gerenciamento de API do Azure, poderá restringir seu aplicativo lógico para aceitar solicitações apenas a partir do endereço IP para a instância de gerenciamento de API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Restringir faixas de IP de entrada no portal Azure

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Em **Configuração** > de controle de**acesso permitido endereços IP de entrada,** selecione **faixas IP específicas**.

1. Em **Intervalos de IP para gatilhos**, especifique os intervalos de endereço IP que aceitam o gatilho.

   Um intervalo IP válido usa estes formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x*

Se você quiser que seu aplicativo lógico seja acionado apenas como um aplicativo de lógica aninhada, a partir da lista **de endereços IP de entrada permitido,** selecione **Apenas outros aplicativos lógicos**. Esta opção grava uma matriz vazia para o recurso do aplicativo lógico. Dessa forma, apenas chamadas do serviço Logic Apps (aplicativos de lógica pai) podem acionar o aplicativo de lógica aninhada.

> [!NOTE]
> Independentemente do endereço IP, você ainda pode executar um aplicativo lógico que tenha um gatilho baseado em solicitação usando `/triggers/<trigger-name>/run` por meio da API REST do Azure ou do Gerenciamento de API. No entanto, esse cenário ainda requer autenticação contra a API Azure REST. Todos os eventos aparecem no Registro de Auditoria do Azure. Certifique-se de definir as políticas de controle de acesso de acordo.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Restringir intervalos de IP de entrada no modelo do Azure Resource Manager

Se você [automatizar a implantação de aplicativos lógicos usando modelos do Gerenciador de recursos,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você pode especificar os intervalos de IP usando a `accessControl` seção com a `triggers` seção na definição de recursos do seu aplicativo lógico, por exemplo:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Adicionar OAuth do Diretório Ativo do Azure ou outra segurança

Para adicionar mais protocolos de autorização ao seu aplicativo lógico, considere usar o serviço de gerenciamento de API do [Azure.](../api-management/api-management-key-concepts.md) Esse serviço ajuda você a expor seu aplicativo lógico como uma API e oferece monitoramento, segurança, política e documentação ricos para qualquer ponto final. O Gerenciamento de API pode expor um ponto final público ou privado para o seu aplicativo lógico. Para autorizar o acesso a este ponto final, você pode usar [o Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), [certificado de cliente](#client-certificate-authentication)ou outras normas de segurança para autorizar o acesso a esse ponto final. Quando o Gerenciamento de API recebe uma solicitação, o serviço envia a solicitação ao aplicativo lógico, fazendo também quaisquer transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas o Gerenciamento de API acione seu aplicativo lógico, você pode usar as configurações de intervalo ip de entrada do seu aplicativo lógico.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso às operações lógicas de aplicativos

Você pode permitir que apenas usuários ou grupos específicos executem tarefas específicas, como gerenciamento, edição e visualização de aplicativos lógicos. Para controlar suas permissões, use [o RBAC (Azure Role-Based Access Control, controle de acesso baseado em funções)](../role-based-access-control/role-assignments-portal.md) para que você possa atribuir funções personalizadas ou incorporadas aos membros da sua assinatura do Azure:

* [Logic App Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor): Permite gerenciar aplicativos lógicos, mas você não pode alterar o acesso a eles.

* [Logic App Operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Permite que você leia, habilite e desative aplicativos lógicos, mas você não pode editá-los ou atualizá-los.

Para impedir que outras pessoas alterem ou excluam seu aplicativo lógico, você pode usar [Bloqueio de Recursos do Azure](../azure-resource-manager/management/lock-resources.md). Essa capacidade impede que outros aliminem ou excluam recursos de produção.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Acesso aos dados do histórico de execução

Durante uma execução de aplicativo lógico, todos os dados são [criptografados durante](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) o trânsito usando TLS (Transport Layer Security) e [em repouso](../security/fundamentals/encryption-atrest.md). Quando seu aplicativo de lógica terminar de ser executado, você poderá visualizar o histórico dessa execução, incluindo as etapas que foram executadas junto com o status, duração, entradas e saídas para cada ação. Esse rico detalhe fornece uma visão de como seu aplicativo lógico foi funcionou e onde você pode começar a solucionar quaisquer problemas que surgirem.

Quando você visualiza o histórico de execução do seu aplicativo lógico, o Logic Apps autentica seu acesso e, em seguida, fornece links para as entradas e saídas para as solicitações e respostas de cada execução. No entanto, para ações que lidam com senhas, segredos, chaves ou outras informações confidenciais, você deseja impedir que outras pessoas visualizam e acessem esses dados. Por exemplo, se o seu aplicativo lógico recebe um segredo do [Azure Key Vault](../key-vault/key-vault-overview.md) para usar ao autenticar uma ação HTTP, você deseja ocultar esse segredo da vista.

Para controlar o acesso às entradas e saídas no histórico de execução do aplicativo lógico, você tem essas opções:

* [Restringir o acesso por intervalo de endereçoIP](#restrict-ip).

  Essa opção ajuda você a garantir o acesso ao histórico de execução com base nas solicitações de uma faixa de endereço IP específica.

* [Ocultar dados do histórico de execução usando ofuscação](#obfuscate).

  Em muitos gatilhos e ações, você pode ocultar suas entradas, saídas ou ambos a partir do histórico de execução de um aplicativo lógico.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restringir o acesso por intervalo de endereçoip

Você pode limitar o acesso às entradas e saídas no histórico de execução do seu aplicativo lógico para que apenas solicitações de faixas específicas de endereçoIP possam visualizar esses dados. Por exemplo, para bloquear qualquer pessoa de acessar entradas e `0.0.0.0-0.0.0.0`saídas, especifique uma faixa de endereço IP, como . Apenas uma pessoa com permissões de administrador pode remover essa restrição, o que fornece a possibilidade de acesso "just-in-time" aos dados do seu aplicativo lógico. Você pode especificar os intervalos de IP para restringir usando o portal Azure ou em um modelo do Azure Resource Manager que você usa para implantação de aplicativos lógicos.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Restringir faixas de IP no portal Azure

1. No portal do Azure, abra o aplicativo lógico no Designer de Aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Em **Configuração** > de controle de**acesso permitido endereços IP de entrada,** selecione **faixas IP específicas**.

1. Em **intervalos IP para conteúdo**, especifique os intervalos de endereços IP que podem acessar o conteúdo de entradas e saídas. 

   Um intervalo IP válido usa estes formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Restringir faixas de IP no modelo do Azure Resource Manager

Se você [automatizar a implantação de aplicativos lógicos usando modelos do Gerenciador de recursos,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você pode especificar os intervalos de IP usando a `accessControl` seção com a `contents` seção na definição de recursos do seu aplicativo lógico, por exemplo:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Ocultar dados do histórico de execução usando ofuscação

Muitos gatilhos e ações têm configurações para ocultar entradas, saídas ou ambos a partir do histórico de execução de um aplicativo lógico. Aqui estão algumas [considerações a serem revisadas](#obfuscation-considerations) quando você usa essas configurações para ajudá-lo a proteger esses dados.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Ocultar entradas e saídas no designer

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

   ![Aplicativo de lógica aberta no Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. No gatilho ou ação onde você deseja ocultar dados confidenciais, selecione o botão elipses (**...**) e, em seguida, selecione **Configurações**.

   ![Abrir configurações de gatilho ou ação](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Ligue **entradas seguras,** **saídas seguras**ou ambas. Quando tiver terminado, selecione **Concluído**.

   ![Ativar "Entradas seguras" ou "Saídas seguras"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A ação ou gatilho agora mostra um ícone de bloqueio na barra de título.

   ![A barra de título de ação ou gatilho mostra o ícone de bloqueio](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Os tokens que representam saídas seguras de ações anteriores também mostram ícones de bloqueio. Por exemplo, quando você seleciona essa saída da lista de conteúdo dinâmico para usar em uma ação, esse token mostra um ícone de bloqueio.

   ![Selecione token para saída segura](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Depois que o aplicativo lógico for executado, você pode visualizar o histórico dessa execução.

   1. No painel Visão **Geral** do aplicativo lógico, selecione a execução que deseja exibir.

   1. No **painel de execução do aplicativo Logic,** expanda as ações que você deseja rever.

      Se você optou por obscurecer tanto entradas quanto saídas, esses valores agora aparecem escondidos.

      ![Entradas e saídas ocultas na história do run](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Ocultar entradas e saídas na exibição de código

Na definição de gatilho ou ação subjacente, adicione ou atualize a `runtimeConfiguration.secureData.properties` matriz com ambos ou ambos os valores:

* `"inputs"`: Garante entradas no histórico de execução.
* `"outputs"`: Segura saídas no histórico de execução.

Aqui estão algumas [considerações a serem revisadas](#obfuscation-considerations) quando você usa essas configurações para ajudá-lo a proteger esses dados.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Considerações ao ocultar entradas e saídas

* Quando você obscurece as entradas ou saídas em um gatilho ou ação, a Logic Apps não envia os dados protegidos para o Azure Log Analytics. Além disso, você não pode adicionar [propriedades rastreadas](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) a esse gatilho ou ação para monitoramento.

* A [API logic apps para lidar com o histórico do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/) não retorna saídas seguras.

* Para ocultar saídas de uma ação que obscurece entradas ou obscurece explicitamente saídas, ligue manualmente **saídas seguras** nessa ação.

* Certifique-se de ativar **Entradas seguras** ou **saídas seguras** em ações a jusante onde espera que o histórico de execução obscureça esses dados.

  **Configuração de saídas seguras**

  Quando você ativa manualmente **saídas seguras** em um gatilho ou ação, o Logic Apps protege essas saídas no histórico de execução. Se uma ação downstream usar explicitamente essas saídas seguras como entradas, o Logic Apps ocultaas entradas dessa ação no histórico de execução, mas *não habilita a* configuração **Entradas Seguras** da ação.

  ![Saídas seguras como entradas e impacto a jusante na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  As ações Compose, Parse JSON e Response têm apenas a configuração **Entradas seguras.** Quando ligado, o ajuste também oculta as saídas dessas ações. Se essas ações usarem explicitamente as saídas seguras upstream como entradas, a Logic Apps ocultaas entradas e saídas dessas ações, mas *não habilitaa* a configuração **de Entradas Seguras** dessas ações. Se uma ação a jusante usar explicitamente as saídas ocultas das ações Compose, Parse JSON ou Response como entradas, a Logic Apps *não oculta as entradas ou saídas dessa ação a jusante*.

  ![Saídas seguras como entradas com impacto a jusante em ações específicas](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Configuração de entradas seguras**

  Quando você ativa manualmente **entradas seguras** em um gatilho ou ação, o Logic Apps protege essas entradas no histórico de execução. Se uma ação a jusante usar explicitamente as saídas visíveis desse gatilho ou ação como entradas, o Logic Apps oculta as entradas dessa ação a jusante no histórico de execução, mas *não habilita* **entradas seguras** nesta ação e não oculta as saídas desta ação.

  ![Entradas seguras e impacto a jusante na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se as ações Compose, Parse JSON e Response usarem explicitamente as saídas visíveis do gatilho ou da ação que tiver as entradas protegidas, o Logic Apps oculta as entradas e saídas dessas ações, mas *não habilita a* configuração **Entradas Seguras** dessas ações. Se uma ação a jusante usar explicitamente as saídas ocultas das ações Compose, Parse JSON ou Response como entradas, a Logic Apps *não oculta as entradas ou saídas dessa ação a jusante*.

  ![Entradas seguras e impacto a jusante em ações específicas](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso a entradas de parâmetros

Se você implantar em diferentes ambientes, considere parametrizar os valores na definição do fluxo de trabalho que variam de acordo com esses ambientes. Dessa forma, você pode evitar dados codificados usando um [modelo do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para implantar seu aplicativo lógico, proteger dados confidenciais definindo parâmetros protegidos e passar esses dados como entradas separadas através dos [parâmetros do modelo](../azure-resource-manager/templates/template-parameters.md) usando um arquivo de [parâmetros](../azure-resource-manager/templates/parameter-files.md).

Por exemplo, se você autenticar ações HTTP com [o Azure Active Directory OAuth,](#azure-active-directory-oauth-authentication)você pode definir e obscurecer os parâmetros que aceitam o ID do cliente e o segredo do cliente que são usados para autenticação. Para definir esses parâmetros em `parameters` seu aplicativo lógico, use a seção na definição do fluxo de trabalho do seu aplicativo lógico e o modelo resource manager para implantação. Para ocultar valores de parâmetro que você não deseja mostrados ao editar seu `securestring` aplicativo `secureobject` lógico ou visualizar o histórico de execução, defina os parâmetros usando ou digite e use a codificação conforme necessário. Os parâmetros que possuem esse tipo não são retornados com a definição de recurso e não são acessíveis ao visualizar o recurso após a implantação. Para acessar esses valores de parâmetro `@parameters('<parameter-name>')` durante o tempo de execução, use a expressão dentro da definição do fluxo de trabalho. Essa expressão é avaliada apenas em tempo de execução e é descrita pela [Linguagem de Definição de Fluxo de Trabalho](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Se você usar um parâmetro em um cabeçalho ou corpo de solicitação, esse parâmetro pode ser visível quando você visualizar o histórico de execução do seu aplicativo lógico e a solicitação HTTP de saída. Certifique-se de que você também defina suas políticas de acesso ao conteúdo de acordo. Você também pode usar [ofuscação](#obfuscate) para ocultar entradas e saídas em seu histórico de execução. Cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas. Portanto, se um segredo for usado lá, ele não será recuperável.

Para obter mais informações, consulte estas seções neste tópico:

* [Parâmetros seguros nas definições de fluxo de trabalho](#secure-parameters-workflow)
* [Ocultar dados do histórico de execução usando ofuscação](#obfuscate)

Se você [automatizar a implantação de aplicativos lógicos usando modelos do Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você `securestring` pode `secureobject` definir [parâmetros de modelo saqueados,](../azure-resource-manager/templates/template-parameters.md)que são avaliados na implantação, usando os e tipos. Para definir parâmetros de modelo, `parameters` use a seção de nível superior do `parameters` modelo, que é separada e diferente da seção de definição do fluxo de trabalho. Para fornecer os valores para parâmetros de modelo, use um [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado .

Por exemplo, se você usar segredos, você pode definir e usar parâmetros de modelo protegidos que recuperam esses segredos do [Azure Key Vault](../key-vault/key-vault-overview.md) na implantação. Em seguida, você pode referenciar o cofre de chaves e o segredo em seu arquivo de parâmetros. Para saber mais, consulte esses tópicos:

* [Passe valores sensíveis na implantação usando o Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Parâmetros seguros nos modelos do Azure Resource Manager](#secure-parameters-deployment-template) mais tarde neste tópico

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Parâmetros seguros nas definições de fluxo de trabalho

Para proteger informações confidenciais na definição do fluxo de trabalho do seu aplicativo lógico, use parâmetros protegidos para que essas informações não possam ser visíveis depois de salvar seu aplicativo lógico. Por exemplo, suponha que você tenha uma ação HTTP requer autenticação básica, que usa um nome de usuário e senha. Na definição do fluxo `parameters` de trabalho, a seção define os `basicAuthPasswordParam` parâmetros usando `basicAuthUsernameParam` o `securestring` tipo. A definição de ação então `authentication` faz referência a esses parâmetros na seção.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Parâmetros seguros nos modelos do Azure Resource Manager

Um [modelo de Gerenciador de recursos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) para um aplicativo lógico tem várias `parameters` seções. Para proteger senhas, chaves, segredos e outras informações confidenciais, defina parâmetros `securestring` `secureobject` protegidos no nível de modelo e no nível de definição do fluxo de trabalho usando o ou tipo. Em seguida, você pode armazenar esses valores no [Azure Key Vault](../key-vault/key-vault-overview.md) e usar o [arquivo parâmetro](../azure-resource-manager/templates/parameter-files.md) para referenciar o cofre de chaves e o segredo. Em seguida, seu modelo recupera essas informações na implantação. Para obter mais informações, consulte [Passar valores sensíveis na implantação usando o Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Aqui estão mais `parameters` informações sobre essas seções:

* No nível superior do modelo, uma `parameters` seção define os parâmetros para os valores que o modelo usa na *implantação*. Por exemplo, esses valores podem incluir strings de conexão para um ambiente de implantação específico. Em seguida, você pode armazenar esses valores em um [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado, o que facilita a alteração desses valores.

* Dentro da definição de recurso do seu aplicativo lógico, mas fora da definição do fluxo de trabalho, uma `parameters` seção especifica os valores para os parâmetros da definição do fluxo de trabalho. Nesta seção, você pode atribuir esses valores usando expressões de modelo que fazem referência aos parâmetros do seu modelo. Essas expressões são avaliadas na implantação.

* Dentro da definição do `parameters` fluxo de trabalho, uma seção define os parâmetros que seu aplicativo lógico usa em tempo de execução. Em seguida, você pode referenciar esses parâmetros dentro do fluxo de trabalho do seu aplicativo lógico usando expressões de definição de fluxo de trabalho, que são avaliadas em tempo de execução.

Este modelo de exemplo que tem várias `securestring` definições de parâmetros protegidos que usam o tipo:

| Nome do parâmetro | Descrição |
|----------------|-------------|
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita uma senha que é `basicAuthPasswordParam` então passada para o parâmetro da definição do fluxo de trabalho |
| `TemplateUsernameParam` | Um parâmetro de modelo que aceita um nome de usuário que `basicAuthUserNameParam` é então passado para o parâmetro da definição do fluxo de trabalho |
| `basicAuthPasswordParam` | Um parâmetro de definição de fluxo de trabalho que aceita a senha para autenticação básica em uma ação HTTP |
| `basicAuthUserNameParam` | Um parâmetro de definição de fluxo de trabalho que aceita o nome de usuário para autenticação básica em uma ação HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Acesso a serviços e sistemas chamados de aplicativos lógicos

Aqui estão algumas maneiras que você pode ajudar a proteger pontos finais que recebem chamadas ou solicitações do seu aplicativo lógico:

* Adicione autenticação às solicitações de saída.

  Quando você trabalha com um gatilho ou ação baseado em HTTP que faz chamadas de saída, como HTTP, HTTP + Swagger ou Webhook, você pode adicionar autenticação à solicitação enviada pelo seu aplicativo lógico. Por exemplo, você pode usar esses tipos de autenticação:

  * [Autenticação básica](#basic-authentication)

  * [Autenticação de certificado de cliente](#client-certificate-authentication)

  * [Autenticação ativa do Diretório OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticação de identidade gerenciada](#managed-identity-authentication)
  
  Para obter mais informações, consulte [Adicionar autenticação às chamadas de saída](#add-authentication-outbound) mais tarde neste tópico.

* Restringir o acesso a partir de endereços IP do aplicativo lógico.

  Todas as chamadas para pontos finais de aplicativos lógicos são originárias de endereços IP designados específicos que são baseados nas regiões de seus aplicativos lógicos. Você pode adicionar filtragem que aceite solicitações somente de endereços IP. Para obter esses endereços IP, consulte [Limites e configuração para aplicativos de lógica do Azure](logic-apps-limits-and-config.md#configuration).

* Melhore a segurança das conexões aos sistemas locais.

  O Azure Logic Apps oferece integração com esses serviços para ajudar a fornecer uma comunicação no local mais segura e confiável.

  * Gateway de dados local

    Muitos conectores gerenciados no Azure Logic Apps facilitam conexões seguras para sistemas locais, como Sistema de Arquivos, SQL, SharePoint e DB2. O gateway envia dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. Todo o tráfego se origina como tráfego de saída protegido do agente gateway. Saiba [como o gateway de dados local funciona](logic-apps-gateway-install.md#gateway-cloud-service).

  * Conecte-se através do gerenciamento de API do Azure

    O [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) fornece opções de conexão locais, como rede privada virtual site a site e integração do ExpressRoute para proxy seguro e comunicação com sistemas locais. A partir do fluxo de trabalho do seu aplicativo lógico no Logic App Designer, você pode selecionar uma API exposta pelo Gerenciamento de API, que fornece acesso rápido a sistemas locais.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Adicionar autenticação a chamadas de saída

Os pontos finais HTTP e HTTPS suportam vários tipos de autenticação. Com base no gatilho ou ação que você usa para fazer chamadas ou solicitações de saída que acessam esses pontos finais, você pode selecionar entre diferentes faixas de tipos de autenticação. Para garantir que você proteja qualquer informação sensível que seu aplicativo lógico manuseie, use parâmetros protegidos e codifique dados conforme necessário. Para obter mais informações sobre o uso e a proteção de [parâmetros, consulte Acesso a entradas de parâmetros](#secure-action-parameters).

> [!NOTE]
> No Logic App Designer, a propriedade **Autenticação** pode estar oculta em alguns gatilhos e ações onde você pode especificar o tipo de autenticação. Para fazer com que a propriedade apareça nesses casos, no gatilho ou ação, abra a lista **Adicionar novo parâmetro** e selecione **Autenticação**. Para obter mais informações, consulte [Authenticate acesso com identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Tipo de autenticação | Com suporte por |
|---------------------|--------------|
| [Basic](#basic-authentication) | Gerenciamento de API do Azure, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Certificado do Cliente](#client-certificate-authentication) | Gerenciamento de API do Azure, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Gerenciamento de API do Azure, Serviços de Aplicativos Azure, Funções do Azure, HTTP, HTTP + Swagger, WEBHook HTTP |
| [Raw](#raw-authentication) | Gerenciamento de API do Azure, Serviços de Aplicativos Azure, Funções do Azure, HTTP, HTTP + Swagger, WEBHook HTTP |
| [Identidade gerenciada](#managed-identity-authentication) | Gerenciamento de API do Azure, Serviços de Aplicativos Azure, Funções do Azure, HTTP, HTTP + Swagger, WEBHook HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação Básica

Se a opção [Básico](../active-directory-b2c/secure-rest-api.md) estiver disponível, especifique esses valores de propriedade:

| Propriedade (designer) | Property (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Basic | O tipo de autenticação a ser usado |
| **Nome de Usuário** | `username` | Sim | <*nome de usuário*>| O nome de usuário para autenticar o acesso ao ponto de extremidade de serviço de destino |
| **Senha** | `password` | Sim | <*Senha*> | A senha para autenticar o acesso ao ponto de extremidade de serviço de destino |
||||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para lidar e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você pode usar expressões para acessar esses valores de parâmetro satisdar em tempo de execução. Este exemplo de definição de `type` ação `Basic` HTTP especifica a autenticação como e usa a [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autenticação de certificado do cliente

Se a opção [Certificado do Cliente](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) estiver disponível, especifique esses valores de propriedade:

| Propriedade (designer) | Property (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Certificado do Cliente** <br>ou <br>`ClientCertificate` | O tipo de autenticação a ser usado para certificados clientes TLS/SSL. Embora os certificados auto-assinados sejam suportados, os certificados auto-assinados para TLS/SSL não são suportados. |
| **Pfx** | `pfx` | Sim | <*codificado-pfx-conteúdo de arquivo*> | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) <p><p>Para converter o arquivo PFX em formato codificado pelo base64, você pode usar o PowerShell seguindo estas etapas: <p>1. Salve o conteúdo do certificado em uma variável: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converta o conteúdo `ToBase64String()` do certificado usando a função e salve esse conteúdo em um arquivo de texto: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Senha** | `password`| Não | <*senha-para-pfx-arquivo*> | A senha para acessar o arquivo PFX |
|||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para lidar e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você pode usar expressões para acessar esses valores de parâmetro satisdar em tempo de execução. Este exemplo de definição de `type` ação `ClientCertificate` HTTP especifica a autenticação como e usa a [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Para obter mais informações sobre como proteger serviços usando a autenticação do certificado do cliente, consulte estes tópicos:

* [Melhore a segurança das APIs usando a autenticação de certificados do cliente no Gerenciamento de API do Azure](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Melhore a segurança para serviços back-end usando a autenticação de certificado sustal no Gerenciamento de API do Azure](../api-management/api-management-howto-mutual-certificates.md)
* [Melhore a segurança do seu serviço RESTfuL usando certificados de cliente](../active-directory-b2c/secure-rest-api.md)
* [Credenciais de certificado para autenticação do aplicativo](../active-directory/develop/active-directory-certificate-credentials.md)
* [Use um certificado TLS/SSL em seu código no Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Autenticação do Azure Active Directory OAuth

Se a opção [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) estiver disponível, especifique esses valores de propriedade:

| Propriedade (designer) | Property (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Active Directory OAuth** <br>ou <br>`ActiveDirectoryOAuth` | O tipo de autenticação a ser usado. Logic Apps atualmente segue o [protocolo OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Autoridade** | `authority` | Não | <*Emissor de url-for-authority-token*> | A URL da autoridade que fornece o token de autenticação. Por padrão, esse valor é `https://login.windows.net`. |
| **Locatário** | `tenant` | Sim | <*inquilino-ID*> | A ID do locatário para o locatário do Azure AD |
| **Público** | `audience` | Sim | <*recurso para autorizar*> | O recurso que você deseja usar para autorização, por exemplo, `https://management.core.windows.net/` |
| **ID do cliente** | `clientId` | Sim | <*iD cliente-iD*> | A ID do cliente para o aplicativo solicitando a autorização |
| **Tipo de credencial** | `credentialType` | Sim | Certificado <br>ou <br>Segredo | O tipo de credencial que o cliente usa para solicitar autorização. Essa propriedade e valor não aparecem na definição subjacente do seu aplicativo lógico, mas determina as propriedades que aparecem para o tipo de credencial selecionada. |
| **Segredo** | `secret` | Sim, mas apenas para o tipo de credencial "Segredo" | <*cliente secreto*> | O segredo do cliente para solicitar autorização |
| **Pfx** | `pfx` | Sim, mas apenas para o tipo de credencial "Certificado" | <*codificado-pfx-conteúdo de arquivo*> | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) |
| **Senha** | `password` | Sim, mas apenas para o tipo de credencial "Certificado" | <*senha-para-pfx-arquivo*> | A senha para acessar o arquivo PFX |
|||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para lidar e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você pode usar expressões para acessar esses valores de parâmetro satisdar em tempo de execução. Este exemplo de definição de `type` ação `ActiveDirectoryOAuth`HTTP especifica a `Secret`autenticação como , o tipo de credencial como , e usa a [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Autenticação bruta

Se a opção **Raw** estiver disponível, você poderá usar esse tipo de autenticação quando tiver que usar [esquemas de autenticação](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) que não sigam o [protocolo OAuth 2.0](https://oauth.net/2/). Com esse tipo, você cria manualmente o valor do cabeçalho de autorização que você envia com a solicitação de saída e especifica esse valor de cabeçalho no gatilho ou ação.

Por exemplo, aqui está um cabeçalho de amostra para uma solicitação HTTPS que segue o [protocolo OAuth 1.0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

No gatilho ou ação que suporta autenticação bruta, especifique esses valores de propriedade:

| Propriedade (designer) | Property (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Raw | O tipo de autenticação a ser usado |
| **Valor** | `value` | Sim | <*valor de cabeçalho de autorização*> | O valor do cabeçalho de autorização para usar para autenticação |
||||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para lidar e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você pode usar expressões para acessar esses valores de parâmetro satisdar em tempo de execução. Este exemplo de definição de `type` ação `Raw`HTTP especifica a autenticação como , e usa a [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Autenticação de identidade gerenciada

Se a opção [Identidade Gerenciada](../active-directory/managed-identities-azure-resources/overview.md) estiver disponível, seu aplicativo lógico poderá usar a identidade atribuída ao sistema ou uma *única* identidade atribuída pelo usuário criada manualmente para autenticar o acesso aos recursos em outros inquilinos do Azure Active Directory (Azure AD) sem fazer login. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos. Saiba mais sobre [os serviços do Azure que suportam identidades gerenciadas para autenticação AD do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Antes que seu aplicativo lógico possa usar uma identidade gerenciada, siga as etapas do [acesso autenticado aos recursos do Azure usando identidades gerenciadas no Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Essas etapas permitem a identidade gerenciada em seu aplicativo lógico e configuram o acesso dessa identidade ao recurso azure alvo.

1. Antes que uma função Azure possa usar uma identidade gerenciada, primeiro [habilite a autenticação para funções do Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. No gatilho ou ação em que você deseja usar a identidade gerenciada, especifique esses valores de propriedade:

   | Propriedade (designer) | Property (JSON) | Obrigatório | Valor | Descrição |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticação** | `type` | Sim | **Identidade Gerenciada** <br>ou <br>`ManagedServiceIdentity` | O tipo de autenticação a ser usado |
   | **Identidade Gerenciada** | `identity` | Sim | * **Identidade gerenciada atribuída ao sistema** <br>ou <br>`SystemAssigned` <p><p>* <*nome de identidade atribuído pelo usuário*> | A identidade gerenciada para usar |
   | **Público** | `audience` | Sim | <*ID de recursos de destino*> | O ID de recurso para o recurso de destino que você deseja acessar. <p>Por exemplo, `https://storage.azure.com/` torna válido os tokens de acesso para autenticação para todas as contas de armazenamento. No entanto, você também pode especificar uma URL de serviço raiz, como `https://fabrikamstorageaccount.blob.core.windows.net` para uma conta de armazenamento específica. <p>**Nota**: A propriedade **Audience** pode estar escondida em alguns gatilhos ou ações. Para tornar essa propriedade visível, no gatilho ou ação, abra a lista **Adicionar novo parâmetro** e selecione **Audiência**. <p><p>**Importante**: Certifique-se de que esse ID de recurso de destino *corresponda exatamente* ao valor que o Azure AD espera, incluindo quaisquer barras de arrasto necessárias. Assim, o `https://storage.azure.com/` ID de recurso para todas as contas do Azure Blob Storage requer uma barra de saque. No entanto, o ID de recurso para uma conta de armazenamento específica não requer uma barra de arrasto. Para encontrar esses IDs de recursos, consulte [os serviços do Azure que suportam o Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Quando você usa [parâmetros protegidos](#secure-action-parameters) para lidar e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)você pode usar expressões para acessar esses valores de parâmetro satisdar em tempo de execução. Este exemplo de definição de `type` ação `ManagedServiceIdentity` HTTP especifica a autenticação como e usa a [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Próximas etapas

* [Automatize a implantação para aplicativos de lógica do Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorar aplicativos lógicos](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnosticar falhas e problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatizar implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
