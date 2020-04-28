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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67476243"
---
## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais revisões de acesso

1. Clique em **novo** para criar uma nova revisão de acesso.

1. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Defina a **Data de início**. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

    ![Data de início, frequência, duração, fim, número de vezes e data de término](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Para fazer com que a revisão de acesso seja recorrente, altere a configuração de **frequência** de **uma vez** para **semanal**, **mensal**, **trimestral**, **anual**ou **semianual**. Use o controle deslizante **duração** ou a caixa de texto para definir o número de dias que cada revisão da série recorrente será aberta para entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **Final** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou após a conclusão de um número definido de ocorrências. Você, outro administrador de usuário ou outro administrador global pode interromper a série após a criação alterando a data em **configurações**, de modo que ela termine nessa data.

1. Na seção **usuários** , selecione uma ou mais funções das quais você deseja revisar a associação.

    ![Escopo dos usuários para revisar a associação de função de](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > A seleção de mais de uma função criará várias revisões de acesso. Por exemplo, a seleção de cinco funções criará cinco revisões de acesso separadas.

    Se você estiver criando uma revisão de acesso das funções do Azure AD, veja a seguir um exemplo da lista examinar associação.

    ![Examine o painel Associação listando as funções do Azure AD que você pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Se você estiver criando uma revisão de acesso das funções de recurso do Azure, o exemplo a seguir mostrará uma lista de membros de revisão.

    ![Examinar o painel Associação listando as funções de recurso do Azure que você pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Na seção **revisores** , selecione uma ou mais pessoas para examinar todos os usuários. Ou você pode selecionar para que os membros examinem seus próprios acessos.

    ![Lista de revisores de usuários ou membros selecionados (próprio)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Usuários selecionados** – Use essa opção quando você não souber quem precisa de acesso. Com essa opção, você pode atribuir a revisão a um proprietário de recurso ou ao gerente do grupo para conclusão.
    - **Membros (próprio)** – Use esta opção para fazer com que os usuários examinem suas próprias atribuições de função.

### <a name="upon-completion-settings"></a>Após configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **Após configurações de conclusão**.

    ![Após as configurações de conclusão para aplicação automática e deve revisar não responder](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Se você quiser remover automaticamente o acesso para usuários que foram negados, defina **Resultados de aplicação automática ao recurso** para **Habilitar**. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para **Desabilitar**.

1. Use a lista **Se o revisor não responder** para especificar o que acontece para usuários que não foram examinados pelo revisor dentro do período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

    - **Nenhuma alteração** - deixar o acesso do usuário inalterado
    - **Remover o acesso** - remover o acesso do usuário
    - **Aprovar o acesso** - aprovar o acesso do usuário
    - **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

### <a name="advanced-settings"></a>Configurações avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas**.

    ![Configurações avançadas para mostrar recomendações, exigir motivo de aprovação, notificações por email e lembretes](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Definir **Mostrar recomendações** à **Habilitar** para mostrar aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Definir **Requer motivo sob aprovação** para **Habilitar** para exigir que o revisor forneça um motivo para aprovação.

1. Definir **Notificações por email** para **Habilitar** para que o Azure Active Directory envie notificações por email para os revisores quando uma revisão de acesso começar e para os administradores quando uma revisão terminar.

1. Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise.
