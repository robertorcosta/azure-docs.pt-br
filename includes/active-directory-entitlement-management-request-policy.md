---
title: incluir arquivo
description: incluir arquivo
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 11/11/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: c73a62b2d5feeae42a5ea35c88073dd5fcc0d78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77192479"
---
## <a name="for-users-in-your-directory"></a>Para usuários em seu diretório

Siga essas etapas se quiser permitir que os usuários em seu diretório possam solicitar esse pacote de acesso. Ao definir a política de solicitação, você pode especificar usuários individuais ou grupos de usuários mais comumente. Por exemplo, sua organização pode já ter um grupo como **Todos os funcionários**.  Se esse grupo for adicionado na política para usuários que podem solicitar acesso, qualquer membro desse grupo poderá solicitar acesso.

1. Na seção **Usuários que podem solicitar acesso**, clique em **Para usuários em seu diretório**.

    Quando você seleciona essa opção, novas opções aparecem para refinar ainda mais quem em seu diretório pode solicitar esse pacote de acesso.

    ![Pacote de acesso - Solicitações - Para usuários em seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Usuários e grupos específicos** | Escolha esta opção se quiser apenas os usuários e grupos em seu diretório que você especificar para poder solicitar este pacote de acesso. |
    | **Todos os membros (excluindo convidados)** | Escolha esta opção se você quiser que todos os usuários membros em seu diretório possam solicitar este pacote de acesso. Essa opção não inclui nenhum usuário convidado que você possa ter convidado em seu diretório. |
    | **Todos os usuários (incluindo convidados)** | Escolha esta opção se você quiser que todos os usuários membros e usuários convidados em seu diretório possam solicitar este pacote de acesso. |

    Os usuários convidados referem-se a usuários externos que foram convidados para o seu diretório com [o Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Para obter mais informações sobre as diferenças entre usuários membros e usuários [convidados, consulte Quais são as permissões de usuário padrão no Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md)

1. Se você selecionou **Usuários e grupos específicos,** clique **em Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione os usuários e grupos que deseja adicionar.

    ![Pacote de acesso - Solicitações - Selecionar usuários e grupos](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Clique **em Selecionar** para adicionar os usuários e grupos.

1. Pule para a seção [de aprovação.](#approval)

## <a name="for-users-not-in-your-directory"></a>Para usuários que não estão em seu diretório

 **Usuários que não estão em seu diretório** se referem a usuários que estão em outro diretório ou domínio do Azure AD. Esses usuários podem ainda não ter sido convidados para o seu diretório. Os diretórios Azure AD devem ser configurados para permitir convites em **restrições de colaboração**. Para obter mais informações, consulte [Ativar colaboração externa B2B e gerenciar quem pode convidar os convidados](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Uma conta de usuário convidado será criada para um usuário que ainda não está em seu diretório cuja solicitação seja aprovada ou aprovada automaticamente. O convidado será convidado, mas não receberá um e-mail de convite. Em vez disso, eles receberão um e-mail quando sua atribuição de pacote de acesso for entregue. Por padrão, mais tarde, quando esse usuário convidado não tiver mais nenhuma atribuição de pacote de acesso, porque sua última atribuição expirou ou foi cancelada, essa conta de usuário convidado será bloqueada do login e posteriormente excluída. Se você quiser que os usuários convidados permaneçam em seu diretório indefinidamente, mesmo que eles não tenham atribuições de pacote de acesso, você pode alterar as configurações para sua configuração de gerenciamento de direitos. Para obter mais informações sobre o objeto de usuário convidado, consulte [Propriedades de um usuário de colaboração B2B do Azure Active Directory](../articles/active-directory/b2b/user-properties.md).

Siga estas etapas se quiser permitir que usuários que não estão em seu diretório solicitem este pacote de acesso:

1. Nos **Usuários que podem solicitar** a seção de acesso, clique em Para usuários que não estão em seu **diretório**.

    Quando você seleciona essa opção, novas opções aparecem.

    ![Pacote de acesso - Solicitações - Para usuários que não estão em seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Organizações conectadas específicas** | Escolha essa opção se quiser selecionar em uma lista de organizações que o administrador adicionou anteriormente. Todos os usuários das organizações selecionadas podem solicitar este pacote de acesso. |
    | **Todas as organizações conectadas** | Escolha esta opção se todos os usuários de todas as suas organizações conectadas podem solicitar este pacote de acesso. |
    | **Todos os usuários (Todas as organizações conectadas + quaisquer novos usuários externos)** | Escolha esta opção se todos os usuários de todas as suas organizações conectadas podem solicitar este pacote de acesso e que as configurações de lista B2B permitam ou neguem devem ter precedência para qualquer novo usuário externo. |

    Uma organização conectada é um diretório ou domínio Ad externo do Azure com o que você tem um relacionamento.

1. Se você selecionou **Organizações conectadas específicas,** clique **em Adicionar diretórios** para selecionar a partir de uma lista de organizações conectadas que o administrador adicionou anteriormente.

1. Digite o nome ou nome de domínio para procurar uma organização previamente conectada.

    ![Pacote de acesso - Solicitações - Selecionar diretórios](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se a organização com a qual você deseja colaborar não estiver na lista, você pode pedir ao administrador para adicioná-la como uma organização conectada. Para obter mais informações, consulte [Adicionar uma organização conectada](../articles/active-directory/governance/entitlement-management-organization.md).

1. Depois de selecionar todas as suas organizações conectadas, clique em **Selecionar**.

    > [!NOTE]
    > Todos os usuários das organizações conectadas selecionadas poderão solicitar este pacote de acesso. Isso inclui usuários no Azure AD de todos os subdomínios associados à organização, a menos que esses domínios sejam bloqueados pela lista Azure B2B. Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](../articles/active-directory/b2b/allow-deny-list.md).

1. Pule para a seção [de aprovação.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>Nenhuma (somente atribuições diretas do administrador)

Siga essas etapas se quiser ignorar as solicitações de acesso e permitir que os administradores atribuam diretamente usuários específicos a este pacote de acesso. Os usuários não terão que solicitar o pacote de acesso. Você ainda pode definir as configurações do ciclo de vida, mas não há configurações de solicitação.

1. Na **seção Usuários que podem solicitar acesso,** clique em **Nenhum (somente atribuições diretas de administrador .**

    ![Pacote de acesso - Solicitações - Nenhum administrador apenas atribuições diretas](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Depois de criar o pacote de acesso, você pode atribuir diretamente usuários internos e externos específicos ao pacote de acesso. Se você especificar um usuário externo, uma conta de usuário convidado será criada em seu diretório. Para obter informações sobre como atribuir diretamente um usuário, consulte [Exibir, adicionar e remover atribuições para um pacote de acesso](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Desça para a [seção Ativar solicitações.](#enable-requests)

## <a name="approval"></a>Aprovação

Na seção Aprovação, você especifica se uma aprovação é necessária quando os usuários solicitam esse pacote de acesso. As configurações de aprovação funcionam da seguinte forma:

- Apenas um dos aprovadores selecionados ou aprovadores de recuo precisa aprovar um pedido de aprovação em etapa única. 
- Apenas um dos aprovadores selecionados de cada etapa precisa aprovar um pedido de aprovação em duas etapas.
- O aprovador pode ser um gerente, patrocinador interno ou patrocinador externo, dependendo de quem a política está regindo o acesso.
- A aprovação de cada aprovador selecionado não é necessária para aprovação única ou em duas etapas.
- A decisão de aprovação baseia-se em qualquer aprovador que revise o pedido primeiro.

Para uma demonstração de como adicionar aprovadores a uma política de solicitação, assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Para uma demonstração de como adicionar uma aprovação em vários estágios a uma política de solicitação, assista ao vídeo a seguir:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Siga estas etapas para especificar as configurações de aprovação das solicitações para o pacote de acesso:

1. Para exigir aprovação para solicitações dos usuários selecionados, defina o alternador de **aprovação Exigir** para **Sim**. Ou, para ter os pedidos automaticamente aprovados, defina o **alternância**para Não .

1. Para exigir que os usuários forneçam uma justificativa para solicitar o pacote de acesso, defina a **justificativa de solicitação** de exigência alternar para **Sim**.
    
1. Agora determine se as solicitações exigirão aprovação única ou em dois estágios. Defina o **Número de etapas** alternando para **1** para aprovação de um estágio ou defina o alternador para **2** para aprovação em dois estágios.

    ![Pacote de acesso - Solicitações - Configurações de aprovação](./media/active-directory-entitlement-management-request-policy/approval.png)

Use as seguintes etapas para adicionar aprovadores depois de selecionar quantos estágios você precisa: 

### <a name="single-stage-approval"></a>Aprovação em estágio único

1. Adicione o **primeiro aprovador:**
    
    Se a diretiva estiver definida para reger o acesso aos usuários em seu diretório, você poderá selecionar **o Manager como aprovador**. Ou, adicione um usuário específico clicando em **Adicionar aprovadores** depois de selecionar Escolher aprovadores específicos no menu suspenso.
    
    ![Pacote de acesso - Solicitações - Para usuários em diretório - Primeiro Aprovador](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Se esta política for definida para governar o acesso para usuários que não estão em seu diretório, você pode selecionar **patrocinador externo** ou **patrocinador interno**. Ou, adicione um usuário específico clicando em **Adicionar aprovadores** ou grupos em Escolher aprovadores específicos.
    
    ![Pacote de acesso - Solicitações - Para usuários fora do diretório - Primeiro Aprovador](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Se você selecionou **o Manager** como o primeiro aprovador, clique em **Adicionar recuo** para selecionar um ou mais usuários ou grupos em seu diretório para ser um aprovador de recuo. Os aprovadores de retorno recebem a solicitação se o gerenciamento de direitos não encontrar o gerente para o usuário que solicita acesso.

    O gerente é encontrado pela gestão de direitos usando o atributo **Manager.** O atributo está no perfil do usuário no Azure AD. Para obter mais informações, consulte [Adicionar ou atualizar as informações do perfil de um usuário usando o Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Se você selecionou **Escolher aprovadores específicos,** clique **em Adicionar aprovadores** para selecionar um ou mais usuários ou grupos em seu diretório para serem aprovadores.

1. Na caixa em **Decisão deve ser feita em quantos dias?**, especifique o número de dias que um aprovador tem que rever uma solicitação para este pacote de acesso.

    Se uma solicitação não for aprovada dentro desse prazo, ela será automaticamente negada. O usuário terá que enviar outra solicitação para o pacote de acesso.

1. Para exigir que os aprovadores forneçam uma justificativa para sua decisão, defina a justificativa do aprovador para **Sim**.

    A justificativa é visível para outros aprovadores e o solicitante.

### <a name="2-stage-approval-preview"></a>Aprovação em 2 estágios (Visualização)

Se você selecionou uma aprovação de duas etapas, você precisará adicionar um segundo aprovador.

1. Adicionar o **segundo aprovador:** 
    
    Se os usuários estiverem em seu diretório, adicione um usuário específico como o segundo aprovador clicando em **Adicionar aprovadores** em Escolher aprovadores específicos.

    ![Pacote de acesso - Solicitações - Para usuários em diretório - Segundo Aprovador](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Se os usuários não estão em seu diretório, selecione **patrocinador interno** ou **patrocinador externo** como o segundo aprovador. Depois de selecionar o aprovador, adicione os aprovadores de recuo.

    ![Pacote de acesso - Solicitações - Para usuários fora do diretório - Segundo Aprovador](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Especificar o número de dias que o segundo aprovador tem para aprovar a solicitação na caixa **Decisão deve ser feita em quantos dias?**. 

1. Defina a justificativa do aprovador de Exigir alternar **para Sim** ou **Não**.

### <a name="alternate-approvers"></a>Aprovadores alternativos

Você pode especificar aprovadores alternativos, semelhante saem especificando os aprovadores primeiro e segundo que podem aprovar solicitações. Ter aprovadores alternativos ajudará a garantir que os pedidos sejam aprovados ou negados antes de expirarem (tempo de intervalo). Você pode listar os aprovadores suplentes o primeiro aprovador e o segundo aprovador para aprovação em duas etapas. 

Ao especificar os aprovadores suplentes, caso o primeiro ou segundo aprovadores não conseguissem aprovar ou negar a solicitação, a solicitação pendente é encaminhada aos aprovadores alternativos, de acordo com o cronograma de encaminhamento especificado durante a configuração da política. Eles recebem um e-mail para aprovar ou negar a solicitação pendente.

Após o encaminhamento do pedido aos aprovadores suplentes, o primeiro ou segundo aprovadorainda pode aprovar ou negar o pedido. Os aprovadores alternativos usam o mesmo site do Meu Acesso para aprovar ou negar a solicitação pendente.

Podemos listar pessoas ou grupos de pessoas para serem aprovadores e aprovadores alternativos. Por favor, certifique-se de listar diferentes conjuntos de pessoas para serem os primeiros, segundo e aprovadores alternativos.
Por exemplo, se você listou Alice e Bob como os primeiros aprovadores, liste Carol e Dave como os aprovadores alternativos. Use as seguintes etapas para adicionar aprovadores alternativos a um pacote de acesso:

1. No primeiro aprovador, segundo aprovador, ou ambos, clique em **Mostrar configurações avançadas de solicitação**.

    ![Pacote de acesso - Política - Mostrar configurações avançadas de solicitação](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Definir **Se nenhuma ação for tomada, encaminhe para os aprovadores alternativos?** **Yes**

1. Clique **em Adicionar aprovadores alternativos** e selecione os aprovadores alternativos da lista.

    ![Pacote de acesso - Política - Adicionar aprovadores alternativos](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. No **Forward para suplente de aprovador(s) após quantos dias** caixa, colocar no número de dias os aprovados têm que aprovar ou negar um pedido. Se nenhum aprovador tiver aprovado ou negado a solicitação antes da duração da solicitação, a solicitação expira (tempo mínimo), e o usuário terá que enviar outra solicitação para o pacote de acesso. 

    Os pedidos só podem ser encaminhados a aprovadores suplentes um dia após a duração da solicitação atingir meia-vida. Neste exemplo, a duração da solicitação é de 14 dias. Assim, a duração da solicitação chega à meia-vida no dia 7. Portanto, o pedido não pode ser encaminhado antes do dia 8. Além disso, os pedidos não podem ser encaminhados no último dia da duração da solicitação. Assim, no exemplo, o mais recente que a solicitação pode ser encaminhada é o dia 13.

## <a name="enable-requests"></a>Habilitar solicitações

1. Se você quiser que o pacote de acesso seja disponibilizado imediatamente para os usuários na política de solicitação para solicitar, mova o Alternar Para **Sim**.

    Você sempre pode habilitá-lo no futuro depois de terminar de criar o pacote de acesso.

    Se você selecionou **Nenhum (somente atribuições diretas de administrador)** e você definir ativar **não,** então os administradores não poderão atribuir diretamente esse pacote de acesso.

    ![Pacote de acesso - Política - Ativar a configuração de diretiva](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Clique em **Avançar**.
