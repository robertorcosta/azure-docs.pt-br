---
title: Collaborate with others - LUIS
titleSuffix: Azure Cognitive Services
description: An app owner can add contributors to the authoring resource. These contributors can modify the model, train, and publish the app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: efeb8b28ef2b8eec480fcf5090e0173d6451deef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225524"
---
# <a name="add-contributors-to-your-app"></a>Add contributors to your app

An app owner can add contributors to apps. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Add contributor to Azure authoring resource

The following procedure is for all users that have **migrated** to use the Azure authoring resource.

You have migrated if your LUIS authoring experience is tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

1. In the Azure portal, find the Language Understanding (LUIS) authoring resource. It has the type `LUIS.Authoring`.
1. On this resource's **Access Control (IAM)** page, select **+Add** then select **Add role assignment**.

    ![In Azure portal, add role assignment on authoring resource.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. In the **Add role assignment** window, select the **Role** of Contributor. In the **Assign access to** option, select **Azure AD user, group, or service principal**. In the **Select** option, enter the user's email address. If the user is known by more than 1 email address for the same domain, make sure the enter the _primary_ email account.

    ![Add user's email to the contributor role for Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    When the user's email is found, select the account and select **Save**. 

    If you have trouble with this role assignment, review [Azure role assignments](../../role-based-access-control/role-assignments-portal.md) and [Azure access control troubleshooting](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Add collaborator to LUIS app

The following procedure is for all users that have **not migrated** to use the Azure authoring resource.

You have not migrated if your LUIS authoring experience is not tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

Um aplicativo tem um único autor, mas pode ter vários colaboradores. Para permitir que os colaboradores editem o aplicativo LUIS, você deve adicionar o email que eles usam para acessar o portal do LUIS à lista de colaboradores. Depois que eles são adicionados, o aplicativo aparece no portal do LUIS deles.

1. Selecione **Gerenciar** no menu superior direito e selecione **Colaboradores** no menu esquerdo.

1. Selecione **Adicionar Colaborador** na barra de ferramentas.

1. Insira o endereço de email que o colaborador usa para entrar no portal do LUIS.

    ![Adicionar o endereço de email do colaborado](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Users with multiple emails 

If you add contributors/collaborators to a LUIS app, you are specifying the exact email address. While Azure Active Directory (Azure AD) allows a single user to have more than one email account used interchangeably, LUIS requires the user to sign in with the email address specified when adding the contributor/collaborator.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Recursos do Azure Active Directory

Se você usar o [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) em sua organização, o Reconhecimento Vocal (LUIS) precisará de permissão para acessar as informações sobre o acesso de seus usuários quando eles quiserem usar o LUIS. Os recursos exigidos pelo LUIS são mínimos. 

Você vê a descrição detalhada ao tentar se inscrever com uma conta que tem consentimento do administrador ou não precisa dele, como o consentimento do administrador:

* Permite que você entre no aplicativo com sua conta organizacional e permite que o aplicativo leia seu perfil. Ele também permite que o aplicativo leia informações básicas da empresa. Isso permite ao LUIS ler dados de perfil básicos, como ID de usuário, email e nome
* Permite que o aplicativo veja e atualize seus dados, mesmo quando não estiver usando o aplicativo no momento. A permissão é necessária para atualizar o token de acesso do usuário.


### <a name="azure-active-directory-tenant-user"></a>Usuário de locatário do Azure Active Directory

LUIS usa o fluxo de autorização padrão do Azure Active Directory (Azure AD). 

O administrador do locatário deverá trabalhar diretamente com o usuário que precisa de ter o acesso concedido para usar LUIS no Azure AD. 

* Primeiro, o usuário faz logon no LUIS e verá a caixa de diálogo pop-up que necessita de aprovação do administrador. O usuário entra em contato com o administrador do locatário antes de continuar. 
* Segundo, o administrador do locatário faz logon no LUIS e verá uma caixa de diálogo de pop-up de fluxo de autorização. Esta é a caixa de diálogo que o administrador deve conceder permissão para o usuário. Depois que o administrador aceita a permissão, o usuário pode continuar com o LUIS. Se o administrador do locatário não fizer logon no LUIS, o administrador pode acessar [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) para LUIS, mostrado na captura de tela a seguir. Observe que a lista é filtrada para itens que incluem o nome `LUIS`.

![Permissão do Azure Active Directory pelo website do aplicativo](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador de locatários desejar que somente determinados usuários usem o LUIS, haverá duas soluções possíveis:
* Fornecer o "consentimento do administrador" (consentimento a todos os usuários do Azure AD), mas, em seguida, definir como "Sim" a opção "Atribuição de usuário obrigatória" em Propriedades do Aplicativo Empresarial e, por fim, atribuir/adicionar somente os usuários desejados ao Aplicativo. Com esse método, o Administrador ainda fornece o "consentimento do administrador" ao Aplicativo, mas é possível controlar os usuários que podem acessá-lo.
* Uma segunda solução é obtida por meio da [API do Graph do Azure AD](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento a cada usuário específico. 

Saiba mais sobre o consentimento e os usuários do Azure Active Directory: 
* [Restringir o aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

## <a name="next-steps"></a>Próximos passos

* Learn [how to use versions](luis-how-to-manage-versions.md) to control your app life cycle.
* Understand the concepts including the [authoring resource](luis-concept-keys.md#authoring-key) and [contributors](luis-concept-keys.md#contributions-from-other-authors) on that resource.
* Learn [how to create](luis-how-to-azure-subscription.md) authoring and runtime resources
* Migrate to the new [authoring resource](luis-migration-authoring.md) 
