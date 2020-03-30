---
title: Concluir a revisão de acesso dos papéis do Azure AD no PIM - Azure AD | Microsoft Docs
description: Saiba como concluir uma revisão de acesso das funções do Azure AD no Azure AD Privileged Identity Management (PIM) e veja os resultados
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022277"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Complete uma revisão de acesso das funções do Azure AD no Gerenciamento de Identidade Privilegiada

Os administradores de função com privilégios podem examinar o acesso privilegiado quando uma [revisão de acesso tiver sido iniciada](pim-how-to-start-security-review.md).  O PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) enviará automaticamente um e-mail aos usuários da sua organização Azure Active Directory (Azure AD), solicitando que eles revisem seu acesso. Se um usuário não tiver recebido um email, você poderá enviar para ele as instruções sobre [como executar uma revisão de acesso](pim-how-to-perform-security-review.md).

Depois do fim do período da revisão de acesso, ou quando todos os usuários tiverem terminado a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso

1. Vá para o [portal Azure](https://portal.azure.com/) e selecione o serviço **de Gerenciamento de Identidade Privilegiada AZure AD** em seu painel.
1. Clique na seção **Revisões de acesso** do painel.
1. Selecione a análise de acesso que você deseja gerenciar.

Na lâmina de detalhes da revisão de acesso, há uma série de opções para gerenciar essa revisão.

![Botões de revisão de acesso ao Gerenciamento de Identidade Privilegiados - captura de tela](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Lembrar

Se uma análise de acesso é configurada para que os usuários examinem a si mesmos, o botão **Lembrar** envia uma notificação.

### <a name="stop"></a>Stop

Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **Parar** para concluí-las mais cedo. Se quaisquer usuários ainda não tiverem sido examinados até este momento, eles não poderão ser após você parar a análise. Não é possível reiniciar uma análise após ela ter sido interrompida.

### <a name="apply"></a>Aplicar

Após uma análise de acesso ser concluída, seja porque você atingiu a data de término ou a interrompeu manualmente, o botão **Aplicar** implementa o resultado da análise. Se o acesso de um usuário foi negado na análise, esta é a etapa que removerá sua atribuição de função.  

### <a name="export"></a>Exportação

Se você quiser aplicar os resultados da revisão de acesso manualmente, poderá exportar a revisão. O botão **Exportar** começará a baixar um arquivo CSV. Você pode gerenciar os resultados no Excel ou em outros programas que abrem arquivos CSV.

### <a name="delete"></a>Excluir

Se você não estiver mais interessado na revisão, exclua-a. O botão **Excluir** remove a revisão do serviço de Gerenciamento de Identidade Privilegiada.

> [!IMPORTANT]
> Você não será obrigado a confirmar essa mudança destrutiva, então verifique se deseja excluir essa revisão.

## <a name="next-steps"></a>Próximas etapas

- [Inicie uma revisão de acesso para funções do Azure AD no Gerenciamento de Identidade Privilegiada](pim-how-to-start-security-review.md)
- [Realize uma revisão de acesso das minhas funções do Azure AD no Gerenciamento de Identidade Privilegiada](pim-how-to-perform-security-review.md)
