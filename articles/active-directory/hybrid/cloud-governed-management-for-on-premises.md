---
title: Gerenciamento de nuvem azure para cargas de trabalho on-premises - Azure
description: Este tópico descreve o gerenciamento em nuvem para cargas de trabalho no local.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109514"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Como o Azure AD oferece gerenciamento em nuvem para cargas de trabalho on-premises

O Azure Active Directory (Azure AD) é uma solução abrangente de identidade como serviço (IDaaS) usada por milhões de organizações que abrangem todos os aspectos de identidade, gerenciamento de acesso e segurança. O Azure AD possui mais de um bilhão de identidades de usuários e ajuda os usuários a fazer login e acessar com segurança ambos:

* Recursos externos, como o Microsoft Office 365, o portal Azure e milhares de outros aplicativos de Software como Serviço (SaaS).
* Recursos internos, como aplicações na rede corporativa e intranet de uma organização, juntamente com quaisquer aplicações em nuvem desenvolvidas por essa organização.

As organizações podem usar o Azure AD se forem "nuvem pura", ou como uma implantação "híbrida", se tiverem cargas de trabalho no local. Uma implantação híbrida do Azure AD pode ser parte de uma estratégia para uma organização migrar seus ativos de TI para a nuvem, ou continuar a integrar a infra-estrutura local existente ao lado de novos serviços em nuvem.

Historicamente, as organizações "híbridas" têm visto o Azure AD como uma extensão de sua infra-estrutura local existente. Nessas implantações, a administração de governança de identidade no local, o Windows Server Active Directory ou outros sistemas de diretório interno, são os pontos de controle e os usuários e grupos são sincronizados desses sistemas para um diretório em nuvem, como o Azure AD. Uma vez que essas identidades estejam na nuvem, elas podem ser disponibilizadas para o Office 365, Azure e outras aplicações.

