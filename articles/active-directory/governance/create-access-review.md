---
title: Criar uma revisão de acesso de grupos & aplicativos-Azure AD
description: Saiba como criar uma revisão de acesso de membros do grupo ou acesso ao aplicativo em Azure Active Directory revisões de acesso.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18f0627b809f56b813052cc763e6ff961f31aa02
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697128"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos e aplicativos nas revisões de acesso do Azure AD

Acesso a grupos e aplicativos para funcionários e visitantes muda ao longo do tempo. Para reduzir os riscos associados às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar revisões de acesso para membros do grupo ou usuários atribuídos a um aplicativo. Se você precisar revisar rotineiramente o acesso, você também pode criar as revisões de acesso recorrentes. Para obter mais informações sobre esses cenários, consulte [Gerenciar acesso de usuário](manage-user-access-with-access-reviews.md) e [Gerenciar acesso de convidado](manage-guest-access-with-access-reviews.md).

Você pode assistir a um vídeo rápido falando sobre como habilitar as revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Este artigo descreve como criar uma ou mais revisões de acesso para membros do grupo ou acesso ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de usuário
- Apresentação Os proprietários de recursos de grupos de Microsoft 365 podem criar análises sobre os grupos de Microsoft 365 que eles possuem
- Apresentação Os proprietários de recursos dos grupos de segurança do Azure AD podem criar revisões nos grupos de segurança do Azure AD que eles possuem

Para obter mais informações, veja [Requisitos de licença](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais revisões de acesso

1. Entre no portal do Azure e abra a [página governança de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu à esquerda, clique em **revisões de acesso**.

1. Clique em **Nova análise de acesso** para criar uma nova revisão de acesso.

    ![Painel de revisões de acesso no controle de identidade](./media/create-access-review/access-reviews.png)

1. Nomeie a revisão de acesso. Opcionalmente, forneça uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - nome da revisão e descrição](./media/create-access-review/name-description.png)

1. Defina a **Data de início**. Por padrão, uma revisão de acesso ocorre uma vez, inicia na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

    ![Criar uma revisão de acesso - Data inicial e final](./media/create-access-review/start-end-dates.png)

1. Para fazer com que a revisão de acesso seja recorrente, altere a configuração de **frequência** de **uma vez** para **semanal**, **mensal**, **trimestral**, **semianual** ou **anualmente**. Use o controle deslizante **Duração** ou caixa de texto para definir por quantos dias cada revisão da série recorrente será aberta para entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **Final** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: 
    1. Ele é executado continuamente para iniciar revisões indefinidamente
    1. Até uma data específica,
    1. Até que um número definido de ocorrências tenha sido concluído. 
  
    Você, outro usuário administrador ou outro administrador global pode interromper a série após a criação, alterando a data em **Configurações** para que ela encerre nessa data.

1. Na seção **usuários** , especifique os usuários aos quais a revisão de acesso se aplica. As revisões de acesso podem ser de membros de um grupo ou usuários que foram atribuídos a um aplicativo. Você pode detalhar ainda mais o escopo da análise de acesso para examinar apenas os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de examinar todos os usuários que são membros ou que têm acesso ao aplicativo.

    ![Criar uma revisão de acesso - Usuários](./media/create-access-review/users.png)

1. Na seção **grupo** , selecione um ou mais grupos dos quais você gostaria de examinar a associação.

    > [!NOTE]
    > A seleção de mais de um grupo criará várias revisões de acesso. Por exemplo, a seleção de cinco grupos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar grupo](./media/create-access-review/select-group.png)

1. Na seção **aplicativos** (se você selecionou **atribuído a um aplicativo** na etapa 8), selecione os aplicativos para os quais deseja revisar o acesso.

    > [!NOTE]
    > A seleção de mais de um aplicativo criará várias revisões de acesso. Por exemplo, a seleção de cinco aplicativos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar aplicativo](./media/create-access-review/select-application.png)

1. Na seção **Revisores**, selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos. Se o recurso for um grupo, você pode pedir que a revisão seja realizada pelos proprietários de grupo. Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.

    ![Criar uma revisão de acesso - Revisores](./media/create-access-review/reviewers.png)

1. Na seção **Programas**, selecione o programa que você deseja usar. O **Programa Padrão** sempre está presente.

    ![Criar uma revisão de acesso - Programas](./media/create-access-review/programs.png)

    Você pode simplificar a coleta e o acompanhamento de revisões de acesso organizando-as em programas. Cada análise de acesso pode ser vinculada a um programa. Em seguida, ao preparar relatórios para um auditor, você poderá concentrar-se nas revisões de acesso no escopo de uma iniciativa específica. Os resultados de análise de programas e acesso são visíveis para os usuários no administrador global, administrador de usuários, administrador de segurança ou função de leitor de segurança.

    Para ver uma lista de programas, vá para a página revisões de acesso e selecione **Programas**. Se você estiver em uma função de administrador global ou de administrador de usuários, poderá criar programas adicionais. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios. Quando você não precisar mais de um programa e ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

### <a name="upon-completion-settings"></a>Após configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **Após configurações de conclusão**.

    ![Criar uma revisão de acesso-após as configurações de conclusão](./media/create-access-review/upon-completion-settings-new.png)

2. Se você quiser remover automaticamente o acesso para usuários negados, defina **auto aplicar resultados para recurso** a ser **habilitado**. Se você deseja aplicar manualmente os resultados quando a revisão for concluída, defina a opção para **Desabilitar**.

