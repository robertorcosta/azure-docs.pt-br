---
title: Cenários comuns de gerenciamento de aplicativos para o Azure Active Directory | Microsoft Docs
description: Centralize o gerenciamento de aplicativos com o Azure AD
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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261033"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralize o gerenciamento de aplicativos com o Azure AD

Senhas, um pesadelo de TI e uma dor para funcionários em todo o mundo. É por isso que cada vez mais empresas estão se voltando para o Azure Active Directory, a solução de Gerenciamento de Identidade e Acesso da Microsoft para a nuvem e todos os seus outros recursos. Pule de aplicativo para aplicativo sem ter que digitar uma senha para cada um. Pule do Outlook, para o Workday, para o ADP o mais rápido possível para abri-los, de forma rápida e segura. Em seguida, colabore com parceiros e até mesmo outros fora da sua organização, tudo sem ter que chamar de TI. Além disso, o Azure AD ajuda a gerenciar o risco, protegendo os aplicativos que você usa com coisas como autenticação multifatorial para verificar quem você é, usando aprendizado de máquina e inteligência de segurança continuamente adaptativos para detectar logins suspeitos que lhe dão acesso seguro aos aplicativos que você precisa, onde quer que você esteja. Não é apenas ótimo para os usuários, mas também para a TI. Com avaliações de acesso just-in-time e um pacote de governança em larga escala, o Azure AD ajuda você a permanecer em conformidade e aplicar políticas também. E veja só, você pode até automatizar o provisionamento de contas de usuários, tornando o gerenciamento de acesso uma brisa. confira alguns dos cenários comuns para os quais o cliente usa os recursos de gerenciamento de aplicativos do Azure Active Directory.

**Cenários comuns**


> [!div class="checklist"]
> * SSO para todas as suas aplicações
> * Automatizar o provisionamento e o desprovisionamento 
> * Proteja suas aplicações
> * Governe o acesso às suas aplicações
> * Acesso seguro híbrido

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Cenário 1: Configure o SSO para todas as suas aplicações

Chega de gerenciar senhas. Acesse com segurança todos os recursos necessários com suas credenciais corporativas. 

|Recurso  | Descrição | Recomendação |
|---------|---------|---------|
|SSO|SSO federado baseado em padrões usando padrões confiáveis do setor.|Use sempre [OSM/SAML para](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) ativar o SSO quando o aplicativo o suportar.|
|Painel de acesso|Oferece a seus usuários um hub simples para descobrir e acessar todos os seus aplicativos. Permitir que eles sejam mais produtivos com recursos de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciar o acesso a recursos em nome de terceiros.| Implante o [painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) em sua organização depois de integrar seus aplicativos com o Azure AD para SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Cenário 2: Automatizar o provisionamento e o desprovisionamento 


A maioria dos aplicativos exige que um usuário seja provisionado no aplicativo antes de acessar os recursos de que precisa. Usar arquivos CSV ou scripts complexos pode ser caro e difícil de gerenciar. Além disso, os clientes precisam garantir que as contas sejam removidas quando alguém não deve mais ter acesso. Aproveite as ferramentas abaixo para automatizar o provisionamento e o desprovisionamento. 


