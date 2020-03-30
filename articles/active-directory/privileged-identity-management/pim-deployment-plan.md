---
title: Implantar o Privileged Identity Management (PIM) - Azure AD | Microsoft Docs
description: Descreve como planejar a implantação do Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025989"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Implantar o Azure AD Privileged Identity Management (PIM)

Este guia passo-a-passo descreve como planejar a implantação do PIM (Privileged Identity Management, gerenciamento de identidade privilegiada) na organização Azure Active Directory (Azure AD).

> [!TIP]
> Ao longo deste artigo, você verá itens marcados como:
> 
> :heavy_check_mark: **A Microsoft recomenda**
> 
> Essas são recomendações gerais e você só deverá implementá-las se elas se aplicarem às suas necessidades corporativas específicas.

## <a name="learn-about-privileged-identity-management"></a>Conheça o Gerenciamento de Identidade Privilegiada

O Azure AD Privileged Identity Management ajuda você a gerenciar funções administrativas privilegiadas em recursos azure AD, Azure e outros Serviços Online da Microsoft. Em um mundo onde identidades privilegiadas são atribuídas e esquecidas, o Privileged Identity Management fornece soluções como acesso just-in-time, fluxos de trabalho de aprovação de solicitação e revisões de acesso totalmente integradas para que você possa identificar, descobrir e prevenir mal-intencionados atividades de papéis privilegiados em tempo real. A implantação do Gerenciamento de Identidade Privilegiada para gerenciar suas funções privilegiadas em toda a sua organização reduzirá consideravelmente o risco, ao mesmo tempo em que surgirá insights valiosos sobre as atividades de suas funções privilegiadas.

### <a name="business-value-of-privileged-identity-management"></a>Valor de negócios da Gestão de Identidade Privilegiada

**Gerenciar riscos** - proteja sua organização aplicando o princípio do [acesso de privilégios mínimos](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e do acesso just-in-time. Ao minimizar o número de atribuições permanentes de usuários para funções com privilégios e impor aprovações e MFA para elevação, você poderá reduzir bastante os riscos de segurança relacionados ao acesso com privilégios em sua organização. A aplicação de privilégios mínimos e acesso just-in-time também permitirá que você visualize um histórico de acesso a funções com privilégios e rastreie problemas de segurança à medida que eles ocorrem.

**Controle de endereços e governança** - A implantação do Gerenciamento de Identidade Privilegiada cria um ambiente para a governança de identidade em curso. A elevação just-in-time de identidades privilegiadas fornece uma maneira de o Gerenciamento de Identidade Privilegiada acompanhar atividades de acesso privilegiado em sua organização. Você também poderá exibir e receber notificações de todas as atribuições de funções permanentes e qualificadas dentro de sua organização. Com a revisão de acesso, você pode auditar e remover regularmente identidades com privilégios desnecessárias e garantir que sua organização esteja em conformidade com os mais rigorosos padrões de identidade, acesso e segurança.

**Reduza custos** - Reduza os custos eliminando ineficiências, erros humanos e problemas de segurança, implantando o Gerenciamento de Identidade Privilegiada corretamente. O resultado líquido é uma redução de crimes cibernéticos associados a identidades com privilégios que são caras e difíceis de recuperar. O Gerenciamento de Identidade Privilegiado também ajudará sua organização a reduzir o custo associado à auditoria de informações de acesso quando se trata de cumprir regulamentos e normas.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](pim-configure.md).

### <a name="licensing-requirements"></a>Requisitos de licenciamento

Para usar o Gerenciamento de Identidade Privilegiada, seu diretório deve ter uma das seguintes licenças pagas ou de teste:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Para obter mais informações, consulte [os requisitos de licença para usar o Gerenciamento de Identidade Privilegiado](subscription-requirements.md).

### <a name="key-terminology"></a>Terminologia chave

| Termo ou conceito | Descrição |
| --- | --- |
| qualificado | Uma atribuição de função que requer que um usuário execute uma ou mais ações para usá-la. Se um usuário se qualificou para uma função, isso significa que ele poderá ativá-la quando precisar executar tarefas privilegiadas. Não há nenhuma diferença no modo de acesso concedido a uma pessoa com uma atribuição de função permanente em comparação com uma qualificada. A única diferença é que algumas pessoas não precisam desse acesso o tempo todo. |
| ativar | O processo de execução de uma ou mais ações a fim de usar uma função para a qual um usuário está qualificado. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados. |
| Acesso JIT (Just-In-Time) | Um modelo no qual os usuários recebem permissões temporárias para executar tarefas privilegiadas, o que impede que usuários mal-intencionados ou não autorizados obtenham acesso após a expiração das permissões. O acesso é concedido somente quando os usuários precisam dele. |
| princípio de acesso de privilégios mínimos | Uma prática de segurança recomendada na qual todos os usuários recebem apenas os privilégios mínimos necessários para realizar as tarefas que estão autorizados a executar. Essa prática minimiza o número de Administradores Globais usando funções de administrador específicas para determinados cenários. |

