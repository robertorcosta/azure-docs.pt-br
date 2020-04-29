---
title: Autenticar com identidades gerenciadas
description: Acessar recursos em outros locatários Azure Active Directory sem entrar com credenciais ou segredos usando uma identidade gerenciada
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117548"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticar o acesso aos recursos do Azure usando identidades gerenciadas em aplicativos lógicos do Azure

Para acessar recursos em outros locatários do Azure Active Directory (AD do Azure) e autenticar sua identidade sem entrar, seu aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (antigamente identidade de serviço gerenciada ou msi), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos.

O aplicativo lógico do Azure dá suporte a identidades gerenciadas atribuídas pelo [*sistema*](../active-directory/managed-identities-azure-resources/overview.md) e pelo [*usuário*](../active-directory/managed-identities-azure-resources/overview.md) . Seu aplicativo lógico pode usar a identidade atribuída pelo sistema ou uma *única* identidade atribuída pelo usuário, que você pode compartilhar em um grupo de aplicativos lógicos, mas não ambos. Atualmente, somente [gatilhos e ações internas específicas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) dão suporte a identidades gerenciadas, não conectores ou conexões gerenciadas, por exemplo:

* HTTP
* Funções do Azure
* Gerenciamento de API do Azure
* Serviços de Aplicativo do Azure

Este artigo mostra como configurar os dois tipos de identidades gerenciadas para seu aplicativo lógico. Para saber mais, consulte esses tópicos:

* [Gatilhos e ações que dão suporte a identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Tipos de autenticação com suporte em chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limites de identidade gerenciada para aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Serviços do Azure que dão suporte à autenticação do Azure AD com identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). A identidade gerenciada e o recurso de destino do Azure onde você precisa de acesso devem usar a mesma assinatura do Azure.

* Para conceder acesso de identidade gerenciada a um recurso do Azure, você precisa adicionar uma função ao recurso de destino para essa identidade. Para adicionar funções, você precisa de [permissões de administrador do Azure ad](../active-directory/users-groups-roles/directory-assign-admin-roles.md) que podem atribuir funções a identidades no locatário do Azure ad correspondente.

* O recurso de destino do Azure que você deseja acessar. Nesse recurso, você adicionará uma função para a identidade gerenciada, que ajuda o aplicativo lógico a autenticar o acesso ao recurso de destino.

