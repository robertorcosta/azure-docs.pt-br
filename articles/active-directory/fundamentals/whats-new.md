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
ms.date: 09/03/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff942cdad74c3b8b71a8f1658f13faae021b983
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567445"
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

Para saber mais, consulte [permissões de acesso de convidado restrito](../users-groups-roles/users-restrict-guest-permissions.md) e [permissões padrão de usuários](./users-default-permissions.md).
 
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
** Capacidade do produto: ** Serviços de Domínio do Azure AD
 
A capacidade das florestas de recursos no Azure AD Domain Services agora está disponível para o público em geral. Agora você pode habilitar a autorização sem a sincronização de hash de senha para usar Azure AD Domain Services, incluindo a autorização de cartão inteligente. Para saber mais, consulte [conjuntos de réplicas e recursos para Azure Active Directory Domain Services (versão prévia)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>O suporte à réplica regional para os domínios gerenciados do Azure AD DS agora está disponível

**Tipo:** novo recurso   
**Categoria de serviço:** Serviços de Domínio do Azure AD  
** Capacidade do produto: ** Serviços de Domínio do Azure AD
 
Você pode expandir um domínio gerenciado para ter mais de um conjunto de réplicas por locatário do Azure AD. Os conjuntos de réplicas podem ser adicionados a qualquer rede virtual emparelhada em qualquer região do Azure que ofereça suporte a Azure AD Domain Services. Conjuntos de réplicas adicionais em diferentes regiões do Azure fornecem recuperação de desastre geográfica para aplicativos herdados se uma região do Azure fica offline. Para saber mais, consulte [conjuntos de réplicas e recursos para Azure Active Directory Domain Services (versão prévia)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilidade geral do Azure AD minhas entradas

**Tipo:** novo recurso  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Experiências do usuário final
 
Minhas entradas do Azure AD é um novo recurso que permite que os usuários empresariais examinem seu histórico de entrada para verificar se há atividades incomuns. Além disso, esse recurso permite que os usuários finais relatem "isso não foi" ou "isso me foi" em atividades suspeitas. Para saber mais sobre como usar esse recurso, consulte [Exibir e pesquisar sua atividade de entrada recente na página minhas](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)entradas.
 
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

Agora você pode atribuir funções internas do Azure AD a grupos de nuvem com esse novo recurso. Por exemplo, você pode atribuir a função de administrador do SharePoint a Contoso_SharePoint_Admins grupo. Você também pode usar o PIM para tornar o grupo um membro qualificado da função, em vez de conceder acesso à sua posição. Para saber como configurar esse recurso, consulte [usar grupos de nuvem para gerenciar atribuições de função no Azure Active Directory (versão prévia)](../users-groups-roles/roles-groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Função interna do líder de negócios do insights agora disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários na função de líder de negócios do insights podem acessar um conjunto de dashboards e ideias por meio do [aplicativo M365 insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Isso inclui acesso completo a todos os dashboards e a funcionalidade de exploração de dados e informações apresentadas. No entanto, os usuários nessa função não têm acesso às definições de configuração do produto, que é responsabilidade da função de administrador do insights. Para saber mais sobre essa função, consulte [permissões de função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Função interna do administrador do insights agora disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Funções do Azure AD  
**Funcionalidade do produto:** Controle de Acesso
 
Os usuários na função Administrador do insights podem acessar o conjunto completo de recursos administrativos no [aplicativo M365 insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Um usuário nessa função pode ler informações de diretório, monitorar a integridade do serviço, tíquetes de suporte de arquivo e acessar os aspectos das configurações do administrador do insights. Para saber mais sobre essa função, consulte [permissões de função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-administrator)
 
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

Ao criar uma nova política, certifique-se de excluir usuários e contas de serviço que ainda estão usando a autenticação herdada; caso contrário, eles serão bloqueados. [Saiba mais](https://aka.ms/caclientapps).
 
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

Os administradores agora podem ver se uma autenticação do Windows usou o Windows Hello para empresas verificando a guia detalhes de autenticação de um evento de entrada do Windows na folha de entradas do Azure AD no portal do Azure. As autenticações do Windows Hello para empresas incluirão "WindowsHelloForBusiness" no campo método de autenticação. Para obter mais informações sobre como interpretar logs de entrada, consulte a [documentação de logs de entrada](../reports-monitoring/concept-sign-ins.md).
 
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

Para obter mais informações sobre fluxos de usuários, consulte [versões de fluxo de usuário em Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – julho de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em julho de 2020, adicionamos os seguintes 55 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepcionista](https://msteams.peterconnects.com/), [ALOHACLOUD](https://appfusions.alohacloud.com/auth), [Control torre](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [moedas de construção Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GREMINDERS](https://app.greminders.com/o365-oauth), [titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [bloge](../saas-apps/blogin-tutorial.md), [INTSIGHTS](../saas-apps/intsights-tutorial.md), [Kpifire](../saas-apps/kpifire-tutorial.md) [, Textline,](../saas-apps/textline-tutorial.md) [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [comunidade Spark](../saas-apps/community-spark-tutorial.md), [chat](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [controle de nuvem C3M](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan data Hub logon único](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [egresso](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [direito de segurança cibernética Adi](https://right-hand.ai/), Fyde [Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [Lenses.Io](../saas-apps/lensesio-tutorial.md), [momentânea](../saas-apps/momenta-tutorial.md), [uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme bot](https://tellme365liteweb.azurewebsites.net/), [inspirar](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (sistema de gerenciamento escolar)](https://smart-schoolapp.com/frmLoginForm), [Zepto – timecontinueing inteligente](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack logon único](../saas-apps/browserstack-single-sign-on-tutorial.md)

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
 
Agora você pode exibir as atribuições de função em todos os escopos para uma função na guia "funções e administradores" no portal do Azure AD. Você também pode baixar essas atribuições de função para cada função em um arquivo CSV. Para obter orientação sobre como exibir e adicionar atribuições de função, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md).
 
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
 
Alguns aplicativos SAML exigem que SPNameQualifier sejam retornados no assunto da asserção quando solicitado. Agora, o Azure AD responde corretamente quando um SPNameQualifier é solicitado na política NameID da solicitação. Isso também funciona para entrada iniciada pelo SP e a entrada iniciada pelo IdP será a seguinte.  Para saber mais sobre o protocolo SAML no Azure Active Directory, consulte [protocolo SAML de logon único](../develop/single-sign-on-saml-protocol.md).

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

Rótulos de sensibilidade são importantes para proteger seus dados críticos para os negócios e permitem gerenciar grupos em escala, de maneira segura e em conformidade. Para obter orientação sobre como usar rótulos de sensibilidade, consulte [atribuir rótulos de sensibilidade a grupos de Microsoft 365 no Azure Active Directory (versão prévia)](../users-groups-roles/groups-assign-sensitivity-labels.md).
 
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
 
## <a name="may-2020"></a>Maio de 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Desativação de propriedades em APIs entrada, riskyUsers e riskDetections

**Tipo:** plano de alteração  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Atualmente, os tipos enumerados são usados para representar a propriedade riscotype na API riskDetections e riskyUserHistoryItem (em versão prévia). Os tipos enumerados também são usados para a propriedade riskEventTypes na API entrada. No futuro, representaremos essas propriedades como cadeias de caracteres. 

Os clientes devem fazer a transição para a propriedade riskEventType na API beta riskDetections e riskyUserHistoryItem e para riskEventTypes_v2 Propriedade na API beta entrada por 9 de setembro de 2020. Nessa data, iremos desativar as propriedades CurrentType e riskEventTypes atuais. Para obter mais informações, consulte [alterações nas propriedades do evento de risco e APIs de proteção de identidade no Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Substituição da propriedade riskEventTypes na API entrada v 1.0 no Microsoft Graph

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** segurança e proteção da identidade

Os tipos enumerados mudarão para os tipos de cadeia de caracteres ao representar as propriedades do evento de risco no Microsoft Graph de setembro de 2020. Além de impactar as APIs de visualização, essa alteração também afetará a API entrada em produção.

Introduzimos uma nova propriedade riskEventsTypes_v2 (String) para a API entrada v 1.0. Desativaremos a propriedade riskEventTypes (enum) atual em 11 de junho de 2022 de acordo com nossa política de substituição de Microsoft Graph. Os clientes devem fazer a transição para a propriedade riskEventTypes_v2 na API entrada v 1.0 até 11 de junho de 2022. Para obter mais informações, consulte [reprovação da propriedade riskEventTypes na API entrada v 1.0 no Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Alterações futuras nas notificações de email do MFA

**Tipo:** plano de alteração  
**Categoria de serviço:** FATO  
**Funcionalidade do produto:** segurança e proteção da identidade
 

Estamos fazendo as seguintes alterações nas notificações de email para a MFA de nuvem:

As notificações de email serão enviadas do seguinte endereço: azure-noreply@microsoft.com e msonlineservicesteam@microsoftonline.com . Estamos atualizando o conteúdo dos emails de alerta de fraude para indicar melhor as etapas necessárias para desbloquear os usos.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nova inscrição de autoatendimento para usuários em domínios federados que não podem acessar o Microsoft Teams porque eles não são sincronizados com o Azure Active Directory.

**Tipo:** plano de alteração  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 

Atualmente, os usuários que estão em domínios federados no Azure AD, mas que não estão sincronizados com o locatário, não podem acessar as equipes. A partir do final de junho, essa nova funcionalidade permitirá que ele faça isso estendendo o recurso de inscrição verificada por email existente. Isso permitirá que os usuários que podem entrar em um IdP federado, mas que ainda não tenham um objeto de usuário na ID do Azure, tenham um objeto de usuário criado automaticamente e sejam autenticados para as equipes. Seu objeto de usuário será marcado como "inscrição de autoatendimento". Essa é uma extensão da capacidade existente de enviar por email a autoinscrição verificada que os usuários em domínios gerenciados podem fazer e podem ser controlados usando o mesmo sinalizador. Essa alteração concluirá a distribuição durante os dois meses a seguir. Veja as atualizações de documentação [aqui](../users-groups-roles/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Próxima correção: o documento de descoberta do OIDC para a nuvem do Azure governamental está sendo atualizado para fazer referência aos pontos de extremidade corretos do grafo.

**Tipo:** plano de alteração  
**Categoria de serviço:** nuvens soberanas  
**Funcionalidade do produto:** Autenticação do usuário
 
A partir de junho, o documento de descoberta do OIDC da [plataforma Microsoft Identity e o protocolo OpenID Connect](../develop/v2-protocols-oidc.md) no ponto de extremidade de [nuvem do Azure governamental](../develop/authentication-national-cloud.md) (login.microsoftonline.us) começarão a retornar o ponto de extremidade correto do [grafo de nuvem nacional](/graph/deployments) ( https://graph.microsoft.us ou https://dod-graph.microsoft.us) , com base no locatário fornecido.  Atualmente, ele fornece o campo de ponto de extremidade de grafo incorreto (graph.microsoft.com) "msgraph_host".  

Essa correção de bug será distribuída gradualmente por aproximadamente 2 meses.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Os usuários do Azure governamental não poderão mais entrar no login.microsoftonline.com

**Tipo:** Planejar alteração  
**Categoria de serviço:** nuvens soberanas  
**Funcionalidade do produto:** Autenticação do usuário
 
Em 1 de junho de 2018, a autoridade oficial do Azure Active Directory (Azure AD) para o Azure governamental mudou de https://login-us.microsoftonline.com para https://login.microsoftonline.us . Se você possui um aplicativo em um locatário do Azure governamental, você deve atualizar seu aplicativo para conectar os usuários no ponto de extremidade. EUA.

A partir de 1º de maio, o Azure AD começará a impor a alteração do ponto de extremidade, bloqueando os usuários do Azure governamental de entrar em aplicativos hospedados nos locatários do Azure governamental usando o ponto de extremidade público (microsoftonline.com). Os aplicativos impactados começarão a ver um erro AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Haverá uma distribuição gradual dessa alteração com a imposição prevista para ser concluída em todos os aplicativos de junho de 2020. Para obter mais detalhes, consulte a [postagem no blog do Azure governamental](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>A solicitação de logoff único do SAML agora envia NameID no formato correto

**Tipo:** corrigido  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Quando um usuário clica em sair (por exemplo, no portal myapps), o Azure AD envia uma mensagem de logoff único do SAML para cada aplicativo que está ativo na sessão do usuário e tem uma URL de logout configurada. Essas mensagens contêm uma NameID em um formato persistente.

Se o token de entrada SAML original usou um formato diferente para NameID (por exemplo, email/UPN), o aplicativo SAML não poderá correlacionar a NameID na mensagem de logout a uma sessão existente (já que o NameIDs usado em ambas as mensagens é diferente), o que fez com que a mensagem de logout seja descartada pelo aplicativo SAML e o usuário permaneça conectado. Essa correção torna a mensagem de saída consistente com a NameID configurada para o aplicativo.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>A função de administrador de identidade híbrida agora está disponível com provisionamento de nuvem

**Tipo:** novo recurso  
**Categoria de serviço:** Provisionamento de nuvem do Azure AD  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Os administradores de ti podem começar a usar a nova função "administrador híbrido" como a função menos privilegiada para configurar o provisionamento de nuvem do Azure ADConnect. Com essa nova função, você não precisa mais usar a função de administrador global para configurar e configurar o provisionamento de nuvem. [Saiba mais](../users-groups-roles/roles-delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Novos aplicativos federados disponíveis na Galeria de aplicativos do Azure AD – maio de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em maio de 2020, adicionamos os seguintes 36 novos aplicativos em nossa galeria de aplicativos com suporte à Federação:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-engrenado](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), [meu IBISWorld](https://identity.imagesoftinc.com/), [adequação](../saas-apps/insuite-tutorial.md), [Gerenciamento de processo de alteração](../saas-apps/change-process-management-tutorial.md), plataforma de garantia do [Cyara CX](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [governança inteligente](../saas-apps/smart-global-governance-tutorial.md) [,,](../saas-apps/prezi-tutorial.md) [Mapbox](../saas-apps/mapbox-tutorial.md), [datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [estranho](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO para Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO para BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO para bambu](../saas-apps/easysso-for-bamboo-tutorial.md), [torii](../saas-apps/torii-tutorial.md), [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md), [humanment](../saas-apps/humanage-tutorial.md), ColorTokens [ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [cch Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [superexpander](../saas-apps/textexpander-tutorial.md), [todos os CRM](../saas-apps/anyone-home-crm-tutorial.md), [askSpoke](../saas-apps/askspoke-tutorial.md), [centro de contato Ice](../saas-apps/ice-contact-center-tutorial.md)

Você também pode encontrar a documentação de todos os aplicativos aqui https://aka.ms/AppsTutorial .

Para listar seu aplicativo na Galeria de aplicativos do Azure AD, leia os detalhes aqui https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo somente de relatório para acesso condicional agora está disponível para o público geral

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

O [modo somente de relatório para acesso condicional do Azure ad](../conditional-access/concept-conditional-access-report-only.md) permite avaliar o resultado de uma política sem impor controles de acesso. Você pode testar políticas somente de relatório em sua organização e entender seu impacto antes de habilitá-las, tornando a implantação mais segura e fácil. Nos últimos meses, vimos uma adoção forte do modo somente de relatório – em 26M, os usuários já estão no escopo de uma política somente de relatório. Com o comunicado hoje, as novas políticas de acesso condicional do Azure AD serão criadas no modo somente de relatório por padrão. Isso significa que você pode monitorar o impacto de suas políticas desde o momento em que elas são criadas. E para aqueles que usam as APIs do MS Graph, você também pode [gerenciar políticas somente de relatório de forma programática](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Inscrição de autoatendimento para usuários convidados

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Com identidades externas no Azure AD, você pode permitir que as pessoas fora da sua organização acessem seus aplicativos e recursos enquanto permitem que eles entrem usando qualquer identidade que preferir. Quando compartilhar um aplicativo com usuários externos, é possível que você não saiba com antecedência quem precisará de acesso a um aplicativo. Com a [inscrição de autoatendimento](../external-identities/self-service-sign-up-overview.md), você pode habilitar usuários convidados para inscrever-se e obter uma conta de convidado para seus aplicativos de linha de negócios (LOB). O fluxo de inscrição pode ser criado e personalizado para dar suporte a identidades sociais e do Azure AD. Você também pode coletar informações adicionais sobre o usuário durante a inscrição.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>As informações de acesso condicional e a pasta de trabalho de relatórios estão geralmente disponíveis

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

A [pasta de trabalho insights e Reporting](../conditional-access/howto-conditional-access-insights-reporting.md) fornece aos administradores uma exibição resumida do acesso condicional do Azure AD em seu locatário. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorar as alterações em tempo real. A pasta de trabalho transmite os dados armazenados no Azure Monitor, que você pode configurar em alguns minutos [seguindo estas instruções](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Para tornar o painel mais detectável, nós o movemos para a guia novas informações e relatórios no menu de acesso condicional do Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A folha detalhes da política para acesso condicional está em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** segurança e proteção da identidade

A [folha detalhes](../conditional-access/troubleshoot-conditional-access.md) da nova política exibe as atribuições, as condições e os controles atendidos durante a avaliação da política de acesso condicional. Você pode acessar a folha selecionando uma linha nas guias acesso condicional ou somente relatório dos detalhes de entrada.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Novos recursos de consulta para objetos de diretório no Microsoft Graph estão em visualização pública

**Tipo:** novo recurso  
**Categoria de serviço:** Funcionalidade do **produto** MS Graph: experiência do desenvolvedor

Novos recursos estão sendo introduzidos para APIs de objetos do Microsoft Graph Directory, habilitando as operações de contagem, pesquisa, filtro e classificação. Isso dará aos desenvolvedores a capacidade de consultar rapidamente nossos objetos de diretório sem soluções alternativas como filtragem e classificação na memória. Saiba mais nesta [postagem no blog](https://aka.ms/CountFilterMSGraphAAD).

Estamos atualmente em visualização pública, procurando comentários. Envie seus comentários com esta [breve pesquisa](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Configurar o logon único baseado em SAML usando a API do Microsoft Graph (beta)

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
O suporte para criar e configurar um aplicativo da galeria do Azure AD usando APIs do MS Graph no beta agora está disponível. Se você precisar configurar o logon único baseado em SAML para várias instâncias de um aplicativo, Economize tempo usando as APIs de Microsoft Graph para [automatizar a configuração do logon único baseado em SAML](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Novos conectores de provisionamento na Galeria de aplicativos do Azure AD – maio de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** integração de terceiros
 
Agora você pode automatizar a criação, a atualização e a exclusão de contas de usuário para esses aplicativos integrados recentemente:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic por Organização](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Console da Infraestrutura de Nuvem da Oracle](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Para obter mais informações para proteger melhor sua organização com o provisionamento automatizado de contas de usuário, consulte [Automatizar o provisionamento de usuário para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>A criptografia de token SAML está geralmente disponível

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
A [criptografia de token SAML](../manage-apps/howto-saml-token-encryption.md) permite que os aplicativos sejam configurados para receber asserções SAML criptografadas. Agora, o recurso está disponível em todas as nuvens.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>As declarações de nome de grupo em tokens de aplicativo estão geralmente disponíveis

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO
 
As declarações de grupo emitidas em um token agora podem ser limitadas apenas aos grupos atribuídos ao aplicativo.  Isso é especialmente importante quando os usuários são membros de um grande número de grupos e houve um risco de exceder os limites de tamanho de token. Com esse novo recurso em vigor, a capacidade de [Adicionar nomes de grupo a tokens](../hybrid/how-to-connect-fed-group-claims.md) está disponível para o público geral.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>O Write-back do workday agora dá suporte à configuração de atributos de número de

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Aprimoramos o aplicativo de provisionamento do workday write-back para agora dar suporte ao Write-back dos atributos número de telefone comercial e número móvel. Além de email e nome de usuário, agora você pode configurar o aplicativo de provisionamento de write-back do WORKDAY para transmitir valores de número de telefone do Azure AD para o workday. Para obter mais detalhes sobre como configurar o Write-back de número de telefone, consulte o tutorial do aplicativo de [write-back do workday](https://aka.ms/WorkdayWriteback) . 

---

### <a name="publisher-verification-preview"></a>Verificação do Publicador (versão prévia)

**Tipo:** novo recurso  
**Categoria de serviço:** outro  
**Funcionalidade do produto:** experiência de desenvolvedor
 
A verificação do editor (versão prévia) ajuda administradores e usuários finais a entender a autenticidade dos desenvolvedores de aplicativos que se integram com a plataforma de identidade da Microsoft. Para obter detalhes, consulte [verificação do Publicador (versão prévia)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Fluxo de código de autorização para aplicativos de página única

**Tipo:** Categoria do **serviço** de recurso alterado: **funcionalidade do produto** de autenticação: experiência do desenvolvedor

Devido às restrições de cookie de terceiros do navegador moderno, [como o Safari ITP](../develop/reference-third-party-cookies-spas.md), o spas terá que usar o fluxo do código de autorização em vez do fluxo implícito para manter o SSO; MSAL.js v 2. x agora dará suporte ao fluxo do código de autorização. Como as atualizações correspondentes para o portal do Azure, você pode atualizar seu SPA para que ele seja do tipo "Spa" e usar o fluxo de código de autenticação. Para obter diretrizes, consulte [início rápido: conectar usuários e obter um token de acesso em um JavaScript Spa usando o fluxo de código de autenticação](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>A filtragem aprimorada para dispositivos está em visualização pública

**Tipo:** Recurso alterado   
**Categoria de serviço:** Funcionalidade do **produto** de gerenciamento de dispositivos: gerenciamento de ciclo de vida do dispositivo
 
Anteriormente, os únicos filtros que você poderia usar eram "Enabled" e "data da atividade". Agora, você pode [filtrar a lista de dispositivos em mais propriedades](../devices/device-management-azure-portal.md#device-list-filtering-preview), incluindo tipo de sistema operacional, tipo de junção, conformidade e muito mais. Essas adições devem simplificar a localização de um dispositivo específico.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>A nova experiência de Registros de aplicativo para Azure AD B2C agora está disponível para o público geral

**Tipo:** Recurso alterado   
**Categoria de serviço:** B2C - gerenciamento de identidades de consumidor  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
A nova experiência de Registros de aplicativo para Azure AD B2C agora está disponível para o público em geral. 

Anteriormente, era necessário gerenciar seus aplicativos voltados ao consumidor B2C separadamente do restante dos seus aplicativos usando a experiência de "aplicativos" herdada. Isso significava experiências de criação de aplicativo diferentes em locais diferentes no Azure.

A nova experiência mostra todos os registros de aplicativo do B2C e registros de aplicativo do Azure AD em um único local e fornece uma maneira consistente de gerenciá-los. Independentemente de você precisar gerenciar um aplicativo voltado para o cliente ou um aplicativo que tenha acesso ao Microsoft Graph para gerenciar programaticamente Azure AD B2C recursos, você só precisa aprender uma maneira de fazer coisas.

Você pode acessar a nova experiência navegando pelo serviço Azure AD B2C e selecionando a folha Registros de aplicativo. A experiência também pode ser acessada pelo serviço de Azure Active Directory.

A experiência de Registros de aplicativo Azure AD B2C é baseada na experiência geral de [registro de aplicativo](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) para locatários do Azure AD, mas é adaptada para Azure ad B2C. A experiência de "aplicativos" herdada será preterida no futuro.

Para obter mais informações, visite [a nova experiência de registro de aplicativo para Azure ad B2C](https://aka.ms/b2cappregtraining).

---

## <a name="april-2020"></a>Abril de 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>A experiência de registro de informações de segurança combinada já está disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** autenticações (logons)

**Funcionalidade do produto:** segurança e proteção da identidade

A experiência de registro combinada para MFA (autenticação multifator) e redefinição de senha de autoatendimento (SSPR) agora está disponível para o público em geral. Essa nova experiência de registro permite que os usuários se registrem para MFA e SSPR em um único processo passo a passo. Quando você implanta a nova experiência para sua organização, os usuários podem se registrar em menos tempo e com menos complicações. Confira a postagem do blog [aqui](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Avaliação de acesso contínuo

**Tipo:** novo recurso

**Categoria de serviço:** autenticações (logons)

**Funcionalidade do produto:** segurança e proteção da identidade

A avaliação de acesso contínuo é um novo recurso de segurança que permite a imposição quase em tempo real de políticas em partes confiáveis que consomem tokens de acesso do Azure AD quando ocorrem eventos no Azure AD (como a exclusão da conta de usuário). Estamos lançando esse recurso primeiro para equipes e clientes do Outlook. Para obter mais detalhes, leia nosso [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e  [documentação](./concept-fundamentals-continuous-access-evaluation.md).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Entrada do SMS: os trabalhos de início podem entrar em aplicativos com suporte do Azure AD com seu número de telefone e nenhuma senha

**Tipo:** novo recurso

**Categoria de serviço:** autenticações (logons)

**Funcionalidade do produto:** Autenticação do usuário

O Office está lançando uma série de aplicativos de negócios para dispositivos móveis que atendem a organizações não tradicionais e para funcionários em grandes organizações que não usam email como seu método de comunicação principal. Esses aplicativos visam funcionários frentes, trabalhadores sem mesa, agentes de campo ou funcionários de varejo que podem não receber um endereço de email de seu empregador, ter acesso a um computador ou a ele. Esse projeto permitirá que esses funcionários entrem em aplicativos de negócios inserindo um número de telefone e roundtripping um código. Para obter mais detalhes, consulte nossa [documentação de administração](../authentication/howto-authentication-sms-signin.md) e [documentação do usuário final](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Convidar usuários internos para usar a colaboração B2B

**Tipo:** novo recurso

**Categoria de serviço:** B2B

**Funcionalidade do produto:**

Estamos expandindo o recurso de convite B2B para permitir que contas internas existentes sejam convidadas para usar as credenciais de colaboração B2B no futuro. Isso é feito passando o objeto de usuário para a API de convite, além de parâmetros típicos, como o endereço de email convidado. A ID de objeto do usuário, o UPN, a associação de grupo, a atribuição de aplicativo etc. permanecem intactos, mas continuará a usar o B2B para autenticar com suas credenciais de locatário inicial, em vez das credenciais internas usadas antes do convite. Para obter detalhes, consulte a [documentação](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo somente de relatório para acesso condicional agora está disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** Acesso condicional

**Funcionalidade do produto:** segurança e proteção da identidade

O [modo somente de relatório para acesso condicional do Azure ad](../conditional-access/concept-conditional-access-report-only.md) permite avaliar o resultado de uma política sem impor controles de acesso. Você pode testar políticas somente de relatório em sua organização e entender seu impacto antes de habilitá-las, tornando a implantação mais segura e fácil. Nos últimos meses, vimos uma adoção forte do modo somente de relatório, com 26M usuários já no escopo de uma política somente de relatório. Com este anúncio, as novas políticas de acesso condicional do Azure AD serão criadas no modo somente de relatório por padrão. Isso significa que você pode monitorar o impacto de suas políticas desde o momento em que elas são criadas. E para aqueles que usam as APIs do MS Graph, você também pode [gerenciar políticas somente de relatório programaticamente](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>As informações de acesso condicional e a pasta de trabalho de relatórios estão geralmente disponíveis

**Tipo:** novo recurso

**Categoria de serviço:** Acesso condicional

**Funcionalidade do produto:** segurança e proteção da identidade

A pasta de [trabalho informações](../conditional-access/howto-conditional-access-insights-reporting.md) de acesso condicional e relatórios fornece aos administradores uma exibição resumida do acesso condicional do Azure AD em seu locatário. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorar as alterações em tempo real. A pasta de trabalho transmite os dados armazenados no Azure Monitor, que você pode configurar em alguns minutos [seguindo estas instruções](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Para tornar o painel mais detectável, nós o movemos para a guia novas informações e relatórios no menu de acesso condicional do Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A folha detalhes da política para acesso condicional está em visualização pública

**Tipo:** novo recurso

**Categoria de serviço:** Acesso condicional

**Funcionalidade do produto:** segurança e proteção da identidade

A [folha detalhes](../conditional-access/troubleshoot-conditional-access.md) da nova política exibe quais atribuições, condições e controles foram atendidos durante a avaliação da política de acesso condicional. Você pode acessar a folha selecionando uma linha nas guias **acesso condicional** ou **somente relatório** dos detalhes de entrada.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – abril de 2020

**Tipo:** novo recurso

**Categoria de serviço:** Aplicativos empresariais

**Funcionalidade do produto:** integração de terceiros

Em abril de 2020, adicionamos esses 31 novos aplicativos com suporte à Federação para a Galeria de aplicativos: 

[Aplicativos SincroPool](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT de compras](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO para Jira](../saas-apps/easysso-for-jira-tutorial.md), [virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 mover](https://app.mover.io/login), [palestrante](https://speakerengage.com/login.php), [honestamente](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 People](../saas-apps/gr8-people-tutorial.md), [pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [harmonia](../saas-apps/harmony-tutorial.md), [timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet Click](../saas-apps/synchronet-click-tutorial.md), [capacitação](https://www.made-in-office.com/en/), [Forts](../saas-apps/fortes-change-cloud-tutorial.md), decisivo [, GroupTalk](../saas-apps/litmus-tutorial.md) [,](https://recorder.grouptalk.com/) [dataFrontify](../saas-apps/frontify-tutorial.md), [nuvem do MongoDB](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md), [coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md) , TMWS [(Trend Micro Web Security)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Suporte de consulta Delta Microsoft Graph para oAuth2PermissionGrant disponível para visualização pública

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

A consulta Delta para oAuth2PermissionGrant está disponível para visualização pública! Agora você pode controlar as alterações sem precisar sondar continuamente Microsoft Graph. [Saiba mais.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Suporte de consulta Delta Microsoft Graph para contato organizacional disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

A consulta Delta para contatos organizacionais está geralmente disponível! Agora você pode controlar as alterações nos aplicativos de produção sem precisar sondar continuamente Microsoft Graph. Substitua qualquer código existente que sonda continuamente dados de orgContact por consulta Delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Suporte de consulta Delta Microsoft Graph para o aplicativo disponível para o público geral

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

A consulta Delta para aplicativos está geralmente disponível! Agora você pode controlar as alterações nos aplicativos de produção sem precisar sondar continuamente Microsoft Graph. Substitua qualquer código existente que sonda continuamente os dados do aplicativo por consulta Delta para melhorar significativamente o desempenho. [Saiba mais.](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Suporte de consulta Delta Microsoft Graph para unidades administrativas disponíveis para visualização pública

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** A consulta Delta de experiência do desenvolvedor para unidades administrativas está disponível para visualização pública! Agora você pode controlar as alterações sem precisar sondar continuamente Microsoft Graph. [Saiba mais.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gerenciar números de telefone de autenticação e mais em novas APIs do Microsoft Graph beta

**Tipo:** novo recurso

**Categoria de serviço:** MS Graph

**Funcionalidade do produto:** experiência de desenvolvedor

Essas APIs são uma ferramenta importante para gerenciar os métodos de autenticação dos seus usuários. Agora, você pode pré-configurar e gerenciar programaticamente os autenticadores usados para MFA e redefinição de senha de autoatendimento (SSPR). Esse é um dos recursos mais solicitados nos espaços do Azure MFA, SSPR e Microsoft Graph. As novas APIs que lançamos nesta onda oferecem a você a capacidade de:

- Ler, adicionar, atualizar e remover telefones de autenticação de um usuário
- Redefinir a senha de um usuário
- Ligar e desligar o SMS-Sign-in

Para obter mais informações, consulte [visão geral da API dos métodos de autenticação do Azure ad](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Visualização pública de unidades administrativas

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

Unidades administrativas permitem que você conceda permissões de administrador restritas a um departamento, uma região ou outro segmento de sua organização que você define. Você pode usar unidades administrativas para delegar permissões a administradores regionais ou definir uma política em um nível granular. Por exemplo, um administrador de conta de usuário poderia atualizar as informações do perfil, redefinir senhas e atribuir licenças para usuários apenas na unidade administrativa dele.

Usando unidades administrativas, um administrador central poderia:

- Criar uma unidade administrativa para o gerenciamento descentralizado de recursos
- Atribuir uma função com permissões administrativas somente para usuários do Azure AD em uma unidade administrativa
- Popular as unidades administrativas com usuários e grupos conforme necessário

Para obter mais informações, consulte [Gerenciamento de unidades administrativas em Azure Active Directory (versão prévia)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Funções internas de administrador de impressora e técnico de impressora

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

**Administrador de impressora**: os usuários com essa função podem registrar impressoras e gerenciar todos os aspectos de todas as configurações de impressora na solução de impressão universal da Microsoft, incluindo as configurações do conector de impressão universal. Eles podem consentir com todas as solicitações de permissão de impressão delegadas. Os Administradores de impressora também têm acesso aos relatórios de impressão. 

**Técnico de impressora**: os usuários com essa função podem registrar impressoras e gerenciar o status da impressora na solução de impressão universal da Microsoft. Eles também podem ler todas as informações do conector. As principais tarefas que um técnico de impressora não pode fazer são definir permissões de usuário em impressoras e compartilhar impressoras. [Saiba mais.](../users-groups-roles/directory-assign-admin-roles.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Função interna do administrador de identidade híbrida

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

Os usuários com essa função podem habilitar, definir e gerenciar serviços e configurações relacionados à habilitação da identidade híbrida no Azure AD. Essa função concede a capacidade de configurar o Azure AD para um dos três métodos de autenticação com suporte&#8212;sincronização de hash de senha (PHS), autenticação de passagem (PTA) ou Federação (AD FS ou provedor de Federação de terceiros) &#8212;e implantar a infraestrutura local relacionada para habilitá-los. A infraestrutura local inclui agentes de provisionamento e PTA. Essa função concede a capacidade de habilitar o S-SSO (Logon único contínuo) para habilitar a autenticação direta em dispositivos que não usem o Windows 10 ou em computadores que não usem o Windows Server 2016. Além disso, essa função concede a capacidade de ver os logs de entrada e acessar a integridade e a análise para fins de monitoramento e solução de problemas. [Saiba mais.](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Função interna de administrador de rede

**Tipo:** novo recurso

**Categoria de serviço:** Funções do Azure AD

**Funcionalidade do produto:** Controle de Acesso

Os usuários com essa função podem revisar as recomendações de arquitetura de perímetro de rede da Microsoft que se baseiam na telemetria de rede de seus locais de usuário. O desempenho de rede para Microsoft 365 conta com uma arquitetura de perímetro de rede de cliente de negócios cuidadosa, que geralmente é específica ao local do usuário. Essa função permite a edição de locais de usuários descobertos e a configuração de parâmetros de rede desses locais para facilitar medidas de telemetria e recomendações de design melhores. [Saiba mais.](../users-groups-roles/directory-assign-admin-roles.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Atividade em massa e downloads na experiência do portal de administração do Azure AD

**Tipo:** novo recurso

**Categoria de serviço:** Gerenciamento de usuários

**Funcionalidade do produto:** diretório

Agora você pode executar atividades em massa em usuários e grupos no Azure AD carregando um arquivo CSV na experiência do portal de administração do Azure AD. Você pode criar usuários, excluir usuários e convidar usuários convidados. E você pode adicionar e remover membros de um grupo.

Você também pode baixar listas de recursos do Azure AD da experiência do portal de administração do Azure AD. Você pode baixar a lista de usuários no diretório, a lista de grupos no diretório e os membros de um grupo específico.

Para obter mais informações, confira o seguinte:

- [Criar usuários](../users-groups-roles/users-bulk-add.md) ou [convidar usuários convidados](../external-identities/tutorial-bulk-invite.md)
- [Excluir usuários](../users-groups-roles/users-bulk-delete.md) ou [restaurar usuários excluídos](../users-groups-roles/users-bulk-restore.md)
- [Baixar lista de usuários](../users-groups-roles/users-bulk-download.md) ou [baixar lista de grupos](../users-groups-roles/groups-bulk-download.md)
- [Adicionar (importar) Membros](../users-groups-roles/groups-bulk-import-members.md) ou [remover membros](../users-groups-roles/groups-bulk-remove-members.md) ou [baixar a lista de membros](../users-groups-roles/groups-bulk-download-members.md) de um grupo

---

### <a name="my-staff-delegated-user-management"></a>Minha equipe delegou o gerenciamento de usuários

**Tipo:** novo recurso

**Categoria de serviço:** Gerenciamento de usuários

**Funcionalidade do produto:**

Minha equipe permite que os gerentes de primeira a, como um gerente de loja, verifiquem se os membros da equipe podem acessar suas contas do Azure AD. Em vez de depender de um helpdesk central, as organizações podem delegar tarefas comuns, como redefinição de senhas ou alteração de números de telefone, para um gerente de primeira linha. Com a minha equipe, um usuário que não consegue acessar sua conta pode obter acesso novamente em apenas alguns cliques, sem necessidade de assistência técnica ou equipe de ti. Para obter mais informações, consulte [gerenciar seus usuários com minha equipe (versão prévia)](https://aka.ms/MyStaffAdminDocs) e [delegar o gerenciamento de usuários com minha equipe (versão prévia)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Uma experiência do usuário final atualizada nas revisões de acesso

**Tipo:** recurso alterado

**Categoria de serviço:** Revisões de acesso

**Funcionalidade do produto:** Governança de identidade

Atualizamos a experiência do revisor para as revisões de acesso do Azure AD no portal meus aplicativos. No final de abril, os revisores que estiverem conectados à experiência de revisor de revisões de acesso do Azure AD verão uma faixa que permitirá que eles experimentem a experiência atualizada no meu acesso. Observe que a experiência de revisões de acesso atualizada oferece a mesma funcionalidade que a experiência atual, mas com uma interface de usuário aprimorada sobre novos recursos para permitir que os usuários sejam produtivos. [Você pode aprender mais sobre a experiência atualizada aqui](../governance/perform-access-review.md). Essa visualização pública durará até o final de julho de 2020. No final de julho, os revisores que não tiverem optado pela experiência de visualização serão automaticamente direcionados para o meu acesso para realizar revisões de acesso. Se você quiser que os revisores alternem permanentemente para a experiência de visualização em meu acesso agora, [faça uma solicitação aqui](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>O provisionamento e os aplicativos de write-back do usuário de entrada do workday agora dão suporte às versões mais recentes da API de serviços Web do workday

**Tipo:** recurso alterado

**Categoria de serviço:** provisionamento de aplicativos

**Funcionalidade do produto:** 

Com base nos comentários dos clientes, atualizamos agora os aplicativos de write-back e de provisionamento do usuário de entrada do workday na Galeria de aplicativos empresariais para dar suporte às versões mais recentes da API do WWS (workday Web Services). Com essa alteração, os clientes podem especificar a versão da API do WWS que gostaria de usar na cadeia de conexão. Isso oferece aos clientes a capacidade de recuperar mais atributos de RH disponíveis nas versões do workday. O aplicativo de write-back do workday agora usa o serviço Web Change_Work_Contact_Info workday recomendado para superar as limitações do Maintain_Contact_Info.

Se nenhuma versão for especificada na cadeia de conexão, por padrão, os aplicativos de provisionamento de entrada do workday continuarão a usar o WWS v 21.1 para alternar para as APIs mais recentes do WORKDAY para o provisionamento de usuário de entrada, os clientes precisarão atualizar a cadeia de conexão conforme documentado [no tutorial](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) e também atualizar os XPaths usados para os atributos workday, conforme documentado no [Guia de referência](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)de 

Para usar a nova API para Write-back, não há nenhuma alteração necessária no aplicativo de provisionamento de write-back do workday. No lado do workday, verifique se a conta de usuário do sistema de integração do workday (ISU) tem permissões para invocar o processo comercial Change_Work_Contact conforme documentado na seção tutorial, [configurar permissões de política de segurança de processo de negócios](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

Atualizamos nosso [Guia de tutorial](../saas-apps/workday-inbound-tutorial.md) para refletir o novo suporte à versão de API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Os usuários com função de acesso padrão agora estão no escopo para provisionamento

**Tipo:** recurso alterado

**Categoria de serviço:** provisionamento de aplicativos

**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Historicamente, os usuários com a função de acesso padrão estão fora do escopo para provisionamento. Ouvimos comentários de que os clientes querem que os usuários com essa função estejam no escopo para provisionamento. A partir de 16 de abril de 2020, todas as novas configurações de provisionamento permitem que os usuários com a função de acesso padrão sejam provisionados. Gradativamente, alteraremos o comportamento das configurações de provisionamento existentes para dar suporte ao provisionamento de usuários com essa função. [Saiba mais.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Interface do usuário de provisionamento atualizada

**Tipo:** recurso alterado

**Categoria de serviço:** provisionamento de aplicativos

**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade

Atualizamos nossa experiência de provisionamento para criar uma exibição de gerenciamento mais focalizada. Ao navegar até a folha de provisionamento de um aplicativo empresarial que já foi configurado, você poderá monitorar facilmente o progresso do provisionamento e do gerenciamento de ações, como iniciar, parar e reiniciar o provisionamento.... [Saiba mais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A validação de regra de grupo dinâmico agora está disponível para visualização pública

**Tipo:** recurso alterado

**Categoria de serviço:** gerenciamento de grupo

**Recurso de produto:** colaboração

O Azure Active Directory (Azure AD) agora fornece os meios para validar regras de grupo dinâmicos. Na guia **validar regras** , você pode validar sua regra dinâmica em relação aos membros do grupo de exemplo para confirmar se a regra está funcionando conforme o esperado. Ao criar ou atualizar regras de grupo dinâmicos, os administradores desejam saber se um usuário ou um dispositivo será membro do grupo. Isso ajuda a avaliar se um usuário ou dispositivo atende aos critérios de regra e auxilia na solução de problemas quando a associação não é esperada.

Para obter mais informações, consulte [validar uma regra de associação de grupo dinâmico (versão prévia)](../users-groups-roles/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Pontuação segura de identidade-padrões de segurança e atualizações de ação de melhoria de MFA

**Tipo:** recurso alterado

**Categoria de serviço:** N/A

**Funcionalidade do produto:** segurança e proteção da identidade

**Suporte a padrões de segurança para ações de aperfeiçoamento do Azure AD:** A pontuação segura da Microsoft atualizará as ações de aperfeiçoamento para dar suporte aos [padrões de segurança no Azure ad](./concept-fundamentals-security-defaults.md), o que facilita ajudar a proteger sua organização com configurações de segurança pré-configuradas para ataques comuns. Isso afetará as seguintes ações de aprimoramento:

- Verifique se todos os usuários podem concluir a autenticação multifator para acesso seguro
- Exigir MFA para funções administrativas
- Habilitar política para bloquear autenticação herdada
 
**Atualizações de ação de melhoria de MFA:** Para refletir a necessidade de que as empresas assegurem a maior parte da segurança ao aplicar políticas que funcionam com seus negócios, a pontuação segura da Microsoft removeu três ações de melhoria centralizadas em relação à autenticação multifator e adicionadas duas.

Ações de aperfeiçoamento removidas:

- Registrar todos os usuários para autenticação multifator
- Exigir MFA para todos os usuários
- Exigir MFA para funções privilegiadas do Azure AD

Ações de aprimoramento adicionadas:

- Verifique se todos os usuários podem concluir a autenticação multifator para acesso seguro
- Exigir MFA para funções administrativas

Essas novas ações de aprimoramento exigem o registro de seus usuários ou administradores para a MFA (autenticação multifator) em seu diretório e o estabelecimento do conjunto certo de políticas que atenda às suas necessidades organizacionais. O principal objetivo é ter flexibilidade ao garantir que todos os usuários e administradores possam autenticar com vários fatores ou prompts de verificação de identidade com base em risco. Isso pode assumir a forma de ter várias políticas que aplicam decisões com escopo definido ou definir padrões de segurança (a partir de 16 de março) que permitem que a Microsoft decida quando desafiar os usuários para MFA. [Leia mais sobre o que há de novo na pontuação segura da Microsoft](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Contas de Azure Active Directory não gerenciadas na atualização B2B para março de 2021

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
A **partir de 31 de março de 2021**, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários não gerenciados do Azure Active Directory (AD do Azure) para cenários de colaboração B2B. Na preparação para isso, incentivamos você a aceitar o [email de autenticação de senha de uso único](../external-identities/one-time-passcode.md).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Os usuários com a função de acesso padrão estarão no escopo para provisionamento

**Tipo:** plano de alteração  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
Historicamente, os usuários com a função de acesso padrão estão fora do escopo para provisionamento. Ouvimos comentários de que os clientes querem que os usuários com essa função estejam no escopo para provisionamento. Estamos trabalhando na implantação de uma alteração para que todas as novas configurações de provisionamento permitam que os usuários com a função de acesso padrão sejam provisionados. Gradualmente, alteraremos o comportamento das configurações de provisionamento existentes para dar suporte ao provisionamento de usuários com essa função. Nenhuma ação do cliente é necessária. Publicaremos uma atualização em nossa [documentação](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) quando essa alteração estiver em vigor.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>A colaboração B2B do Azure AD estará disponível no Microsoft Azure operado por locatários da 21Vianet (Azure China 21Vianet)

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de colaboração B2B do Azure AD serão disponibilizados no Microsoft Azure operado por locatários da 21Vianet (Azure China 21Vianet), permitindo que os usuários em um locatário do Azure China 21Vianet colaborem de forma integrada com os usuários em outros locatários da 21Vianet da China do Azure. [Saiba mais sobre a colaboração B2B do Azure ad](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Redesignação de email de convite de colaboração B2B do Azure AD

**Tipo:** plano de alteração  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os [emails](../external-identities/invitation-email-elements.md) enviados pelo serviço de convite de colaboração B2B do Azure ad para convidar usuários para o diretório serão reprojetados para tornar mais claras as informações de convite e as próximas etapas do usuário.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>As alterações de política de HomeRealmDiscovery aparecerão nos logs de auditoria

**Tipo:** corrigido  
**Categoria de serviço:** Submeti  
**Funcionalidade do produto:** monitoramento e relatórios
 
Corrigimos um bug em que as alterações na [política HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) não foram incluídas nos logs de auditoria. Agora você poderá ver quando e como a política foi alterada e por quem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Novos aplicativos federados disponíveis na Galeria de Aplicativo Azure AD – março de 2020

**Tipo:** novo recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em março de 2020, adicionamos esses 51 novos aplicativos com suporte de Federação à galeria de aplicativos: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co aplicativo SAML](../saas-apps/profitco-saml-app-tutorial.md), [provedor de serviços iPoint](../saas-apps/ipoint-service-provider-tutorial.md), [esfera Contexxt.ai](https://contexxt-sphere.com/login), [sabedoria por Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), inscrições digitais do [FLARE](https://spark-dev.pixelnebula.com/login), [LOGZ.Io-observação de capacidade de nuvem para engenheiros](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [ESPECTROu](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://bizzcontact.app/), [Elqano SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision compr](../saas-apps/netvision-compas-tutorial.md), Hub de [FCM](../saas-apps/fcm-hub-tutorial.md) [, RIB](https://www.devfinition.com/) [A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks](https://demo.asterapp.io/login) [,](../saas-apps/golinks-tutorial.md)Datadog [,](../saas-apps/datadog-tutorial.md)portal de usuário [B2B,](../saas-apps/zscaler-b2b-user-portal-tutorial.md)Planview Enterprise One [Skills Workflow](../saas-apps/skills-workflow-tutorial.md) [plataforma IP](../saas-apps/ip-platform-tutorial.md), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx) [Invision](../saas-apps/invision-tutorial.md), [Pipedrive](../saas-apps/pipedrive-tutorial.md), [Workshop de apresentação](https://app.showcaseworkshop.com/), [plataforma de integração Greenlight](../saas-apps/greenlight-integration-platform-tutorial.md), [Gerenciamento de acesso compatível com Greenlight](../saas-apps/greenlight-compliant-access-management-tutorial.md), [compreendo Learning](../saas-apps/grok-learning-tutorial.md), [Miradore online](https://login.online.miradore.com/), Khoros [Care](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [BizAgi Studio para automação de processo digital](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-Days](../saas-apps/e-days-tutorial.md), [Kollective Sdn](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md), [campus café](../saas-apps/campus-cafe-tutorial.md), [captura](../saas-apps/catchpoint-tutorial.md), [Code42](../saas-apps/code42-tutorial.md) [LIFT](../saas-apps/lift-tutorial.md) [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Colaboração B2B do Azure AD disponível nos locatários do Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C
 
Os recursos de colaboração B2B do Azure AD agora estão disponíveis entre alguns locatários do Azure governamental.  Para descobrir se o seu locatário é capaz de usar esses recursos, siga as instruções em [como posso saber se a colaboração B2B está disponível no meu locatário do Azure no governo dos EUA?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>A integração do Azure Monitor para logs do Azure agora está disponível no Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A integração do Azure Monitor com os logs do Azure AD agora está disponível no Azure governamental. Você pode rotear logs do Azure AD (logs de auditoria e de logon) para uma conta de armazenamento, Hub de eventos e Log Analytics. Confira a [documentação detalhada](https://aka.ms/aadlogsinamd) , bem como os [planos de implantação para relatórios e monitoramento](../reports-monitoring/plan-monitoring-and-reporting.md) para cenários do Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Atualização da proteção de identidade no Azure governamental

**Tipo:** novo recurso  
**Categoria de serviço:** Proteção de identidade  
**Funcionalidade do produto:** segurança e proteção da identidade

Estamos empolgados em compartilhar que agora distribuímos a experiência de [Azure ad Identity Protection](https://aka.ms/IdentityProtectionDocs)atualizada   no [portal de Microsoft Azure governamental](https://portal.azure.us/). Para obter mais informações, consulte nossa [postagem no blog de anúncios](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Recuperação de desastre: Baixe e armazene sua configuração de provisionamento

**Tipo:** novo recurso  
**Categoria de serviço:** provisionamento de aplicativos  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidade
 
O serviço de provisionamento do Azure AD fornece um rico conjunto de recursos de configuração. Os clientes precisam ser capazes de salvar suas configurações para que possam consultá-las mais tarde ou reverta para uma versão válida conhecida. Adicionamos a capacidade de baixar sua configuração de provisionamento como um arquivo JSON e carregá-la quando necessário. [Saiba mais](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (autoatendimento de redefinição de senha) agora requer duas Gates para administradores no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet) 

**Tipo:** recurso alterado  
**Categoria de serviço:** Redefinição de senha de autoatendimento  
**Funcionalidade do produto:** segurança e proteção da identidade
 
Anteriormente no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet), os administradores usando a redefinição de senha de autoatendimento (SSPR) para redefinir suas próprias senhas precisavam apenas de um "portão" (desafio) para provar sua identidade. No público e em outras nuvens nacionais, os administradores geralmente devem usar duas Gates para provar sua identidade ao usar o SSPR. Mas como não damos suporte a chamadas SMS ou telefônicas na 21Vianet da China do Azure, permitimos a redefinição de senha de uma porta por administradores.

Estamos criando a paridade do recurso SSPR entre o Azure China 21Vianet e a nuvem pública. No futuro, os administradores devem usar dois Gates ao usar o SSPR. O SMS, chamadas telefônicas e códigos e notificações do aplicativo autenticador serão suportados. [Saiba mais](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>O comprimento da senha é limitado a 256 caracteres

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** Autenticação do usuário
 
Para garantir a confiabilidade do serviço do Azure AD, as senhas de usuário agora são limitadas de comprimento a 256 caracteres. Os usuários com senhas maiores que isso serão solicitados a alterar sua senha no logon subsequente, seja entrando em contato com o administrador ou usando o recurso de redefinição de senha de autoatendimento.

Essa alteração foi habilitada em 13 de março de 2020, em 10h PST (18:00 UTC) e o erro é AADSTS 50052, InvalidPasswordExceedsMaxLength. Consulte o [aviso de alteração significativa](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) para obter mais detalhes.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Os logs de entrada do Azure AD agora estão disponíveis para todos os locatários gratuitos por meio do portal do Azure

**Tipo:** recurso alterado  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto:** monitoramento e relatórios
 
A partir de agora, os clientes que têm locatários gratuitos podem acessar os [logs de entrada do Azure ad da portal do Azure](../reports-monitoring/concept-sign-ins.md) por até 7 dias. Anteriormente, os logs de entrada estavam disponíveis apenas para clientes com licenças Azure Active Directory Premium. Com essa alteração, todos os locatários podem acessar esses logs por meio do Portal.

> [!NOTE]
> Os clientes ainda precisam de uma licença Premium (Azure Active Directory Premium P1 ou P2) para acessar os logs de entrada por meio da API Microsoft Graph e Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Substituição da opção de grupos em todo o diretório por meio de grupos configurações gerais no portal do Azure

**Tipo:** preterido  
**Categoria de serviço:** gerenciamento de grupo  
**Recurso de produto:** colaboração

Para fornecer uma maneira mais flexível para os clientes criarem grupos de todo o diretório que melhor atendam às suas necessidades, substituímos a opção **grupos de todo o diretório** das configurações gerais dos **grupos**  >  **General** na portal do Azure com um link para a [documentação do grupo dinâmico](../users-groups-roles/groups-dynamic-membership.md). Aperfeiçoamos nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os usuários que incluem ou excluem usuários convidados.

---