Para saber mais, confira a [Terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Visão geral de alto nível de como funciona o Gerenciamento de Identidade Privilegiada

1. O Gerenciamento de Identidade Privilegiado é configurado para que os usuários sejam elegíveis para funções privilegiadas.
1. Quando um usuário elegível precisa usar sua função privilegiada, ele ativa a função no Gerenciamento de Identidade Privilegiada.
1. Dependendo das configurações de Gerenciamento de Identidade Privilegiada configuradas para a função, o usuário deve concluir determinadas etapas (como realizar autenticação multifatorial, obter aprovação ou especificar um motivo.)
1. Depois que o usuário ativar sua função com sucesso, ele receberá a função por um período de tempo predefinido.
1. Os administradores podem visualizar um histórico de todas as atividades de Gerenciamento de Identidade Privilegiada no registro de auditoria. Eles também podem proteger ainda mais suas organizações AD do Azure e cumprir a conformidade usando recursos de Gerenciamento de Identidade Privilegiada, como avaliações de acesso e alertas.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Funções que podem ser gerenciadas pelo Gerenciamento de Identidade Privilegiada

**Funções Azure AD** – Essas funções estão todas no Azure Active Directory (como Administrador Global, Administrador de Câmbio e Administrador de Segurança). Você pode ler mais sobre as funções e suas funcionalidades em [Permissões da função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter ajuda sobre como determinar quais funções devem ser atribuídas aos administradores, confira [funções com menos privilégios por tarefa](../users-groups-roles/roles-delegate-by-task.md).

**Funções de recurso do Azure** - essas funções são vinculadas a um recurso, grupo de recursos, assinatura ou grupo de gerenciamento do Azure. O Gerenciamento de Identidade Privilegiado fornece acesso just-in-time a ambas as funções incorporadas, como Proprietário, Administrador de Acesso ao Usuário e Contribuinte, bem como [funções personalizadas](../../role-based-access-control/custom-roles.md). Para saber mais sobre funções de recurso do Azure, confira [Controle de acesso baseado em função (RBAC)](../../role-based-access-control/overview.md).

Para obter mais informações, consulte [Funções que você não pode gerenciar no Gerenciamento de Identidade Privilegiada](pim-roles.md).

## <a name="plan-your-deployment"></a>Planejar sua implantação

Esta seção se concentra no que você precisa fazer antes de implantar o Gerenciamento de Identidade Privilegiada em sua organização. É fundamental seguir as instruções e entender os conceitos nesta seção, pois eles orientarão você na criação do melhor plano personalizado para as identidades com privilégios de sua organização.

### <a name="identify-your-stakeholders"></a>Identificar os participantes

A seção a seguir ajuda você a identificar todos os participantes envolvidos no projeto e a necessidade de confirmar, revisar ou manter-se informado sobre o projeto. Ele inclui tabelas separadas para implantar o Gerenciamento de Identidade Privilegiada para funções AD do Azure e o Gerenciamento de Identidade Privilegiada para funções de recursos do Azure. Adicione os participantes à tabela a seguir, conforme apropriado para sua organização.

- SO = Confirmar este projeto
- R = Revisar este projeto e fornecer comentários
- I = Informado sobre este projeto

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Stakeholders: Gerenciamento de identidade privilegiado para funções ad do Azure

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e email | **Arquiteto de identidade ou de Administrador Global do Azure**<br/>Um representante da equipe de gerenciamento de identidade encarregado de definir como essa alteração está alinhada com a infraestrutura de gerenciamento de identidade principal em sua organização. | SO/R/I |
| Nome e email | **Proprietário do serviço / gerente de linha**<br/>Um representante dos proprietários de TI de um serviço ou um grupo de serviços. Eles são fundamentais na tomada de decisões e ajudam a implementar a Gestão privilegiada de Identidade para sua equipe. | SO/R/I |
| Nome e email | **Proprietário de segurança**<br/>Um representante da equipe de segurança que pode confirmar que o plano atende aos requisitos de segurança da organização. | SO/R |
| Nome e email | **Suporte técnico / gerente de suporte de TI**<br/>Um representante da organização de suporte de TI que pode fornecer informações sobre a capacidade de suporte dessa mudança a partir da perspectiva da assistência técnica. | R/I |
| Nome e email de usuários piloto | **Usuários de função com privilégios**<br/>O grupo de usuários para o qual o gerenciamento de identidades com privilégios é implementado. Eles precisarão saber como ativar suas funções assim que o Gerenciamento de Identidade Privilegiada for implementado. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Stakeholders: Gerenciamento de identidade privilegiado para funções de recursos do Azure

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e email | **Proprietário da assinatura / recurso**<br/>Um representante dos proprietários de TI de cada assinatura ou recurso que você deseja implantar o Gerenciamento de Identidade Privilegiada para | SO/R/I |
| Nome e email | **Proprietário de segurança**<br/>Um representante da equipe de segurança que pode confirmar que o plano atende aos requisitos de segurança da organização. | SO/R |
| Nome e email | **Suporte técnico / gerente de suporte de TI**<br/>Um representante da organização de suporte de TI que pode fornecer informações sobre a capacidade de suporte dessa mudança a partir da perspectiva da assistência técnica. | R/I |
| Nome e email de usuários piloto | **Usuários da função RBAC**<br/>O grupo de usuários para o qual o gerenciamento de identidades com privilégios é implementado. Eles precisarão saber como ativar suas funções assim que o Gerenciamento de Identidade Privilegiada for implementado. | I |

### <a name="enable-privileged-identity-management"></a>Habilitar o gerenciamento de identidade privilegiado

Como parte do processo de planejamento, você deve primeiro consentir e habilitar o Gerenciamento de Identidade Privilegiada, seguindo nosso início usando artigo [de Gerenciamento de Identidade Privilegiada.](pim-getting-started.md) A habilitação do Gerenciamento de Identidade Privilegiada dá acesso a alguns recursos que foram projetados especificamente para ajudar na sua implantação.

Se seu objetivo é implantar o Gerenciamento de Identidade Privilegiada para os recursos do Azure, você deve seguir nossos [recursos do Discover Azure para gerenciar no](pim-resource-roles-discover-resources.md) artigo de Gerenciamento de Identidade Privilegiado. Somente proprietários de assinaturas e grupos de gerenciamento podem descobrir e embarcar esses recursos no Gerenciamento de Identidade Privilegiada. Depois de ser embarcado, a funcionalidade do PIM está disponível para proprietários em todos os níveis, incluindo grupo de gerenciamento, assinatura, grupo de recursos e recursos. Se você é um administrador global tentando implantar o Gerenciamento de Identidade Privilegiada para seus recursos do Azure, você pode [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para dar a si mesmo acesso a todos os recursos do Azure no diretório de descobertas. No entanto, aconselhamos que você obtenha aprovação de cada um de seus proprietários de assinatura antes de gerenciar seus recursos com o Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Aplicar o princípio de privilégios mínimos

É importante ter certeza de que você aplicou o princípio de privilégios mínimos em sua organização para o Azure AD e suas funções de recurso do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

Para as funções do Azure AD, é comum que as organizações atribuam a função de Administrador Global a um número significativo de administradores, quando a maioria dos administradores precisa apenas de uma ou duas funções de administrador específicas. As atribuições de funções privilegiadas também tendem a ser deixadas sem gerenciamento.

> [!NOTE]
> Problemas comuns da delegação de funções:
>
> - O administrador responsável pelo Exchange recebe a função de Administrador Global, embora precise apenas da função de Administrador do Exchange para executar o trabalho diário.
> - A função de Administrador Global é atribuída a um administrador do Office porque o administrador precisa das funções de administrador de Segurança e do SharePoint e é mais fácil delegar apenas uma função.
> - O administrador recebeu uma função de Administrador de Segurança para executar uma tarefa há muito tempo, mas ela nunca foi removida.

Siga estas etapas para impor o Princípio de privilégios mínimos para suas funções do Azure AD.

1. Entenda a granularidade das funções lendo e compreendendo as [funções de administrador disponíveis do Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Você e sua equipe também devem fazer referência às [funções de administrador por tarefa de identidade no Azure AD](../users-groups-roles/roles-delegate-by-task.md), o que explica a função de privilégios mínimos de tarefas específicas.

1. Liste quem tem função com privilégios em sua organização. Você pode usar o [assistente de Gerenciamento de Identidade Privilegiado](pim-security-wizard.md#run-the-wizard) para chegar a uma página como a seguinte.

    ![Descubra papéis privilegiados mostrando quem tem papéis privilegiados](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Para todos os Administradores Globais da organização, descubra por que eles precisam da função. Com base na leitura da documentação anterior, se o trabalho da pessoa pode ser realizado por uma ou mais funções de administrador granular, você deve removê-las da função de Administrador Global e fazer atribuições de acordo dentro do Azure Active Directory (Como referência: Atualmente, a Microsoft possui apenas cerca de 10 administradores com a função de Administrador Global. Saiba mais sobre [como a Microsoft usa o Gerenciamento de Identidade Privilegiada](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Para todas as outras funções do Azure AD, examine a lista de atribuições, identifique os administradores que já não precisam da função e remova-os de suas atribuições.

Para automatizar as duas últimas etapas, você pode usar avaliações de acesso em Gerenciamento de Identidade Privilegiada. Seguindo as etapas de [iniciar uma revisão de acesso para as funções do Azure AD no Gerenciamento de Identidade Privilegiada,](pim-how-to-start-security-review.md)você pode configurar uma revisão de acesso para cada função Azure AD que tenha um ou mais membros.

![Crie um painel de revisão de acesso para funções azure AD](./media/pim-deployment-plan/create-access-review.png)

Os revisores deve ser definidos como **Membros (por conta própria)**. Isso enviará um email para todos os membros na função para que eles confirmem se precisam de acesso. Você também deve ativar **Requer motivo sob aprovação** nas configurações avançadas para que os usuários possam indicar por que precisam da função. Com base nessas informações, você poderá remover usuários de funções desnecessárias e delegar funções de administrador mais granulares no caso de Administradores Globais.

As revisões de acesso dependem de emails para notificar as pessoas sobre o acesso às funções. Se você tiver contas que não têm emails vinculados com privilégios, lembre-se de preencher o campo de email secundário nessas contas. Para saber mais, confira [Atributo proxyAddresses no Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Funções de recurso do Azure

Para assinaturas do Azure e recursos, você pode configurar um processo de revisão de acesso semelhante para revisar as funções em cada assinatura ou recurso. O objetivo desse processo é minimizar as atribuições de Administrador de Acesso do Proprietário e Usuário anexadas a cada assinatura ou recurso e, também, remover atribuições desnecessárias. No entanto, as organizações geralmente delegam essas tarefas ao proprietário de cada assinatura ou recurso porque eles têm um melhor entendimento de funções específicas (especialmente funções personalizadas).

Se você é um administrador de TI com a função de Administrador Global tentando implantar o Gerenciamento de Identidade Privilegiada para recursos do Azure em sua organização, você pode [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para ter acesso a cada assinatura. Em seguida, é possível localizar o proprietário de cada assinatura e trabalhar com ele para remover atribuições desnecessárias e minimizar a atribuição de funções do proprietário.

Os usuários com a função de proprietário de uma assinatura do Azure também podem utilizar [revisões de acesso para recursos do Azure](pim-resource-roles-start-access-review.md) para realizar a auditoria e remover atribuições de função desnecessárias semelhantes ao processo descrito anteriormente para as funções do Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Decida quais atribuições de função devem ser protegidas pelo Gerenciamento de Identidade Privilegiada

Depois de limpar as atribuições de funções privilegiadas em sua organização, você precisará decidir quais funções proteger com o Gerenciamento de Identidade Privilegiado.

Se uma função for protegida pelo Gerenciamento de Identidade Privilegiada, os usuários elegíveis atribuídos a ela devem elevar-se para usar os privilégios concedidos pela função. O processo de elevação também pode incluir a obtenção de aprovação, a execução da autenticação multifator e/ou o motivo pelo qual eles estão sendo ativados. O Gerenciamento de Identidade Privilegiado também pode rastrear elevações por meio de notificações e dos registros de eventos de auditoria do Azure AD e do Privileged.

Escolher quais funções proteger com o Gerenciamento de Identidade Privilegiada pode ser difícil e diferente para cada organização. Esta seção fornece nossos conselhos sobre práticas recomendadas para as funções de recurso do Azure AD e do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

É importante priorizar a proteção de funções do Azure AD que têm o maior número de permissões. Com base nos padrões de uso entre todos os clientes do Privileged Identity Management, as 10 principais funções Azure AD gerenciadas pelo Privileged Identity Management são:

1. Administrador global
1. Administrador de segurança
1. Administrador de usuários
1. Administrador do Exchange
1. Administrador do SharePoint
1. Administrador do Intune
1. Leitor de segurança
1. Administrador de serviço
1. Administrador de cobrança
1. Administrador do Skype for Business

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que você gerencie todos os seus administradores **globais** e administradores de segurança usando o Gerenciamento de Identidade Privilegiada como um primeiro passo, pois eles são os que podem fazer mais mal quando comprometidos.

É importante considerar quais dados e permissões são mais confidenciais para sua organização. Como exemplo, algumas organizações podem querer proteger sua função de administrador de POWER BI ou sua função de administrador de equipes usando o Gerenciamento de Identidade Privilegiada, pois eles têm a capacidade de acessar dados e/ou alterar os principais fluxos de trabalho.

Se houver funções com usuários convidados atribuídos, eles estarão particularmente vulneráveis a ataques.

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que você gerencie todas as funções com usuários **convidados** usando o Gerenciamento de Identidade Privilegiada para reduzir o risco associado a contas de usuários convidados comprometidas.

As funções de leitor, como o Leitor de diretório, o Leitor do centro de mensagens e o Leitor de segurança, são, às vezes, consideradas menos importantes em comparação com outras funções, pois não têm permissão de gravação. No entanto, vimos alguns clientes também protegerem essas funções porque os invasores que obtiveram acesso a essas contas podem ser capazes de ler dados confidenciais, como dados pessoais. Você deve levar isso em consideração ao decidir se as funções de leitor em sua organização precisam ser gerenciadas usando o Gerenciamento de Identidade Privilegiada.

#### <a name="azure-resource-roles"></a>Funções de recurso do Azure

Ao decidir quais atribuições de função devem ser gerenciadas usando o recurso Privileged Identity Management for Azure, você deve primeiro identificar as assinaturas/recursos mais vitais para sua organização. Exemplos dessas assinaturas/recursos são:

- Recursos que hospedam os dados mais confidenciais
- Recursos dos quais os principais aplicativos voltados ao cliente dependem

Se você é Administrador Global e tem problemas para decidir quais assinaturas/recursos são mais importantes, entre em contato com os proprietários da assinatura em sua organização para reunir uma lista de recursos gerenciados por cada assinatura. Em seguida, trabalhe com os proprietários da assinatura para agrupar os recursos com base no nível de gravidade, no caso de eles estarem comprometidos (baixo, médio, alto). Você deve priorizar o gerenciamento de recursos com o Gerenciamento de Identidade Privilegiada com base nesse nível de gravidade.

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que você trabalhe com **proprietários** de assinaturas/recursos de serviços críticos para configurar o fluxo de trabalho do Privileged Identity Management para todas as funções dentro de assinaturas/recursos sensíveis.

O gerenciamento de identidade privilegiado para recursos do Azure suporta contas de serviço vinculadas ao tempo. Você deve tratar as contas de serviço exatamente da mesma forma como você trataria uma conta de usuário normal.

Para assinaturas/recursos que não sejam tão críticos, você não precisará configurar o Gerenciamento de Identidade Privilegiada para todas as funções. No entanto, você ainda deve proteger as funções de Administrador de Acesso do Proprietário e do Usuário com gerenciamento de identidade privilegiado.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda que** você gerencie funções de proprietário e administrador de acesso ao usuário de todas as assinaturas/recursos usando o Gerenciamento de Identidade Privilegiado.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidir quais atribuições de função devem ser permanentes ou qualificáveis

Uma vez que você tenha decidido a lista de funções a serem gerenciadas pelo Gerenciamento de Identidade Privilegiada, você deve decidir quais usuários devem obter a função elegível versus a função permanentemente ativa. Funções permanentemente ativas são as funções normais atribuídas através dos recursos do Azure Active Directory e do Azure, enquanto as funções elegíveis só podem ser atribuídas no Gerenciamento de Identidade Privilegiada.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** que você tenha zero atribuições permanentemente ativas para as funções azure AD e a azure, além das duas contas de acesso de emergência de vidro de [quebra](../users-groups-roles/directory-emergency-access.md)recomendadas, que devem ter a função de administrador global permanente.

Apesar de recomendarmos um administrador permanente, às vezes é difícil para as organizações conseguirem isso imediatamente. Veja alguns pontos a considerar ao tomar esta decisão:

- Frequência de elevação - se o usuário precisar da atribuição com privilégios apenas uma vez, ele não deverá ter a atribuição permanente. Por outro lado, se o usuário precisa da função para o seu trabalho diário e usar a Gestão privilegiada de Identidade reduziria consideravelmente sua produtividade, eles podem ser considerados para o papel permanente.
- Casos específicos da organização - se a pessoa que recebe a função qualificada pertencer a uma equipe muito distante ou recebê-la de um executivo de alto escalão, a ponto de a comunicação e o cumprimento do processo de elevação serem difíceis, ela poderá ser considerada para a função permanente.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda que** você configure avaliações de acesso recorrentes para usuários com atribuições de função permanente (caso você tenha alguma). Saiba mais sobre a revisão de acesso recorrente na seção final deste plano de implantação

### <a name="draft-your-privileged-identity-management-settings"></a>Redigir suas configurações privilegiadas de gerenciamento de identidade

Antes de implementar sua solução de Gerenciamento de Identidade Privilegiada, é uma boa prática elaborar suas configurações privilegiadas de Gerenciamento de Identidade para cada função privilegiada que sua organização usa. Esta seção tem alguns exemplos de configurações privilegiadas de gerenciamento de identidade para funções específicas (elas são apenas para referência e podem ser diferentes para sua organização). Cada uma dessas configurações é explicada em detalhes com as recomendações da Microsoft após as tabelas.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Configurações privilegiadas de gerenciamento de identidade para funções Ad do Azure

| Função | Exigir MFA | Notification | Tíquete de incidente | Exigir aprovação | Aprovador | Duração da ativação | Administrador permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrador global | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros administradores globais | 1 hora | Contas de acesso de emergência |
| Administrador do Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Nenhum | 2 horas | Nenhum |
| Administrador de assistência técnica | :x: | :x: | :heavy_check_mark: | :x: | Nenhum | 8 horas | Nenhum |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Configurações privilegiadas de gerenciamento de identidade para funções de recursos do Azure

| Função | Exigir MFA | Notification | Exigir aprovação | Aprovador | Duração da ativação | Administradores ativos | Expiração ativa | Expiração qualificada |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietário de assinaturas críticas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros proprietários da assinatura | 1 hora | Nenhum | n/d | 3 meses |
| Administrador de Acesso do Usuário de assinaturas menos críticas | :heavy_check_mark: | :heavy_check_mark: | :x: | Nenhum | 1 hora | Nenhum | n/d | 3 meses |
| Colaborador de Máquina Virtual | :x: | :heavy_check_mark: | :x: | Nenhum | 3 horas | Nenhum | n/d | 6 meses |

A tabela a seguir descreve cada configuração.

| Configuração | Descrição |
| --- | --- |
| Função | Nome da função para a qual você está definindo as configurações. |
| Exigir MFA | Se o usuário qualificado precisa executar a MFA antes de ativar a função.<br/><br/> :heavy_check_mark: **A Microsoft recomenda que** você imponha o MFA para todas as funções de administrador, especialmente se as funções tiverem usuários convidados. |
| Notification | Se configurado como true, o Administrador Global, o Administrador de Função com Privilégios e o Administrador de Segurança da organização receberão uma notificação por email quando um usuário qualificado ativar a função.<br/><br/>**Nota:** Algumas organizações não têm um endereço de e-mail vinculado às suas contas de administrador, para receber essas notificações de e-mail, você deve definir um endereço de e-mail alternativo para que os administradores recebam esses e-mails. |
| Tíquete de incidente | Se o usuário qualificado precisa registrar um número de tíquete de incidente ao ativar sua função. Essa configuração ajuda uma organização a identificar cada ativação com um número de incidente interno para atenuar ativações indesejadas.<br/><br/> :heavy_check_mark: **A Microsoft recomenda** aproveitar os números de bilhetes de incidente para vincular o Gerenciamento de Identidade Privilegiada ao seu sistema interno. Isso é particularmente útil para os aprovadores que precisam de contexto para a ativação. |
| Exigir aprovação | Se o usuário qualificado precisa obter aprovação para ativar a função.<br/><br/> :heavy_check_mark: A **Microsoft recomenda que** você configure a aprovação para funções com mais permissão. Com base nos padrões de uso de todos os clientes de Gerenciamento de Identidade Privilegiado, Administrador Global, Administrador de Usuário, Administrador de Câmbio, Administrador de Segurança e Administrador de Senhas são as funções mais comuns com configuração de aprovação. |
| Aprovador | Se a aprovação for necessária para ativar a função qualificada, liste as pessoas que deverão aprovar a solicitação. Por padrão, o Privileged Identity Management define o aprovador como sendo todos os usuários que são um administrador de funções privilegiado, sejam eles permanentes ou elegíveis.<br/><br/>**Nota:** Se um usuário for elegível para uma função Azure AD e um aprovador da função, ele não será capaz de aprovar a si mesmo.<br/><br/> :heavy_check_mark: A Microsoft recomenda que você escolha os aprovadores para serem aqueles que têm mais conhecimento sobre a função específica e seus usuários **frequentes,** em vez de um Administrador Global. |
| Duração da ativação | O período de tempo que um usuário será ativado na função antes da expiração. |
| Administrador permanente | Lista de usuários que serão administradores permanentes da função (nunca precisarão ser ativados).<br/><br/> :heavy_check_mark: **A Microsoft recomenda** que você tenha zero administrador permanente para todas as funções, exceto para administradores globais. Leia mais sobre isso na seção deste plano sobre quem deve ser qualificado e quem deve estar permanentemente ativo. |
| Administradores ativos | Para recursos do Azure, o administrador ativo é a lista de usuários que nunca precisarão ser ativados para usar a função. Isso não é chamado de administrador permanente, como nas funções do Azure AD, porque você pode definir um tempo de expiração para quando o usuário perder essa função. |
| Expiração ativa | Uma atribuição de função ativa para funções de recurso do Azure expira após esse período de tempo definido. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente ativa. |
| Expiração qualificada | Uma atribuição de função qualificada para funções de recurso do Azure expira após esse período de tempo definido. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente qualificada. |

## <a name="implement-your-solution"></a>Implementar sua solução

A base do planejamento adequado é a base sobre a qual você pode implantar um aplicativo com êxito com o Azure Active Directory.  Ele fornece segurança e integração inteligentes que simplificam a integração, reduzindo o tempo para implantações eficazes.  Essa combinação garante que seu aplicativo seja integrado com facilidade, ao mesmo tempo que reduz o tempo de inatividade dos usuários finais.

### <a name="identify-test-users"></a>Identificar usuários de teste

Use esta seção para identificar um conjunto de usuários e/ou grupos de usuários para validar a implementação. Com base nas configurações escolhidas na seção de planejamento, identifique os usuários que você deseja testar para cada função.

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que você faça com que os proprietários de **serviços** de cada função Azure AD sejam os usuários de teste para que eles possam se familiarizar com o processo e se tornar um defensor interno do lançamento.

Nesta tabela, identifique os usuários de teste que verificarão se as configurações de cada função estão funcionando.

| Nome da função | Usuários de teste |
| --- | --- |
| &lt;Nome da função&gt; | &lt;Usuários para testar a função&gt; |
| &lt;Nome da função&gt; | &lt;Usuários para testar a função&gt; |

### <a name="test-implementation"></a>Implementação de teste

Agora que você identificou os usuários de teste, use esta etapa para configurar o Gerenciamento de Identidade Privilegiada para seus usuários de teste. Se sua organização quiser incorporar o fluxo de trabalho privileged Identity Management em seu próprio aplicativo interno em vez de usar o Gerenciamento de Identidade Privilegiada no portal Azure, todas as operações em Gerenciamento de Identidade Privilegiada também são suportadas por meio de nossa [API de gráficos](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configurar o gerenciamento de identidade privilegiada para funções Ad do Azure

1. [Configure as configurações de função Azure AD](pim-how-to-change-default-settings.md) com base no que você planejou.

1. Navegue até **Funções do Azure AD**, clique em **Funções** e escolha a função que você acabou de configurar.

1. Para o grupo de usuários de teste, se eles já forem administradores permanentes, você poderá torná-los qualificados pesquisando-os e convertendo-os de permanentes em qualificados clicando nas reticências da linha. Se eles ainda não tiverem as atribuições de função, [crie uma nova atribuição qualificada](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Repita as etapas de 1 a 3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar o link sobre a [ativação de sua função do Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Configurar o Gerenciamento de Identidade Privilegiada para funções de recurso do Azure

1. [Defina as configurações da função de recurso do Azure](pim-resource-roles-configure-role-settings.md) para uma função dentro de uma assinatura ou recurso que você deseja testar.

1. Navegue até **Recursos do Azure** da assinatura e clique em **Funções**, escolha a função que você acabou de configurar.

1. Para o grupo de usuários de teste, se eles já forem administradores ativos, você poderá torná-los qualificados pesquisando-os e [atualize sua atribuição de função](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se eles ainda não tiverem a função, você pode [atribuir uma nova função](pim-resource-roles-assign-roles.md#assign-a-role).

1. Repita as etapas de 1 a 3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar o link sobre a [ativação de sua função de recurso do Azure](pim-resource-roles-activate-your-roles.md).

Você deve usar este estágio para verificar se todas as configurações configuradas para as funções estão funcionando corretamente. Use a tabela a seguir para documentar seus testes. Você também deve usar esse estágio para otimizar a comunicação com os usuários afetados.

| Função | Comportamento esperado durante a ativação | Resultados reais |
| --- | --- | --- |
| Administrador global | (1) Exigir o MFA<br/>(2) Exigir aprovação<br/>(3) O aprovador recebe a notificação e pode aprová-la<br/>(4) A função expira após o horário predefinido |  |
| Proprietário da assinatura *X* | (1) Exigir o MFA<br/>(2) A atribuição qualificada expira após o período de tempo configurado |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Comunique o Gerenciamento de Identidade Privilegiada às partes interessadas afetadas

A implantação do Gerenciamento de Identidade Privilegiada introduzirá etapas adicionais para usuários de funções privilegiadas. Embora o Gerenciamento de Identidade Privilegiada reduza consideravelmente os problemas de segurança associados a identidades privilegiadas, a mudança precisa ser efetivamente comunicada antes da implantação em todo o inquilino. Dependendo do número de administradores afetados, as organizações geralmente optam por criar um documento interno, um vídeo ou um email sobre a alteração. Frequentemente incluídos nessas comunicações:

- O que é o PIM
- Qual é o benefício para a organização
- Quem será afetado
- Quando o PIM será implementado
- Quais etapas adicionais serão necessárias para que os usuários ativem suas funções
    - Você deve enviar links para a documentação:
    - [Ativar funções do Azure AD](pim-how-to-activate-role.md)
    - [Ativar funções de recurso do Azure](pim-resource-roles-activate-your-roles.md)
- Informações de contato ou link do suporte técnico para quaisquer problemas associados ao PIM

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda que** você configure tempo com sua equipe de suporte/helpdesk para levá-los ao fluxo de trabalho privileged Identity Management (se sua organização tiver uma equipe interna de suporte de TI). Forneça à equipe as documentações apropriadas e as informações de contato.

### <a name="move-to-production"></a>Mover para ambiente de produção

Uma vez que seus testes sejam concluídos e bem-sucedidos, mova o Gerenciamento de Identidade Privilegiada para a produção, repetindo todas as etapas nas fases de teste para todos os usuários de cada função que você definiu em sua configuração de Gerenciamento de Identidade Privilegiada. Para o Gerenciamento de Identidade Privilegiada para funções AD do Azure, as organizações geralmente testam e implementam o Gerenciamento de Identidade Privilegiada para Administradores Globais antes de testar e implementar o Gerenciamento de Identidade Privilegiada para outras funções. Enquanto isso, para o recurso do Azure, as organizações normalmente testam e implementam a assinatura do Privileged Identity Management one Azure por vez.

### <a name="in-the-case-a-rollback-is-needed"></a>No caso de uma reversão ser necessária

Se o Gerenciamento de Identidade Privilegiada não funcionou como desejado no ambiente de produção, as seguintes etapas de reversão podem ajudá-lo a reverter para um estado bom conhecido antes de configurar o Gerenciamento de Identidade Privilegiado:

#### <a name="azure-ad-roles"></a>Funções do Azure AD

1. Faça login no [portal Azure](https://portal.azure.com/).
1. Abra **o Azure AD Privileged Identity Management**.
1. Clique em **Funções do Azure AD** e, em seguida, clique em **Funções**.
1. Para cada função configurada, clique nas reticências (**...**) para todos os usuários com uma atribuição qualificada.
1. Clique na opção **Tornar permanente** para tornar a atribuição de função permanente.

#### <a name="azure-resource-roles"></a>Funções de recurso do Azure

1. Faça login no [portal Azure](https://portal.azure.com/).
1. Abra **o Azure AD Privileged Identity Management**.
1. Clique em **Recursos do Azure** e, em seguida, clique em uma assinatura ou recurso que deseja reverter.
1. Clique em **Funções**.
1. Para cada função configurada, clique nas reticências (**...**) para todos os usuários com uma atribuição qualificada.
1. Clique na opção **Tornar permanente** para tornar a atribuição de função permanente.

## <a name="next-steps-after-deploying"></a>Próximos passos após a implantação

A implantação com sucesso do Gerenciamento de Identidade Privilegiada na produção é um avanço significativo em termos de garantir as identidades privilegiadas da sua organização. Com a implantação do Privileged Identity Management, vêm recursos adicionais de Gerenciamento de Identidade Privilegiada que você deve usar para segurança e conformidade.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Use alertas de gerenciamento de identidade privilegiado para proteger seu acesso privilegiado

Você deve utilizar a funcionalidade de alerta incorporada do Privileged Identity Management para proteger melhor seu inquilino. Para saber mais, confira [Alertas de segurança](pim-how-to-configure-security-alerts.md#security-alerts). Esses alertas incluem: os administradores não estão usando funções privilegiadas, as funções estão sendo atribuídas fora do Gerenciamento de Identidade Privilegiada, as funções estão sendo ativadas com muita freqüência e mais. Para proteger totalmente sua organização, confira regularmente sua lista de alertas e corrija os problemas. Você pode exibir e corrigir os alertas da seguinte maneira:

1. Faça login no [portal Azure](https://portal.azure.com/).
1. Abra **o Azure AD Privileged Identity Management**.
1. Clique em **Funções do Azure AD** e, em seguida, clique em **Alertas**.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** que você lide com todos os alertas marcados com alta gravidade imediatamente. Para alertas de gravidade média e baixa, você deve se manter informado e fazer alterações se achar que existe uma ameaça à segurança.

Se algum dos alertas específicos não for útil ou não se aplicar à sua organização, você poderá descartar o alerta sempre que desejar na página de alertas. Sempre que desejar, você pode reverter esse descarte posteriormente na página de configurações do Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurar revisões de acesso recorrentes para auditar regularmente as identidades com privilégios da organização

As revisões de acesso são a melhor maneira de solicitar aos usuários designados com funções com privilégios ou revisores específicos se cada usuário precisa da identidade com privilégios. As revisões de acesso são ótimas se você quiser reduzir a superfície de ataque e manter a conformidade. Para saber mais sobre como iniciar uma revisão de acesso, confira as [revisões de acesso das funções do Azure AD](pim-how-to-start-security-review.md) e as [revisões de acesso de funções de recurso do Azure](pim-resource-roles-start-access-review.md). Para algumas organizações, a revisão periódica do acesso é necessária para manter a conformidade com as leis e regulamentações, enquanto para outras, a revisão de acesso é a melhor maneira de impor o princípio do privilégio mínimo em toda a organização.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda que** você configure avaliações trimestrais de acesso para todas as suas funções de recursos Do Azure AD e Azure.

Na maioria dos casos, o revisor das funções do Azure AD é o próprio usuário, enquanto o revisor das funções de recurso do Azure é o proprietário da assinatura na qual a função está. No entanto, geralmente é o caso em que as empresas têm contas com privilégios que não estão vinculadas ao endereço de email de uma pessoa em particular. Nesses casos, ninguém lê e analisa o acesso.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** que você adicione um endereço de e-mail secundário para todas as contas com atribuições de função privilegiadas que não estão vinculadas a um endereço de e-mail regularmente verificado

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Obter o máximo proveito de seu log de auditoria para aprimorar a segurança e a conformidade

O registro de auditoria é o lugar onde você pode se atualizar e estar em conformidade com os regulamentos. O Gerenciamento de Identidade Privilegiada atualmente armazena um histórico de 30 dias de todo o histórico da sua organização dentro de seu registro de auditoria, incluindo:

- Ativação/desativação de funções qualificadas
- Atividades de atribuição de papéis dentro e fora do Gerenciamento de Identidade Privilegiada
- Alterações nas configurações de funções
- Solicitar/aprovar/negar atividades para ativação de funções com configuração de aprovação
- Atualizar para alertas

Se você for um Administrador Global ou um administrador de função com privilégios, poderá acessar esses logs de auditoria. Para saber mais, confira o [histórico de auditoria para funções do Azure AD](pim-how-to-use-audit-log.md) e o [histórico de auditoria para funções de recurso do Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** que você tenha pelo menos um administrador lendo todos os eventos de auditoria semanalmente e exporte seus eventos de auditoria mensalmente.

Se você quiser armazenar automaticamente seus eventos de auditoria por um período maior de tempo, o registro de auditoria do Privileged Identity Management será automaticamente sincronizado nos registros de auditoria do [Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda que** você configure o monitoramento de log do [Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) para arquivar eventos de auditoria em uma conta de armazenamento do Azure para a necessidade de segurança e conformidade.
