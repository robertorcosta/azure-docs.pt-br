---
title: Cenários comuns de gerenciamento de aplicativos para Azure Active Directory | Microsoft Docs
description: Centralizar o gerenciamento de aplicativos com o Azure AD
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21caec50bf9c20ec60194976b1bfbf16e4815914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261033"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizar o gerenciamento de aplicativos com o Azure AD

Senhas, um pesadelo de ti e um problema para os funcionários em todo o mundo. É por isso que cada vez mais empresas estão ligando Azure Active Directory, a solução de gerenciamento de acesso e identidade da Microsoft para a nuvem e todos os outros recursos. Vá do aplicativo para o aplicativo sem precisar inserir uma senha para cada um. Passe do Outlook, para o workday, para a ADP o mais rápido possível para abri-los, com rapidez e segurança. Em seguida, colabore com parceiros e mesmo para outros fora da sua organização sem precisar chamá-lo. Além do mais, o Azure AD ajuda a gerenciar riscos ao proteger os aplicativos que você usa com coisas como autenticação multifator para verificar quem é, usando o aprendizado de máquina e inteligência de segurança adaptados continuamente para detectar entradas suspeitas, oferecendo acesso seguro aos aplicativos de que você precisa, onde quer que esteja. Ele não é apenas ótimo para os usuários, mas também para ele. Com as revisões de acesso just-in-time e um conjunto de governança de dimensionamento completo, o Azure AD ajuda você a manter a conformidade e também impor políticas. E obter isso, você pode até mesmo automatizar as contas de usuário de provisionamento, tornando o gerenciamento de acesso uma Breeze. Confira alguns dos cenários comuns que o cliente usa Azure Active Directory recursos de gerenciamento de aplicativos para o.

**Cenários comuns**


> [!div class="checklist"]
> * SSO para todos os seus aplicativos
> * Automatizar o provisionamento e o desprovisionamento 
> * Proteger seus aplicativos
> * Controlar o acesso aos seus aplicativos
> * Acesso seguro híbrido

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Cenário 1: configurar o SSO para todos os seus aplicativos

Não há mais gerenciamento de senha. Acesse com segurança todos os recursos de que você precisa com suas credenciais corporativas. 

|Recurso  | Descrição | Recomendação |
|---------|---------|---------|
|SSO|SSO Federado baseado em padrões usando padrões de indústria confiáveis.|Sempre use [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) para habilitar o SSO quando seu aplicativo oferecer suporte a ele.|
|Painel de acesso|Oferece a seus usuários um hub simples para descobrir e acessar todos os seus aplicativos. Habilite-os a serem mais produtivos com recursos de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciar o acesso a recursos em nome de outros.| Implante o [painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) em sua organização depois de integrar seus aplicativos com o Azure ad para sso.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Cenário 2: automatizar o provisionamento e o desprovisionamento 


A maioria dos aplicativos exige que um usuário seja provisionado no aplicativo antes de acessar os recursos de que precisam. O uso de arquivos CSV ou scripts complexos pode ser dispendioso e difícil de gerenciar. Além disso, os clientes precisam garantir que as contas sejam removidas quando alguém não precisar mais de acesso. Aproveite as ferramentas abaixo para automatizar o provisionamento e o desprovisionamento. 


