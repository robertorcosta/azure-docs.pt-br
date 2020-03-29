---
title: Revisar o acesso de um pacote de acesso no gerenciamento de direitos ad do Azure
description: Saiba como concluir uma revisão de acesso a pacotes de acesso ao gerenciamento de direitos nas avaliações de acesso do Azure Active Directory (Preview).
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
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968739"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Revisar o acesso de um pacote de acesso no gerenciamento de direitos ad do Azure

O gerenciamento de direitos do Azure AD simplifica a forma como as empresas gerenciam o acesso a grupos, aplicativos e sites do SharePoint. Este artigo descreve como executar avaliações de acesso para outros usuários que são atribuídos a um pacote de acesso como um revisor designado.

## <a name="prerequisites"></a>Pré-requisitos

Para revisar as atribuições do pacote de acesso ativo dos usuários, você deve atender aos pré-requisitos para fazer uma revisão de acesso:
- Azure AD Premium P2
- Administrador global
- Administrador de usuário designado, proprietário de catálogo ou gerenciador de pacotes de acesso

Para obter mais informações, veja [Requisitos de licença](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Abra a revisão de acesso

Use as seguintes etapas para encontrar e abrir a revisão de acesso:

1. Você pode receber um e-mail da Microsoft que pede que você revise o acesso. Localize o e-mail para abrir a revisão de acesso. Aqui está um e-mail de exemplo para revisar o acesso:
    
    ![E-mail do revisor de revisão de acesso](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Clique no link **de acesso ao usuário de Revisão** para abrir a revisão de acesso. 

1. Se você não tiver o e-mail, você pode encontrar suas https://myaccess.microsoft.comavaliações de acesso pendentes navegando diretamente para .  (Para o governo `https://myaccess.microsoft.us` dos EUA, use em vez disso.)

1. Clique em **Acessar avaliações** na barra de navegação à esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a você.
    
    ![Selecione avaliações de acesso no Meu Acesso](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Clique na opinião que você gostaria de começar.
    
    ![Selecione a revisão de acesso](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Realize a revisão de acesso

Depois de abrir a revisão de acesso, você verá os nomes dos usuários para os quais você precisa revisar. Há duas maneiras que você pode aprovar ou negar o acesso:
- Você pode aprovar ou negar manualmente o acesso a um ou mais usuários
- Você pode aceitar as recomendações do sistema

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar acesso manualmente para um ou mais usuários
1. Revise a lista de usuários e determine quais usuários precisam continuar a ter acesso.

    ![Lista de usuários a serem analisados](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Para aprovar ou negar acesso, selecione o botão de rádio à esquerda do nome do usuário.

1. Selecione **Aprovar** ou **Negar** na barra acima dos nomes de usuário.

    ![Selecione o usuário](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Se você não tiver certeza, você pode clicar no botão **Não sei.**

    Se você fizer essa seleção, o usuário mantém o acesso, e essa seleção vai para os registros de auditoria. O registro mostra a todos os outros revisores que você ainda completou a revisão.

1. Você pode ser obrigado a fornecer uma razão para sua decisão. Digite um motivo e clique **em Enviar**.

    ![Aprovar ou negar acesso](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Você pode mudar sua decisão a qualquer momento antes do final da revisão. Para isso, selecione o usuário da lista e altere a decisão. Por exemplo, você pode aprovar o acesso para um usuário que você negou anteriormente.

Se houver vários revisores, a última resposta enviada será gravada. Considere um exemplo onde um administrador designa dois revisores – Alice e Bob. Alice abre a revisão primeiro e aprova o acesso. Antes da revisão terminar, Bob abre a revisão e nega acesso. Neste caso, a última decisão de acesso negado é registrada.

>[!NOTE]
>Se um usuário tiver acesso negado, ele não será removido do pacote de acesso imediatamente. O usuário será removido do pacote de acesso quando a revisão terminar ou um administrador terminar á revisão.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Aprovar ou negar acesso usando as recomendações geradas pelo sistema

Para rever o acesso de vários usuários mais rapidamente, você pode usar as recomendações geradas pelo sistema, aceitando as recomendações com um único clique. As recomendações são geradas com base na atividade de login do usuário.

1.  Na barra no topo da página, clique em **Aceitar recomendações**.
    
    ![Selecione Aceitar recomendações](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Você verá um resumo das ações recomendadas.

1.  Clique **em Enviar** para aceitar as recomendações.

## <a name="next-steps"></a>Próximas etapas

- [Auto-revisão de pacotes de acesso](entitlement-management-access-reviews-self-review.md)