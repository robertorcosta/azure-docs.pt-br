---
title: Arquivo morto para o que há de novo no Azure Active Directory? | Microsoft Docs
description: As notas da versão Novidades na seção Visão geral deste conjunto de conteúdo contêm seis meses de atividade. Após 6 meses, os itens são removidos do artigo principal e colocados neste artigo.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64229e4433ee029787ebd88eba264833827e706c
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136458"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arquivo morto para o que há de novo no Azure Active Directory?

O artigo [novidades principais no Azure Active Directory? notas de versão](whats-new.md) contém atualizações dos últimos seis meses, enquanto este artigo contém todas as informações mais antigas.

O que há de novo no Azure Active Directory? as notas de versão fornecem informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

---

## <a name="august-2019"></a>Agosto de 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Pesquisa, filtragem e classificação aprimoradas para grupos estão disponíveis no portal do AD do Azure (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Temos o prazer de anunciar a disponibilidade de visualização pública das experiências relacionadas a grupos aprimoradas no portal do AD do Azure. Esses aprimoramentos ajudam você a gerenciar melhor grupos e listas de membros, fornecendo:

- Recursos de pesquisa avançada, como a pesquisa de subcadeia de caracteres em listas de grupos.
- Opções avançadas de filtragem e classificação em listas de membros e proprietários.
- Novos recursos de pesquisa para listas de membros e proprietários.
- Contagens de grupos mais precisas para grupos grandes.