|Recurso  |Descrição|Recomendação |
|---------|---------|---------|
|Provisionamento do SCIM|[Scim](https://aka.ms/SICMOverview) é uma prática recomendada do setor para automatizar o provisionamento de usuários. Qualquer aplicativo compatível com SCIM pode ser integrado ao Azure AD. Crie, atualize e exclua automaticamente as contas de usuário sem precisar manter arquivos CSV, scripts personalizados ou soluções locais.|Confira a lista crescente de aplicativos [previamente integrados](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) na Galeria de aplicativos do Azure AD|
|Microsoft Graph|Aproveite a respire e a profundidade dos dados que o Azure AD tem para enriquecer seu aplicativo com os dados de que precisa.|Aproveite o [Microsoft Graph](https://developer.microsoft.com/graph/) para obter dados de todo o ecossistema da Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Cenário 3: proteger seus aplicativos
Identidade é o pivô de segurança. Se uma identidade for comprometida, é incrivelmente difícil interromper o efeito do Domino antes que seja tarde demais. Na média de mais de 100 dias, passe antes que as organizações descubram que houve um comprometimento. Use as ferramentas fornecidas pelo Azure AD para melhorar a postura de segurança de seus aplicativos. 

|Recurso  |Descrição| Recomendação |
|---------|---------| ---------|
|MFA do Azure|A Autenticação Multifator do Azure é uma solução de verificação em duas etapas da Microsoft. Usando métodos de autenticação aprovados pelo administrador, o Azure MFA ajuda a proteger o acesso aos seus dados e aplicativos, ao mesmo tempo em que atende à demanda por um processo de entrada simples.| [Habilite a MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) para seus usuários.  |
|Acesso condicional|Com o acesso condicional, você pode implementar decisões de controle de acesso automatizadas para quem pode acessar seus aplicativos de nuvem, com base em condições.| Examine os [padrões de segurança](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) e [as políticas comuns](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) que os clientes estão usando. | 
|Identity Protection|O Identity Protection usa os aprendizados que a Microsoft adquiriu com sua posição em organizações com o Azure AD, com o espaço do consumidor com as contas Microsoft e com os jogos do Xbox para proteger seus usuários. A Microsoft analisa 6,5 trilhões de sinais por dia para identificar e proteger os clientes contra ameaças.|Habilite as [políticas de proteção de identidade padrão](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) fornecidas pelo nosso serviço. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Cenário 4: controlar o acesso aos seus aplicativos
A governança de identidade ajuda as organizações a alcançar um equilíbrio entre a produtividade – com que rapidez uma pessoa pode ter acesso aos aplicativos de que precisam, como quando eles se unem à minha organização? E segurança – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa? 

|Recurso  |Descrição|Recomendação |
|---------|---------| ---------|
|ELM|O gerenciamento de direitos do Azure AD pode ajudar os usuários dentro e fora da sua organização a gerenciar o acesso a seus aplicativos de maneira mais eficiente.| Permitir que não administradores gerenciem o acesso de seus aplicativos a [pacotes de acesso](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Revisões de acesso|O acesso do usuário a aplicativos pode ser revisado regularmente para garantir que apenas as pessoas certas tenham acesso contínuo.| [Examine o acesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) aos seus aplicativos mais confidenciais. |
|Log Analytics|Gere relatórios sobre quem está acessando quais aplicativos e armazene-os em sua ferramenta SIEM de sua escolha para correlacionar dados entre fontes de dados e ao longo do tempo.| Habilite o [log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) e configure alertas para eventos críticos relacionados aos seus aplicativos. |


## <a name="scenario-5-hybrid-secure-access"></a>Cenário 5: acesso seguro híbrido
A identidade só poderá ser seu plano de controle se puder conectar tudo em aplicativos de nuvem e locais. Aproveite as ferramentas fornecidas pelo Azure AD e seus parceiros para proteger o acesso a aplicativos baseados em autenticação herdada.

|Recurso  |Descrição|Recomendação |
|---------|---------|---------|
|Proxy de aplicativo|Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Eles precisam acessar aplicativos SaaS na nuvem e nos aplicativos corporativos locais. O proxy de aplicativo do Azure AD permite esse acesso robusto sem dispendiosos e complexas redes virtuais privadas (VPNs) ou zonas desmilitarizadas (DMZs).|Configure o [acesso remoto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para seus aplicativos locais. |
|F5, Akamai, Zscaler|Usando o controlador de rede e entrega existente, você pode proteger facilmente aplicativos herdados que ainda são críticos para seus processos de negócios, mas que antes não podiam ser protegidos com o Azure AD. É provável que você já tenha tudo de que precisa para começar a proteger esses aplicativos.| Usando Akamai, Citrix, F5 ou Zscaler? Confira nossas [soluções predefinidas](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). | 

## <a name="related-articles"></a>Artigos relacionados

- [Gerenciamento de aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Provisionamento de aplicativo](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Acesso seguro híbrido](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Governança de identidade](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Segurança de identidade](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
