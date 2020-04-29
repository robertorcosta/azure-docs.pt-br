---
title: Gerenciar usuários excluídos de políticas de acesso condicional – Azure AD
description: Saiba como usar as revisões de acesso do Azure Active Directory (AD do Azure) para gerenciar usuários que foram excluídos das políticas de acesso condicional
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91bb5a342eea079b6e9abcf109ad472151d3c13d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144492"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usar as revisões de acesso do Azure AD para gerenciar usuários excluídos das políticas de acesso condicional

Em um mundo ideal, todos os usuários seguem as políticas de acesso para proteger o acesso aos recursos da sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Este artigo apresenta alguns exemplos de situações em que podem ser necessárias exclusões. Você, como administrador de ti, pode gerenciar essa tarefa, evitar a supervisão de exceções de política e fornecer aos auditores uma prova de que essas exceções são revisadas regularmente usando as revisões de acesso do Azure Active Directory (Azure AD).

>[!NOTE]
> Para usar as revisões de acesso do Azure AD, é necessário ter uma licença válida do Azure AD Premium P2, uma licença paga do Enterprise Mobility + Security E5 ou uma licença de avaliação. Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Por que excluir usuários das políticas?

Digamos que, como administrador, você decida usar o [acesso condicional do Azure ad](../conditional-access/overview.md) para exigir a MFA (autenticação multifator) e limitar as solicitações de autenticação a redes ou dispositivos específicos. Durante o planejamento da implantação, você percebe que nem todos os usuários podem atender a esses requisitos. Por exemplo, você pode ter usuários que trabalham em escritórios remotos, e não parte da sua rede interna. Também pode ser necessário acomodar os usuários que se conectam usando dispositivos sem suporte enquanto aguardam a substituição desses dispositivos. Em suma, a empresa precisa que esses usuários entrem e realizem seu trabalho para que você os exclua das políticas de acesso condicional.

Como outro exemplo, você pode estar usando [locais nomeados](../conditional-access/location-condition.md) em acesso condicional para especificar um conjunto de países e regiões dos quais você não deseja permitir que os usuários acessem seu locatário.

![Locais nomeados no acesso condicional](./media/conditional-access-exclusion/named-locations.png)

Infelizmente, alguns usuários ainda podem ter um motivo válido para entrar nesses países/regiões bloqueados. Por exemplo, os usuários podem estar viajando para o trabalho e precisam acessar recursos corporativos. Nesse caso, a política de acesso condicional para bloquear esses países/regiões poderia usar um grupo de segurança de nuvem para os usuários excluídos da política. Usuários que precisam de acesso quando estão viajando podem adicionar a si mesmos ao grupo usando o [Gerenciamento de Grupos de Autoatendimento do Azure AD](../users-groups-roles/groups-self-service-management.md).

Outro exemplo seria que você tem uma política de acesso condicional que [bloqueia a autenticação herdada para a grande maioria dos seus usuários](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). No entanto, se você tiver alguns usuários que precisam usar métodos de autenticação herdados para acessar seus recursos por meio de clientes baseados no Office 2010 ou IMAP/SMTP/POP, você poderá excluir esses usuários da política que bloqueia os métodos de autenticação herdados.

>[!NOTE]
>A Microsoft recomenda enfaticamente que você bloqueie o uso de protocolos herdados em seu locatário para melhorar sua postura de segurança.

## <a name="why-are-exclusions-challenging"></a>Por que as exclusões são um desafio?

No Azure AD, você pode definir o escopo de uma política de acesso condicional para um conjunto de usuários. Você também pode configurar exclusões selecionando funções do Azure AD, usuários individuais ou convidados. Você deve ter em mente que, quando as exclusões são configuradas, a intenção de diretiva não pode ser imposta a usuários excluídos. Se as exclusões forem configuradas usando uma lista de usuários ou usando grupos de segurança locais herdados, você terá visibilidade limitada das exclusões. Como resultado:

- Os usuários podem não saber que eles foram excluídos.

- Os usuários podem ingressar no grupo de segurança para ignorar a política.

- Usuários excluídos podem ter qualificado para a exclusão antes, mas podem não ser mais qualificados para ele.

Frequentemente, quando você configura uma exclusão pela primeira vez, há uma agreguem de usuários que ignoram a política. Ao longo do tempo, cada vez mais usuários são adicionados à exclusão e a lista aumenta. Em algum momento, você precisa examinar a lista e confirmar se cada um desses usuários ainda está qualificado para exclusão. O gerenciamento da lista de exclusões, de um ponto de vista técnico, pode ser relativamente fácil, mas quem toma as decisões de negócios e como você garante que ela seja auditada? No entanto, se você configurar a exclusão usando um grupo do Azure AD, poderá usar as revisões de acesso como um controle de compensação, para impulsionar a visibilidade e reduzir o número de usuários excluídos.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Como criar um grupo de exclusão em uma política de acesso condicional

Siga estas etapas para criar um novo grupo do Azure AD e uma política de acesso condicional que não se aplica a esse grupo.

### <a name="create-an-exclusion-group"></a>Criar um grupo de exclusão

1. Entre no Portal do Azure.

