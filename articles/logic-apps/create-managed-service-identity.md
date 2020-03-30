---
title: Autenticar com identidades gerenciadas
description: Acesse recursos em outros inquilinos do Azure Active Directory sem fazer login com credenciais ou segredos usando uma identidade gerenciada
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117548"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticar o acesso aos recursos do Azure usando identidades gerenciadas em Aplicativos lógicos do Azure

Para acessar recursos em outros inquilinos do Azure Active Directory (Azure AD) e autenticar sua identidade sem fazer login, seu aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente Managed Service Identity ou MSI), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos.

O Azure Logic Apps suporta identidades [*gerenciadas atribuídas ao sistema*](../active-directory/managed-identities-azure-resources/overview.md) e [*atribuídas pelo usuário.*](../active-directory/managed-identities-azure-resources/overview.md) Seu aplicativo lógico pode usar a identidade atribuída ao sistema ou uma *única* identidade atribuída pelo usuário, que você pode compartilhar em um grupo de aplicativos lógicos, mas não em ambos. Atualmente, apenas [gatilhos e ações incorporadas específicos](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) suportam identidades gerenciadas, não conectores ou conexões gerenciados, por exemplo:

* HTTP
* Funções do Azure
* Gerenciamento de API do Azure
* Serviços de Aplicativo do Azure

Este artigo mostra como configurar ambos os tipos de identidades gerenciadas para o seu aplicativo lógico. Para saber mais, consulte esses tópicos:

* [Gatilhos e ações que suportam identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Tipos de autenticação suportados em chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limites de identidade gerenciados para aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Serviços do Azure que suportam autenticação AD do Azure com identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). Tanto a identidade gerenciada quanto o recurso alvo do Azure onde você precisa de acesso devem usar a mesma assinatura do Azure.

* Para dar acesso de identidade gerenciado a um recurso do Azure, você precisa adicionar um papel ao recurso de destino para essa identidade. Para adicionar funções, você precisa de [permissões de administrador Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) que possam atribuir funções a identidades no inquilino Azure AD correspondente.

* O recurso alvo do Azure que você deseja acessar. Neste recurso, você adicionará uma função para a identidade gerenciada, o que ajuda o aplicativo lógico a autenticar o acesso ao recurso de destino.

