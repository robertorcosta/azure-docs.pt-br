---
title: Novidades? Notas sobre a versão – Azure Active Directory | Microsoft Docs
description: Conheça as novidades do Azure Active Directory, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274189"
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

>Seja notificado sobre quando revisitar esta página para obter as atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` em seu ícone do ![leitor de feed de RSS da](./media/whats-new/feed-icon-16x16.png) leitor de feeds.

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente. Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo no Azure Active Directory](whats-new-archive.md).

---

## <a name="february-2020"></a>Fevereiro de 2020
 
### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Pontuação segura de identidade-atualizações de ação de melhoria de MFA

**Tipo:** Plano para alteração  
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** Segurança de Identidade e Proteção
 
Para refletir a necessidade de que as empresas assegurem a maior parte da segurança ao aplicar políticas que funcionam com seus negócios, a pontuação segura da Microsoft está removendo três ações de aprimoramento centralizadas em relação à MFA (autenticação multifator) e à adição de duas.

As seguintes ações de aprimoramento serão removidas:

- Registrar todos os usuários para MFA
- Exigir MFA para todos os usuários
- Exigir MFA para funções privilegiadas do Azure AD

As seguintes ações de aprimoramento serão adicionadas:

- Garantir que todos os usuários possam concluir a MFA para acesso seguro
- Exigir MFA para funções administrativas

Essas novas ações de aprimoramento exigirão o registro de seus usuários ou administradores para MFA em seu diretório e o estabelecimento do conjunto certo de políticas que atendam às suas necessidades organizacionais. O principal objetivo é ter flexibilidade ao garantir que todos os usuários e administradores possam autenticar com vários fatores ou prompts de verificação de identidade com base em risco. Isso pode assumir a forma de definir os padrões de segurança que permitem que a Microsoft decida quando desafiar os usuários para MFA ou ter várias políticas que apliquem decisões com escopo. Como parte dessas atualizações de ação de aperfeiçoamento, as políticas de proteção de linha de base não serão mais incluídas nos cálculos de pontuação. [Leia mais sobre o que está chegando na pontuação segura da Microsoft](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).
 
---

### <a name="azure-ad-domain-services-sku-selection"></a>Seleção Azure AD Domain Services SKU

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services
 
Ouvimos comentários que Azure AD Domain Services os clientes desejam mais flexibilidade na seleção de níveis de desempenho para suas instâncias. A partir de 1º de fevereiro de 2020, mudamos de um modelo dinâmico (no qual o Azure AD determina o desempenho e o tipo de preço com base na contagem de objetos) para um modelo de seleção automática. Agora, os clientes podem escolher um nível de desempenho que corresponda a seu ambiente. Essa alteração também nos permite habilitar novos cenários como florestas de recursos e recursos premium como backups diários. A contagem de objetos agora é ilimitada para todas as SKUs, mas continuaremos a oferecer sugestões de contagem de objetos para cada camada.

**Nenhuma ação imediata do cliente é necessária.** Para clientes existentes, a camada dinâmica que estava em uso em 1º de fevereiro de 2020 determina a nova camada padrão. Não há nenhum impacto de preço ou desempenho como resultado dessa alteração. No futuro, os clientes do Azure AD DS precisarão avaliar os requisitos de desempenho à medida que o tamanho do diretório e as características da carga de trabalho forem alterados. A alternância entre as camadas de serviço continuará a ser uma operação sem tempo de inatividade, e não mais moveremos os clientes automaticamente para novas camadas com base no crescimento de seu diretório. Além disso, não haverá aumento de preço e novos preços serão alinhados com nosso modelo de cobrança atual. Para obter mais informações, consulte a [documentação dos SKUs do Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) e a [página de preços do Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).


---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – fevereiro de 2020

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros
 
Em fevereiro de 2020, adicionamos esses 31 novos aplicativos com suporte à Federação para a Galeria de aplicativos: 

[Plataforma de patente IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [experiência em nuvem](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [SSO ns1 para Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [serviço de segurança de email Barracuda](https://ess.barracudanetworks.com/sso/azure), [relatório aba](https://myaba.co.uk/client-access/signin/auth/msad), [no caso de crise – portal online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [design de nuvem de BIC](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), o conector de [dados do Azure ad](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [avaliações de Beekeeper Korn](https://www.kornferry.com/solutions/kf-digital/kf-assess), ferry [comando](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Verkada](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [Syxsense Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [novo EAB (versão limitada)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) [,](https://admin.thulium.com/login/instance), [Gerenciador de tíquetes](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [modelo Seletor para equipes](https://links.officeatwork.com/templatechooser-download-teams), [abelhas](https://www.beesy.me/index.php/site/login), [sistema de suporte de integridade](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [mural](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink para professores e escolas](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [reimpressões Desk-artigo Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – fevereiro de 2020

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros
 
Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Suporte do Azure AD para chaves de segurança FIDO2 em ambientes híbridos

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de Usuário
 
Estamos anunciando a visualização pública do suporte do Azure AD para chaves de segurança FIDO2 em ambientes híbridos. Agora, os usuários podem usar as chaves de segurança FIDO2 para entrar em seus dispositivos Windows 10 híbridos ingressados no Azure AD e obter logon contínuo em seus recursos locais e na nuvem. O suporte para ambientes híbridos tem sido o principal recurso mais solicitado de nossos clientes sem senha, pois inicialmente lançamos a visualização pública para suporte FIDO2 em dispositivos ingressados no Azure AD. A autenticação sem senha usando tecnologias avançadas, como a biometria e a criptografia de chave pública/privada, proporcionam conveniência e facilidade de uso enquanto são seguras. Com essa visualização pública, agora você pode usar autenticação moderna, como chaves de segurança FIDO2, para acessar recursos tradicionais de Active Directory. Para obter mais informações, acesse [SSO para recursos locais](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Para começar, visite [habilitar chaves de segurança FIDO2 para seu locatário](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) para obter instruções passo a passo. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>A nova experiência de minha conta agora está disponível para o público geral 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Meu perfil/conta  
**Funcionalidade do produto:** Experiências do usuário final
 
Minha conta, a única loja para todas as necessidades de gerenciamento de conta de usuário final, já está disponível para o público geral! Os usuários finais podem acessar esse novo site via URL ou no cabeçalho da nova experiência meus aplicativos. Saiba mais sobre todos os recursos de autoatendimento que a nova experiência oferece em [minha visão geral do portal de contas](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>URL do site da minha conta Atualizando para o myaccount.microsoft.com

**Tipo:** Recurso alterado  
**Categoria de serviço:** Meu perfil/conta  
**Funcionalidade do produto:** Experiências do usuário final
 
A nova experiência do usuário final da minha conta atualizará sua URL para https://myaccount.microsoft.com no próximo mês. Encontre mais informações sobre a experiência e todos os recursos de autoatendimento da conta que ele oferece aos usuários finais na [ajuda do portal da minha conta](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>O novo portal meus aplicativos agora está disponível para o público geral

**Tipo:** Plano para alteração  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** Experiências do usuário final
 
Atualize sua organização para o novo portal meus aplicativos que agora está disponível para o público geral! Encontre mais informações sobre o novo portal e coleções em [criar coleções no portal meus aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Os espaços de trabalho no Azure AD foram renomeados para coleções

**Tipo:** Recurso alterado  
**Categoria de serviço:** Meus Aplicativos   
**Funcionalidade do produto:** Experiências do usuário final
 
Nos espaços de trabalho, os administradores de filtros podem configurar o para organizar seus aplicativos de usuários, agora serão chamados de coleções. Encontre mais informações sobre como configurá-los em [criar coleções no portal meus aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C inscrição e entrada no telefone usando a política personalizada (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Com a inscrição e a entrada do número de telefone, os desenvolvedores e as empresas podem permitir que seus clientes se inscrevam e entrem usando uma senha de uso único enviada ao número de telefone do usuário via SMS. Esse recurso também permite que o cliente altere seu número de telefone se eles perderem o acesso ao seu telefone. Com o poder das políticas personalizadas, a inscrição e a entrada do telefone permitem que os desenvolvedores e as empresas comuniquem sua marca por meio da personalização da página. Descubra como configurar a [inscrição e a entrada do telefone com políticas personalizadas no Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – janeiro de 2020

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros
 
Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – janeiro de 2020

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros
 
Em janeiro de 2020, adicionamos esses 33 novos aplicativos com suporte de Federação à galeria de aplicativos: 

[Josa](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [nuvem de ponta rápida](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick Cloud backup para Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [upcursod](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [Datacamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [inteligente](https://www.intumit.com/english/SmartWork.html), [Dotcom-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-o gateway de SSO do Azure ad para Oracle E-Business Suite-EBS, PeopleSoft e JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [hospedagem MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu plataforma de gerenciamento de propriedade](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Trabalho empresarial](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB para Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient conduzindo o software Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [coreview](https://portal.coreview.com/), [silenciar Cloud Office365 Connector](https://laxmi.squelch.io/login), [autenticação PingFlow](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [tarefas Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Assistente virtual](https://akari.io/akari-virtual-assistant/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Duas novas detecções de proteção de identidade

**Tipo:** Novo recurso  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de Identidade e Proteção
 
Adicionamos dois novos tipos de detecção de entrada vinculada à proteção de identidade: Regras de manipulação de caixa de entrada suspeitas e viagem impossível. Essas detecções offline são descobertas por Microsoft Cloud App Security (MCAS) e influenciam o usuário e o risco de entrada no Identity Protection. Para obter mais informações sobre essas detecções, consulte nossos [tipos de risco de entrada](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Alteração da falha: Os fragmentos de URI não serão transferidos por meio do redirecionamento de logon

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de Usuário
 
A partir de 8 de fevereiro de 2020, quando uma solicitação for enviada ao login.microsoftonline.com para entrar em um usuário, o serviço acrescentará um fragmento vazio à solicitação.  Isso impede uma classe de ataques de redirecionamento, garantindo que o navegador apague qualquer fragmento existente na solicitação. Nenhum aplicativo deve ter uma dependência desse comportamento. Para obter mais informações, consulte [alterações recentes](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) na documentação da plataforma de identidade da Microsoft.

---

## <a name="december-2019"></a>Dezembro de 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrar o provisionamento do SAP SuccessFactors ao Azure AD e ao AD local (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Gerenciamento do Ciclo de Vida de Identidades

Agora você pode integrar o SAP SuccessFactors como uma fonte de identidade autoritativa no Azure AD. Essa integração ajuda a automatizar o ciclo de vida de identidade de ponta a ponta, incluindo o uso de eventos baseados em RH, como novas contratações ou encerramentos, para controlar o provisionamento de contas do Azure AD.

Para obter mais informações sobre como configurar o provisionamento de entrada do SAP SuccessFactors para o Azure AD, consulte o tutorial [Configurar provisionamento automático do SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Suporte para emails personalizados no Azure AD B2C (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode usar Azure AD B2C para criar emails personalizados quando os usuários se inscrevem para usar seus aplicativos. Usando o DisplayControls (atualmente em visualização) e um provedor de email de terceiros (como, [SendGrid](https://sendgrid.com/), [SPARKPOST](https://sparkpost.com/)ou uma API REST personalizada), você pode usar seu próprio modelo de email, endereço e texto **de** assunto, bem como a localização de suporte e configurações de senha de uso único (OTP) personalizadas.

Para obter mais informações, consulte [verificação de email personalizada em Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Substituição de políticas de linha de base com padrões de segurança

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Segurança e proteção de identidade

Como parte de um modelo seguro por padrão para autenticação, estamos removendo as políticas de proteção de linha de base existentes de todos os locatários. Essa remoção é destinada à conclusão no final de fevereiro. A substituição para essas políticas de proteção de linha de base é o padrão de segurança. Se você estiver usando políticas de proteção de linha de base, deverá planejar a movimentação para a nova política de padrões de segurança ou para o acesso condicional. Se você não usou essas políticas, não há nenhuma ação a ser tomada.

Para obter mais informações sobre os novos padrões de segurança, consulte [o que são os padrões de segurança?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Para obter mais informações sobre políticas de acesso condicional, consulte [políticas de acesso condicional comum](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Novembro de 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Suporte para o atributo SameSite e Chrome 80

**Tipo:** Plano para alteração  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de Usuário

Como parte de um modelo seguro por padrão para cookies, o navegador Chrome 80 está alterando como ele trata cookies sem o atributo `SameSite`. Qualquer cookie que não especifique o atributo `SameSite` será tratado como se estivesse definido como `SameSite=Lax`, o que resultará no cromo bloquear determinados cenários de compartilhamento de cookies entre domínios em que seu aplicativo possa depender. Para manter o comportamento mais antigo do Chrome, você pode usar o atributo `SameSite=None` e adicionar um atributo `Secure` adicional, de modo que os cookies entre sites só possam ser acessados por conexões HTTPS. O Chrome está agendado para concluir essa alteração até 4 de fevereiro de 2020.

Recomendamos que todos os nossos desenvolvedores testem seus aplicativos usando estas diretrizes:

- Defina o valor padrão para a configuração **usar cookie seguro** como **Sim**.

- Defina o valor padrão para o atributo **SameSite** como **None**.

- Adicione um atributo de `SameSite` adicional de **Secure**.

Para obter mais informações, consulte [próximas alterações de cookie de SameSite em ASP.net e ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e [potencial interrupção em sites de clientes e produtos e serviços da Microsoft no Chrome versão 79 e posterior](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Novo hotfix para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** Correção  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto:** Gerenciamento do Ciclo de Vida de Identidades

Um pacote cumulativo de atualizações de hotfix (Build 4.6.34.0) está disponível para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Esse pacote cumulativo resolve problemas e adiciona melhorias que são descritas na seção "problemas corrigidos e aprimoramentos adicionados nesta atualização".

Para obter mais informações e baixar o pacote de hotfix, consulte [Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0). o pacote cumulativo de atualizações está disponível](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Novo relatório de atividade do aplicativo AD FS para ajudar a migrar aplicativos para o Azure AD (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Use o novo relatório de atividade de aplicativo Serviços de Federação do Active Directory (AD FS) (AD FS), no portal do Azure, para identificar quais dos seus aplicativos podem ser migrados para o Azure AD. O relatório avalia todos os aplicativos AD FS para compatibilidade com o Azure AD, verifica se há problemas e fornece orientação sobre como preparar aplicativos individuais para migração.

Para obter mais informações, consulte [usar o relatório de atividade do aplicativo AD FS para migrar aplicativos para o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Novo fluxo de trabalho para os usuários solicitarem o consentimento do administrador (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Controle de Acesso

O novo fluxo de trabalho de consentimento do administrador fornece aos administradores uma maneira de conceder acesso a aplicativos que exigem aprovação de administrador. Se um usuário tentar acessar um aplicativo, mas não puder fornecer consentimento, ele agora poderá enviar uma solicitação de aprovação de administrador. A solicitação é enviada por email e colocada em uma fila que pode ser acessada pelo portal do Azure, para todos os administradores que foram designados como revisores. Depois que um revisor executa uma ação em uma solicitação pendente, os usuários solicitantes são notificados sobre a ação.

Para obter mais informações, consulte [Configurar o fluxo de trabalho de consentimento do administrador (versão prévia)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nova experiência de configuração de token de registros de Aplicativo Azure AD para gerenciar declarações opcionais (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Experiência do Desenvolvedor

A folha de configuração do novo **token de registros de aplicativo Azure ad** no portal do Azure agora mostra aos desenvolvedores de aplicativos uma lista dinâmica de declarações opcionais para seus aplicativos. Essa nova experiência ajuda a simplificar as migrações de aplicativo do Azure AD e a minimizar as configurações incorretas de declarações opcionais.

Para obter mais informações, consulte [fornecer declarações opcionais para seu aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Novo fluxo de trabalho de aprovação de dois estágios no gerenciamento de direitos do Azure AD (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Gerenciamento de direitos

Apresentamos um novo fluxo de trabalho de aprovação de dois estágios que permite que você exija que dois aprovadores aprovem a solicitação de um usuário para um pacote do Access. Por exemplo, você pode defini-lo para que o gerente do usuário solicitante deva primeiro aprovar e, em seguida, você também pode exigir que um proprietário do recurso aprove. Se um dos aprovadores não aprovar, o acesso não será concedido.

Para obter mais informações, consulte [alterar as configurações de solicitação e aprovação para um pacote de acesso no gerenciamento de direitos do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizações para a página meus aplicativos junto com novos espaços de trabalho (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** Integração de Terceiros

Agora você pode personalizar a maneira como os usuários da sua organização exibem e acessam a experiência dos meus aplicativos atualizados. Essa nova experiência também inclui o novo recurso de espaços de trabalho, o que torna mais fácil para os usuários localizar e organizar aplicativos.

Para obter mais informações sobre a nova experiência meus aplicativos e criar espaços de trabalho, consulte [criar espaços de trabalho no portal meus aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Suporte de ID do Google social para colaboração B2B do Azure AD (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** Autenticação de Usuário

O novo suporte para o uso de IDs sociais do Google (contas do Gmail) no Azure AD ajuda a tornar a colaboração mais simples para seus usuários e parceiros. Não há mais necessidade de que seus parceiros criem e gerenciem uma nova conta específica da Microsoft. O Microsoft Teams agora dá suporte completo aos usuários do Google em todos os clientes e nos pontos de extremidade de autenticação comuns e relacionados ao locatário.

Para obter mais informações, consulte [Adicionar o Google como um provedor de identidade para usuários de convidado B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Suporte móvel do Microsoft Edge para acesso condicional e logon único (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de Identidade e Proteção

O Azure AD para Microsoft Edge no iOS e Android agora dá suporte ao logon único do Azure AD e ao acesso condicional:

- **Logon único (SSO) do Microsoft Edge:** O logon único agora está disponível entre clientes nativos (como o Microsoft Outlook e o Microsoft Edge) para todos os aplicativos conectados ao Azure AD.

- **Acesso condicional do Microsoft Edge:** Por meio de políticas de acesso condicional baseadas em aplicativo, os usuários devem usar navegadores protegidos por Microsoft Intune, como o Microsoft Edge.

Para obter mais informações sobre o acesso condicional e o SSO com o Microsoft Edge, consulte a postagem do blog de [suporte móvel do Microsoft Edge para acesso condicional e logon único agora disponível](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) . Para obter mais informações sobre como configurar seus aplicativos cliente usando o [acesso condicional baseado em aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) ou o [acesso condicional baseado em dispositivo](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), consulte Gerenciar o [acesso à Web usando um navegador Microsoft Intune protegido por política](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gerenciamento de direitos do Azure AD (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Gerenciamento de direitos

O gerenciamento de direitos do Azure AD é um novo recurso de governança de identidade, que ajuda as organizações a gerenciar o ciclo de vida de identidade e acesso em escala. Esse novo recurso ajuda a automatizar fluxos de trabalho de solicitação de acesso, atribuições de acesso, análises e expiração entre grupos, aplicativos e sites do SharePoint Online.

Com o gerenciamento de direitos do Azure AD, você pode gerenciar o acesso de forma mais eficiente para funcionários e também para usuários fora da sua organização que precisam de acesso a esses recursos.

Para obter mais informações, consulte [o que é o gerenciamento de direitos do Azure ad?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros  

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

[Serviço de autenticação de identidade da SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [matriz de prioridade](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – novembro de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros

Em novembro de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

O [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [acesso azul para Membros (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal SSO (logon único)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & o gerenciamento de inovação](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [JISC Student eleitor Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galeria de aplicativos novos e aprimorados do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Atualizamos a Galeria de aplicativos do Azure AD para facilitar a localização de aplicativos previamente integrados que dão suporte ao provisionamento, ao OpenID Connect e ao SAML em seu locatário Azure Active Directory.

Para obter mais informações, consulte [Adicionar um aplicativo ao seu locatário Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Maior limite de tamanho de definição de função de aplicativo de 120 a 240 caracteres

**Tipo:** Recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Ouvimos clientes de que o limite de comprimento para o valor de definição de função de aplicativo em alguns aplicativos e serviços é muito curto em 120 caracteres. Em resposta, aumentamos o comprimento máximo da definição do valor da função para 240 caracteres.

Para obter mais informações sobre como usar definições de função específicas do aplicativo, consulte [adicionar funções de aplicativo em seu aplicativo e recebê-las no token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Outubro de 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Substituição da API identityRiskEvent para Azure AD Identity Protection as detecções de risco  

**Tipo:** Plano para alteração  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de Identidade e Proteção

Em resposta aos comentários do desenvolvedor, os assinantes do Azure AD Premium P2 agora podem executar consultas complexas nos dados de detecção de risco Azure AD Identity Protection usando a nova API do riskDetection para Microsoft Graph. A versão beta da API do [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) existente deixará de retornar dados em até **10 de janeiro de 2020**. Se sua organização estiver usando a API identityRiskEvent, você deverá fazer a transição para a nova API riskDetection.

Para obter mais informações sobre a nova API do riskDetection, consulte a [documentação de referência de API de detecção de risco](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Suporte ao proxy de aplicativo para o atributo SameSite e Chrome 80

**Tipo:** Plano para alteração  
**Categoria de serviço:** Proxy de Aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Algumas semanas antes da versão do navegador Chrome 80, planejamos atualizar como os cookies de proxy de aplicativo tratam o atributo **SameSite** . Com o lançamento do Chrome 80, qualquer cookie que não especifique o atributo **SameSite** será tratado como se estivesse definido como `SameSite=Lax`.

Para ajudar a evitar impactos potencialmente negativos devido a essa alteração, estamos atualizando o acesso do proxy de aplicativo e cookies de sessão por:

- Definindo o valor padrão para a configuração **usar cookie seguro** como **Sim**.

- Definindo o valor padrão para o atributo **SameSite** como **None**.

    >[!NOTE]
    > Os cookies de acesso ao proxy de aplicativo sempre foram transmitidos exclusivamente por canais seguros. Essas alterações se aplicam somente a cookies de sessão.

Para obter mais informações sobre as configurações de cookie do proxy de aplicativo, consulte [configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Registros de aplicativo (Herdado) e o gerenciamento de aplicativo convergido do apps.dev.microsoft.com (portal de registro de aplicativos) não estarão mais disponíveis

**Tipo:** Plano para alteração  
**Categoria de serviço:** N/D  
**Funcionalidade do produto:** Experiência do Desenvolvedor

Em breve, os usuários com contas do Azure AD não poderão mais registrar e gerenciar aplicativos convergidos usando o apps.dev.microsoft.com (portal de registro de aplicativos) ou registrar e gerenciar aplicativos no Registros de aplicativo (Herdado) experiência no portal do Azure.

Para saber mais sobre a nova experiência de Registros de aplicativo, consulte o [registros de aplicativo no guia de treinamento de portal do Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Os usuários não precisam mais se registrar novamente durante a migração do MFA por usuário para MFA baseada em acesso condicional

**Tipo:** Correção  
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** Segurança de Identidade e Proteção

Corrigimos um problema conhecido no qual quando os usuários eram solicitados a se registrar novamente se eles estivessem desabilitados para MFA (autenticação multifator) por usuário e, em seguida, habilitados para MFA por meio de uma política de acesso condicional.

Para exigir que os usuários se registrem novamente, você pode selecionar a opção **reregistrar o MFA necessária** nos métodos de autenticação do usuário no portal do Azure AD. Para obter mais informações sobre como migrar usuários do MFA por usuário para MFA baseada em acesso condicional, consulte [converter usuários de MFA por usuário para MFA baseada em acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Novos recursos para transformar e enviar declarações em seu token SAML

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Adicionamos recursos adicionais para ajudá-lo a personalizar e enviar declarações em seu token SAML. Esses novos recursos incluem:

- Funções de transformação de declarações adicionais, ajudando a modificar o valor que você envia na declaração.

- Capacidade de aplicar várias transformações a uma única declaração.

- Capacidade de especificar a origem da declaração, com base no tipo de usuário e no grupo ao qual o usuário pertence.

Para obter informações detalhadas sobre esses novos recursos, incluindo como usá-los, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nova página minhas entradas para usuários finais no Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Monitoramento e Relatório

Adicionamos uma nova página **minhas** entradas (https://mysignins.microsoft.com) para permitir que os usuários da sua organização exibam seu histórico de entrada recente para verificar se há atividades incomuns. Essa nova página permite que os usuários vejam:

- Se alguém estiver tentando adivinhar sua senha.

- Se um invasor tiver entrado com êxito em sua conta e a partir de qual local.

- Quais aplicativos o invasor tentou acessar.

Para obter mais informações, consulte os [usuários agora podem verificar seu histórico de entrada para o blog de atividades incomuns](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migração de Azure AD Domain Services (AD DS do Azure) do clássico para Azure Resource Manager redes virtuais

**Tipo:** Novo recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Para nossos clientes que ficaram presos em redes virtuais clássicas, temos excelentes notícias para você! Agora você pode executar uma migração única de uma rede virtual clássica para uma rede virtual do Resource Manager existente. Depois de mudar para a rede virtual do Resource Manager, você poderá aproveitar os recursos adicionais e atualizados, como políticas de senha refinadas, notificações por email e logs de auditoria.

Para obter mais informações, consulte [Preview-migrar Azure AD Domain Services do modelo de rede virtual clássica para o Gerenciador de recursos](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Atualizações para o layout do contrato de página Azure AD B2C

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Apresentamos algumas novas alterações na versão 1.2.0 do contrato de página para Azure AD B2C. Nesta versão atualizada, agora você pode controlar a ordem de carga para seus elementos, o que também pode ajudar a parar a cintilação que acontece quando a folha de estilos (CSS) é carregada.

Para obter uma lista completa das alterações feitas no contrato de página, consulte o [log de alterações de versão](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizar para a página meus aplicativos junto com novos espaços de trabalho (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** Controle de Acesso

Agora você pode personalizar a maneira como os usuários da sua organização exibem e acessam a experiência da nova empresa de aplicativos, incluindo o uso do novo recurso de espaços de trabalho para facilitar a localização de aplicativos. A nova funcionalidade de espaços de trabalho atua como um filtro para os aplicativos aos quais os usuários da sua organização já têm acesso.

Para obter mais informações sobre como distribuir a nova experiência meus aplicativos e criar espaços de trabalho, consulte [criar espaços de trabalho no portal meus aplicativos (versão prévia)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Suporte para o modelo de cobrança baseado no usuário ativo mensal (disponibilidade geral)

**Tipo:** Novo recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Azure AD B2C agora dá suporte à cobrança MAU (usuários ativos mensais). A cobrança do MAU é baseada no número de usuários exclusivos com atividade de autenticação durante um mês civil. Os clientes existentes podem alternar para esse novo método de cobrança a qualquer momento.

A partir de 1º de novembro de 2019, todos os novos clientes serão automaticamente cobrados usando esse método. Esse método de cobrança beneficia os clientes por meio de benefícios de custo e a capacidade de planejar com antecedência.

Para obter mais informações, consulte [atualizar para o modelo de cobrança de usuários ativos mensais](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – outubro de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros

Em outubro de 2019, adicionamos esses 35 novos aplicativos com suporte de Federação à galeria de aplicativos:

[No caso de crise – móvel](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [intacto](https://tact.ai/assistant/), [OpusCapita caixa Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [ECornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contently](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – UE](https://www.hirevue.com/), [HireVue Coordinate-USOnly](https://www.hirevue.com/), [HireVue Coordinate-US](https://www.hirevue.com/), [WittyParrot Caixa de conhecimento](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [cambium Xirrus EasyPass portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [sorte de email!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [teamie](https://theteamie.com/), [Velocity for](https://velocity.peakup.org/teams/login)Teams [, SIGNL4,](https://account.signl4.com/manage) [EAB Navigate impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [ômega Point](https://pi.ompnt.com/), [falando de email para o Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [falando email para Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome de navegação](https://ihealthnav.com/account/signin)para o sistema, [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Item de menu de segurança consolidado no portal do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de Identidade e Proteção

Agora você pode acessar todos os recursos de segurança do Azure AD disponíveis no novo item de menu **segurança** e, na barra de **pesquisa** , na portal do Azure. Além disso, a nova página de aterrissagem de **segurança** , chamada **introdução à segurança**, fornecerá links para nossa documentação pública, diretrizes de segurança e guias de implantação.

O novo menu de **segurança** inclui:

- Acesso condicional
- Proteção de identidade
- Central de Segurança
- Classificação de segurança de identidade
- Métodos de autenticação
- MFA
- Relatórios de risco-usuários arriscados, entradas arriscadas, detecções de risco
- E muito mais...

Para obter mais informações, consulte [segurança-introdução](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Política de expiração dos grupos do Office 365 aprimorada com renovação automática

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de Grupo  
**Funcionalidade do produto:** Gerenciamento do Ciclo de Vida de Identidades

A política de expiração dos grupos do Office 365 foi aprimorada para renovar automaticamente os grupos que estão ativamente em uso por seus membros. Os grupos serão renovados com base na atividade do usuário em todos os aplicativos do Office 365, incluindo o Outlook, o SharePoint e as equipes.

Esse aprimoramento ajuda a reduzir as notificações de expiração do grupo e ajuda a garantir que os grupos ativos continuem disponíveis. Se você já tiver uma política de expiração ativa para seus grupos do Office 365, não precisará fazer nada para ativar essa nova funcionalidade.

Para obter mais informações, consulte [Configurar a política de expiração para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Experiência de criação de Azure AD Domain Services (AD DS do Azure) atualizada

**Tipo:** Recurso alterado  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Atualizamos Azure AD Domain Services (AD DS do Azure) para incluir uma nova e aprimorada experiência de criação, ajudando você a criar um domínio gerenciado em apenas três cliques! Além disso, agora você pode carregar e implantar o Azure AD DS de um modelo.

Para saber mais, confira [Tutorial: Criar e configurar uma instância de Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Setembro de 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planejar alteração: Substituição dos pacotes de conteúdo do Power BI

**Tipo:** Plano para alteração  
**Categoria de serviço:** Relatório  
**Funcionalidade do produto:** Monitoramento e Relatório

A partir de 1º de outubro de 2019, Power BI começará a substituir todos os pacotes de conteúdo, incluindo o pacote de conteúdo do Azure AD Power BI. Como alternativa para esse pacote de conteúdo, você pode usar pastas de trabalho do Azure AD para obter informações sobre seus serviços relacionados ao Azure AD. Pastas de trabalho adicionais são disponibilizadas, incluindo pastas de trabalho sobre políticas de acesso condicional no modo somente de relatório, informações baseadas em consentimento do aplicativo e muito mais.

Para obter mais informações sobre as pastas de trabalho, consulte [como usar pastas de trabalho do Azure monitor para relatórios Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Para obter mais informações sobre a substituição dos pacotes de conteúdo, consulte a postagem do blog [anunciando Power bi aplicativos de modelo de disponibilidade geral](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Meu perfil está renomeando e integrando com a página da conta Microsoft Office

**Tipo:** Plano para alteração  
**Categoria de serviço:** Meu perfil/conta  
**Funcionalidade do produto:** Colaboração

A partir de outubro, a minha experiência de perfil se tornará minha conta. Como parte dessa alteração, em todos os lugares que, no momento, **o meu perfil** será alterado para **minha conta**. Além da alteração de nomenclatura e de algumas melhorias de design, a experiência atualizada oferecerá integração adicional com a página de Microsoft Office conta. Especificamente, você poderá acessar as instalações e assinaturas do Office na página da conta de **visão geral** , juntamente com as preferências de contato relacionadas ao Office na página de **privacidade** .

Para obter mais informações sobre a experiência meu perfil (versão prévia), consulte [visão geral do portal meu perfil (versão prévia)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gerenciar grupos e membros em massa usando arquivos CSV no portal do AD do Azure (visualização pública)

**Tipo:** Novo recurso  
**Categoria de serviço:** Gerenciamento de Grupo  
**Funcionalidade do produto:** Colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências de gerenciamento de grupo em massa no portal do Azure AD. Agora você pode usar um arquivo CSV e o portal do AD do Azure para gerenciar grupos e listas de membros, incluindo:

- Adicionando ou removendo membros de um grupo.

- Baixando a lista de grupos do diretório.

- Baixando a lista de membros do grupo para um grupo específico.

Para obter mais informações, consulte [adicionar membros em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [remover membros em](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)massa, [lista de membros de download em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)e lista de grupos de [download em massa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>O consentimento dinâmico agora tem suporte por meio de um novo ponto de extremidade de consentimento do administrador

**Tipo:** Novo recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de Usuário

Criamos um novo ponto de extremidade de consentimento do administrador para dar suporte ao consentimento dinâmico, o que é útil para aplicativos que desejam usar o modelo de consentimento dinâmico na plataforma de identidade da Microsoft.

Para obter mais informações sobre como usar esse novo ponto de extremidade, consulte [usando o ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – setembro de 2019

**Tipo:** Novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de Terceiros

Em setembro de 2019, adicionamos esses 29 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex Compliance Office™-logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), portal do [IServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [concur Travel e Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [instalações Arc](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [Wide ideas](https://wideideas.online/wideideas/), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT cliente Hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [Penneo,](https://app.penneo.com/) [Hiretual](https://app.testhtm.com/settings/email-integration)e [Cintoo Cloud](https://aec.cintoo.com/login), [Whitestate](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [SSO hospedado online de patrimônio](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Gerenciamento de identidades da Adobe](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), benefícios de [descoberta SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [ITask](https://itask.yipinapp.com/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nova função de leitor global do Azure AD

**Tipo:** Novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso

A partir de 24 de setembro de 2019, vamos começar a distribuir uma nova função de Azure Active Directory (AD) chamada leitor global. Essa distribuição começará com a produção e os clientes de nuvem global (GCC), concluindo todo o mundo em outubro.

A função de leitor global é a contraparte somente leitura para o administrador global. Os usuários nessa função podem ler configurações e informações administrativas entre Microsoft 365 serviços, mas não podem tomar ações de gerenciamento. Criamos a função de leitor global para ajudar a reduzir o número de administradores globais em sua organização. Como as contas de administrador global são poderosas e vulneráveis a ataques, recomendamos que você tenha menos de cinco administradores globais. É recomendável usar a função de leitor global para planejamento, auditorias ou investigações. Também é recomendável usar a função de leitor global em combinação com outras funções de administrador limitadas, como o administrador do Exchange, para ajudar a realizar o trabalho sem exigir a função de administrador global.

A função leitor global funciona com o novo centro de administração do Microsoft 365, centro de administração do Exchange, centro de administração do Team, central de segurança, centro de conformidade, centro de administração do Azure AD e centro de administração do gerenciamento de dispositivos.

>[!NOTE]
> No início da visualização pública, a função de leitor global não funcionará com: SharePoint, Privileged Access Management, Sistema de Proteção de Dados do Cliente, rótulos de sensibilidade, ciclo de vida de equipes, relatórios de equipes & análise de chamadas, gerenciamento de dispositivo de telefone IP de equipes e catálogo de aplicativos de equipes. Todos esses serviços têm a finalidade de trabalhar com a função no futuro.

Para obter mais informações, consulte [permissões de função de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Acessar um servidor de relatório local de seu aplicativo Power BI Mobile usando Proxy de Aplicativo do Azure Active Directory

**Tipo:** Novo recurso  
**Categoria de serviço:** Proxy de Aplicativo  
**Funcionalidade do produto:** Controle de Acesso

A nova integração entre o aplicativo móvel Power BI e o Azure Proxy de Aplicativo do AD permite que você entre com segurança no aplicativo Power BI Mobile e exiba os relatórios de sua organização hospedados no Servidor de Relatórios do Power BI local.

Para obter informações sobre o aplicativo Power BI Mobile, incluindo onde baixar o aplicativo, consulte o [site do Power bi](https://powerbi.microsoft.com/mobile/). Para obter mais informações sobre como configurar o aplicativo móvel Power BI com o Azure Proxy de Aplicativo do AD, consulte [habilitar o acesso remoto para Power bi Mobile com o azure proxy de aplicativo do AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>A nova versão do módulo do PowerShell do AzureADPreview está disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Diretório

Novos cmdlets foram adicionados ao módulo AzureADPreview para ajudar a definir e atribuir funções personalizadas no Azure AD, incluindo:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nova versão do Azure AD Connect

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Diretório

Lançamos uma versão atualizada do Azure AD Connect para clientes de atualização automática. Essa nova versão inclui vários novos recursos, aprimoramentos e correções de bugs. Para obter mais informações sobre essa nova versão, consulte [Azure AD Connect: histórico de lançamento de versões](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>O servidor de autenticação multifator (MFA) do Azure, versão 8.0.2 agora está disponível

**Tipo:** Correção  
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** Segurança de Identidade e Proteção

Se você for um cliente existente, que ativou o servidor MFA antes de 1º de julho de 2019, agora você pode baixar a versão mais recente do servidor MFA (versão 8.0.2). Nesta nova versão, nós:

- Correção de um problema para quando o Azure AD Sync altera um usuário de desabilitado para habilitado, um email é enviado para o usuário.

- Correção de um problema para que os clientes possam atualizar com êxito, ao mesmo tempo em que continuam a usar a funcionalidade de marcas.

- Adicionou o código do país Kosovo (+ 383).

- Adicionado log de auditoria de bypass único para o MultiFactorAuthSvc. log.

- Desempenho aprimorado para o SDK do serviço Web.

- Correção de outros bugs secundários.

A partir de 1º de julho de 2019, a Microsoft parou de oferecer o servidor MFA para novas implantações. Novos clientes que exigem autenticação multifator devem usar a autenticação multifator do Azure baseada em nuvem. Para obter mais informações, consulte [planejando uma implantação da autenticação multifator do Azure baseada em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---
