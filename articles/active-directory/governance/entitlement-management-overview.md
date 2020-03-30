---
title: O que é gerenciamento de direitos? – Azure AD
description: Obtenha uma visão geral do gerenciamento de direitos do Azure Active Directory e como você pode usá-lo para gerenciar o acesso a grupos, aplicativos e sites SharePoint Online para usuários internos e externos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3deb87fec8241ad6126314f3f6ce5fb9600ad1fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128557"
---
# <a name="what-is-azure-ad-entitlement-management"></a>O que é o gerenciamento de direitos do Azure AD?

O gerenciamento de direitos do Azure Active Directory (Azure AD) é um recurso [de governança de identidade](identity-governance-overview.md) que permite que as organizações gerenciem identidade e acessem o ciclo de vida em escala, automatizando fluxos de trabalho de solicitação de acesso, atribuições de acesso, revisões e expiração.

Os funcionários das organizações precisam ter acesso a vários grupos, aplicativos e sites para realizar seu trabalho. Gerenciar esse acesso é um desafio, pois os requisitos mudam - novos aplicativos são adicionados ou os usuários precisam de direitos adicionais de acesso.  Esse cenário fica mais complicado quando você colabora com organizações externas - você pode não saber quem na outra organização precisa de acesso aos recursos da sua organização, e eles não saberão quais aplicativos, grupos ou sites sua organização está usando.

O gerenciamento de direitos do Azure AD pode ajudá-lo a gerenciar de forma mais eficiente o acesso a grupos, aplicativos e sites SharePoint Online para usuários internos, e também para usuários fora da sua organização que precisam de acesso a esses recursos.

## <a name="why-use-entitlement-management"></a>Por que usar a gestão de direitos?

As organizações corporativas geralmente enfrentam desafios ao gerenciar o acesso dos funcionários a recursos como:

- Os usuários podem não saber que acesso devem ter, e mesmo que o façam, podem ter dificuldade em localizar os indivíduos certos para aprovar seu acesso
- Uma vez que os usuários encontrem e recebam acesso a um recurso, eles podem manter o acesso por mais tempo do que o necessário para fins comerciais

Esses problemas são agravados para usuários que precisam de acesso de outra organização, como usuários externos que são de organizações da cadeia de suprimentos ou outros parceiros de negócios. Por exemplo: 

- Ninguém pode não conhecer todos os indivíduos específicos nos diretórios de outra suiça para poder convidá-los
- Mesmo que eles fossem capazes de convidar esses usuários, ninguém nessa organização pode se lembrar de gerenciar todo o acesso do usuário de forma consistente