3. Use a lista **se os revisores não responderem** para especificar o que acontece para os usuários que não são revisados pelo revisor no período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar o acesso do usuário será removido.

    - **Nenhuma alteração** - deixar o acesso do usuário inalterado
    - **Remover o acesso** - remover o acesso do usuário
    - **Aprovar o acesso** - aprovar o acesso do usuário
    - **Fazer recomendações** - levar a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

    ![Criar uma revisão de acesso – configurações avançadas](./media/create-access-review/advanced-settings-preview-new.png)

4. Apresentação Use a ação a ser aplicada a usuários negados para especificar o que acontece com os usuários convidados se eles forem negados.
    - A **opção 1** removerá o acesso negado do usuário ao grupo ou aplicativo que está sendo revisado, ainda poderá entrar no locatário. 
    - A **opção 2** impedirá que os usuários negados se inscrevam no locatário, independentemente de terem acesso a outros recursos. Se houvesse um erro ou se um administrador decidir reabilitar o acesso de um, ele poderá fazer isso dentro de 30 dias depois que o usuário tiver sido desabilitado. Se não houver nenhuma ação executada nos usuários desabilitados, eles serão excluídos do locatário.

Para saber mais sobre as práticas recomendadas para remover usuários convidados que não têm mais acesso aos recursos em sua organização, leia o artigo intitulado [Use Azure ad Identity Governance para revisar e remover usuários externos que não têm mais acesso a recursos.](access-reviews-external-users.md)

>[!NOTE]
> A ação a ser aplicada a usuários negados só funcionará se você tiver definido anteriormente uma revisão para apenas os usuários convidados (consulte a seção **criar uma ou mais revisões de acesso** etapa 8)

### <a name="advanced-settings"></a>Configurações avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas**.

1. Definir **Mostrar recomendações** à **Habilitar** para mostrar aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Definir **Requer motivo sob aprovação** para **Habilitar** para exigir que o revisor forneça um motivo para aprovação.

1. Definir **Notificações por email** para **Habilitar** para que o Azure Active Directory envie notificações por email para os revisores quando uma revisão de acesso começar e para os administradores quando uma revisão terminar.

1. Defina **Lembretes** para **Habilitar** para que o Azure Active Directory envie lembretes de análises de acesso em andamento para os revisores que não concluíram a sua análise. 

    >[!NOTE]
    > Por padrão, o Azure AD envia automaticamente um lembrete na metade da data de término para os revisores que ainda não responderam

1. Apresentação O conteúdo do email enviado aos revisores é gerado automaticamente com base nos detalhes da revisão, como nome da revisão, nome do recurso, data de vencimento, etc. Se você precisar de uma maneira de comunicar informações adicionais, como instruções adicionais ou informações de contato, poderá especificar esses detalhes no **conteúdo adicional do email do revisor** que será incluído nos emails de convite e lembrete enviados aos revisores atribuídos. A seção realçada abaixo é onde essas informações serão exibidas.

    ![Examinar um acesso de usuários a um grupo](./media/create-access-review/review-users-access-group.png)

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso e seu status](./media/create-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar a eles as instruções de como [revisar o acesso a grupos ou aplicativos](perform-access-review.md). Se sua análise for para convidados para revisar seu próprio acesso, mostre-lhes as instruções de como [examinar o acesso a grupos ou aplicativos](review-your-access.md).

Se você tiver atribuído convidados como revisores e eles não tiverem aceitado o convite, eles não receberão um email das revisões de acesso, pois eles devem primeiro aceitar o convite antes da revisão.

## <a name="access-review-status-table"></a>Tabela de status de análise de acesso

| Status | Definição |
|--------|------------|
|NotStarted | A revisão foi criada, a descoberta de usuário está aguardando para ser iniciada. |
|Inicializando   | A descoberta de usuário está em andamento para identificar todos os usuários que fazem parte da revisão. |
|Iniciando | A revisão está sendo iniciada. Se as notificações por email estiverem habilitadas, os emails serão enviados aos revisores. |
|InProgress | A revisão foi iniciada. Se as notificações por email forem habilitadas, os emails serão enviados aos revisores. Os revisores podem enviar decisões até a data de vencimento. |
|Finaliza | A revisão está sendo concluída e os emails estão sendo enviados para o proprietário da revisão. |
|Revisão automática | A revisão está em um estágio de revisão do sistema. O sistema está gravando decisões para usuários que não foram revisados com base em recomendações ou decisões pré-configuradas. |
|Revisado automaticamente | As decisões foram registradas pelo sistema para todos os usuários que não foram revisados. A revisão está pronta para continuar a **aplicação** se a aplicação automática estiver habilitada. |
|Solicita | Não haverá nenhuma alteração no acesso para usuários que foram aprovados. |
|Aplicado | Os usuários negados, se houver algum, foram removidos do recurso ou diretório. |
|Com falha | A revisão não pôde ser executada. Esse erro pode estar relacionado à exclusão do locatário, uma alteração nas licenças ou outras alterações internas do locatário. |

## <a name="create-reviews-via-apis"></a>Criar revisões via APIs

Você também pode criar as revisões de acesso usando as APIs. O que você faz para gerenciar as revisões de acesso dos grupos e usuários do aplicativo no portal do Azure também pode ser feito usando as APIs do Microsoft Graph. Para obter mais informações, consulte a [referência da API de revisões de acesso do Azure ad](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter um exemplo de código, consulte o [exemplo de recuperação de revisões de acesso do Azure ad via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Examinar o acesso a grupos ou aplicativos](review-your-access.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)