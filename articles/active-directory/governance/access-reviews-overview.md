---
title: O que são revisões de acesso? - Active Directory do Azure | Microsoft Docs
description: Usando as avaliações de acesso ao Azure Active Directory, você pode controlar a adesão ao grupo e o acesso a aplicativos para atender às iniciativas de governança, gerenciamento de riscos e conformidade em sua organização.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262081"
---
# <a name="what-are-azure-ad-access-reviews"></a>Quais são as revisões de acesso do Azure AD?

As avaliações de acesso do Azure Active Directory (Azure AD) permitem que as organizações gerenciem eficientemente membros do grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser examinado regularmente para garantir que somente as pessoas corretas tenham acesso contínuo.

Aqui está um vídeo que fornece uma visão geral das revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que as revisão de acesso são importantes?

O Microsoft Azure Active Directory permite que você colabore internamente em sua organização e com os usuários de organizações externas, como parceiros. Os usuários podem ingressar em grupos, convidar pessoas, conectar-se aos aplicativos de nuvem e trabalhar remotamente com seus dispositivos pessoais ou de trabalho. A conveniência de aproveitar o poder do autoatendimento levou a uma necessidade de melhores recursos de gerenciamento de acesso.

- Conforme novos funcionários ingressam, como garantir que eles tenham o acesso correto para serem produtivos?
- Quando as pessoas mudam de equipes ou saem da empresa, como garantir que seu antigo acesso seja removido, especialmente quando envolve convidados?
- Direitos de acesso excessivos podem levar a conclusões e comprometimentos de auditoria pois indicam uma falta de controle de acesso.
- Você tem que se envolver proativamente com os proprietários do recurso para garantir que eles revisam regularmente quem tem acesso a seus recursos.

## <a name="when-to-use-access-reviews"></a>Quando usar as revisões de acesso?

