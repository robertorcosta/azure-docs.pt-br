---
title: Novidades Notas sobre a versão – Azure Active Directory | Microsoft Docs
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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802502"
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

>Seja notificado sobre quando revisitar esta página para atualizações copiando ![e colando esta](./media/whats-new/feed-icon-16x16.png) URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` no leitor de feed do ícone do feed RSS.

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente. Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo no Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Contas do Diretório Ativo do Azure não gerenciado satisfações na atualização B2B para março de 2021

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
**A partir de 31 de março de 2021**, a Microsoft não suportará mais o resgate de convites criando contas e inquilinos do Azure Active Directory (Azure AD) não gerenciados para cenários de colaboração B2B. Em preparação para isso, encorajamos você a optar por enviar por [e-mail autenticação de senha única](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Os usuários com a função de acesso padrão estarão no escopo para o provisionamento

**Tipo:** plano de alteração  
**Categoria de serviço:** provisionamento de aplicativos  
**Capacidade do produto:** Gerenciamento do ciclo de vida da identidade
 
Historicamente, os usuários com a função de acesso padrão estão fora do escopo para o provisionamento. Ouvimos comentários de que os clientes querem que os usuários com essa função estejam no escopo do provisionamento. Estamos trabalhando na implantação de uma alteração para que todas as novas configurações de provisionamento permitam que os usuários com a função de acesso padrão sejam provisionados. Gradualmente, mudaremos o comportamento das configurações de provisionamento existentes para apoiar os usuários de provisionamento com essa função. Nenhuma ação do cliente é necessária. Postaremos uma atualização em nossa [documentação](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) assim que essa mudança estiver no lugar.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>A colaboração Azure AD B2B estará disponível no Microsoft Azure operado por 21Vianet (Azure China 21Vianet) inquilinos

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de colaboração Azure AD B2B serão disponibilizados no Microsoft Azure operado por 21vianet (Azure China 21Vianet), permitindo que os usuários de um inquilino do Azure China 21Vianet colaborem perfeitamente com os usuários de outros inquilinos do Azure China 21Vianet. [Saiba mais sobre a colaboração Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Colaboração convite redesign de e-mail

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os [e-mails](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) enviados pelo serviço de convite de colaboração Azure AD B2B para convidar os usuários para o diretório serão redesenhados para tornar as informações do convite e os próximos passos do usuário mais claros.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>As alterações da diretiva do HomeRealmDiscovery aparecerão nos registros de auditoria

**Tipo:** corrigido  
**Categoria de serviço:** Auditoria  
**Funcionalidade do produto:** monitoramento e relatórios
 
Corrigimos um bug no qual as alterações na [política HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) não foram incluídas nos registros de auditoria. Agora você poderá ver quando e como a política foi alterada, e por quem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Novos aplicativos federados disponíveis na galeria azure AD App - Março 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros
 
Em março de 2020, adicionamos esses 51 novos aplicativos com suporte à Federação à galeria de aplicativos: 

[Cisco AnyConnect,](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect) [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus,](https://test.plusplus.app/auth/login/azuread-outlook/) [Profit.co SAML App,](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial) [iPoint Service Provider,](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial) [contexxt.ai SPHERE](https://contexxt-sphere.com/login), Wisdom [By Invictus,](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial) [Flare Digital Signage,](https://spark-dev.pixelnebula.com/login) [Logz.io - Observabilidade em Nuvem para Engenheiros,](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial) [SpectrumU,](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial) [BizzContact,](https://bizzcontact.app/) [Elqano SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial) [Mercado SignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas,](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial) [FCM HUB,](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial) [RIB A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog,](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [Zscaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One,](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [WatchTeams,](https://www.devfinition.com/) [Aster](https://demo.asterapp.io/login), [Skills Fluxo de trabalho,](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial) [Node Insight,](https://admin.nodeinsight.com/AADLogin.aspx) [Plataforma IP,](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial) [InVision,](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial) [Pipedrive,](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial) [Showcase Workshop,](https://app.showcaseworkshop.com/) [Plataforma de Integração Greenlight, Greenlight](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial) [Compliant Access Management,](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial) [Grok Learning,](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial) [Miradore Online,](https://login.online.miradore.com/) [Khoros Care,](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial) [AskYourTeam,](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [TruNarrative,](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [Smartwaiver,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Bizagi Studio para Automação de Processos Digitais,](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) [insuiteX,](https://www.insuite.jp/) [sybo,](https://www.systexsoftware.com.tw/) [Britive,](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [WhosOffice,](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial) [E-days,](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial) [Kollective SDN,](https://portal.kollective.app/login) [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360,](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial) [Campus Café,](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [Catchpoint,](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Colaboração Azure AD B2B disponível em inquilinos do governo azure

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de colaboração Azure AD B2B estão agora disponíveis entre alguns inquilinos do governo azure.  Para saber se o seu inquilino é capaz de usar esses recursos, siga as instruções em Como posso dizer se a [colaboração B2B está disponível no meu inquilino do Governo dos EUA do Azure?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Integração do Azure Monitor para Azure Logs já está disponível no Governo do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A integração do Azure Monitor com os logs Azure AD já está disponível no Governo Azure. Você pode encaminhar os Logs Ad do Azure (Logs de auditoria e login) para uma conta de armazenamento, Event Hub e Log Analytics. Por favor, confira a [documentação detalhada,](https://aka.ms/aadlogsinamd) bem como [os planos de implantação para relatórios e monitoramento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) de cenários Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Atualização de proteção de identidade no governo azure

**Tipo:** novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Estamos entusiasmados em compartilhar que agora implementamos a experiência atualizada de  [Proteção de Identidade Azure AD](https://aka.ms/IdentityProtectionDocs)no [portal do Governo Microsoft Azure](https://portal.azure.us/). Para obter mais informações, consulte nosso [post no blog de anúncios](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Recuperação de desastres: Baixe e armazene sua configuração de provisionamento

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Capacidade do produto:** Gerenciamento do ciclo de vida da identidade
 
O serviço de provisionamento Azure AD fornece um rico conjunto de recursos de configuração. Os clientes precisam ser capazes de salvar sua configuração para que possam se referir a ela mais tarde ou reverter para uma versão boa conhecida. Adicionamos a capacidade de baixar sua configuração de provisionamento como um arquivo JSON e carregá-la quando você precisar. [Saiba mais](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>O SSPR (reset de senha de autoatendimento) agora requer dois portões para administradores no Microsoft Azure operado sivianet (Azure China 21Vianet) 

**Tipo:** recurso alterado  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Anteriormente no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet), administradores usando reset de senha de autoatendimento (SSPR) para redefinir suas próprias senhas precisavam de apenas um "portão" (desafio) para provar sua identidade. Em nuvens públicas e outras nacionais, os admins geralmente devem usar dois portões para provar sua identidade ao usar SSPR. Mas como não suportamos SMS ou chamadas telefônicas no Azure China 21Vianet, permitimos a redefinição de senha de um portão por administradores.

Estamos criando paridade de recursos SSPR entre o Azure China 21Vianet e a nuvem pública. Daqui para frente, os admins devem usar dois portões ao usar SSPR. Serão suportadas notificações e códigos de aplicativos de SMS, chamadas telefônicas e authenticator. [Saiba mais](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>O comprimento da senha é limitado a 256 caracteres

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Capacidade do produto:** Autenticação do usuário
 
Para garantir a confiabilidade do serviço Azure AD, as senhas do usuário agora são limitadas em comprimento a 256 caracteres. Os usuários com senhas mais longas do que isso serão solicitados a alterar sua senha no login subsequente, seja entrando em contato com seu admin ou usando o recurso de redefinição de senha de autoatendimento.

Esta alteração foi habilitada em 13 de março de 2020, às 10:00 PST (18:00 UTC), e o erro é AADSTS 50052, InvalidPasswordExceedsMaxLength. Consulte o [aviso de mudança de quebra](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) para obter mais detalhes.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Os logs de login do Azure AD já estão disponíveis para todos os inquilinos gratuitos através do portal Azure

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A partir de agora, os clientes que possuem inquilinos gratuitos podem acessar os [logins do Azure AD no portal Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) por até 7 dias. Anteriormente, os logs de login estavam disponíveis apenas para clientes com licenças Azure Active Directory Premium. Com essa mudança, todos os inquilinos podem acessar esses registros através do portal.

> [!NOTE]
> Os clientes ainda precisam de uma licença premium (Azure Active Directory Premium P1 ou P2) para acessar os logs de login através da Microsoft Graph API e do Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Opção de depreciação de grupos em todo o diretório a partir de Configurações Gerais de Grupos no portal Azure

**Tipo:** preterido  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Para fornecer uma maneira mais flexível para os clientes criarem grupos em todo o diretório que melhor atendam às suas necessidades, substituímos a opção **Grupos em todo** o Diretório das configurações**gerais** de **grupos** > no portal Azure por um link para a [documentação do grupo dinâmico.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) Melhoramos nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os usuários que incluem ou excluem usuários convidados.

---

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="upcoming-changes-to-custom-controls"></a>Próximas alterações para controles personalizados

**Tipo:** plano de alteração  
**Categoria de serviço:** Amf  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Estamos planejando substituir a visualização atual de controles personalizados por uma abordagem que permite que os recursos de autenticação fornecidos por parceiros funcionem perfeitamente com o administrador do Azure Active Directory e experiências do usuário final. Hoje, as soluções MFA parceiras enfrentam as seguintes limitações: elas funcionam somente após a entrada de uma senha; eles não servem como MFA para autenticação intensificada em outros cenários-chave; e eles não se integram com funções de gerenciamento de credenciais do usuário final ou administrativas. A nova implementação permitirá que fatores de autenticação fornecidos por parceiros trabalhem em conjunto com fatores incorporados para cenários-chave, incluindo registro, uso, reivindicações de MFA, intensificação da autenticação, emissão de relatórios e registro. 

Os controles personalizados continuarão a ser suportados na pré-visualização ao lado do novo design até que ele atinja a disponibilidade geral. Nesse ponto, daremos aos clientes tempo para migrar para o novo design. Devido às limitações da abordagem atual, não embarcaremos novos provedores até que o novo design esteja disponível. Estamos trabalhando em estreita colaboração com clientes e provedores e comunicaremos a linha do tempo à medida que nos aproximamos. [Saiba mais](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Pontuação segura de identidade - atualizações de ação de melhoria do MFA

**Tipo:** plano de alteração  
**Categoria de serviço:** Amf  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Para refletir a necessidade de as empresas garantirem a maior segurança ao aplicar políticas que trabalham com seus negócios, o Microsoft Secure Score está removendo três ações de melhoria centradas em autenticação multifatorial (MFA) e adicionando duas.

As seguintes ações de melhoria serão removidas:

- Registre todos os usuários para MFA
- Exigir MFA para todos os usuários
- Exigir Funções privilegiadas do MFA para Azure AD

Serão adicionadas as seguintes ações de melhoria:

- Certifique-se de que todos os usuários podem completar o MFA para acesso seguro
- Exigir MFA para funções administrativas

Essas novas ações de melhoria exigirão o registro de seus usuários ou admins para MFA em todo o seu diretório e estabelecer o conjunto certo de políticas que se ajustem às suas necessidades organizacionais. O objetivo principal é ter flexibilidade, garantindo que todos os seus usuários e administradores possam autenticar com vários fatores ou solicitações de verificação de identidade baseadas em riscos. Isso pode assumir a forma de definir padrões de segurança que permitem que a Microsoft decida quando desafiar os usuários para o MFA ou ter várias políticas que aplicam decisões escopo. Como parte dessas atualizações de ação de melhoria, as políticas de proteção da linha de base não serão mais incluídas nos cálculos de pontuação. [Leia mais sobre o que está por vir no Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Seleção SKU de Serviços de Domínio Ad do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
** Capacidade do produto: ** Serviços de Domínio do Azure AD
 
Ouvimos comentários de que os clientes do Azure AD Domain Services querem mais flexibilidade na seleção de níveis de desempenho para suas instâncias. A partir de 1º de fevereiro de 2020, mudamos de um modelo dinâmico (onde o Azure AD determina o desempenho e o nível de preços com base na contagem de objetos) para um modelo de autosseleção. Agora, os clientes podem escolher um nível de desempenho que corresponda ao seu ambiente. Essa mudança também nos permite habilitar novos cenários, como Florestas de Recursos e recursos Premium, como backups diários. A contagem de objetos agora é ilimitada para todas as SKUs, mas continuaremos a oferecer sugestões de contagem de objetos para cada nível.

**Nenhuma ação imediata do cliente é necessária.** Para os clientes existentes, o nível dinâmico que estava em uso em 1º de fevereiro de 2020 determina o novo nível padrão. Não há preço ou impacto de desempenho como resultado dessa mudança. Daqui para frente, os clientes do Azure AD DS precisarão avaliar os requisitos de desempenho à medida que o tamanho do diretório e as características da carga de trabalho mudam. A troca entre os níveis de serviço continuará sendo uma operação sem inatividade, e não mudaremos automaticamente os clientes para novos níveis com base no crescimento de seu diretório. Além disso, não haverá aumento de preços, e novos preços se alinharão ao nosso modelo de faturamento atual. Para obter mais informações, consulte a [documentação Azure AD DS SKUs](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) e a [página de preços do Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Novos aplicativos federados disponíveis na galeria de aplicativos Azure AD - fevereiro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros
 
Em fevereiro de 2020, adicionamos esses 31 novos aplicativos com suporte à Federação à galeria de aplicativos: 

Plataforma de [patentes IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure,](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial) [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), In Case of Crisis - Portal [Online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), BIC [Cloud Design,](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial) [Apicultor Azure AD Data Connector,](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), New Relic [(Versão Limitada),](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) [Thulium,](https://admin.thulium.com/login/instance) [Ticket Manager,](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial) [Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda,](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Novos conectores de provisionamento na Azure AD Application Gallery - Fevereiro 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos recém-integrados:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando provisionamento automatizado de contas de usuário, consulte [Automatificar o provisionamento do usuário para aplicativos SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Suporte a Azure AD para chaves de segurança FIDO2 em ambientes híbridos

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Capacidade do produto:** Autenticação do usuário
 
Estamos anunciando a visualização pública do suporte ao Azure AD para chaves de segurança FIDO2 em ambientes Híbridos. Os usuários agora podem usar as chaves de segurança FIDO2 para entrar em seus dispositivos Hybrid Azure AD que se juntaram ao Windows 10 e obter o login perfeito em seus recursos no local e na nuvem. O suporte para ambientes híbridos tem sido o recurso mais solicitado de nossos clientes sem senha desde que lançamos inicialmente a pré-visualização pública para suporte ao FIDO2 em dispositivos aderidos ao Azure AD. A autenticação sem senha usando tecnologias avançadas como biometria e criptografia de chaves públicas/privadas fornece conveniência e facilidade de uso enquanto está segura. Com essa visualização pública, agora você pode usar autenticação moderna como chaves de segurança FIDO2 para acessar os recursos tradicionais do Active Directory. Para obter mais informações, acesse [o SSO para obter recursos no local.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Para começar, visite habilitar as chaves de [segurança FIDO2 para o seu inquilino](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) para obter instruções passo a passo. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>A nova experiência my account está agora geralmente disponível

**Tipo:** recurso alterado  
**Categoria de serviço:** Meu perfil/conta  
**Capacidade do produto:** Experiências do usuário final
 
Minha conta, a única parada para todas as necessidades de gerenciamento de contas de usuário final, agora está geralmente disponível! Os usuários finais podem acessar este novo site via URL, ou no cabeçalho da nova experiência My Apps. Saiba mais sobre todos os recursos de autoatendimento que a nova experiência oferece no [Portal Minha Conta Visão Geral](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Atualização da URL do meu site da conta para myaccount.microsoft.com

**Tipo:** recurso alterado  
**Categoria de serviço:** Meu perfil/conta  
**Capacidade do produto:** Experiências do usuário final
 
A nova experiência do usuário final da `https://myaccount.microsoft.com` Minha Conta estará atualizando sua URL para o próximo mês. Encontre mais informações sobre a experiência e todos os recursos de autoatendimento da conta que oferece aos usuários finais no [portal Minha Conta](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)ajuda .