|Recurso  |Descrição|Recomendação |
|---------|---------|---------|
|Provisionamento SCIM|[O SCIM](https://aka.ms/SICMOverview) é uma prática recomendada do setor para automatizar o provisionamento de usuários. Qualquer aplicativo compatível com SCIM pode ser integrado ao Azure AD. Crie, atualize e exclua automaticamente contas de usuário sem ter que manter arquivos CSV, scripts personalizados ou soluções on-prem.|Confira a crescente lista de aplicativos [pré-integrados](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) na galeria de aplicativos do Azure AD|
|Microsoft Graph|Aproveite a respiração e a profundidade dos dados que o Azure AD tem para enriquecer sua aplicação com os dados que ele precisa.|Aproveite o gráfico da [Microsoft](https://developer.microsoft.com/graph/) para obter dados de todo o ecossistema da Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Cenário 3: Proteja suas aplicações
A identidade é o eixo da segurança. Se uma identidade fica comprometida, é incrivelmente difícil parar o efeito dominó antes que seja tarde demais. Em média, mais de 100 dias passam antes que as organizações descubram que houve um compromisso. Use as ferramentas fornecidas pelo Azure AD para melhorar a postura de segurança de seus aplicativos. 

|Recurso  |Descrição| Recomendação |
|---------|---------| ---------|
|MFA do Azure|A Autenticação Multifator do Azure é uma solução de verificação em duas etapas da Microsoft. Usando métodos de autenticação aprovados pelo administrador, o Azure MFA ajuda a proteger o acesso aos seus dados e aplicativos, ao mesmo tempo em que atende à demanda por um simples processo de login.| [Habilite o MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) para seus usuários.  |
|Acesso Condicional|Com o Conditional Access, você pode implementar decisões automatizadas de controle de acesso para quem pode acessar seus aplicativos na nuvem, com base nas condições.| Revise os [padrões de segurança](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) e as políticas [comuns](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) que os clientes estão usando. | 
|Identity Protection|O Identity Protection usa os aprendizados que a Microsoft adquiriu com sua posição em organizações com o Azure AD, com o espaço do consumidor com as contas Microsoft e com os jogos do Xbox para proteger seus usuários. A Microsoft analisa 6,5 trilhões de sinais por dia para identificar e proteger os clientes contra ameaças.|Habilite as [políticas de proteção de identidade padrão](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) fornecidas pelo nosso serviço. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Cenário 4: Governe o acesso às suas aplicações
A Governança de Identidade ajuda as organizações a alcançar um equilíbrio entre produtividade - Quão rapidamente uma pessoa pode ter acesso aos aplicativos de que precisa, como quando entra na minha organização? E segurança – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa? 

|Recurso  |Descrição|Recomendação |
|---------|---------| ---------|
|Elm|O gerenciamento de direitos do Azure AD pode ajudar os usuários dentro e fora de sua organização a gerenciar de forma mais eficiente o acesso aos seus aplicativos.| Permitir que os não-admins gerenciem o acesso aos seus aplicativos com [pacotes de acesso](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Revisões de acesso|O acesso do usuário aos aplicativos pode ser revisto regularmente para garantir que apenas as pessoas certas tenham acesso contínuo.| [Revise](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) o acesso aos seus aplicativos mais sensíveis. |
|Log Analytics|Gere relatórios sobre quem está acessando quais aplicativos e armazená-los em sua ferramenta de escolha do SIEM para correlacionar dados entre fontes de dados e ao longo do tempo.| Habilite [a análise de log](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) e configure alertas para eventos críticos relacionados aos seus aplicativos. |


## <a name="scenario-5-hybrid-secure-access"></a>Cenário 5: Acesso seguro híbrido
A identidade só pode ser o seu plano de controle se ele pode conectar tudo através de aplicativos em nuvem e no local. Aproveite as ferramentas fornecidas pelo Azure AD e seus parceiros para garantir o acesso a aplicativos baseados em herdade.

|Recurso  |Descrição|Recomendação |
|---------|---------|---------|
|Proxy do Aplicativo|Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Eles precisam acessar aplicativos SaaS na nuvem e aplicativos corporativos no local. O proxy do Aplicativo Azure AD permite esse acesso robusto sem vpns (vpns) ou zonas desmilitarizadas (DMZs) de redes virtuais caras e complexas.|Configure [o acesso remoto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para seus aplicativos on-prem. |
|F5, Akamai, Zscaler|Usando o controlador de rede e entrega existente, você pode proteger facilmente aplicativos herdados que ainda são críticos para seus processos de negócios, mas que antes não podiam ser protegidos com o Azure AD. É provável que você já tenha tudo de que precisa para começar a proteger esses aplicativos.| Usando Akamai, Citrix, F5 ou Zscaler? Confira nossas [soluções pré-construídas.](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access) | 

## <a name="related-articles"></a>Artigos relacionados

- [Gerenciamento de aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Provisionamento de aplicativo](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Acesso seguro híbrido](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Governança de identidade](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Segurança de identidade](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