- **Muitos usuários em funções privilegiadas:** É uma boa idéia verificar quantos usuários têm acesso administrativo, quantos deles são Administradores Globais e se há algum convidado ou parceiro que não tenha sido removido após ser designado para fazer uma tarefa administrativa. Você pode recertificar os usuários de atribuição de funções de atribuição de funções do [Azure AD,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como administradores globais ou [funções de recursos do Azure,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como administrador de acesso ao usuário na experiência [PIM (AD Privileged Identity Management, gerenciamento de identidade privilegiada) do Azure.](../privileged-identity-management/pim-configure.md)
- **Quando a automação é inviável:** Você pode criar regras para a adesão dinâmica em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estão no Azure AD ou se os usuários ainda precisam de acesso depois de deixar o grupo para treinar sua substituição? Em seguida, você pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é usado para uma nova finalidade:** se você tiver um grupo que será sincronizado ao Azure Active Directory, ou se você planeja habilitar o aplicativo Salesforce para qualquer pessoa no grupo de equipe de vendas, seria útil pedir ao proprietário do grupo para examinar a associação do grupo antes de o grupo ser usado em um conteúdo de risco diferente.
- **Acesso a dados críticos dos negócios:** para certos recursos, pode ser necessário pedir às pessoas fora da TI que assinem regularmente e dêem uma justificativa sobre por que precisam de acesso para fins de auditoria.
- **Para manter a lista de exceção de uma política:** Em um mundo ideal, todos os usuários seguiriam as políticas de acesso para garantir o acesso aos recursos da sua organização. No entanto, às vezes, há casos comerciais em que é necessário fazer exceções. Como o administrador de TI, você pode gerenciar essa tarefa, evitar supervisão de exceções à política e fornecer os auditores a comprovação de que essas exceções são revisadas regularmente.
- **Peça aos proprietários do grupo que confirmem que ainda precisam de convidados em seus grupos:** O acesso dos funcionários pode ser automatizado com alguns no local IAM, mas não convidados. Se um grupo oferece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo se confirmar os convidados ainda terão uma necessidade comercial legítima de acesso.
- **Realizar revisões periodicamente:** você pode configurar revisões de acesso recorrente de usuários no conjunto de frequências como mensal, trimestral de semanalmente, ou anualmente e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde você cria as revisões?

Dependendo do que você deseja analisar, você criará sua revisão de acesso em avaliações de acesso ao Azure AD, aplicativos corporativos Azure AD (em pré-visualização) ou Azure AD PIM.

| Direitos de acesso de usuários | Os revisores podem ser | Revisão criada em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo do escritório | Revisores especificados</br>Proprietários do grupo</br>Auto-revisão | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a um aplicativo conectado | Revisores especificados</br>Auto-revisão | Revisões de acesso do Azure AD</br>Aplicativos corporativos do Azure Active Directory (visualização) | Painel de acesso |
| Função de AD do Azure | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Função de recurso do Azure | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |


## <a name="create-access-reviews"></a>Criar avaliações de acesso

Para criar uma revisão de acesso, siga estas etapas:

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar avaliações de acesso e faça login como administrador global ou administrador de usuário.

1. Pesquise **Azure Active Directory** e selecione-o.

      ![Busca do portal Azure pelo Diretório Ativo do Azure](media/access-reviews-overview/search-azure-active-directory.png)

1. Selecione **Governança de Identidade**.

1. Na página Como iniciar, clique no botão Criar um botão **de revisão de acesso.**

   ![Comentários de acesso página inicial](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Conheça as avaliações de acesso

Para saber mais sobre como criar e executar as revisões de acesso, assista a este breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se você estiver pronto para implantar as revisões de acesso em sua organização, siga estas etapas no vídeo para integrar, treinar seus administradores e criar sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças você deve ter?

Certifique-se de que seu diretório tenha pelo menos tantas licenças Azure AD Premium P2 quanto você tem funcionários que estarão executando as seguintes tarefas:

- Usuários membros e convidados que são designados como revisores
- Usuários de membros e convidados que realizam uma auto-revisão
- Proprietários de grupos que realizam uma revisão de acesso
- Proprietários de aplicativos que realizam uma revisão de acesso

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para os usuários com as funções de Administrador Global ou Administrador de Usuário que configurem revisões de acesso, configurem configurações ou apliquem as decisões das avaliações.

Para cada licença Azure AD Premium P2 paga que você atribui a um dos usuários da sua própria organização, você pode usar o Azure AD business-to-business (B2B) para convidar até cinco usuários convidados a Mesa externa do usuário. Esses usuários convidados também poderão usar os recursos do Azure AD Premium P2. Para obter mais informações, consulte [a orientação de licenciamento de colaboração Azure AD B2B](../b2b/licensing-guidance.md).

Para obter mais informações sobre licenças, consulte [Atribuir ou remover licenças usando o portal Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exemplos de cenários de licença

Aqui estão alguns exemplos de cenários de licenças para ajudá-lo a determinar o número de licenças que você deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do Grupo A com 75 usuários e 1 proprietário do grupo, e atribui o proprietário do grupo como revisor. | 1 licença para o proprietário do grupo como revisor | 1 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 usuários e 3 proprietários de grupo, e atribui os 3 proprietários do grupo como revisores. | 3 licenças para cada proprietário de grupo como revisores | 3 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 usuários. Faz disso uma auto-revisão. | 500 licenças para cada usuário como auto-revisores | 500 |
| Um administrador cria uma revisão de acesso do Grupo C com 50 usuários membros e 25 usuários convidados. Faz disso uma auto-revisão. | 50 licenças para cada usuário como auto-revisores.<br/>(os usuários convidados estão cobertos na proporção 1:5 necessária) | 50 |
| Um administrador cria uma revisão de acesso do Grupo D com 6 usuários membros e 108 usuários convidados. Faz disso uma auto-revisão. | 6 licenças para cada usuário como auto-revisores + 16 licenças adicionais para cobrir todos os 108 usuários convidados na proporção de 1:5 necessária. 6 licenças, que\*cobrem 6 5=30 usuários convidados. Para os demais usuários\*(108-6 5)=78 usuários convidados, são necessárias 78/5=16 licenças adicionais. Assim, no total, são necessárias 6+16=22 licenças. | 22 |

## <a name="next-steps"></a>Próximas etapas

- [Crie uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Revisar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Complete uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
