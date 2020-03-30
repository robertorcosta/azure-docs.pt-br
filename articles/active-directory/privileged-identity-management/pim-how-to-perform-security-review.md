---
title: Revisar o acesso aos papéis do Azure AD no PIM - Azure AD | Microsoft Docs
description: Saiba como revisar o acesso às funções do Azure Active Directory no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847102"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Revisar o acesso às funções do Azure AD no Gerenciamento de Identidade Privilegiada

O PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) simplifica a forma como as empresas gerenciam acesso privilegiado aos recursos no Azure Active Directory (AD) e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune. Siga as etapas deste artigo para auto-rever com sucesso suas funções atribuídas.

Se você for atribuído a uma função administrativa, o administrador de função com privilégios de sua organização poderá solicitar que você confirme regularmente que ainda precisa da função para seu trabalho. Você pode receber um e-mail que inclui um link, ou você pode ir direto para o [portal Azure](https://portal.azure.com) e começar.

Se você for um administrador com privilégios de função ou um administrador global interessado em revisões de acesso, obtenha mais detalhes em [Como iniciar uma revisão de acesso](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Adicione um painel pim

Se você não tiver o serviço de Gerenciamento de Identidade Privilegiada AD do Azure ad fixado no seu painel no portal Azure, siga estas etapas para começar.

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório em que você vai operar.
3. Selecione **Todos os serviços** e use a caixa de texto Filtrar para pesquisar o **Azure AD Privileged Identity Management**.
4. Marque **Fixar no painel** e então clique em **Criar**. O aplicativo Privileged Identity Management será aberto.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso

Ao aprovar ou negar o acesso, você está apenas dizendo ao revisor se ainda usa essa função ou não. Escolha **Aprovar** se você quiser manter a função ou **Negar** se não precisar mais do acesso. Seu status não mudará imediatamente até que o revisor aplique os resultados.
Siga estas etapas para localizar e concluir a análise de acesso:

1. No serviço de Gerenciamento de Identidade Privilegiada, selecione **Revisar acesso privilegiado**. Se você tiver quaisquer avaliações de acesso pendentes, elas aparecerão na página de comentários do Azure AD **Access.**
2. Selecione a análise que deseja concluir.
3. A menos que tenha criado a análise, você aparece como o único usuário na análise. Selecione a marca de seleção ao lado de seu nome.
4. Escolha **Aprovar** ou **Negar**. Talvez seja necessário incluir um motivo para a sua decisão na caixa de texto **Fornecer um motivo** .  
5. Feche a folha **Funções de análise do AD do Azure** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

- [Realizar uma revisão de acesso das minhas funções de recurso do Azure no PIM](pim-resource-roles-perform-access-review.md)