Para obter mais informações, consulte [gerenciar grupos na portal do Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Novas funções personalizadas estão disponíveis para gerenciamento de registro de aplicativo (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** RBAC  
**Funcionalidade do produto:** Controle de Acesso

As funções personalizadas (disponíveis com uma assinatura P1 ou P2 do Azure AD) agora podem ajudar a fornecer acesso refinado, permitindo que você crie definições de função com permissões específicas e, em seguida, atribua essas funções a recursos específicos. No momento, você cria funções personalizadas usando permissões para gerenciar registros de aplicativo e, em seguida, atribuir a função a um aplicativo específico. Para obter mais informações sobre funções personalizadas, consulte [funções de administrador personalizado no Azure Active Directory (versão prévia)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Se você precisar de permissões ou recursos adicionais com suporte, que você não vê no momento, poderá enviar comentários para nosso [site de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) e adicionaremos sua solicitação ao mapa de estrada de atualização.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Novos logs de provisionamento podem ajudá-lo a monitorar e solucionar problemas de implantação de provisionamento de aplicativo (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade

Novos logs de provisionamento estão disponíveis para ajudá-lo a monitorar e solucionar problemas de implantação de provisionamento de usuários e grupos. Esses novos arquivos de log incluem informações sobre:

- Quais grupos foram criados com êxito no [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Quais funções foram importadas de [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- O que os funcionários não foram importados do [workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Para obter mais informações, consulte [Provisionando relatórios no portal de Azure Active Directory (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Novos relatórios de segurança para todos os administradores do Azure AD (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Por padrão, todos os administradores do Azure AD serão capazes de acessar relatórios de segurança modernos no Azure AD. Até o fim de setembro, você poderá usar a faixa na parte superior dos relatórios de segurança modernos para retornar aos relatórios antigos.

Os relatórios de segurança modernos fornecerão recursos adicionais das versões mais antigas, incluindo:

- Filtragem e classificação avançadas
- Ações em massa, como ignorar o risco do usuário
- Confirmação de entidades comprometidas ou seguras
- Estado de risco, abrangendo: em risco, ignorado, remediado e confirmado comprometido
- Novas detecções relacionadas a riscos (disponíveis para assinantes Azure AD Premium)

Para obter mais informações, consulte [usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [entradas arriscadas](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)e [detecções de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A identidade gerenciada atribuída pelo usuário está disponível para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** Identidades gerenciadas para recursos do Azure  
**Funcionalidade do produto:** experiência de desenvolvedor

Agora, as identidades gerenciadas atribuídas pelo usuário estão disponíveis para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. Como parte disso, o Azure pode criar uma identidade no locatário do Azure AD que é confiável para a assinatura em uso e pode ser atribuída a uma ou mais instâncias de serviço do Azure. Para obter mais informações sobre identidades gerenciadas atribuídas pelo usuário, consulte [o que são identidades gerenciadas para recursos do Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Os usuários podem redefinir suas senhas usando um token de hardware ou aplicativo móvel (disponibilidade geral)

**Tipo:** recurso alterado  
**Categoria de serviço:** redefinição de senha de autoatendimento  
**Funcionalidade do produto:** autenticação de usuário

Os usuários que registraram um aplicativo móvel com sua organização agora podem redefinir sua própria senha, aprovando uma notificação do aplicativo Microsoft Authenticator ou inserindo um código de seu aplicativo móvel ou token de hardware.

Para obter mais informações, consulte [como funciona: redefinição de senha de autoatendimento do Azure ad](https://aka.ms/authappsspr). Para obter mais informações sobre a experiência do usuário, consulte [visão geral de redefinir sua própria senha corporativa ou de estudante](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora o cache compartilhado MSAL.NET para cenários em nome de

**Tipo:** corrigido  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

A partir da ADAL.NET (biblioteca de autenticação do Azure AD) versão 5.0.0-Preview, os desenvolvedores de aplicativos devem [serializar um cache por conta para aplicativos Web e APIs da Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Caso contrário, alguns cenários que usam o [fluxo em nome de](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), juntamente com alguns casos de uso específicos de `UserAssertion`, podem resultar em uma elevação de privilégio. Para evitar essa vulnerabilidade, o ADAL.NET agora ignora o cache compartilhado da biblioteca de autenticação da Microsoft para dotNet (MSAL.NET) para cenários em nome de.

Para obter mais informações sobre esse problema, consulte [Azure Active Directory vulnerabilidade de elevação de privilégio da biblioteca de autenticação](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – agosto de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em agosto de 2019, adicionamos esses 26 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Plataforma cívico](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [pronovo Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Portal de Inativ da Viareport (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [participação na Academia](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), matriz de [prioridade](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [entregar. Media&trade; portal](https://portal.deliver.media), [frente Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [ stashcat AD Connect](https://www.stashcat.com), [piscar](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [pronovo Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [assistir por cores](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB navegar pelo atendimento estratégico](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Novas versões dos módulos AzureAD PowerShell e AzureADPreview PowerShell estão disponíveis

**Tipo:** recurso alterado  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** diretório

Novas atualizações para os módulos do PowerShell de visualização do AzureAD e AzureAD estão disponíveis:

- Um novo parâmetro `-Filter` foi adicionado ao parâmetro `Get-AzureADDirectoryRole` no módulo AzureAD. Esse parâmetro ajuda você a filtrar as funções de diretório retornadas pelo cmdlet.
- Novos cmdlets foram adicionados ao módulo AzureADPreview para ajudar a definir e atribuir funções personalizadas no Azure AD, incluindo:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Melhorias na interface do usuário do construtor de regras de grupo dinâmico no portal do Azure

**Tipo:** recurso alterado  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Fizemos algumas melhorias da interface do usuário no construtor de regras de grupo dinâmico, disponível na portal do Azure, para ajudá-lo a configurar uma nova regra com mais facilidade ou alterar as regras existentes. Essa melhoria de design permite que você crie regras com até cinco expressões, em vez de apenas uma. Também atualizamos a lista de propriedades do dispositivo para remover as propriedades de dispositivo preteridas.

Para obter mais informações, consulte [gerenciar regras de associação dinâmica](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nova permissão de aplicativo Microsoft Graph disponível para uso com revisões de acesso

**Tipo:** recurso alterado  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** Governança de identidade

Apresentamos uma nova permissão de aplicativo Microsoft Graph, `AccessReview.ReadWrite.Membership`, que permite aos aplicativos criar e recuperar automaticamente as revisões de acesso para associações de grupo e atribuições de aplicativo. Essa permissão pode ser usada por seus trabalhos agendados ou como parte de sua automação, sem a necessidade de um contexto de usuário conectado.

Para obter mais informações, confira o [exemplo como criar revisões de acesso do Azure ad usando Microsoft Graph permissões de aplicativo com o blog do PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Os logs de atividade do Azure AD agora estão disponíveis para instâncias de nuvem do governo no Azure Monitor

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Estamos empolgados em anunciar que os logs de atividades do Azure AD agora estão disponíveis para instâncias de nuvem do governo no Azure Monitor. Agora você pode enviar logs do Azure AD para sua conta de armazenamento ou para um hub de eventos para integrar com suas ferramentas SIEM, como [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)e [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Para obter mais informações sobre como configurar Azure Monitor, consulte [logs de atividades do Azure AD no Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Atualize seus usuários para a nova experiência de informações de segurança aprimorada

**Tipo:** recurso alterado  
**Categoria de serviço:**  Autenticações (logons)   
**Funcionalidade do produto:** autenticação de usuário

Em 25 de setembro de 2019, vamos desligar a experiência de informações de segurança antiga e não avançada para registrar e gerenciar informações de segurança do usuário e ativar apenas a nova [versão aprimorada](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Isso significa que os usuários não poderão mais usar a experiência antiga.

Para obter mais informações sobre a experiência de informações de segurança aprimorada, consulte nossa [documentação de administração](https://aka.ms/securityinfodocs) e a [documentação do usuário](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Para ativar essa nova experiência, você deve:

1. Entre no portal do Azure como um administrador global ou administrador de usuários.

2. Vá para **Azure Active Directory > configurações de usuário > gerenciar configurações para recursos de visualização do painel de acesso**.

3. No, os **usuários podem usar os recursos de visualização para registrar e gerenciar informações de segurança – área avançada** , selecionar **selecionado**e, em seguida, escolher um grupo de usuários ou escolher **tudo** para ativar esse recurso para todos os usuários no locatário.

4. Em * * os usuários podem usar recursos de visualização para registrar e gerenciar segurança * * info * * * *, selecione **nenhum**.

5. Salve suas configurações.

    Depois de salvar suas configurações, você não terá mais acesso à antiga experiência de informações de segurança.

>[!Important]
>Se você não concluir essas etapas antes de 25 de setembro de 2019, seu locatário de Azure Active Directory será habilitado automaticamente para a experiência aprimorada. Se você tiver dúvidas, entre em contato conosco em registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>As solicitações de autenticação que usam os logons POST serão mais rigorosamente validadas

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Padrões

A partir de 2 de setembro de 2019, as solicitações de autenticação usando o método POST serão mais rigorosamente validadas em relação aos padrões HTTP. Especificamente, espaços e aspas duplas (") não serão mais removidos dos valores do formulário de solicitação. Essas alterações não devem interromper nenhum cliente existente e ajudarão a garantir que as solicitações enviadas ao Azure AD sejam manipuladas de maneira confiável todas as vezes.

Para obter mais informações, consulte os [avisos de alterações significativas do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Julho de 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plano para alteração: atualização do serviço de proxy de aplicativo para dar suporte apenas a TLS 1,2

**Tipo:** plano de alteração  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Para ajudar a fornecer a criptografia mais forte, vamos começar limitando o acesso do serviço de proxy de aplicativo somente a protocolos TLS 1,2. Essa limitação inicialmente será distribuída para os clientes que já estão usando protocolos TLS 1,2, portanto, você não verá o impacto. A reprovação completa dos protocolos TLS 1,0 e TLS 1,1 será concluída em 31 de agosto de 2019. Os clientes que ainda usam o TLS 1,0 e o TLS 1,1 receberão um aviso avançado para se preparar para essa alteração.

Para manter a conexão com o serviço de proxy de aplicativo durante essa alteração, recomendamos que você verifique se as combinações de servidor cliente e navegador-servidor estão atualizadas para usar o TLS 1,2. Também recomendamos que você inclua todos os sistemas clientes usados por seus funcionários para acessar os aplicativos publicados por meio do serviço de proxy de aplicativo.

Para obter mais informações, consulte [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plano para alteração: as atualizações de design são recebidas para a Galeria de aplicativos

**Tipo:** plano de alteração  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO

Novas alterações de interface do usuário estão chegando ao design do **Adicionar da** área da Galeria da folha **Adicionar um aplicativo** . Essas alterações ajudarão você a localizar mais facilmente seus aplicativos que dão suporte ao provisionamento automático, ao OpenID Connect, Security Assertion Markup Language (SAML) e ao SSO (logon único) com senha.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plano de alteração: remoção do endereço IP do servidor MFA do endereço IP do Office 365

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade

Estamos removendo o endereço IP do servidor MFA do [endereço IP do Office 365 e do serviço Web URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Se, no momento, você confiar nessas páginas para atualizar as configurações do firewall, deverá ter certeza de que também está incluindo a lista de endereços IP documentados na seção **requisitos do firewall do azure servidor de autenticação multifator** no artigo [introdução ao servidor de autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokens somente de aplicativo agora exigem que o aplicativo cliente exista no locatário do recurso

**Tipo:** corrigido  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

Em 26 de julho de 2019, alteramos a maneira como fornecemos tokens somente de aplicativo por meio da [concessão de credenciais de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Anteriormente, os aplicativos podiam obter tokens para chamar outros aplicativos, independentemente se o aplicativo cliente estava no locatário. Atualizamos esse comportamento para que recursos de locatário único, às vezes chamados de APIs da Web, só possam ser chamados por aplicativos cliente que existem no locatário do recurso.

Se seu aplicativo não estiver localizado no locatário de recursos, você receberá uma mensagem de erro que diz, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` para corrigir esse problema, você deve criar a entidade de serviço de aplicativo cliente no locatário, usando o [ponto de extremidade de consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) ou [por meio do PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), que garante que seu locatário tenha dado a permissão do aplicativo para operar dentro do locatário.

Para obter mais informações, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> O consentimento existente entre o cliente e a API continua a não ser necessário. Os aplicativos ainda devem estar fazendo suas próprias verificações de autorização.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nova entrada sem senha no Azure AD usando as chaves de segurança do FIDO2

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

Os clientes do Azure AD agora podem definir políticas para gerenciar chaves de segurança FIDO2 para os usuários e grupos de sua organização. Os usuários finais também podem registrar automaticamente suas chaves de segurança, usar as chaves para entrar em suas contas da Microsoft em sites, enquanto em dispositivos com capacidade de FIDO, bem como entrar em seus dispositivos Windows 10 ingressados no Azure AD.

Para obter mais informações, consulte [Habilitar entrada sem senha para o Azure AD (versão prévia)](/azure/active-directory/authentication/concept-authentication-passwordless) para obter informações relacionadas ao administrador e configurar informações de [segurança para usar uma chave de segurança (versão prévia)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) para informações relacionadas ao usuário final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – julho de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em julho de 2019, adicionamos esses 18 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Omnichannel de padrão brilhante, centro de contatos](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Nelly inteligente](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [acesso de SSO do MS Azure para Ethidex Compliance Office&trade;](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [atenção](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstrato](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [ascentl](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [hiperanna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [pharmingid WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), JFrog [artefatoy](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** monitoramento e relatórios

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Teclado](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Diretório federado](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Pico](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nova marca de serviço de Azure AD Domain Services para o grupo de segurança de rede

**Tipo:** novo recurso  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
**Capacidade do produto:** Serviços de Domínio do Azure AD

Se estiver cansado de gerenciar listas longas de endereços IP e intervalos, você poderá usar a nova marca de serviço de rede **AzureActiveDirectoryDomainServices** em seu grupo de segurança de rede do Azure para ajudar a proteger o tráfego de entrada para sua sub-rede Azure AD Domain Services rede virtual.

Para obter mais informações sobre essa nova marca de serviço, consulte [grupos de segurança de rede para Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
**Capacidade do produto:** Serviços de Domínio do Azure AD

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio do Azure AD para a visualização pública. A auditoria de segurança ajuda a fornecer informações críticas sobre os serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o armazenamento do Azure, os espaços de trabalho do Azure Log Analytics e o Hub de eventos do Azure, usando o serviço de domínio do Azure AD Portal.

Para obter mais informações, consulte [habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Novos métodos de autenticação uso & insights (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** redefinição de senha de autoatendimento  
**Funcionalidade do produto:** monitoramento e relatórios

Os novos métodos de autenticação de uso & relatórios do insights podem ajudá-lo a entender como os recursos como a autenticação multifator do Azure e a redefinição de senha de autoatendimento estão sendo registrados e usados em sua organização, incluindo o número de registrado os usuários para cada recurso, com que frequência a redefinição de senha de autoatendimento é usada para redefinir senhas e por qual método a redefinição ocorre.

Para obter mais informações, consulte [métodos de autenticação uso & insights (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Novos relatórios de segurança estão disponíveis para todos os administradores do Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Todos os administradores do Azure AD agora podem selecionar a faixa na parte superior dos relatórios de segurança existentes, como o relatório **usuários sinalizados para risco** , para começar a usar a nova experiência de segurança, conforme mostrado nos relatórios de **usuários arriscados** e de **entradas arriscadas** . Ao longo do tempo, todos os relatórios de segurança serão movidos das versões mais antigas para as novas versões, com os novos relatórios, fornecendo os seguintes recursos adicionais:

- Filtragem e classificação avançadas

- Ações em massa, como ignorar o risco do usuário

- Confirmação de entidades comprometidas ou seguras

- Estado de risco, abrangendo: em risco, ignorado, remediado e confirmado comprometido

Para obter mais informações, consulte relatório de [usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) e [registro de entradas arriscadas](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Novas auditorias de segurança para Azure AD Domain Services (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
**Capacidade do produto:** Serviços de Domínio do Azure AD

Temos o prazer de anunciar o lançamento da auditoria de segurança do serviço de domínio do Azure AD para a visualização pública. A auditoria de segurança ajuda a fornecer informações críticas sobre os serviços de autenticação transmitindo eventos de auditoria de segurança para os recursos de destino, incluindo o armazenamento do Azure, os espaços de trabalho do Azure Log Analytics e o Hub de eventos do Azure, usando o serviço de domínio do Azure AD Portal.

Para obter mais informações, consulte [habilitar auditorias de segurança para Azure AD Domain Services (versão prévia)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nova Federação B2B direta usando SAML/WS-enalimentado (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

A Federação direta ajuda a facilitar o trabalho com parceiros cuja solução de identidade gerenciada por ti não é o AD do Azure, trabalhando com sistemas de identidade que dão suporte aos padrões SAML ou WS-enalimentados. Depois de configurar uma relação de Federação direta com um parceiro, qualquer usuário convidado novo que você convidar desse domínio poderá colaborar com você usando sua conta institucional existente, tornando a experiência do usuário para seus convidados mais direta.

Para obter mais informações, consulte [Federação direta com AD FS e provedores de terceiros para usuários convidados (visualização)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** monitoramento e relatórios

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Teclado](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Diretório federado](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Pico](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nova verificação de nomes de grupo duplicados no portal do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Agora, quando você criar ou atualizar um nome de grupo no portal do Azure AD, realizaremos uma verificação para ver se você está duplicando um nome de grupo existente em seu recurso. Se determinarmos que o nome já está em uso por outro grupo, você será solicitado a modificar seu nome.

Para obter mais informações, consulte [gerenciar grupos no portal do Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>O Azure AD agora dá suporte a parâmetros de consulta estáticos em URIs de resposta (redirecionamento)

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

Os aplicativos do Azure AD agora podem registrar e usar URIs de resposta (redirecionamento) com parâmetros de consulta estáticos (por exemplo, `https://contoso.com/oauth2?idp=microsoft`) para solicitações do OAuth 2,0. O parâmetro de consulta estática está sujeito à correspondência de cadeia de caracteres para URIs de resposta, assim como qualquer outra parte do URI de resposta. Se não houver nenhuma cadeia de caracteres registrada que corresponda ao redirecionamento de URL decodificado-URI, a solicitação será rejeitada. Se o URI de resposta for encontrado, a cadeia de caracteres inteira será usada para redirecionar o usuário, incluindo o parâmetro de consulta estática.

Os URIs de resposta dinâmica ainda são proibidos porque representam um risco de segurança e não podem ser usados para reter informações de estado em uma solicitação de autenticação. Para essa finalidade, use o parâmetro `state`.

Atualmente, as telas de registro do aplicativo do portal do Azure ainda bloqueiam parâmetros de consulta. No entanto, você pode editar manualmente o manifesto do aplicativo para adicionar e testar parâmetros de consulta em seu aplicativo. Para obter mais informações, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Os logs de atividade (APIs do MS Graph) para o Azure AD agora estão disponíveis por meio de cmdlets do PowerShell

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Estamos empolgados em anunciar que os logs de atividades do Azure AD (relatórios de auditoria e de entradas) agora estão disponíveis por meio do módulo do PowerShell do Azure AD. Anteriormente, você poderia criar seus próprios scripts usando os pontos de extremidade do MS API do Graph e agora estendemos esse recurso aos cmdlets do PowerShell.

Para obter mais informações sobre como usar esses cmdlets, consulte [cmdlets do PowerShell do Azure ad para relatórios](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controles de filtro atualizados para logs de auditoria e de entrada no Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Atualizamos os relatórios de auditoria e log de entrada para que você possa aplicar vários filtros sem precisar adicioná-los como colunas nas telas de relatório. Além disso, agora você pode decidir quantos filtros deseja mostrar na tela. Todas as atualizações funcionam em conjunto para facilitar a leitura e o escopo de seus relatórios de forma a suas necessidades.

Para obter mais informações sobre essas atualizações, consulte [Filtrar logs de auditoria](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) e [Filtrar atividades de entrada](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Junho de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nova API do riskDetections para Microsoft Graph (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Temos o prazer de anunciar a nova API do riskDetections para Microsoft Graph está agora em visualização pública. Você pode usar essa nova API para exibir uma lista de usuários relacionados à proteção de identidade da sua organização e as detecções de risco de entrada. Você também pode usar essa API para consultar com mais eficiência suas detecções de risco, incluindo detalhes sobre o tipo de detecção, status, nível e muito mais.

Para obter mais informações, consulte a [documentação de referência de API de detecção de riscos](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – junho de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em junho de 2019, adicionamos esses 22 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Kit de ferramentas de SAML do Azure ad](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [anágua](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [cliente de VPN do Azure](https://portal.azure.com/), [despesa](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial) [, auxiliar auxiliar,](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial) [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz no carro](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [console de infraestrutura de nuvem da Oracle](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [autenticação SAML CyberArk](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible edu](https://www.scrible.com/sign-in/#/create-account) [, PandaDoc, Perceptyx](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial) [, Proptimise](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial) [os](https://proptimise.co.uk/software/), vtiger [CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager para Oracle Merchandising de varejo, Oracle Access Manager para Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o provisionamento de conta de usuário para esses aplicativos SaaS recentemente suportados

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** monitoramento e relatórios

Agora você pode automatizar a criação, atualização e exclusão de contas de usuário para esses aplicativos integrados recentemente:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor sua organização usando o provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuários para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Exibir o progresso em tempo real do serviço de provisionamento do Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade

Atualizamos a experiência de provisionamento do Azure AD para incluir uma nova barra de progresso que mostre a você o quanto você está no processo de provisionamento do usuário. Essa experiência atualizada também fornece informações sobre o número de usuários provisionados durante o ciclo atual, bem como quantos usuários foram provisionados até o momento.

Para obter mais informações, consulte [verificar o status do provisionamento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A identidade visual da empresa agora aparece nas telas sair e erro

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

Atualizamos o Azure AD para que a identidade visual da sua empresa agora apareça nas telas de saída e erro, bem como na página de entrada. Você não precisa fazer nada para ativar esse recurso, o AD do Azure simplesmente usa os ativos que você já configurou na área de **identidade visual da empresa** do portal do Azure.

Para obter mais informações sobre como configurar a identidade visual da sua empresa, consulte [Adicionar identidade visual às páginas de Azure Active Directory da sua organização](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>O servidor de autenticação multifator (MFA) do Azure não está mais disponível para novas implantações

**Tipo:** preterido  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade

A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Os novos clientes que desejam exigir a autenticação multifator em sua organização agora devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes que ativaram o servidor MFA antes de 1º de julho não verão uma alteração. Você ainda poderá baixar a versão mais recente, obter atualizações futuras e gerar credenciais de ativação.

Para obter mais informações, consulte [introdução ao servidor de autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Para obter mais informações sobre a autenticação multifator do Azure baseada em nuvem, consulte [planejando uma implantação da autenticação multifator do Azure baseada em nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maio de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Alteração de serviço: suporte futuro somente para protocolos TLS 1,2 no serviço de proxy de aplicativo

**Tipo:** plano de alteração  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Para ajudar a fornecer a melhor criptografia para nossos clientes, estamos limitando o acesso somente a protocolos TLS 1,2 no serviço de proxy de aplicativo. Essa alteração é gradualmente distribuída para clientes que já usam protocolos TLS 1,2, portanto, você não deve ver nenhuma alteração.

A substituição do TLS 1,0 e do TLS 1,1 ocorre em 31 de agosto de 2019, mas forneceremos um aviso avançado adicional, portanto, você terá tempo para se preparar para essa alteração. Para se preparar para essa alteração, verifique se as combinações de servidor cliente e navegador-servidor, incluindo os clientes que os usuários usam para acessar os aplicativos publicados por meio do proxy de aplicativo, foram atualizadas para usar o protocolo TLS 1,2 para manter a conexão com o aplicativo Serviço de proxy. Para obter mais informações, consulte [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usar o relatório de uso e informações para exibir os dados de entrada relacionados ao aplicativo

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** monitoramento e relatórios

Agora você pode usar o relatório de uso e informações, localizado na área **aplicativos empresariais** do portal do Azure, para obter uma exibição centrada no aplicativo de seus dados de entrada, incluindo informações sobre:

- Principais aplicativos usados para sua organização

- Aplicativos com as entradas mais malsucedidas

- Principais erros de entrada para cada aplicativo

Para obter mais informações sobre esse recurso, consulte [relatório de informações e uso no portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizar seu provisionamento de usuário para aplicativos de nuvem usando o Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** monitoramento e relatórios

Siga estes novos tutoriais para usar o serviço de provisionamento do Azure AD para automatizar a criação, a exclusão e a atualização de contas de usuário para os seguintes aplicativos baseados em nuvem:

- [Coencontro](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Você também pode seguir este novo [tutorial do Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), que fornece informações sobre como provisionar objetos de grupo.

Para obter mais informações sobre como proteger melhor sua organização por meio do provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuário para aplicativos SaaS com o Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>A pontuação de segurança de identidade agora está disponível no Azure AD (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** segurança e proteção da identidade

Agora você pode monitorar e melhorar sua postura de segurança de identidade usando o recurso identidade de Pontuação segura no Azure AD. O recurso de Pontuação segura de identidade usa um único painel para ajudá-lo:

- Meça objetivamente sua postura de segurança de identidade, com base em uma pontuação entre 1 e 223.

- Planejar suas melhorias de segurança de identidade

- Examine o sucesso de suas melhorias de segurança

Para obter mais informações sobre o recurso de Pontuação de segurança de identidade, consulte [o que é a pontuação segura de identidade em Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nova experiência de Registros de aplicativo agora está disponível (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** experiência de desenvolvedor

A nova experiência de [registros de aplicativo](https://aka.ms/appregistrations) agora está em disponibilidade geral. Essa nova experiência inclui todos os principais recursos com os quais você está familiarizado a partir da portal do Azure e do portal de registro de aplicativos e o melhora por meio de:

- **Melhor gerenciamento de aplicativos.** Em vez de ver seus aplicativos em portais diferentes, agora você pode ver todos os seus aplicativos em um único local.

- **Registro de aplicativo simplificado.** Da melhor experiência de navegação à experiência de seleção de permissão remodelada, agora é mais fácil registrar e gerenciar seus aplicativos.

- **Informações mais detalhadas.** Você pode encontrar mais detalhes sobre seu aplicativo, incluindo guias de início rápido e muito mais.

Para obter mais informações, consulte [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) e a experiência de [registros de aplicativo agora está disponível para o público geral!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) comunicado do blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Novos recursos disponíveis na API de usuários arriscados para a proteção de identidade

**Tipo:** novo recurso  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Temos o prazer de anunciar que agora você pode usar a API de usuários arriscados para recuperar o histórico de riscos dos usuários, ignorar usuários arriscados e confirmar os usuários como comprometidos. Essa alteração ajuda a atualizar com mais eficiência o status de risco de seus usuários e a entender seu histórico de riscos.

Para obter mais informações, consulte a [documentação de referência da API de usuários arriscados](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – maio de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em maio de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [real links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Sign-simples](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [displayer](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [marketo Sales entrave](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [outsubsystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [WebMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [alimento](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Experiência aprimorada de criação e gerenciamento de grupos no portal do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Fizemos melhorias nas experiências relacionadas a grupos no portal do Azure AD. Esses aprimoramentos permitem que os administradores gerenciem melhor listas de grupos, listas de membros e forneçam opções de criação adicionais.

As melhorias incluem:

- Filtragem básica por tipo de associação e tipo de grupo.

- Adição de novas colunas, como a origem e o endereço de email.

- Capacidade de selecionar vários grupos, membros e listas de proprietários para fácil exclusão.

- Capacidade de escolher um endereço de email e adicionar proprietários durante a criação do grupo.

Para obter mais informações, consulte [criar um grupo básico e adicionar membros usando Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do AD do Azure (disponibilidade geral)

**Tipo:** recurso alterado  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Agora, os administradores podem configurar uma política de nomenclatura para grupos do Office 365 usando o portal do AD do Azure. Essa alteração ajuda a impor convenções de nomenclatura consistentes para grupos do Office 365 criados ou editados por usuários em sua organização.

Você pode configurar a política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são adicionados automaticamente a um nome de grupo.

- Carregue um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidas em nomes de grupos (por exemplo, "CEO, Payroll, HR").

Para obter mais informações, consulte [impor uma política de nomenclatura para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Os pontos de extremidade de API Microsoft Graph agora estão disponíveis para logs de atividade do Azure AD (disponibilidade geral)

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Estamos felizes em anunciar a disponibilidade geral do suporte aos pontos de extremidade da API de Microsoft Graph para os logs de atividades do Azure AD. Com esta versão, agora você pode usar a versão 1,0 dos logs de auditoria do Azure AD, bem como as APIs de logs de entrada.

Para obter mais informações, consulte [visão geral da API de log de auditoria do Azure ad](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Os administradores agora podem usar o acesso condicional para o processo de registro combinado (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade  

Agora, os administradores podem criar políticas de acesso condicional para uso pela página de registro combinada. Isso inclui a aplicação de políticas para permitir o registro se:

- Os usuários estão em uma rede confiável.

- Os usuários são um risco de entrada baixo.

- Os usuários estão em um dispositivo gerenciado.

- Os usuários concordam com os termos de uso da organização (TOU).

Para obter mais informações sobre o acesso condicional e a redefinição de senha, você pode ver o [acesso condicional para a publicação de blog de MFA combinada do Azure AD e a experiência de registro de redefinição de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Para obter mais informações sobre políticas de acesso condicional para o processo de registro combinado, consulte [políticas de acesso condicional para registro combinado](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Para obter mais informações sobre o recurso de termos de uso do Azure AD, consulte [Azure Active Directory recurso termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>A nova detecção de inteligência contra ameaças do Azure AD agora está disponível como parte do Azure AD Identity Protection

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD Identity Protection  
**Funcionalidade do produto:** segurança e proteção da identidade

A detecção de inteligência contra ameaças do Azure AD agora está disponível como parte do recurso de Azure AD Identity Protection atualizado. Essa nova funcionalidade ajuda a indicar atividade de usuário incomum para um usuário ou atividade específica que é consistente com padrões de ataque conhecidos com base nas fontes de inteligência contra ameaças internas e externas da Microsoft.

Para obter mais informações sobre a versão atualizada do Azure AD Identity Protection, consulte os [quatro principais aprimoramentos de Azure ad Identity Protection agora estão no blog de visualização pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) e o [que é Azure Active Directory Identity Protection (atualizado)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artigo. Para obter mais informações sobre a detecção de inteligência contra ameaças do Azure AD, consulte o artigo [Azure Active Directory Identity Protection de detecções de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>O gerenciamento de direitos do Azure AD agora está disponível (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Governança de identidade  
**Funcionalidade do produto:** Governança de identidade

O gerenciamento de direitos do Azure AD, agora em visualização pública, ajuda os clientes a delegar o gerenciamento de pacotes de acesso, que define como os funcionários e parceiros de negócios podem solicitar acesso, quem deve aprovar e por quanto tempo eles têm acesso. Os pacotes do Access podem gerenciar a associação nos grupos do Azure AD e do Office 365, atribuições de função em aplicativos empresariais e atribuições de função para sites do SharePoint Online. Leia mais sobre o gerenciamento de direitos na [visão geral do gerenciamento de direitos do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Para saber mais sobre a amplitude dos recursos de Azure AD Identity Governance, incluindo Privileged Identity Management, revisões de acesso e termos de uso, consulte [o que é Azure ad Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurar uma política de nomenclatura para grupos do Office 365 no portal do Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Agora, os administradores podem configurar uma política de nomenclatura para grupos do Office 365 usando o portal do AD do Azure. Essa alteração ajuda a impor convenções de nomenclatura consistentes para grupos do Office 365 criados ou editados por usuários em sua organização.

Você pode configurar a política de nomenclatura para grupos do Office 365 de duas maneiras diferentes:

- Defina prefixos ou sufixos, que são adicionados automaticamente a um nome de grupo.

- Carregue um conjunto personalizado de palavras bloqueadas para sua organização, que não são permitidas em nomes de grupos (por exemplo, "CEO, Payroll, HR").

Para obter mais informações, consulte [impor uma política de nomenclatura para grupos do Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Os logs de atividade do Azure AD agora estão disponíveis no Azure Monitor (disponibilidade geral)

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Para ajudar a resolver seus comentários sobre visualizações com os logs de atividades do Azure AD, estamos introduzindo um novo recurso de informações no Log Analytics. Esse recurso ajuda você a obter informações sobre seus recursos do Azure AD usando nossos modelos interativos, chamados de pastas de trabalho. Essas pastas de trabalho predefinidas podem fornecer detalhes para aplicativos ou usuários e incluem:

- **Entradas.** Fornece detalhes para aplicativos e usuários, incluindo o local de entrada, o sistema operacional em uso ou o cliente e a versão do navegador e o número de entradas com êxito ou com falha.

- **Autenticação herdada e acesso condicional.** Fornece detalhes para aplicativos e usuários usando a autenticação herdada, incluindo o uso da autenticação multifator disparado pelas políticas de acesso condicional, aplicativos que usam políticas de acesso condicional e assim por diante.

- **Análise de falha de entrada.** Ajuda a determinar se os erros de entrada estão ocorrendo devido a uma ação do usuário, a problemas de política ou à sua infraestrutura.

- **Relatórios personalizados.** Você pode criar novas ou editar pastas de trabalho existentes para ajudar a personalizar o recurso de informações para sua organização.

Para obter mais informações, consulte [como usar pastas de trabalho do Azure monitor para relatórios Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – abril de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em abril de 2019, adicionamos esses 21 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix Netscaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [bancada](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [alibaba Cloud (SSO baseado em função)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent gerenciamento de patrimônio](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial) [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial) [Workgrid, Monday.com, SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nova opção de frequência de revisões de acesso e seleção de várias funções

**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** Governança de identidade

Novas atualizações nas revisões de acesso do Azure AD permitem que você:

- Altere a frequência de suas revisões de acesso para **semianualmente**, além das opções de semana, mensal, trimestral e anual existentes anteriormente.

- Selecione várias funções do Azure AD e recursos do Azure ao criar uma revisão de acesso único. Nessa situação, todas as funções são configuradas com as mesmas configurações e todos os revisores são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso, consulte [criar uma revisão de acesso de grupos ou aplicativos nas revisões de acesso do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect sistema (es) de alertas de email estão em transição, enviando novas informações de remetente de email para alguns clientes

**Tipo:** recurso alterado  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** plataforma

Azure AD Connect está no processo de transição de nossos sistemas de alerta de email, potencialmente mostrando a alguns clientes um novo remetente de email. Para resolver isso, você deve adicionar `azure-noreply@microsoft.com` à lista de permissões da sua organização ou não poderá continuar recebendo alertas importantes do seu Office 365, do Azure ou dos serviços de sincronização.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>As alterações de sufixo UPN agora são bem-sucedidas entre domínios federados no Azure AD Connect

**Tipo:** corrigido  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** plataforma

Agora você pode alterar com êxito o sufixo UPN do usuário de um domínio federado para outro domínio federado em Azure AD Connect. Essa correção significa que você não deve mais experimentar a mensagem de erro FederatedDomainChangeError durante o ciclo de sincronização ou receber um email de notificação informando "não é possível atualizar este objeto no Azure Active Directory, pois o atributo [ FederatedUser. UserPrincipalName], não é válido. Atualize o valor em seus serviços de diretório local ".

Para obter mais informações, consulte [Solucionando erros durante a sincronização](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maior segurança usando a política de acesso condicional com base na proteção do aplicativo no Azure AD (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

O acesso condicional baseado em proteção de aplicativo agora está disponível usando a política de **proteção de aplicativo necessária** . Essa nova política ajuda a aumentar a segurança da sua organização, ajudando a evitar:

- Usuários que obtêm acesso a aplicativos sem uma licença de Microsoft Intune.

- Os usuários não conseguem obter uma política de proteção de aplicativo Microsoft Intune.

- Usuários que obtêm acesso a aplicativos sem uma política de proteção de aplicativo Microsoft Intune configurada.

Para obter mais informações, consulte [como exigir a política de proteção de aplicativo para acesso de aplicativo de nuvem com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para logon único do Azure AD e acesso condicional no Microsoft Edge (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

Aprimoramos nosso suporte do Azure AD para o Microsoft Edge, incluindo o fornecimento de novo suporte para logon único do Azure AD e acesso condicional. Se você já usou Microsoft Intune Managed Browser, agora você pode usar o Microsoft Edge em vez disso.

Para obter mais informações sobre como configurar e gerenciar seus dispositivos e aplicativos usando o acesso condicional, consulte [exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Para obter mais informações sobre como gerenciar o acesso usando o Microsoft Edge com políticas de Microsoft Intune, consulte [gerenciar o acesso à Internet usando um navegador Microsoft Intune protegido por política](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Março de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>O Identity Experience Framework e o suporte de política personalizada no Azure Active Directory B2C agora estão disponíveis (GA)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode criar políticas personalizadas no Azure AD B2C, incluindo as seguintes tarefas, que têm suporte em escala e em nosso SLA do Azure:

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.

- Descrever percursos do usuário passo a passo como trocas entre provedores de declarações.

- Definir a ramificação condicional em percursos do usuário.

- Transforme e mapeie declarações para uso em decisões e comunicações em tempo real.

- Use os serviços habilitados para API REST em suas jornadas do usuário de autenticação personalizada. Por exemplo, com provedores de email, CRMs e sistemas de autorização proprietários.

- Federar com provedores de identidade que estão em conformidade com o protocolo OpenIDConnect. Por exemplo, com vários locatários do Azure AD, provedores de contas sociais ou provedores de verificação de dois fatores.

Para obter mais informações sobre como criar políticas personalizadas, consulte [notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leia a [postagem do blog de Alex Simon, incluindo estudos de caso](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – março de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em março de 2019, adicionamos esses 14 novos aplicativos com suporte de Federação à galeria de aplicativos:

[ISEC7 Mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [sistema de auditoria baseado em explicação](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool de desempenho importante](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [intranet da íris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [horizontes de Confirmit](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [tas](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novos conectores de provisionamento Zscaler e Atlassian na galeria do Azure AD – março de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros

Automatize a criação, atualização e exclusão de contas de usuário para os seguintes aplicativos:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler dois](https://aka.ms/ZscalerTwoProvisioning), [Zscaler três](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Para obter mais informações sobre como proteger melhor sua organização por meio do provisionamento automatizado de contas de usuário, confira automatizar o [provisionamento de usuário para aplicativos SaaS com o Azure ad](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerenciar seus grupos excluídos do Office 365 no portal do AD do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Agora você pode exibir e gerenciar seus grupos excluídos do Office 365 no portal do Azure AD. Essa alteração ajuda a ver quais grupos estão disponíveis para restauração, além de permitir que você exclua permanentemente todos os grupos que não são necessários para sua organização.

Para obter mais informações, consulte [restauração expirada ou grupos excluídos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>O logon único agora está disponível para aplicativos locais do Azure AD com segurança SAML por meio do proxy de aplicativo (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Agora você pode fornecer uma experiência de SSO (logon único) para aplicativos locais, autenticados por SAML, junto com o acesso remoto a esses aplicativos por meio do proxy de aplicativo. Para obter mais informações sobre como configurar o SSO do SAML com seus aplicativos locais, consulte [logon único do SAML para aplicativos locais com o proxy de aplicativo (versão prévia)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Os aplicativos cliente em loops de solicitação serão interrompidos para melhorar a confiabilidade e a experiência do usuário

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

Os aplicativos cliente podem emitir incorretamente centenas das mesmas solicitações de logon em um curto período de tempo. Essas solicitações, sejam elas bem-sucedidas ou não, todas contribuem para uma experiência de usuário ruim e cargas de trabalho aumentadas para o IDP, aumentando a latência para todos os usuários e reduzindo a disponibilidade do IDP.

Essa atualização envia um erro de `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` aos aplicativos cliente que emitem solicitações duplicadas várias vezes por um curto período de tempo, além do escopo da operação normal. Os aplicativos cliente que encontram esse problema devem mostrar um prompt interativo, exigindo que o usuário entre novamente. Para obter mais informações sobre essa alteração e sobre como corrigir seu aplicativo se ele encontrar esse erro, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Novos logs de auditoria a experiência do usuário agora está disponível

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Criamos uma nova página de **logs de auditoria** do Azure ad para ajudar a melhorar a legibilidade e como pesquisar suas informações. Para ver a página novos **logs de auditoria** , selecione logs de **auditoria** na seção **atividade** do Azure AD.

![Página novos logs de auditoria, com informações de exemplo](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre a página novos **logs de auditoria** , consulte [auditoria de relatórios de atividade no portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e diretrizes para ajudar a impedir o bloqueio acidental de administrador de políticas de acesso condicional configuradas incorretamente

**Tipo:** recurso alterado  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

Para ajudar a impedir que os administradores bloqueiem acidentalmente seus próprios locatários por meio de políticas de acesso condicional configuradas incorretamente, criamos novos avisos e diretrizes atualizadas no portal do Azure. Para obter mais informações sobre as novas diretrizes, consulte [o que são dependências de serviço em Azure Active Directory acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Experiência aprimorada dos termos de uso do usuário final em dispositivos móveis

**Tipo:** recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança

Atualizamos nossas experiências existentes de termos de uso para ajudar a melhorar o modo de revisar e consentir os termos de uso em um dispositivo móvel. Agora você pode ampliar e reduzir, voltar, baixar as informações e selecionar hiperlinks. Para obter mais informações sobre os termos de uso atualizados, consulte [Azure Active Directory recurso termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nova experiência de download de logs de atividades do Azure AD disponível

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Agora você pode baixar grandes quantidades de logs de atividade diretamente do portal do Azure. Essa atualização permite que você:

- Baixe até 250.000 linhas.

- Seja notificado após a conclusão do download.

- Personalize o nome do arquivo.

- Determine o formato de saída, JSON ou CSV.

Para obter mais informações sobre esse recurso, consulte [início rápido: baixar um relatório de auditoria usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alteração significativa: atualizações para a avaliação de condição pelo Exchange ActiveSync (EAS)

**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Controle de Acesso

Estamos no processo de atualizar como o Exchange ActiveSync (EAS) avalia as seguintes condições:

- Local do usuário, com base no país, na região ou no endereço IP

- Risco de entrada

- Plataforma de dispositivos

Se você já usou essas condições em suas políticas de acesso condicional, lembre-se de que o comportamento da condição pode ser alterado. Por exemplo, se você usou anteriormente a condição de local de usuário em uma política, você pode achar que a política agora está sendo ignorada com base no local do usuário.

---

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Criptografia do token SAML do Azure AD configurável (Versão prévia pública) 

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO

Agora você pode configurar qualquer aplicativo SAML com suporte para receber tokens SAML criptografados. Quando configurado e usado com um aplicativo, o AD do Azure criptografa as asserções SAML emitidas usando uma chave pública Obtida de um certificado armazenado no Azure AD.

Para obter mais informações sobre como configurar a criptografia de token SAML, consulte [Configurar a criptografia de token SAML do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicativos usando as Revisões de Acesso do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** governança

Agora você pode incluir vários grupos ou aplicativos em uma única revisão de acesso do Azure AD para associação de grupo ou atribuição de aplicativo. As revisões de acesso com vários grupos ou aplicativos são configuradas usando as mesmas configurações e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma análise de acesso usando revisões de acesso do Azure AD, consulte [criar uma revisão de acesso de grupos ou aplicativos nas revisões de acesso do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novos Aplicativos Federados disponíveis na galeria de aplicativos do Azure AD – fevereiro de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em fevereiro de 2019, adicionamos esses 27 novos aplicativos com suporte à Federação para a Galeria de aplicativos:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [Fat Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [enstack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), permissão clique, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile, Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial) [, Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [sísmica](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Compartilhe um sonho](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [nuvem de integração de WebMethods](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial) [conhecimento Em qualquer lugar LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [campus da UO](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.Io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registro de MFA/SSPR combinado aprimorado

**Tipo:** recurso alterado  
**Categoria de serviço:** redefinição de senha de autoatendimento  
**Funcionalidade do produto:** autenticação de usuário

Em resposta aos comentários dos clientes, aprimoramos a experiência combinada de visualização do registro MFA/SSPR, ajudando seus usuários a registrarem com mais rapidez suas informações de segurança tanto para MFA quanto para SSPR. 

**Para ativar a experiência aprimorada para os seus usuários hoje, siga estas etapas:**

1. Como administrador global ou administrador de usuários, entre no portal do Azure e vá para **Azure Active Directory > configurações de usuário > gerenciar configurações para recursos de visualização do painel de acesso**. 

2. Nos **usuários que podem usar os recursos de visualização para registrar e gerenciar informações de segurança –** opção de atualização, escolha Ativar os recursos para um **grupo de usuários selecionado** ou para **todos os usuários**.

Nas próximas semanas, removeremos a capacidade de ativar a experiência de visualização de registro MFA/SSPR combinada antiga para locatários que ainda não estão ativados.

**Para ver se o controle será removido para seu locatário, siga estas etapas:**

1. Como administrador global ou administrador de usuários, entre no portal do Azure e vá para **Azure Active Directory > configurações de usuário > gerenciar configurações para recursos de visualização do painel de acesso**.  

2. Se os **usuários que podem usar os recursos de visualização para registrar e gerenciar informações de segurança** estiverem definidos como **nenhum**, a opção será removida do seu locatário.

Independentemente de você ter ativado a antiga experiência de visualização de registro MFA/SSPR combinada para usuários ou não, a experiência antiga será desativada em uma data futura. Por isso, é altamente recomendável que você migre para a nova experiência aprimorada assim que possível.

Para obter mais informações sobre a experiência de registro aprimorada, consulte os [aprimoramentos legais para a experiência combinada do Azure AD e do registro de redefinição de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência de gerenciamento de política atualizada para fluxos dos usuários

**Tipo:** recurso alterado  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Atualizamos o processo de criação e gerenciamento de política para fluxos de usuário (anteriormente conhecidos como políticas internas) mais fácil. Essa nova experiência agora é o padrão para todos os seus locatários do Azure AD.

Você pode fornecer comentários e sugestões adicionais usando os ícones Smiley ou rosto triste na área **enviar comentários** na parte superior da tela do Portal.

Para obter mais informações sobre a nova experiência de gerenciamento de política, consulte o blog [Azure ad B2C agora tem personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolha as versões de elemento de página específicas fornecidas pelo Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode escolher uma versão específica dos elementos da página fornecidos por Azure AD B2C. Ao selecionar uma versão específica, você pode testar suas atualizações antes que elas apareçam em uma página e você pode obter um comportamento previsível. Além disso, agora você pode optar por impor versões de página específicas para permitir personalizações de JavaScript. Para ativar esse recurso, vá para a página **Propriedades** em seus fluxos de usuário.

Para obter mais informações sobre como escolher versões específicas de elementos de página, consulte o blog [Azure ad B2C agora tem personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) .

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de senha do usuário final configuráveis para B2C (GA)

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode configurar a complexidade de senha de sua organização para seus usuários finais, em vez de ter que usar sua política de senha nativa do Azure AD. Na folha **Propriedades** de seus fluxos de usuário (anteriormente conhecido como suas políticas internas), você pode escolher uma complexidade de senha **simples** ou **forte**, ou pode criar um conjunto **personalizado** de requisitos.

Para obter mais informações sobre a configuração de requisito de complexidade de senha, consulte [Configurar requisitos de complexidade para senhas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos padrão para as experiências de autenticação de marca personalizada

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C

Você pode usar nossos novos modelos padrão, localizados na folha **layouts de página** de seus fluxos de usuário (anteriormente conhecidos como políticas internas), para criar uma experiência de autenticação com marca personalizada para seus usuários.

Para obter mais informações sobre como usar os modelos, consulte [Azure ad B2C agora tem a personalização de JavaScript e muitos outros recursos novos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração do Active Directory B2B usando a autenticação de senha de uso único (Versão prévia pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Apresentamos a OTP (autenticação por senha de uso único) para usuários convidados de B2B que não podem ser autenticados por outros meios, tais como o Azure AD, uma MSA (conta Microsoft) ou uma federação do Google. Esse novo método de autenticação significa que os usuários convidados não precisarão criar uma nova conta Microsoft. Em vez disso, ao resgatar um convite ou acessar um recurso compartilhado, o usuário convidado poderá solicitar um código temporário que será enviado para um endereço de email. Usando esse código temporário, o usuário convidado pode continuar a conexão.

Para obter mais informações, consulte [Autenticação de senha de uso único por email (versão prévia)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e o blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (O Azure AD torna a colaboração e o compartilhamento perfeitos para qualquer usuário com qualquer conta).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas configurações de cookies do Proxy de Aplicativo do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Apresentamos três novas configurações de cookie, disponíveis para seus aplicativos publicados por meio do Proxy de Aplicativo:

- **Usar Cookie Somente HTTP.** Define o sinalizador **HTTPOnly** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração fornece benefícios de segurança adicionais, como ajudar a prevenir cópia ou modificação de cookies por meio de script do lado do cliente. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar um cookie seguro.** Define o sinalizador **Secure** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração proporciona benefícios de segurança adicionais ao garantir que os cookies só sejam transmitidos em canais seguros TLS, como HTTPS. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar cookie persistente.** Impede que cookies de acesso expirem quando o navegador da Web é fechado. Esses cookies duram todo o tempo de vida do token de acesso. No entanto, os cookies são redefinidos se o tempo de término é atingido ou se o usuário o exclui manualmente. Recomendamos que você mantenha a configuração padrão de **Não**, apenas ativando essa configuração para aplicativos mais antigos que não compartilham cookies entre processos.

Para obter mais informações sobre os novos cookies, confira [Configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD – janeiro de 2019

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em janeiro de 2019, adicionamos esses 35 novos aplicativos com suporte para Federação à galeria de aplicativos:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [paleta de talentos](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial)Cloud, [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), InstaVR [Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [serviço de nuvem Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [viável](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNEXUS SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE Insight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ares for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 para Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [visitado](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novas melhorias do Azure AD Identity Protection (versão prévia pública)

**Tipo:** recurso alterado  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Temos o prazer de anunciar que adicionamos os seguintes aprimoramentos à oferta de versão prévia pública do Azure AD Identity Protection, incluindo:

- Uma interface do usuário mais integrada e atualizada

- APIs adicionais

- Avaliação de risco aprimorada por meio de aprendizado de máquina

- Alinhamento de todo o produto entre usuários arriscados e entradas arriscadas

Para obter mais informações sobre os aprimoramentos, confira [O que é o Azure Active Directory Identity Protection (atualizado)?](https://aka.ms/IdentityProtectionDocs) Para saber mais e compartilhar suas ideias por meio dos prompts no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Novo recurso de Bloqueio de Aplicativo para o aplicativo Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** novo recurso  
**Categoria de serviço:** Microsoft Authenticator aplicativo  
**Funcionalidade do produto:** segurança e proteção da identidade

Para manter suas senhas únicas, informações do aplicativo e configurações de aplicativo mais seguras, você pode ativar o recurso de Bloqueio de Aplicativo no aplicativo Microsoft Authenticator. Ativar o bloqueio do aplicativo significa que você será solicitado a autenticar usando seu PIN ou biométrica toda vez que abrir o aplicativo Microsoft Authenticator.

Para obter mais informações, confira as [Perguntas frequentes do aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Funcionalidades de exportação aprimoradas do Azure AD PIM (Privileged Identity Management)

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management

Administradores do PIM (Privileged Identity Management) agora podem exportar todas as atribuições de função ativas e qualificadas para um recurso específico, incluindo atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico.

Para obter mais informações, confira [Exibir histórico de atividade e auditoria para funções de recurso do Azure no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembro/dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Os usuários removidos do escopo de sincronização não alternam mais para contas somente nuvem

**Tipo:** corrigido  
**Categoria de serviço:** Gerenciamento de usuários  
**Funcionalidade do produto:** diretório

>[!Important]
>Nós ouvimos e entendemos sua frustração devido a essa correção. Portanto, revertemos essa alteração até o momento em que possamos facilitar a correção, para que você possa implementar em sua organização.

Corrigimos um bug no qual o sinalizador DirSyncEnabled de um usuário seria erroneamente alternado para **falso** quando o objeto Active Directory Domain Services (AD DS) foi excluído do escopo de sincronização e, em seguida, movido para a lixeira no Azure AD no ciclo de sincronização a seguir. Como resultado dessa correção, se o usuário for excluído do escopo de sincronização e, em seguida, restaurado da Lixeira do Azure AD, a conta do usuário permanecerá como sincronizada do AD local, conforme o esperado, e não poderá ser gerenciada na nuvem, já que a SoA (origem da autoridade) permanecerá como AD local.

Antes dessa correção, havia um problema quando o sinalizador DirSyncEnabled era alternado para Falso. Isso resultava na impressão errada de que essas contas eram convertidas em objetos somente nuvem e que as contas poderiam ser gerenciadas na nuvem. No entanto, as contas ainda retinham a SoA como propriedades locais e todas as propriedades sincronizadas (atributos de sombra) provenientes do AD local. Essa condição causou vários problemas no Azure AD e em outras cargas de trabalho de nuvem (como o Exchange Online) que esperavam tratar essas contas como sincronizadas do AD, mas agora se comportavam como contas somente nuvem.

No momento, a única maneira de converter verdadeiramente uma conta do AD de sincronizada para uma conta somente nuvem é desabilitando o DirSync no nível do locatário, que disparará uma operação de back-end para transferir a SoA. Esse tipo de alteração da SoA exige (mas não limita-se a) limpar todos os atributos relacionados locais (como LastDirSyncTime e atributos de sombra) e enviar um sinal para outras cargas de trabalho de nuvem para que o respectivo objeto também seja convertido em uma conta somente nuvem.

Essa correção, consequentemente, impede atualizações diretas no atributo ImmutableID de um usuário sincronizado do AD, que em alguns cenários no passado foram necessários. Por padrão, o ImmutableID de um objeto no Azure AD, como o nome indica, é imutável. Os novos recursos implementados no Azure AD Connect Health e no cliente de Sincronização do Azure AD Connect estão disponíveis para tratar esses cenários:

- **Atualização do ImmutableID em grande escala para muitos usuários em uma abordagem em etapas**
  
  Por exemplo, você precisa fazer uma migração longa entre florestas do AD DS. Solução: Use Azure AD Connect para **Configurar a âncora de origem** e, conforme o usuário migra, copie os valores imutáveis existentes do Azure ad para o atributo ms-DS-Consistency-GUID do usuário AD DS local da nova floresta. Para obter mais informações, consulte [Usar ms-DS-ConsistencyGuid como sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações do ImmutableID em grande escala para muitos usuários em um único disparo**

  Por exemplo, ao implementar o Azure AD Connect, você comete um erro e agora precisa alterar o atributo SourceAnchor. Solução: desabilite o DirSync no nível do locatário e limpe todos os valores imutáveis inválidos. Para obter mais informações, consulte [Desativar a sincronização de diretório do Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Corresponder novamente o usuário local com um usuário existente no Azure AD** Por exemplo, um usuário que foi recriado no AD DS gera uma duplicata na conta do Azure AD, em vez de correspondê-la novamente com uma conta do Azure AD (objeto órfão) existente. Solução: Use Azure AD Connect Health na portal do Azure para remapear a âncora de origem/imutável. Para obter mais informações, consulte [Cenário de objeto órfão](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alteração significativa: atualizações para o esquema de logs de auditoria e de entrada por meio do Azure Monitor

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

No momento, estamos publicando os fluxos de log de Auditoria e Entrada por meio do Azure Monitor, para que você possa integrar os arquivos de log às suas ferramentas SIEM ou ao Log Analytics. Com base em seus comentários e em preparação para o comunicado de disponibilidade geral desse recurso, estamos fazendo as alterações a seguir em nosso esquema. Essas alterações de esquema e suas atualizações de documentação relacionadas ocorrerão até a primeira semana de janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de Auditoria
Estamos adicionando um novo campo **Tipo de Operação**, para fornecer o tipo de operação executada no recurso. Por exemplo, **Adicionar**, **Atualizar** ou **Excluir**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de Auditoria
Os seguintes campos estão sendo alterados no esquema de Auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|Categoria|Esse era o campo **Nome do Serviço**. Agora ele passou a ser o campo **Categorias de Auditoria**. **Nome do Serviço** foi renomeado para o campo **loggedByService**.|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|<ul><li>Gerenciamento de Usuários</li><li>Gerenciamento de grupos</li><li>Gerenciamento de aplicativos</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicativo</li><li>Usuário</li><li>Agrupar</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o log de auditoria.|Nulo|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|
|Result|Fornece o resultado dos logs de auditoria. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sucesso</li><li>Falha</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de Entrada
Os seguintes campos estão sendo alterados no esquema de Entrada:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Esse era o campo **conditionalaccessPolicies**. Agora ele passou a ser o campo **appliedConditionalAccessPolicies**.|Nenhuma alteração|Nenhuma alteração|
|conditionalAccessStatus|Fornece o resultado do Status da Política de Acesso Condicional na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Falha</li><li>Não aplicado</li><li>Desabilitado</li></ul>|
|appliedConditionalAccessPolicies: resultado|Fornece o resultado do Status da Política de Acesso Condicional individual na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Falha</li><li>Não aplicado</li><li>Desabilitado</li></ul>|

Para obter mais informações sobre o esquema, confira [Interpretar o esquema de logs de auditoria do Azure AD no Azure Monitor (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias de Proteção de Identidade no modelo de machine learning supervisionado e no mecanismo de pontuação de risco

**Tipo:** recurso alterado  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** Pontuações de risco

As melhorias no mecanismo e avaliação de risco de entrada e de usuário relacionadas à Proteção de Identidade podem ajudar a melhorar a cobertura e a precisão de risco de usuário. Os administradores talvez observem que o nível de risco de usuário deixou de estar vinculado ao nível de risco de detecções específicas e que há um aumento no número e no nível de eventos de entrada de risco.

As detecções de risco agora são avaliadas pelo modelo de aprendizado de máquina supervisionado, que calcula o risco do usuário usando recursos adicionais das entradas do usuário e um padrão de detecções. Com base nesse modelo, o administrador poderá encontrar usuários com pontuações de alto risco, mesmo se as detecções associadas a esse usuário forem de risco baixo ou médio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir suas próprias senhas usando o aplicativo Microsoft Authenticator (versão prévia pública)

**Tipo:** recurso alterado  
**Categoria de serviço:** redefinição de senha de autoatendimento  
**Funcionalidade do produto:** autenticação de usuário

Os administradores do Azure AD agora podem redefinir suas próprias senhas usando as notificações do aplicativo Microsoft Authenticator ou um código em qualquer aplicativo de autenticador móvel ou token de hardware. Para redefinir suas próprias senhas, os administradores agora poderão usar dois dos seguintes métodos:

- Notificação do aplicativo Microsoft Authenticator

- Outro aplicativo de autenticador móvel/código de token de hardware

- Email

- chamada telefônica

- mensagem de texto

Para obter mais informações sobre como usar o aplicativo Microsoft Authenticator para redefinir senhas, confira [Redefinição de senha de autoatendimento do Azure AD – aplicativo móvel e SSPR (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função Administrador de Dispositivo de Nuvem do Azure AD (versão prévia pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** Controle de acesso

Os administradores podem atribuir usuários à nova função Administrador de Dispositivo de Nuvem para executar tarefas de administrador de dispositivo de nuvem. Os usuários que recebem a função Administradores de Dispositivo de Nuvem podem habilitar, desabilitar e excluir dispositivos no Azure AD, além de poder ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure.

Para obter mais informações sobre funções e permissões, confira [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerenciar seus dispositivos usando o novo carimbo de data/hora de atividade no Azure AD (versão prévia pública)

**Tipo:** novo recurso  
**Categoria de serviço:** Gerenciamento e registro de dispositivos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida do dispositivo

Percebemos que, com o tempo, você deve atualizar e desativar os dispositivos de sua organização no Azure AD, para evitar ter dispositivos obsoletos em seu ambiente. Para ajudar com esse processo, o Azure AD agora atualiza seus dispositivos com um novo carimbo de data/hora de atividade, ajudando você a gerenciar o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e usar esse carimbo de data/hora, consulte [como gerenciar os dispositivos obsoletos no Azure ad](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os usuários aceitem os termos de uso em cada dispositivo

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança
 
Os administradores agora podem ativar a opção **exigir que os usuários consentissem em cada dispositivo** para exigir que seus usuários aceitem seus termos de uso em cada dispositivo que estão usando em seu locatário.

Para obter mais informações, consulte a [seção termos de uso por dispositivo do Azure Active Directory recurso termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar os termos de uso para expirar com base em um agendamento recorrente

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança
 

Agora, os administradores podem ativar a opção **expirar consentis** para fazer com que os termos de uso expirem para todos os seus usuários com base em sua agenda recorrente especificada. O agendamento pode ser anual, semestral, trimestral ou mensalmente. Depois que os termos de uso expirarem, os usuários deverão aceitar novamente.

Para obter mais informações, consulte a [seção adicionar termos de uso do recurso Azure Active Directory termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar os termos de uso para expirar com base na agenda de cada usuário

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança

Os administradores agora podem especificar uma duração que o usuário deve reaceitar os termos de uso. Por exemplo, os administradores podem especificar que os usuários devem reaceitar os termos de uso a cada 90 dias.

Para obter mais informações, consulte a [seção adicionar termos de uso do recurso Azure Active Directory termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos emails do Azure AD PIM (Privileged Identity Management) para funções do Azure Active Directory

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Os clientes que usam o Azure AD PIM (Privileged Identity Management) agora podem receber um email de resumo semanal, incluindo as seguintes informações para os últimos sete dias:

- Visão geral sobre as principais atribuições de função qualificadas e permanentes

- Número de usuários que ativaram funções

- Número de usuários atribuídos a funções no PIM

- Número de usuários atribuídos a funções fora do PIM

- Número de usuários que passaram a ser "permanentes" no PIM

Para obter mais informações sobre o PIM e as notificações por email disponíveis, confira [Notificações por email no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo agora está disponível

**Tipo:** recurso alterado  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** diretório

O licenciamento baseado em grupo está fora da versão prévia pública e agora está disponível. Como parte dessa versão geral, tornamos esse recurso mais escalonável e adicionamos a capacidade de reprocessar as atribuições de licenciamento baseado em grupo para um único usuário e a capacidade de usar o licenciamento baseado em grupo com licenças do Office 365 E3/A3.

Para obter mais informações sobre o licenciamento baseado em grupo, confira [O que é o licenciamento baseado em grupo no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novos Aplicativos Federados disponíveis na galeria de aplicativos do Azure AD – novembro de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em novembro de 2018, adicionamos estes 26 novos aplicativos com suporte de Federação à galeria de aplicativos:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps – Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps – UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/) e [CRAFTS – Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os Logs do Azure AD agora funcionam com o Azure Log Analytics (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Temos o prazer de anunciar que agora você pode encaminhar seus logs do Azure AD ao Azure Log Analytics! Esse recurso mais solicitados ajuda dão acesso ainda melhor a análise para seu negócio, operações e segurança, bem como uma maneira de ajudar a monitorar sua infra-estrutura. Para obter mais informações, consulte o [logs de atividades de diretório do Azure Active Directory no Azure Log Analytics agora disponível](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - outubro de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em outubro de 2018, adicionamos esses 14 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Meus Pontos de Prêmio](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Teclado de discagem, [ON24 Ambiente Virtual](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Controle do Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações por Email de serviços de domínio do AD do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** Serviços de Domínio do Azure AD  
**Capacidade do produto:** Serviços de Domínio do Azure AD

Os Serviços de Domínio do Azure AD fornecem alertas no portal do Azure sobre configurações incorretas ou problemas com o domínio gerenciado. Esses alertas incluem guias passo a passo para que você possa tentar corrigir os problemas sem precisar entrar em contato com o suporte.

A partir de outubro, você poderá personalizar as configurações de notificação do seu domínio gerenciado para que, quando novos alertas ocorrerem, um e-mail seja enviado a um grupo designado de pessoas, eliminando a necessidade de verificar constantemente o portal em busca de atualizações.

Para obter mais informações, consulte [Configurações de notificação nos Serviços de Domínio do AD do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD portal dá suporte ao uso do domínio ForceDelete API para excluir os domínios personalizados 

**Tipo:** recurso alterado  
**Categoria de serviço:** gerenciamento de diretório  
**Funcionalidade do produto:** diretório

Temos o prazer de anunciar que agora você pode usar o domínio ForceDelete API para excluir nomes de domínios personalizados renomeando referências, como usuários, grupos e aplicativos de seu nome de domínio personalizado (contoso.com) volta para o (de nome de domínio padrão inicial de forma assíncrona Contoso.onmicrosoft.com).

Essa alteração ajuda você a excluir mais rapidamente seus nomes de domínio personalizados se sua organização não usar mais o nome ou se você precisar usar o nome de domínio com outro AD do Azure.

Para obter mais informações, consulte [Excluir um nome de domínio personalizado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de função de administrador atualizadas para grupos dinâmicos

**Tipo:** corrigido  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Corrigimos um problema para que funções de administrador específicas agora possam criar e atualizar regras de associação dinâmica, sem a necessidade de ser o proprietário do grupo.

As funções são:

- Administrador global

- Administrador do Intune

- Administrador de usuários

Para obter mais informações, consulte [Criar um grupo dinâmico e verificar o status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Definições de configuração simplificada de SSO (logon único) para alguns aplicativos de terceiros

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO

Sabemos que configurar SSO (logon único) para aplicativos SaaS (software como serviço) pode ser um desafio devido à natureza específica de cada configuração de aplicativo. Criamos uma experiência de configuração simplificada para preencher automaticamente as definições de configuração de SSO para os seguintes aplicativos SaaS de terceiros:

- Zendesk

- ArcGIS Online

- Jamf Pro

Para começar a usar essa experiência de um único clique, acesse a página do **portal do Azure** > **configuração de SSO** do aplicativo. Para obter mais informações, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Página: Azure Active Directory – Onde seus dados estão localizados?

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** GoLocal

Selecione a região da sua empresa na página **Azure Active Directory – Onde seus dados estão localizados** para exibir qual datacenter do Azure armazena os dados em repouso do Azure AD de todos os seus serviços do Azure AD. Você pode filtrar as informações por serviços específicos do Azure AD da região da sua empresa.

Para acessar esse recurso e obter mais informações, consulte [Azure Active Directory – Onde seus dados estão localizados](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implantação disponível para o painel de acesso Meus Aplicativos

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

Confira o novo plano de implantação que está disponível para o painel de acesso Meus Aplicativos (https://aka.ms/deploymentplans).
O painel de acesso Meus Aplicativos fornece aos usuários um único lugar para localizar e acessar os aplicativos. Esse portal também fornece aos usuários oportunidades de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciamento de acesso a esses recursos em nome de outros.

Para obter mais informações, consulte [O que é o portal Meus Aplicativos?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nova guia de Solução de problemas e Suporte na página de entradas de Logs do portal do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

A nova guia de **solução de problemas e suporte** na página de **entradas** do portal do Azure, destina-se a ajudar administradores e engenheiros de suporte a solucionar problemas relacionados a entradas do Azure AD. Essa nova guia fornece o código de erro, a mensagem de erro e as recomendações de correção (se houver) para ajudar a resolver o problema. Se você não conseguir resolver o problema, também oferecemos uma nova maneira de criar um tíquete de suporte usando a experiência de **Copiar para a área de transferência**, que preenche os campos **ID da Solicitação** e **Data (UTC)** do arquivo de log em seu tíquete de suporte.  

![Logs de entrada mostrando a nova guia](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte aprimorado para propriedades de extensão personalizadas usadas para criar regras de associação dinâmica

**Tipo:** recurso alterado  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Com essa atualização, agora você pode clicar no link **Obter propriedades de extensão personalizadas** no construtor de regra dinâmico de grupo dos usuários, inserir sua ID do aplicativo exclusiva e receber a lista completa das propriedades de extensão personalizadas para usar ao criar uma regra de associação dinâmica para usuários. Essa lista também pode ser atualizada para obter as novas propriedades de extensão personalizadas do aplicativo.

Para obter mais informações sobre como usar propriedades de extensão personalizadas para regras de associação dinâmica, consulte [Propriedades de extensão e propriedades de extensão personalizadas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para acesso condicional baseado em aplicativo do Azure AD

**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, consulte:

- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para redefinição de senha de autoatendimento da tela de bloqueio do Windows 7/8/8.1

**Tipo:** novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** autenticação de usuário

Depois de configurar esse novo recurso, os usuários verão um link para redefinir a senha da tela de **bloqueio** de um dispositivo que executa o Windows 7, Windows 8 ou Windows 8.1. Ao clicar nesse link, o usuário é guiado pelo mesmo fluxo de redefinição de senha que no navegador da Web.

Para obter mais informações, consulte [Como habilitar a redefinição de senha do Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: os códigos de autorização não estarão disponíveis para reutilização 

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – setembro de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em setembro de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Software de Recrutamento Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Suporte para métodos de transformações de declarações adicionais

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO

Apresentamos os novos métodos de transformação de declaração, ToLower () e ToUpper (), que podem ser aplicados aos tokens SAML por meio da página baseada em SAML **Configuração de logon único**.

Para obter mais informações, consulte [Como personalizar declarações emitidas no token SAML para aplicativos empresariais no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interface do usuário atualizada da configuração de aplicativo baseado em SAML (versão prévia)

**Tipo:** recurso alterado  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO

Como parte da nossa interface do usuário atualizada de configuração de aplicativo baseado em SAML, você obterá:

- Uma experiência atualizada de explicação passo a passo para configurar seus aplicativos baseados em SAML.

- Maior visibilidade sobre o que está ausente ou incorreto em sua configuração.

- A capacidade de adicionar vários endereços de email para notificação de expiração de certificado.

- Novos métodos de transformação de declaração, ToLower () e ToUpper () e muito mais.

- Uma forma de carregar seu próprio token de assinatura de certificado para aplicativos empresariais.

- Uma maneira de definir o formato NameID para aplicativos SAML e uma maneira de definir o valor NameID como Extensões de diretório.

Para ativar este modo de exibição atualizado, clique no link **Experimentar nossa nova experiência** na parte superior da página **Logon único**. Para obter mais informações, consulte [Tutorial: Configurar logon único baseado em SAML para um aplicativo com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Agosto de 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Alterações em intervalos de endereços IP do Azure Active Directory

**Tipo:** plano de alteração  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** plataforma

Estamos introduzindo intervalos de IP maiores no Azure AD, o que significa que, se você tiver configurado intervalos de endereços IP do Azure AD para firewalls, roteadores ou grupos de segurança de rede, será necessário atualizá-los. Estamos fazendo essa atualização para que você não tenha que alterar as configurações do intervalo IP, do roteador ou do grupo de segurança de rede novamente quando o Azure AD adicionar novos pontos de extremidade. 

O tráfego de rede está se movendo para esses novos intervalos nos próximos dois meses. Para continuar com o serviço ininterrupto, você deve adicionar esses valores atualizados aos seus endereços IP antes de 10 de setembro de 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

É altamente recomendável não remover os intervalos de endereços IP antigos até que todo o tráfego da rede seja movido para os novos intervalos. Para obter atualizações sobre a movimentação e saber quando você pode remover os intervalos antigos, consulte [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: os códigos de autorização não estarão disponíveis para reutilização 

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gerenciamento de informações de segurança convergida para SSPR (senha de autoatendimento) e MFA (Autenticação Multifator do Microsoft Azure)

**Tipo:** novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** autenticação de usuário
 
Esse novo recurso ajuda as pessoas a gerenciar suas informações de segurança (por exemplo, número de telefone, aplicativo móvel e assim por diante) para SSPR e MFA em um único local e a experiência; anteriormente, isso era feito em dois locais diferentes.

Essa experiência convergida também funciona para pessoas que usam SSPR ou MFA. Além disso, se sua organização não impõe registro de MFA ou SSPR, as pessoas ainda podem registrar métodos de informações de segurança de MFA ou SSPR permitidos pela sua organização no portal Meus aplicativos.

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado ou para todos os usuários em um locatário. Para obter mais informações sobre a experiência convergida, consulte o [Blog da experiência convergida](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nova configuração de cookies somente HTTP em aplicativos do proxy de aplicativo do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Há uma nova configuração chamada **Cookies somente HTTP** em seus aplicativos de proxy de aplicativo. Essa configuração ajuda a fornecer segurança adicional, pois inclui o sinalizador HTTPOnly no cabeçalho de resposta HTTP para os cookies de sessão e acesso de proxy de aplicativo, impede o acesso ao cookie de um script do lado do cliente e também impede ações como copiar ou modificar o cookie. Embora esse sinalizador não tenha sido usado anteriormente, os cookies sempre foram criptografados e transmitidos usando uma conexão SSL para ajudar a proteger contra modificações inadequadas.

Essa configuração não é compatível com aplicativos que usam os controles ActiveX, como Área de Trabalho Remota. Caso esteja nessa situação, é recomendável desativar essa configuração.

Para obter mais informações sobre a configuração Cookies somente HTTP, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>O PIM (Privileged Identity Management) para recursos do Azure dá suporte aos tipos de recurso do grupo de gerenciamento

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Agora, as configurações de ativação e atribuição Just-In-Time podem ser aplicadas aos tipos de recursos de Grupo de Gerenciamento, como você já faz para Assinaturas, Grupos de Recursos e Recursos (tais como VMs, Serviços de Aplicativos e muito mais). Além disso, qualquer pessoa com uma função que conceda acesso de administrador para um Grupo de Gerenciamento pode descobrir e gerenciar tal recurso no PIM.

Para obter mais informações sobre o PIM e os recursos do Azure, consulte [Descobrir e gerenciar recursos do Azure usando o Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>O acesso do aplicativo (versão prévia) fornece um acesso mais rápido ao portal do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Hoje, ao ativar uma função usando o PIM, as permissões podem levar mais de 10 minutos para entrar em vigor. Se você optar por usar o acesso do aplicativo, que está em visualização pública no momento, os administradores poderão acessar o portal do Azure AD assim que a solicitação de ativação for concluída.

Atualmente, o acesso do aplicativo dá suporte apenas à experiência do portal do Azure AD e aos recursos do Azure. Para obter mais informações sobre o PIM e o acesso do Aplicativo, confira [O que é o Privileged Identity Management do Azure AD?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – agosto de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em agosto de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (por Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>O suporte nativo ao Tableau já está disponível no Proxy de Aplicativo do Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Com nossa atualização do OpenID Connect para o protocolo OAuth 2.0 Code Grant para nosso protocolo de pré-autenticação, você não precisa mais fazer nenhuma configuração adicional para usar o Tableau com Proxy de Aplicativo. Essa alteração de protocolo também ajuda o Proxy de Aplicativo a oferecer um suporte melhor a aplicativos mais modernos usando apenas redirecionamentos de HTTP, que normalmente recebem suporte em marcações HTML e JavaScript.

Para obter mais informações sobre o suporte nativo para Tableau, consulte [Proxy de Aplicativo do Azure AD agora com suporte nativo para Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Novo suporte para adicionar o Google como provedor de identidade para usuários convidados B2B no Azure Active Directory (versão prévia)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Ao configurar a federação com o Google em sua organização, você pode permitir que os usuários convidados do Gmail entrem em seus aplicativos e recursos compartilhados usando as respectivas contas do Google existentes, sem precisar criar uma MSA (Conta Microsoft) pessoal ou uma conta do Azure AD.

Esta é uma visualização pública opt-in. Para obter mais informações sobre a federação do Google, consulte [Adicionar o Google como provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Julho de 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Melhorias nas notificações por email do Azure Active Directory

**Tipo:** recurso alterado  
**Categoria de serviço:** outro  
**Funcionalidade do produto**: gerenciamento do ciclo de vida da identidade
 
Os emails do Active Directory do Azure (Azure AD) agora apresentam um design atualizado, bem como alterações no endereço de email do remetente e no nome de exibição do remetente, quando enviados dos seguintes serviços:
 
- Revisões de acesso do Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Gerenciamento de identidades com privilégios do AD do Azure
- Notificação de certificado expirando do Aplicativo Enterprise
- Notificações do serviço de provisionamento do Aplicativo Enterprise
 
As notificações por email serão enviadas a partir do seguinte endereço de email e nome de exibição:

- Endereço de email: azure-noreply@microsoft.com
- Nome de exibição: Microsoft Azure
 
Para obter um exemplo de alguns dos novos designs de email e mais informações, consulte [Notificações por email no PIM do Azure AD](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Os Logs de atividade do Azure AD agora estão disponíveis por meio do Azure Monitor

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Os Logs de atividade do Azure AD agora estão disponíveis na visualização pública do Monitor do Azure (serviço de monitoramento da plataforma do Azure). O Monitor do Azure oferece retenção a longo prazo e integração perfeita, além dessas melhorias:

- Retenção de longo prazo roteando seus arquivos de log para sua própria conta de armazenamento do Azure.

- Integração perfeita do SIEM, sem exigir que você escreva ou mantenha scripts personalizados.

- Integração perfeita com suas próprias soluções personalizadas, ferramentas de análise ou soluções de gerenciamento de incidentes.

Para obter mais informações sobre esses novos recursos, consulte o [log de atividades do Azure AD no diagnóstico do Monitor do Azure agora na visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e nossa documentação, [logs de atividades do Azure Active Directory no Azure Monitor (visualização)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informações de acesso condicional adicionadas ao relatório de entradas do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Esta atualização permite ver quais políticas são avaliadas quando um usuário faz login junto com o resultado da política. Além disso, o relatório agora inclui o tipo de aplicativo cliente usado pelo usuário, para que você possa identificar o tráfego de protocolo herdado. Agora, as entradas de relatório também podem ser pesquisadas em busca de um ID de correlação, que pode ser encontrado na mensagem de erro do usuário, e pode ser usado para identificar e solucionar problemas da solicitação de entrada correspondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Veja autenticações herdadas por meio dos logs de atividades de Entradas

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
Com a introdução do campo **Client App** nos logs de atividades de login, os clientes agora podem ver usuários que estão usando autenticações herdadas. Os clientes poderão acessar essas informações usando as entradas Microsoft Graph API ou por meio dos logs de atividade de entrada no portal do AD do Azure, em que você pode usar o controle de **aplicativo cliente** para filtrar as autenticações herdadas. Confira a documentação para obter mais detalhes.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - julho de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em julho de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certos SSO de administrador](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Suporte Remoto Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [ImagineVisualizar Web](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Base de Habilidades](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Novas integrações de aplicativos SaaS para provisionamento de usuários - julho de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como Dropbox, Salesforce, ServiceNow e muito mais. Para julho de 2018, adicionamos o suporte ao aprovisionamento de usuários aos seguintes aplicativos na galeria de aplicativos do Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na Galeria do Azure AD, consulte [integração de aplicativos SaaS ao Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Conecte o Health for Sync - Uma maneira mais fácil de corrigir erros de sincronização de atributos órfãos e duplicados

**Tipo:** novo recurso  
**Categoria de serviço:** AD Connect  
**Funcionalidade do produto:** monitoramento e relatórios
 
O Azure AD Connect Health apresenta a correção de autoatendimento para ajudá-lo a destacar e corrigir erros de sincronização. Esse recurso soluciona erros de sincronização de atributos duplicados e corrige objetos que são órfãos do AD do Azure. Este diagnóstico tem os seguintes benefícios:

- Diminui os erros de sincronização de atributos duplicados, fornecendo correções específicas

- Aplica uma correção para cenários dedicados do AD do Azure, resolvendo erros em uma única etapa

- Nenhuma atualização ou configuração é necessária para ativar e usar esse recurso

Para obter mais informações, consulte [Diagnosticar e corrigir erros de sincronização de atributos duplicados](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Atualizações visuais nas experiências de entrada do Azure AD e MSA

**Tipo:** recurso alterado  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** autenticação de usuário

Atualizamos a interface do usuário para a experiência de login de serviços on-line da Microsoft, como no Office 365 e no Azure. Essa alteração torna as telas menos confusas e mais simples. Para obter mais informações sobre essa alteração, consulte as [próximas melhorias no blog de experiência de logon do Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nova versão do Azure AD Connect - julho de 2018

**Tipo:** recurso alterado  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade

A versão mais recente do Azure AD Connect inclui: 

- Correções de bugs e atualizações de capacidade de suporte 

- Disponibilidade geral da integração federada de Ping

- Atualizações para o cliente mais recente do SQL 2012 

Para obter mais informações sobre essa atualização, consulte [Azure AD Connect: histórico de versões da versão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Atualizações dos termos de uso da interface do usuário final

**Tipo:** recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança

Estamos atualizando a sequência de aceitação na interface do usuário final do TOU.

**Texto atual.** Para acessar os recursos do [tenantName], você deve aceitar os termos de uso.<br>**Novo texto.** Para acessar o recurso [tenantName], você deve ler os termos de uso.

**Texto atual:** optando por aceitar significa que você concorda com todos os termos de uso acima.<br>**Novo texto:** Clique em Aceitar para confirmar que leu e compreendeu os termos de uso.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>A autenticação de passagem dá suporte a aplicativos e protocolos herdados

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
A autenticação de passagem agora suporta protocolos e aplicativos legados. As limitações a seguir agora são totalmente suportadas:

- Logins de usuário para aplicativos cliente legados do Office, Office 2010 e Office 2013, sem exigir autenticação moderna.

- Acesso ao compartilhamento de calendário e informações livres/ocupadas nos ambientes híbridos do Exchange somente no Office 2010.

- Entradas de usuário para aplicativos cliente do Skype for Business sem a necessidade de autenticação moderna.

- Entradas de usuário no PowerShell versão 1.0.

- O Programa de Inscrição de Dispositivos da Apple (Apple DEP), usando o Assistente de Configuração do iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gerenciamento de informações de segurança convergida para redefinição de senha de autoatendimento e a autenticação multifator

**Tipo:** novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** autenticação de usuário

Esse novo recurso permite que os usuários gerenciem suas informações de segurança (por exemplo, número de telefone, endereço de email, aplicativo móvel etc.) para redefinição de senha de autoatendimento (SSPR) e autenticação de vários fatores (MFA) em uma única experiência. Os usuários não precisarão mais registrar as mesmas informações de segurança para SSPR e MFA em duas experiências diferentes. Essa nova experiência também se aplica a usuários que possuem SSPR ou MFA.

Se uma organização não estiver aplicando o registro de MFA ou SSPR, os usuários poderão registrar suas informações de segurança no portal **Meus aplicativos**. A partir daí, os usuários podem registrar qualquer método habilitado para MFA ou SSPR. 

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado de usuários ou todos os usuários em um locatário.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Use o aplicativo Microsoft Authenticator para verificar sua identidade quando você redefinir sua senha

**Tipo:** recurso alterado  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** autenticação de usuário

Esse recurso permite que não administradores verifiquem sua identidade ao redefinir uma senha usando uma notificação ou código do Microsoft Authenticator (ou qualquer outro aplicativo autenticador). Depois que os administradores ativarem esse método de redefinição de senha de autoatendimento, os usuários que tiverem registrado um aplicativo móvel por meio de aka.ms/mfasetup ou aka.ms/setupsecurityinfo poderão usar o aplicativo móvel como um método de verificação ao redefinir a senha.

A notificação de aplicativos para dispositivos móveis só pode ser ativada como parte de uma política que exige dois métodos para redefinir sua senha.

---

## <a name="june-2018"></a>Junho de 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Aviso de Alteração: correção de segurança para fluxo de autorização delegada para aplicativos usando a API de Logs de Atividades do Azure AD

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Devido à nossa maior imposição de segurança, tivemos que fazer uma alteração nas permissões para aplicativos que usam um fluxo de autorização delegado para acessar as [APIs de logs de atividades do Azure ad](https://aka.ms/aadreportsapi). Essa alteração ocorrerá até **26 de junho de 2018**.

Se qualquer um de seus aplicativos usarem as APIs de log de atividades do Azure AD, siga estas etapas para garantir que o aplicativo não seja interrompido após a alteração ocorrer.

**Para atualizar as permissões do aplicativo**

1. Entre no portal do Azure, selecione **Active Directory do Azure**, e em seguida, selecione **Registros de Aplicativos**.
2. Selecione seu aplicativo que usa a API de Logs de Atividade do Azure AD, selecione **Configurações**, selecione **Permissões necessárias**, e em seguida, selecione a **API do Windows Azure Active Directory**.
3. Na área **Permissões delegadas** da folha **Habilitar acesso**, selecione a caixa ao lado de **Ler dados de diretório** e, em seguida, selecione **Salvar**.
4. Selecione **Conceder permissões** e, em seguida, selecione **Sim**.
    
    >[!Note]
    >Você deve ser um administrador Global para conceder permissões ao aplicativo.

Para obter mais informações, consulte a área [ Conceder permissões ](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) dos Pré-requisitos para acessar o artigo da API de relatórios do Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Definir configurações de TLS para se conectar aos serviços do Azure AD para conformidade com o PCI DSS

**Tipo:** novo recurso  
**Categoria de serviço:** N/A  
**Funcionalidade do produto:** plataforma

O TLS (Transport Layer Security) é um protocolo que fornece privacidade e integridade de dados entre dois aplicativos de comunicação e é o protocolo de segurança mais utilizado atualmente.

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) determinou que as primeiras versões de TLS e Secure Sockets Layer (SSL) devem ser desativadas em favor de permitir protocolos de aplicativos novos e mais seguros, com conformidade a partir de **30 de junho de 2018**. Essa alteração significa que, se você se conectar aos serviços do Azure AD e exigir conformidade com o PCI DSS, deverá desabilitar o TLS 1.0. Várias versões do TLS estão disponíveis, mas o TLS 1.2 é a versão mais recente disponível para os Serviços do Active Directory do Azure. É altamente recomendável mover diretamente para o TLS 1.2 para as combinações cliente / servidor e navegador / servidor.

Os navegadores desatualizados podem não suportar versões mais recentes do TLS, como o TLS 1.2. Para ver quais versões do TLS são suportadas pelo seu navegador, acesse o site [Qualys SSL Labs](https://www.ssllabs.com/) e clique em **Test your browser**. Recomendamos que você atualize para a versão mais recente do seu navegador da Web e, de preferência, ative somente o TLS 1.2.

**Para ativar o TLS 1.2, pelo navegador**

- **Microsoft Edge e Internet Explorer (ambos são definidos usando o Internet Explorer)**

    1. Abra o Internet Explorer, selecione **Ferramentas** > **Opções da Internet**  >  **Avançadas**.
    2. Na área **Segurança**, selecione **use TLS 1.2** e, em seguida, selecione **OK**.
    3. Feche todas as janelas do navegador e reinicie o Internet Explorer. 

- **Google Chrome**

    1. Abra o Google Chrome, digite *chrome: // settings /* na barra de endereço e pressione **Enter**.
    2. Expanda as opções **Avançadas**, vá para a área **System** e selecione **Open proxy settings**.
    3. Na caixa **Propriedades da Internet**, selecione a guia **Avançado**, vá para a área **Segurança**, selecione **use TLS 1.2** e selecione **OK**.
    4. Feche todas as janelas do navegador e reinicie o Google Chrome.

- **Mozilla Firefox**

    1. Abra o Firefox, digite *about: config* na barra de endereços e, em seguida, pressione **Enter**.
    2. Pesquise pelo termo *TLS* e selecione a entrada **security.tls.version.max**.
    3. Defina o valor para **3** para forçar o navegador a usar a versão TLS 1.2 e, em seguida, selecione **OK**.

        >[!NOTE]
        >A versão 60.0 do Firefox suporta o TLS 1.3, portanto você também pode definir o valor de security.tls.version.max como **4**.

    4. Feche todas as janelas do navegador e reinicie o Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - junho de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em junho de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Liquidação de música](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Aplicativo de LOB habilitado para tokens](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint local](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>A proteção de senha do Azure AD está disponível na visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** autenticação de usuário

Use a proteção de senha do Azure AD para ajudar a eliminar senhas fáceis de adivinhar do seu ambiente. A eliminação dessas senhas ajuda a diminuir o risco de comprometimento de um tipo de ataque de spray de senha.

Especificamente, o Azure AD Password Protection ajuda você a:

- Proteja as contas da sua organização no AD do Azure e no Active Directory (AD) do Windows Server. 
- Impede que os usuários usem senhas em uma lista com mais de 500 das senhas mais usadas e mais de 1 milhão de variações de substituição de caracteres dessas senhas.
- Administre a Proteção de Senha do Azure AD a partir de um único local no portal do Azure AD, tanto para o Azure AD quanto para o Windows Server AD local.

Para obter mais informações sobre o Azure AD Password Protection, consulte [Eliminar senhas incorretas em sua organização](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "todos os convidados" criado durante a criação de termos de uso

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança

Durante a criação dos seus termos de uso, um novo modelo de política de acesso condicional também é criado para "todos os convidados" e "todos os aplicativos". Esse novo modelo de política aplica os ToU recém-criados, simplificando o processo de criação e execução para os convidados.

Para obter mais informações, consulte [Recurso de Termos de Uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Novo modelo de política de acesso condicional "personalizado" criado durante a criação de termos de uso

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança

Durante a criação dos seus termos de uso, um novo modelo de política de acesso condicional "personalizado" também é criado. Esse novo modelo de política permite criar o ToU e, em seguida, ir imediatamente para a folha de criação de política de acesso condicional, sem a necessidade de navegar manualmente pelo portal.

Para obter mais informações, consulte [Recurso de Termos de Uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Orientação nova e abrangente sobre a implantação da autenticação de vários fatores do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Lançamos uma nova orientação passo a passo sobre como implantar a Autenticação Multifator do Azure (MFA) em sua organização.

Para visualizar o guia de implantação do MFA, vá para o repositório [Guias de Implantação de Identidade](https://aka.ms/DeploymentPlans) no GitHub. Para fornecer feedback sobre os guias de implantação, use o [formulário de Feedback do Plano de Implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>As funções de gerenciamento de aplicativos delegados do Azure AD estão em pré-visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** Controle de Acesso

Os administradores agora podem delegar tarefas de gerenciamento de aplicativos sem atribuir a função de administrador global. As novas funções e capacidades são:

- **Novas funções administrativas padrão do AD do Azure:**

    - **Administrador do Aplicativo.** Concede a capacidade de gerenciar todos os aspectos de todos os aplicativos, incluindo registro, configurações de SSO, atribuições de aplicativos e licenciamento, configurações de proxy de aplicativo e consentimento (exceto para recursos do Azure AD).

    - **Administrador do aplicativo na nuvem.** Concede todas as habilidades de Administrador do Aplicativo, exceto para o proxy de Aplicativo, porque ele não fornece acesso local.

    - **Desenvolvedor de aplicativos.** Concede a capacidade de criar registros de aplicativos, mesmo se a opção **permitir que os usuários registrem aplicativos** seja desativada.

- **Propriedade (configure o registro por aplicativo e o aplicativo por empresa, semelhante ao processo de propriedade do grupo:**
 
    - **Proprietário do registro do aplicativo.** Concede a capacidade de gerenciar todos os aspectos do registro de aplicativos de propriedade, incluindo o manifesto do aplicativo e a adição de outros proprietários.

    - **Proprietário do aplicativo corporativo.** Concede a capacidade de gerenciar muitos aspectos de aplicativos corporativos próprios, incluindo configurações de SSO, designações de aplicativos e consentimento (exceto para recursos do Azure AD).

Para obter mais informações sobre a visualização pública, consulte as [funções de gerenciamento de aplicativos delegados do Azure AD em pré-visualização pública!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Para obter mais informações sobre funções e permissões, consulte [Atribuindo funções de administrador no Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maio de 2018

### <a name="expressroute-support-changes"></a>Alterações de suporte do ExpressRoute

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** plataforma  

A oferta de Software como Serviço, como Azure AD (Azure Active Directory), foi projetada para funcionar melhor passando diretamente pela Internet, sem a necessidade do ExpressRoute ou de qualquer outro túnel VPN privado. Por isso, em **1º de agosto de 2018**, deixaremos de dar suporte ao ExpressRoute para serviços do Azure AD usando emparelhamento público do Azure e comunidades do Azure no emparelhamento da Microsoft. Quaisquer serviços afetados por essa alteração podem observar o tráfego do Azure AD alternando gradualmente do ExpressRoute para a Internet.

Enquanto estamos alterando nosso suporte, também sabemos que ainda há situações em que você poderá precisar usar um conjunto dedicado de circuitos para o tráfego de autenticação. Por esse motivo, o Azure AD continuará dando suporte a restrições de intervalo de IP por locatário usando o ExpressRoute e serviços já em emparelhamento da Microsoft com a comunidade "Outros serviços online do Office 365". Se seus serviços forem afetados, mas você precisar do ExpressRoute, deverá fazer o seguinte:

- **Se estiver no emparelhamento público do Azure.** Vá para a Microsoft peering e inscreva-se na comunidade **Outros serviços do Office 365 Online (12076: 5100)** . Para obter mais informações sobre como passar do emparelhamento público do Azure para emparelhamento da Microsoft, consulte o artigo [Mover um emparelhamento público para o emparelhamento da Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Se estiver no emparelhamento da Microsoft.** Inscreva-se na comunidade **Other Office 365 Online service (12076: 5100)** . Para obter mais informações sobre requisitos de roteamento, consulte a seção [Suporte comunidades BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) do artigo de requisitos de roteamento ExpressRoute.

Se você precisar continuar usando circuitos dedicados, fale com a equipe da sua conta da Microsoft sobre como obter autorização para usar a comunidade **Outros serviços online do Office 365 (12076:5100)** . O quadro de revisão gerenciado do MS Office verificará se você precisará desses circuitos e se reconhece as implicações técnicas de mantê-los. Assinaturas não autorizadas tentando criar filtros de rota para o Office 365 receberão uma mensagem de erro. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>APIs do Microsoft Graph para cenários administrativos para TOU

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** experiência de desenvolvedor
 
Adicionamos Microsoft Graph APIs para a operação de administração de termos de uso do Azure AD. Você pode criar, atualizar, excluir o objeto de termos de uso.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Adicionar ponto de extremidade multilocatário do Azure AD como um provedor de identidade no Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Agora, usando políticas personalizadas, você pode adicionar o ponto de extremidade comum do Azure AD como um provedor de identidade no Azure AD B2C. Isso permite que você tenha um único ponto de entrada para todos os usuários do Azure AD que estão entrando em seus aplicativos. Para saber mais, confira [Azure Active Directory B2C: Permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Use URLs internas para acessar os aplicativos de qualquer lugar com nossa Extensão de Entrada Meus Aplicativos e o Proxy de Aplicativo do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO
 
Os usuários agora podem acessar aplicativos por meio de URLs internas, mesmo de fora da rede corporativa, usando a Extensão Logon Protegido dos Meus aplicativos para Azure AD. Isso funcionará com qualquer aplicativo que você tenha publicado usando o Proxy de Aplicativo do Azure AD, em qualquer navegador que também tenha a extensão de navegador do Painel de Acesso instalada. A funcionalidade de redirecionamento de URL é habilitada automaticamente quando um usuário faz logon na extensão. A extensão está disponível para fazer o download no [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Dados na Europa para clientes europeus

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** GoLocal

Os clientes na Europa exigem que seus dados permaneçam na Europa e não sejam replicados fora de datacenters europeus para o cumprimento da legislação europeia sobre privacidade. Este [artigo](https://go.microsoft.com/fwlink/?linkid=872328) fornece detalhes específicos sobre quais informações de identidade serão armazenadas na Europa e também sobre as informações que serão armazenadas fora dos datacenters europeus. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Novo usuário provisionando integrações de aplicativo SaaS – maio de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como Dropbox, Salesforce, ServiceNow e muito mais. Em maio de 2018, adicionamos suporte ao provisionamento de usuários para os seguintes aplicativos na Galeria de aplicativo do Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na Galeria do Azure AD, confira [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>O Azure AD acessa revisões de grupos e o acesso do aplicativo agora fornece revisões recorrentes

**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** governança
 
A revisão de acesso de grupos e aplicativos agora está geralmente disponível como parte do Azure AD Premium P2.  Os administradores poderão configurar revisões de acesso de membros de grupos e atribuições de aplicativo para que ocorram automaticamente em intervalos regulares, como mensal ou trimestralmente.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Os logs de atividades do Azure AD (entradas e auditoria) agora estão disponíveis por meio do MS Graph

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
Os logs de atividades do Azure AD, que incluem entradas e logs de auditoria, agora estão disponíveis por meio da API Microsoft Graph. Expuseram dois pontos de extremidade por meio da API de Microsoft Graph para acessar esses logs. Confira nossos [documentos](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) para acesso programático às APIs de relatórios do Azure AD para começar. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Aprimoramentos à experiência de resgate B2B e deixar uma organização

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

**Resgate just in time:** Depois de compartilhar um recurso com um usuário convidado usando a API B2B – você não precisa enviar um email de convite especial. Na maioria dos casos, o usuário convidado pode acessar o recurso e será levado até a experiência de resgate a tempo. Chega de consequências devido a emails não recebidos. Não é mais perguntado aos seus usuários convidados "você clicou nesse link de resgate que o sistema enviou?". Isso significa que quando o SPO usa o gerenciador de convites, os anexos de nuvem podem ter a mesma URL canônica para todos os usuários, internos e externos, em qualquer estado do resgate.

**Experiência de resgate moderna:** chega de página de aterrissagem de resgate em tela dividida. Os usuários verão uma experiência moderna de consentimento com a declaração de privacidade da organização convidativa, assim como fazem para aplicativos de terceiros.

**Os usuários convidados podem sair da organização:** Depois que a relação de um usuário com uma organização terminar, ela poderá se manter por conta própria, deixando a organização. Não é mais necessário chamar o administrador da organização que está convidando para "ser removido", não mais gerando tíquetes de suporte.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - maio de 2018

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em maio de 2018, adicionamos esses 18 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Novos guias de implantação passo a passo para o Active Directory do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** diretório
 
Novas orientações passo a passo sobre como implantar o Azure Active Directory (Azure AD), incluindo SSPR (autoatendimento de redefinição de senha), logon único (SSO), acesso condicional (CA), proxy de aplicativo, provisionamento de usuário, Serviços de Federação do Active Directory (AD FS) (ADFS) para Autenticação de passagem (PTA) e ADFS para sincronização de hash de senha (PHS).

Para visualizar os guias de implantação, acesse o repositório [Guias de Implantação de Identidade](https://aka.ms/DeploymentPlans) no GitHub. Para fornecer feedback sobre os guias de implantação, use o [formulário de Feedback do Plano de Implantação](https://aka.ms/deploymentplanfeedback). Se você tiver alguma dúvida sobre os guias de implantação, entre em contato conosco em [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Pesquisa de Aplicativos Empresariais – Carregar Mais Aplicativos

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
Problemas para localizar seus aplicativos/entidades de serviço? Adicionamos a capacidade de carregar mais aplicativos em sua lista Aplicativos corporativos – todos os aplicativos. Por padrão, mostramos 20 aplicativos. Agora você pode clicar em **Carregar mais** para visualizar aplicativos adicionais. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>O lançamento de maio do AADConnect contém uma prévia pública da integração com PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas e ótimas novas ferramentas de solução de problemas. 

**Tipo:** recurso alterado  
**Categoria de serviço:** AD Connect  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade
 
O lançamento de maio do AADConnect contém uma prévia pública da integração com PingFederate, atualizações de segurança importantes, muitas correções de bugs e novas e ótimas novas ferramentas de solução de problemas. Você pode encontrar as notas de versão [aqui](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Revisões de acesso do Azure AD: aplicação automática

**Tipo:** recurso alterado  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** governança

As revisões de acesso de grupos e aplicativos agora estão disponíveis como parte do Azure AD Premium P2. Um administrador pode configurar para aplicar as alterações do revisor automaticamente a esse grupo ou aplicativo quando a revisão de acesso for concluída. O administrador também pode especificar o que acontece com o acesso continuado do usuário se os revisores não responderem, removerem o acesso, manterem o acesso ou receberem recomendações do sistema. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Os tokens de ID não podem ser mais retornados usando a consulta response_mode para novos aplicativos. 

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
Os aplicativos criados em ou após 25 de abril de 2018 não poderão mais solicitar um **id_token** usando a **consulta** response_mode.  Isso coloca o Azure AD de acordo com as especificações de OIDC e ajuda a reduzir a superfície de ataque dos aplicativos.  Aplicativos criados antes de 25 de abril de 2018 não são impedidos de usar a **consulta** response_mode com um response_type de **id_token**.  O erro retornado ao solicitar uma id_token do AAD, é **AADSTS70007: ' Query ' não é um valor com suporte de ' response_mode ' ao solicitar um token**.

O **fragmento** e **form_post** response_modes continuam funcionando - ao criar novos objetos de aplicativo (por exemplo, para uso do App Proxy), garanta o uso de um desses response_modes antes que eles criem um novo aplicativo.  

---
 
## <a name="april-2018"></a>Abril de 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Os Tokens de Acesso do Azure AD B2C estão disponíveis ao público

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C 

Agora você pode acessar APIs da Web protegidas pelo Azure AD B2C usando tokens de acesso. O recurso está mudando da versão prévia pública para GA. A experiência da interface do usuário para configurar aplicativos do Azure AD B2C e APIs da Web foi aprimorada e outras pequenas melhorias foram feitas.
 
Para obter mais informações, consulte [Azure AD B2C: solicitando tokens de acesso](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testar configuração de logon único para aplicativos baseados em SAML

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO

Ao configurar aplicativos SSO baseados em SAML, você pode testar a integração na página de configuração. Se você encontrar um erro durante o login, poderá fornecer o erro na experiência de teste e o Azure AD fornecerá etapas de resolução para resolver o problema específico.

Para obter mais informações, consulte:

- [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Como depurar o logon único baseado em SAML em aplicativos no Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Os termos de uso do Azure AD agora têm relatórios por usuário

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** conformidade
 
Agora os administradores podem selecionar um Termo de Uso determinado e ver todos os usuários que consentiram com ele e em que data/hora isso aconteceu.

Para obter mais informações, consulte o [Azure AD terms of use feature](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use) (Recurso de termos de uso do Azure AD).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: IP arriscado para proteção de bloqueio de extranet do AD FS 

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** monitoramento e relatórios

Agora o Connect Health é compatível com a capacidade de detectar endereços IP que excedem um limite de logons U/P com falha por hora ou diariamente. As funcionalidades fornecidas por esse recurso são:

- Relatório completo que mostra o endereço IP e o número de logons com falha gerados por hora/diariamente com limite personalizável.
- Alertas de email que mostram quando um endereço IP específico excedeu o limite de logons U/P com falha por hora/diariamente.
- Uma opção de download para fazer uma análise detalhada dos dados

Para obter mais informações, consulte [Risky IP Report](https://aka.ms/aadchriskyip) (Relatório IP arriscado).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Configuração fácil do aplicativo com arquivos ou URL de metadados

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO

Na página Aplicativos empresariais, os administradores podem carregar um arquivo de metadados SAML para configurar o logon baseado em SAML para um aplicativo da Galeria e não da galeria do AAD.

Além disso, é possível usar a URL de metadados de federação do aplicativo Azure AD para configurar o SSO com o aplicativo direcionado.

Para obter mais informações, consulte [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Agora os Termos de uso do Azure AD já estão disponíveis

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** conformidade
 

Os termos de uso do Azure AD foram movidos da visualização pública para disponibilidade geral.

Para obter mais informações, consulte o [Azure AD terms of use feature](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use) (Recurso de termos de uso do Azure AD).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Permitir ou bloquear convites para usuários B2B de organizações específicas

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 

Agora é possível especificar com quais organizações parceiras você deseja compartilhar e colaborar na Colaboração B2B do Azure AD. Para fazer isso, é possível optar por criar a lista de permissão ou negação de domínios específica. Quando um domínio é bloqueado usando essas funcionalidades, os funcionários não podem mais enviar convites para pessoas nesse domínio.

Isso ajuda a controlar o acesso aos recursos e permite uma experiência perfeita para usuários aprovados.

Esse recurso de colaboração B2B está disponível para todos os clientes Azure Active Directory e pode ser usado em conjunto com Azure AD Premium recursos como o acesso condicional e a proteção de identidade para um controle mais granular de quando e como os usuários corporativos externos assinam em e obter acesso.

Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em abril de 2018, adicionamos esses 13 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

Criterion HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (local)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial), [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Shelf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Permitir acesso a seus aplicativos locais a usuários B2B no Azure AD (versão prévia pública)

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Como uma organização que usa recursos de colaboração B2B do Microsoft Azure AD (Azure Active Directory) para convidar usuários convidados de organizações parceiras para o Microsoft Azure AD, agora é possível fornecer a esses usuários B2B acesso a aplicativos locais. Esses aplicativos locais podem usar a autenticação baseada em SAML ou a IWA (Autenticação Integrada do Windows) com KCD (delegação restrita de Kerberos).

Para obter mais informações, consulte [Conceda aos usuários B2B no acesso do Azure AD aos seus aplicativos locais](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Obter tutoriais de integração de SSO do Azure Marketplace

**Tipo:** recurso alterado  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** integração de terceiros

Se um aplicativo listado no [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) for compatível com o logon único baseado em SAML, clicar em **Obter agora** fornecerá o tutorial de integração associado a esse aplicativo. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Desempenho mais rápido do provisionamento automático de usuários do Azure AD para aplicativos SaaS

**Tipo:** recurso alterado  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Anteriormente, os clientes que usavam os conectores de provisionamento de usuário do Azure Active Directory para aplicativos SaaS (por exemplo, Salesforce, ServiceNow e Box) poderiam ter um desempenho lento se os inquilinos do Azure AD contivessem mais de 100.000 usuários e grupos combinados e estivessem usando atribuições de usuário e grupo determinar quais usuários devem ser provisionados.

Em 2 de abril de 2018, aprimoramentos de desempenho significativos foram implantados no serviço de provisionamento do Azure AD que reduzem muito o tempo necessário para executar as sincronizações iniciais entre o Active Directory do Azure e os aplicativos de SaaS de destino.

Como resultado, muitos clientes que tinham sincronizações iniciais para aplicativos que demoravam dias ou nunca eram concluídas, agora estão sendo concluídas em questão de minutos ou horas.

Para obter mais informações, consulte [What happens during provisioning?](/azure//active-directory/app-provisioning/how-provisioning-works) (O que acontece durante o provisionamento?)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Redefinição de senha de autoatendimento da tela de bloqueio do Windows 10 para computadores associados ao Azure AD híbrido

**Tipo:** recurso alterado  
**Categoria de serviço:** redefinição de senha de autoatendimento  
**Funcionalidade do produto:** autenticação de usuário
 
Atualizamos o recurso SSPR do Windows 10 para incluir suporte para computadores ingressados no Azure AD híbridos. Esse recurso está disponível no Windows 10 RS4 e permite que os usuários redefinam sua senha da tela de bloqueio de um computador Windows 10. Os usuários habilitados e registrados para redefinição de senha de autoatendimento podem usar esse recurso.

Para obter mais informações, consulte [Redefinição de senha do Azure AD na tela de logon](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Março de 2018
 
### <a name="certificate-expire-notification"></a>Notificação de expiração de certificado

**Tipo:** corrigido  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
O Azure AD envia uma notificação quando um certificado para um aplicativo que está na galeria ou não está prestes a expirar. 

Alguns usuários não recebiam as notificações para aplicativos corporativos configurados para SSO baseado em SAML. Esse problema foi resolvido. O Azure AD envia uma notificação sobre certificados que expiram em 7, 30 e 60 dias. Você pode ver esse evento nos logs de auditoria. 

Para obter mais informações, consulte:

- [Gerenciar certificados para logon único federado no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Audit activity reports in the Azure Active Directory portal (Relatórios de atividades de auditoria no portal do Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Provedores de identidade do Twitter e do GitHub no Azure AD B2C

**Tipo:** novo recurso  
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** B2B/B2C
 
Agora você pode adicionar o Twitter ou o GitHub como um provedor de identidade no Azure AD B2C. O Twitter está mudando da visualização pública para GA. O GitHub está sendo lançado em visualização pública.

Para saber mais, confira [O que é a colaboração B2B do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Restringir o acesso ao navegador usando Intune Managed Browser com acesso condicional baseado em aplicativo do Azure AD para iOS e Android

**Tipo:** novo recurso  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade
 
**Agora, em visualização pública!**

**SSO do Intune Managed Browser:** seus funcionários podem usar o logon único em clientes nativos (como o Microsoft Outlook) e o Intune Managed Browser para todos os aplicativos conectados ao Microsoft Azure AD.

**Intune Managed browser suporte a acesso condicional:** Agora você pode exigir que os funcionários usem o navegador gerenciado do Intune usando políticas de acesso condicional baseadas em aplicativo.

Leia mais sobre isso em nossa [postagem no blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Para obter mais informações, consulte:

- [Configurar o acesso condicional baseado no aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Configurar políticas do navegador gerenciado](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdlets de proxy de aplicativo no módulo do PowerShell GA

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** Controle de Acesso
 
O suporte para cmdlets de proxy de aplicativo agora está no módulo de GA do PowerShell! Isso exige que você permaneça atualizado nos módulos do PowerShell – se você se tornar mais de um ano atrás, alguns cmdlets podem parar de funcionar. 

Para saber mais, veja [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Os clientes nativos do Office 365 têm suporte do SSO Contínuo usando um protocolo não interativo

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
O usuário que utiliza clientes nativos do Office 365 (versão 16.0.8730.xxxx e acima) obtém uma experiência de logon silencioso usando o SSO Contínuo. Esse suporte é fornecido com a adição de um protocolo não interativo (WS-Trust) ao Azure AD.

Para saber mais, confira [Como entrar em um cliente nativo com o trabalho de SSO contínuo?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Os usuários obtêm uma experiência de logon silenciosa, com o SSO contínuo, se um aplicativo envia solicitações de entrada para os terminais de inquilino do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
Os usuários terão uma experiência de logon silenciosa, com SSO contínuo, se um aplicativo (por exemplo, `https://contoso.sharepoint.com`) enviar solicitações de entrada para pontos de extremidade com locatários do Microsoft Azure AD, ou seja, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>`, em vez de enviar para o ponto de extremidade comum do Azure AD (`https://login.microsoftonline.com/common/<...>`).

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>É preciso adicionar apenas uma URL do Azure AD, em vez de duas URLs como anteriormente, às configurações de zona de Intranet dos usuários para implementar o SSO Contínuo

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
Para implementar o SSO contínuo para seus usuários, você deve adicionar apenas uma URL do Azure Active Directory às configurações de zona da Intranet dos usuários usando a política de grupo no Active Directory: `https://autologon.microsoftazuread-sso.com`. Anteriormente, era necessário que os clientes adicionassem duas URLs.

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em março de 2018, adicionamos esses 15 novos aplicativos com suporte de Federação à nossa galeria de aplicativos:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant by FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>O PIM para Recursos do Azure está disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Se você estiver usando o Azure AD Privileged Identity Management para funções de diretório, agora poderá usar o acesso com limite de tempo do PIM e os recursos de atribuição para funções de recurso do Azure como Assinaturas, Grupos de Recursos, Máquinas Virtuais e qualquer outro recurso com suporte pelo Azure Resource Manager. Imponha a Autenticação Multifator ao ativar funções Just-In-Time e agende as ativações em coordenação com os períodos de alterações aprovadas. Além disso, esta versão adiciona aprimoramentos não disponíveis durante a visualização pública, incluindo uma interface do usuário atualizada, fluxos de trabalho de aprovação e a capacidade de estender funções que expirarão em breve e renovar funções expiradas.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (Versão Prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Adicionar declarações opcionais aos tokens de aplicativos (visualização pública)

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
O aplicativo Azure AD agora pode solicitar declarações personalizadas ou opcionais em tokens SAML ou JWTs.  Essas são as declarações sobre o usuário ou locatário que não são incluídas por padrão no token, devido a restrições de tamanho ou aplicabilidade.  Isso está atualmente em visualização pública para aplicativos Azure AD nos pontos de extremidade v1.0 e v2.0.  Confira a documentação para saber mais sobre quais declarações podem ser adicionadas e como editar o manifesto do aplicativo para solicitá-las.  

Para saber mais, confira [Declarações opcionais no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>O Azure AD dá suporte a PKCE para fluxos de OAuth mais seguros

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
Os documentos do Azure AD foram atualizados para indicar o suporte para PKCE, que permite a comunicação mais segura durante o fluxo de concessão de Código de Autorização do OAuth 2.0.  Code_challenges S256 e o texto sem formatação têm suporte nos pontos de extremidade v1.0 e v2.0. 

Para saber mais, confira [Solicitar um código de autorização](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>O suporte para o provisionamento de todos os valores de atributo de usuário está disponível na API Workday Get_Workers

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
A visualização pública do provisionamento de entrada do Workday para Active Directory e Azure AD agora dá suporte à capacidade de extrair e provisionar todos os valores de atributo disponíveis na API Workday Get_Workers. Isso inclui suporte a centenas de atributos padrão e personalizados adicionais, além daqueles fornecidos na versão inicial do conector de provisionamento de entrada Workday.

Para saber mais, confira: [Personalizar a lista de atributos de usuário do Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Alterar a associação de grupo de dinâmica para estática e vice-versa

**Tipo:** novo recurso  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração
 
É possível alterar como a associação é gerida em um grupo. Isso é útil quando você quer manter o mesmo ID e nome de grupo no sistema, então, todas as referências existentes no grupo ainda serão válidas; criar um novo grupo exigiria atualizar essas referências.
Atualizamos o centro de administração do Azure AD para oferecer suporte a essa funcionalidade. Agora, os clientes podem converter grupos existentes de associação dinâmica para associação atribuída e vice-versa. Os cmdlets do PowerShell existentes também ainda estão disponíveis.

Para obter mais informações, consulte [regras de associação dinâmica para grupos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Comportamento aprimorado de saída com o SSO contínuo

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário
 
Anteriormente, mesmo que os usuários saíssem explicitamente de um aplicativo protegido pelo Azure AD, seriam conectados automaticamente de novo usando o SSO Contínuo se tentassem acessar um aplicativo Azure AD novamente na rede corporativa por meio de seus dispositivos associados ao domínio. Com essa alteração, há suporte para sair.  Isso permite que os usuários escolham a mesma conta do Azure AD ou outra para entrar novamente, em vez de serem conectados automaticamente usando o SSO contínuo.

Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Versão de conector de proxy de aplicativo 1.5.402.0 lançado

**Tipo:** recurso alterado  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Esta versão do conector está sendo distribuída gradualmente até novembro. Essa nova versão do conector inclui as seguintes alterações:

- O conector agora define cookies no nível do domínio em vez do nível do subdomínio. Isso garante uma experiência de SSO sem problemas e evita prompts de autenticação redundantes.
- Suporte para solicitações de codificação em partes
- Monitoramento de integridade de conector aprimorado 
- Várias correções de bug e melhorias de estabilidade

Para saber mais, veja [Noções básicas sobre conectores de proxy de aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Fevereiro de 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Navegação aprimorada para gerenciar usuários e grupos

**Tipo:** plano de alteração  
**Categoria de serviço:** gerenciamento de diretório  
**Funcionalidade do produto:** diretório

A experiência de navegação para gerenciar usuários e grupos foi simplificada. Agora, é possível navegar da visão geral do diretório diretamente para a lista de todos os usuários, com acesso mais fácil à lista dos usuários excluídos. Adicionalmente, você também pode navegar da visão geral do diretório diretamente para a lista de todos os grupos, com acesso mais fácil às configurações de gerenciamento de grupo. Além disso, a partir da página de visão geral do diretório, é possível procurar um usuário, grupo, aplicativo empresarial ou registro do aplicativo. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Disponibilidade de inscrições e relatórios de auditoria no Microsoft Azure operado pela 21Vianet (parceiro 21Vianet do Azure na China)

**Tipo:** novo recurso  
**Categoria de serviço:** pilha do Azure  
**Funcionalidade do produto:** monitoramento e relatórios

Relatórios de log de atividades do Azure Active Directory já estão disponíveis no Microsoft Azure operado pelas instâncias 21Vianet (parceiro 21Vianet do Azure na China). Os logs a seguir estão incluídos:

- **Logs de atividades de entradas** - Inclui todos os logs de entradas associados ao locatário.

- **Logs de auditoria de senha de autoatendimento** - Inclui todos os logs de auditoria SSPR.

- **Logs de auditoria de gerenciamento de diretório** - Inclui todos os registros de auditoria relacionados ao gerenciamento de diretório, como gerenciamento de usuários, gerenciamento de aplicativos e outros.

Com esses logs, é possível obter informações sobre como o ambiente está funcionando. Os dados fornecidos permitem a você:

- Determinar como os aplicativos e serviços serão utilizados pelos usuários.

- Solucionar problemas que impedem a conclusão dos trabalhos pelos usuários.

Para obter mais informações sobre como usar esses relatórios, consulte [Relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Usar a função "Leitor do Relatório" (função não administrador) para exibir relatórios de atividades do Azure Active Directory

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios

Como parte dos comentários dos clientes para permitir que funções não administrativas tenham acesso aos logs de atividades do Azure AD, habilitamos a capacidade para os usuários que estão na função "leitor de relatório" para acessar a atividade de entrada e auditoria no portal do Azure, bem como usando o Microsoft Graph API. 

Para obter mais informações sobre como usar esses relatórios, consulte [Relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Declaração EmployeeID disponível como atributo de usuário e identificador de usuário

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
É possível configurar **EmployeeID** como o identificador de usuário e atributo de usuário para usuários membros e usuários B2B em aplicativos de logon com base em SAML da interface do usuário do aplicativo Enterprise.

Para obter mais informações, consulte [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Gerenciamento de aplicativo simplificado usando caracteres curingas no Proxy de Aplicativo do AD

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** autenticação de usuário
 
Para tornar a implantação de aplicativo mais fácil e reduzir a sobrecarga administrativa, agora podemos dar suporte à capacidade de publicar aplicativos usando caracteres curingas. Para publicar um aplicativo curinga, você pode seguir o fluxo de publicação de aplicativo padrão, mas usar um caractere curinga nas URLs internas e externas.

Para obter mais informações, consulte [Aplicativos curinga no proxy de aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Novos cmdlets para dar suporte à configuração do Proxy de Aplicativo

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** plataforma

A versão mais recente do módulo de visualização do AzureAD PowerShell contém novos cmdlets que permitem que os clientes configurem aplicativos de proxy de aplicativo usando o PowerShell.

Os novos cmdlets são: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Novos cmdlets para dar suporte à configuração de grupos

**Tipo:** novo recurso  
**Categoria de serviço:** proxy de aplicativo  
**Funcionalidade do produto:** plataforma

A versão mais recente do módulo AzureAD PowerShell contém cmdlets para gerenciar grupos no Azure Active Directory. Esses cmdlets já estavam disponíveis no módulo AzureADPreview e agora adicionados ao módulo AzureAD

Os cmdlets de Grupo agora liberados para disponibilidade geral são: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Uma nova versão do Azure AD Connect está disponível

**Tipo:** novo recurso  
**Categoria de serviço:** AD Sync  
**Funcionalidade do produto:** plataforma
 
O Azure AD Connect é a ferramenta preferencial para sincronizar dados entre o Azure Active Directory e nas fontes de dados locais, incluindo Windows Server Active Directory e LDAP.

>[!Important]
>Essa compilação apresenta alterações de regras de sincronização e esquema. O Serviço de Sincronização do Azure AD Connect dispara as etapas de Importação Completa e Sincronização Completa após uma atualização. Para obter informações sobre como alterar esse comportamento, consulte [Como adiar a sincronização completa após a atualização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Essa versão contém as alterações e atualizações a seguir:

**Problemas corrigidos**

- Correção da janela de tempo nas tarefas em segundo plano da página de Filtragem de Partições ao comutar para a próxima página.

- Corrigido um bug que causou violação de Acesso durante a ação personalizada do ConfigDB.

- Corrigido um bug para recuperar do tempo limite de conexão do SQL.

- Corrigido um bug onde os certificados com curingas SAN falharam no teste de pré-requisito.

- Corrigido um erro que causa o crash miiserver.exe durante a exportação do conector AAD.

- Corrigido um erro no qual uma tentativa de senha incorreta conectada ao DC durante a execução fazia com que o assistente de conexão do AAD alterasse a configuração

**Novos recursos e aprimoramentos**
 
- Telemetria de aplicativos - Os administradores podem ativar/desativar essa classe de dados.

- Dados de Integridade do Azure Active Directory - Os administradores devem visitar o portal de integridade para controlar as configurações de integridade. Quando a política de atendimento for alterada, os agentes farão a leitura e irão impor a política.

- Adicionadas ações de configuração de write-back de dispositivo e uma barra de progresso para inicialização de página.

- Diagnósticos gerais aprimorados com relatório HTML e coleta de dados completa em um relatório HTML/ZIP-Text.

- Aperfeiçoada confiabilidade da atualização automática e adicionada a telemetria adicional para garantir que a integridade do servidor possa ser determinada.

- Restringir permissões disponíveis para contas privilegiadas na conta do AD Connector. Para novas instalações, o assistente restringe as permissões que as contas privilegiadas tenham na conta MSOL, após criar a conta MSOL. As alterações afetam instalações expressas e instalações personalizadas com a conta de Criação Automática.

- Alterado o instalador para não exigir privilégio de SA na instalação limpa do AADConnect.

- Novo utilitário para solucionar problemas de sincronização para um objeto específico. Atualmente, o utilitário verifica os seguintes itens:

    - UserPrincipalName incompatível entre o objeto de usuário sincronizado e a conta de usuário no Locatário do Azure Active Directory.
  
    - Se o objeto for filtrado da sincronização devido à filtragem de domínio
  
    - Se o objeto for filtrado da sincronização devido à filtragem da OU (unidade organizacional)

- Novo utilitário para sincronizar o hash de senha atual armazenado no Active Directory local para uma conta de usuário específica. O utilitário não exige uma alteração de senha. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplicativos que dão suporte a políticas de Proteção de Aplicativo do Intune adicionadas para uso com o acesso condicional baseado em aplicativo do Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

Adicionamos mais aplicativos que dão suporte ao acesso condicional baseado em aplicativo. Agora, é possível acessar o Office 365 e outros aplicativos de nuvem conectados ao Azure Active Directory usando esses aplicativos clientes aprovados.

As seguintes aplicações serão adicionadas até o final de fevereiro:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Termos de uso atualizar para a experiência móvel 

**Tipo:** recurso alterado  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** conformidade

Quando os termos de uso são exibidos, agora você pode clicar em **tendo problemas para exibir? Clique aqui**. Clicar nesse link, abre os termos de uso nativamente no seu dispositivo. Independentemente do tamanho da fonte no documento ou do tamanho da tela do dispositivo, é possível ampliar e ler o documento, conforme necessário. 

---
 
## <a name="january-2018"></a>Janeiro de 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD 

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em janeiro de 2018, os seguintes novos aplicativos com suporte à federação foram adicionados à galeria de aplicativos:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [Software de Gerenciamento de Privacidade OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [Diretório Federado IriusRisk e [NetBenefits de Fidelidade](https://go.microsoft.com/fwlink/?linkid=864701).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Entre com risco adicional detectado

**Tipo:** novo recurso  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

O insight que você obtém para uma detecção de risco detectado está vinculado à sua assinatura do Azure AD. Com a edição Azure AD Premium P2, você obtém as informações mais detalhadas sobre todas as detecções subjacentes.

Com a edição P1 do Azure AD Premium, as detecções que não são cobertas pela sua licença são exibidas como a entrada de detecção de risco com risco adicional detectado.

Para obter mais informações, confira [Detecções de risco do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ocultar aplicativos do Office 365 dos painéis de acesso do usuário final

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

Agora você pode gerenciar melhor como os aplicativos do Office 365 são exibidos nos painéis de acesso do usuário por meio de uma nova configuração de usuário. Essa opção é útil para reduzir o número de aplicativos nos painéis de acesso de um usuário, se você preferir mostrar apenas os aplicativos do Office no portal do Office. A configuração está localizada nas **Configurações do usuário** e está marcada como **Os usuários só podem ver os aplicativos do Office 365 no portal do Office 365**.

Para obter mais informações, consulte [Ocultar um aplicativo da experiência de um usuário no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Entrada direta em aplicativos habilitados para SSO de Senha diretamente na URL do aplicativo 

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

A extensão do navegador Meus Aplicativos agora está disponível por meio de uma ferramenta conveniente que fornece a funcionalidade de logon único de Meus Aplicativos como um atalho no navegador. Após a instalação, o usuário verá um ícone de waffle no navegador que fornece acesso rápido aos aplicativos. Os usuários agora podem usufruir dos seguintes benefícios:

- A capacidade de entrar diretamente em aplicativos baseados em SSO de senha na página de entrada do aplicativo
- Iniciar um aplicativo usando o recurso de pesquisa rápida
- Atalhos para os aplicativos usados recentemente da extensão
- A extensão está disponível para Microsoft Edge, Chrome e Firefox.
 
Para obter mais informações, consulte [Extensão de Entrada Segura dos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A experiência de administração do Azure AD no Portal Clássico do Azure foi retirada

**Tipo:** preterido   
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** diretório

A partir de 8 de janeiro de 2018, a experiência de administração do Azure AD no portal clássico do Azure foi desativada. Isso ocorreu em conjunto com a desativação do próprio portal clássico do Azure. No futuro, você deve usar o [centro de administração do Azure AD](https://aad.portal.azure.com) para toda a administração baseada em portal do AD do Azure.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>O portal da Web do PhoneFactor foi desativado

**Tipo:** preterido  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** diretório
 
A partir de 8 de janeiro de 2018, o portal da Web do PhoneFactor foi desativado. Este portal foi usado para a administração do servidor MFA, mas essas funções foram movidas para o portal do Azure em portal.azure.com. 

A configuração do MFA está localizada em: **Azure Active Directory \> Servidor MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** preterido  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade  


Com a disponibilidade geral do novo Azure Active Directory, um console de Administração e novas APIs agora estão disponíveis para relatórios de atividade e de segurança, as APIs de relatório no ponto de extremidade “/reports” foram desativadas em 31 de dezembro de 2017.

**O que está disponível?**

Como parte da transição para o novo console de administrador, disponibilizamos duas novas APIs para recuperar os Logs de Atividades do Azure AD. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados anteriormente disponíveis por meio dos relatórios de segurança agora podem ser acessados por meio da API de detecções de risco da proteção de identidade no Microsoft Graph.

Para obter mais informações, consulte:

- [Introdução à API de relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Dezembro de 2017

### <a name="terms-of-use-in-the-access-panel"></a>Termos de uso no Painel de Acesso

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** conformidade
 
Agora você pode acessar o Painel de Acesso e exibir os termos de uso que aceitou anteriormente.

Siga estas etapas:

1. Acesse o [portal do MyApps](https://myapps.microsoft.com) e entre.

2. No canto superior direito, selecione seu nome e, em seguida, selecione **Perfil** na lista. 

3. Em seu **Perfil**, selecione **Examinar termos de uso**. 

4. Agora, você pode ler os termos de uso que você aceitou. 

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de entrada do Azure AD.

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** autenticação de usuário
 
As interfaces do usuário do Azure AD e do sistema de identidade da conta da Microsoft foram reformuladas para que tenham uma aparência consistente. Além disso, a página de entrada do Azure AD coleta o nome de usuário primeiro, seguido da credencial em uma segunda tela.

Para obter mais informações, consulte [A nova experiência de entrada do Azure AD agora está em visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos prompts de entrada: uma nova experiência “Manter-me conectado” para a entrada do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** autenticação de usuário
 
A caixa de seleção **Manter-me conectado** na página de entrada do Azure AD foi substituída por um novo prompt exibido depois que você é autenticado com êxito. 

Se você responder **Sim** a esse prompt, o serviço fornecerá um token de atualização persistente. Esse comportamento é o mesmo exibido quando você selecionava a caixa de seleção **Manter-me conectado** na experiência antiga. Para locatários federados, esse prompt é mostrado após a autenticação bem-sucedida no serviço federado.

Para obter mais informações, consulte [Menos prompts de entrada: a nova experiência “Manter-me conectado” do Azure AD está em versão prévia](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Adicionar uma configuração para exigir que os termos de uso sejam expandidos antes da aceitação

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** conformidade
 
Uma opção para os administradores exige que seus usuários expandam os termos de uso antes de aceitar os termos.

Selecione **Ativado** ou **Desativado** para solicitar que os usuários expandam os termos de uso. A configuração **Ativado** exige que os usuários visualizem os termos de uso antes de aceitá-los.

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação com escopo para atribuições de função qualificadas

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Use a ativação com escopo para ativar atribuições de função qualificadas em recursos do Azure com menos autonomia do que nos padrões de atribuição originais. Um exemplo é quando você está atribuído como o proprietário de uma assinatura em seu locatário. Com a ativação com escopo, é possível ativar a função de proprietário para até cinco recursos contidos na assinatura (como grupos de recursos e máquinas virtuais). A ativação com escopo pode reduzir a possibilidade de executar alterações indesejadas em recursos críticos do Azure.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Novos aplicativos federados na galeria de aplicativos Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros

Em dezembro de 2017, adicionamos esses novos aplicativos com suporte de federação à nossa galeria de aplicativos:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial).

Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para as funções de diretório do Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
O fluxo de trabalho de aprovação para as funções de diretório do Azure AD está geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de função com privilégios podem exigir que membros de função qualificada solicitem a ativação de função antes que possam usar a função com privilégios. Vários usuários e grupos podem receber responsabilidades de aprovação. Os membros de função qualificados recebem notificações quando a aprovação é concluída e suas funções estão ativas.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticação de passagem: Suporte para o Skype for Business

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário

A autenticação de passagem agora dá suporte a entradas do usuário em aplicativos cliente do Skype for Business que dão suporte à autenticação moderna, que incluem topologias online e híbridas. 

Para obter mais informações, consulte [Topologias do Skype for Business compatíveis com a autenticação moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Atualizações no Azure AD Privileged Identity Management para o RBAC do Azure (versão prévia)

**Tipo:** recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Com a atualização da visualização pública do Azure AD PIM (Privileged Identity Management) para o RBAC (Controle de Acesso Baseado em Função), agora você pode:

* Usar a Administração Just Enough.
* Exigir aprovação para ativar as funções de recurso.
* Agendar uma ativação futura de uma função que exige aprovação para funções do Azure AD e de RBAC do Azure.
 
Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (versão prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Novembro de 2017
 
### <a name="access-control-service-retirement"></a>Desativação do serviço de Controle de Acesso

**Tipo:** plano de alteração  
**Categoria de serviço:** serviço de Controle de Acesso  
**Funcionalidade do produto:** serviço de Controle de Acesso 

O Controle de Acesso do Azure Active Directory (também conhecido como serviço de Controle de Acesso) será desativado no final de 2018. Mais informações que incluem um cronograma detalhado e diretrizes de migração de alto nível serão fornecidas nas próximas semanas. Você pode deixar comentários nesta página com dúvidas sobre o serviço de Controle de Acesso e um membro da equipe responderá a suas dúvidas.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso do navegador ao Intune Managed Browser 

**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade

Você pode restringir o acesso do navegador ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD usando o Intune Managed Browser como um aplicativo aprovado. 

Agora você pode configurar a seguinte condição para acesso condicional baseado em aplicativo:

**Aplicativos cliente:** Browser

**Qual é o efeito da alteração?**

Atualmente, o acesso está bloqueado quando essa condição é usada. Quando a versão prévia estiver disponível, todo o acesso exigirá o uso do aplicativo de navegador gerenciado. 

Procure essa funcionalidade e mais informações em notas de versão e blogs futuros. 

Para obter mais informações, consulte [acesso condicional no Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para acesso condicional baseado em aplicativo do Azure AD

**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte dos termos de uso para diversos idiomas

**Tipo:** novo recurso    
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** conformidade

Os administradores agora podem criar novos termos de uso que contêm vários documentos PDF. Você pode marcar esses documentos em PDF com um idioma correspondente. Os usuários recebem o PDF com o idioma correspondente de acordo com suas preferências. Se não houver nenhuma correspondência, o idioma padrão será mostrado.

---
 
### <a name="real-time-password-writeback-client-status"></a>Status do cliente de write-back de senha em tempo real

**Tipo:** novo recurso  
**Categoria de serviço:** redefinição de senha de autoatendimento  
**Funcionalidade do produto:** autenticação de usuário

Agora você pode examinar o status do cliente de write-back de senha local. Essa opção está disponível na seção **Integração local** da página [Redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Se houver problemas com sua conexão com o cliente de write-back local, você verá uma mensagem de erro que exibirá:

- Informações sobre o motivo pelo qual não é possível se conectar ao cliente de write-back local.
- Um link para a documentação que auxilia na resolução do problema. 

Para obter mais informações, consulte [Integração local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Acesso condicional com base no aplicativo do Azure AD 
 
**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** segurança e proteção de identidade

Agora você pode restringir o acesso ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD a [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) que dão suporte a políticas de proteção de aplicativo do Intune usando o [acesso condicional baseado em aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Políticas da Proteção de Aplicativo do Intune são usadas para configurar e proteger os dados da empresa nesses aplicativos cliente.

Ao combinar [aplicativos baseados em aplicativo](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) com políticas de acesso condicional [com base no dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) , você tem a flexibilidade de proteger dados para dispositivos pessoais e da empresa.

As seguintes condições e controles agora estão disponíveis para uso com acesso condicional baseado em aplicativo:

**Condição de plataforma com suporte**

- iOS
- Android

**Condição de aplicativos cliente**

- Aplicativos móveis e clientes de desktop

**Controle de acesso**

- Exigir um aplicativo cliente aprovado

Para obter mais informações, consulte [acesso condicional baseado em aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gerenciar dispositivos do Azure AD no portal do Azure

**Tipo:** novo recurso  
**Categoria de serviço:** registro e gerenciamento de dispositivos  
**Funcionalidade do produto:** segurança e proteção de identidade

Agora você pode encontrar todos os dispositivos conectados ao Azure AD e as atividades relacionadas ao dispositivo em um único local. Há uma nova experiência de administração para gerenciar todas as suas identidades e configurações de dispositivos no portal do Azure. Nesta versão, você pode:

- Exiba todos os dispositivos que estão disponíveis para acesso condicional no Azure AD.
- Exibir propriedades, incluindo dispositivos vinculados do Azure AD híbrido.
- Encontrar chaves do BitLocker para os dispositivos ingressados no Azure AD, gerencie seu dispositivo com o Intune e muito mais.
- Gerenciar configurações relacionadas ao dispositivo do Azure AD.

Para obter mais informações, consulte [Gerenciar dispositivos usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como uma plataforma de dispositivo para acesso condicional do Azure AD 

**Tipo:** novo recurso    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade 

Agora você pode incluir (ou excluir) o macOS como uma condição de plataforma de dispositivo em sua política de acesso condicional do Azure AD. Com a adição do macOS às plataformas de dispositivos com suporte, você pode:

- **Registrar e gerenciar dispositivos macOS usando o Intune.** Semelhante a outras plataformas como iOS e Android, um aplicativo do portal da empresa está disponível para o macOS para realizar registros unificados. Use o novo aplicativo do portal da empresa para o macOS para registrar um dispositivo no Intune e registrá-lo no Azure AD.
- **Garantir que os dispositivos macOS seguem as políticas de conformidade de sua organização definidas no Intune.** No Intune no portal do Azure, agora você pode configurar políticas de conformidade para dispositivos macOS. 
- **Restringir o acesso a aplicativos no Azure AD apenas a dispositivos macOS em conformidade.** A criação de política de acesso condicional tem o macOS como uma opção de plataforma de dispositivo separada. Agora você pode criar políticas de acesso condicional específicas do macOS para o conjunto de aplicativos de destino no Azure.

Para obter mais informações, consulte:

- [Criar uma política de conformidade do dispositivo para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso condicional no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Extensão Servidor de Políticas de Rede para a Autenticação Multifator do Azure 

**Tipo:** novo recurso    
**Categoria de serviço:** autenticação multifator  
**Funcionalidade do produto:** autenticação de usuário

A extensão do Servidor de Políticas de Rede para a Autenticação de Vários Fator do Azure adiciona recursos de Autenticação de Vários Fatores com base na nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão Servidor de Políticas de Rede, você pode adicionar uma chamada telefônica, mensagem de texto ou verificação de aplicativo de telefone a seu fluxo de autenticação existente. Você não precisa instalar, configurar nem manter novos servidores. 

Essa extensão foi criada para organizações que desejam proteger as conexões de rede virtual privada sem implantar o Servidor de Autenticação Multifator do Azure. A extensão Servidor de Políticas de Rede atua como um adaptador entre o RADIUS e a Autenticação Multifator do Azure baseada em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.

Para obter mais informações, consulte [Integrar sua infraestrutura existente do Servidor de Políticas de Rede à Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurar ou remover permanentemente os usuários excluídos

**Tipo:** novo recurso    
**Categoria de serviço:** gerenciamento de usuário  
**Funcionalidade do produto:** diretório 

No centro de administração do Azure AD, agora você pode:

- Restaurar um usuário excluído. 
- Excluir permanentemente um usuário.

**Para experimentar:**

1. No centro de administração do Azure AD, selecione [Todos os usuários](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) na seção **Gerenciar**. 

2. Na lista **Mostrar**, selecione **Usuários excluídos recentemente**. 

3. Selecione um ou mais usuários excluídos recentemente e, em seguida, restaure-os ou exclua-os permanentemente.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos cliente aprovados para acesso condicional baseado em aplicativo do Azure AD
 
**Tipo:** recurso alterado  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade

Os seguintes aplicativos foram adicionados à lista de [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft Planner
- Proteção de Informações do Azure 

Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Acesso condicional com base no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Use "OR" entre os controles em uma política de acesso condicional 

**Tipo:** recurso alterado    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade
 
Agora você pode usar "OR" (exigir um dos controles selecionados) para controles de acesso condicional. Use esse recurso para criar políticas com “OR” entre os controles de acesso. Por exemplo, você pode usar esse recurso para criar uma política que exige que um usuário faça login usando a Autenticação Multifator "OU" para estar em um dispositivo compatível.

Para obter mais informações, consulte [controles no acesso condicional do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregação de detecções de risco em tempo real

**Tipo:** recurso alterado    
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção de identidade

Em Azure AD Identity Protection, todas as detecções de risco em tempo real originadas do mesmo endereço IP em um determinado dia agora são agregadas para cada tipo de detecção de risco. Essa alteração limita o volume de detecções de risco mostrado sem nenhuma alteração na segurança do usuário.

A detecção em tempo real subjacente funciona sempre que o usuário se conecta. Se você tiver uma política de segurança de risco de login configurada para Autenticação de vários fatores ou bloqueio de acesso, ela ainda será acionada durante cada entrada arriscada.
 
---
 
## <a name="october-2017"></a>Outubro de 2017

### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade  

O Portal do Azure oferece:

- Um novo console de administração do Azure AD.
- Novas APIs para relatórios de atividade e segurança.
 
Devido a essas novas funcionalidades, as APIs de relatórios no ponto de extremidade /reports foram desativadas em 10 de dezembro de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detecção automática de campo de conexão

**Tipo:** corrigido   
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** logon único  

O Azure AD é compatível com a detecção automática de campo de entrada para os aplicativos que renderizam um campo de nome de usuário e senha HTML. Essas etapas são documentadas em [Como capturar automaticamente os campos de entrada para um aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Você pode encontrar essa funcionalidade adicionando um aplicativo *Inexistente na galeria* na página **Aplicativos empresariais** do [Portal do Azure](https://aad.portal.azure.com). Além disso, você pode configurar o modo **Logon Único** neste novo aplicativo como **Logon Único Baseado em Senha**, inserir uma URL da Web e, em seguida, salvar a página.
 
Devido a um problema do serviço, essa funcionalidade ficou temporariamente desabilitada. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

### <a name="new-multi-factor-authentication-features"></a>Novos recursos de autenticação multifator

**Tipo:** novo recurso  
**Categoria de serviço:** autenticação multifator  
**Funcionalidade do produto:** segurança e proteção de identidade  

O MFA (Autenticação multifator) é uma parte essencial da proteção de sua organização. Para tornar as credenciais mais adaptáveis e a experiência mais direta, os seguintes recursos foram adicionados: 

- Os resultados do desafio multifator são diretamente integrados ao relatório de entrada do Azure AD, que inclui o acesso programático aos resultados do MFA.
- A configuração do MFA está mais profundamente integrada à experiência de configuração do Azure AD no portal do Azure.

Com essa versão prévia pública, o gerenciamento e os relatórios de MFA são parte integrante da experiência de configuração central do Azure AD. Agora, você pode gerenciar a funcionalidade do portal de gerenciamento do MFA na experiência do Azure AD.

Para obter mais informações, consulte [Referência de relatórios do MFA no portal do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Termos de uso

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** conformidade  

Use os termos de uso do Azure AD para apresentar informações como avisos de isenção de responsabilidade relevantes para requisitos legais ou de conformidade para os usuários.

Use os termos de uso do Azure AD nos seguintes cenários:

- Termos de uso gerais para todos os usuários em sua organização
- Termos de uso específicos com base nos atributos de um usuário (por exemplo, médicos versus enfermeiras ou funcionários nacionais vs. internacionais, feito por grupos dinâmicos)
- Termos de uso específicos para acessar aplicativos de negócios de alto impacto, como o Salesforce

Para obter mais informações, consulte [Termos de uso do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Melhorias no Privileged Identity Management

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management  

Com o Azure AD Privileged Identity Management, você pode gerenciar, controlar e monitorar o acesso aos recursos do Azure (versão prévia) em sua organização para:

- Assinaturas
- Grupos de recursos
- Máquinas virtuais 

Todos os recursos do portal do Azure que usam a funcionalidade de RBAC do Azure podem usufruir todas as funcionalidades de segurança e gerenciamento do ciclo de vida que o Azure AD Privileged Identity Management tem a oferecer.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Análises de acesso

**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** conformidade  

As organizações podem usar revisões de acesso (versão prévia) para gerenciar com eficiência as associações a um grupo e o acesso a aplicativos corporativos: 

- Você pode reconfirmar o acesso do usuário convidado usando as revisões do acesso deles a aplicativos e a associações de grupos. Os revisores podem decidir com eficiência se permitirão o acesso contínuo aos convidados de acordo com as informações fornecidas pelas revisões de acesso.
- Você pode reconfirmar o acesso de funcionários a aplicativos e associações de grupo com as revisões de acesso.

Você pode coletar os controles de revisão de acesso em programas relevantes para que sua organização rastreie as análises de conformidade ou aplicativos suscetíveis a riscos.

Para obter mais informações, consulte [Revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultar aplicativos de terceiros de Meus Aplicativos e do inicializador de aplicativos do Office 365

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** logon único  

Agora é possível gerenciar melhor os aplicativos que são mostrados nos portais dos usuários por meio de uma nova propriedade **ocultar aplicativo**. Você pode ocultar aplicativos para ajudar em casos em que os blocos de aplicativos são mostrados para serviços de back-end ou blocos duplicados e emails secundários de iniciadores de aplicativos dos usuários. A alternância está localizada na seção **Propriedades** do aplicativo de terceiros e é rotulada **Visível para o usuário?** Também é possível ocultar um aplicativo de forma programática por meio do PowerShell. 

Para obter mais informações, consulte [Ocultar um aplicativo de terceiros da experiência de um usuário no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**O que está disponível?**

 Como parte da transição para o novo console de administrador, duas novas APIs para recuperar os logs de atividades do Azure AD estão disponíveis. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados anteriormente disponíveis por meio dos relatórios de segurança agora podem ser acessados por meio da API de detecções de risco da proteção de identidade no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix para o Identity Manager

**Tipo:** recurso alterado  
**Categoria de serviço:** Identity Manager  
**Funcionalidade do produto**: gerenciamento do ciclo de vida da identidade  

Um pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) está disponível a partir de 25 de setembro de 2017 para o Identity Manager 2016 Service Pack 1. Esse pacote cumulativo:

- Resolve problemas e adiciona melhorias.
- É uma atualização cumulativa que substitui todas as atualizações do Identity Manager 2016 Service Pack 1 até o build 4.4.1459.0 para o Identity Manager 2016. 
- Exige o Identity Manager 2016 build 4.4.1302.0. 

Para obter mais informações, consulte [Pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) disponível para o Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