2. No painel de navegação esquerdo, clique em **Azure Active Directory** e clique em **Grupos**.

3. No menu superior, clique em **Novo Grupo** para abrir o painel do grupo.

4. Na lista **Tipo de grupo**, selecione **Segurança**. Especifique um nome e uma descrição.

5. Certifique-se de definir o tipo de **Associação** como **Atribuído**.

6. Selecione os usuários que devem fazer parte do grupo de exclusão e, em seguida, clique em **Criar**.

![Painel novo grupo no Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Criar uma política de acesso condicional que exclui o grupo

Agora você pode criar uma política de acesso condicional que usa esse grupo de exclusão.

1. No painel de navegação esquerdo, clique em **Azure Active Directory** e, em seguida, clique em **acesso condicional** para abrir a folha **políticas** .

2. Clique em **Nova política** para abrir o painel **Novo**.

3. Especifique um nome.

4. Em Atribuições, clique em **Usuários e grupos**.

5. Na guia **Incluir**, selecione **Todos os Usuários**.

6. Na guia **excluir** , adicione uma marca de seleção a **usuários e grupos** e clique em **Selecionar usuários excluídos**.

7. Selecione o grupo de exclusão que você criou.

   > [!NOTE] 
   > Como melhor prática, é recomendável excluir pelo menos uma conta de administrador da política ao fazer testes para garantir que você não esteja bloqueado de seu locatário.

8. Continue com a configuração da política de acesso condicional com base em seus requisitos organizacionais.

![Selecionar o painel usuários excluídos em acesso condicional](./media/conditional-access-exclusion/select-excluded-users.png)
  
Vamos abordar dois exemplos em que você pode usar as revisões de acesso para gerenciar exclusões em políticas de acesso condicional.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exemplo 1: revisão de acesso para usuários que acessam de países/regiões bloqueados

Digamos que você tenha uma política de acesso condicional que bloqueie o acesso de determinados países/regiões. Ela inclui um grupo que é excluído da política. Esta é uma revisão de acesso recomendada em que os membros do grupo são revisados.

> [!NOTE] 
> Uma função de administrador global ou de administrador de usuários é necessária para criar revisões de acesso.

1. A revisão ocorrerá todas as semanas.

2. Nunca será encerrado para garantir que você esteja mantendo esse grupo de exclusão o mais atualizado.

3. Todos os membros do grupo estarão no escopo da revisão.

4. Cada usuário precisará atestar que eles ainda precisam de acesso a partir desses países/regiões bloqueados, portanto, eles ainda precisam ser membros do grupo.

5. Se o usuário não responder à solicitação de revisão, ele será removido automaticamente do grupo e não terá mais acesso ao locatário ao viajar para esses países/regiões.

6. Habilite notificações por email para permitir que os usuários saibam sobre o início e a conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemplo 2: revisão de acesso para usuários que acessam usando autenticação herdada

Digamos que você tenha uma política de acesso condicional que bloqueia o acesso para usuários usando autenticação herdada e versões de cliente mais antigas e inclui um grupo que é excluído da política. Esta é uma revisão de acesso recomendada em que os membros do grupo são revisados.

1. Essa revisão precisaria ser recorrente.

2. Todos no grupo precisam passar por ela.

3. Ela poderia ser configurada para listar os proprietários das unidades de negócios como revisores selecionados.

4. Aplique os resultados automaticamente e remova usuários que não foram aprovados para continuar usando métodos de autenticação herdados.

5. Pode ser útil habilitar as recomendações para que os revisores de grandes grupos possam tomar decisões com facilidade.

6. Habilite as notificações por email para que os usuários sejam notificados do início e da conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Se você tiver muitos grupos de exclusão e, portanto, precisar criar várias revisões de acesso, agora temos uma API no ponto de extremidade Microsoft Graph beta que permite criá-las e gerenciá-las programaticamente. Para começar, consulte a [Referência da API de revisões de acesso do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e o [Exemplo de recuperação de revisões de acesso do Azure AD por meio do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultados e logs de auditoria da revisão de acesso

Agora que você tem tudo em vigor, grupo, política de acesso condicional e revisões de acesso, é hora de monitorar e acompanhar os resultados dessas revisões.

1. Na portal do Azure, abra a folha **revisões de acesso** .

2. Abra o controle e o programa que você criou para gerenciar o grupo de exclusão.

3. Clique em **Resultados** para ver quem foi aprovado para permanecer na lista e quem foi removido.

    ![Resultados de revisões de acesso mostram quem foi aprovado](./media/conditional-access-exclusion/access-reviews-results.png)

4. Em seguida, clique em **Logs de auditoria** para ver as ações que foram executadas durante a revisão.

    ![Acesso examina logs de auditoria listando ações](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Como administrador de TI, você sabe que o gerenciamento de grupos de exclusão de suas políticas, às vezes, é inevitável. No entanto, manter esses grupos, analisá-los regularmente pelo proprietário da empresa ou pelos próprios usuários e auditar essas alterações pode ser mais fácil com as revisões de acesso do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [O que é o Acesso Condicional no Azure Active Directory?](../conditional-access/overview.md)
