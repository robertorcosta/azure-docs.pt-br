---
title: incluir arquivo
description: incluir arquivo
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476243"
---
## <a name="create-one-or-more-access-reviews"></a>Crie uma ou mais avaliações de acesso

1. Clique **em Novo** para criar uma nova revisão de acesso.

1. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Defina a **Data de início**. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

    ![Data de início, frequência, duração, fim, número de vezes e data de término](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a configuração de **freqüência** de **Uma vez** para **Semanal,** **Mensal,** **Trimestral,** **Anual ou** **Semestral**. Use o controle deslizante **de duração** ou caixa de texto para definir quantos dias cada revisão da série recorrente estará aberta para entrada dos revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **Final** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou após a conclusão de um número definido de ocorrências. Você, outro administrador de usuário ou outro administrador Global pode interromper a série após a criação alterando a data em **Configurações,** para que ela termine nessa data.

1. Na seção **Usuários,** selecione uma ou mais funções das que deseja revisar a adesão.

    ![Escopo dos usuários para revisar a adesão à função de](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Selecionar mais de uma função criará várias avaliações de acesso. Por exemplo, selecionar cinco funções criará cinco avaliações de acesso separadas.

    Se você estiver criando uma revisão de acesso das funções do Azure AD, o seguinte mostrará um exemplo da lista de membros do Review.

    ![Revisar o painel de membros listando funções azure AD que você pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Se você estiver criando uma revisão de acesso das funções de recurso do Azure, o seguinte mostrará um exemplo da lista de membros do Review.

    ![Revisar o painel de membros listando funções de recursos do Azure que você pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Na seção **Revisores,** selecione uma ou mais pessoas para revisar todos os usuários. Ou você pode selecionar para que os membros examinem seus próprios acessos.

    ![Lista de revisores de usuários ou membros selecionados (self)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Usuários selecionados** - Use esta opção quando você não sabe quem precisa de acesso. Com essa opção, você pode atribuir a revisão a um proprietário de recurso ou ao gerente do grupo para conclusão.
    - **Membros (self)** - Use essa opção para que os usuários revisem suas próprias atribuições de função.

### <a name="upon-completion-settings"></a>Após configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **Após configurações de conclusão**.

    ![Após as configurações de conclusão para aplicar automaticamente e não deve responder](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Se você quiser remover automaticamente o acesso para usuários que foram negados, defina **Resultados de aplicação automática ao recurso** para **Habilitar**. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para **Desabilitar**.

1. Use a lista **Se o revisor não responder** para especificar o que acontece para usuários que não foram examinados pelo revisor dentro do período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

    - **Nenhuma alteração** - deixar o acesso do usuário inalterado
    - **Remover o acesso** - remover o acesso do usuário
    - **Aprovar o acesso** - aprovar o acesso do usuário
    - **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

### <a name="advanced-settings"></a>Configurações avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas**.

    ![Configurações avançadas para recomendações de exibição, requerem razão sobre aprovação, notificações de e-mail e lembretes](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Definir **Mostrar recomendações** à **Habilitar** para mostrar aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Definir **Requer motivo sob aprovação** para **Habilitar** para exigir que o revisor forneça um motivo para aprovação.

1. Definir **Notificações por email** para **Habilitar** para que o Azure Active Directory envie notificações por email para os revisores quando uma revisão de acesso começar e para os administradores quando uma revisão terminar.

1. Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise.
