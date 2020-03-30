---
title: Alterar funções de recurso para um pacote de acesso no gerenciamento de direitos Azure AD - Azure Active Directory
description: Saiba como alterar as funções de recurso para um pacote de acesso existente no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261886"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar funções de recursos para um pacote de acesso no gerenciamento de direitos AD do Azure

Como gerenciador de pacotes de acesso, você pode alterar os recursos em um pacote de acesso a qualquer momento sem se preocupar em provisionar o acesso do usuário aos novos recursos ou remover seu acesso dos recursos anteriores. Este artigo descreve como alterar as funções de recurso para um pacote de acesso existente.

Este vídeo fornece uma visão geral de como alterar um pacote de acesso.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Verifique o catálogo de recursos

Se você precisar adicionar recursos a um pacote de acesso, você deve verificar se os recursos que sua necessidade estão disponíveis no catálogo. Se você é um gerenciador de pacotes de acesso, não poderá adicionar recursos a um catálogo, mesmo que os possua. Você está restrito a usar os recursos disponíveis no catálogo.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique **em Catalog** e, em seguida, abra o catálogo.

1. No menu à esquerda, clique em **Recursos** para ver a lista de recursos neste catálogo.

    ![Lista de recursos em um catálogo](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Se você é um gerenciador de pacotes de acesso e precisa adicionar recursos ao catálogo, você pode pedir ao proprietário do catálogo para adicioná-los.

## <a name="add-resource-roles"></a>Adicionar funções de recursos

Uma função de recurso é uma coleção de permissões associadas a um recurso. A maneira como você disponibiliza recursos para os usuários solicitarem é adicionando funções de recursos ao seu pacote de acesso. Você pode adicionar funções de recursos para grupos, equipes, aplicativos e sites do SharePoint.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique **em Funções de recurso**.

1. Clique **em Adicionar funções de recurso** para abrir as funções de recurso adicionar para acessar a página do pacote.

    ![Pacote de acesso - Adicionar funções de recursos](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Dependendo se você deseja adicionar um grupo, equipe, aplicativo ou site SharePoint, execute as etapas em uma das seguintes seções de função de recurso.

## <a name="add-a-group-or-team-resource-role"></a>Adicione uma função de recurso de grupo ou equipe

Você pode ter o gerenciamento de direitos automaticamente adicionar usuários a um grupo ou a uma equipe no Microsoft Teams quando eles são atribuídos a um pacote de acesso. 

- Quando um grupo ou equipe faz parte de um pacote de acesso e um usuário é atribuído a esse pacote de acesso, o usuário é adicionado a esse grupo ou equipe, se ainda não estiver presente.
- Quando a atribuição do pacote de acesso de um usuário expira, ela é removida do grupo ou da equipe, a menos que tenha atualmente uma atribuição para outro pacote de acesso que inclua esse mesmo grupo ou equipe.

Você pode selecionar qualquer [grupo de segurança Azure AD ou Office 365 Group](../fundamentals/active-directory-groups-create-azure-portal.md). Os administradores podem adicionar qualquer grupo a um catálogo; os proprietários de catálogos podem adicionar qualquer grupo ao catálogo se eles são proprietários do grupo. Tenha em mente as seguintes restrições do Azure AD ao selecionar um grupo:

- Quando um usuário, incluindo um convidado, é adicionado como membro a um grupo ou equipe, ele pode ver todos os outros membros desse grupo ou equipe.
- O Azure AD não pode alterar a adesão de um grupo sincronizado do Windows Server Active Directory usando o Azure AD Connect, ou que foi criado no Exchange Online como um grupo de distribuição.  
- A adesão de grupos dinâmicos não pode ser atualizada adicionando ou removendo um membro, portanto, as associações de grupos dinâmicos não são adequadas para uso com a gestão de direitos.

Para obter mais informações, consulte [Compare grupos](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) e Grupos do [Office 365 e Equipes Microsoft](https://docs.microsoft.com/microsoftteams/office-365-groups).

1. Na **opção Adicionar funções de recurso para acessar a** página do pacote, clique em Grupos e **Equipes** para abrir o painel Selecionar grupos.

1. Selecione os grupos e equipes que deseja incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecionar grupos](./media/entitlement-management-access-package-resources/group-select.png)

1. Clique em **Selecionar**.

    Depois de selecionar o grupo ou a equipe, a coluna **Sub tipo** listará um dos seguintes subtipos:

    |  |  |
    | --- | --- |
    | Segurança | Usado para conceder acesso a recursos. |
    | Distribuição | Usado para enviar notificações para um grupo de pessoas. |
    | O365 | Grupo Office 365 que não está habilitado para equipes. Usado para colaboração entre usuários, dentro e fora da sua empresa. |
    | Equipe | Grupo Office 365 que é habilitado para equipes. Usado para colaboração entre usuários, dentro e fora da sua empresa. |

1. Na lista **Função,** **selecione Proprietário** ou **Membro**.

    Você normalmente seleciona a função Membro. Se você selecionar a função Proprietário, isso permitirá que os usuários adicionem ou removam outros membros ou proprietários.

    ![Pacote de acesso - Adicionar função de recurso para um grupo ou equipe](./media/entitlement-management-access-package-resources/group-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com atribuições existentes no pacote de acesso se tornarão automaticamente membros deste grupo ou equipe quando ele for adicionado.

## <a name="add-an-application-resource-role"></a>Adicione uma função de recurso de aplicativo

Você pode ter o Azure AD automaticamente atribuindo aos usuários acesso a um aplicativo corporativo Azure AD, incluindo aplicativos SaaS e aplicativos de sua organização federados ao Azure AD, quando um usuário é atribuído a um pacote de acesso. Para aplicativos que se integram ao Azure AD por meio de um único signon,o Azure AD emitirá tokens de federação para usuários atribuídos ao aplicativo.

Os aplicativos podem ter várias funções. Ao adicionar um aplicativo a um pacote de acesso, se esse aplicativo tiver mais de uma função, você precisará especificar a função apropriada para esses usuários. Se você estiver desenvolvendo aplicativos, você pode ler mais sobre como essas funções são adicionadas aos seus aplicativos em [Como: Configurar a reclamação de função emitida no token SAML para aplicativos corporativos](../develop/active-directory-enterprise-app-role-management.md).

Uma vez que uma função de aplicativo faz parte de um pacote de acesso:

- Quando um usuário é atribuído a esse pacote de acesso, o usuário é adicionado a essa função de aplicativo, se ainda não estiver presente.
- Quando a atribuição do pacote de acesso de um usuário expirar, seu acesso será removido do aplicativo, a menos que eles tenham uma atribuição para outro pacote de acesso que inclua essa função de aplicativo.

Aqui estão algumas considerações ao selecionar um aplicativo:

- Os aplicativos também podem ter grupos atribuídos às suas funções.  Você pode optar por adicionar um grupo no lugar de uma função de aplicativo em um pacote de acesso, no entanto, o aplicativo não será visível para o usuário como parte do pacote de acesso no portal Meu Acesso.

1. Na **opção Adicionar funções de recurso para acessar a** página do pacote, clique em **Aplicativos** para abrir o painel Selecionar aplicativos.

1. Selecione os aplicativos que deseja incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecionar aplicativos](./media/entitlement-management-access-package-resources/application-select.png)

1. Clique em **Selecionar**.

1. Na lista **Função,** selecione uma função de aplicativo.

    ![Pacote de acesso - Adicionar função de recurso para um aplicativo](./media/entitlement-management-access-package-resources/application-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com atribuições existentes no pacote de acesso terão acesso automaticamente a este aplicativo quando ele for adicionado.

## <a name="add-a-sharepoint-site-resource-role"></a>Adicione uma função de recurso do site do SharePoint

O Azure AD pode atribuir automaticamente aos usuários acesso a um site do SharePoint Online ou à coleção do site SharePoint Online quando eles recebem um pacote de acesso.

1. Na **opção Adicionar funções de recursos para acessar a** página do pacote, clique nos sites do **SharePoint** para abrir o painel Select SharePoint Online sites.

1. Selecione os sites do SharePoint Online que você deseja incluir no pacote de acesso.

    ![Pacote de acesso - Adicionar funções de recursos - Selecione sites DosharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Clique em **Selecionar**.

1. Na lista **De R$** 1,99, selecione uma função de site do SharePoint Online.

    ![Pacote de acesso - Adicionar função de recurso para um site do SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Clique em **Adicionar**.

    Todos os usuários com atribuições existentes no pacote de acesso terão acesso automaticamente a este site do SharePoint Online quando ele for adicionado.

## <a name="remove-resource-roles"></a>Remover funções de recursos

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique **em Funções de recurso**.

1. Na lista de funções de recursos, encontre a função de recurso que deseja remover.

1. Clique na elipse (**...**) e clique em **Remover função de recurso**.

    Todos os usuários com atribuições existentes no pacote de acesso terão automaticamente seu acesso revogado para esta função de recurso quando ele for removido.

## <a name="when-changes-are-applied"></a>Quando as alterações são aplicadas

No gerenciamento de direitos, o Azure AD processará alterações em massa para atribuição e recursos em seus pacotes de acesso várias vezes ao dia. Então, se você fizer uma atribuição, ou alterar as funções de recurso do seu pacote de acesso, pode levar até 24 horas para que essa alteração seja feita no Azure AD, além do tempo necessário para propagar essas alterações em outros Serviços Online da Microsoft ou SaaS conectado Aplicativos. Se a alteração afetar apenas alguns objetos, a alteração provavelmente levará apenas alguns minutos para ser aplicada no Azure AD, após o qual outros componentes do Azure AD detectarão essa alteração e atualizarão os aplicativos SaaS. Se sua mudança afetar milhares de objetos, a mudança levará mais tempo. Por exemplo, se você tiver um pacote de acesso com 2 aplicativos e 100 atribuições de usuário, e decidir adicionar uma função de site do SharePoint ao pacote de acesso, pode haver um atraso até que todos os usuários faça parte dessa função do site do SharePoint. Você pode monitorar o progresso através do registro de auditoria do Azure AD, do registro de provisionamento do Azure AD e dos logs de auditoria do site do SharePoint.

Quando você remove um membro de uma equipe, ele também é removido do grupo do Office 365. A remoção da funcionalidade de chat da equipe pode ser atrasada. Para obter mais informações, consulte [Membros do Grupo](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Próximas etapas

- [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Como configurar a declaração de função emitida no token SAML para aplicativos empresariais](../develop/active-directory-enterprise-app-role-management.md)
- [Introdução ao SharePoint Online](/sharepoint/introduction)