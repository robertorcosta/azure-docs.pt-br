---
title: Auto-revisão de um pacote de acesso no gerenciamento de direitos ad do Azure
description: Saiba como revisar o acesso do usuário a pacotes de acesso ao gerenciamento de direitos nas avaliações de acesso do Azure Active Directory (Preview).
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
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967768"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Auto-revisão de um pacote de acesso no gerenciamento de direitos ad do Azure

O gerenciamento de direitos do Azure AD simplifica a forma como as empresas gerenciam o acesso a grupos, aplicativos e sites do SharePoint. Este artigo descreve como um usuário faz uma auto-revisão de seus pacotes de acesso atribuídos.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

Para fazer uma revisão de acesso, você deve primeiro abrir a revisão de acesso. Use o seguinte procedimento para encontrar e abrir a revisão de acesso:

1. Você pode receber um e-mail da Microsoft que pede que você revise o acesso. Localize o e-mail para abrir a revisão de acesso. Aqui está um exemplo de um e-mail solicitando uma revisão do acesso: 
    
    ![Acesse o e-mail de auto-revisor de revisão](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Clique no link **de acesso à Revisão.**

1. Você também pode https://myaccess.microsoft.com ir diretamente para encontrar suas avaliações de acesso pendentes se você não receber um e-mail.  (Para o governo `https://myaccess.microsoft.us` dos EUA, use em vez disso.)

1. Clique em **Acessar avaliações** na barra de navegação à esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a você.


1.  Clique na opinião que você gostaria de começar.

## <a name="perform-the-access-review"></a>Realize a revisão de acesso

Depois de abrir a revisão de acesso, você pode ver seu acesso. Use o seguinte procedimento para fazer a revisão de acesso:

1.  Decida se você ainda precisa acessar o pacote de acesso. Por exemplo, o projeto em que você está trabalhando não está completo, então você ainda precisa de acesso para continuar trabalhando no projeto.

1.  Clique **em Sim** para manter seu acesso ou clique em **Não** para remover seu acesso.
    >[!NOTE]
    >Se você afirmou que não precisa mais de acesso, você não será removido do pacote de acesso imediatamente. Você será removido do pacote de acesso quando a revisão terminar ou se um administrador parar a revisão.

1.  Se você clicou **em Sim,** talvez seja necessário incluir uma declaração de justificativa na caixa **Razão.**

1.  Clique em **Enviar**.

Você pode voltar à revisão se mudar de ideia e decidir alterar sua resposta antes do final da revisão.

## <a name="next-steps"></a>Próximas etapas

- [Revisar o acesso aos pacotes de acesso](entitlement-management-access-reviews-review-access.md) 
