---
title: Proteger o acesso e os dados
description: Acesso seguro a entradas, saídas, gatilhos baseados em solicitação, histórico de execução, tarefas de gerenciamento e acesso a outros recursos nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: b00dae5c807cb8bec3b9e345c9b2af2c227139b7
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901153"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Proteger o acesso e os dados no aplicativo lógico do Azure

Para controlar o acesso e proteger dados confidenciais em aplicativos lógicos do Azure, você pode configurar a segurança para essas áreas:

* [Acesso a gatilhos baseados em solicitação](#secure-triggers)
* [Acesso a operações de aplicativo lógico](#secure-operations)
* [Acesso a entradas e saídas do histórico de execução](#secure-run-history)
* [Acesso a entradas de parâmetro](#secure-action-parameters)
* [Acesso a serviços e sistemas chamados de aplicativos lógicos](#secure-outbound-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Acesso a gatilhos baseados em solicitação

Se seu aplicativo lógico usa um gatilho baseado em solicitação, que recebe chamadas ou solicitações de entrada, como o gatilho de [solicitação](../connectors/connectors-native-reqres.md) ou [webhook](../connectors/connectors-native-webhook.md) , você pode limitar o acesso para que somente clientes autorizados possam chamar seu aplicativo lógico. Todas as solicitações recebidas por um aplicativo lógico são criptografadas e protegidas com segurança de camada de transporte (TLS), anteriormente conhecido como protocolo SSL (SSL), protocolo.

Aqui estão opções que podem ajudá-lo a proteger o acesso a esse tipo de gatilho:

* [Gerar assinaturas de acesso compartilhado](#sas)
* [Habilitar Azure Active Directory autenticação aberta (OAuth do Azure AD)](#enable-oauth)
* [Restringir endereços IP de entrada](#restrict-inbound-ip-addresses)
* [Adicionar Azure Active Directory autenticação aberta (Azure AD OAuth) ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar SAS (assinaturas de acesso compartilhado)

Cada ponto de extremidade de solicitação em um aplicativo lógico tem uma [assinatura de acesso compartilhado (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) na URL do ponto de extremidade, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL contém o `sp`parâmetro `sv`de consulta `sig` , e, conforme descrito nesta tabela:

| Parâmetro de consulta | Descrição |
|-----------------|-------------|
| `sp` | Especifica as permissões para os métodos HTTP permitidos a serem usados. |
| `sv` | Especifica a versão SAS a ser usada para gerar a assinatura. |
| `sig` | Especifica a assinatura a ser usada para autenticar o acesso ao gatilho. Essa assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos e propriedades de URL. Nunca exposto ou publicado, essa chave é mantida criptografada e armazenada com o aplicativo lógico. Seu aplicativo lógico autoriza somente gatilhos que contenham uma assinatura válida criada com a chave secreta. |
|||

Para obter mais informações sobre como proteger o acesso com SAS, consulte estas seções neste tópico:

* [Regenerar chaves de acesso](#access-keys)
* [Criar URLs de retorno de chamada prestes a expirar](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Para gerar uma nova chave de acesso de segurança a qualquer momento, use a API REST do Azure ou portal do Azure. Todas as URLs geradas anteriormente que usam a chave antiga são invalidadas e não têm mais autorização para disparar o aplicativo lógico. As URLs que você recupera após a regeneração são assinadas com a nova chave de acesso.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico que tem a chave que você deseja regenerar.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Chaves de Acesso**.

1. Selecione a chave que você deseja regenerar e conclua o processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Criar URLs de retorno de chamada prestes a expirar

Se você compartilhar a URL do ponto de extremidade para um gatilho baseado em solicitação com outras partes, poderá gerar URLs de retorno de chamada que usam chaves específicas e têm datas de expiração. Dessa forma, você pode reverter as chaves ou restringir o acesso para disparar seu aplicativo lógico com base em um período de tempo específico. Para especificar uma data de validade para uma URL, use a [API REST dos aplicativos lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `NotAfter`propriedade usando uma cadeia de caracteres de data JSON. Essa propriedade retorna uma URL de retorno de chamada válida somente até a data e hora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Ao gerar ou listar URLs de retorno de chamada para um gatilho baseado em solicitação, você pode especificar a chave a ser usada para assinar a URL. Para gerar uma URL que é assinada por uma chave específica, use a [API REST de aplicativos lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `KeyType` como `Primary` ou `Secondary`. Essa propriedade retorna uma URL que é assinada pela chave de segurança especificada.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>Habilitar Azure Active Directory OAuth

Se seu aplicativo lógico começar com um gatilho de solicitação, você poderá habilitar [Azure Active Directory autenticação aberta](../active-directory/develop/about-microsoft-identity-platform.md) (Azure ad OAuth) para autorizar chamadas de entrada para o gatilho de solicitação. Antes de habilitar essa autenticação, examine estas considerações:

* Seu aplicativo lógico está limitado a um número máximo de políticas de autorização. Cada política de autorização também tem um número máximo de [declarações](../active-directory/develop/developer-glossary.md#claim). Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Uma política de autorização deve incluir pelo menos a declaração do **emissor** , que tem um valor que começa `https://sts.windows.net/` com como a ID do emissor do Azure AD.

* Uma chamada de entrada para seu aplicativo lógico pode usar apenas um esquema de autorização, o OAuth do Azure AD ou as [SAS (assinaturas de acesso compartilhado)](#sas).

* Os tokens OAuth têm suporte apenas para o gatilho de solicitação.

* Somente esquemas de autorização de [tipo de portador](../active-directory/develop/active-directory-v2-protocols.md#tokens) têm suporte para tokens OAuth.

Para habilitar o OAuth do Azure AD, siga estas etapas para adicionar uma ou mais políticas de autorização ao seu aplicativo lógico.

1. Na [portal do Azure](https://portal.microsoft.com), localize e abra seu aplicativo lógico no designer de aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **autorização**. Depois que o painel autorização for aberto, selecione **Adicionar política**.

   ![Selecione "autorização" > "Adicionar política"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Forneça informações sobre a política de autorização especificando os [tipos de declaração](../active-directory/develop/developer-glossary.md#claim) e os valores que seu aplicativo lógico espera no token de autenticação apresentado por cada chamada de entrada para o gatilho de solicitação:

   ![Fornecer informações para a política de autorização](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da política** | Sim | O nome que você deseja usar para a política de autorização |
   | **Declarações** | Sim | Os tipos de declaração e os valores que seu aplicativo lógico aceita de chamadas de entrada. Estes são os tipos de declaração disponíveis: <p><p>- **Emissor** <br>- **Platéia** <br>- **Assunto** <br>- **ID do JWT** (ID do token Web JSON) <p><p>No mínimo, a lista de **declarações** deve incluir a declaração do **emissor** , que tem um valor que começa com a `https://sts.windows.net/` ID do emissor do Azure AD. Para obter mais informações sobre esses tipos de declaração, consulte [declarações em tokens de segurança do Azure ad](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Você também pode especificar seu próprio tipo e valor de declaração. |
   |||

1. Para adicionar outra declaração, selecione uma destas opções:

   * Para adicionar outro tipo de declaração, selecione **Adicionar declaração padrão**, selecione o tipo de declaração e especifique o valor da declaração.

   * Para adicionar sua própria declaração, selecione **Adicionar declaração personalizada**e especifique o valor da declaração personalizada.

1. Para adicionar outra política de autorização, selecione **Adicionar política**. Repita as etapas anteriores para configurar a política.

1. Quando terminar, selecione **Salvar**.

Seu aplicativo lógico agora está configurado para usar o OAuth do Azure AD para autorizar solicitações de entrada. Quando seu aplicativo lógico recebe uma solicitação de entrada que inclui um token de autenticação, os aplicativos lógicos do Azure comparam as declarações do token com as declarações em cada política de autorização. Se houver uma correspondência entre as declarações do token e todas as declarações em pelo menos uma política, a autorização terá sucesso para a solicitação de entrada. O token pode ter mais declarações do que o número especificado pela política de autorização.

Por exemplo, suponha que seu aplicativo lógico tenha uma política de autorização que exija dois tipos de declaração, emissor e público. Este [token de acesso](../active-directory/develop/access-tokens.md) decodificado de exemplo inclui ambos os tipos de declaração:

```json
{
   "aud": "https://management.core.windows.net/",
   "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
   "iat": 1582056988,
   "nbf": 1582056988,
   "exp": 1582060888,
   "_claim_names": {
      "groups": "src1"
   },
   "_claim_sources": {
      "src1": {
         "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
    }
   },
   "acr": "1",
   "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
   "amr": [
      "rsa",
      "mfa"
   ],
   "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
   "appidacr": "2",
   "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
   "family_name": "Sophia Owen",
   "given_name": "Sophia Owen (Fabrikam)",
   "ipaddr": "167.220.2.46",
   "name": "sophiaowen",
   "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
   "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
   "puid": "1003000000098FE48CE",
   "scp": "user_impersonation",
   "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
   "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
   "unique_name": "SophiaOwen@fabrikam.com",
   "upn": "SophiaOwen@fabrikam.com",
   "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
   "ver": "1.0"
}
```

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Restringir endereços IP de entrada

Junto com a SAS (assinatura de acesso compartilhado), talvez você queira limitar especificamente os clientes que podem chamar seu aplicativo lógico. Por exemplo, se você gerenciar seu ponto de extremidade de solicitação usando o gerenciamento de API do Azure, poderá restringir seu aplicativo lógico para aceitar solicitações somente do endereço IP da instância de gerenciamento de API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Restringir intervalos de IP de entrada no portal do Azure

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Em **configuração** > de controle de acesso**permitidos endereços IP de entrada**, selecione **intervalos de IP específicos**.

1. Em **Intervalos de IP para gatilhos**, especifique os intervalos de endereço IP que aceitam o gatilho.

   Um intervalo IP válido usa estes formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x*

Se você quiser que seu aplicativo lógico seja disparado apenas como um aplicativo lógico aninhado, na lista **endereços IP de entrada permitidos** , selecione **somente outros aplicativos lógicos**. Essa opção grava uma matriz vazia em seu recurso de aplicativo lógico. Dessa forma, somente chamadas do serviço de aplicativos lógicos (aplicativos lógicos pai) podem disparar o aplicativo lógico aninhado.

> [!NOTE]
> Independentemente do endereço IP, você ainda pode executar um aplicativo lógico que tenha um gatilho baseado em solicitação usando `/triggers/<trigger-name>/run` por meio da API REST do Azure ou do Gerenciamento de API. No entanto, esse cenário ainda requer [autenticação](../active-directory/develop/authentication-scenarios.md) na API REST do Azure. Todos os eventos aparecem no log de auditoria do Azure. Verifique se você definiu as políticas de controle de acesso de forma adequada.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Restringir intervalos de IP de entrada no modelo de Azure Resource Manager

Se você [automatizar a implantação para aplicativos lógicos usando modelos do Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), poderá especificar os intervalos de IP usando `accessControl` a seção com `triggers` a seção na definição de recurso do aplicativo lógico, por exemplo:

```json
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

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>Adicionar Azure Active Directory autenticação aberta ou outra segurança

Para adicionar mais protocolos de [autenticação](../active-directory/develop/authentication-scenarios.md) ao seu aplicativo lógico, considere usar o serviço de [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) . Esse serviço ajuda você a expor seu aplicativo lógico como uma API e oferece monitoramento, segurança, política e documentação avançados para qualquer ponto de extremidade. O gerenciamento de API pode expor um ponto de extremidade público ou privado para seu aplicativo lógico. Para autorizar o acesso a esse ponto de extremidade, você pode usar [Azure Active Directory autenticação aberta](#azure-active-directory-oauth-authentication) (Azure ad OAuth), [certificado de cliente](#client-certificate-authentication)ou outros padrões de segurança para autorizar o acesso a esse ponto de extremidade. Quando o Gerenciamento de API recebe uma solicitação, o serviço envia a solicitação ao aplicativo lógico, fazendo também quaisquer transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas o gerenciamento de API dispare seu aplicativo lógico, você pode usar as configurações de intervalo de IP de entrada do aplicativo lógico.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso a operações de aplicativo lógico

Você pode permitir que somente usuários ou grupos específicos executem tarefas específicas, como gerenciar, editar e exibir aplicativos lógicos. Para controlar suas permissões, use o [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/role-assignments-portal.md) para que você possa atribuir funções personalizadas ou internas aos membros em sua assinatura do Azure:

* [Colaborador do aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor): permite que você gerencie aplicativos lógicos, mas não pode alterar o acesso a eles.

* [Operador de aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-operator): permite que você leia, habilite e desabilite aplicativos lógicos, mas não pode editá-los ou atualizá-los.

Para impedir que outras pessoas alterem ou excluam seu aplicativo lógico, você pode usar [Bloqueio de Recursos do Azure](../azure-resource-manager/management/lock-resources.md). Essa funcionalidade impede que outras pessoas alterem ou excluam recursos de produção.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Acesso a dados de histórico de execução

Durante a execução de um aplicativo lógico, todos os dados são [criptografados durante o trânsito](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) usando TLS (segurança de camada de transporte) e [em repouso](../security/fundamentals/encryption-atrest.md). Quando seu aplicativo lógico terminar de ser executado, você poderá exibir o histórico dessa execução, incluindo as etapas que foram executadas junto com o status, a duração, as entradas e as saídas de cada ação. Esse detalhe rico fornece informações sobre como seu aplicativo lógico foi executado e onde você pode começar a solucionar problemas que surgem.

Quando você exibe o histórico de execução do aplicativo lógico, os aplicativos lógicos autenticam seu acesso e fornecem links para as entradas e saídas para as solicitações e respostas para cada execução. No entanto, para ações que lidam com senhas, segredos, chaves ou outras informações confidenciais, você deseja impedir que outras pessoas exibam e acessem esses dados. Por exemplo, se seu aplicativo lógico obtiver um segredo de [Azure Key Vault](../key-vault/general/overview.md) para usar ao autenticar uma ação http, você deseja ocultar esse segredo da exibição.

Para controlar o acesso às entradas e saídas no histórico de execução do aplicativo lógico, você tem estas opções:

* [Restringir o acesso por intervalo de endereços IP](#restrict-ip).

  Essa opção ajuda a proteger o acesso ao histórico de execução com base nas solicitações de um intervalo de endereços IP específico.

* [Proteja os dados no histórico de execução usando ofuscação](#obfuscate).

  Em muitos gatilhos e ações, você pode proteger as entradas, saídas ou ambos no histórico de execução de um aplicativo lógico.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restringir o acesso por intervalo de endereços IP

Você pode limitar o acesso às entradas e saídas no histórico de execução do aplicativo lógico para que somente as solicitações de intervalos de endereços IP específicos possam exibir esses dados. Por exemplo, para impedir que qualquer pessoa acesse entradas e saídas, especifique um intervalo de endereços `0.0.0.0-0.0.0.0`IP, como. Somente uma pessoa com permissões de administrador pode remover essa restrição, que fornece a possibilidade de acesso "Just-in-time" aos dados do seu aplicativo lógico. Você pode especificar os intervalos de IP para restringir usando o portal do Azure ou em um modelo de Azure Resource Manager que você usa para a implantação de aplicativo lógico.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Restringir intervalos de IP no portal do Azure

1. No portal do Azure, abra o aplicativo lógico no Designer de Aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Em **configuração** > de controle de acesso**permitidos endereços IP de entrada**, selecione **intervalos de IP específicos**.

1. Em **intervalos IP para conteúdo**, especifique os intervalos de endereços IP que podem acessar o conteúdo de entradas e saídas. 

   Um intervalo IP válido usa estes formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Restringir intervalos de IP no modelo de Azure Resource Manager

Se você [automatizar a implantação para aplicativos lógicos usando modelos do Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), poderá especificar os intervalos de IP usando `accessControl` a seção com `contents` a seção na definição de recurso do aplicativo lógico, por exemplo:

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

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Proteger dados no histórico de execução usando ofuscação

Muitos gatilhos e ações têm configurações para proteger entradas, saídas ou ambos do histórico de execução de um aplicativo lógico. Antes de usar essas configurações para ajudá-lo a proteger esses dados, [examine essas considerações](#obfuscation-considerations).

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Proteger entradas e saídas no designer

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

   ![Abrir o aplicativo lógico no designer de aplicativo lógico](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. No gatilho ou na ação em que você deseja proteger dados confidenciais, selecione o botão de reticências (**...**) e, em seguida, selecione **configurações**.

   ![Abrir configurações de gatilho ou ação](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Ative as **entradas seguras**, as **saídas seguras**ou ambas. Quando tiver terminado, selecione **Concluído**.

   ![Ativar "entradas seguras" ou "saídas seguras"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A ação ou gatilho agora mostra um ícone de cadeado na barra de título.

   ![A barra de título da ação ou do gatilho mostra o ícone de cadeado](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Os tokens que representam saídas seguras de ações anteriores também mostram ícones de bloqueio. Por exemplo, quando você seleciona tal saída da lista de conteúdo dinâmico a ser usada em uma ação, esse token mostra um ícone de cadeado.

   ![Selecionar token para saída segura](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Depois que o aplicativo lógico for executado, você poderá exibir o histórico dessa execução.

   1. No painel **visão geral** do aplicativo lógico, selecione a execução que você deseja exibir.

   1. No painel **execução do aplicativo lógico** , expanda as ações que você deseja examinar.

      Se você optar por obscurecer as entradas e saídas, esses valores agora aparecerão ocultos.

      ![Entradas e saídas ocultas no histórico de execuções](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Proteger entradas e saídas no modo de exibição de código

Na definição de gatilho ou ação subjacente, adicione ou atualize a `runtimeConfiguration.secureData.properties` matriz com um ou ambos os valores:

* `"inputs"`: Protege as entradas no histórico de execução.
* `"outputs"`: Protege as saídas no histórico de execuções.

Aqui estão algumas [considerações a serem examinadas](#obfuscation-considerations) ao usar essas configurações para ajudá-lo a proteger esses dados.

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

#### <a name="considerations-when-securing-inputs-and-outputs"></a>Considerações ao proteger entradas e saídas

* Quando você obscurece as entradas ou saídas em um gatilho ou ação, os aplicativos lógicos não enviam os dados protegidos para o Azure Log Analytics. Além disso, você não pode adicionar [Propriedades rastreadas](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) a esse gatilho ou ação para monitoramento.

* A [API dos aplicativos lógicos para manipular o histórico de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/) não retorna saídas seguras.

* Para proteger as saídas de uma ação que obscurece entradas ou oculta explicitamente as saídas, ative manualmente as **saídas seguras** nessa ação.

* Certifique-se de ativar **entradas seguras** ou **proteger as saídas** em ações de downstream em que você espera que o histórico de execução oculte esses dados.

  **Configuração de saídas seguras**

  Quando você ativa manualmente as **saídas seguras** em um gatilho ou ação, os aplicativos lógicos ocultam essas saídas no histórico de execuções. Se uma ação downstream usar explicitamente essas saídas protegidas como entradas, os aplicativos lógicos ocultarão as entradas dessa ação no histórico de execuções, mas *não habilitarão* a configuração de **entradas seguras** da ação.

  ![Saídas protegidas como entradas e impacto de downstream na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  As ações compor, analisar JSON e resposta têm apenas a configuração **entradas seguras** . Quando ativado, a configuração também oculta as saídas das ações. Se essas ações usarem explicitamente as saídas protegidas de upstream como entradas, os aplicativos lógicos ocultarão as entradas e saídas das ações, mas *não habilitarão* a configuração de **entradas seguras** de ações. Se uma ação downstream usar explicitamente as saídas ocultas das ações compor, analisar JSON ou resposta como entradas, os aplicativos lógicos *não ocultarão as entradas ou saídas da ação downstream*.

  ![Saídas protegidas como entradas com impacto de downstream em ações específicas](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Configuração de entradas seguras**

  Quando você ativa manualmente as **entradas seguras** em um gatilho ou uma ação, os aplicativos lógicos ocultam essas entradas no histórico de execuções. Se uma ação downstream usar explicitamente as saídas visíveis desse gatilho ou ação como entradas, os aplicativos lógicos ocultarão as entradas da ação downstream no histórico de execução, mas *não habilitarão* **entradas seguras** nessa ação e não ocultarão as saídas dessa ação.

  ![Entradas seguras e impacto de downstream na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se as ações Compose, analisar JSON e resposta usarem explicitamente as saídas visíveis do gatilho ou da ação que tem as entradas protegidas, os aplicativos lógicos ocultarão as entradas e saídas das ações, mas *não habilitarão* a configuração de **entradas seguras** da ação. Se uma ação downstream usar explicitamente as saídas ocultas das ações compor, analisar JSON ou resposta como entradas, os aplicativos lógicos *não ocultarão as entradas ou saídas da ação downstream*.

  ![Entradas seguras e impacto de downstream em ações específicas](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso a entradas de parâmetro

Se você implantar em ambientes diferentes, considere a possibilidade de parametrização dos valores na definição do fluxo de trabalho que variam de acordo com esses ambientes. Dessa forma, você pode evitar dados embutidos em código usando um [modelo de Azure Resource Manager](../azure-resource-manager/templates/overview.md) para implantar seu aplicativo lógico, proteger dados confidenciais definindo parâmetros protegidos e passá-los como entradas separadas por meio dos [parâmetros do modelo](../azure-resource-manager/templates/template-parameters.md) usando um arquivo de [parâmetro](../azure-resource-manager/templates/parameter-files.md).

Por exemplo, se você autenticar ações HTTP com [Azure Active Directory autenticação aberta](#azure-active-directory-oauth-authentication) (Azure ad OAuth), poderá definir e obscurecer os parâmetros que aceitam a ID do cliente e o segredo do cliente que são usados para autenticação. Para definir esses parâmetros em seu aplicativo lógico, use a `parameters` seção na definição de fluxo de trabalho do aplicativo lógico e no modelo do Resource Manager para implantação. Para ajudar a proteger os valores de parâmetro que você não deseja mostrar ao editar seu aplicativo lógico ou exibir o histórico de execução, defina `securestring` os `secureobject` parâmetros usando o tipo ou e use a codificação conforme necessário. Parâmetros que têm esse tipo não são retornados com a definição de recurso e não são acessíveis ao exibir o recurso após a implantação. Para acessar esses valores de parâmetro durante o tempo de `@parameters('<parameter-name>')` execução, use a expressão dentro de sua definição de fluxo de trabalho. Essa expressão é avaliada apenas em tempo de execução e é descrita pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Se você usar um parâmetro em um cabeçalho ou corpo de solicitação, esse parâmetro poderá ser visível quando você exibir o histórico de execução do aplicativo lógico e a solicitação HTTP de saída. Certifique-se de também definir suas políticas de acesso de conteúdo adequadamente. Você também pode usar [ofuscação](#obfuscate) para ocultar entradas e saídas em seu histórico de execução. Cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas. Portanto, se um segredo for usado lá, ele não será recuperável.

Para obter mais informações, consulte estas seções neste tópico:

* [Proteger parâmetros em definições de fluxo de trabalho](#secure-parameters-workflow)
* [Proteger dados no histórico de execução usando ofuscação](#obfuscate)

Se você [automatizar a implantação para aplicativos lógicos usando modelos do Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), poderá definir [parâmetros de modelo](../azure-resource-manager/templates/template-parameters.md)protegidos, que são avaliados na implantação, `securestring` usando `secureobject` os tipos e. Para definir parâmetros de modelo, use a seção de nível `parameters` superior do modelo, que é separada e diferente da `parameters` seção de sua definição de fluxo de trabalho. Para fornecer os valores para parâmetros de modelo, use um [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado.

Por exemplo, se você usar segredos, poderá definir e usar parâmetros de modelo protegidos que recuperem esses segredos de [Azure Key Vault](../key-vault/general/overview.md) na implantação. Em seguida, você pode fazer referência ao cofre de chaves e ao segredo em seu arquivo de parâmetro. Para saber mais, consulte esses tópicos:

* [Passar valores confidenciais na implantação usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Proteger os parâmetros em modelos de Azure Resource Manager](#secure-parameters-deployment-template) mais adiante neste tópico

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteger parâmetros em definições de fluxo de trabalho

Para proteger informações confidenciais na definição de fluxo de trabalho do aplicativo lógico, use parâmetros protegidos para que essas informações não fiquem visíveis depois que você salvar seu aplicativo lógico. Por exemplo, suponha que você tenha uma ação HTTP requer autenticação básica, que usa um nome de usuário e senha. Na definição de fluxo de trabalho `parameters` , a seção `basicAuthPasswordParam` define `basicAuthUsernameParam` os parâmetros e usando `securestring` o tipo. Em seguida, a definição de ação faz referência `authentication` a esses parâmetros na seção.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Proteger parâmetros em modelos de Azure Resource Manager

Um [modelo do Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) para um aplicativo lógico tem `parameters` várias seções. Para proteger senhas, chaves, segredos e outras informações confidenciais, defina parâmetros protegidos no nível do modelo e nível de definição de fluxo de trabalho `securestring` usando `secureobject` o tipo ou. Em seguida, você pode armazenar esses valores em [Azure Key Vault](../key-vault/general/overview.md) e usar o [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md) para fazer referência ao cofre de chaves e ao segredo. Em seguida, o modelo recupera essas informações na implantação. Para obter mais informações, consulte [passar valores confidenciais na implantação usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Aqui estão mais informações sobre estas `parameters` seções:

* No nível superior do modelo, uma `parameters` seção define os parâmetros para os valores que o modelo usa na *implantação*. Por exemplo, esses valores podem incluir cadeias de conexão para um ambiente de implantação específico. Você pode armazenar esses valores em um arquivo de [parâmetro](../azure-resource-manager/templates/parameter-files.md)separado, o que torna a alteração desses valores mais fácil.

* Dentro da definição de recurso do aplicativo lógico, mas fora de sua definição de `parameters` fluxo de trabalho, uma seção especifica os valores para os parâmetros da definição de fluxo de trabalho. Nesta seção, você pode atribuir esses valores usando expressões de modelo que fazem referência aos parâmetros do modelo. Essas expressões são avaliadas na implantação.

* Dentro de sua definição de fluxo `parameters` de trabalho, uma seção define os parâmetros que seu aplicativo lógico usa no tempo de execução. Em seguida, você pode fazer referência a esses parâmetros dentro do fluxo de trabalho do aplicativo lógico usando expressões de definição de fluxo de trabalho, que são avaliadas no tempo de execução.

Este modelo de exemplo que tem várias definições de parâmetros protegidas `securestring` que usam o tipo:

| Nome do parâmetro | Descrição |
|----------------|-------------|
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita uma senha que é passada para o parâmetro da `basicAuthPasswordParam` definição de fluxo de trabalho |
| `TemplateUsernameParam` | Um parâmetro de modelo que aceita um nome de usuário que é passado para o parâmetro `basicAuthUserNameParam` da definição de fluxo de trabalho |
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

<a name="secure-outbound-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Acesso a serviços e sistemas chamados de aplicativos lógicos

Aqui estão algumas maneiras que você pode ajudar a proteger pontos de extremidade que recebem chamadas ou solicitações de seu aplicativo lógico:

* Adicionar autenticação a solicitações de saída.

  Quando você trabalha com um gatilho ou uma ação baseada em HTTP que faz chamadas de saída, como HTTP, HTTP + Swagger ou webhook, você pode adicionar autenticação à solicitação enviada pelo seu aplicativo lógico. Por exemplo, você pode selecionar estes tipos de autenticação:

  * [Autenticação básica](#basic-authentication)

  * [Autenticação de certificado de cliente](#client-certificate-authentication)

  * [Active Directory Autenticação OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticação de identidade gerenciada](#managed-identity-authentication)
  
  Para obter mais informações, consulte [Adicionar autenticação a chamadas de saída](#add-authentication-outbound) mais adiante neste tópico.

* Restringir o acesso de endereços IP do aplicativo lógico.

  Todas as chamadas para pontos de extremidade de aplicativos lógicos originam-se de endereços IP designados específicos que se baseiam nas regiões dos seus aplicativos lógicos. Você pode adicionar filtragem que aceite solicitações somente de endereços IP. Para obter esses endereços IP, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md#configuration).

* Aumente a segurança de conexões com sistemas locais.

  Os aplicativos lógicos do Azure fornecem integração com esses serviços para ajudar a fornecer comunicação local mais segura e confiável.

  * Gateway de dados local

    Muitos conectores gerenciados nos aplicativos lógicos do Azure facilitam conexões seguras com sistemas locais, como sistema de arquivos, SQL, SharePoint e DB2. O gateway envia dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. Todo o tráfego é originado como tráfego de saída protegido do agente de gateway. Saiba [como o gateway de dados local funciona](logic-apps-gateway-install.md#gateway-cloud-service).

  * Conectar-se por meio do gerenciamento de API do Azure

    O [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) fornece opções de conexão locais, como rede privada virtual site a site e integração do ExpressRoute para proxy seguro e comunicação com sistemas locais. Do fluxo de trabalho do aplicativo lógico no designer do aplicativo lógico, você pode selecionar uma API que é exposta pelo gerenciamento de API, que fornece acesso rápido a sistemas locais.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Adicionar autenticação a chamadas de saída

Os pontos de extremidade HTTP e HTTPS dão suporte a vários tipos de autenticação. Com base no gatilho ou na ação que você usa para fazer chamadas ou solicitações de saída que acessam esses pontos de extremidade, você pode selecionar entre intervalos variados de tipos de autenticação. Para garantir que você proteja todas as informações confidenciais que seu aplicativo lógico manipula, use parâmetros protegidos e codifique os dados conforme necessário. Para obter mais informações sobre como usar e proteger parâmetros, consulte [acesso a entradas de parâmetro](#secure-action-parameters).

> [!NOTE]
> No designer do aplicativo lógico, a propriedade de **autenticação** pode estar oculta em alguns gatilhos e ações em que você pode especificar o tipo de autenticação. Para fazer com que a propriedade apareça nesses casos, no gatilho ou na ação, abra a lista **Adicionar novo parâmetro** e selecione **autenticação**. Para obter mais informações, consulte [autenticar o acesso com identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

| Tipo de autenticação | Com suporte por |
|---------------------|--------------|
| [Basic](#basic-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, HTTP, HTTP + Swagger, webhook HTTP |
| [Certificado do cliente](#client-certificate-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, HTTP, HTTP + Swagger, webhook HTTP |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, Azure Functions, HTTP, HTTP + Swagger, webhook HTTP |
| [Recebem](#raw-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, Azure Functions, HTTP, HTTP + Swagger, webhook HTTP |
| [Identidade gerenciada](#managed-identity-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, Azure Functions, HTTP, HTTP + Swagger, webhook HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação básica

Se a opção [básica](../active-directory-b2c/secure-rest-api.md) estiver disponível, especifique esses valores de propriedade:

| Propriedade (Designer) | Property (JSON) | Necessária | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Basic | O tipo de autenticação a ser usado |
| **Nome de usuário** | `username` | Sim | <*nome do usuário*>| O nome de usuário para autenticar o acesso ao ponto de extremidade de serviço de destino |
| **Senha** | `password` | Sim | <*la*> | A senha para autenticar o acesso ao ponto de extremidade de serviço de destino |
||||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo de Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em tempo de execução. Esta definição de ação HTTP de exemplo especifica `type` a `Basic` autenticação como e usa a [função Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

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

Se a opção de [certificado de cliente](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) estiver disponível, especifique esses valores de propriedade:

| Propriedade (Designer) | Property (JSON) | Necessária | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Certificado do cliente** <br>ou <br>`ClientCertificate` | O tipo de autenticação a ser usado para certificados de cliente TLS/SSL <p><p>**Observação**: embora haja suporte para certificados autoassinados, não há suporte para certificados autoassinados para TLS/SSL. O conector HTTP não oferece suporte a certificados TLS/SSL intermediários. |
| **Pfx** | `pfx` | Sim | <*Encoded-arquivo PFX-conteúdo*> | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) <p><p>Para converter o arquivo PFX em formato codificado em base64, você pode usar o PowerShell seguindo estas etapas: <p>1. Salve o conteúdo do certificado em uma variável: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. converta o conteúdo do certificado usando `ToBase64String()` a função e salve esse conteúdo em um arquivo de texto: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Senha** | `password`| Não | <*senha para arquivo PFX*> | A senha para acessar o arquivo PFX |
|||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo de Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em tempo de execução. Esta definição de ação HTTP de exemplo especifica `type` a `ClientCertificate` autenticação como e usa a [função Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

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

Para obter mais informações sobre como proteger serviços usando a autenticação de certificado do cliente, consulte estes tópicos:

* [Melhorar a segurança para APIs usando a autenticação de certificado de cliente no gerenciamento de API do Azure](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Melhorar a segurança para serviços de back-end usando a autenticação de certificado de cliente no gerenciamento de API do Azure](../api-management/api-management-howto-mutual-certificates.md)
* [Melhorar a segurança para seu serviço RESTfuL usando certificados de cliente](../active-directory-b2c/secure-rest-api.md)
* [Credenciais de certificado para autenticação do aplicativo](../active-directory/develop/active-directory-certificate-credentials.md)
* [Usar um certificado TLS/SSL no seu código no Serviço de Aplicativo do Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory a autenticação aberta

Em gatilhos de solicitação, você pode usar [Azure Active Directory autenticação aberta](../active-directory/develop/about-microsoft-identity-platform.md) (Azure ad OAuth) para autenticar chamadas de entrada depois de [configurar as políticas de autorização do Azure ad](#enable-oauth) para seu aplicativo lógico. Para todos os outros gatilhos e ações que fornecem o Active Directory tipo de autenticação **OAuth** a ser selecionado, especifique estes valores de propriedade:

| Propriedade (Designer) | Property (JSON) | Necessária | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Active Directory OAuth** <br>ou <br>`ActiveDirectoryOAuth` | O tipo de autenticação a ser usado. Os aplicativos lógicos atualmente seguem o [protocolo OAuth 2,0](../active-directory/develop/v2-overview.md). |
| **Authority** | `authority` | Não | <*URL-para-Authority-token-emissor*> | A URL para a autoridade que fornece o token de autenticação. Por padrão, esse valor é `https://login.windows.net`. |
| **Vários** | `tenant` | Sim | <*ID do locatário*> | A ID do locatário para o locatário do Azure AD |
| **Platéia** | `audience` | Sim | <*recurso a ser autorizado*> | O recurso que você deseja usar para autorização, por exemplo, `https://management.core.windows.net/` |
| **ID do Cliente** | `clientId` | Sim | <*ID do cliente*> | A ID do cliente para o aplicativo solicitando a autorização |
| **Tipo de Credencial** | `credentialType` | Sim | Certificado <br>ou <br>Segredo | O tipo de credencial que o cliente usa para solicitar autorização. Essa propriedade e o valor não aparecem na definição subjacente do aplicativo lógico, mas determina as propriedades que aparecem para o tipo de credencial selecionado. |
| **Segredo** | `secret` | Sim, mas apenas para o tipo de credencial "segredo" | <*segredo do cliente*> | O segredo do cliente para solicitar autorização |
| **Pfx** | `pfx` | Sim, mas somente para o tipo de credencial "certificado" | <*Encoded-arquivo PFX-conteúdo*> | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) |
| **Senha** | `password` | Sim, mas somente para o tipo de credencial "certificado" | <*senha para arquivo PFX*> | A senha para acessar o arquivo PFX |
|||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo de Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em tempo de execução. Esta definição de ação HTTP de exemplo especifica `type` a `ActiveDirectoryOAuth`autenticação como, o tipo `Secret`de credencial como e usa a [função Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

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

Se a opção **RAW** estiver disponível, você poderá usar esse tipo de autenticação quando precisar usar [esquemas de autenticação](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) que não seguem o [protocolo OAuth 2,0](https://oauth.net/2/). Com esse tipo, você cria manualmente o valor do cabeçalho de autorização que você envia com a solicitação de saída e especifica esse valor de cabeçalho em seu gatilho ou ação.

Por exemplo, aqui está um cabeçalho de exemplo para uma solicitação HTTPS que segue o [protocolo OAuth 1,0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

No gatilho ou ação que dá suporte à autenticação bruta, especifique estes valores de propriedade:

| Propriedade (Designer) | Property (JSON) | Necessária | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Raw | O tipo de autenticação a ser usado |
| **Valor** | `value` | Sim | <*autorização-cabeçalho-valor*> | O valor do cabeçalho de autorização a ser usado para autenticação |
||||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo de Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em tempo de execução. Esta definição de ação HTTP de exemplo especifica `type` a `Raw`autenticação como e usa a [função Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

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

Se a opção de [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) estiver disponível, seu aplicativo lógico poderá usar a identidade atribuída pelo sistema ou uma *única* identidade atribuída pelo usuário criada manualmente para autenticar o acesso a recursos em outros locatários do Azure Active Directory (AD do Azure) sem entrar. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais porque você não precisa fornecer ou girar segredos. Saiba mais sobre os [Serviços do Azure que dão suporte a identidades gerenciadas para autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Antes que seu aplicativo lógico possa usar uma identidade gerenciada, siga as etapas em [autenticar o acesso aos recursos do Azure usando identidades gerenciadas nos aplicativos lógicos do Azure](../logic-apps/create-managed-service-identity.md). Essas etapas habilitam a identidade gerenciada em seu aplicativo lógico e configuram o acesso da identidade ao recurso do Azure de destino.

1. Antes que uma função do Azure possa usar uma identidade gerenciada, primeiro [habilite a autenticação do Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. No gatilho ou na ação em que você deseja usar a identidade gerenciada, especifique estes valores de propriedade:

   | Propriedade (Designer) | Property (JSON) | Necessária | Valor | Descrição |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticação** | `type` | Sim | **Identidade gerenciada** <br>ou <br>`ManagedServiceIdentity` | O tipo de autenticação a ser usado |
   | **Identidade gerenciada** | `identity` | Sim | * **Identidade gerenciada atribuída ao sistema** <br>ou <br>`SystemAssigned` <p><p>* <*nome de identidade atribuído pelo usuário*> | A identidade gerenciada a ser usada |
   | **Platéia** | `audience` | Sim | <*Target-Resource-ID*> | A ID de recurso para o recurso de destino que você deseja acessar. <p>Por exemplo, `https://storage.azure.com/` o torna os [tokens de acesso](../active-directory/develop/access-tokens.md) para autenticação válidos para todas as contas de armazenamento. No entanto, você também pode especificar uma URL de serviço raiz `https://fabrikamstorageaccount.blob.core.windows.net` , como para uma conta de armazenamento específica. <p>**Observação**: a propriedade **Audience** pode estar oculta em alguns gatilhos ou ações. Para tornar essa propriedade visível, no gatilho ou na ação, abra a lista **Adicionar novo parâmetro** e selecione **público**. <p><p>**Importante**: Verifique se essa ID de recurso de destino *corresponde exatamente* ao valor esperado pelo Azure AD, incluindo as barras à direita necessárias. Portanto, a `https://storage.azure.com/` ID de recurso para todas as contas de armazenamento de BLOBs do Azure requer uma barra à direita. No entanto, a ID de recurso para uma conta de armazenamento específica não requer uma barra à direita. Para encontrar essas IDs de recurso, consulte [Serviços do Azure que dão suporte ao Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo de Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em tempo de execução. Esta definição de ação HTTP de exemplo especifica `type` a `ManagedServiceIdentity` autenticação como e usa a [função Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

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

* [Automatizar a implantação para aplicativos lógicos do Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorar aplicativos lógicos](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnosticar falhas e problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatizar implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
