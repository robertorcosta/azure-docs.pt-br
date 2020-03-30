---
title: Ativar funções de recursos do Azure no PIM - Azure AD | Microsoft Docs
description: Saiba como ativar suas funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023142"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Ative minhas funções de recurso do Azure no Gerenciamento de Identidade Privilegiada

Use o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) para permitir que os membros elegíveis dos recursos do Azure agendem a ativação para uma data e hora futuras. Eles também podem selecionar uma duração de ativação específica até o valor máximo (configurado pelos administradores).

Este artigo é para membros que precisam ativar sua função de recurso do Azure no Gerenciamento de Identidade Privilegiada.

## <a name="activate-a-role"></a>Ativar uma função

Quando você precisa assumir uma função de recurso do Azure, você pode solicitar ativação usando a opção De navegação **Minhas funções** no Gerenciamento de Identidade Privilegiada.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o bloco de gerenciamento de identidade privilegiado ao seu painel, consulte [Iniciar usando o Gerenciamento de Identidade Privilegiada](pim-getting-started.md).

1. Selecione **Minhas funções**.

    ![Minha página de funções mostrando funções que você pode ativar](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selecione **as funções de recurso do Azure** para ver uma lista das funções de recurso elegíveis do Azure.

   ![Minhas funções - Página de funções de recursos do Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Na lista **Funções de recursos do Azure**, encontre a função que você deseja ativar.

    ![Funções de recurso do Azure - Minha lista de funções elegíveis](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Selecione **Ativar** para abrir o painel Ativar.

1. Se sua função exigir autenticação multifator, selecione **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verifique minha identidade com o MFA antes da ativação da função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecione **Verificar minha identidade** e siga as instruções para fornecer verificação de segurança adicional.

    ![Tela para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se você quiser especificar um escopo reduzido, selecione **Escopo** para abrir o painel do filtro de recursos.

    É uma prática recomendada solicitar apenas o acesso aos recursos de que você precisa. No painel de filtro Recursos, você pode especificar os grupos de recursos ou recursos aos quais você precisa acessar.

    ![Ativar - Painel de filtro de recursos para especificar escopo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. O membro seria ativado após o horário selecionado.

1. Na caixa **Motivo**, insira o motivo da solicitação de ativação.

    ![Concluído Ativar painel com escopo, tempo de início, duração e razão](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Selecione **Ativar**.

    Se a função não exigir aprovação, ela já estará ativada e a função será exibida na lista de funções ativas. Se você quiser usar a função, siga os passos na próxima seção.

    Se a [função exigir aprovação](pim-resource-roles-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![A solicitação de ativação está pendente de notificação de aprovação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usar uma função imediatamente após a ativação

Em caso de atraso após a ativação, siga estas etapas após ativar para usar imediatamente as funções de recurso do Azure.

1. Abra o Azure AD Privileged Identity Management.

1. Selecione **Minhas funções** para ver uma lista de suas funções azure AD elegíveis e funções de recurso do Azure.

1. Selecione **funções de recurso do Azure**.

1. Selecione a **guia 'Funções ativas'.**

1. Uma vez que a função esteja ativa, saia do portal e faça login.

    O papel agora deve estar disponível para uso.

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Selecione **Minhas solicitações** para ver uma lista de suas solicitações de função Azure AD e azure.

    ![Minhas solicitações - Página de recursos do Azure mostrando suas solicitações pendentes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Role para a direita para exibir o **Status da solicitação** coluna.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento.

1. Abra o Azure AD Privileged Identity Management.

1. Selecione **Minhas solicitações**.

1. Para a função que deseja cancelar, selecione o link **Cancelar.**

    Quando você selecionar Cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Minha lista de pedidos com ação cancelar destacada](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Solução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>As permissões não são concedidas depois de ativar uma função

Quando você ativa uma função no Gerenciamento de Identidade Privilegiada, a ativação pode não se propagar instantaneamente para todos os portais que requerem a função privilegiada. Às vezes, mesmo quando a alteração é propagada, o cache da web em um portal pode fazer com que a alteração não entre em vigor de imediato. Se sua ativação está atrasada, aqui está o que você deve fazer.

1. Saia do portal do Azure e entre novamente.

    Quando você ativar uma função de recurso do Azure, você verá as etapas de sua ativação. Quando todas as fases forem concluídas, você verá um link **Sair**. Você pode usar este link para sair. Isso resolverá a maioria dos casos por atraso de ativação.

1. Em Gerenciamento de Identidade Privilegiada, verifique se você está listado como membro da função.

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Ative minhas funções ad do Azure no Gerenciamento de Identidade Privilegiada](pim-how-to-activate-role.md)
