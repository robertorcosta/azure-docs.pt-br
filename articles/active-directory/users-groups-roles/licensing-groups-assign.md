---
title: Atribuir licenças a um grupo - Azure Active Directory | Microsoft Docs
description: Como atribuir licenças a usuários com o licenciamento de grupo do Azure Active Directory
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253059"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Atribuir licenças a usuários por meio da associação a grupos no Azure Active Directory

Este artigo orienta você a atribuir licenças de produtos a um grupo de usuários e verificar se eles estão licenciados corretamente no Azure Active Directory (Azure AD).

Neste exemplo, o locatário contém um grupo de segurança chamado **Departamento de RH**. Esse grupo inclui todos os membros do departamento de recursos humanos (cerca de 1.000 usuários). Você deseja atribuir licenças do Office 365 Enterprise E3 para todo o departamento. O serviço Yammer Enterprise, que está incluído no produto, precisa ser desabilitado temporariamente até que o departamento esteja pronto para começar a usá-lo. Você também deseja implantar licenças do Enterprise Mobility + Security para o mesmo grupo de usuários.

> [!NOTE]
> Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade “Local de uso” para o usuário.
>
> Para a atribuição de licenças de grupo, qualquer usuário sem um local de uso especificado herdará o local do diretório. Se você tiver usuários em vários locais, é recomendável sempre definir o local de uso como parte de seu fluxo de criação do usuário no Microsoft Azure AD (por exemplo, por meio da configuração do AAD Connect) – isso garante que o resultado da atribuição de licenças sempre seja correto e que os usuários não recebam serviços em locais que não são permitidos.

## <a name="step-1-assign-the-required-licenses"></a>Etapa 1: Atribuir as licenças necessárias

1. Faça login no centro de administração do [**Azure AD**](https://aad.portal.azure.com) com uma conta de administrador de licença. Para gerenciar licenças, a conta deve ser um administrador de licenças, administrador de usuário ou administrador global.

1. Selecione **Licenças** para abrir uma página onde você pode ver e gerenciar todos os produtos licenciáveis no inquilino.

1. Em **Todos os produtos,** selecione o Office 365 Enterprise E5 e o Enterprise Mobility + Security E3 selecionando os nomes dos produtos. Para iniciar a atribuição, **selecione Atribuir** no topo da página.

   ![Selecione produtos para atribuir licenças](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Na página **de licença Atribuir,** selecione **Usuários e grupos** para abrir uma lista de usuários e grupos.

1. Selecione um usuário ou grupo e use o botão **Selecionar** na parte inferior da página para confirmar sua seleção.

1. Na página **de licença Atribuir,** clique **em Opções de Atribuição**, que exibe todos os planos de serviço incluídos nos dois produtos que selecionamos anteriormente. Encontre **a Yammer Enterprise** e **desligue-a** para desativar esse serviço da licença do produto. Confirme clicando em **OK** na parte inferior das **opções de licença**.

   ![selecionar planos de serviço para licenças](./media/licensing-groups-assign/assignment-options.png)
  
1. Para concluir a atribuição, na página **de licença Atribuir,** clique em **Atribuir** na parte inferior da página.

1. Uma notificação é exibida no canto superior direito mostrando o status e o resultado do processo. Se a atribuição para o grupo não pode ser concluída (por exemplo, devido a licenças já existentes no grupo), clique na notificação para exibir detalhes da falha.

Ao atribuir licenças a um grupo, o Azure AD processa todos os membros existentes desse grupo. Esse processo pode levar algum tempo, variando com o tamanho do grupo. A próxima etapa descreve como verificar se o processo foi concluído e como determinar se atenção adicional é necessária para resolver problemas.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Etapa 2: Verificar se a atribuição inicial foi concluída

1. Vá para **grupos de diretórios ativos do** > Azure **.** Selecione o grupo ao que as licenças foram atribuídas.

1. Na página do grupo, selecione **Licenças**. Isso lhe permite confirmar rapidamente se as licenças foram totalmente atribuídas a usuários e se houve erros que exigem atenção. As informações a seguir estão disponíveis:

   - Licenças de serviço atualmente atribuídas ao grupo. Selecione uma entrada para mostrar a serviços específicos que foram habilitada e fazer alterações.

   - Atualizações de status das últimas alterações de licença, que estão disponíveis se as alterações estão sendo processadas ou se o processamento tiver sido concluído para todos os membros do usuário.

   - Informações sobre atribuições de licença de usuário que estão em estado de erro.

   ![erros de licenciamento e status de licença](./media/licensing-groups-assign/assignment-errors.png)

1. Veja informações mais detalhadas sobre o processamento de licenças em **usuários do Azure Active Directory** > **e grupos** > *de nomes de grupos Registros* > **de auditoria**. Confira as seguintes atividades:

   - Atividade: `Start applying group based license to users`. Isso é registrado quando o sistema identifica a alteração de atribuição de licença no grupo e inicia a aplicá-la a todos os membros de usuário. Ele contém informações sobre a alteração foi feita.

   - Atividade: `Finish applying group based license to users`. Isso é registrado quando o sistema conclui o processamento de todos os usuários no grupo. Ele contém um resumo de quantos usuários foram processados com êxito e quantos usuários não puderam ter licenças de grupo atribuídas.

   [Leia esta seção](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) para saber mais sobre como logs de auditoria podem ser usados para analisar alterações feitas pelo licenciamento baseado em grupo.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Etapa 3: verificar problemas de licença e resolvê-los

1. Vá para **Azure Active Directory** > **Groups**e encontre o grupo ao qual as licenças foram atribuídas.
1. Na página do grupo, selecione **Licenças**. A notificação no topo da página mostra que há 10 usuários aos quais as licenças não poderiam ser atribuídas. Abra-o para ver uma lista de todos os usuários em um estado de erro de licenciamento para este grupo.
1. A coluna **Atribuições com falha** informa que ambas as licenças de produtos não puderam ser atribuídas aos usuários. A coluna **Principal motivo para falha** contém a causa da falha. Nesse caso, **Planos de serviço conflitante**.

   ![licenças que não poderiam ser atribuídos](./media/licensing-groups-assign/failed-assignments.png)

1. Selecione um usuário para abrir a página **Licenças** do usuário. Esta página mostra todas as licenças que estão atualmente atribuídas ao usuário. Neste exemplo, o usuário tem a licença Office 365 Enterprise E1 herdada do grupo **Usuários do quiosque**. Isso está em conflito com a licença E3 que o sistema tentou aplicar a partir do grupo **Departamento de RH**. Como resultado, nenhuma das licenças do grupo foram atribuídas ao usuário.

   ![Exibir todos os conflitos de licença para um usuário](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Para resolver este conflito, podemos remover o usuário do grupo **Usuários do quiosque**. Após a alteração do Azure AD processa o **departamento de RH** licenças são atribuídas corretamente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o conjunto de recursos para atribuição de licençausando grupos, consulte os seguintes artigos:

- [O que é o licenciamento baseado em grupo no Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](licensing-groups-migrate-users.md)
- [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Microsoft Azure Active Directory](licensing-groups-change-licenses.md)
- [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](../active-directory-licensing-group-advanced.md)
- [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](licensing-ps-examples.md)
