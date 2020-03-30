---
title: Adicionar uma organização conectada no gerenciamento de direitos Ad do Azure - Azure Active Directory
description: Saiba como permitir que pessoas fora da sua organização solicitem pacotes de acesso para que você possa colaborar em projetos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128605"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Adicionar uma organização conectada no gerenciamento de direitos AD do Azure

Com o gerenciamento de direitos do Azure Active Directory (Azure AD), você pode colaborar com pessoas fora da sua organização. Se você colaborar frequentemente com usuários em um diretório ou domínio Azure AD externo, você pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que você possa permitir que usuários fora de sua organização solicitem recursos em seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização conectada?

Uma organização conectada é um diretório ou domínio Ad externo do Azure com o que você tem um relacionamento.

Por exemplo, suponha que você trabalhe no Woodgrove Bank e queira colaborar com duas organizações externas. Essas duas organizações têm configurações diferentes:

- O Graphic Design Institute usa o Azure AD, e seus usuários têm um nome principal de usuário que termina com *graphicdesigninstitute.com*.
- Contoso ainda não usa a Azure AD. Os usuários do Contoso têm um nome principal de usuário que termina com *contoso.com*.

Neste caso, você pode configurar duas organizações conectadas. Você cria uma organização conectada para o Graphic Design Institute e outra para contoso. Se você adicionar as duas organizações conectadas a uma diretiva, os usuários de cada organização com um nome principal de usuário que corresponda à diretiva podem solicitar pacotes de acesso. Os usuários com um nome principal de usuário que tenha um domínio de *graphicdesigninstitute.com* corresponderiam à organização conectada ao Graphic Design Institute e seriam autorizados a enviar solicitações. Usuários com um nome principal de usuário que tenha um domínio de *contoso.com* corresponderiam à organização conectada ao Contoso e também seriam autorizados a solicitar pacotes. E, como o Graphic Design Institute usa o Azure AD, qualquer usuário com um nome principal que corresponda a um [domínio verificado](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) que é adicionado ao seu inquilino, como *graphicdesigninstitute.example,* também seria capaz de solicitar pacotes de acesso usando a mesma diretiva.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

A forma como os usuários do diretório Azure AD ou autenticação de domínio dependem do tipo de autenticação. Os tipos de autenticação para organizações conectadas são:

- AD do Azure
- [Federação direta](../b2b/direct-federation.md)
- [Senha única](../b2b/one-time-passcode.md) (domínio)

Para uma demonstração de como adicionar uma organização conectada, assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Adicionar uma organização conectada

Para adicionar um diretório ou domínio Azure AD externo como uma organização conectada, siga as instruções nesta seção.

**Função pré-requisito :** *Administrador global,* *administrador de usuário*ou *convidado convidado*

1. No portal Azure, selecione **O Diretório Ativo do Azure**e **selecione Governança de Identidade**.

1. No painel esquerdo, selecione **Organizações conectadas**e selecione **Adicionar organização conectada**.

    ![O botão "Adicionar organização conectada"](./media/entitlement-management-organization/connected-organization.png)

1. Selecione a guia **Básico** e digite um nome de exibição e descrição para a organização.

    ![O painel básico "Adicionar organização conectada"](./media/entitlement-management-organization/organization-basics.png)

1. Selecione a guia **Diretório + domínio** e selecione Adicionar diretório + **domínio**.

    O **painel Select directories + domains** é aberto.

1. Na caixa de pesquisa, digite um nome de domínio para procurar o diretório ou domínio Azure AD. Certifique-se de inserir o nome de domínio inteiro.

1. Verifique se o nome da organização e o tipo de autenticação estão corretos. A forma como os usuários acessam depende do tipo de autenticação.

    ![O painel "Select directories + domains"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selecione **Adicionar** para adicionar o diretório ou domínio Azure AD. Atualmente, você pode adicionar apenas um diretório Azure AD ou domínio por organização conectada.

    > [!NOTE]
    > Todos os usuários do diretório ou domínio do Azure AD poderão solicitar este pacote de acesso. Isso inclui usuários no Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios sejam bloqueados pela lista de negócios do Azure AD para negócios (B2B). Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](../b2b/allow-deny-list.md).

1. Depois de adicionar o diretório ou domínio Azure AD, selecione **Selecionar**.

    A organização aparece na lista.

    ![O painel "Diretório + domínio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecione a guia **Patrocinadores** e adicione patrocinadores opcionais para essa organização conectada.

    Os patrocinadores são usuários internos ou externos já em seu diretório que são o ponto de contato para o relacionamento com essa organização conectada. Patrocinadores internos são usuários membros em seu diretório. Patrocinadores externos são usuários convidados da organização conectada que foram previamente convidados e já estão em seu diretório. Os patrocinadores podem ser utilizados como aprovadores quando os usuários desta organização conectada solicitam acesso a este pacote de acesso. Para obter informações sobre como convidar um usuário convidado para o seu diretório, consulte adicionar usuários de [colaboração B2B do Active Directory no portal Azure](../b2b/add-users-administrator.md).

    Quando você **seleciona Adicionar/Remover,** um painel é aberto no qual você pode escolher patrocinadores internos ou externos. O painel exibe uma lista não filtrada de usuários e grupos em seu diretório.

    ![O painel patrocinadores](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecione a guia **'Revisar + criar',** revisar as configurações da organização e, em seguida, selecionar **Criar**.

    ![O painel "Review + create"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Atualize uma organização conectada 

Se a organização conectada mudar para um domínio diferente, o nome da organização mudar ou você quiser alterar os patrocinadores, você pode atualizar a organização conectada seguindo as instruções nesta seção.

**Função pré-requisito :** *Administrador global,* *administrador de usuário*ou *convidado convidado*

1. No portal Azure, selecione **O Diretório Ativo do Azure**e **selecione Governança de Identidade**.

1. No painel esquerdo, selecione **Organizações Conectadas**e selecione a organização conectada para abri-la.

1. No painel de visão geral da organização conectada, selecione **Editar** para alterar o nome ou descrição da organização.  

1. No **painel diretório + domínio,** selecione **Atualizar diretório + domínio** para alterar para um diretório ou domínio diferente.

1. No painel **Patrocinadores,** selecione **Adicionar patrocinadores internos** ou **Adicionar patrocinadores externos** para adicionar um usuário como patrocinador. Para remover um patrocinador, selecione o patrocinador e, no painel direito, selecione **Excluir**.


## <a name="delete-a-connected-organization"></a>Exclua uma organização conectada

Se você não tiver mais um relacionamento com um diretório ou domínio Ad externo do Azure, você poderá excluir a organização conectada.

**Função pré-requisito :** *Administrador global,* *administrador de usuário*ou *convidado convidado*

1. No portal Azure, selecione **O Diretório Ativo do Azure**e **selecione Governança de Identidade**.

1. No painel esquerdo, selecione **Organizações Conectadas**e selecione a organização conectada para abri-la.

1. No painel de visão geral da organização conectada, selecione **Excluir** para excluí-lo.

    Atualmente, você só pode excluir uma organização conectada se não houver usuários conectados.

    ![O botão excluir organização conectada](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Próximas etapas

- [Administrar o acesso de usuários externos](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Governe o acesso para usuários que não estão em seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