A gestão de direitos azure AD pode ajudar a enfrentar esses desafios.  Para saber mais sobre como os clientes têm usado o gerenciamento de direitos Azure AD, você pode ler o [estudo de caso avanade](https://aka.ms/AvanadeELMCase) e o [estudo de caso Centrica](https://aka.ms/CentricaELMCase).  Este vídeo fornece uma visão geral da gestão de direitos e seu valor:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com a gestão de direitos?

Aqui estão algumas das capacidades de gerenciamento de direitos:

- Delegar aos não-administradores a capacidade de criar pacotes de acesso. Esses pacotes de acesso contêm recursos que os usuários podem solicitar, e os gerentes de pacotes de acesso delegadopodem definir políticas com regras para as quais os usuários podem solicitar, quem deve aprovar seu acesso e quando o acesso expira.
- Selecione organizações conectadas cujos usuários podem solicitar acesso.  Quando um usuário que ainda não está em seu diretório solicita acesso e é aprovado, ele é automaticamente convidado para o seu diretório e atribui acesso.  Quando seu acesso expirar, se eles não tiverem outras atribuições de pacote de acesso, sua conta B2B em seu diretório pode ser removida automaticamente.

Você pode começar com o nosso [tutorial para criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md). Você também pode ler os [cenários comuns,](entitlement-management-scenarios.md)ou assistir vídeos, incluindo

- [Como implantar o gerenciamento de direitos AD do Azure em sua organização](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Como monitorar e dimensionar o uso do gerenciamento de direitos AD do Azure](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Como delegar na gestão de direitos](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Quais são os pacotes de acesso e quais recursos posso gerenciar com eles?

O gerenciamento de direitos introduz ao Azure AD o conceito de um pacote de *acesso.* Um pacote de acesso é um pacote de todos os recursos com o acesso que um usuário precisa para trabalhar em um projeto ou executar sua tarefa. Os pacotes de acesso são usados para reger o acesso de seus funcionários internos e também usuários fora da sua organização.

 Aqui estão os tipos de recursos aos os que você pode gerenciar o acesso do usuário com o gerenciamento de direitos:

- Adesão a grupos de segurança Azure AD
- Adesão ao Office 365 Grupos e Equipes
- Atribuição aos aplicativos corporativos do Azure AD, incluindo aplicativos SaaS e aplicativos personalizados que suportam a entrada e/ou provisionamento da federação/único
- Adesão aos sites sharepoint online

Você também pode controlar o acesso a outros recursos que dependem de grupos de segurança Azure AD ou Grupos office 365.  Por exemplo: 

- Você pode dar aos usuários licenças para o Microsoft Office 365 usando um grupo de segurança Azure AD em um pacote de acesso e configurando [licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para esse grupo
- Você pode dar aos usuários acesso para gerenciar recursos do Azure usando um grupo de segurança Azure AD em um pacote de acesso e criando uma [atribuição de função Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo

## <a name="how-do-i-control-who-gets-access"></a>Como eu controlo quem tem acesso?

Com um pacote de acesso, um gerenciador de pacotes de acesso delegado ou administrador lista os recursos (grupos, aplicativos e sites) e as funções que os usuários precisam para esses recursos.

Os pacotes de acesso também incluem uma ou mais *políticas*. Uma política define as regras ou guardrails para atribuição de acesso ao pacote. Cada política pode ser usada para garantir que apenas os usuários apropriados possam solicitar acesso, que haja aprovadores para sua solicitação e que seu acesso a esses recursos seja limitado por tempo e expirará se não for renovado.

![Pacote de acesso e políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Dentro de cada diretiva, um administrador ou gerenciador de pacotes de acesso define

- Tanto os usuários já existentes (normalmente funcionários ou convidados já convidados), ou as organizações parceiras de usuários externos, que são elegíveis para solicitar acesso
- O processo de aprovação e os usuários que podem aprovar ou negar acesso
- A duração da atribuição de acesso de um usuário, uma vez aprovada, antes da atribuição expirar

O diagrama a seguir mostra um exemplo dos diferentes elementos na gestão de direitos. Ele mostra um catálogo com dois pacotes de acesso de exemplo.

- **O pacote de acesso 1** inclui um único grupo como recurso. O acesso é definido com uma diretiva que permite que um conjunto de usuários no diretório solicite acesso.
- **O pacote de acesso 2** inclui um grupo, um aplicativo e um site SharePoint Online como recursos. O acesso é definido com duas políticas diferentes. A primeira diretiva permite que um conjunto de usuários no diretório solicite acesso. A segunda diretiva permite que os usuários em um diretório externo solicitem acesso.

![Visão geral do gerenciamento de direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando devo usar pacotes de acesso?

Os pacotes de acesso não substituem outros mecanismos para atribuição de acesso.  São mais apropriados em situações como as seguintes:

- Os funcionários precisam de acesso limitado por tempo para uma determinada tarefa.  Por exemplo, você pode usar o licenciamento baseado em grupo e um grupo dinâmico para garantir que todos os funcionários tenham uma caixa de correio Exchange Online e, em seguida, usar pacotes de acesso para situações em que os funcionários precisam de acesso adicional, como ler recursos departamentais de outro Departamento.
- O acesso precisa ser aprovado pelo gerente de um funcionário ou por outros indivíduos designados.
- Os departamentos desejam gerenciar suas próprias políticas de acesso para seus recursos sem envolvimento em TI.  
- Duas ou mais organizações estão colaborando em um projeto e, como resultado, vários usuários de uma organização precisarão ser trazidos via Azure AD B2B para acessar os recursos de outra organização.

## <a name="how-do-i-delegate-access"></a>Como delego o acesso?

 Pacotes de acesso são definidos em contêineres chamados *catálogos*.  Você pode ter um único catálogo para todos os seus pacotes de acesso, ou pode designar indivíduos para criar e possuir seus próprios catálogos. Um administrador pode adicionar recursos a qualquer catálogo, mas um não-administrador só pode adicionar a um catálogo os recursos que possui. Um proprietário de catálogo pode adicionar outros usuários como co-proprietários de catálogo, ou como gerentes de pacotes de acesso.  Esses cenários são descritos ainda mais na delegação do artigo [e funções na gestão de direitos azure AD](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Resumo da terminologia

Para entender melhor a gestão de direitos e sua documentação, você pode consultar a seguinte lista de termos.

| Termo | Descrição |
| --- | --- |
| pacote de acesso | Um conjunto de recursos que uma equipe ou projeto precisa e é regido com políticas. Um pacote de acesso está sempre contido em um catálogo. Você criaria um novo pacote de acesso para um cenário no qual os usuários precisam solicitar acesso.  |
| solicitação de acesso | Uma solicitação para acessar os recursos em um pacote de acesso. Uma solicitação normalmente passa por um fluxo de trabalho de aprovação.  Se aprovado, o usuário solicitante recebe uma atribuição de pacote de acesso. |
| atribuição | Uma atribuição de um pacote de acesso a um usuário garante que o usuário tenha todas as funções de recurso desse pacote de acesso.  As atribuições do pacote de acesso normalmente têm um limite de tempo antes de expirarem. |
| catálogo | Um contêiner de recursos relacionados e pacotes de acesso.  Os catálogos são usados para delegação, para que os não-administradores possam criar seus próprios pacotes de acesso. Os proprietários de catálogos podem adicionar recursos que possuem a um catálogo. |
| criador de catálogo | Uma coleção de usuários autorizados a criar novos catálogos.  Quando um usuário não administrador que está autorizado a ser um criador de catálogo cria um novo catálogo, ele automaticamente se torna o proprietário desse catálogo. |
| organização conectada | Um diretório ou domínio Azure AD externo com o que você tem um relacionamento. Os usuários de uma organização conectada podem ser especificados em uma política como sendo permitido solicitar acesso. |
| policy | Um conjunto de regras que definem o ciclo de vida do acesso, como como os usuários têm acesso, quem pode aprovar e quanto tempo os usuários têm acesso através de uma atribuição. Uma política está vinculada a um pacote de acesso. Por exemplo, um pacote de acesso poderia ter duas políticas - uma para os funcionários solicitarem acesso e outra para usuários externos solicitarem acesso. |
| recurso | Um ativo, como um grupo office, um grupo de segurança, um aplicativo ou um site SharePoint Online, com uma função para a qual um usuário pode receber permissões. |
| diretório de recursos | Um diretório que tem um ou mais recursos para compartilhar. |
| função de recurso | Uma coleção de permissões associadas e definidas por um recurso. Um grupo tem duas funções - membro e proprietário. Os sites do SharePoint normalmente têm 3 funções, mas podem ter funções personalizadas adicionais. Os aplicativos podem ter funções personalizadas. |


## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como o Azure Government, a Azure Germany e a Azure China 21Vianet, não estão disponíveis no momento para uso.

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças você deve ter?

Certifique-se de que seu diretório tenha pelo menos tantas licenças Azure AD Premium P2 quanto você tem funcionários que estarão executando as seguintes tarefas:

- Usuários membros que **podem** solicitar um pacote de acesso.
- Usuários de membros e convidados que solicitam um pacote de acesso.
- Usuários de membros e convidados que aprovam solicitações para um pacote de acesso.

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para usuários com a função Administrador Global que configuram os catálogos iniciais, pacotes de acesso e políticas e delegar tarefas administrativas para outros usuários.
- Não são necessárias licenças para usuários que foram delegadas tarefas administrativas, como criador de catálogo, proprietário de catálogo e gerenciador de pacotes de acesso.
- Não são necessárias licenças para hóspedes que **podem** solicitar pacotes de acesso, mas **não** solicitar um pacote de acesso.

Para cada licença Azure AD Premium P2 paga que você compra para seus usuários membros (funcionários), você pode usar o Azure AD B2B para convidar até 5 usuários convidados. Esses usuários convidados também poderão usar os recursos do Azure AD Premium P2. Para obter mais informações, consulte [a orientação de licenciamento de colaboração Azure AD B2B](../b2b/licensing-guidance.md).

Para obter mais informações sobre licenças, consulte [Atribuir ou remover licenças usando o portal Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exemplos de cenários de licença

Aqui estão alguns exemplos de cenários de licenças para ajudá-lo a determinar o número de licenças que você deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador global do Woodgrove Bank cria catálogos iniciais e delega tarefas administrativas para 6 outros usuários. Uma das políticas especifica que **todos os funcionários** (2.000 funcionários) podem solicitar um conjunto específico de pacotes de acesso. 150 funcionários solicitam os pacotes de acesso. | 2.000 funcionários que **podem** solicitar os pacotes de acesso | 2.000 |
| Um administrador global do Woodgrove Bank cria catálogos iniciais e delega tarefas administrativas para 6 outros usuários. Uma das políticas especifica que **todos os funcionários** (2.000 funcionários) podem solicitar um conjunto específico de pacotes de acesso. Outra política especifica que alguns usuários **de Usuários do parceiro Contoso** (convidados) podem solicitar os mesmos pacotes de acesso sujeitos à aprovação. Contoso tem 30.000 usuários. 150 funcionários solicitam os pacotes de acesso e 10.500 usuários do Contoso solicitam acesso. | 2.000 funcionários + 500 usuários convidados de Contoso que excedem a proporção de 1:5 (10.500 - (2.000 * 5)) | 2.500 |

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Crie seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Cenários comuns](entitlement-management-scenarios.md)
