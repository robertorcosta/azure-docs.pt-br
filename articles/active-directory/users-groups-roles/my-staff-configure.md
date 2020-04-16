---
title: Use minha equipe para delegar o gerenciamento de usuários (preview) - Azure AD | Microsoft Docs
description: Delegue a gestão de usuários usando Minha Equipe e unidades administrativas
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394209"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Gerencie seus usuários com minha equipe (visualização)

Minha equipe permite que você delegue a uma figura de autoridade, como um gerente de loja ou um líder de equipe, as permissões para garantir que seus membros da equipe tenham acesso às suas contas Azure AD. Em vez de contar com um helpdesk central, as organizações podem delegar tarefas comuns, como redefinir senhas ou alterar números de telefone para um gerente de equipe. Com o My Staff, um usuário que não pode acessar sua conta pode recuperar o acesso em apenas alguns cliques, sem necessidade de helpdesk ou equipe de TI.

Antes de configurar o My Staff para sua organização, recomendamos que você revise esta documentação, bem como a [documentação](../user-help/my-staff-team-manager.md) do usuário para garantir que você entenda a funcionalidade e o impacto desse recurso em seus usuários. Você pode aproveitar a documentação do usuário para treinar e preparar seus usuários para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

## <a name="how-my-staff-works"></a>Como funciona minha equipe

Minha Equipe é baseada em unidades administrativas (UA), que são um contêiner de recursos que podem ser usados para restringir o escopo do controle administrativo de uma atribuição de função. No My Staff, as UA são usadas para definir um subconjunto de usuários de uma organização, como uma loja ou departamento. Então, por exemplo, um gerente de equipe pode ser designado para uma função cujo escopo é uma ou mais UA. No exemplo abaixo, foi concedida ao usuário a função Administrativa de Autenticação, e as três UA são o escopo da função. Para obter mais informações sobre unidades administrativas, consulte [Gerenciamento de unidades administrativas no Diretório Ativo do Azure](directory-administrative-units.md).

## <a name="how-to-enable-my-staff"></a>Como ativar minha equipe

Depois de configurar as UA, você pode aplicar esse escopo aos seus usuários que acessam a Minha Equipe. Somente usuários que recebem uma função administrativa podem acessar minha equipe. Para habilitar minha equipe, complete as seguintes etapas:

1. Entre no portal do Azure como administrador do Usuário.
2. Navegue até as configurações >  **do usuário do azure Active Directory** > **Visualizações** > do recurso do usuário**Gerenciar configurações de visualização de recursos**do usuário .**User settings**
3. Em **Administradores pode acessar Minha Equipe,** você pode optar por habilitar para todos os usuários, usuários selecionados ou nenhum acesso ao usuário.

> [!Note]
> Somente usuários que tenham sido designados para uma função de admin podem acessar Minha Equipe. Se você habilitar minha equipe para um usuário que não tenha uma função de admin, ele não poderá acessar minha equipe.

## <a name="using-my-staff"></a>Usando minha equipe

Quando um usuário vai ao Meu Funcionário, são mostrados os nomes das [unidades administrativas](directory-administrative-units.md) sobre as quais eles têm permissões administrativas. Na [documentação do usuário My Staff,](../user-help/my-staff-team-manager.md)usamos o termo "localização" para consultar unidades administrativas. Se as permissões de um administrador não tiverem um escopo de UA, as permissões serão aplicadas em toda a organização. Depois que minha equipe for habilitada, os usuários habilitados e designados [https://mystaff.microsoft.com](https://mystaff.microsoft.com)para uma função administrativa podem acessá-lo através de . Eles podem selecionar um UA para visualizar os usuários nesse AU e selecionar um usuário para abrir seu perfil.

## <a name="licenses"></a>Licenças

Cada usuário habilitado no Meu Staff deve ser licenciado, mesmo que não use o portal Minha Equipe. Cada usuário habilitado deve ter uma das seguintes licenças Azure AD ou Microsoft 365:

- O Azure AD Premium P1 ou P2
- Microsoft 365 F1 ou F3

## <a name="reset-a-users-password"></a>Redefinir a senha de um usuário

As seguintes funções têm permissão para redefinir a senha de um usuário:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiado](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Administrador de assistência técnica](directory-assign-admin-roles.md#helpdesk-administrator)
- [Administrador de usuários](directory-assign-admin-roles.md#user-administrator)
- [Administrador de senhas](directory-assign-admin-roles.md#password-administrator)

A partir de **Minha Equipe**, abra o perfil de um usuário. Selecione **Redefinir senha**.

- Se o usuário for somente na nuvem, você poderá ver uma senha temporária que você pode dar ao usuário.
- Se o usuário estiver sincronizado a partir do Active Directory no local, você poderá inserir uma senha que atenda às políticas de AD no local. Em seguida, você pode dar essa senha ao usuário.

    ![Indicador de progresso de redefinição de senha e notificação de sucesso](media/my-staff-configure/reset-password.png)

O usuário é obrigado a alterar sua senha na próxima vez que fizer login.

## <a name="manage-a-phone-number"></a>Gerenciar um número de telefone

A partir de **Minha Equipe**, abra o perfil de um usuário.

- Selecione Adicionar seção **de número de telefone** para adicionar um número de telefone para o usuário
- Selecione **Editar número de telefone** para alterar o número de telefone
- Selecione **Remover número de telefone** para remover o número de telefone do usuário

Dependendo das configurações, o usuário pode usar o número de telefone configurado para fazer login com SMS, executar autenticação multifatorial e executar a redefinição de senha de autoatendimento.

Para gerenciar o número de telefone de um usuário, você deve ser designado para uma das seguintes funções:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiado](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Search

Você pode procurar por UA e usuários em sua organização usando a barra de pesquisa no Meu Funcionário. Você pode pesquisar em todas as UAs e usuários da sua organização, mas você só pode fazer alterações em usuários que estão em um UA sobre o qual você recebeu permissões de admin.

Você também pode procurar um usuário dentro de uma UA. Para isso, use a barra de pesquisa no topo da lista de usuários.

## <a name="audit-logs"></a>Logs de auditoria

Você pode visualizar registros de auditoria para ações tomadas no Meu Staff no portal do Diretório Ativo do Azure. Se um registro de auditoria foi gerado por uma ação tomada no My Staff, você verá isso indicado em DETALHES ADICIONAIS no evento de auditoria.

## <a name="next-steps"></a>Próximas etapas

[Documentação das](../user-help/my-staff-team-manager.md)
[unidades administrativas](directory-administrative-units.md) do meu pessoal
