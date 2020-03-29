---
title: Colaborar com os demais - LUIS
titleSuffix: Azure Cognitive Services
description: Um proprietário de aplicativo pode adicionar contribuintes ao recurso de autoria. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053453"
---
# <a name="add-contributors-to-your-app"></a>Adicione colaboradores ao seu aplicativo

Um proprietário de aplicativo pode adicionar contribuintes aos aplicativos. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Adicionar contribuinte ao recurso de autoria do Azure

O procedimento a seguir é para todos os usuários que **migraram** para usar o recurso de autoria do Azure.

Você migrou se sua experiência de autoria do LUIS estiver vinculada a um recurso de Autoria na página **gerenciar -> recursos do Azure** no portal LUIS.

1. No portal Azure, encontre o recurso de autoria do Language Understanding (LUIS). Tem o `LUIS.Authoring`tipo.
1. Na página iam **(Access Control, controle** de acesso) deste recurso, selecione **+Adicione** e selecione **Adicionar atribuição de função**.

    ![No portal Azure, adicione atribuição de função no recurso de autoria.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Na janela **Adicionar função,** selecione a **função** De contribuinte. Na opção **Atribuir acesso à** opção, selecione **usuário, grupo ou principal de serviço do Azure AD**. Na opção **Selecionar,** digite o endereço de e-mail do usuário. Se o usuário for conhecido por mais de 1 endereço de e-mail para o mesmo domínio, certifique-se de inserir a conta de e-mail _principal._

    ![Adicione o e-mail do usuário à função contribuinte do Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando o e-mail do usuário for encontrado, selecione a conta e selecione **Salvar**. 

    Se você tiver problemas com essa atribuição de função, [revise as atribuições de função do Azure](../../role-based-access-control/role-assignments-portal.md) e a solução de [problemas de controle de acesso do Azure](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Adicionar colaborador ao aplicativo LUIS

O procedimento a seguir é para todos os usuários que **não migraram** para usar o recurso de autoria do Azure.

Você não migrou se sua experiência de autoria do LUIS não estiver vinculada a um recurso de Autoria na página **gerenciar -> recursos do Azure** no portal LUIS.

Um aplicativo tem um único autor, mas pode ter vários colaboradores. Para permitir que os colaboradores editem o aplicativo LUIS, você deve adicionar o email que eles usam para acessar o portal do LUIS à lista de colaboradores. Depois que eles são adicionados, o aplicativo aparece no portal do LUIS deles.

1. Selecione **Gerenciar** no menu superior direito e selecione **Colaboradores** no menu esquerdo.

1. Selecione **Adicionar Colaborador** na barra de ferramentas.

1. Insira o endereço de email que o colaborador usa para entrar no portal do LUIS.

    ![Adicionar o endereço de email do colaborado](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Usuários com vários e-mails 

Se você adicionar colaboradores/colaboradores a um aplicativo LUIS, você está especificando o endereço de e-mail exato. Embora o Azure Active Directory (Azure AD) permita que um único usuário tenha mais de uma conta de e-mail usada de forma intercambiável, o LUIS exige que o usuário faça login com o endereço de e-mail especificado ao adicionar o colaborador/colaborador.

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
* Uma segunda solução, é usando a [API de gerenciamento de identidade e acesso do Azure AD no Microsoft Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento a cada usuário específico. 

Saiba mais sobre o consentimento e os usuários do Azure Active Directory: 
* [Restringir seu aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

## <a name="next-steps"></a>Próximas etapas

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de autoria](luis-concept-keys.md#authoring-key) e os [contribuintes](luis-concept-keys.md#contributions-from-other-authors) sobre esse recurso.
* [Saiba como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md) 