* O aplicativo lógico onde você deseja usar o [gatilho ou ações que suportam identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Habilitar identidade gerenciada

Para configurar a identidade gerenciada que deseja usar, siga o link para essa identidade:

* [Identidade atribuída pelo sistema](#system-assigned)
* [Identidade atribuída pelo usuário](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Habilitar a identidade atribuída ao sistema

Ao contrário das identidades atribuídas pelo usuário, você não precisa criar manualmente a identidade atribuída ao sistema. Para configurar a identidade atribuída ao sistema para o seu aplicativo lógico, aqui estão as opções que você pode usar:

* [Portal Azure](#azure-portal-system-logic-app)
* [Modelos do Azure Resource Manager](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Habilitar identidade atribuída ao sistema no portal Azure

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade**. Selecione **o sistema atribuído** > **em** > **salvar**. Quando o Azure solicitar que você confirme, selecione **Sim**.

   ![Habilite a identidade atribuída ao sistema](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se você tiver um erro de que você pode ter apenas uma única identidade gerenciada, seu aplicativo lógico já está associado à identidade atribuída pelo usuário. Antes de adicionar a identidade atribuída ao sistema, você deve primeiro *remover* a identidade atribuída pelo usuário do seu aplicativo lógico.

   Seu aplicativo lógico agora pode usar a identidade atribuída ao sistema, que está registrada no Azure Active Directory e é representada por um ID de objeto.

   ![ID de objeto para identidade atribuída ao sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **ID do objeto** | <*ID de recursos de identidade*> | Um identificador globalmente exclusivo (GUID) que representa a identidade atribuída ao sistema para o seu aplicativo lógico em um inquilino Azure AD |
   ||||

1. Agora siga as [etapas que dão acesso a essa identidade ao recurso](#access-other-resources) mais tarde neste tópico.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Habilitar a identidade atribuída ao sistema no modelo do Azure Resource Manager

Para automatizar a criação e a implantação de recursos do Azure, como aplicativos lógicos, você pode usar [os modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Para habilitar a identidade gerenciada atribuída ao sistema para `identity` seu aplicativo `type` lógico no modelo, adicione o objeto e a propriedade filho à definição de recurso do aplicativo lógico no modelo, por exemplo:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Quando o Azure cria a `identity` definição de recurso do aplicativo lógico, o objeto obtém essas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | O Globally Unique Identifier (GUID) do objeto principal do serviço para a identidade gerenciada que representa seu aplicativo lógico no inquilino Azure AD. Este GUID às vezes aparece como `objectID`um "ID de objeto" ou . |
| `tenantId` | <*Azure-AD-inquilino-ID*> | O Globally Unique Identifier (GUID) que representa o inquilino Azure AD onde o aplicativo lógico é agora um membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Habilitar a identidade atribuída pelo usuário

Para configurar uma identidade gerenciada atribuída pelo usuário para o seu aplicativo lógico, você deve primeiro criar essa identidade como um recurso azure independente separado. Aqui estão as opções que você pode usar:

* [Portal Azure](#azure-portal-user-identity)
* [Modelos do Azure Resource Manager](#template-user-identity)
* Azure PowerShell
  * [Criar identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* CLI do Azure
  * [Criar identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* API REST do Azure
  * [Criar identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Adicionar atribuição de função](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Crie identidade atribuída ao usuário no portal Azure

1. No [portal Azure](https://portal.azure.com), na caixa de `managed identities`pesquisa em qualquer página, digite e **selecione Identidades Gerenciadas**.

   ![Encontre e selecione "Identidades Gerenciadas"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Em **Identidades Gerenciadas,** selecione **Adicionar**.

   ![Adicionar nova identidade gerenciada](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Forneça informações sobre sua identidade gerenciada e selecione **Criar,** por exemplo:

   ![Criar identidade gerenciada atribuída pelo usuário](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome do recurso** | Sim | <*nome de identidade atribuído pelo usuário*> | O nome para dar sua identidade atribuída ao usuário. Este exemplo usa "Fabrikam-user-atribuído-identidade". |
   | **Assinatura** | Sim | <*Nome de assinatura do Azure*> | O nome da assinatura do Azure a ser usado |
   | **Grupo de recursos** | Sim | <*Nome do grupo azure-resource*> | O nome para o grupo de recursos usar. Crie um novo grupo ou selecione um grupo existente. Este exemplo cria um novo grupo chamado "fabrikam-managed-identities-RG". |
   | **Local** | Sim | <*Região Azul*> | A região do Azure onde armazenar informações sobre o seu recurso. Este exemplo usa "Oeste dos EUA". |
   |||||

   Agora você pode adicionar a identidade atribuída pelo usuário ao seu aplicativo lógico. Você não pode adicionar mais de uma identidade atribuída pelo usuário ao seu aplicativo lógico.

1. No Portal do Azure, encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações,** selecione **Identidade**e selecione**Adicionar** **adicionado ao** > Usuário .

   ![Adicionar identidade gerenciada atribuída pelo usuário](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. No **painel de identidade gerenciado atribuído ao usuário Add,** na lista **de assinaturas,** selecione sua assinatura do Azure se ainda não estiver selecionada. Na lista que mostra *todas as* identidades gerenciadas nessa assinatura, encontre e selecione a identidade atribuída pelo usuário que você deseja. Para filtrar a lista, na caixa de pesquisa **identidades gerenciadas atribuídas** ao Usuário, digite o nome do grupo de identidade ou recurso. Quando terminar, selecione **Adicionar**.

   ![Selecione a identidade atribuída pelo usuário para usar](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se você tiver um erro de que você pode ter apenas uma única identidade gerenciada, seu aplicativo lógico já está associado à identidade atribuída ao sistema. Antes de adicionar a identidade atribuída pelo usuário, você deve primeiro desativar a identidade atribuída ao sistema em seu aplicativo lógico.

   Seu aplicativo lógico agora está associado à identidade gerenciada atribuída pelo usuário.

   ![Associação com a identidade atribuída pelo usuário](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Agora siga as [etapas que dão acesso a essa identidade ao recurso](#access-other-resources) mais tarde neste tópico.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Crie a identidade atribuída pelo usuário em um modelo do Azure Resource Manager

Para automatizar a criação e a implantação de recursos do Azure, como aplicativos lógicos, você pode usar [modelos do Azure Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)que suportam [identidades atribuídas pelo usuário para autenticação.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) Na seção `resources` do seu modelo, a definição de recursos do seu aplicativo lógico requer esses itens:

* Um `identity` objeto `type` com a propriedade definida para`UserAssigned`

* Um `userAssignedIdentities` objeto filho que especifica o ID de recurso da identidade, que é outro objeto filho que tem as `principalId` propriedades e `clientId` propriedades

Este exemplo mostra uma definição de recurso de aplicativo lógico para `identity` uma solicitação HTTP PUT e inclui um objeto não parametrizado. A resposta à solicitação PUT e à `identity` operação GET subsequente também têm esse objeto:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Property (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | O Identificador Globally Unique (GUID) para a identidade gerenciada atribuída pelo usuário no inquilino Azure AD |
| `clientId` | <*iD cliente-iD*> | Um identificador globalmente exclusivo (GUID) para a nova identidade do seu aplicativo lógico que é usado para chamadas durante o tempo de execução |
||||

Se o seu modelo também incluir a definição de recurso da `identity` identidade gerenciada, você poderá parametrizar o objeto. Este exemplo mostra `userAssignedIdentities` como o `userAssignedIdentity` objeto filho faz referência a `variables` uma variável que você define na seção do modelo. Essa variável faz referência ao ID de recurso para sua identidade atribuída pelo usuário.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Property (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-inquilino-ID*> | O Globally Unique Identifier (GUID) que representa o inquilino Azure AD onde a identidade atribuída pelo usuário é agora um membro. Dentro do inquilino Azure AD, o diretor do serviço tem o mesmo nome do nome de identidade atribuído pelo usuário. |
| `principalId` | <*principal-ID*> | O Identificador Globally Unique (GUID) para a identidade gerenciada atribuída pelo usuário no inquilino Azure AD |
| `clientId` | <*iD cliente-iD*> | Um identificador globalmente exclusivo (GUID) para a nova identidade do seu aplicativo lógico que é usado para chamadas durante o tempo de execução |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Dar acesso à identidade aos recursos

Antes de usar a identidade gerenciada do seu aplicativo lógico para autenticação, configure o acesso para essa identidade no recurso Azure onde você planeja usar a identidade. Para concluir essa tarefa, atribua a função apropriada a essa identidade no recurso Azure alvo. Aqui estão as opções que você pode usar:

* [Portal Azure](#azure-portal-assign-access)
* [Modelo de Gerenciador de recursos do Azure](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) - Para obter mais informações, consulte [Adicionar atribuição de função usando o Azure RBAC e o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([az role assignment create)](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)- Para obter mais informações, consulte [Adicionar atribuição de função usando o Azure RBAC e o Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [API REST do Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Atribuir acesso no portal Azure

1. No [portal Azure,](https://portal.azure.com)vá até o recurso Azure onde deseja que sua identidade gerenciada tenha acesso.

1. No menu do recurso, selecione**As atribuições** de função de controle de **acesso (IAM)** > onde você pode rever as atribuições atuais de função para esse recurso. Na barra de ferramentas, selecione **Adicionar** > **atribuição de função**.

   ![Selecione "Adicionar" > "Adicionar atribuição de função"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se a opção **'Adicionar função',** você provavelmente não terá permissões. Para obter mais informações sobre as permissões que permitem gerenciar funções para recursos, consulte [permissões de função administradorno Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Em **Adicionar atribuição de função,** selecione uma **função** que dê à sua identidade o acesso necessário ao recurso de destino.

   Para o exemplo deste tópico, sua identidade precisa de uma [função que possa acessar a bolha em um contêiner azure Storage](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![Selecione a função "Storage Blob Data Contributor"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Siga estas etapas para sua identidade gerenciada:

   * **Identidade atribuída pelo sistema**

     1. No **acesso atribuir à** caixa, selecione **Logic App**. Quando a propriedade **Assinatura** for exibida, selecione a assinatura do Azure associada à sua identidade.

        ![Selecione o acesso para a identidade atribuída ao sistema](./media/create-managed-service-identity/assign-access-system.png)

     1. Na caixa **Selecionar,** selecione seu aplicativo lógico na lista. Se a lista for muito longa, use a caixa **Selecionar** para filtrar a lista.

        ![Selecione o aplicativo lógico para a identidade atribuída ao sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identidade atribuída pelo usuário**

     1. Na caixa **Atribuir acesso à** caixa, selecione **'Usuário atribuído identidade gerenciada .'** Quando a propriedade **Assinatura** for exibida, selecione a assinatura do Azure associada à sua identidade.

        ![Selecione o acesso para a identidade atribuída pelo usuário](./media/create-managed-service-identity/assign-access-user.png)

     1. Na **caixa Selecionar,** selecione sua identidade na lista. Se a lista for muito longa, use a caixa **Selecionar** para filtrar a lista.

        ![Selecione sua identidade atribuída pelo usuário](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Quando terminar, selecione **Salvar**.

   A lista de atribuições de função do recurso de destino agora mostra a identidade e a função gerenciadas selecionadas. Este exemplo mostra como você pode usar a identidade atribuída ao sistema para um aplicativo lógico e uma identidade atribuída pelo usuário para um grupo de outros aplicativos lógicos.

   ![Adicionadoidentidades gerenciados e funções para direcionar recursos](./media/create-managed-service-identity/added-roles-for-identities.png)

   Para obter mais informações, [atribua um acesso gerenciado de identidade a um recurso usando o portal Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Agora siga as [etapas para autenticar o acesso com a identidade](#authenticate-access-with-identity) em um gatilho ou ação que suporte identidades gerenciadas.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticar acesso com identidade gerenciada

Depois [de habilitar a identidade gerenciada para o seu aplicativo lógico](#azure-portal-system-logic-app) e dar a essa identidade acesso ao recurso ou entidade de [destino,](#access-other-resources)você pode usar essa identidade em [gatilhos e ações que suportam identidades gerenciadas.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

> [!IMPORTANT]
> Se você tiver uma função Azure onde deseja usar a identidade atribuída ao sistema, primeiro [habilitar a autenticação para funções do Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Essas etapas mostram como usar a identidade gerenciada com um gatilho ou ação através do portal Azure. Para especificar a identidade gerenciada em uma definição JSON subjacente a um gatilho ou ação, consulte [Autenticação gerenciada de identidade](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Se você ainda não fez isso, adicione o [gatilho ou a ação que suporta identidades gerenciadas](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Por exemplo, o gatilho ou ação HTTP pode usar a identidade atribuída ao sistema que você habilitou para o seu aplicativo lógico. Em geral, o gatilho ou ação HTTP usa essas propriedades para especificar o recurso ou entidade que você deseja acessar:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Método** | Sim | O método HTTP que é usado pela operação que você deseja executar |
   | **URI** | Sim | A URL de ponto final para acessar o recurso ou entidade do Azure alvo. A sintaxe URI geralmente inclui o [ID de recurso](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para o recurso ou serviço do Azure. |
   | **Cabeçalhos** | Não | Quaisquer valores de cabeçalho que você precisa ou deseja incluir na solicitação de saída, como o tipo de conteúdo |
   | **Consultas** | Não | Quaisquer parâmetros de consulta que você precisa ou deseja incluir na solicitação, como o parâmetro para uma operação específica ou a versão aPI para a operação que você deseja executar |
   | **Autenticação** | Sim | O tipo de autenticação a ser usado para autenticar o acesso ao recurso ou entidade-alvo |
   ||||

   Como um exemplo específico, suponha que você deseja executar a [operação Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) em uma bolha na conta azure Storage onde você previamente configurou acesso para sua identidade. No entanto, o [conector Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/) não oferece atualmente esta operação. Em vez disso, você pode executar esta operação usando a [ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ou outra operação [de API Blob Service REST](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Para acessar contas de armazenamento do Azure atrás de firewalls usando solicitações HTTP e identidades gerenciadas, certifique-se de que você também configure sua conta de armazenamento com a [exceção que permite o acesso por serviços confiáveis da Microsoft.](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)

   Para executar a [operação Snapshot Blob,](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)a ação HTTP especifica essas propriedades:

   | Propriedade | Obrigatório | Valor de exemplo | Descrição |
   |----------|----------|---------------|-------------|
   | **Método** | Sim | `PUT`| O método HTTP que a operação Snapshot Blob usa |
   | **URI** | Sim | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | O ID de recurso para um arquivo Azure Blob Storage no ambiente Azure Global (público), que usa essa sintaxe |
   | **Cabeçalhos** | Sim, para armazenamento Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Os `x-ms-blob-type` `x-ms-version` valores de cabeçalho necessários para as operações de armazenamento do Azure. <p><p>**Importante**: Em solicitações de gatilho e ação HTTP de `x-ms-version` saída para o Armazenamento Azure, o cabeçalho requer a propriedade e a versão da API para a operação que você deseja executar. <p>Para saber mais, consulte esses tópicos: <p><p>- [Solicitar cabeçalhos - Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Versão para serviços de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Consultas** | Sim, para esta operação. | `comp` = `snapshot` | O nome e o valor do parâmetro de consulta para a operação Snapshot Blob. |
   |||||

   Aqui está o exemplo de ação HTTP que mostra todos esses valores de propriedade:

   ![Adicione uma ação HTTP para acessar um recurso do Azure](./media/create-managed-service-identity/http-action-example.png)

1. Agora adicione a propriedade **Autenticação** à ação HTTP. Na **lista Adicionar novo parâmetro,** selecione **Autenticação**.

   ![Adicionar propriedade "Autenticação" à ação HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem todos os gatilhos e ações suportam permitir que você adicione um tipo de autenticação. Para obter mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Na lista **de tipos de autenticação,** selecione **Identidade gerenciada**.

   ![Para "Autenticação", selecione "Identidade gerenciada"](./media/create-managed-service-identity/select-managed-identity.png)

1. Na lista de identidade gerenciada, selecione entre as opções disponíveis com base no seu cenário.

   * Se você configurar a identidade atribuída ao sistema, selecione **'Identidade gerenciada atribuída ao sistema',** se ainda não estiver selecionada.'.

     ![Selecione "Identidade gerenciada atribuída ao sistema"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se você configurar uma identidade atribuída pelo usuário, selecione essa identidade se ainda não estiver selecionada.

     ![Selecione a identidade atribuída pelo usuário](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Este exemplo continua com a **identidade gerenciada atribuída ao sistema**.

1. Em alguns gatilhos e ações, a propriedade **Audience** também aparece para você definir o ID de recurso de destino. Defina a propriedade **Audience** como o [ID de recurso para o recurso ou serviço de destino](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Caso contrário, por **Audience** padrão, a `https://management.azure.com/` propriedade Audience usa o ID de recurso, que é o ID de recurso para o Azure Resource Manager.

   > [!IMPORTANT]
   > Certifique-se de que o ID de recurso de destino *corresponda exatamente* ao valor que o Azure Active Directory (AD) espera, incluindo quaisquer barras de arrasto necessárias. Por exemplo, o ID de recurso para todas as contas do Azure Blob Storage requer uma barra de saque. No entanto, o ID de recurso para uma conta de armazenamento específica não requer uma barra de arrasto. Verifique os [IDs de recursos dos serviços do Azure que suportam o Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Este exemplo **Audience** define a `https://storage.azure.com/` propriedade Audience para que os tokens de acesso usados para autenticação sejam válidos para todas as contas de armazenamento. No entanto, você também pode `https://fabrikamstorageaccount.blob.core.windows.net`especificar o URL de serviço raiz, para uma conta de armazenamento específica.

   ![Defina a propriedade "Audience" para direcionar o ID de recursos](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Para obter mais informações sobre como autorizar o acesso ao Azure AD para armazenamento azure, consulte estes tópicos:

   * [Autorize o acesso a blobs e filas do Azure usando o Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorize o acesso ao Armazenamento Azure com o Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continue construindo o aplicativo lógico do jeito que você quiser.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Desativar a identidade gerenciada

Para parar de usar uma identidade gerenciada para o seu aplicativo lógico, você tem essas opções:

* [Portal Azure](#azure-portal-disable)
* [Modelos do Azure Resource Manager](#template-disable)
* Azure PowerShell
  * [Remover atribuição de função](../role-based-access-control/role-assignments-powershell.md)
  * [Excluir identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* CLI do Azure
  * [Remover atribuição de função](../role-based-access-control/role-assignments-cli.md)
  * [Excluir identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* API REST do Azure
  * [Remover atribuição de função](../role-based-access-control/role-assignments-rest.md)
  * [Excluir identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Se você excluir seu aplicativo lógico, o Azure removerá automaticamente a identidade gerenciada do Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Desativar identidade gerenciada no portal Azure

No portal Azure, primeiro remova o acesso da identidade ao [seu recurso de destino](#disable-identity-target-resource). Em seguida, desligue a identidade atribuída ao sistema ou remova a identidade atribuída pelo usuário do [seu aplicativo lógico](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Remover acesso à identidade dos recursos

1. No [portal Azure,](https://portal.azure.com)vá para o recurso azure de destino onde deseja remover o acesso para a identidade gerenciada.

1. No menu do recurso de destino, selecione **Controle de acesso (IAM)**. Na barra de ferramentas, selecione **'Atribuições de função'.**

1. Na lista de funções, selecione as identidades gerenciadas que deseja remover. Na barra de ferramentas, selecione **Remover**.

   > [!TIP]
   > Se a opção **Remover** estiver desativada, provavelmente não terá permissões. Para obter mais informações sobre as permissões que permitem gerenciar funções para recursos, consulte [permissões de função administradorno Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

A identidade gerenciada agora é removida e não tem mais acesso ao recurso de destino.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Desativar identidade gerenciada no aplicativo lógico

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações,** selecione **Identidade**e siga os passos da sua identidade:

   * Selecione **o sistema atribuído** > **em** > **salvar**. Quando o Azure solicitar que você confirme, selecione **Sim**.

     ![Desativar a identidade atribuída ao sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecione **O Usuário atribuído** e a identidade gerenciada e, em seguida, **selecione Remover**. Quando o Azure solicitar que você confirme, selecione **Sim**.

     ![Remova a identidade atribuída pelo usuário](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A identidade gerenciada agora está desativada em seu aplicativo lógico.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Desativar identidade gerenciada no modelo do Azure Resource Manager

Se você criou a identidade gerenciada do aplicativo lógico usando um modelo `identity` do `type` Azure `None`Resource Manager, defina a propriedade filho do objeto para . Para a identidade gerenciada pelo sistema, essa ação também exclui o ID principal do Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Próximas etapas

* [Acesso seguro e dados em Aplicativos lógicos do Azure](../logic-apps/logic-apps-securing-a-logic-app.md)