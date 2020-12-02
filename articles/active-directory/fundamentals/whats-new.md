---
title: Quais são as novidades? Notas sobre a versão – Azure Active Directory | Microsoft Docs
description: Saiba o que há de novo no Azure Active Directory; como as notas de versão mais recentes, problemas conhecidos, correções de bugs, funcionalidade preterida e alterações futuras.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b836c28443790466084b1840edcf08dc09dcf4cc
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518270"
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

>Seja notificado sobre quando revisitar esta página para ver atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` no seu leitor de feed ![ícone do leitor de RSS feed](./media/whats-new/feed-icon-16x16.png).

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente. Se você estiver procurando itens com mais de seis meses, poderá encontrá-los em [arquivo morto para o que há de novo no Azure Active Directory](whats-new-archive.md).

---
## <a name="november-2020"></a>Novembro de 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 e 3DES de substituição

**Tipo:** plano de alteração  
**Categoria de serviço:** Todos os aplicativos do Azure AD  
**Funcionalidade do produto:** Padrões

O Azure Active Directory substituirá os seguintes protocolos em Azure Active Directory regiões mundiais até 30 de junho de 2021:

- TLS 1.0
- TLS 1.1
- pacote de codificação 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Os ambientes afetados são:
- Nuvem comercial do Microsoft Azure
- Office 365 GCC e WW

Comunicado relacionado todas as combinações de servidor cliente e navegador-servidor devem usar o TLS 1,2 e os pacotes de codificação modernos para manter uma conexão segura com o Azure Active Directory para os serviços Azure, Office 365 e Microsoft 365. Essa alteração está relacionada a [Azure Active Directory TLS 1,0 & 1,1 e à substituição do conjunto de codificação 3DES no US gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – novembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em novembro de 2020, adicionamos os seguintes 52 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Gerenciamento de despesas de viagem &](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [seletor de arquivos Itslearning](https://pmteam.itslearning.com/), [crises Control](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum – ilha de estudo](https://app.studyisland.com/cfw/login/), [gerente de riscos virtual](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [plataforma de análise de pesquisa](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-recrutar](https://recruit.talview.com/login), Tradutor de tempo real, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [despesamanager](https://api.expense-manager.com/), [netsparkr Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [en-Trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/) [,](https://portal.brightbooking.eu/) [Eva check-in](https://my.evacheckin.com/organization), [HowNow webapp SSO](../saas-apps/hownow-webapp-sso-tutorial.md) [,](../saas-apps/sailpoint-identitynow-tutorial.md) [Compensating Risk avalie](../saas-apps/coupa-risk-assess-tutorial.md), [lucida (todos os produtos](../saas-apps/lucid-tutorial.md)[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [JEDOX](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [trabalho](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [appsec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), avaliação de [frete](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), título [Research](../saas-apps/nature-research-tutorial.md), [Play Digital](https://login.playsignage.com/login)Information, [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [madrugada software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [mentor® por eDriving: Business](https://www.edriving.com/), [gradle Enterprise](https://gradle.com/)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Visualização pública-funções personalizadas para aplicativos empresariais

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
 As [funções personalizadas de RBAC para o gerenciamento de aplicativos empresariais delegado](../users-groups-roles/roles-custom-available-permissions.md) agora estão em visualização pública. Essas novas permissões se baseiam nas funções personalizadas para o gerenciamento de registro de aplicativo, o que permite um controle refinado sobre o acesso dos seus administradores. Ao longo do tempo, serão lançadas permissões adicionais para delegar o gerenciamento do Azure AD.

Alguns cenários comuns de delegação:
- atribuição de usuário e grupos que podem acessar aplicativos de logon único baseados em SAML
- a criação de aplicativos da galeria do Azure AD
- atualização e leitura de configurações básicas do SAML para aplicativos de logon único baseados em SAML
- gerenciamento de certificados de autenticação para aplicativos de logon único baseados em SAML
- atualização dos endereços de email de notificação de entrada de certificados expirados para aplicativos de logon único baseados em SAML
- atualização da assinatura de token SAML e do algoritmo de entrada para aplicativos de logon único baseados em SAML
- criar, excluir e atualizar atributos de usuário e declarações para aplicativos de logon único baseados em SAML
- capacidade de ativar, desativar e reiniciar trabalhos de provisionamento
- atualizações para mapeamento de atributos
- capacidade de ler configurações de provisionamento associadas ao objeto
- capacidade de ler configurações de provisionamento associadas à entidade de serviço
- capacidade de autorizar o acesso ao aplicativo para provisionamento

---

### <a name="azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>O Azure Proxy de Aplicativo do AD dá suporte nativo ao acesso de logon único a aplicativos que usam cabeçalhos para autenticação

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso
 
O proxy de aplicativo Azure Active Directory (Azure AD) nativamente dá suporte ao acesso de logon único a aplicativos que usam cabeçalhos para autenticação. Você pode configurar valores de cabeçalho exigidos por seu aplicativo no Azure AD. Os valores de cabeçalho serão enviados para o aplicativo por meio do proxy de aplicativo. Para saber mais, confira [logon único baseado em cabeçalho para aplicativos locais com aplicativo Azure ad proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Disponibilidade geral-inscrição Azure AD B2C telefone e entrada usando política personalizada

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Com a inscrição e a entrada do número de telefone, os desenvolvedores e as empresas podem permitir que seus clientes se inscrevam e entrem usando uma senha de uso único enviada ao número de telefone do usuário via SMS. Esse recurso também permite que o cliente altere seu número de telefone se eles perderem o acesso ao seu telefone. Com o poder das políticas personalizadas, permita que desenvolvedores e empresas comuniquem sua marca por meio da personalização de página. Descubra como configurar a [inscrição e a entrada do telefone com políticas personalizadas no Azure ad B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – novembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Gerenciamento de identidade do Adobe](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blog](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Com conteúdo](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Jogo – TAC Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Para obter mais informações, consulte [automatizar o provisionamento de usuário para aplicativos SaaS com o Azure ad](../manage-apps/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Visualização pública-Sign-In de email com ProxyAddresses agora implantável por meio de distribuição em etapas

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Os administradores de locatários agora podem usar a distribuição em etapas para implantar Sign-In de email com o ProxyAddresses em grupos específicos do Azure AD. Isso pode ajudar ao experimentar o recurso antes de implantá-lo em todo o locatário por meio da política de descoberta de realm inicial. As instruções para a implantação de Sign-In de email com o ProxyAddresses via distribuição em etapas estão na [documentação](../authentication/howto-authentication-use-email-signin.md)do.
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Versão prévia limitada-diagnóstico de entrada

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
Com a versão prévia inicial do diagnóstico de entrada, os administradores agora podem examinar as entradas do usuário. Os administradores podem receber detalhes contextuais, específicos e relevantes e orientações sobre o que aconteceu durante uma entrada e como corrigir problemas. O diagnóstico está disponível no nível do Azure AD e no diagnóstico condicional e na resolução de lâminas. Os cenários de diagnóstico abordados nesta versão são o acesso condicional, a autenticação multifator e a entrada bem-sucedida.
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Propriedades de entrada desconhecidas aprimoradas

**Tipo:** recurso alterado  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

  As detecções de propriedades de entrada desconhecidas foram atualizadas. Os clientes podem notar mais detecções de propriedades de entrada desconhecidas de alto risco. Para obter mais informações, consulte [o que é risco?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>A atualização de visualização pública do agente de provisionamento de nuvem agora está disponível (versão: 1.1.281.0)

**Tipo:** recurso alterado  
**Categoria de serviço:** Provisionamento de nuvem do Azure AD  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O agente de provisionamento de nuvem foi lançado em visualização pública e agora está disponível por meio do Portal. Esta versão contém várias melhorias, incluindo suporte para GMSA para seus domínios, que fornece melhor segurança, ciclos de sincronização inicial aprimorados e suporte para grupos grandes. Confira o [histórico](../app-provisioning/provisioning-agent-release-version-history.md) de versão de lançamento para obter mais detalhes. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Ponto de extremidade da API da chave de recuperação do BitLocker agora em/informationProtection

**Tipo:** recurso alterado  
**Categoria de serviço:** Gerenciamento de acesso do dispositivo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo
 
Anteriormente, você poderia recuperar as chaves do BitLocker por meio do ponto de extremidade/BitLocker. Eventualmente, vamos substituir esse ponto de extremidade, e os clientes devem começar a consumir a API que agora está em/informationProtection. 

Consulte [API de recuperação do BitLocker](https://docs.microsoft.com/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) para obter atualizações na documentação para refletir essas alterações.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Disponibilidade geral do suporte de proxy de aplicativo para Serviços de Área de Trabalho Remota cliente Web HTML5

**Tipo:** recurso alterado  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso
 
O Azure Proxy de Aplicativo do AD support para o cliente Web do Serviços de Área de Trabalho Remota (RDS) agora está em disponibilidade geral. O cliente Web do RDS permite que os usuários acessem a infraestrutura do Área de Trabalho Remota por meio de qualquer navegador compatível com HTLM5, como o Microsoft Edge, Internet Explorer 11, Google Chrome e assim por diante. Os usuários podem interagir com aplicativos ou áreas de trabalho remotas, como fariam com um dispositivo local de qualquer lugar. 

Usando o Azure Proxy de Aplicativo do AD, você pode aumentar a segurança de sua implantação de RDS impondo políticas de acesso condicional e pré-autenticação para todos os tipos de aplicativos cliente avançados. Para saber mais, consulte [publicar área de trabalho remota com o Azure proxy de aplicativo do AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>O novo serviço de grupo dinâmico avançado está em visualização pública

**Tipo:** recurso alterado  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração
 
O serviço de grupo dinâmico avançado agora está em visualização pública. Novos clientes que criam grupos dinâmicos em seus locatários usarão o novo serviço. O tempo necessário para criar um grupo dinâmico será proporcional ao tamanho do grupo que está sendo criado, em vez do tamanho do locatário. Essa atualização melhorará o desempenho de locatários grandes significativamente quando os clientes criarem grupos menores. 

O novo serviço também visa concluir a adição e a remoção de membros devido a alterações de atributo em alguns minutos. Além disso, as falhas de processamento único não bloquearão o processamento do locatário. Para saber mais sobre a criação de grupos dinâmicos, consulte nossa [documentação](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Outubro de 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Agentes híbridos locais do Azure AD afetados pelas alterações de certificado do Azure TLS

**Tipo:** plano de alteração  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** plataforma

A Microsoft está atualizando os serviços do Azure para que eles usem certificados TLS de outro conjunto de ACs (autoridades de certificação) raiz. Esta atualização se deve aos certificados de autoridade de certificação atuais que não estão em conformidade com um dos requisitos de linha de base do fórum de CA/navegador. Essa alteração afetará os agentes híbridos do Azure AD instalados no local que têm ambientes protegidos com uma lista fixa de certificados raiz e precisarão ser atualizados para confiar nos novos emissores de certificado.

Essa alteração resultará em interrupção do serviço se você não tomar uma ação imediatamente. Esses agentes incluem [conectores de proxy de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) para acesso remoto aos agentes de [autenticação de passagem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) locais, que permitem aos usuários entrar em aplicativos usando as mesmas senhas e agentes de visualização de provisionamento de [nuvem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que executam o AD para o Azure ad Sync. 

Se você tiver um ambiente com regras de firewall definidas para permitir chamadas de saída somente para download de CRL (lista de certificados revogados) específico, será necessário permitir as seguintes URLs de CRL e OCSP. Para obter detalhes completos sobre a alteração e as URLs de CRL e OCSP para habilitar o acesso, consulte  [alterações de certificado TLS do Azure](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Os eventos de provisionamento serão removidos dos logs de auditoria e publicados somente para logs de provisionamento

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A atividade pelo [serviço de provisionamento](../app-provisioning/user-provisioning.md) scim é registrada nos logs de auditoria e nos logs de provisionamento. Isso inclui a atividade, como a criação de um usuário no ServiceNow, o grupo em GSuite ou a importação de uma função do AWS. No futuro, esses eventos só serão publicados nos logs de provisionamento. Essa alteração está sendo implementada para evitar eventos duplicados em logs e custos adicionais incorridos por clientes que consomem os logs no log Analytics. 

Forneceremos uma atualização quando uma data for concluída. Essa reprovação não está planejada para o ano civil 2020. 

> [!NOTE]
> Isso não afeta nenhum evento nos logs de auditoria fora dos eventos de sincronização emitidos pelo serviço de provisionamento. Eventos como a criação de um aplicativo, política de acesso condicional, um usuário no diretório, etc., continuarão a ser emitidos nos logs de auditoria. [Saiba mais](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Agentes híbridos locais do Azure AD afetados pelas alterações de certificado de TLS (segurança de camada de transporte) do Azure

**Tipo:** plano de alteração  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** plataforma
 
A Microsoft está atualizando os serviços do Azure para que eles usem certificados TLS de outro conjunto de ACs (autoridades de certificação) raiz. Haverá uma atualização devido aos certificados de autoridade de certificação atuais não após um dos requisitos de linha de base do fórum de autoridade de certificação/navegador. Essa alteração afetará os agentes híbridos do Azure AD instalados no local que têm ambientes protegidos com uma lista fixa de certificados raiz. Esses agentes precisarão ser atualizados para confiar nos novos emissores de certificado.

Essa alteração resultará em interrupção do serviço se você não tomar uma ação imediatamente. Esses agentes incluem: 
- [Conectores de proxy de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) para acesso remoto ao local 
- Os agentes de [autenticação de passagem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que permitem que os usuários entrem em aplicativos usando as mesmas senhas
- Agentes de [visualização de provisionamento de nuvem](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) que fazem o AD para o Azure ad Sync. 

Se você tiver um ambiente com regras de firewall definidas para permitir chamadas de saída somente para download de CRL (lista de certificados revogados) específico, será necessário permitir URLs de CRL e OCSP. Para obter detalhes completos sobre a alteração e as URLs de CRL e OCSP para habilitar o acesso, consulte  [alterações de certificado TLS do Azure](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 e substituição 3DES no US Gov Cloud

**Tipo:** plano de alteração  
**Categoria de serviço:** Todos os aplicativos do Azure AD  
**Funcionalidade do produto:** Padrões
 
O Azure Active Directory substituirá os seguintes protocolos até 31 de março de 2021:
- TLS 1.0
- TLS 1.1
- pacote de codificação 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Todas as combinações de servidor cliente e navegador-servidor devem usar o TLS 1,2 e os pacotes de codificação modernos para manter uma conexão segura com o Azure Active Directory para o Azure, o Office 365 e os serviços de Microsoft 365.

Os ambientes afetados são:
- US Gov do Azure
- [Office 365 GCC alto & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Atribuir aplicativos a funções em AU e escopo de objeto

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
Esse recurso habilita a capacidade de atribuir um aplicativo (SPN) a uma função de administrador no escopo da unidade administrativa. Para saber mais, consulte [atribuir funções de escopo a uma unidade administrativa](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Agora você pode desabilitar e excluir usuários convidados quando eles têm acesso negado a um recurso

**Tipo:** novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade
 
Desabilitar e excluir é um controle avançado nas revisões de acesso do Azure AD para ajudar as organizações a gerenciarem melhor os convidados externos em grupos e aplicativos. Se os convidados forem negados em uma revisão de acesso, a **desabilitação e a exclusão** irão bloqueá-los automaticamente de entrar por 30 dias. Após 30 dias, eles serão removidos do locatário completamente.

Para obter mais informações sobre esse recurso, consulte [desabilitar e excluir identidades externas com revisões de acesso do Azure AD (versão prévia)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Criadores de revisão de acesso podem adicionar mensagens personalizadas em emails aos revisores

**Tipo:** novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade
 
Nas revisões de acesso do Azure AD, os administradores que criam revisões agora podem gravar uma mensagem personalizada nos revisores. Os revisores verão a mensagem no email que recebem, solicitando que eles concluam a revisão. Para saber mais sobre como usar esse recurso, consulte a etapa 6 da seção [Configurações avançadas](../governance/create-access-review.md#advanced-settings) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – outubro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [Serviços de Diretório OpenText](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Sincronização Global de Identidade de Retransmissão](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Assistente de integração para Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
A experiência do assistente de integração (versão prévia) agora está disponível para Azure AD B2C Registros de aplicativo. Essa experiência ajuda a orientá-lo a configurar seu aplicativo para cenários comuns. Saiba mais sobre [as práticas recomendadas e recomendações da plataforma de identidade da Microsoft](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Exibir a ID do modelo de função na interface do usuário portal do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure  
**Funcionalidade do produto:** Controle de Acesso
 

Agora você pode exibir a ID do modelo de cada função do Azure AD na portal do Azure. No Azure AD, selecione a  **Descrição** da função selecionada. 

É recomendável que os clientes usem IDs de modelo de função no código e script do PowerShell, em vez de no nome de exibição. A ID do modelo de função tem suporte para uso para objetos [directoryRoles](/graph/api/resources/directoryrole?view=graph-rest-1.0) e [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Para obter mais informações sobre IDs de modelo de função, consulte [IDs de modelo de função](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Os conectores de API para Azure AD B2C fluxos de usuário de inscrição agora estão em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 

Os conectores de API agora estão disponíveis para uso com Azure Active Directory B2C. Os conectores de API permitem que você use APIs Web para personalizar os fluxos de usuário de inscrição e integrá-los a sistemas de nuvem externos. Você pode usar conectores de API para:

- Integrar com fluxos de trabalho de aprovação personalizados
- Validar dados de entrada do usuário
- Substituir atributos de usuário 
- Executar lógica de negócios personalizada 

 Visite o [usar conectores de API para personalizar e estender](../../active-directory-b2c/api-connectors-overview.md) a documentação de inscrição para saber mais.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Propriedade de estado para organizações conectadas no gerenciamento de direitos

**Tipo:** novo recurso  
**Categoria de serviço:** Funcionalidade do **produto** de gerenciamento de diretório: gerenciamento de direitos
 

 Todas as organizações conectadas agora terão uma propriedade adicional chamada "State". O estado controlará como a organização conectada será usada em políticas que se referem a "todas as organizações conectadas configuradas". O valor será "configurado" (o que significa que a organização está no escopo das políticas que usam a cláusula "All") ou "propostas" (o que significa que a organização não está no escopo).  

As organizações conectadas criadas manualmente terão uma configuração padrão de "configurado". Enquanto isso, os criados automaticamente (criados por meio de políticas que permitem que qualquer usuário da Internet solicitem acesso) serão padronizados como "propostos".  Todas as organizações conectadas criadas antes de setembro de 9 2020 serão definidas como "configuradas". Os administradores podem atualizar essa propriedade conforme necessário. [Saiba mais](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory identidades externas agora tem configurações de segurança avançadas Premium para B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de detecção de risco e acesso condicional com base em risco do Identity Protection agora estão disponíveis no [Azure ad B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Com esses recursos avançados de segurança, os clientes agora podem:
- Aproveite as informações inteligentes para avaliar o risco com os aplicativos B2C e as contas de usuário final. As detecções incluem viagem atípicos, endereços IP anônimos, endereços IP vinculados a malware e inteligência contra ameaças do Azure AD. O portal e os relatórios baseados em API também estão disponíveis.
- Resolva riscos automaticamente Configurando políticas de autenticação adaptáveis para usuários do B2C. Os desenvolvedores e administradores de aplicativos podem reduzir o risco em tempo real exigindo a autenticação multifator (MFA) ou bloquear o acesso dependendo do nível de risco do usuário detectado, com controles adicionais disponíveis com base no local, grupo e aplicativo.
- Integre com Azure AD B2C fluxos de usuário e políticas personalizadas. As condições podem ser disparadas de fluxos de usuários internos no Azure AD B2C ou podem ser incorporadas em políticas personalizadas do B2C. Assim como ocorre com outros aspectos do fluxo de usuário B2C, as mensagens de experiência do usuário final podem ser personalizadas. A personalização é de acordo com as alternativas de voz, marca e mitigação da organização.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – outubro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em outubro de 2020, adicionamos os seguintes 27 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee-Productivity superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [integração de nuvem do Genesys para o Azure](https://apps.mypurecloud.com/msteams-integration/), [portal de tecnologias de zona](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [lembrete de compromisso](https://app.appointmentreminder.co.nz/account/login), [Cloud. Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [saudação](https://app.greetly.com/), [OrgVitality SSO} (.. /SaaS-apps/orgvitality-SSO-tutorial.MD), [carga de ar da Web](../saas-apps/web-cargo-air-tutorial.md), [CRM de fluxo de loop](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [varejo zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Gerenciador de tráfego virtual seguro do Pulse](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Os logs de provisionamento agora podem ser transmitidos para o log Analytics

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 

Publique seus logs de provisionamento no log Analytics para:
- Armazenar logs de provisionamento por mais de 30 dias
- Definir notificações e alertas personalizados
- Criar painéis para visualizar os logs
- Executar consultas complexas para analisar os logs 

Para saber como usar o recurso, consulte [entender como o provisionamento se integra com os logs de Azure monitor](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Os logs de provisionamento agora podem ser exibidos por proprietários do aplicativo

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
Agora você pode permitir que os proprietários do aplicativo monitorem a atividade pelo serviço de provisionamento e solucionem problemas sem fornecer a eles uma função privilegiada ou torná-lo um afunilamento. [Saiba mais](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Renomeando 10 funções Azure Active Directory

**Tipo:** recurso alterado  
**Categoria de serviço:** Funções do Azure  
**Funcionalidade do produto:** Controle de Acesso
 
Algumas funções internas do Azure Active Directory (AD) têm nomes que diferem daqueles que aparecem no centro de administração Microsoft 365, no portal do AD do Azure e Microsoft Graph. Essa inconsistência pode causar problemas em processos automatizados. Com essa atualização, estamos renomeando 10 nomes de função para torná-los consistentes. A tabela a seguir tem os novos nomes de função:

![Tabela de novos nomes de função](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C suporte para o fluxo de código de autenticação para SPAs usando o MSAL JS 2. x

**Tipo:** recurso alterado  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
O MSAL.js versão 2. x agora inclui suporte para o fluxo de código de autorização para aplicativos Web de página única (SPAs). Azure AD B2C agora dará suporte ao uso do tipo de aplicativo SPA no portal do Azure e ao uso de MSAL.js fluxo de código de autorização com PKCE para aplicativos de página única. Isso permitirá que o SPAs use Azure AD B2C para manter o SSO com navegadores mais recentes e obedecer às recomendações de protocolo de autenticação mais recentes. Introdução ao " [registrar um aplicativo de página única (Spa)" no tutorial Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) .

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Atualizações para lembrar a MFA (autenticação multifator) em uma configuração de dispositivo confiável

**Tipo:** recurso alterado  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 

Recentemente, atualizamos o [Lembre-se da autenticação multifator (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) em um recurso de dispositivo confiável para estender a autenticação por até 365 dias. As licenças Premium do Azure Active Directory (Azure AD) também podem usar a [política de acesso condicional – frequência de entrada](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) que fornece mais flexibilidade para configurações de reautenticação.

Para obter a melhor experiência do usuário, é recomendável usar a frequência de entrada de acesso condicional para estender os tempos de vida de sessão em dispositivos confiáveis, locais ou sessões de baixo risco como uma alternativa para lembrar a MFA em uma configuração de dispositivo confiável. Para começar, examine nossas [diretrizes mais recentes sobre como otimizar a experiência de reautenticação](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>Setembro de 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – setembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Sincronização de Destinatário da Cofense](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Reconhecimento de segurança do Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Atualização de visualização pública de provisionamento de nuvem

**Tipo:** novo recurso  
**Categoria de serviço:** **Funcionalidade do produto** de provisionamento de nuvem do Azure AD: gerenciamento do ciclo de vida de identidade
 
Azure AD Connect a atualização de visualização pública de provisionamento de nuvem apresenta dois principais aprimoramentos desenvolvidos com base nos comentários dos clientes: 

- Experiência de mapeamento de atributos por meio de portal do Azure

    Com esse recurso, os administradores de ti podem mapear atributos de usuário, grupo ou contato do AD para o Azure AD usando vários tipos de mapeamento presentes hoje. O mapeamento de atributos é um recurso usado para padronizar os valores dos atributos que fluem de Active Directory para Azure Active Directory. É possível determinar se o valor do atributo deve ser mapeado diretamente como é do AD para o Azure AD ou usar expressões para transformar os valores de atributo ao provisionar os usuários. [Saiba mais](../cloud-provisioning/how-to-attribute-mapping.md)

- Provisionamento sob demanda ou experiência do usuário de teste

    Depois de configurar sua configuração, talvez você queira testar para ver se a transformação do usuário está funcionando conforme o esperado antes de aplicá-la a todos os usuários no escopo. Com o provisionamento sob demanda, os administradores de ti podem inserir o DN (nome distinto) de um usuário do AD e ver se eles estão sendo sincronizados conforme o esperado. O provisionamento sob demanda fornece uma ótima maneira de garantir que os mapeamentos de atributo que você realizou anteriormente funcionem conforme o esperado. [Saiba mais](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Recuperação do BitLocker auditada no Azure AD – visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de acesso do dispositivo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo
 
Quando os administradores de ti ou os usuários finais lêem as chaves de recuperação do BitLocker às quais eles têm acesso, Azure Active Directory agora gera um log de auditoria que captura quem acessou a chave de recuperação. A mesma auditoria fornece detalhes do dispositivo ao qual a chave do BitLocker foi associada.

Os usuários finais podem [acessar suas chaves de recuperação por meio da minha conta](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Os administradores de ti podem acessar as chaves de recuperação por meio da [API de chave de recuperação do BitLocker na versão beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) ou por meio do portal do Azure AD. Para saber mais, consulte [Exibir ou copiar chaves do BitLocker no portal do Azure ad](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Função interna Administrador de dispositivos de equipes

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários com a função de [administrador de dispositivos de equipes](../roles/permissions-reference.md#teams-devices-administrator) podem gerenciar [dispositivos certificados por equipes](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) no centro de administração de equipes. 

Essa função permite que o usuário exiba todos os dispositivos em um único relance, com a capacidade de Pesquisar e filtrar dispositivos. O usuário também pode verificar os detalhes de cada dispositivo, incluindo a conta conectada e a marca e o modelo do dispositivo. O usuário pode alterar as configurações no dispositivo e atualizar as versões do software. Essa função não concede permissões para verificar a atividade de equipes e chamar a qualidade do dispositivo.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Recursos de consulta avançada para objetos de diretório

**Tipo:** novo recurso  
**Categoria de serviço:** MS Graph  
**Funcionalidade do produto:** experiência de desenvolvedor
 
Todos os novos recursos de consulta introduzidos para objetos de diretório em APIs do Azure AD agora estão disponíveis no ponto de extremidade v 1.0 e pronto para produção. Os desenvolvedores podem contar, Pesquisar, filtrar e classificar objetos de diretório e links relacionados usando os operadores OData padrão.

Para saber mais, confira a documentação [aqui](https://aka.ms/BlogPostMezzoGA)e você também pode enviar comentários com esta [breve pesquisa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Visualização pública: avaliação de acesso contínuo para locatários que configuraram políticas de acesso condicional

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** segurança e proteção da identidade
 
A avaliação de acesso contínuo (CAE) agora está disponível em visualização pública para locatários do Azure AD com políticas de acesso condicional. Com o CAE, eventos de segurança e políticas críticas são avaliados em tempo real. Isso inclui desativação de conta, redefinição de senha e alteração de local. Para saber mais, consulte [avaliação de acesso contínuo](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Visualização pública: solicitar que os usuários solicitem a um pacote de acesso outras perguntas para melhorar as decisões de aprovação

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Os administradores agora podem exigir que os usuários que solicitam um pacote de acesso respondam a perguntas adicionais além da justificativa de negócios no meu portal de acesso do gerenciamento de direitos do Azure AD. As respostas dos usuários serão mostradas aos aprovadores para ajudá-los a tomar uma decisão de aprovação de acesso mais precisa. Para saber mais, consulte [coletar informações adicionais do solicitante para aprovação (versão prévia)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Visualização pública: gerenciamento de usuários aprimorado

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de usuários  
**Funcionalidade do produto:** Gerenciamento de usuários
 

O portal do AD do Azure foi atualizado para facilitar a localização de usuários nas páginas todos os usuários e usuários excluídos. As alterações na visualização incluem: 
- Mais propriedades de usuário visíveis, incluindo ID de objeto, status de sincronização de diretório, tipo de criação e emissor de identidade.
- A pesquisa agora permite a pesquisa combinada de nomes, emails e IDs de objeto.
- Filtragem aprimorada por tipo de usuário (membro, convidado e nenhum), status de sincronização de diretório, tipo de criação, nome da empresa e nome de domínio.
- Novos recursos de classificação em propriedades como nome, nome principal do usuário e data de exclusão.
- Uma nova contagem total de usuários que é atualizada com quaisquer pesquisas ou filtros.

Para obter mais informações, consulte [aprimoramentos de gerenciamento de usuário (versão prévia) no Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Novo campo de observações para aplicativos empresariais

**Tipo:** novo recurso  
**Categoria de serviço:** Funcionalidade do **produto** de aplicativos empresariais: SSO

Você pode adicionar anotações de texto livre a aplicativos empresariais. Você pode adicionar qualquer informação relevante que ajudará você a gerentes de aplicativos em aplicativos empresariais. Para obter mais informações, consulte [início rápido: configurar propriedades para um aplicativo em seu locatário do Azure Active Directory (AD do Azure)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – setembro de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em setembro de 2020, adicionamos os seguintes 34 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[VMware horizonte – Unified Access Gateway](), [Pulse Secure PCs](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [sexo adequação](https://app.genderfitness.com/), [COEO portal](https://my.coeo.com/), [gramatical](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA arqueiro Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile armário](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [análise de Simfoni](https://simfonianalytics.com/accounts/microsoft/login/), [priva Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [FEXA](../saas-apps/fexa-tutorial.md), [assinatura protegida Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [assinatura protegida Enterprise Portal configuração do AAD](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft-protegido por F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui: https://aka.ms/AppsTutorial .

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nova função de delegação no gerenciamento de direitos do Azure AD: Gerenciador de atribuição de pacotes de acesso

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Uma nova função de Gerenciador de atribuição de pacote de acesso foi adicionada ao gerenciamento de direitos do Azure AD para fornecer permissões granulares para gerenciar atribuições. Agora você pode delegar tarefas a um usuário nessa função, que pode delegar o gerenciamento de atribuições de um pacote de acesso a um proprietário de negócios. No entanto, um Gerenciador de atribuição de pacote do Access não pode alterar as políticas de pacote de acesso ou outras propriedades que são definidas pelos administradores. 

Com essa nova função, você se beneficia dos privilégios mínimos necessários para delegar o gerenciamento de atribuições e manter o controle administrativo em todas as outras configurações de pacote de acesso. Para saber mais, consulte [funções de gerenciamento](../governance/entitlement-management-delegate.md#entitlement-management-roles)de direitos.
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Alterações no fluxo de integração de Privileged Identity Management

**Tipo:** recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Anteriormente, a integração ao Privileged Identity Management (PIM) exigia o consentimento do usuário e um fluxo de integração na folha do PIM que incluía o registro no Azure AD MFA. Com a recente integração da experiência do PIM na folha de funções e administradores do Azure AD, estamos removendo essa experiência. Qualquer locatário com licença P2 válida será integrado automaticamente ao PIM.

A integração ao PIM não tem nenhum efeito adverso direto no seu locatário. Você pode esperar as seguintes alterações:
- Opções de atribuição adicionais, como ativas versus qualificadas com hora de início e de término, quando você faz uma atribuição na folha de funções e administradores do PIM ou do Azure AD. 
- Mecanismos de escopo adicionais, como unidades administrativas e funções personalizadas, introduzidos diretamente na experiência de atribuição. 
- Se você for um administrador global ou administrador de função com privilégios, poderá começar a obter alguns emails adicionais como o resumo semanal do PIM. 
- Você também pode ver a entidade de serviço MS-PIM no log de auditoria relacionado à atribuição de função. Essa alteração esperada não deve afetar o fluxo de trabalho regular.

 Para obter mais informações, consulte [começar a usar Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Gerenciamento de direitos do Azure AD: o painel Selecionar de recursos de pacote do Access agora é mostrado por padrão nos recursos atualmente no catálogo selecionado

**Tipo:** recurso alterado  
**Categoria de serviço:** Gerenciamento de acesso do usuário  
**Funcionalidade do produto:** Gerenciamento de direitos
 

No fluxo de criação do pacote do Access, na guia funções de recurso, o comportamento selecionar painel é alterado. Atualmente, o comportamento padrão é mostrar todos os recursos que pertencem ao usuário e os recursos adicionados ao catálogo selecionado. 

Essa experiência será alterada para exibir apenas os recursos atualmente adicionados ao catálogo por padrão, para que os usuários possam facilmente escolher recursos do catálogo. A atualização ajudará na descoberta dos recursos para adicionar aos pacotes do Access e reduzirá o risco de adicionar inadvertidamente recursos pertencentes ao usuário que não fazem parte do catálogo. Para saber mais, confira [criar um novo pacote de acesso no gerenciamento de direitos do Azure ad](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Agosto de 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Atualizações para os requisitos do firewall do Azure Servidor de Autenticação Multifator

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 
A partir de 1 de outubro de 2020, os requisitos de firewall do servidor do Azure MFA exigirão intervalos IP adicionais.

Se você tiver regras de firewall de saída em sua organização, atualize as regras para que seus servidores MFA possam se comunicar com todos os intervalos de IP necessários. Os intervalos de IP são documentados nos [requisitos do firewall do Azure servidor de autenticação multifator](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Alterações futuras na experiência do usuário na pontuação segura de identidade

**Tipo:** plano de alteração  
**Categoria de serviço:** Funcionalidade do **produto** de proteção de identidade: proteção de & de segurança de identidade

Estamos atualizando o portal de Pontuação segura de identidade para alinhar com as alterações introduzidas no [novo lançamento](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)da Pontuação de segurança da Microsoft. 

A versão de visualização com as alterações estará disponível no início de setembro. As alterações na versão de visualização incluem:
- "Pontuação segura de identidade" renomeado como "Pontuação segura para identidade" para o alinhamento de marca com a pontuação de segurança da Microsoft
- Pontos normalizados para a escala padrão e relatados em percentuais em vez de pontos

Nesta versão prévia, os clientes podem alternar entre a experiência existente e a nova experiência. Essa versão prévia durará até o final de novembro de 2020. Após a versão prévia, os clientes serão automaticamente direcionados para a nova experiência de UX.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Novas permissões de acesso de convidado restrito no Azure AD – visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Controle de acesso   
**Funcionalidade do produto:** Gerenciamento de usuários

Atualizamos as permissões de nível de diretório para usuários convidados. Essas permissões permitem que os administradores exijam restrições e controles adicionais no acesso de usuário convidado externo. Agora, os administradores podem adicionar restrições adicionais para acesso de convidados externos ao perfil de usuários e grupos e informações de associação. Com esse recurso de visualização pública, os clientes podem gerenciar o acesso de usuário externo em escala por meio da ofuscação de associações de grupo, incluindo a restrição de usuários convidados de ver associações dos grupos nos quais eles estão.

Para saber mais, consulte [permissões de acesso de convidado restrito](../enterprise-users/users-restrict-guest-permissions.md) e [permissões padrão de usuários](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Disponibilidade geral de consultas Delta para entidades de serviço

**Tipo:** novo recurso  
**Categoria de serviço:** MS Graph  
**Funcionalidade do produto:** experiência de desenvolvedor
 
Microsoft Graph consulta Delta agora dá suporte ao tipo de recurso em v 1.0:
- Entidade de Serviço

Agora, os clientes podem controlar as alterações feitas nesses recursos com eficiência e fornecer a melhor solução para sincronizar as alterações desses recursos com um armazenamento de dados local. Para saber como configurar esses recursos em uma consulta, consulte [usar a consulta Delta para controlar alterações nos dados de Microsoft Graph](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Disponibilidade geral de consultas Delta para oAuth2PermissionGrant

**Tipo:** novo recurso  
**Categoria de serviço:** MS Graph  
**Funcionalidade do produto:** experiência de desenvolvedor

Microsoft Graph consulta Delta agora dá suporte ao tipo de recurso em v 1.0:
- OAuth2PermissionGrant

Os clientes agora podem controlar as alterações nesses recursos com eficiência e fornecer a melhor solução para sincronizar as alterações nesses recursos com um armazenamento de dados local. Para saber como configurar esses recursos em uma consulta, consulte [usar a consulta Delta para controlar alterações nos dados de Microsoft Graph](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – agosto de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em agosto de 2020, adicionamos os seguintes 25 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [alma SIS](https://almau.getalma.com/), [conector Enlyft do Dynamics 365](http://enlyft.com/), [soluções de software de utilização de espaço Serraview](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniqs](https://web.uniq.app/), [visivelmente](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [comparações de Edmentum-cursos exatos caminho](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [zix conformidade e captura](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [, Genetec](https://www.clearance.network/)de trabalho, [ISAMs](../saas-apps/isams-tutorial.md) [, VeraSMART](../saas-apps/verasmart-tutorial.md) [, Amiko](https://amiko.web.rivero.app/), [Twingate,](https://auth.twingate.com/signup) [concessão de funil](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/), [Bpanda](https://goto.bpanda.com/login), [Wandera End User](https://www.wandera.com/) [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect), [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>As florestas de recursos agora estão disponíveis para o Azure AD DS 

**Tipo:** Nova **categoria de serviço** de recurso: Azure AD Domain Services   
**Capacidade do produto:** Serviços de Domínio do Azure AD
 
A capacidade das florestas de recursos no Azure AD Domain Services agora está disponível para o público em geral. Agora você pode habilitar a autorização sem a sincronização de hash de senha para usar Azure AD Domain Services, incluindo a autorização de cartão inteligente. Para saber mais, consulte [conjuntos de réplicas e recursos para Azure Active Directory Domain Services (versão prévia)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>O suporte à réplica regional para os domínios gerenciados do Azure AD DS agora está disponível

**Tipo:** novo recurso   
**Categoria de serviço:** Serviços de Domínio do Azure AD  
**Capacidade do produto:** Serviços de Domínio do Azure AD
 
Você pode expandir um domínio gerenciado para ter mais de um conjunto de réplicas por locatário do Azure AD. Os conjuntos de réplicas podem ser adicionados a qualquer rede virtual emparelhada em qualquer região do Azure que ofereça suporte a Azure AD Domain Services. Conjuntos de réplicas adicionais em diferentes regiões do Azure fornecem recuperação de desastre geográfica para aplicativos herdados se uma região do Azure fica offline. Para saber mais, consulte [conjuntos de réplicas e recursos para Azure Active Directory Domain Services (versão prévia)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilidade geral do meu Sign-Ins do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Experiências do usuário final
 
O meu Sign-Ins do Azure AD é um novo recurso que permite que os usuários empresariais examinem seu histórico de entrada para verificar se há atividades incomuns. Além disso, esse recurso permite que os usuários finais relatem "isso não foi" ou "isso me foi" em atividades suspeitas. Para saber mais sobre como usar esse recurso, confira [Exibir e pesquisar sua atividade de entrada recente na página meu Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>O provisionamento de usuário controlado pelo SAP SuccessFactors HR para o Azure AD já está disponível para o público em geral

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Agora você pode integrar o SAP SuccessFactors como a fonte de identidade autoritativa com o Azure AD e automatizar o ciclo de vida de identidade de ponta a ponta usando eventos de RH, como novas contratações e rescisão, para impulsionar o provisionamento e desprovisionamento de contas no Azure AD. 

Para saber mais sobre como configurar o provisionamento de entrada do SAP SuccessFactors para o Azure AD, consulte o tutorial [Configurar o SAP SuccessFactors para Active Directory provisionamento de usuário](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Suporte personalizado do Open ID Connect do MS API do Graph para Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Anteriormente, os provedores de conexão Open ID personalizados só podiam ser adicionados ou gerenciados por meio do portal do Azure. Agora, a Azure AD B2C clientes podem adicioná-los e gerenciá-los por meio da versão beta de APIs do Microsoft Graph também. Para saber como configurar esse recurso com APIs, consulte [tipo de recurso identityprovider](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Atribuir funções internas do Azure AD a grupos de nuvem

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso

Agora você pode atribuir funções internas do Azure AD a grupos de nuvem com esse novo recurso. Por exemplo, você pode atribuir a função de administrador do SharePoint a Contoso_SharePoint_Admins grupo. Você também pode usar o PIM para tornar o grupo um membro qualificado da função, em vez de conceder acesso à sua posição. Para saber como configurar esse recurso, consulte [usar grupos de nuvem para gerenciar atribuições de função no Azure Active Directory (versão prévia)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Função interna do líder de negócios do insights agora disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários na função de líder de negócios do insights podem acessar um conjunto de dashboards e ideias por meio do [aplicativo M365 insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Isso inclui acesso completo a todos os dashboards e a funcionalidade de exploração de dados e informações apresentadas. No entanto, os usuários nessa função não têm acesso às definições de configuração do produto, que é responsabilidade da função de administrador do insights. Para saber mais sobre essa função, consulte [permissões de função de administrador no Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Função interna do administrador do insights agora disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários na função Administrador do insights podem acessar o conjunto completo de recursos administrativos no [aplicativo M365 insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Um usuário nessa função pode ler informações de diretório, monitorar a integridade do serviço, tíquetes de suporte de arquivo e acessar os aspectos das configurações do administrador do insights. Para saber mais sobre essa função, consulte [permissões de função de administrador no Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>O administrador de aplicativos e o administrador de aplicativos de nuvem podem gerenciar Propriedades de extensão de aplicativos

**Tipo:** recurso alterado  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Anteriormente, somente o administrador global poderia gerenciar a [propriedade de extensão](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Agora estamos habilitando esse recurso para o administrador de aplicativos e o administrador de aplicativos de nuvem.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>Hotfix 4.6.263.0 e conectores do MIM 2016 SP2 1.1.1301.0

**Tipo:** recurso alterado  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Um [pacote cumulativo de atualizações de hotfix (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) está disponível para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Este pacote cumulativo contém atualizações para o MIM CM, o Gerenciador de sincronização do MIM e os componentes do PAM. Além disso, os conectores genéricos do MIM criam 1.1.1301.0 incluem atualizações para o conector do Graph.

---
 
## <a name="july-2020"></a>Julho de 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Como administrador de ti, quero direcionar aplicativos cliente usando o acesso condicional

**Tipo:** plano de alteração   
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Com a versão GA da condição de aplicativos cliente no acesso condicional, novas políticas agora serão aplicadas por padrão a todos os aplicativos cliente. Isso inclui clientes de autenticação herdados. As políticas existentes permanecerão inalteradas, mas a opção *Configurar Sim/não* será removida das políticas existentes para ver facilmente quais aplicativos cliente são aplicados pela política. 

Ao criar uma nova política, certifique-se de excluir usuários e contas de serviço que ainda estão usando a autenticação herdada; caso contrário, eles serão bloqueados. [Saiba mais](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Correções de conformidade SCIM futuras

**Tipo:** plano de alteração  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento do Azure AD utiliza o padrão SCIM para integração com aplicativos. Nossa implementação do padrão SCIM está em evolução, e esperamos fazer alterações em nosso comportamento em relação a como executamos operações de PATCH, bem como definir a propriedade "active" em um recurso. [Saiba mais](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>A configuração do proprietário do grupo no portal de administração do Azure será alterada

**Tipo:** plano de alteração  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

As configurações de proprietário na página de configuração geral de grupos podem ser configuradas para restringir os privilégios de atribuição de proprietário a um grupo limitado de usuários no portal de administração do Azure e no painel de acesso. Em breve, você terá a capacidade de atribuir o privilégio de proprietário do grupo não apenas nesses portais de UX, mas também impor a política no back-end para fornecer comportamento consistente entre pontos de extremidade, como PowerShell e Microsoft Graph. 

Começaremos a desabilitar a configuração atual para os clientes que não estão usando-o e oferecerão uma opção para definir o escopo dos usuários para o privilégio de proprietário do grupo nos próximos meses. Para obter orientação sobre como atualizar configurações de grupo, consulte Editar suas informações de grupo usando [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>O serviço de registro Azure Active Directory está terminando o suporte para TLS 1,0 e 1,1

**Tipo:** plano de alteração  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** plataforma

Os servidores de segurança de camada de transporte (TLS) 1,2 e de atualização serão comunicados em breve com o serviço Registro de Dispositivos do Azure Active Directory. O suporte para TLS 1,0 e 1,1 para comunicação com Registro de Dispositivos do Azure AD serviço será desativado:
- Em 31 de agosto de 2020, em todas as nuvens soberanas (GCC alto, DoD, etc.)
- Em 30 de outubro de 2020, em todas as nuvens comerciais

[Saiba mais](../devices/reference-device-registration-tls-1-2.md) sobre o TLS 1,2 para o serviço de registro do Azure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Entradas do Windows Hello para empresas visíveis nos logs de entrada do Azure AD

**Tipo:** corrigido  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
O Windows Hello para empresas permite que os usuários finais entrem em computadores Windows com um gesto (como um PIN ou biométrica). Os administradores do Azure AD podem querer diferenciar as entradas do Windows Hello para empresas de outras entradas do Windows como parte da jornada de uma organização até a autenticação sem senha. 

Os administradores agora podem ver se uma autenticação do Windows usou o Windows Hello para empresas verificando a guia detalhes de autenticação de um evento de entrada do Windows na folha Sign-Ins do Azure AD no portal do Azure. As autenticações do Windows Hello para empresas incluirão "WindowsHelloForBusiness" no campo método de autenticação. Para obter mais informações sobre como interpretar logs de Sign-In, consulte a [documentação de logs de entrada](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Correções para melhorias de desempenho e comportamento de exclusão de grupos

**Tipo:** corrigido  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Anteriormente, quando um grupo foi alterado de "dentro do escopo" para "fora do escopo" e um administrador clicou em reiniciar antes de a alteração ser concluída, o objeto de grupo não estava sendo excluído. Agora o objeto de grupo será excluído do aplicativo de destino quando ele sair do escopo (desabilitado, excluído, não atribuído ou não aprovado no filtro de escopo). [Saiba mais](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Visualização pública: os administradores agora podem adicionar conteúdo personalizado no email aos revisores ao criar uma revisão de acesso

**Tipo:** novo recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança de identidade
 
Quando uma nova revisão de acesso é criada, o revisor recebe um email solicitando que ele conclua a revisão de acesso. Muitos de nossos clientes solicitaram a capacidade de adicionar conteúdo personalizado ao email, como informações de contato ou outro conteúdo adicional de suporte para guiar o revisor. 

Agora disponível na visualização pública, os administradores podem especificar o conteúdo personalizado no email enviado aos revisores adicionando conteúdo na seção "avançado" das revisões de acesso do Azure AD. Para obter orientação sobre a criação de revisões de acesso, consulte [criar uma revisão de acesso de grupos e aplicativos nas revisões de acesso do Azure ad](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Fluxo de código de autorização para aplicativos de página única disponível

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** experiência de desenvolvedor
 
Devido às restrições de cookie de terceiros do navegador moderno, como o Safari ITP, o SPAs terá que usar o fluxo do código de autorização em vez do fluxo implícito para manter o SSO, e MSAL.js v 2. x agora dará suporte ao fluxo do código de autorização. 

Há atualizações correspondentes ao portal do Azure para que você possa atualizar seu SPA para que ele seja do tipo "Spa" e use o fluxo do código de autenticação. Consulte [conectar usuários e obter um token de acesso em um spa JavaScript usando o fluxo do código de autenticação](../develop/quickstart-v2-javascript-auth-code.md) para obter mais diretrizes.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>O Azure Proxy de Aplicativo do AD agora dá suporte ao cliente Web Serviços de Área de Trabalho Remota

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

O Azure Proxy de Aplicativo do AD agora dá suporte ao cliente Web do Serviços de Área de Trabalho Remota (RDS). O cliente Web do RDS permite que os usuários acessem a infraestrutura do Área de Trabalho Remota por meio de qualquer navegador compatível com HTLM5, como o Microsoft Edge, Internet Explorer 11, Google Chrome etc. Os usuários podem interagir com aplicativos ou áreas de trabalho remotas, como fariam com um dispositivo local de qualquer lugar. Usando o Azure Proxy de Aplicativo do AD você pode aumentar a segurança de sua implantação de RDS impondo políticas de acesso condicional e pré-autenticação para todos os tipos de aplicativos cliente avançados. Para obter diretrizes, consulte [publicar área de trabalho remota com o proxy de aplicativo do AD do Azure](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Próxima geração Azure AD B2C fluxos de usuário na visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
A experiência simplificada de fluxo de usuário oferece paridade de recursos com recursos de visualização e é a página inicial para todos os novos recursos. Os usuários poderão habilitar novos recursos dentro do mesmo fluxo de usuário, reduzindo a necessidade de criar várias versões com cada nova versão de recurso. Por fim, o novo UX amigável do usuário simplifica a seleção e a criação de fluxos de usuário. Experimente agora [criando um fluxo de usuário](../../active-directory-b2c/tutorial-create-user-flows.md). 

Para obter mais informações sobre fluxos de usuários, consulte [versões de fluxo de usuário em Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – julho de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em julho de 2020, adicionamos os seguintes 55 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepcionista](https://msteams.peterconnects.com/), [ALOHACLOUD](https://appfusions.alohacloud.com/auth), [Control torre](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [moedas de construção Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GREMINDERS](https://app.greminders.com/o365-oauth), [titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [bloge](../saas-apps/blogin-tutorial.md), [INTSIGHTS](../saas-apps/intsights-tutorial.md), [Kpifire](../saas-apps/kpifire-tutorial.md) [, Textline,](../saas-apps/textline-tutorial.md) [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [comunidade Spark](../saas-apps/community-spark-tutorial.md), [chat](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [controle de nuvem C3M](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan data Hub logon único](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [egresso](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [direito de segurança cibernética Adi](https://right-hand.ai/), Fyde [Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [Lenses.Io](../saas-apps/lensesio-tutorial.md), [momentânea](../saas-apps/momenta-tutorial.md), [uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme bot](https://tellme365liteweb.azurewebsites.net/), [inspirar](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (sistema de gerenciamento escolar)](https://smartschoolz.com/login), [Zepto – timecontinueing inteligente](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack logon único](../saas-apps/browserstack-single-sign-on-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – julho de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para o aplicativo do [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md)recentemente integrado.

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Exibir atribuições de função em todos os escopos e capacidade de baixá-las em um arquivo CSV

**Tipo:** recurso alterado  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Agora você pode exibir as atribuições de função em todos os escopos para uma função na guia "funções e administradores" no portal do Azure AD. Você também pode baixar essas atribuições de função para cada função em um arquivo CSV. Para obter orientação sobre como exibir e adicionar atribuições de função, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Reprovação do desenvolvimento de software de autenticação multifator do Azure (SDK do Azure MFA)

**Tipo:** preterido  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 
O desenvolvimento de software da autenticação multifator do Azure (SDK do Azure MFA) atingiu o fim da vida útil em 14 de novembro de 2018, como anunciado pela primeira vez em 2017 de novembro. A Microsoft estará desligando o serviço SDK em vigor em 30 de setembro de 2020. Qualquer chamada feita ao SDK falhará.

Se sua organização estiver usando o SDK do Azure MFA, você precisará migrar até 30 de setembro de 2020:
- SDK do Azure MFA para MIM: se você usar o SDK com o MIM, deverá migrar para o servidor do Azure MFA e ativar Privileged Access Management (PAM) seguindo estas [instruções](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- SDK do Azure MFA para aplicativos personalizados: considere integrar seu aplicativo ao Azure AD e use o acesso condicional para impor a MFA. Para começar, examine esta [página](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>Junho de 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condição de risco do usuário na política de acesso condicional

**Tipo:** plano de alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 

O suporte a riscos do usuário na política de acesso condicional do Azure AD permite que você crie várias políticas baseadas em risco do usuário. Diferentes níveis de risco do usuário mínimo podem ser necessários para diferentes usuários e aplicativos. Com base no risco do usuário, você pode criar políticas para bloquear o acesso, exigir autenticação multifator, alteração de senha segura ou redirecionar para Microsoft Cloud App Security para impor a política de sessão, como auditoria adicional.

A condição de risco do usuário requer o Azure AD Premium P2 porque ele usa a proteção de identidade do Azure, que é uma oferta P2. para obter mais informações sobre o acesso condicional, consulte a [documentação de acesso condicional do Azure ad](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>O SSO do SAML agora dá suporte a aplicativos que exigem que o SPNameQualifier seja definido quando solicitado

**Tipo:** corrigido  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
Alguns aplicativos SAML exigem que SPNameQualifier sejam retornados no assunto da asserção quando solicitado. Agora, o Azure AD responde corretamente quando um SPNameQualifier é solicitado na política NameID da solicitação. Isso também funciona para entrada iniciada pelo SP e a entrada iniciada pelo IdP será a seguinte.  Para saber mais sobre o protocolo SAML no Azure Active Directory, consulte [protocolo SAML de Sign-On único](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>A colaboração B2B do Azure AD dá suporte a convidar usuários do MSA e do Google nos locatários do Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 

Os locatários do Azure governamental usando os recursos de colaboração B2B agora podem convidar usuários que têm uma conta da Microsoft ou do Google. Para descobrir se o seu locatário pode usar esses recursos, siga as instruções em [como posso saber se a colaboração B2B está disponível no meu locatário do governo dos EUA do Azure?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>O objeto de usuário no MS Graph v1 agora inclui as propriedades externalUserState e externalUserStateChangedDateTime

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 

As propriedades externalUserState e externalUserStateChangedDateTime podem ser usadas para encontrar convidados B2B convidados que ainda não aceitaram seus convites, bem como a automação da compilação, como a exclusão de usuários que não aceitaram seus convites após um número de dias. Essas propriedades agora estão disponíveis no MS Graph v1. Para obter orientação sobre como usar essas propriedades, consulte [tipo de recurso de usuário](/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gerenciar sessões de autenticação no acesso condicional do Azure AD agora está disponível para o público geral

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Os recursos de gerenciamento de sessão de autenticação permitem que você configure com que frequência os usuários precisam fornecer credenciais de entrada e se precisam fornecer credenciais após fechar e reabrir os navegadores para oferecer mais segurança e flexibilidade em seu ambiente.
 
Além disso, o gerenciamento de sessão de autenticação usado para se aplicar somente à autenticação de primeiro fator no Azure AD ingressado, ingressado no Azure AD híbrido e dispositivos registrados no Azure AD. Agora, o gerenciamento de sessão de autenticação também será aplicado ao MFA. Para obter mais informações, consulte [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – junho de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em junho de 2020, adicionamos os 29 novos aplicativos a seguir em nossa galeria de aplicativos com suporte à Federação:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [Segment](../saas-apps/segment-tutorial.md), [ai auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.Io](../saas-apps/proto.io-tutorial.md), [gatekeeper](https://www.gatekeeperhq.com/), [planejador de Hub](../saas-apps/hub-planner-tutorial.md), [caixa de ferramentas de Ansira para o mercado](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation na nuvem](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [segurança física Kisi](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [zero](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [edX para negócios SAML 2,0 integração](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning plataforma](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [ID de campo](../saas-apps/field-id-tutorial.md), [currículos SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core-Helix serviço de autenticação](../saas-apps/perforce-helix-core-tutorial.md), minha [conformidade Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial . Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Conectores de API para identidade externa a inscrição de autoatendimento agora estão em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Conectores de API de identidades externas permitem que você aproveite as APIs da Web para integrar a inscrição de autoatendimento com sistemas de nuvem externos. Isso significa que agora você pode invocar APIs Web como etapas específicas em um fluxo de inscrição para disparar fluxos de trabalho personalizados baseados em nuvem. Por exemplo, você pode usar conectores de API para:

- Integre-se a fluxos de trabalho de aprovação personalizados.
- Executar a verificação de identidade
- Validar dados de entrada do usuário
- Substituir atributos de usuário
- Executar lógica de negócios personalizada

Para obter mais informações sobre todas as experiências possíveis com conectores de API, consulte [usar conectores de API para personalizar e estender a inscrição de autoatendimento](../external-identities/api-connectors-overview.md)ou [Personalizar identidades externas inscrição de autoatendimento com integrações de API Web](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Provisione sob demanda e obtenha usuários em seus aplicativos em segundos

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento do Azure AD opera em um momento cíclico. O serviço é executado a cada 40 minutos. O [recurso de provisionamento sob demanda](https://aka.ms/provisionondemand) permite que você escolha um usuário e provisione-os em segundos. Essa funcionalidade permite que você solucione problemas de provisionamento rapidamente, sem precisar fazer uma reinicialização para forçar o ciclo de provisionamento a iniciar novamente. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nova permissão para usar o gerenciamento de direitos do Azure AD no grafo

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** Gerenciamento de direitos
 
Uma nova permissão delegada EntitlementManagement. Read. All agora está disponível para uso com a API de gerenciamento de direitos no Microsoft Graph beta. Para saber mais sobre as APIs disponíveis, consulte [trabalhando com a API de gerenciamento de direitos do Azure ad](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>APIs de proteção de identidade disponíveis em v 1.0

**Tipo:** novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade
 
As APIs de Microsoft Graph riskyUsers e riskDetections agora estão disponíveis para o público geral. Agora que estão disponíveis no ponto de extremidade v 1.0, convidamos você a usá-los na produção. Para obter mais informações, consulte o [Microsoft Graph docs](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Os rótulos de sensibilidade para aplicar políticas a grupos de Microsoft 365 agora estão disponíveis para o público geral

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração
 

Agora você pode criar rótulos de sensibilidade e usar as configurações de rótulo para aplicar políticas a grupos de Microsoft 365, incluindo privacidade (pública ou privada) e política de acesso de usuário externo. Você pode criar um rótulo com a política de privacidade como particular e a política de acesso de usuário externo para não permitir que o adicione usuários convidados. Quando um usuário aplica esse rótulo a um grupo, o grupo será particular e nenhum usuário convidado poderá ser adicionado ao grupo. 

Rótulos de sensibilidade são importantes para proteger seus dados críticos para os negócios e permitem gerenciar grupos em escala, de maneira segura e em conformidade. Para obter orientação sobre como usar rótulos de sensibilidade, consulte [atribuir rótulos de sensibilidade a grupos de Microsoft 365 no Azure Active Directory (versão prévia)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Atualizações para suporte para Microsoft Identity Manager para clientes do Azure AD Premium

**Tipo:** recurso alterado  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O suporte do Azure agora está disponível para os componentes de integração do Azure AD do Microsoft Identity Manager 2016, por meio do fim do suporte estendido para Microsoft Identity Manager 2016. Leia mais em [atualização de suporte para clientes Azure ad Premium usando Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>O uso de condições de associação de grupo na configuração de declarações de SSO é aumentado

**Tipo:** recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
Anteriormente, o número de grupos que você poderia usar ao alterar condicionalmente as declarações com base na associação de grupo em qualquer configuração de aplicativo individual era limitada a 10. O uso de condições de associação de grupo na configuração de declarações de SSO agora aumentou para um máximo de 50 grupos. Para obter mais informações sobre como configurar declarações, consulte [configuração de declarações de SSO de aplicativos empresariais](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Habilitando a formatação básica no componente texto da página de entrada na identidade visual da empresa.

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
A funcionalidade de identidade visual da empresa na experiência de logon do Azure AD/Microsoft 365 foi atualizada para permitir que o cliente adicione hiperlinks e formatação simples, incluindo negrito, fonte, sublinhado e itálico. Para obter orientação sobre como usar essa funcionalidade, consulte [Adicionar identidade visual à página de entrada Azure Active Directory de sua organização](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Aprimoramentos de desempenho de provisionamento

**Tipo:** recurso alterado  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento foi atualizado para reduzir o tempo de conclusão de um [ciclo incremental](../app-provisioning/how-provisioning-works.md#incremental-cycles) . Isso significa que os usuários e grupos serão provisionados em seus aplicativos mais rapidamente do que antes. Todos os novos trabalhos de provisionamento criados após 6/10/2020 irão se beneficiar automaticamente das melhorias de desempenho. Todos os aplicativos configurados para provisionamento antes de 6/10/2020 precisarão ser reiniciados uma vez após 6/10/2020 para aproveitar as melhorias de desempenho. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Anunciando a reprovação da ADAL e da paridade do MS Graph

**Tipo:** preterido  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo

Agora que as MSAL (bibliotecas de autenticação da Microsoft) estão disponíveis, não adicionaremos mais novos recursos às bibliotecas de autenticação de Azure Active Directory (ADAL) e encerraremos os patches de segurança em 30 de junho de 2022. Para obter mais informações sobre como migrar para o MSAL, consulte [migrar aplicativos para a biblioteca de autenticação da Microsoft (MSAL)](../develop/msal-migration.md).

Além disso, concluimos o trabalho para disponibilizar todas as funcionalidades do Azure AD Graph por meio do MS Graph. Assim, as APIs do Azure AD Graph receberão apenas Bugfix e correções de segurança até 30 de junho de 2022. Para obter mais informações, consulte [atualizar seus aplicativos para usar a biblioteca de autenticação da Microsoft e a API de Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