![Ciclo de vida de identidade](media/cloud-governed-management-for-on-premises//image1.png)

À medida que as organizações movem mais de sua infra-estrutura de TI junto com seus aplicativos para a nuvem, muitas estão procurando a segurança melhorada e recursos de gerenciamento simplificados do gerenciamento de identidade como um serviço. Os recursos IDaaS fornecidos em nuvem no Azure AD aceleram a transição para o gerenciamento regido por nuvem, fornecendo soluções e recursos que permitem que as organizações adotem e movam mais de seu gerenciamento de identidade a partir de locais tradicionais sistemas para Azure AD, enquanto continua a suportar aplicativos existentes, bem como novos.

Este artigo descreve a estratégia da Microsoft para iDaaS híbrido e descreve como as organizações podem usar o Azure AD para seus aplicativos existentes.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>A abordagem do Azure AD para o gerenciamento de identidade governado em nuvem

À medida que as organizações transitam para a nuvem, elas precisam de garantias de que têm controles sobre seu ambiente completo - mais segurança e mais visibilidade nas atividades, apoiadas pela automação e insights proativos. "**Gerenciamento governado em nuvem**" descreve como as organizações gerenciam e governam seus usuários, aplicativos, grupos e dispositivos da nuvem.

Neste mundo moderno, as organizações precisam ser capazes de gerenciar efetivamente em escala, devido à proliferação de aplicações SaaS e ao crescente papel da colaboração e identidades externas. O novo cenário de risco da nuvem significa que uma organização deve ser mais responsiva - um ator mal-intencionado que compromete um usuário em nuvem pode afetar aplicativos em nuvem e no local.

Em particular, as organizações híbridas precisam ser capazes de delegar e automatizar tarefas, o que historicamente a TI fazia manualmente. Para automatizar tarefas, eles precisam de APIs e processos que orqueiem o ciclo de vida dos diferentes recursos relacionados à identidade (usuários, grupos, aplicativos, dispositivos), para que possam delegar o gerenciamento diário desses recursos para mais indivíduos fora do equipe central de TI. O Azure AD aborda esses requisitos através do gerenciamento de contas de usuário e autenticação nativa para usuários sem exigir infra-estrutura de identidade no local. A não construção de infra-estrutura local pode beneficiar organizações que têm novas comunidades de usuários, como parceiros de negócios, que não se originaram em seu diretório local, mas cuja gestão de acesso é fundamental para alcançar resultados de negócios.

Além disso, a gestão não está completa sem governança --- e a governança neste novo mundo é uma parte integrada do sistema de identidade, em vez de um complemento. A governança de identidade dá às organizações a capacidade de gerenciar a identidade e o ciclo de vida de acesso entre funcionários, parceiros de negócios e fornecedores e serviços e aplicativos.

A incorporação da governança de identidade facilita a habilitação da organização para a transição para a gestão regida em nuvem, permite que a TI dimensione, resolva novos desafios com os hóspedes e forneça insights e automação mais profundos do que o que os clientes tinham com infra-estrutura no local. A governança neste novo mundo significa a capacidade de uma organização ter transparência, visibilidade e controles adequados sobre o acesso aos recursos dentro da organização. Com o Azure AD, as operações de segurança e as equipes de auditoria têm visibilidade sobre quem tem --- e quem deve ter - acesso a quais recursos na organização (em quais dispositivos), o que esses usuários estão fazendo com esse acesso, e se a organização tem e usa o devido controles para remover ou restringir o acesso de acordo com as políticas da empresa ou regulatórias.

O novo modelo de gestão beneficia as organizações com aplicativos SaaS e line-of-business (LOB), pois são mais facilmente capazes de gerenciar e garantir acesso a esses aplicativos. Ao integrar aplicativos com o Azure AD, as organizações poderão usar e gerenciar o acesso em nuvem e no local, originando identidades de forma consistente. O gerenciamento do ciclo de vida do aplicativo se torna mais automatizado, e o Azure AD fornece insights ricos sobre o uso de aplicativos que não eram facilmente alcançáveis no gerenciamento de identidade no local. Através do Ad Do Azure, grupos do Office 365 e recursos de autoatendimento de equipes, as organizações podem facilmente criar grupos para gerenciamento de acesso e colaboração e adicionar ou remover usuários na nuvem para permitir a colaboração e os requisitos de gerenciamento de acesso.

A seleção dos recursos azure AD certos para gerenciamento de regidos em nuvem depende dos aplicativos a serem usados e de como esses aplicativos serão integrados com o Azure AD. As seções a seguir descrevem as abordagens a serem tomadas para aplicativos integrados a DD e aplicativos que usam protocolos de federação (por exemplo, SAML, OAuth ou OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gerenciamento regido em nuvem para aplicativos integrados a Anúncios

O Azure AD melhora o gerenciamento de aplicativos integrados ao Active Directory de uma organização no local por meio de acesso remoto seguro e acesso condicional a esses aplicativos. Além disso, o Azure AD também fornece gerenciamento e gerenciamento de credenciais do ciclo de vida da conta para as contas AD existentes do usuário, incluindo:

* **Acesso remoto seguro e acesso condicional para aplicativos locais**

Para muitas organizações, o primeiro passo para gerenciar o acesso da nuvem para aplicativos web integrados à Web e remotas baseadas em desktop no local é implantar o proxy de [aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) na frente desses aplicativos para fornecer acesso remoto seguro.

Após um logon único para o Azure AD, os usuários podem acessar aplicativos locais e de nuvem por meio de uma URL externa ou um portal interno do aplicativo. Por exemplo, o Application Proxy fornece acesso remoto e logon único para desktop remoto, SharePoint, bem como aplicativos como Tableau e Qlik, e aplicativos de linha de negócios (LOB). Além disso, as políticas de Acesso Condicional podem incluir a exibição [dos termos de uso](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) e a garantia de que o usuário tenha [concordado com eles](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) antes de poder acessar um aplicativo.

![Arquitetura proxy de aplicativos](media/cloud-governed-management-for-on-premises/image2.png)

* **Gerenciamento automático do ciclo de vida para contas do Active Directory**

A governança de identidade ajuda as organizações a alcançar um equilíbrio entre *produtividade* --- a rapidez com que uma pessoa pode ter acesso aos recursos de que precisa, como quando entra na organização? --- e *segurança* --- como seu acesso deve mudar ao longo do tempo, como quando o status de emprego dessa pessoa muda? O gerenciamento do ciclo de vida de identidades é a base para governança de identidade, e governança efetiva em escala requer modernizar a infraestrutura de gerenciamento do ciclo de vida de identidades para aplicativos.

Para muitas organizações, o ciclo de vida da identidade dos funcionários está vinculado à representação desse usuário em um sistema de gestão de capital humano (HCM). Para as organizações que usam o Workday como seu sistema HCM, o Azure AD pode garantir que as contas de usuário em AD sejam [automaticamente provisionadas e desprovisionadas para trabalhadores em Jornada de Trabalho](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Isso leva a uma melhor produtividade do usuário por meio da automação de contas de direito de nascença e gerencia o risco, garantindo que o acesso ao aplicativo seja atualizado automaticamente quando um usuário muda de função ou deixa a organização. O plano de implantação de [provisionamento](https://aka.ms/WorkdayDeploymentPlan) de usuários orientado para o dia de trabalho é um guia passo-a-passo que orienta as organizações através da implementação de práticas recomendadas da solução workday to active directory user provisioning em um processo de cinco etapas.

O Azure AD Premium também inclui o Microsoft Identity Manager, que pode importar registros de outros sistemas HCM no local, incluindo SAP, Oracle eBusiness e Oracle PeopleSoft.

A colaboração entre empresas requer cada vez mais a concessão de acesso a pessoas fora da sua organização. A colaboração [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) permite que as organizações compartilhem seus aplicativos e serviços com segurança com usuários convidados e parceiros externos, mantendo o controle sobre seus próprios dados corporativos.

O Azure AD pode [criar automaticamente contas em AD para usuários convidados](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) conforme necessário, permitindo que os hóspedes de negócios acessem aplicativos integrados ao AD no local sem precisar de outra senha. As organizações podem configurar [políticas de autenticação multifatorial (MFA) para o usuário convidado para](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)que as verificações de MFA sejam feitas durante a autenticação do proxy do aplicativo. Além disso, quaisquer [avaliações de acesso](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) que são feitas em usuários B2B na nuvem se aplicam a usuários on-premises. Por exemplo, se o usuário da nuvem for excluído através de políticas de gerenciamento do ciclo de vida, o usuário local também será excluído.

**Gerenciamento de credenciais para contas do Diretório Ativo** A redefinição de senha de autoatendimento do Azure AD permite que os usuários que esqueceram suas senhas sejam reautenticadas e redefinissem suas senhas, com as senhas alteradas [gravadas no Active Directory no local.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) O processo de redefinição de senha também pode usar as políticas de senha do Active Directory no local: Quando um usuário redefine sua senha, é verificado para garantir que ele atenda à política de diretório ativo no local antes de comprometê-la a esse diretório. O plano de [implantação](https://aka.ms/deploymentplans/sspr) de redefinição de senha de autoatendimento descreve as melhores práticas para implementar a redefinição de senha de autoatendimento aos usuários através de experiências integradas pela Web e Windows.

![Arquitetura Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Finalmente, para as organizações que permitem que os usuários alterem suas senhas em AD, o AD pode ser configurado para usar a mesma política de senha que a organização está usando no Azure AD através do [recurso de proteção por senha do Azure AD,](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)atualmente em visualização pública.

Quando uma organização está pronta para mover um aplicativo integrado a DD para a nuvem, movendo o sistema operacional que hospeda o aplicativo para o Azure, o [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) fornece serviços de domínio compatíveis com AD (como a junta de domínio, a política de grupo, a autenticação LDAP e Kerberos/NTLM). O Azure AD Domain Services integra-se ao inquilino Azure AD existente da organização, possibilitando que os usuários entrem usando suas credenciais corporativas. Além disso, grupos e contas de usuário existentes podem ser usados para garantir o acesso aos recursos, garantindo um "levantamento e deslocamento" mais suave dos recursos locais aos serviços de infra-estrutura do Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gerenciamento regido em nuvem para aplicativos baseados em federação no local

Para uma organização que já usa um provedor de identidade no local, a movimentação de aplicativos para o Azure AD permite um acesso mais seguro e uma experiência administrativa mais fácil para o gerenciamento da federação. O Azure AD permite configurar controles de acesso granulares por aplicativo, incluindo a autenticação multifatorial do Azure, usando o Azure AD Conditional Access. O Azure AD suporta mais recursos, incluindo certificados de assinatura de token específicos do aplicativo e datas de validade de certificados configuráveis. Esses recursos, ferramentas e orientações permitem que as organizações aposentem seus provedores de identidade no local. A própria TI da Microsoft, por exemplo, transferiu 17.987 aplicativos dos Serviços internos de Federação de Diretórios Ativos (AD FS) da Microsoft para o Azure AD.

![Evolução do Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Para começar a migrar aplicativos federados para o Azure https://aka.ms/migrateapps AD como provedor de identidade, consulte que inclui links para:

* O white paper [Migrando suas aplicações para o Azure Active Directory](https://aka.ms/migrateapps/whitepaper), que apresenta os benefícios da migração e descreve como planejar a migração em quatro fases claramente delineadas: detecção, classificação, migração e gerenciamento contínuo. Você será guiado sobre como pensar sobre o processo e dividir seu projeto em peças fáceis de consumir. Ao longo do documento, haverá links para recursos importantes que irão ajudá-lo durante o processo.

* O guia de soluções [Migrando autenticação de aplicativos dos Serviços ativos da Federação de Diretórios para](https://aka.ms/migrateapps/adfssolutionguide) o Diretório Ativo do Azure explora com mais detalhes as mesmas quatro fases de planejamento e execução de um projeto de migração de aplicativos. Neste guia, você aprenderá como aplicar essas fases ao objetivo específico de mover um aplicativo dos Serviços de Federação de DiretórioS Ativos (AD FS) para o Azure AD.

* O Script de [prontidão de migração de serviços da Federação de Diretórioativo ativo](https://aka.ms/migrateapps/adfstools) pode ser executado em servidores ad FS (Active Directory Federation Services, serviços de federação de diretórios ativos) existentes para determinar a prontidão dos aplicativos para migração para o Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gerenciamento contínuo de acesso em aplicativos em nuvem e locais

As organizações precisam de um processo para gerenciar o acesso que é escalável. Os usuários continuam a acumular direitos de acesso e acabam com além do que foi inicialmente provisionado para eles. Além disso, as organizações empresariais precisam ser capazes de escalar de forma eficiente para desenvolver e impor políticas de acesso e controles de forma contínua.

Em geral, a IT delega as decisões de aprovação de acesso aos tomadores de decisão empresariais. Além disso, a IT pode envolver os próprios usuários. Por exemplo, os usuários que acessam os dados confidenciais do cliente no aplicativo de marketing da empresa na Europa precisam conhecer as políticas da empresa. Os usuários convidados também podem desconhecer os requisitos de tratamento de dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo do ciclo de vida de acesso através de tecnologias como [grupos dinâmicos,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)juntamente com o provisionamento do usuário para [aplicativos SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)ou aplicativos integrados usando o padrão [System for Cross-Domain Identity Management (SCIM).](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais.](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) Esses direitos de acesso podem então ser revisados regularmente usando [revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) recorrentes.

## <a name="future-directions"></a>Direções futuras

Em ambientes híbridos, a estratégia da Microsoft é permitir implantações onde a **nuvem é o plano de controle para identidade,** e diretórios locais e outros sistemas de identidade, como active directory e outros aplicativos locais, são o alvo para provisionar usuários com acesso. Essa estratégia continuará a garantir os direitos, identidades e acesso nesses aplicativos e cargas de trabalho que dependem deles. Neste estado final, as organizações poderão impulsionar a produtividade do usuário final inteiramente da nuvem.

![Arquitetura do Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como começar esta jornada, consulte os planos <https://aka.ms/deploymentplans> de implantação do Azure AD, localizados em . Eles fornecem orientação de ponta a ponta sobre como implantar recursos do Azure Active Directory (Azure AD). Cada plano explica o valor do negócio, as considerações de planejamento, o design e os procedimentos operacionais necessários para implementar com sucesso os recursos comuns do Azure AD. A Microsoft atualiza continuamente os planos de implantação com as melhores práticas aprendidas com as implantações dos clientes e outros feedbacks quando adicionamos novos recursos ao gerenciamento da nuvem com o Azure AD.
