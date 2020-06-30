---
title: O que são revisões de acesso? - Active Directory do Azure | Microsoft Docs
description: Usando as revisões de acesso do Azure Active Directory, você pode controlar a associação a grupos e o acesso a aplicativos para atender às iniciativas de governança, gerenciamento de riscos e conformidade da sua organização.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a7cafbf8223c3d9e7641851d02f61bc2ad16ae
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078890"
---
# <a name="what-are-azure-ad-access-reviews"></a>Quais são as revisões de acesso do Azure AD?

As Revisões de Acesso do Microsoft Azure Active Directory (Azure AD) permitem que as organizações gerenciem com eficiência as associações a grupos, o acesso a aplicativos empresariais e atribuições de função. O acesso do usuário pode ser examinado regularmente para garantir que somente as pessoas corretas tenham acesso contínuo.

Aqui está um vídeo que fornece uma visão geral das revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que as revisão de acesso são importantes?

O Microsoft Azure Active Directory permite que você colabore internamente em sua organização e com os usuários de organizações externas, como parceiros. Os usuários podem ingressar em grupos, convidar pessoas, conectar-se aos aplicativos de nuvem e trabalhar remotamente com seus dispositivos pessoais ou de trabalho. A conveniência de aproveitar o poder do autoatendimento levou a uma necessidade de melhores recursos de gerenciamento de acesso.

- Conforme novos funcionários ingressam, como garantir que eles tenham o acesso correto para serem produtivos?
- Quando as pessoas mudam de equipes ou saem da empresa, como garantir que seu antigo acesso seja removido, especialmente quando envolve convidados?
- Direitos de acesso excessivos podem levar a conclusões e comprometimentos de auditoria pois indicam uma falta de controle de acesso.
- Você tem que se envolver proativamente com os proprietários do recurso para garantir que eles revisam regularmente quem tem acesso a seus recursos.

## <a name="when-to-use-access-reviews"></a>Quando usar as revisões de acesso?

- **Muitos usuários em funções com privilégios:** é recomendável verificar quantos usuários têm acesso administrativo, quantos deles são Administradores Globais e se há convidados ou parceiros que não foram removidos após serem atribuídos para executar uma tarefa administrativa. Você pode renovar os usuários de atribuição de função nas [funções do Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), como administradores globais ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), como o administrador de acesso do usuário na experiência do [Azure AD PIM (Privileged Identity Management)](../privileged-identity-management/pim-configure.md).
- **Quando a automação é inviável:** Você pode criar regras para associação dinâmica em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiverem no Azure AD ou se os usuários ainda precisarem de acesso após deixar o grupo para treinar a substituição deles? Em seguida, você pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é usado para uma nova finalidade:** Se você tiver um grupo que será sincronizado com o Azure AD, ou se planeja habilitar o aplicativo Salesforce para todos no grupo de equipe de Vendas, será útil solicitar ao proprietário do grupo para revisar a associação ao grupo antes que o grupo seja utilizado em um conteúdo de risco diferente.
- **Acesso a dados comercialmente críticos:** para certos recursos, talvez seja necessário pedir que as pessoas fora do departamento de TI desconectem-se regularmente e forneçam uma justificativa sobre por que eles precisam de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** em um mundo ideal, todos os usuários seguiriam as políticas de acesso para proteger o acesso aos recursos de sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Como o administrador de TI, você pode gerenciar essa tarefa, evitar supervisão de exceções à política e fornecer os auditores a comprovação de que essas exceções são revisadas regularmente.
- **Solicite aos proprietários do grupo para confirmar se ainda precisam de convidados em seus grupos:** o acesso dos funcionários pode ser automatizado com algum IAM local, mas não como convidados. Se um grupo oferece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo se confirmar os convidados ainda terão uma necessidade comercial legítima de acesso.
- **Realizar revisões periodicamente:** É possível configurar revisões de acesso recorrentes de usuários em frequências definidas como semanal, mensal, trimestral ou anual e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde você cria as revisões?

Dependendo do que você deseja revisar, você criará sua revisão de acesso nas revisões de acesso do Azure AD, nos aplicativos empresariais do Azure AD (em versão prévia) ou no Azure AD PIM.

| Direitos de acesso de usuários | Os revisores podem ser | Revisão criada em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo do escritório | Revisores especificados</br>Proprietários do grupo</br>Autorrevisão | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a um aplicativo conectado | Revisores especificados</br>Autorrevisão | Revisões de acesso do Azure AD</br>Aplicativos corporativos do Azure Active Directory (visualização) | Painel de acesso |
| Função do Azure AD | Revisores especificados</br>Autorrevisão | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Função de recurso do Azure | Revisores especificados</br>Autorrevisão | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |


