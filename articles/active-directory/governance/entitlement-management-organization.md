---
title: Adicionar uma organização conectada no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como permitir que as pessoas de fora da sua organização solicitem pacotes de acesso para que você possa colaborar em projetos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8928e7293f184e8eb366df6a29e50cbea6a7c93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078199"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Adicionar uma organização conectada no gerenciamento de direitos do Azure AD

Com o gerenciamento de direitos do Azure Active Directory (AD do Azure), você pode colaborar com pessoas de fora da sua organização. Se você colabora com frequência com usuários em um diretório ou domínio externo do Azure AD, você pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que você possa permitir que usuários fora da sua organização solicitem recursos em seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização conectada?

Uma organização conectada é um diretório ou domínio externo do Azure AD com o qual você tem uma relação.

Por exemplo, suponha que você trabalhe no Banco Woodgrove e queira colaborar com duas organizações externas. Essas duas organizações têm configurações diferentes:

- O design gráfico Institute usa o Azure AD e seus usuários têm um nome principal de usuário que termina com *graphicdesigninstitute.com*.
- A contoso ainda não usa o Azure AD. Os usuários da Contoso têm um nome principal de usuário que termina com *contoso.com*.

Nesse caso, você pode configurar duas organizações conectadas. Você cria uma organização conectada para o design gráfico Institute e outra para a contoso. Se você adicionar as duas organizações conectadas a uma política, os usuários de cada organização com um nome principal de usuário que corresponda à política poderão solicitar pacotes de acesso. Os usuários com um nome UPN que tenha um domínio de *graphicdesigninstitute.com* corresponderão à organização conectada ao design gráfico e terão permissão para enviar solicitações. Os usuários com um nome principal de usuário que tem um domínio de *contoso.com* corresponderão à organização conectada à contoso e também teriam permissão para solicitar pacotes. E, como o design gráfico Institute usa o Azure AD, qualquer usuário com um nome principal que corresponda a um [domínio verificado](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) que é adicionado ao seu locatário, como *graphicdesigninstitute. example*, também seria capaz de solicitar pacotes de acesso usando a mesma política.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

A forma como os usuários do diretório do Azure AD ou do domínio se autenticam depende do tipo de autenticação. Os tipos de autenticação para organizações conectadas são:

- Azure AD
- [Federação direta](../b2b/direct-federation.md)
- [Senha de uso único](../b2b/one-time-passcode.md) (domínio)

Para ver uma demonstração de como adicionar uma organização conectada, Assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Adicionar uma organização conectada

Para adicionar um diretório ou domínio do Azure AD externo como uma organização conectada, siga as instruções nesta seção.

**Função de pré-requisito**: *administrador global*, *administrador de usuário*ou convite de *convidado*

1. Na portal do Azure, selecione **Azure Active Directory**e, em seguida, selecione **governança de identidade**.

1. No painel esquerdo, selecione **organizações conectadas**e, em seguida, selecione **Adicionar organização conectada**.

    ![O botão "Adicionar organização conectada"](./media/entitlement-management-organization/connected-organization.png)

1. Selecione a guia **noções básicas** e insira um nome de exibição e uma descrição para a organização.

    ![O painel básico "Adicionar organização conectada"](./media/entitlement-management-organization/organization-basics.png)

1. Selecione a guia **diretório + domínio** e, em seguida, selecione **Adicionar diretório + domínio**.

    O painel **selecionar diretórios + domínios** é aberto.

1. Na caixa de pesquisa, insira um nome de domínio para pesquisar o diretório ou domínio do Azure AD. Certifique-se de inserir o nome de domínio inteiro.

1. Verifique se o nome da organização e o tipo de autenticação estão corretos. A forma como os usuários entram depende do tipo de autenticação.

    ![O painel "selecionar diretórios + domínios"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selecione **Adicionar** para adicionar o diretório ou domínio do Azure AD. No momento, você pode adicionar apenas um diretório ou domínio do Azure AD por organização conectada.

    > [!NOTE]
    > Todos os usuários do diretório ou domínio do Azure AD poderão solicitar esse pacote de acesso. Isso inclui os usuários no Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios estejam bloqueados pela lista de permitidos ou negações B2B (Business to Business) do Azure AD. Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](../b2b/allow-deny-list.md).

1. Depois de adicionar o diretório ou domínio do Azure AD, selecione **selecionar**.

    A organização aparece na lista.

    ![O painel "diretório + domínio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecione a guia **patrocinadores** e, em seguida, adicione patrocinadores opcionais para esta organização conectada.

    Os patrocinadores são usuários internos ou externos que já estão em seu diretório que são o ponto de contato para a relação com essa organização conectada. Os patrocinadores internos são usuários Membros em seu diretório. Os patrocinadores externos são usuários convidados da organização conectada que foram convidados anteriormente e já estão em seu diretório. Os patrocinadores podem ser utilizados como aprovadores quando os usuários nesta organização conectada solicitam acesso a esse pacote de acesso. Para obter informações sobre como convidar um usuário convidado para seu diretório, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../b2b/add-users-administrator.md).

    Quando você seleciona **Adicionar/remover**, um painel é aberto no qual você pode escolher patrocinadores internos ou externos. O painel exibe uma lista não filtrada de usuários e grupos em seu diretório.

    ![O painel de patrocinadores](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecione a guia **revisar + criar** , examine as configurações da organização e, em seguida, selecione **criar**.

    ![O painel "revisar + criar"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Atualizar uma organização conectada 

Se a organização conectada mudar para um domínio diferente, o nome da organização for alterado ou você quiser alterar os patrocinadores, você poderá atualizar a organização conectada seguindo as instruções nesta seção.

**Função de pré-requisito**: *administrador global*, *administrador de usuário*ou convite de *convidado*

1. Na portal do Azure, selecione **Azure Active Directory**e, em seguida, selecione **governança de identidade**.

1. No painel esquerdo, selecione **organizações conectadas**e, em seguida, selecione a organização conectada para abri-la.

1. No painel Visão geral da organização conectada, selecione **Editar** para alterar o nome ou a descrição da organização.  

1. No painel **diretório + domínio** , selecione **Atualizar diretório + domínio** para alterar para um diretório ou domínio diferente.

1. No painel de **patrocinadores** , selecione **Adicionar patrocinadores internos** ou **Adicionar patrocinadores externos** para adicionar um usuário como um patrocinador. Para remover um patrocinador, selecione o patrocinador e, no painel direito, selecione **excluir**.


## <a name="delete-a-connected-organization"></a>Excluir uma organização conectada

Se você não tiver mais uma relação com um diretório ou domínio do Azure AD externo, poderá excluir a organização conectada.

**Função de pré-requisito**: *administrador global*, *administrador de usuário*ou convite de *convidado*

1. Na portal do Azure, selecione **Azure Active Directory**e, em seguida, selecione **governança de identidade**.

1. No painel esquerdo, selecione **organizações conectadas**e, em seguida, selecione a organização conectada para abri-la.

1. No painel Visão geral da organização conectada, selecione **excluir** para excluí-la.

    No momento, você pode excluir uma organização conectada somente se não houver nenhum usuário conectado.

    ![O botão excluir da organização conectada](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Próximas etapas

- [Administrar o acesso de usuários externos](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Controlar o acesso para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
