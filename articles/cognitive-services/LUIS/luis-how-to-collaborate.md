---
title: Colaborar com outras pessoas – LUIS
titleSuffix: Azure Cognitive Services
description: Um proprietário de aplicativo pode adicionar colaboradores ao recurso de criação. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/19/2019
ms.openlocfilehash: 30b4256b37dc3329801a730192e25f7c24a45594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540891"
---
# <a name="add-contributors-to-your-app"></a>Adicionar colaboradores ao seu aplicativo

Um proprietário de aplicativo pode adicionar colaboradores a aplicativos. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo.

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Adicionar colaborador ao recurso de criação do Azure

O procedimento a seguir é para todos os usuários que **migraram** para usar o recurso de criação do Azure.

Você migrou se sua experiência de criação do LUIS está vinculada a um recurso de criação na página **gerenciar recursos do Azure->** no portal do Luis.

1. Na portal do Azure, localize o recurso de criação de Reconhecimento vocal (LUIS). Ele tem o tipo `LUIS.Authoring` .
1. Na página controle de **acesso deste recurso (iam)** , selecione **+ Adicionar** e selecione **Adicionar atribuição de função**.

    ![Em portal do Azure, adicione atribuição de função ao recurso de criação.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Na janela **Adicionar atribuição de função** , selecione a **função** de colaborador. Na opção **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço do Azure ad**. Na opção **selecionar** , insira o endereço de email do usuário. Se o usuário for conhecido por mais de um endereço de email para o mesmo domínio, certifique-se de inserir a conta de email _principal_ .

    ![Adicionar email do usuário à função colaborador do Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando o email do usuário for encontrado, selecione a conta e selecione **salvar**.

    Se você tiver problemas com essa atribuição de função, examine as [atribuições de função do Azure](../../role-based-access-control/role-assignments-portal.md) e [solução de problemas do controle de acesso do Azure](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Adicionar colaborador ao aplicativo LUIS

O procedimento a seguir é para todos os usuários que **não migraram** para usar o recurso de criação do Azure.

Você não migrou se sua experiência de criação do LUIS não estiver vinculada a um recurso de criação na página **gerenciar recursos do Azure->** no portal do Luis.

Um aplicativo tem um único autor, mas pode ter vários colaboradores. Para permitir que os colaboradores editem o aplicativo LUIS, você deve adicionar o email que eles usam para acessar o portal do LUIS à lista de colaboradores. Depois que eles são adicionados, o aplicativo aparece no portal do LUIS deles.

1. Selecione **Gerenciar** no menu superior direito e selecione **Colaboradores** no menu esquerdo.

1. Selecione **Adicionar Colaborador** na barra de ferramentas.

1. Insira o endereço de email que o colaborador usa para entrar no portal do LUIS.

    ![Adicionar o endereço de email do colaborado](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Usuários com vários emails

Se você adicionar colaboradores/colaboradores a um aplicativo LUIS, você estará especificando o endereço de email exato. Embora Azure Active Directory (Azure AD) permita que um único usuário tenha mais de uma conta de email usada de forma intercambiável, o LUIS exige que o usuário entre com o endereço de email especificado ao adicionar o colaborador/colaborador.

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
* Uma segunda solução é usar a API de [Gerenciamento de acesso e identidade do Azure AD no Microsoft Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento para cada usuário específico.

Saiba mais sobre o consentimento e os usuários do Azure Active Directory:
* [Restringir seu aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

## <a name="next-steps"></a>Próximas etapas

* Saiba [como usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de criação](luis-how-to-azure-subscription.md#authoring-key) e os [colaboradores](luis-how-to-azure-subscription.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de criação](luis-migration-authoring.md)