## <a name="create-access-reviews"></a>Criar revisões de acesso

Para criar uma revisão de acesso, siga estas etapas:

1. Entrar no [portal do Azure](https://portal.azure.com) para gerenciar revisões de acesso e entrar como administrador global ou administrador de usuários.

1. Pesquise **Azure Active Directory** e selecione-o.

      ![Pesquisa do portal do Azure para o Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Selecione **Governança de Identidade**.

1. Na página de Introdução, clique no botão **Criar uma revisão de acesso**.

   ![Página inicial das revisões de acesso](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 

### <a name="creating-access-review-on-a-group-that-can-be-assigned-to-azure-ad-role"></a>Como criar uma revisão de acesso em um grupo que pode ser atribuído a uma função do Azure AD
Se você estiver na versão mais recente das Revisões de Acesso (os revisores são direcionados para **Meus Acessos** por padrão), somente o administrador global poderá criar a revisão de acesso em grupos atribuíveis a uma função. No entanto, se você estiver em uma versão mais antiga das Revisões de Acesso (os revisores são direcionados para o **Painel de Acesso** por padrão), o administrador global e o administrador do usuário poderão criar a revisão de acesso em grupos atribuíveis a uma função.  

A nova experiência será distribuída para todos os clientes em 1º de agosto de 2020, mas se você quiser atualizar mais cedo, faça uma solicitação aqui: [Revisões de Acesso do Azure AD – Inscrição para experiência de revisor atualizada nos Meus Acessos](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

[Saiba mais sobre como atribuir grupos a funções do Azure AD](https://go.microsoft.com/fwlink/?linkid=2103037).

## <a name="learn-about-access-reviews"></a>Saiba mais sobre revisões de acesso

Para saber mais sobre como criar e executar as revisões de acesso, assista a este breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se você estiver pronto para implantar as revisões de acesso em sua organização, siga estas etapas no vídeo para integrar, treinar seus administradores e criar sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças você precisa ter?

Verifique se o seu diretório tem um número de licenças do Azure AD Premium P2 igual ou superior ao número dos seus funcionários que executarão as seguintes tarefas:

- Usuários membros e convidados que são atribuídos como revisores
- Usuários membros e convidados que executam uma autorrevisão
- Proprietários de grupo que executam uma revisão de acesso
- Proprietários de aplicativo que executam uma revisão de acesso

As licenças do Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Nenhuma licença é necessária para os usuários com funções de administrador global ou de administrador de usuários que configuram revisões de acesso, definem configurações ou aplicam as decisões das revisões.

Para cada licença paga do Azure AD Premium P2 atribuída a um dos usuários de sua organização, você poderá usar o Azure AD B2B (entre empresas) para convidar até cinco usuários convidados sob a Provisão de Usuário Externo. Esses usuários convidados também poderão usar os recursos do Azure AD Premium P2. Para obter mais informações, confira [Diretrizes de licenciamento de colaboração B2B do Azure AD](../b2b/licensing-guidance.md).

Para obter mais informações sobre licenças, confira [Atribuir ou remover licenças usando o portal do Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns exemplos de cenários de licença para ajudá-lo a determinar o número de licenças que você precisa ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do grupo A com 75 usuários e um proprietário do grupo e atribui o proprietário do grupo como o revisor. | Uma licença para o proprietário do grupo como revisor | 1 |
| Um administrador cria uma revisão de acesso do grupo A com 500 usuários e três proprietários do grupo e atribui os três como revisores. | Três licenças para cada proprietário do grupo como revisores | 3 |
| Um administrador cria uma revisão de acesso do grupo B com 500 usuários. Ele a torna uma autorrevisão. | 500 licenças para cada usuário como autorrevisores | 500 |
| Um administrador cria uma revisão de acesso do grupo C com 50 usuários membros e 25 usuários convidados. Ele a torna uma autorrevisão. | 50 licenças para cada usuário como autorrevisores.<br/>(as licenças são atribuídas aos usuários convidados na proporção 1:5 necessária) | 50 |
| Um administrador cria uma revisão de acesso do grupo D com seis usuários membros e 108 usuários convidados. Ele a torna uma autorrevisão. | Seis licenças para cada usuário como autorrevisores + 16 licenças adicionais para atender a todos os 108 usuários convidados, na proporção 1:5 necessária. Seis licenças, que atendem a 6\*5 = 30 usuários convidados. Para os (108-6\*5) = 78 usuários convidados restantes, 78/5 = 16 licenças adicionais são necessárias. Portanto, no total, são necessárias 6 + 16 = 22 licenças. | 22 |

## <a name="next-steps"></a>Próximas etapas

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