* O aplicativo lógico no qual você deseja usar o [gatilho ou as ações que dão suporte a identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Habilitar identidade gerenciada

Para configurar a identidade gerenciada que você deseja usar, siga o link para essa identidade:

* [Identidade atribuída pelo sistema](#system-assigned)
* [Identidade atribuída pelo usuário](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Habilitar identidade atribuída pelo sistema

Ao contrário das identidades atribuídas pelo usuário, você não precisa criar manualmente a identidade atribuída pelo sistema. Para configurar a identidade atribuída pelo sistema para seu aplicativo lógico, aqui estão as opções que você pode usar:

* [Azure portal](#azure-portal-system-logic-app)
* [Modelos do Azure Resource Manager](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Habilitar identidade atribuída pelo sistema no portal do Azure

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Identidade**. Selecione **sistema atribuído** > **ao** > **salvar**. Quando o Azure solicitar que você confirme, selecione **Sim**.

   ![Habilitar a identidade atribuída pelo sistema](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se você receber um erro de que você pode ter apenas uma única identidade gerenciada, seu aplicativo lógico já estará associado à identidade atribuída pelo usuário. Antes de adicionar a identidade atribuída pelo sistema, você deve primeiro *remover* a identidade atribuída pelo usuário do seu aplicativo lógico.

   Seu aplicativo lógico agora pode usar a identidade atribuída pelo sistema, que é registrada com Azure Active Directory e é representada por uma ID de objeto.

   ![ID de objeto para identidade atribuída pelo sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **ID do objeto** | <*Identity-Resource-ID*> | Um GUID (identificador global exclusivo) que representa a identidade atribuída pelo sistema para seu aplicativo lógico em um locatário do Azure AD |
   ||||

1. Agora, siga as [etapas que concedem a essa identidade acesso ao recurso](#access-other-resources) mais adiante neste tópico.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Habilitar identidade atribuída pelo sistema no modelo Azure Resource Manager

Para automatizar a criação e implantação de recursos do Azure, como aplicativos lógicos, você pode usar [modelos de Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Para habilitar a identidade gerenciada atribuída pelo sistema para seu aplicativo lógico no modelo, adicione o `identity` objeto e a `type` Propriedade filho à definição de recurso do aplicativo lógico no modelo, por exemplo:

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

Quando o Azure cria sua definição de recurso de aplicativo `identity` lógico, o objeto obtém estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*ID da entidade*> | O GUID (identificador global exclusivo) do objeto de entidade de serviço para a identidade gerenciada que representa seu aplicativo lógico no locatário do Azure AD. Esse GUID, às vezes, aparece como uma "ID `objectID`de objeto" ou. |
| `tenantId` | <*Azure-AD-Tenant-ID*> | O GUID (identificador global exclusivo) que representa o locatário do Azure AD em que o aplicativo lógico agora é um membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Habilitar identidade atribuída pelo usuário

Para configurar uma identidade gerenciada atribuída pelo usuário para seu aplicativo lógico, você deve primeiro criar essa identidade como um recurso autônomo do Azure separado. Aqui estão as opções que você pode usar:

* [Azure portal](#azure-portal-user-identity)
* [Modelos do Azure Resource Manager](#template-user-identity)
* Azure PowerShell
  * [Criar identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* CLI do Azure
  * [Criar identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* API REST do Azure
  * [Criar identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Adicionar atribuição de função](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Criar identidade atribuída pelo usuário no portal do Azure

1. Na [portal do Azure](https://portal.azure.com), na caixa de pesquisa em qualquer página, digite `managed identities`e selecione **identidades gerenciadas**.

   ![Localize e selecione "identidades gerenciadas"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Em **identidades gerenciadas**, selecione **Adicionar**.

   ![Adicionar nova identidade gerenciada](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Forneça informações sobre sua identidade gerenciada e, em seguida, selecione **criar**, por exemplo:

   ![Criar identidade gerenciada atribuída pelo usuário](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome do recurso** | Sim | <*nome-da-identidade atribuído pelo usuário*> | O nome para fornecer sua identidade atribuída pelo usuário. Este exemplo usa "fabrikam-User-Assigned-Identity". |
   | **Assinatura** | Sim | <*Azure-nome da assinatura*> | O nome da assinatura do Azure a ser usado |
   | **Grupo de recursos** | Sim | <*Azure-Resource-Group-Name*> | O nome do grupo de recursos a ser usado. Crie um novo grupo ou selecione um grupo existente. Este exemplo cria um novo grupo chamado "fabrikam-Managed-Identities-RG". |
   | **Local** | Sim | <*Azure-região*> | A região do Azure onde armazenar informações sobre o recurso. Este exemplo usa "Oeste dos EUA". |
   |||||

   Agora você pode adicionar a identidade atribuída pelo usuário ao seu aplicativo lógico. Você não pode adicionar mais de uma identidade atribuída pelo usuário ao seu aplicativo lógico.

1. No Portal do Azure, encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **identidade**e, em seguida, selecione**adição**de **usuário atribuída** > .

   ![Adicionar identidade gerenciada atribuída pelo usuário](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. No painel **Adicionar identidade gerenciada atribuída pelo usuário** , na lista **assinatura** , selecione sua assinatura do Azure, se ainda não tiver selecionado. Na lista que mostra *todas* as identidades gerenciadas nessa assinatura, localize e selecione a identidade atribuída pelo usuário desejada. Para filtrar a lista, na caixa de pesquisa de **identidades gerenciadas atribuídas pelo usuário** , digite o nome da identidade ou do grupo de recursos. Quando terminar, selecione **Adicionar**.

   ![Selecione a identidade atribuída pelo usuário a ser usada](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se você receber um erro de que você pode ter apenas uma única identidade gerenciada, seu aplicativo lógico já estará associado à identidade atribuída pelo sistema. Antes de adicionar a identidade atribuída pelo usuário, você deve primeiro desabilitar a identidade atribuída pelo sistema em seu aplicativo lógico.

   Seu aplicativo lógico agora está associado à identidade gerenciada atribuída pelo usuário.

   ![Associação com identidade atribuída pelo usuário](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Agora, siga as [etapas que concedem a essa identidade acesso ao recurso](#access-other-resources) mais adiante neste tópico.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Criar identidade atribuída pelo usuário em um modelo de Azure Resource Manager

Para automatizar a criação e implantação de recursos do Azure, como aplicativos lógicos, você pode usar [Azure Resource Manager modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), que dão suporte [a identidades atribuídas pelo usuário para autenticação](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). Na seção do `resources` modelo, a definição de recurso do aplicativo lógico requer estes itens:

* Um `identity` objeto com a `type` propriedade definida como`UserAssigned`

* Um objeto `userAssignedIdentities` filho que especifica a ID de recurso da identidade, que é outro objeto filho que tem `principalId` as `clientId` Propriedades e

Este exemplo mostra uma definição de recurso de aplicativo lógico para uma solicitação HTTP PUT e inclui um `identity` objeto não parametrizado. A resposta para a solicitação PUT e a operação GET subsequente também têm `identity` esse objeto:

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
| `principalId` | <*ID da entidade*> | O GUID (identificador global exclusivo) para a identidade gerenciada atribuída pelo usuário no locatário do Azure AD |
| `clientId` | <*ID do cliente*> | Um GUID (identificador global exclusivo) para a nova identidade do seu aplicativo lógico que é usado para chamadas durante o tempo de execução |
||||

Se o modelo também incluir a definição de recurso de identidade gerenciada, você poderá parametrizar o `identity` objeto. Este exemplo mostra como o objeto `userAssignedIdentities` filho faz referência `userAssignedIdentity` a uma variável que você define na seção `variables` do modelo. Essa variável faz referência à ID de recurso para sua identidade atribuída pelo usuário.

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
| `tenantId` | <*Azure-AD-Tenant-ID*> | O GUID (identificador global exclusivo) que representa o locatário do Azure AD no qual a identidade atribuída pelo usuário agora é um membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que o nome de identidade atribuído pelo usuário. |
| `principalId` | <*ID da entidade*> | O GUID (identificador global exclusivo) para a identidade gerenciada atribuída pelo usuário no locatário do Azure AD |
| `clientId` | <*ID do cliente*> | Um GUID (identificador global exclusivo) para a nova identidade do seu aplicativo lógico que é usado para chamadas durante o tempo de execução |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Conceder acesso de identidade aos recursos

Antes de poder usar a identidade gerenciada do aplicativo lógico para autenticação, configure o acesso para essa identidade no recurso do Azure em que você planeja usar a identidade. Para concluir essa tarefa, atribua a função apropriada a essa identidade no recurso de destino do Azure. Aqui estão as opções que você pode usar:

* [Azure portal](#azure-portal-assign-access)
* [Modelo do Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment))-para obter mais informações, consulte [Adicionar atribuição de função usando o RBAC do Azure e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* CLI do Azure ([criação de atribuição de função AZ](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) – para obter mais informações, consulte [Adicionar atribuição de função usando o RBAC e CLI do Azure do Azure](../role-based-access-control/role-assignments-cli.md).
* [API REST do Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Atribuir acesso no portal do Azure

1. Na [portal do Azure](https://portal.azure.com), vá para o recurso do Azure no qual você deseja que sua identidade gerenciada tenha acesso.

1. No menu do recurso, selecione**atribuições de função** de **controle de acesso (iam)** > , em que você pode examinar as atribuições de função atuais para esse recurso. Na barra de ferramentas, selecione **Adicionar** > **Adicionar atribuição de função**.

   ![Selecione "Adicionar" > "Adicionar atribuição de função"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se a opção **Adicionar atribuição de função** estiver desabilitada, provavelmente você não terá permissões. Para obter mais informações sobre as permissões que permitem gerenciar funções para recursos, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Em **Adicionar atribuição de função**, selecione uma **função** que dê à sua identidade o acesso necessário ao recurso de destino.

   Para o exemplo deste tópico, sua identidade precisa de uma [função que possa acessar o blob em um contêiner de armazenamento do Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![Selecione a função "colaborador de dados do blob de armazenamento"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Siga estas etapas para sua identidade gerenciada:

   * **Identidade atribuída pelo sistema**

     1. Na caixa **atribuir acesso a** , selecione **aplicativo lógico**. Quando a propriedade **assinatura** for exibida, selecione a assinatura do Azure associada à sua identidade.

        ![Selecionar acesso para identidade atribuída pelo sistema](./media/create-managed-service-identity/assign-access-system.png)

     1. Na caixa **selecionar** , selecione seu aplicativo lógico na lista. Se a lista for muito longa, use a caixa **selecionar** para filtrar a lista.

        ![Selecionar aplicativo lógico para identidade atribuída pelo sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identidade atribuída pelo usuário**

     1. Na caixa **atribuir acesso a** , selecione **identidade gerenciada atribuída pelo usuário**. Quando a propriedade **assinatura** for exibida, selecione a assinatura do Azure associada à sua identidade.

        ![Selecionar acesso para identidade atribuída pelo usuário](./media/create-managed-service-identity/assign-access-user.png)

     1. Na caixa **selecionar** , selecione sua identidade na lista. Se a lista for muito longa, use a caixa **selecionar** para filtrar a lista.

        ![Selecione sua identidade atribuída pelo usuário](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Quando terminar, selecione **Salvar**.

   A lista de atribuições de função do recurso de destino agora mostra a identidade e a função gerenciadas selecionadas. Este exemplo mostra como você pode usar a identidade atribuída pelo sistema para um aplicativo lógico e uma identidade atribuída pelo usuário para um grupo de outros aplicativos lógicos.

   ![Funções e identidades gerenciadas adicionadas ao recurso de destino](./media/create-managed-service-identity/added-roles-for-identities.png)

   Para obter mais informações, [atribua um acesso de identidade gerenciada a um recurso usando o portal do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Agora, siga as [etapas para autenticar o acesso com a identidade](#authenticate-access-with-identity) em um gatilho ou ação que dá suporte a identidades gerenciadas.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticar o acesso com identidade gerenciada

Depois de [habilitar a identidade gerenciada para seu aplicativo lógico](#azure-portal-system-logic-app) e [conceder a essa identidade acesso ao recurso ou à entidade de destino](#access-other-resources), você pode usar essa identidade em [gatilhos e ações que dão suporte a identidades gerenciadas](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Se você tiver uma função do Azure na qual deseja usar a identidade atribuída pelo sistema, primeiro [habilite a autenticação para o Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Estas etapas mostram como usar a identidade gerenciada com um gatilho ou ação por meio do portal do Azure. Para especificar a identidade gerenciada em uma definição de JSON subjacente de um gatilho ou ação, consulte [autenticação de identidade gerenciada](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Se você ainda não tiver feito isso, adicione o [gatilho ou a ação que dá suporte a identidades gerenciadas](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Por exemplo, o gatilho ou a ação HTTP pode usar a identidade atribuída pelo sistema que você habilitou para seu aplicativo lógico. Em geral, o gatilho ou a ação HTTP usa essas propriedades para especificar o recurso ou a entidade que você deseja acessar:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Forma** | Sim | O método HTTP usado pela operação que você deseja executar |
   | **URI** | Sim | A URL do ponto de extremidade para acessar a entidade ou o recurso do Azure de destino. A sintaxe de URI geralmente inclui a [ID de recurso](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para o recurso ou serviço do Azure. |
   | **headers** | Não | Todos os valores de cabeçalho que você precisa ou deseja incluir na solicitação de saída, como o tipo de conteúdo |
   | **Consultas** | Não | Todos os parâmetros de consulta que você precisa ou deseja incluir na solicitação, como o parâmetro para uma operação específica ou a versão da API para a operação que você deseja executar |
   | **Autenticação** | Sim | O tipo de autenticação a ser usado para autenticar o acesso ao recurso ou à entidade de destino |
   ||||

   Como um exemplo específico, suponha que você deseja executar a [operação de blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) em um blob na conta de armazenamento do Azure em que você configurou o acesso para sua identidade anteriormente. No entanto, o [conector do armazenamento de BLOBs do Azure](https://docs.microsoft.com/connectors/azureblob/) atualmente não oferece essa operação. Em vez disso, você pode executar essa operação usando a [ação http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ou outra [operação da API REST do serviço blob](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Para acessar contas de armazenamento do Azure por trás de firewalls usando solicitações HTTP e identidades gerenciadas, verifique se você também configurou sua conta de armazenamento com a [exceção que permite o acesso por serviços confiáveis da Microsoft](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Para executar a [operação de blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), a ação http especifica essas propriedades:

   | Propriedade | Obrigatório | Valor de exemplo | Descrição |
   |----------|----------|---------------|-------------|
   | **Forma** | Sim | `PUT`| O método HTTP usado pela operação de blob de instantâneo |
   | **URI** | Sim | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | A ID de recurso para um arquivo de armazenamento de BLOBs do Azure no ambiente global (público) do Azure, que usa essa sintaxe |
   | **headers** | Sim, para o armazenamento do Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Os `x-ms-blob-type` valores `x-ms-version` de cabeçalho e que são necessários para operações de armazenamento do Azure. <p><p>**Importante**: em solicitações de ação e gatilho http de saída para o armazenamento do Azure, o `x-ms-version` cabeçalho requer a propriedade e a versão da API para a operação que você deseja executar. <p>Para saber mais, consulte esses tópicos: <p><p>- [Cabeçalhos de solicitação-blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Controle de versão para serviços de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Consultas** | Sim, para esta operação | `comp` = `snapshot` | O nome e o valor do parâmetro de consulta para a operação de blob de instantâneo. |
   |||||

   Aqui está o exemplo de ação HTTP que mostra todos esses valores de propriedade:

   ![Adicionar uma ação HTTP para acessar um recurso do Azure](./media/create-managed-service-identity/http-action-example.png)

1. Agora, adicione a propriedade de **autenticação** à ação http. Na lista **Adicionar novo parâmetro** , selecione **autenticação**.

   ![Adicionar a propriedade "Authentication" à ação HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem todos os gatilhos e ações dão suporte para permitir que você adicione um tipo de autenticação. Para obter mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Na lista **tipo de autenticação** , selecione **identidade gerenciada**.

   ![Para "autenticação", selecione "identidade gerenciada"](./media/create-managed-service-identity/select-managed-identity.png)

1. Na lista identidade gerenciada, selecione entre as opções disponíveis com base em seu cenário.

   * Se você configurar a identidade atribuída pelo sistema, selecione **identidade gerenciada atribuída pelo sistema** se ainda não estiver selecionada.

     ![Selecione "identidade gerenciada atribuída pelo sistema"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se você configurar uma identidade atribuída pelo usuário, selecione essa identidade, se ainda não tiver selecionado.

     ![Selecione a identidade atribuída pelo usuário](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Este exemplo continua com a **identidade gerenciada atribuída pelo sistema**.

1. Em alguns gatilhos e ações, a propriedade **Audience** também é exibida para que você defina a ID do recurso de destino. Defina a propriedade **Audience** como a [ID de recurso para o recurso ou serviço de destino](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Caso contrário, por padrão, a propriedade **Audience** usa `https://management.azure.com/` a ID do recurso, que é a id do recurso para Azure Resource Manager.

   > [!IMPORTANT]
   > Verifique se a ID do recurso de destino *corresponde exatamente* ao valor que Azure Active Directory (AD) espera, incluindo as barras à direita necessárias. Por exemplo, a ID de recurso para todas as contas de armazenamento de BLOBs do Azure requer uma barra à direita. No entanto, a ID de recurso para uma conta de armazenamento específica não requer uma barra à direita. Verifique as [IDs de recurso dos serviços do Azure que dão suporte ao Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Este exemplo define a propriedade **Audience** como `https://storage.azure.com/` para que os tokens de acesso usados para autenticação sejam válidos para todas as contas de armazenamento. No entanto, você também pode especificar a URL do `https://fabrikamstorageaccount.blob.core.windows.net`serviço raiz,, para uma conta de armazenamento específica.

   ![Definir a propriedade "Audience" para a ID de recurso de destino](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Para obter mais informações sobre como autorizar o acesso com o Azure AD para o armazenamento do Azure, consulte estes tópicos:

   * [Autorizar o acesso a BLOBs e filas do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizar o acesso ao armazenamento do Azure com Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continue criando o aplicativo lógico da maneira desejada.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Desabilitar identidade gerenciada

Para parar de usar uma identidade gerenciada para seu aplicativo lógico, você tem estas opções:

* [Azure portal](#azure-portal-disable)
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

### <a name="disable-managed-identity-in-the-azure-portal"></a>Desabilitar a identidade gerenciada no portal do Azure

No portal do Azure, primeiro remova o acesso da identidade ao [recurso de destino](#disable-identity-target-resource). Em seguida, desative a identidade atribuída pelo sistema ou remova a identidade atribuída pelo usuário do [seu aplicativo lógico](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Remover acesso de identidade dos recursos

1. Na [portal do Azure](https://portal.azure.com), vá para o recurso de destino do Azure no qual você deseja remover o acesso para a identidade gerenciada.

1. No menu do recurso de destino, selecione **controle de acesso (iam)**. Na barra de ferramentas, selecione **atribuições de função**.

1. Na lista funções, selecione as identidades gerenciadas que você deseja remover. Na barra de ferramentas, selecione **remover**.

   > [!TIP]
   > Se a opção **remover** estiver desabilitada, provavelmente você não terá permissões. Para obter mais informações sobre as permissões que permitem gerenciar funções para recursos, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

A identidade gerenciada agora é removida e não tem mais acesso ao recurso de destino.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Desabilitar identidade gerenciada no aplicativo lógico

1. No [Portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **identidade**e siga as etapas para sua identidade:

   * Selecione **sistema atribuído** > **ao** > **salvar**. Quando o Azure solicitar que você confirme, selecione **Sim**.

     ![Desabilitar a identidade atribuída pelo sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecione **usuário atribuído** e a identidade gerenciada e, em seguida, selecione **remover**. Quando o Azure solicitar que você confirme, selecione **Sim**.

     ![Remover a identidade atribuída pelo usuário](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A identidade gerenciada agora está desabilitada em seu aplicativo lógico.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Desabilitar identidade gerenciada no modelo Azure Resource Manager

Se você criou a identidade gerenciada do aplicativo lógico usando um modelo de Azure Resource Manager, defina `identity` a propriedade `type` filho do objeto `None`como. Para a identidade gerenciada pelo sistema, essa ação também exclui a ID da entidade de segurança do Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Próximas etapas

* [Proteger o acesso e os dados no aplicativo lógico do Azure](../logic-apps/logic-apps-securing-a-logic-app.md)