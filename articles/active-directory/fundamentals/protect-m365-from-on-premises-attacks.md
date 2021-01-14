---
title: Protegendo Microsoft 365 de ataques locais
description: Diretrizes sobre como garantir que um ataque local não afete Microsoft 365
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97893dece068dfdde85159f734095401288231d2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201341"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Protegendo Microsoft 365 de ataques locais

Muitos clientes conectam suas redes corporativas privadas a Microsoft 365 para beneficiar seus usuários, dispositivos e aplicativos. No entanto, há muitas maneiras bem documentadas pelas quais essas redes privadas podem ser comprometidas. Como Microsoft 365 atua como o "sistema preocupados" para muitas organizações, é essencial protegê-la contra a infraestrutura local comprometida.

Este artigo mostra como configurar seus sistemas para proteger seu ambiente de Microsoft 365 Nuvem do comprometimento local. Nos concentramos principalmente nas definições de configuração de locatário do Azure AD, como os locatários do Azure AD podem ser conectados com segurança a sistemas locais e as compensações necessárias para operar seus sistemas de maneiras que protegem seus sistemas de nuvem contra o comprometimento local.

É altamente recomendável que você implemente essas diretrizes para proteger seu ambiente de Microsoft 365 Nuvem.
> [!NOTE]
> Este artigo foi publicado inicialmente como uma postagem no blog. Ele foi movido aqui para longevidade e manutenção. <br>
Para criar uma versão offline deste artigo, use a funcionalidade imprimir para PDF do navegador. Verifique novamente com frequência para obter atualizações.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Principais vetores de ameaça de ambientes locais comprometidos


Seu ambiente de nuvem Microsoft 365 se beneficia de uma ampla infraestrutura de monitoramento e segurança. Usar o aprendizado de máquina e a inteligência humana que examina o tráfego em todo o mundo pode detectar ataques rapidamente e permitir que você reconfigure em tempo quase real. Em implantações híbridas que conectam a infraestrutura local para Microsoft 365, muitas organizações delegam confiança a componentes locais para as decisões de autenticação crítica e gerenciamento de estado do objeto de diretório.
Infelizmente, se o ambiente local estiver comprometido, essas relações de confiança resultarão em oportunidades de ataques para comprometer seu ambiente de Microsoft 365.

Os dois vetores de ameaça principais são **relações de confiança de Federação** e sincronização de **conta.** Ambos os vetores podem conceder a um invasor acesso administrativo à sua nuvem.

* As **relações de confiança federadas**, como a autenticação SAML, são usadas para autenticar para Microsoft 365 por meio de sua infraestrutura de identidade local. Se um certificado de autenticação de token SAML for comprometido, a Federação permitiria que qualquer pessoa com esse certificado represente qualquer usuário em sua nuvem. **Recomendamos que você desabilite as relações de confiança de Federação para autenticação para Microsoft 365 quando possível.**

* A **sincronização de conta** pode ser usada para modificar usuários com privilégios (incluindo suas credenciais) ou grupos com privilégios administrativos concedidos em Microsoft 365. É **recomendável garantir que os objetos sincronizados não contenham nenhum privilégio além de um usuário no Microsoft 365,** diretamente ou por meio da inclusão em funções ou grupos confiáveis. Verifique se esses objetos não têm nenhuma atribuição direta ou aninhada em grupos ou funções de nuvem confiáveis.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Protegendo Microsoft 365 do comprometimento local


Para abordar os vetores de ameaça descritos acima, recomendamos que você siga os princípios ilustrados abaixo:

![Arquitetura de referência para proteger Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **Isole completamente suas contas de administrador de Microsoft 365.** Eles devem ser

    * Mestre no Azure AD.

     * Autenticado com a autenticação multifator (MFA).

     *  Protegido pelo acesso condicional do Azure AD.

     *  Acessado somente usando estações de trabalho gerenciadas do Azure.

Essas são contas de uso restritos. **Não deve haver contas locais com privilégios administrativos em Microsoft 365.** Para obter mais informações, consulte esta [visão geral de Microsoft 365 funções de administrador](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Consulte também [funções para Microsoft 365 em Azure Active Directory](../roles/m365-workload-docs.md).

*  **Gerenciar dispositivos de Microsoft 365.** Use o Azure AD Join e o MDM (gerenciamento de dispositivo móvel) baseado em nuvem para eliminar dependências em sua infraestrutura de gerenciamento de dispositivo local, que pode comprometer o dispositivo e os controles de segurança.

* **Nenhuma conta local tem privilégios elevados para Microsoft 365.**
    Contas que acessam aplicativos locais que exigem autenticação NTLM, LDAP ou Kerberos precisam de uma conta na infraestrutura de identidade local da organização. Verifique se essas contas, incluindo as contas de serviço, não estão incluídas em funções ou grupos de nuvem privilegiados e se as alterações nessas contas não podem afetar a integridade do ambiente de nuvem. O software local privilegiado não deve ser capaz de afetar Microsoft 365 contas com privilégios ou funções.

*  **Use a autenticação de nuvem do AD do Azure** para eliminar dependências em suas credenciais locais. Sempre use a autenticação forte, como o Windows Hello, o FIDO, o Microsoft Authenticator ou o Azure AD MFA.

## <a name="specific-recommendations"></a>Recomendações específicas


As seções a seguir fornecem diretrizes específicas sobre como implementar os princípios descritos acima.

### <a name="isolate-privileged-identities"></a>Isolar identidades privilegiadas


No Azure AD, os usuários com funções privilegiadas, como administradores, são a raiz de confiança para criar e gerenciar o restante do ambiente. Implemente as práticas a seguir para minimizar o impacto de um comprometimento.

* Usar contas somente em nuvem para o Azure AD e Microsoft 365 funções privilegiadas. d

* Implantar [dispositivos com acesso privilegiado](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) para acesso privilegiado para gerenciar o Microsoft 365 e o Azure AD.

*  Implantar [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) para acesso JIT (just in time) a todas as contas humanas que têm funções privilegiadas e exigir autenticação forte para ativar funções.

* Fornecer funções administrativas o [privilégio mínimo possível para executar suas tarefas](../roles/delegate-by-task.md).

* Para habilitar uma experiência de atribuição de função mais rica que inclui delegação e várias funções ao mesmo tempo, considere usar grupos de segurança do Azure AD ou grupos de Microsoft 365 (coletivamente "grupos de nuvem") e [habilitar o controle de acesso baseado em função](../roles/groups-assign-role.md). Você também pode usar [unidades administrativas](../roles/administrative-units.md) para restringir o escopo de funções a uma parte da organização.

* Implante [contas de acesso de emergência](../roles/security-emergency-access.md) e não use cofres de senha locais para armazenar credenciais.

Para obter mais informações, consulte [protegendo o acesso privilegiado](https://aka.ms/SPA), que tem orientações detalhadas sobre este tópico. Além disso, consulte [práticas de acesso seguro para administradores no Azure ad](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Usar autenticação de nuvem 

As credenciais são um vetor de ataque primário. Implemente as práticas a seguir para tornar as credenciais mais seguras.

* [Implantar a autenticação com senha](../authentication/howto-authentication-passwordless-deployment.md): Reduza o uso de senhas tanto quanto possível implantando credenciais com senha. Essas credenciais são gerenciadas e validadas nativamente na nuvem. Escolha:

   * [Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Aplicativo Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Chaves de segurança FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Implantar a autenticação multifator](https://aka.ms/deploymentplans/mfa): provisionar [várias credenciais fortes usando o Azure ad MFA](../fundamentals/resilience-in-credentials.md). Dessa forma, o acesso aos recursos de nuvem exigirá uma credencial gerenciada no Azure AD, além de uma senha local que possa ser manipulada.

   * Para obter mais informações, consulte [criar uma estratégia de gerenciamento de controle de acesso resiliente com o Azure Active Directory](https://aka.ms/resilientaad).

**Limitações e compensações**

* O gerenciamento de senha de conta híbrida requer componentes híbridos, como agentes de proteção de senha e agentes de write-back de senha. Se sua infraestrutura local estiver comprometida, os invasores poderão controlar os computadores nos quais esses agentes residem. Embora isso não comprometa sua infraestrutura de nuvem, suas contas de nuvem não protegerão esses componentes contra o comprometimento local.

*  As contas locais sincronizadas de Active Directory são marcadas para nunca expirarem no Azure AD, com base na suposição de que as políticas de senha do AD local reduzirão isso. Se o seu AD local estiver comprometido e a sincronização do AD Connect precisar ser desabilitada, você deverá definir a opção [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="provision-user-access-from-the-cloud"></a>Provisionar o acesso do usuário na nuvem

O provisionamento refere-se à criação de contas de usuário e grupos em aplicativos ou provedores de identidade.

![Diagrama de arquitetura de provisionamento](media/protect-m365/protect-m365-provision.png)

* **Provisionar de aplicativos de RH de nuvem para o Azure AD:** Isso permite que um comprometimento local seja isolado sem interromper o ciclo de movimentação do seu associador de seus aplicativos de RH na nuvem para o Azure AD.

* **Aplicativos de nuvem:** Sempre que possível, implante [aplicativo Azure ad provisionamento](../app-provisioning/user-provisioning.md) , em oposição às soluções de provisionamento local. Isso protegerá alguns dos seus aplicativos SaaS de ser inviabilizada com perfis de usuário mal-intencionados devido a violações locais. 

* **Identidades externas:** Use a [colaboração B2B do Azure ad](../external-identities/what-is-b2b.md).
    Isso reduzirá a dependência de contas locais para colaboração externa com parceiros, clientes e fornecedores. Avalie cuidadosamente qualquer Federação direta com outros provedores de identidade. É recomendável limitar as contas de convidado B2B das seguintes maneiras.

   *  Limite o acesso de convidado a grupos de navegação e outras propriedades no diretório. Use as configurações de colaboração externas para restringir a capacidade do convidado de ler grupos dos quais eles não são membros. 

    *   Bloquear o acesso ao portal do Azure. Você pode fazer exceções raras necessárias.  Crie uma política de acesso condicional que inclua todos os convidados e usuários externos e, em seguida, [implemente uma política para bloquear o acesso](/azure/role-based-access-control/conditional-access-azure-management). 

* **Florestas desconectadas:** Use o [provisionamento de nuvem do Azure ad](../cloud-provisioning/what-is-cloud-provisioning.md). Isso permite que você se conecte a florestas desconectadas, eliminando a necessidade de estabelecer confiança ou conectividade entre florestas, o que pode ampliar o impacto de uma violação local. * 
 
**Limitações e compensações:**

* Quando usado para provisionar contas híbridas, o Azure AD dos sistemas de RH de nuvem depende da sincronização local para concluir o fluxo de dados do AD para o Azure AD. Se a sincronização for interrompida, os novos registros de funcionários não estarão disponíveis no Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Usar grupos de nuvem para colaboração e acesso

Os grupos de nuvem permitem dissociar sua colaboração e acesso de sua infraestrutura local.

* **Colaboração:** Use grupos de Microsoft 365 e Microsoft Teams para colaboração moderna. Descomissionar listas de distribuição locais e [atualizar listas de distribuição para Microsoft 365 grupos no Outlook](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Acesso:** Use grupos de segurança do Azure AD ou grupos de Microsoft 365 para autorizar o acesso a aplicativos no Azure AD.
* **Licenciamento do Office 365:** Use o licenciamento baseado em grupo para provisionar o Office 365 usando grupos somente de nuvem. Isso dissocia o controle de associação de grupo da infraestrutura local.

Os proprietários de grupos usados para acesso devem ser considerados identidades privilegiadas para evitar a associação tomada do comprometimento local.
Assumir inclui a manipulação direta da Associação de grupo no local ou a manipulação de atributos locais que podem afetar a associação de grupo dinâmico no Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gerenciar dispositivos de nuvem


Use os recursos do Azure AD para gerenciar dispositivos com segurança.

-   **Usar estações de trabalho do Windows 10:** [implantar dispositivos ingressados no Azure ad](../devices/azureadjoin-plan.md) com as políticas de MDM. Habilite o [piloto automático do Windows](https://docs.microsoft.com/mem/autopilot/windows-autopilot) para uma experiência de provisionamento totalmente automatizada.

    -   Substitua Windows 8.1 e computadores anteriores.

    -   Não implante computadores do sistema operacional como estações de trabalho.

    -   Use [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) como a origem de autoridade de todas as cargas de trabalho de gerenciamento de dispositivos.

-   [**Implantar dispositivos com acesso privilegiado**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) para acesso privilegiado para gerenciar o Microsoft 365 e o Azure AD.

 ## <a name="workloads-applications-and-resources"></a>Cargas de trabalho, aplicativos e recursos 

-   **Sistemas de SSO locais:** Preterir qualquer Federação local e infraestrutura de gerenciamento de Acesso via Web e configurar aplicativos para usar o Azure AD.  

-   **Aplicativos SaaS e LOB que dão suporte a protocolos de autenticação modernos:** [use o Azure ad para logon único](../manage-apps/what-is-single-sign-on.md). Quanto mais aplicativos você configurar para usar o Azure AD para autenticação, menor será o risco no caso de um comprometimento local.


* **Aplicativos herdados** 

   * A autenticação, a autorização e o acesso remoto a aplicativos herdados que não dão suporte à autenticação moderna podem ser habilitados por meio [do Azure proxy de aplicativo do AD](../manage-apps/application-proxy.md). Eles também podem ser habilitados por meio de uma solução de rede ou de controlador de entrega de aplicativos usando  [integrações de parceiros de acesso híbrido seguro](../manage-apps/secure-hybrid-access.md).   

   * Escolha um fornecedor de VPN que ofereça suporte à autenticação moderna e integre sua autenticação com o Azure AD. No caso de um comprometimento local de anon, você pode usar o Azure AD para desabilitar ou bloquear o acesso desabilitando a VPN.

*  **Aplicativos e servidores de carga de trabalho**

   * Os aplicativos ou recursos que precisam de servidores podem ser migrados para o IaaS do Azure e usam [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (AD DS do Azure) para dissociar a confiança e a dependência no AD local. Para atingir essa desassociação, as redes virtuais usadas para o Azure AD DS não devem ter conexão com redes corporativas.

   * Siga as orientações da [camada de credencial](https://aka.ms/TierModel). Os servidores de aplicativos normalmente são considerados ativos da camada 1.

 ## <a name="conditional-access-policies"></a>Políticas de Acesso Condicional

Use o acesso condicional do Azure AD para interpretar sinais e tomar decisões de autenticação com base neles. Para obter mais informações, consulte o [plano de implantação de acesso condicional.](https://aka.ms/deploymentplans/ca)

* [Protocolos de autenticação herdados](../fundamentals/auth-sync-overview.md): Use o acesso condicional para bloquear protocolos de [autenticação herdados](../conditional-access/howto-conditional-access-policy-block-legacy.md) sempre que possível. Além disso, desabilite os protocolos de autenticação herdados no nível do aplicativo usando a configuração específica do aplicativo.

   * Consulte detalhes específicos para o [Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) e o [SharePoint Online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implemente as configurações recomendadas de [acesso de dispositivo e identidade.](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Se você estiver usando uma versão do Azure AD que não inclui acesso condicional, verifique se você está usando os [padrões de segurança do Azure ad](../fundamentals/concept-fundamentals-security-defaults.md).

   * Para obter mais informações sobre o licenciamento de recursos do Azure AD, consulte o [Guia de preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Monitoramento 

Depois de configurar seu ambiente para proteger seus Microsoft 365 de um comprometimento local, [monitore proativamente](../reports-monitoring/overview-monitoring.md) o ambiente.
### <a name="scenarios-to-monitor"></a>Cenários para monitorar

Monitore os seguintes cenários principais, além de quaisquer cenários específicos para sua organização. Por exemplo, você deve monitorar proativamente o acesso aos seus aplicativos e recursos críticos para os negócios.

* **Atividade suspeita**: todos os [eventos de risco do Azure ad](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation) devem ser monitorados para atividades suspeitas. O [Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) é nativamente integrado à central de segurança do Azure.

   * Defina os [locais nomeados](../reports-monitoring/quickstart-configure-named-locations.md) da rede para evitar detecções com ruídos em sinais baseados no local. 
*  **Alertas de Ueba (análise comportamental) de entidade do usuário** Use UEBA para obter informações sobre a detecção de anomalias.
   * O MCAS (descoberta de aplicativo de Microsoft Cloud) fornece [Ueba na nuvem](https://docs.microsoft.com/cloud-app-security/tutorial-ueba).

   * Você pode [integrar o Ueba local do Azure ATP](https://docs.microsoft.com/defender-for-identity/install-step2). MCAS lê sinais de Azure AD Identity Protection. 

* **Atividade de contas de acesso de emergência**: qualquer acesso usando [contas de acesso de emergência](../roles/security-emergency-access.md) deve ser monitorado e alertas criados para investigações. Esse monitoramento deve incluir: 

   * Entradas. 

   * Gerenciamento de credenciais. 

   * Quaisquer atualizações em associações de grupo. 

   *    Atribuições de aplicativo. 
* **Atividade de função com privilégios**: Configure e examine os [alertas de segurança gerados pelo Azure ad PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts).
    Monitore a atribuição direta de funções privilegiadas fora do PIM, gerando alertas sempre que um usuário é atribuído diretamente.
* **Configurações de todo o locatário do Azure ad**: qualquer alteração nas configurações de todo o locatário deve gerar alertas no sistema. Eles incluem, mas não se limitam a
  *  Atualizando domínios personalizados  

  * Alterações na lista de permissões/bloqueios B2B do Azure AD.
  * Os provedores de identidade permitidos do Azure AD B2B (SAML IDPs por meio de Federação direta ou logons sociais).  
  * Alterações de política de risco ou de acesso condicional 

* **Objetos de aplicativo e entidade de serviço**:
   * Novos aplicativos ou entidades de serviço que podem exigir políticas de acesso condicional. 

   * Credenciais adicionais adicionadas às entidades de serviço.
   * Atividade de consentimento do aplicativo. 

* **Funções personalizadas**:
   * Atualizações das definições de função personalizadas. 

   * Novas funções personalizadas criadas. 

### <a name="log-management"></a>Gerenciamento de Log

Defina um armazenamento de log e uma estratégia de retenção, design e implementação para facilitar um conjunto de ferramentas consistente, como sistemas SIEM, como o Azure Sentinel, consultas comuns e guias estratégicos de investigação e análise forense.

* **Logs do Azure ad** Os logs de ingestão e o sinal produzidos seguindo as práticas recomendadas consistentes, incluindo configurações de diagnóstico, retenção de log e ingestão de SIEM. A estratégia de log deve incluir os seguintes logs do Azure AD:
   * Atividade de entrada 

   * Logs de auditoria 

   * Eventos de risco 

O Azure AD fornece [Azure monitor integração](../reports-monitoring/concept-activity-logs-azure-monitor.md) para o log de atividades de entrada e logs de auditoria. Os eventos de risco podem ser ingeridos por meio da [API Microsoft Graph](https://aka.ms/AzureADSecuredAzure/32b). Você pode [transmitir logs do Azure ad para logs do Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Logs de segurança do sistema operacional de infraestrutura híbrida.** Todos os logs do sistema operacional de infraestrutura de identidade híbrida devem ser arquivados e cuidadosamente monitorados como um <br>Sistema de camada 0, considerando as implicações da área de superfície. Isso inclui: 

   *  o Azure AD Connect. [Azure ad Connect Health](https://aka.ms/AzureADSecuredAzure/32e) deve ser implantado para monitorar a sincronização de identidades.

   *  Agentes de proxy de aplicativo 


   * Agentes de write-back de senha 

   * Computadores do gateway de proteção de senha  

   * NPS que têm a extensão RADIUS do Azure MFA 

## <a name="next-steps"></a>Próximas etapas
* [Incorporar resiliência no gerenciamento de identidade e acesso com o Azure AD](resilience-overview.md)

* [Proteger o acesso externo a recursos](secure-external-access-resources.md) 
* [Integre todos os seus aplicativos com o Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)
