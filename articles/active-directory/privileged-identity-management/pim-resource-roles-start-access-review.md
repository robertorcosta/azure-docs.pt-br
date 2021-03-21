---
title: Criar uma revisão de acesso das funções de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 110a94c78427087f4ca5555f59055ab8e3bebcee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592542"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management

A necessidade de acesso a funções privilegiadas de recursos do Azure por funcionários muda com o passar do tempo. Para reduzir o risco associado a atribuições de função obsoletas, você deve examinar o acesso regularmente. Você pode usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para criar revisões de acesso para acesso privilegiado às funções de recurso do Azure. Você também pode configurar revisões de acesso recorrentes que ocorrem automaticamente. Este artigo descreve como criar uma ou mais revisões de acesso.

## <a name="prerequisite-role"></a>Função de pré-requisito

 Para criar revisões de acesso, você deve ser atribuído à função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) ou [administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) do Azure para o recurso.

## <a name="open-access-reviews"></a>Abrir revisões de acesso

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que é atribuído a uma das funções de pré-requisito.

1. Abra **Azure ad Privileged Identity Management**.

1. No menu à esquerda, selecione **recursos do Azure**.

1. Selecione o recurso que você deseja gerenciar, como uma assinatura.

1. Em gerenciar, selecione **revisões de acesso**.

    ![Recursos do Azure-lista de revisões de acesso mostrando o status de todas as revisões](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. Clique em **Novo** para criar uma revisão de acesso.

1. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/pim-resource-roles-start-access-review/name-description.png)

1. Defina a **Data de início**. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

    ![Data de início, frequência, duração, fim, número de vezes e data de término](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. Para fazer com que a revisão de acesso seja recorrente, altere a configuração **Frequência** de **Uma vez** para **Semanal**, **Mensal**, **Trimestral**, **Anual** ou **Semestral**. Use o controle deslizante **Duração** ou caixa de texto para definir por quantos dias cada revisão da série recorrente será aberta para entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **Final** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou após a conclusão de um número definido de ocorrências. Você, outro usuário administrador ou outro administrador global pode interromper a série após a criação, alterando a data em **Configurações** para que ela encerre nessa data.

1. Na seção **Usuários**, selecione uma ou mais funções das quais você deseja examinar a associação.

    ![Escopo dos usuários dos quais examinar a associação](./media/pim-resource-roles-start-access-review/users.png)

    > [!NOTE]
    > - As funções selecionadas aqui incluem [funções permanentes e qualificadas](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - A seleção de mais de uma função criará várias revisões de acesso. Por exemplo, a seleção de cinco funções criará cinco revisões de acesso separadas.
    Se você estiver criando uma revisão de acesso de **funções do Azure AD**, a imagem a seguir mostra um exemplo da lista Examinar associação.

    ![Examine o painel de associação listando as funções do Azure AD que você pode selecionar](./media/pim-resource-roles-start-access-review/review-membership.png)

    Se você estiver criando uma revisão de acesso de **funções de recurso do Azure**, a imagem a seguir mostra um exemplo da lista Examinar associação.

    ![Examine o painel de associação listando as funções de recurso do Azure que você pode selecionar](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. Na seção **Revisores**, selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos.

    ![Lista de revisores de usuários ou membros selecionados (próprio)](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **Usuários selecionados** – Use essa opção quando você não souber quem precisa de acesso. Com essa opção, você pode atribuir a revisão a um proprietário de recurso ou ao gerente do grupo para conclusão.
    - **Membros (próprio)** – Use essa opção para fazer com que os usuários examinem suas próprias atribuições de função. 
    - **Gerenciador** – Use essa opção para fazer com que o gerente do usuário examine sua atribuição de função. Após selecionar o Gerenciador, você também terá a opção de especificar um revisor de fallback. Os revisores de fallback são solicitados a revisar um usuário quando o usuário não tem nenhum Gerenciador especificado no diretório. 

### <a name="upon-completion-settings"></a>Após configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **Após configurações de conclusão**.

    ![Após configurações de conclusão para aplicação automática e Se o revisor não responder](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. Se você quiser remover automaticamente o acesso para usuários que foram negados, defina **Resultados de aplicação automática ao recurso** para **Habilitar**. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para **Desabilitar**.

1. Use a lista **Se o revisor não responder** para especificar o que acontece para usuários que não foram examinados pelo revisor dentro do período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

    - **Nenhuma alteração** - deixar o acesso do usuário inalterado
    - **Remover o acesso** - remover o acesso do usuário
    - **Aprovar o acesso** - aprovar o acesso do usuário
    - **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

### <a name="advanced-settings"></a>Configurações avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas**.

    ![Configurações avançadas para mostrar recomendações, requer motivo sob aprovação, notificações por email e lembretes](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. Definir **Mostrar recomendações** à **Habilitar** para mostrar aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Definir **Requer motivo sob aprovação** para **Habilitar** para exigir que o revisor forneça um motivo para aprovação.

1. Definir **Notificações por email** para **Habilitar** para que o Azure Active Directory envie notificações por email para os revisores quando uma revisão de acesso começar e para os administradores quando uma revisão terminar.

1. Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise.
1. O conteúdo do email enviado aos revisores é gerado automaticamente com base nos detalhes da revisão, como nome da revisão, nome do recurso, data de vencimento, etc. Se você precisar de uma maneira de comunicar informações adicionais, como instruções adicionais ou informações de contato, poderá especificar esses detalhes no **conteúdo adicional do email do revisor** que será incluído nos emails de convite e lembrete enviados aos revisores atribuídos. A seção realçada abaixo é onde essas informações serão exibidas.

    ![Conteúdo do email enviado aos revisores com destaques](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso mostrando o status da revisão iniciada](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar as instruções sobre como [revisar o acesso às funções de recurso do Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Você pode acompanhar o progresso à medida que os revisores concluírem suas revisões na página **visão geral** da revisão de acesso. Nenhum direito de acesso é alterado no diretório até que a [revisão seja concluída](pim-resource-roles-complete-access-review.md).

![Página Visão geral das revisões de acesso mostrando os detalhes da revisão](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se esta for uma revisão única, depois que o período de revisão de acesso for concluído ou o administrador parar a revisão de acesso, siga as etapas em [concluir uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-complete-access-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de revisões de acesso, navegue até a revisão de acesso e você encontrará ocorrências futuras nas revisões agendadas e edite a data de término ou adicione/remova revisores adequadamente.

Com base em suas seleções nas **configurações de conclusão**, a aplicação automática será executada após a data de término da revisão ou quando você interromper manualmente a revisão. O status da revisão será alterado de **concluído** por meio de Estados intermediários, como **aplicar** e, por fim, o estado **aplicado**. Você deve esperar que os usuários negados, se houver, sejam removidos das funções em alguns minutos.

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso às funções de recurso do Azure](pim-resource-roles-perform-access-review.md)
- [Concluir uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-complete-access-review.md)
- [Criar uma revisão de acesso das funções do Azure AD](pim-how-to-start-security-review.md)
