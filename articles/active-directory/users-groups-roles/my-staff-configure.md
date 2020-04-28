---
title: Usar minha equipe para delegar o gerenciamento de usuários (visualização)-Azure AD | Microsoft Docs
description: Delegar o gerenciamento de usuários usando minha equipe e unidades administrativas
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 282946a023e4e79ee79b05cc2a317efc5a4056e4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165851"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Gerenciar seus usuários com minha equipe (versão prévia)

Minha equipe permite delegar a uma figura de autoridade, como um gerente de loja ou um líder de equipe, as permissões para garantir que os membros da equipe possam acessar suas contas do Azure AD. Em vez de depender de um helpdesk central, as organizações podem delegar tarefas comuns, como redefinição de senhas ou alteração de números de telefone para um gerente de equipe. Com a minha equipe, um usuário que não consegue acessar sua conta pode reobter o acesso em apenas alguns cliques, sem necessidade de assistência técnica ou equipe de ti.

Antes de configurar minha equipe para sua organização, recomendamos que você examine esta documentação, bem como a [documentação do usuário](../user-help/my-staff-team-manager.md) , para garantir que você compreenda a funcionalidade e o impacto desse recurso em seus usuários. Você pode aproveitar a documentação do usuário para treinar e preparar seus usuários para a nova experiência e ajudar a garantir uma distribuição bem-sucedida.

## <a name="how-my-staff-works"></a>Como minha equipe funciona

Minha equipe é baseada em unidades administrativas (AUs), que são um contêiner de recursos que podem ser usados para restringir o escopo do controle administrativo de uma atribuição de função. Na minha equipe, a AUs é usada para definir um subconjunto de usuários de uma organização, como uma loja ou um departamento. Em seguida, por exemplo, um gerente de equipe pode ser atribuído a uma função cujo escopo é um ou mais AUs. No exemplo a seguir, o usuário recebeu a função administrativa de autenticação e as três AUs são o escopo da função. Para obter mais informações sobre unidades administrativas, consulte [Gerenciamento de unidades administrativas em Azure Active Directory](directory-administrative-units.md).

## <a name="how-to-enable-my-staff"></a>Como habilitar minha equipe

Depois de configurar a AUs, você pode aplicar esse escopo aos usuários que acessam a minha equipe. Somente os usuários que recebem uma função administrativa podem acessar minha equipe. Para habilitar minha equipe, conclua as seguintes etapas:

1. Entre no portal do Azure como um administrador de usuário.
2. Navegue até **Azure Active Directory** > **configurações** > do usuário**recursos do usuário visualizações** > **gerenciar configurações de recurso do usuário**.
3. Em **administradores podem acessar minha equipe**, você pode optar por habilitar para todos os usuários, usuários selecionados ou sem acesso de usuário.

> [!Note]
> Somente os usuários que receberam uma função de administrador podem acessar minha equipe. Se você habilitar minha equipe para um usuário que não recebe uma função de administrador, ela não poderá acessar a minha equipe.

## <a name="conditional-access"></a>Acesso condicional

Você pode proteger o portal minha equipe usando a política de acesso condicional do Azure AD. Use-o para tarefas como exigir a autenticação multifator antes de acessar a minha equipe.

É altamente recomendável que você proteja minha equipe usando [as políticas de acesso condicional do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/). Para aplicar uma política de acesso condicional à minha equipe, você deve criar manualmente a entidade de serviço minha equipe usando o PowerShell.

### <a name="apply-a-----conditional-access-policy-to-my-staff"></a>Aplicar uma política de acesso condicional à minha equipe

1. Instale os [cmdlets do PowerShell do Microsoft Graph beta](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1).
1. Execute os comandos a seguir:

        Connect-Graph -Scopes "Directory.AccessAsUser.All"
        New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"

1. Crie uma política de acesso condicional que se aplica ao aplicativo de nuvem minha equipe.

    ![Criar uma política de acesso condicional para o aplicativo minha equipe](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Usando minha equipe

Quando um usuário vai para a minha equipe, ele mostra os nomes das [unidades administrativas](directory-administrative-units.md) nas quais eles têm permissões administrativas. Na [documentação do usuário da minha equipe](../user-help/my-staff-team-manager.md), usamos o termo "local" para fazer referência a unidades administrativas. Se as permissões de um administrador não tiverem um escopo de AU, as permissões serão aplicadas em toda a organização. Após a habilitação da minha equipe, os usuários que estão habilitados e foram atribuídos a uma função administrativa podem acessá-la por meio [https://mystaff.microsoft.com](https://mystaff.microsoft.com)do. Eles podem selecionar uma AU para exibir os usuários nessa AU e selecionar um usuário para abrir seu perfil.

## <a name="licenses"></a>Licenças

Cada usuário habilitado na minha equipe deve ser licenciado, mesmo que não use o portal da minha equipe. Cada usuário habilitado deve ter uma das seguintes licenças do Azure AD ou do Microsoft 365:

- O Azure AD Premium P1 ou P2
- Microsoft 365 F1 ou F3

## <a name="reset-a-users-password"></a>Redefinir a senha de um usuário

As seguintes funções têm permissão para redefinir a senha de um usuário:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiada](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Administrador de assistência técnica](directory-assign-admin-roles.md#helpdesk-administrator)
- [Administrador do usuário](directory-assign-admin-roles.md#user-administrator)
- [Administrador de senhas](directory-assign-admin-roles.md#password-administrator)

Na **minha equipe**, abra o perfil de um usuário. Selecione **Redefinir senha**.

- Se o usuário for somente na nuvem, você poderá ver uma senha temporária que pode ser dada ao usuário.
- Se o usuário for sincronizado no local Active Directory, você poderá inserir uma senha que atenda às suas políticas do AD local. Em seguida, você pode fornecer essa senha ao usuário.

    ![Indicador de progresso de redefinição de senha e notificação de êxito](media/my-staff-configure/reset-password.png)

O usuário precisa alterar sua senha na próxima vez que entrar.

## <a name="manage-a-phone-number"></a>Gerenciar um número de telefone

Na **minha equipe**, abra o perfil de um usuário.

- Selecione a seção **Adicionar número de telefone** para adicionar um número de telefone para o usuário
- Selecione **Editar número de telefone** para alterar o número de telefone
- Selecione **Remover número de telefone** para remover o número de telefone do usuário

Dependendo de suas configurações, o usuário pode usar o número de telefone que você configurou para entrar com o SMS, executar a autenticação multifator e executar a redefinição de senha de autoatendimento.

Para gerenciar o número de telefone de um usuário, você deve receber uma das seguintes funções:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiada](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Search

Você pode pesquisar a AUs e os usuários em sua organização usando a barra de pesquisa na minha equipe. Você pode pesquisar em toda a AUs e todos os usuários em sua organização, mas só pode fazer alterações aos usuários que estão em uma AU sobre a qual você recebeu permissões de administrador.

Você também pode pesquisar um usuário em uma AU. Para fazer isso, use a barra de pesquisa na parte superior da lista de usuários.

## <a name="audit-logs"></a>Logs de auditoria

Você pode exibir os logs de auditoria para ações executadas em minha equipe no portal de Azure Active Directory. Se um log de auditoria foi gerado por uma ação realizada em minha equipe, você verá isso indicado em detalhes adicionais no evento de auditoria.

## <a name="next-steps"></a>Próximas etapas

[My Staff user documentation](../user-help/my-staff-team-manager.md)
Documentação de[unidades administrativas](directory-administrative-units.md) da documentação do usuário da minha equipe