---
 
## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>O novo portal My Apps já está disponível em geral

**Tipo:** plano de alteração  
**Categoria de serviço:** Meus Aplicativos  
**Capacidade do produto:** Experiências do usuário final
 
Atualize sua organização para o novo portal Meus Aplicativos que agora está geralmente disponível! Encontre mais informações sobre o novo portal e coleções no [Create collections no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Espaços de trabalho no Azure AD foram renomeados para coleções

**Tipo:** recurso alterado  
**Categoria de serviço:** Meus Aplicativos   
**Capacidade do produto:** Experiências do usuário final
 
Os espaços de trabalho, os administradores de filtros podem configurar para organizar os aplicativos de seus usuários, agora serão chamados de coleções. Encontre mais informações sobre como configurá-las no [Create collections no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Phone inscrição e login usando política personalizada (Visualização Pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Com o número de telefone cadasignado e o login, desenvolvedores e empresas podem permitir que seus clientes se inscrevam e se inscrevam usando uma senha única enviada para o número de telefone do usuário via SMS. Esse recurso também permite que o cliente altere seu número de telefone se perder o acesso ao telefone. Com o poder das políticas personalizadas, o login e o login de telefone permitem que desenvolvedores e empresas comuniquem sua marca através da personalização de páginas. Descubra como configurar o [login e o login do telefone com políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Novos conectores de provisionamento na Azure AD Application Gallery - Janeiro 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos recém-integrados:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando provisionamento automatizado de contas de usuário, consulte [Automatificar o provisionamento do usuário para aplicativos SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Novos aplicativos federados disponíveis na galeria de aplicativos Azure AD - janeiro 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros
 
Em janeiro de 2020, adicionamos esses 33 novos aplicativos com suporte à Federação à galeria de aplicativos: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Nuvem de Borda Rápida,](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial) [Terraform Enterprise,](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial) [Spintr SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial) [Abibot Netlogistik,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot,](https://leavebot.io/#home) [DataCamp,](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [TripActions,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [SmartWork,](https://www.intumit.com/english/SmartWork.html) [Dotcom-Monitor,](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial) [SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft, e JDE,](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial) [Hospedado MyCirqa SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial) [Yuhu Property Management Platform,](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial) [LumApps,](https://sites.lumapps.com/login) [Upwork Enterprise,](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial) [Talentsoft,](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial) [SmartDB para Equipes Microsoft,](http://teams.smartdb.jp/login/) [PressPage,](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial) [ContractSafe Saml2 SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial) [Maxient Conduct Manager Software,](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial) [Help shift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365,](https://www.portaltalk.com/) [CoreView,](https://portal.coreview.com/) [Squelch Cloud Office365 Conector,](https://laxmi.squelch.io/login) [Autenticação pingFlow,](https://app-staging.pingview.io/) [PrinterLogic SaaS,](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial) [Taskize Connect,](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial) [Sandwai,](https://app.sandwai.com/) [EZRentOut,](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [AssetSonar,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Duas novas detecções de proteção de identidade

**Tipo:** novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Adicionamos dois novos tipos de detecção vinculadas ao Identity Protection: regras suspeitas de manipulação de caixa de entrada e viagens impossíveis. Essas detecções offline são descobertas pelo Microsoft Cloud App Security (MCAS) e influenciam o usuário e o risco de login na Proteção de Identidade. Para obter mais informações sobre essas detecções, consulte nossos [tipos de risco de login](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Mudança de quebra: Fragmentos uri não serão realizados através do redirecionamento de login

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Capacidade do produto:** Autenticação do usuário
 
A partir de 8 de fevereiro de 2020, quando uma solicitação é enviada ao login.microsoftonline.com para assinar em um usuário, o serviço anexará um fragmento vazio à solicitação.  Isso evita uma classe de ataques de redirecionamento, garantindo que o navegador elimine qualquer fragmento existente na solicitação. Nenhuma aplicação deve ter uma dependência desse comportamento. Para obter mais informações, consulte [Quebrando alterações](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) na documentação da plataforma de identidade da Microsoft.

---

## <a name="december-2019"></a>Dezembro de 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integre o provisionamento de fatores de sucesso do SAP no Ad do Azure e no Local (Visualização Pública)

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Capacidade do produto:** Gerenciamento do ciclo de vida da identidade

Agora você pode integrar o SAP SuccessFactors como uma fonte de identidade autoritária no Azure AD. Essa integração ajuda você a automatizar o ciclo de vida de identidade de ponta a ponta, incluindo o uso de eventos baseados em RH, como novas contratações ou terminações, para controlar o provisionamento de contas Azure AD.

Para obter mais informações sobre como configurar o provisionamento de entrada SAP SuccessFactors no Azure AD, consulte o tutorial [de provisionamento automático Configure SAP SuccessFactors.](https://aka.ms/SAPSuccessFactorsInboundTutorial)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Suporte para e-mails personalizados no Azure AD B2C (Visualização Pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode usar o Azure AD B2C para criar e-mails personalizados quando seus usuários se inscreverem para usar seus aplicativos. Usando DisplayControls (atualmente em pré-visualização) e um provedor de e-mail de terceiros (como, [SendGrid](https://sendgrid.com/), [SparkPost,](https://sparkpost.com/)ou uma API rest personalizada), você pode usar seu próprio modelo de e-mail, **de** endereço e texto de assunto, bem como as configurações de localização e senha personalizada (OTP).

Para obter mais informações, consulte [verificação de e-mail personalizada no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Substituição de políticas de linha de base por padrões de segurança

**Tipo:** recurso alterado  
**Categoria de serviço:** outro  
**Capacidade do produto:** Segurança e proteção de identidade

Como parte de um modelo seguro por padrão para autenticação, estamos removendo as políticas de proteção de linha de base existentes de todos os inquilinos. Esta remoção está prevista para ser concluída no final de fevereiro. A substituição dessas políticas de proteção de linha de base são os padrões de segurança. Se você estiver usando políticas de proteção de linha de base, você deve planejar para passar para a nova política de padrões de segurança ou para o Acesso Condicional. Se você não usou essas políticas, não há nenhuma ação para você tomar.

Para obter mais informações sobre os novos padrões de segurança, consulte [Quais são os padrões de segurança?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Para obter mais informações sobre políticas de acesso condicional, consulte [políticas de acesso condicional comum](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Novembro de 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Suporte para o atributo SameSite e Chrome 80

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (logons)  
**Capacidade do produto:** Autenticação do usuário

Como parte de um modelo seguro por padrão para cookies, o navegador Chrome `SameSite` 80 está mudando a forma como trata os cookies sem o atributo. Qualquer cookie que não `SameSite` especificar o atributo será `SameSite=Lax`tratado como se estivesse definido para , o que resultará no bloqueio do Chrome em certos cenários de compartilhamento de cookies entre domínios dos quais seu aplicativo pode depender. Para manter o comportamento mais antigo `SameSite=None` do Chrome, `Secure` você pode usar o atributo e adicionar um atributo adicional, para que cookies entre sites só possam ser acessados em conexões HTTPS. O Chrome está programado para concluir essa mudança até 4 de fevereiro de 2020.

Recomendamos que todos os nossos desenvolvedores testem seus aplicativos usando esta orientação:

- Defina o valor padrão para a configuração **Usar cookies seguro** como **Sim**.

- Defina o valor padrão para o atributo **SameSite** como **Nenhum**.

- Adicione um `SameSite` atributo adicional de **Secure**.

Para obter mais informações, consulte [As próximas alterações de cookies do SameSite no ASP.NET e ASP.NET interrupção do Núcleo](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e potencial para sites de [clientes e produtos e serviços da Microsoft na versão 79 do Chrome e posterior .](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Novo hotfix para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** corrigido  
**Categoria de serviço:** Gerenciador de identidade da Microsoft  
**Capacidade do produto:** Gerenciamento do ciclo de vida da identidade

Um pacote hotfix rollup (build 4.6.34.0) está disponível para o Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Este pacote de rollup resolve problemas e adiciona melhorias descritas na seção "Problemas corrigidos e melhorias adicionadas nesta atualização".

Para obter mais informações e baixar o pacote hotfix, consulte [o Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Update Rollup está disponível](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Novo relatório de atividades do aplicativo AD FS para ajudar a migrar aplicativos para o Azure AD (Public Preview)

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** SSO

Use o novo relatório de atividades de aplicativos do Active Directory Federation Services (AD FS), no portal Azure, para identificar quais de seus aplicativos são capazes de serem migrados para o Azure AD. O relatório avalia todos os aplicativos AD FS para compatibilidade com o Azure AD, verifica quaisquer problemas e dá orientações sobre a preparação de aplicativos individuais para migração.

Para obter mais informações, consulte [Use o relatório de atividade do aplicativo AD FS para migrar aplicativos para o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Novo fluxo de trabalho para os usuários solicitarem o consentimento do administrador (Visualização Pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** Controle de Acesso

O novo fluxo de trabalho de consentimento do admin dá aos admins uma maneira de conceder acesso a aplicativos que requerem aprovação do admin. Se um usuário tentar acessar um aplicativo, mas não conseguir fornecer consentimento, agora ele pode enviar um pedido de aprovação administrativa. A solicitação é enviada por e-mail e colocada em uma fila acessível a partir do portal Azure, para todos os administradores que foram designados como revisores. Após um revisor tomar uma ação sobre uma solicitação pendente, os usuários solicitantes são notificados da ação.

Para obter mais informações, consulte [Configure o fluxo de trabalho de consentimento de administrador (visualização)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nova experiência de configuração do Token de registros de aplicativos azure ad para gerenciar sinistros opcionais (Visualização Pública)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** experiência de desenvolvedor

O novo **azure AD App Registrations Token configuration blade** no portal Azure agora mostra aos desenvolvedores de aplicativos uma lista dinâmica de reivindicações opcionais para seus aplicativos. Essa nova experiência ajuda a simplificar as migrações de aplicativos Ad do Azure e minimizar as configurações erradas de sinistros opcionais.

Para obter mais informações, consulte [Fornecer reivindicações opcionais ao seu aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Novo fluxo de trabalho de aprovação em duas etapas no gerenciamento de direitos AD do Azure (Visualização Pública)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Capacidade do produto:** Gestão de Direitos

Introduzimos um novo fluxo de trabalho de aprovação em duas etapas que permite que você exija que dois aprovadores aprovem a solicitação de um usuário a um pacote de acesso. Por exemplo, você pode configurá-lo para que o gerente do usuário solicitante seja aprovado primeiro e, em seguida, você também pode exigir que um proprietário de recursos aprove. Se um dos aprovados não aprovar, o acesso não é concedido.

Para obter mais informações, consulte [Alterar as configurações de solicitação e aprovação de um pacote de acesso no gerenciamento de direitos AD do Azure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizações para a página Meus Aplicativos, juntamente com novos espaços de trabalho (Visualização Pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** integração de terceiros

Agora você pode personalizar a forma como os usuários da sua organização visualizam e acessar a experiência atualizada dos Meus Aplicativos. Essa nova experiência também inclui o novo recurso de espaços de trabalho, o que facilita a busca e organização de aplicativos pelos usuários.

Para obter mais informações sobre a nova experiência do My Apps e criar espaços de trabalho, consulte [Criar espaços de trabalho no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Suporte ao ID social do Google para colaboração Azure AD B2B (Disponibilidade Geral)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Capacidade do produto:** Autenticação do usuário

O novo suporte para o uso de IDs sociais do Google (contas Gmail) no Azure AD ajuda a tornar a colaboração mais simples para seus usuários e parceiros. Não há mais necessidade de seus parceiros criarem e gerenciarem uma nova conta específica da Microsoft. O Microsoft Teams agora suporta totalmente os usuários do Google em todos os clientes e nos pontos finais de autenticação comuns e relacionados a inquilinos.

Para obter mais informações, consulte [Adicionar o Google como um provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Suporte móvel do Microsoft Edge para acesso condicional e logon único (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

Azure AD para Microsoft Edge no iOS e Android agora suporta a logon único azure AD e acesso condicional:

- **SSO (Single Sign-on) do Microsoft Edge:** O login único agora está disponível em clientes nativos (como o Microsoft Outlook e o Microsoft Edge) para todos os aplicativos conectados ao Azure AD.

- **Acesso condicional do Microsoft Edge:** Por meio de políticas de acesso condicional baseadas em aplicativos, seus usuários devem usar navegadores protegidos pelo Microsoft Intune, como o Microsoft Edge.

Para obter mais informações sobre acesso condicional e SSO com o Microsoft Edge, consulte o [Microsoft Edge Mobile Support for Conditional Access e o Single Sign-on Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blog post. Para obter mais informações sobre como configurar seus aplicativos clientes usando [acesso condicional baseado em aplicativos](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) ou acesso condicional baseado em [dispositivos,](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)consulte [Gerenciar acesso à Web usando um navegador protegido por políticas da Microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gerenciamento de direitos Azure AD (Disponibilidade Geral)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Capacidade do produto:** Gestão de Direitos

O gerenciamento de direitos ad do Azure é um novo recurso de governança de identidade, que ajuda as organizações a gerenciar identidade e acessar o ciclo de vida em escala. Esse novo recurso ajuda a automatizar fluxos de trabalho de solicitação de acesso, atribuições de acesso, avaliações e expiração em grupos, aplicativos e sites SharePoint Online.

Com o gerenciamento de direitos do Azure AD, você pode gerenciar de forma mais eficiente o acesso tanto para funcionários quanto para usuários fora da sua organização que precisam de acesso a esses recursos.

Para obter mais informações, consulte [O que é o gerenciamento de direitos do Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatize o provisionamento de contas de usuário para esses aplicativos SaaS recém-suportados

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros  

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos recém-integrados:

[Serviço de autenticação de identidade da plataforma de nuvem SAP,](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial) [RingCentral,](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial) [SpaceIQ,](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial) [Miro,](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial) [Cloudgate,](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial) [Infor CloudSuite,](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial) [Software OfficeSpace,](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial) [Matriz prioritária](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando provisionamento automatizado de contas de usuário, consulte [Automatificar o provisionamento do usuário para aplicativos SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Novos aplicativos federados disponíveis na galeria azure AD App - Novembro 2019

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros

Em novembro de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação à galeria de aplicativos:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Acesso Azul para Membros (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galeria de aplicativos Ad azure novo e melhorado

**Tipo:** recurso alterado  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** SSO

Atualizamos a galeria de aplicativos Azure AD para facilitar a busca de aplicativos pré-integrados que suportam provisionamento, OpenID Connect e SAML no seu inquilino do Azure Active Directory.

Para obter mais informações, consulte [Adicionar um aplicativo ao inquilino do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Aumento do limite de comprimento de definição de função do aplicativo de 120 para 240 caracteres

**Tipo:** recurso alterado  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** SSO

Ouvimos dos clientes que o limite de comprimento para o valor de definição de função do aplicativo em alguns aplicativos e serviços é muito curto em 120 caracteres. Em resposta, aumentamos o comprimento máximo da definição de valor do papel para 240 caracteres.

Para obter mais informações sobre como usar definições de função específicas do aplicativo, consulte [Adicionar funções de aplicativo em seu aplicativo e recebê-las no token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Outubro de 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Depreciação da imsiculaçãoA API de risco de risco de identificação do Azure AD  

**Tipo:** plano de alteração  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Em resposta ao feedback do desenvolvedor, os assinantes do Azure AD Premium P2 agora podem realizar consultas complexas nos dados de detecção de risco do Azure AD Identity Protection usando a nova API de detecção de risco para o Microsoft Graph. A versão beta da API [IdentityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) parará de retornar dados por volta de **10 de janeiro de 2020**. Se sua organização estiver usando a API identityRiskEvent, você deve fazer a transição para a nova API de detecção de risco.

Para obter mais informações sobre a nova API de detecção de risco, consulte a [documentação de referência da API de detecção de risco](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Suporte ao Proxy do aplicativo para o Atributo SameSite e chrome 80

**Tipo:** plano de alteração  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Algumas semanas antes da versão do navegador Chrome 80, planejamos atualizar como os cookies do Application Proxy tratam o atributo **SameSite.** Com a versão do Chrome 80, qualquer cookie que não especificar o atributo **SameSite** será tratado como se tivesse sido definido como `SameSite=Lax`.

Para ajudar a evitar impactos potencialmente negativos devido a essa mudança, estamos atualizando o acesso ao Proxy do aplicativo e cookies de sessão por:

- Definindo o valor padrão para a configuração **Usar cookies seguro** como **Sim**.

- Definindo o valor padrão para o atributo **SameSite** em **Nenhum**.

    >[!NOTE]
    > Os cookies de acesso ao Proxy de aplicativos sempre foram transmitidos exclusivamente por canais seguros. Essas alterações só se aplicam a cookies de sessão.

Para obter mais informações sobre as configurações de cookies do Proxy do aplicativo, consulte [configurações de cookies para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Os registros de aplicativos (legado) e o gerenciamento convergente de aplicativos do Portal de Registro de Aplicativos (apps.dev.microsoft.com) não estarão mais disponíveis

**Tipo:** plano de alteração  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** experiência de desenvolvedor

Em um futuro próximo, os usuários com contas Azure AD não poderão mais se cadastrar e gerenciar aplicativos convergentes usando o Portal de Registro de Aplicativos (apps.dev.microsoft.com), ou registrar e gerenciar aplicativos na experiência de registros de aplicativos (legados) no portal Azure.

Para saber mais sobre a experiência de inscrições do novo App, consulte as [inscrições do App no guia de treinamento do portal Azure.](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Os usuários não são mais obrigados a se recadastrar durante a migração do MFA por usuário para o MFA baseado em acesso condicional

**Tipo:** corrigido  
**Categoria de serviço:** Amf  
**Funcionalidade do produto:** segurança e proteção da identidade

Corrigimos um problema conhecido no qual quando os usuários eram obrigados a se recadastrar se estavam desativados para autenticação multifatorial (MFA) por usuário e, em seguida, habilitados para MFA através de uma política de Acesso Condicional.

Para exigir que os usuários se registrem, você pode selecionar a opção **De recadastramento Necessário mfa** dos métodos de autenticação do usuário no portal Azure AD. Para obter mais informações sobre a migração de usuários de MFA por usuário para MFA baseado em acesso condicional, consulte [Converter usuários de MFA por usuário para MFA baseado em Acesso Condicional](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Novos recursos para transformar e enviar reclamações em seu token SAML

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** SSO

Adicionamos recursos adicionais para ajudá-lo a personalizar e enviar reclamações em seu token SAML. Esses novos recursos incluem:

- Funções adicionais de transformação de sinistros, ajudando você a modificar o valor que você envia na reclamação.

- Capacidade de aplicar múltiplas transformações a uma única reivindicação.

- Capacidade de especificar a fonte de sinistro, com base no tipo de usuário e no grupo ao qual o usuário pertence.

Para obter informações detalhadas sobre esses novos recursos, incluindo como usá-los, consulte [Personalizar reivindicações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nova página de logins para usuários finais no Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** monitoramento e relatórios

Adicionamos uma nova página **Meus Logins** (https://mysignins.microsoft.com) para permitir que os usuários da sua organização visualizem seu histórico recente de login para verificar qualquer atividade incomum. Esta nova página permite que seus usuários vejam:

- Se alguém está tentando adivinhar sua senha.

- Se um invasor fez um sucesso em sua conta e em que local.

- Quais aplicativos o atacante tentou acessar.

Para obter mais informações, consulte os Usuários agora podem verificar seu histórico de login para um blog [de atividades incomuns.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migração de serviços de domínio Ad do Azure (Azure AD DS) de redes virtuais clássicas para a azure Resource Manager

**Tipo:** novo recurso  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
** Capacidade do produto: ** Serviços de Domínio do Azure AD

Para nossos clientes que estiveram presos em redes virtuais clássicas - temos ótimas notícias para você! Agora você pode executar uma migração única de uma rede virtual clássica para uma rede virtual do Gerenciador de Recursos existente. Depois de passar para a rede virtual do Gerenciador de recursos, você poderá aproveitar os recursos adicionais e atualizados, como políticas de senha sinuosas, notificações de e-mail e registros de auditoria.

Para obter mais informações, consulte [Preview - Migrate Azure AD Domain Services do modelo clássico de rede virtual para Gerenciador de recursos](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Atualizações para o layout do contrato de página Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Introduzimos algumas novas alterações na versão 1.2.0 do contrato de página do Azure AD B2C. Nesta versão atualizada, agora você pode controlar a ordem de carregamento de seus elementos, o que também pode ajudar a parar a cintilação que acontece quando a folha de estilo (CSS) é carregada.

Para obter uma lista completa das alterações feitas no contrato da página, consulte o [registro de alteração de versão .](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizar para a página Meus Aplicativos, juntamente com novos espaços de trabalho (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** Controle de Acesso

Agora você pode personalizar a maneira como os usuários da sua organização visualizam e acessam a nova experiência do My Apps, inclusive usando o novo recurso de espaços de trabalho para facilitar a busca de aplicativos. A nova funcionalidade de espaços de trabalho funciona como um filtro para os aplicativos aos quais os usuários da sua organização já têm acesso.

Para obter mais informações sobre como implementar a nova experiência do My Apps e criar espaços de trabalho, consulte [Criar espaços de trabalho no portal Meus Aplicativos (preview).](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Suporte para o modelo mensal de faturamento ativo do usuário (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

O Azure AD B2C agora suporta faturamento mensal de usuários ativos (MAU). O faturamento mau é baseado no número de usuários únicos com atividade de autenticação durante um mês calendário. Os clientes existentes podem mudar para este novo método de cobrança a qualquer momento.

A partir de 1º de novembro de 2019, todos os novos clientes serão cobrados automaticamente usando este método. Este método de cobrança beneficia os clientes através de benefícios de custo e a capacidade de planejar com antecedência.

Para obter mais informações, consulte [Atualizar para o modelo de cobrança de usuários ativos mensais](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Novos aplicativos federados disponíveis na galeria azure AD App - Outubro 2019

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos corporativos  
**Funcionalidade do produto:** integração de terceiros

Em outubro de 2019, adicionamos esses 35 novos aplicativos com suporte à Federação à galeria de aplicativos:

[Em Caso de Crise – Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat,](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial) [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), Conteúdo [Ful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europa](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - EUA](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), Mail [Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), Talking Email for [Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Talking Email for Office 365 Direct (iPhone / Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Item do menu de segurança consolidado no portal Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Agora você pode acessar todos os recursos de segurança disponíveis do Azure AD a partir do novo item do menu **De Segurança** e da barra **de pesquisa,** no portal Azure. Além disso, a nova página de landing **da Security,** chamada **Security - Getting started**, fornecerá links para nossa documentação pública, orientação de segurança e guias de implantação.

O novo menu **De Segurança** inclui:

- Acesso Condicional
- Identity Protection
- Central de Segurança
- Classificação de segurança de identidade
- Métodos de autenticação
- Amf
- Relatórios de risco - Usuários arriscados, logins arriscados, detecções de risco
- E muito mais...

Para obter mais informações, consulte [Segurança - Começando](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Política de expiração de grupos do Office 365 aprimorada com autorenovação

**Tipo:** recurso alterado  
**Categoria de serviço:** gerenciamento de grupo  
**Capacidade do produto:** Gerenciamento do ciclo de vida da identidade

A política de expiração de grupos do Office 365 foi aprimorada para renovar automaticamente grupos que estão ativamente em uso por seus membros. Os grupos serão renovados automaticamente com base na atividade do usuário em todos os aplicativos do Office 365, incluindo Outlook, SharePoint e Teams.

Esse aprimoramento ajuda a reduzir as notificações de expiração do grupo e ajuda a garantir que grupos ativos continuem disponíveis. Se você já tem uma política de expiração ativa para seus grupos do Office 365, você não precisa fazer nada para ativar essa nova funcionalidade.

Para obter mais informações, consulte [Configurar a política de expiração para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Experiência de criação atualizada do Azure AD Domain Services (Azure AD DS)

**Tipo:** recurso alterado  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
** Capacidade do produto: ** Serviços de Domínio do Azure AD

Atualizamos o Azure AD Domain Services (Azure AD DS) para incluir uma experiência de criação nova e melhorada, ajudando você a criar um domínio gerenciado em apenas três cliques! Além disso, agora você pode carregar e implantar o Azure AD DS a partir de um modelo.

Para obter mais informações, consulte [Tutorial: Crie e configure uma instância de Serviços de Domínio do Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---
