---
title: Ativar minhas funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como ativar funções azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499169"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Ativar minhas funções do Azure AD no PIM

O Azure AD (Azure Active Directory) PIM (Privileged Identity Management) simplifica a forma como as empresas gerenciam o acesso privilegiado a recursos no Azure AD e em outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se você tiver se tornado elegível para uma função administrativa, isso significa que você poderá ativar essa função quando precisar executar ações que demandam privilégios. Por exemplo, se você ocasionalmente gerencia recursos do Office 365, administradores de função com privilégios de sua organização podem não o tornar um Administrador Global permanente, pois essa função também afeta outros serviços. Em vez disso, eles o tornam qualificado para funções do Azure AD, como Administrador do Exchange Online. Você pode solicitar a ativação da função quando precisar de seus privilégios e terá controle de administrador por um período predeterminado.

Este artigo é para administradores que precisam ativar sua função Azure AD no Gerenciamento de Identidade Privilegiada.

## <a name="determine-your-version-of-pim"></a>Determine sua versão do PIM

A partir de novembro de 2019, a parte de funções Azure AD do Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências para funções de recursos do Azure. Isso cria recursos adicionais, bem como [alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo lançada, quais procedimentos você segue neste artigo dependem da versão do Gerenciamento de Identidade Privilegiada que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Gerenciamento de Identidade Privilegiada você tem. Depois de conhecer sua versão do Gerenciamento de Identidade Privilegiada, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Faça login no [portal Azure](https://portal.azure.com/) com a função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Abra **o Azure AD Privileged Identity Management**. Se você tiver um banner na parte superior da página de visão geral, siga as instruções na guia **Nova versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="activate-a-role"></a>Ativar uma função

Quando você precisa assumir uma função AD do Azure, você pode solicitar ativação usando a opção De navegação **Minhas funções** no Gerenciamento de Identidade Privilegiada.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o bloco de gerenciamento de identidade privilegiado ao seu painel, consulte [Iniciar usando o Gerenciamento de Identidade Privilegiada](pim-getting-started.md).

1. Selecione **Minhas funções**e selecione **as funções do Azure AD** para ver uma lista de suas funções Azure AD elegíveis.

    ![Minha página de funções mostrando funções que você pode ativar](./media/pim-how-to-activate-role/my-roles.png)

1. Na lista **de funções do Azure AD,** encontre a função que deseja ativar.

    ![Funções azure AD - Minha lista de funções elegíveis](./media/pim-how-to-activate-role/activate-link.png)

1. Selecione **Ativar** para abrir o painel Ativar.

    ![Funções Azure AD - página de ativação contém duração e escopo](./media/pim-how-to-activate-role/activate-page.png)

1. Se sua função exigir autenticação multifator, selecione **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verifique minha identidade com o MFA antes da ativação da função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecione **Verificar minha identidade** e siga as instruções para fornecer verificação de segurança adicional.

    ![Tela para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se você quiser especificar um escopo reduzido, selecione **Escopo** para abrir o painel do filtro. No painel de filtro, você pode especificar os recursos azure AD aos quais você precisa acessar. É uma prática recomendada solicitar acesso apenas aos recursos que você precisa.

1. Se necessário, especifique uma hora de início de ativação personalizada. A função Azure AD seria ativada após o tempo selecionado.

1. Na caixa **Motivo**, insira o motivo da solicitação de ativação.

1. Selecione **Ativar**.

    Se a função não exigir aprovação, ela já estará ativada e a função será exibida na lista de funções ativas. Se você quiser usar a função, siga os passos na próxima seção.

    ![Concluído Ativar painel com escopo, tempo de início, duração e razão](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Se a [função exigir aprovação](pim-resource-roles-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![A solicitação de ativação está pendente de notificação de aprovação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usar uma função imediatamente após a ativação

Em caso de atraso após a ativação, siga estas etapas após ativar para usar as funções do Azure AD imediatamente.

1. Abra o Azure AD Privileged Identity Management.

1. Selecione **Minhas funções** para ver uma lista de suas funções azure AD elegíveis e funções de recurso do Azure.

1. Selecione **funções Azure AD**.

1. Selecione a **guia 'Funções ativas'.**

1. Uma vez que a função esteja ativa, saia do portal e faça login.

    O papel agora deve estar disponível para uso.

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Selecione **Minhas solicitações** para ver uma lista de suas solicitações de função Azure AD e azure.

    ![Minhas solicitações - Página do Azure AD mostrando suas solicitações pendentes](./media/pim-how-to-activate-role/my-requests-page.png)

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

    Quando você ativar uma função Azure AD, você verá as etapas de sua ativação. Quando todas as fases forem concluídas, você verá um link **Sair**. Você pode usar este link para sair. Isso resolverá a maioria dos casos por atraso de ativação.

1. Em Gerenciamento de Identidade Privilegiada, verifique se você está listado como membro da função.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="activate-a-role"></a>Ativar uma função

Quando você precisa assumir uma função AD do Azure, você pode solicitar ativação usando a opção De navegação **Minhas funções** no Gerenciamento de Identidade Privilegiada.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o bloco de gerenciamento de identidade privilegiado ao seu painel, consulte [Iniciar usando o Gerenciamento de Identidade Privilegiada](pim-getting-started.md).

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas funções** para ver uma lista de suas funções azure AD elegíveis.

    ![Funções Azure AD - Minhas funções mostrando lista de funções elegíveis ou ativas](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Localize uma função que você deseja ativar.

    ![Funções azure AD - Minha lista de funções elegíveis mostrando ativar link](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique em **Ativar** para abrir o painel Detalhes de ativação de função.

1. Se sua função exigir a MFA (autenticação multifator), clique em **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verifique meu painel de identidade com MFA antes da ativação da função](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique em **Verificar minha identidade** e siga as instruções para fornecer a verificação de segurança adicional.

    ![Página de verificação de segurança adicional perguntando como entrar em contato com você](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique em **Ativar** para abrir o painel Ativação.

    ![Painel de ativação para especificar tempo de início, duração, ticket e razão](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizada.

1. Especifique a duração da ativação.

1. Na caixa **Motivo da ativação**, insira o motivo para a solicitação de ativação. Algumas funções exigem que você forneça um número de tíquete de problema.

    ![Painel de ativação concluído com tempo de início personalizado, duração, ticket e razão](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique em **Ativar**.

    Se a função não precisar de aprovação, aparecerá um painel de **status de ativação** que exibe o status da ativação.

    ![Página de status de ativação mostrando os três estágios de ativação](./media/pim-how-to-activate-role/activation-status.png)

    Uma vez que todas as etapas estejam concluídas, clique no link **Sair** para sair do portal Azure. Quando você entrar de volta no portal, agora você pode usar o papel.

    Se a [função exigir aprovação](./azure-ad-pim-approval-workflow.md) para ativar, uma notificação do Azure aparecerá no canto superior direito do seu navegador informando que a solicitação está pendente de aprovação.

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas solicitações** para ver uma lista das suas solicitações.

    ![Funções azure AD - Minha lista de pedidos](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Desativar uma função

Após uma função ter sido ativada, ela será desativada automaticamente quando limite de tempo (duração qualificada) for atingido.

Se concluir suas tarefas de administrador antes, você também poderá desativar uma função manualmente no Azure AD Privileged Identity Management.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas funções**.

1. Clique em **Funções ativas** para ver a lista de funções ativas.

1. Localize a função que você terminou de usar e clique em **Destivar**.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas solicitações**.

1. Para a função que você deseja cancelar, clique no botão **Cancelar**.

    Quando você clicar em Cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Minha lista de solicitações com o botão Cancelar destacado](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Solução de problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>As permissões não são concedidas depois de ativar uma função

Quando você ativa uma função no Gerenciamento de Identidade Privilegiada, a ativação pode não se propagar instantaneamente para todos os portais que requerem a função privilegiada. Às vezes, mesmo quando a alteração é propagada, o cache da web em um portal pode fazer com que a alteração não entre em vigor de imediato. Se sua ativação está atrasada, aqui está o que você deve fazer.

1. Saia do portal do Azure e entre novamente.

    Quando você ativar uma função Azure AD, você verá as etapas de sua ativação. Quando todas as fases forem concluídas, você verá um link **Sair**. Você pode usar este link para sair. Isso resolverá a maioria dos casos por atraso de ativação.

1. Em Gerenciamento de Identidade Privilegiada, verifique se você está listado como membro da função.

 ---

## <a name="next-steps"></a>Próximas etapas

- [Ative minhas funções ad do Azure no Gerenciamento de Identidade Privilegiada](pim-how-to-activate-role.md)
