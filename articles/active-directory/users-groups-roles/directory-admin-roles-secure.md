---
title: Práticas de acesso seguro para administradores no Azure AD | Microsoft Docs
description: Certifique-se de que as contas administrativas de acesso e administração da sua organização estão seguras. Para arquitetos de sistema e profissionais de TI que configurarem o Azure AD, Azure e serviços Online da Microsoft.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c580a39db97e1ce50c3d244db3023bf422bca08
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837185"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Proteger o acesso privilegiado para implantações de nuvem híbrida no Azure AD

A segurança dos ativos de negócios depende da integridade das contas com privilégios que administram seus sistemas de ti. Os invasores virtuais usam ataques de roubo de credenciais para direcionar contas de administrador e outro acesso privilegiado para tentar obter acesso a dados confidenciais.

Para serviços de nuvem, prevenção e resposta são as responsabilidades conjuntas do provedor de serviços de nuvem e do cliente. Para obter mais informações sobre as ameaças mais recentes aos pontos de extremidade e à nuvem, consulte o [Relatório de Inteligência de Segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report). Este artigo pode ajudá-lo a desenvolver um roteiro para fechar as lacunas entre os planos atuais e as diretrizes descritas aqui.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com os padrões e conformidade normativa. Saiba mais sobre como a equipe de resposta a incidentes globais da Microsoft atenua os efeitos de ataques contra serviços de nuvem e como a segurança é criada em produtos comerciais da Microsoft e serviços de nuvem no [Microsoft Trust Center - Segurança](https://www.microsoft.com/trustcenter/security)e destinos de conformidade da Microsoft em [Microsoft Trust Center - Conformidade](https://www.microsoft.com/trustcenter/compliance).

Tradicionalmente, a segurança organizacional se concentrou nos pontos de entrada e saída de uma rede como o perímetro de segurança. No entanto, os aplicativos SaaS e os dispositivos pessoais na Internet tornaram essa abordagem menos eficaz. No Azure AD, substituimos o perímetro de segurança de rede pela autenticação na camada de identidade da sua organização, com usuários atribuídos a funções administrativas privilegiadas no controle. Seu acesso deve ser protegido, independentemente de o ambiente estar no local, na nuvem ou em um híbrido.

Proteger o acesso privilegiado requer alterações em:

* Processos, práticas administrativas e gerenciamento de conhecimento
* Componentes técnicos, como proteção de host, proteções de conta e gerenciamento de identidade

Proteja seu acesso privilegiado de uma maneira que seja gerenciada e relatada nos serviços da Microsoft que você se preocupa. Se você tiver contas de administrador local, consulte as diretrizes para acesso com privilégios híbrido e local em Active Directory em protegendo o [acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> As diretrizes neste artigo referem-se principalmente a recursos do Azure Active Directory que são incluídos em planos de Azure Active Directory Premium P1 e P2. O Azure Active Directory Premium P2 está incluído no conjunto de EMS E5 e Microsoft 365 E5. Este guia pressupõe que sua organização já tem as licenças do Azure AD Premium P2 adquiridas para os usuários. Se você não tiver essas licenças, algumas das orientações podem não se aplicar à sua organização. Além disso, em todo este artigo, o termo administrador global (ou administrador global) significa a mesma coisa que "administrador da empresa" ou "administrador de locatários".

## <a name="develop-a-roadmap"></a>Desenvolver um roteiro

A Microsoft recomenda que você crie e execute um roteiro para proteger o acesso privilegiado contra invasores virtuais. Você sempre pode ajustar seu roteiro para acomodar seus recursos existentes e os requisitos específicos dentro da sua organização. Cada estágio de nossos planos deverá aumentar o custo e a dificuldade de adversários de atacar o acesso privilegiado ao seu local, nuvem e ativos híbridos. A Microsoft recomenda os quatro estágios de roteiro a seguir. Agende primeiro as implementações mais eficazes e mais rápidas. Este artigo pode ser seu guia, com base nas experiências da Microsoft com a implementação de incidente e resposta de ataque cibernético. Os cronogramas deste roteiro são as aproximações.

![Etapas do roteiro com linhas do tempo](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Etapa 1 (24 a 48 horas): itens críticos, recomendamos que você faça imediatamente

* Etapa 2 (2 a 4 semanas): reduzir as técnicas de ataque usados com mais frequência

* Etapa 3 (1 a 3 meses): criar visibilidade e controle total da atividade do administrador

* Etapa 4 (seis meses adiante): continuar criando defesas para proteger ainda mais a plataforma de segurança

Essa estrutura de roteiro foi projetada para maximizar o uso de tecnologias da Microsoft que você já tiver implantado. Considere a possibilidade de se ligar a qualquer ferramenta de segurança de outros fornecedores que você já tenha implantado ou esteja considerando a implantação.

## <a name="stage-1-critical-items-to-do-right-now"></a>Etapa 1: itens críticos para fazer agora

![Etapa 1 itens críticos para fazer primeiro](./media/directory-admin-roles-secure/stage-one.png)

A etapa 1 do roteiro do destina-se as tarefas críticas que são rápidas e fáceis de implementar. É recomendável que você faça alguns desses itens imediatamente dentro de 24 a 48 horas primeiro para garantir um nível básico de proteção ao acesso privilegiado. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir:

### <a name="general-preparation"></a>Preparação geral

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ative o Azure AD Privileged Identity Management

Recomendamos que você ative Azure AD Privileged Identity Management (PIM) em seu ambiente de produção do Azure AD. Depois de ativar o PIM, você receberá mensagens de email de notificação para alterações de função de acesso privilegiado. As notificações fornecem um aviso antecipado quando usuários adicionais são adicionados a funções altamente privilegiadas.

O Azure AD Privileged Identity Management está incluído no Azure AD Premium P2 ou EMS E5. Para ajudá-lo a proteger o acesso a aplicativos e recursos locais e na nuvem, Inscreva-se para a [Enterprise Mobility + Security avaliação gratuita de 90 dias](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Azure AD Privileged Identity Management e Azure AD Identity Protection monitorar a atividade de segurança usando relatórios, auditoria e alertas do Azure AD.

Depois de ativar Azure AD Privileged Identity Management:

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta que seja um administrador global da sua organização de produção do Azure AD.

2. Para selecionar a organização do Azure AD na qual você deseja usar Privileged Identity Management, selecione seu nome de usuário no canto superior direito do portal do Azure.

3. No menu portal do Azure, selecione **todos os serviços** e filtre a lista para **Azure ad Privileged Identity Management**.

4. Abra o Privileged Identity Management a partir de **Todos os serviços** liste e fixá-o ao seu painel.

A primeira pessoa a usar o PIM em sua organização é atribuída às funções **administrador de segurança** e administrador de **função privilegiada** . Somente os administradores com privilégios de função podem gerenciar atribuições de função de usuários do diretório do Azure AD. O assistente de segurança do PIM orienta você pela experiência inicial de descoberta e atribuição. Você pode sair do assistente sem fazer alterações adicionais no momento.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifique e categorize as contas que estão em funções altamente privilegiadas

Depois de ativar Azure AD Privileged Identity Management, exiba os usuários que estão nas seguintes funções do Azure AD:

* Administrador global
* Administrador de função com privilégios
* Administrador do Exchange Online
* Administrador do SharePoint Online

Se você não tiver Azure AD Privileged Identity Management em sua organização, poderá usar a [API do PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comece com a função de administrador global porque um administrador global tem as mesmas permissões em todos os serviços de nuvem para os quais sua organização assinou. Essas permissões são concedidas independentemente de onde foram atribuídas: no centro de administração Microsoft 365, no portal do Azure ou pelo módulo do Azure AD para o Microsoft PowerShell.

Remova todas as contas que não são mais necessárias nessas funções. Em seguida, categorize as contas restantes atribuídas às funções administrativas:

* Atribuído a usuários administrativos, mas também usado para fins não administrativos (por exemplo, email pessoal)
* Atribuídos a usuários administrativos e usados apenas para fins administrativos
* Compartilhada por vários usuários
* Para cenários de acesso de emergência em situação crítica
* Para scripts automatizados
* Para usuários externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Defina pelo menos duas contas de acesso de emergência

É possível que um usuário seja bloqueado acidentalmente de sua função. Por exemplo, se um provedor de identidade local federado não estiver disponível, os usuários não poderão entrar ou ativar uma conta de administrador existente. Você pode se preparar para a falta de acesso acidental armazenando duas ou mais contas de acesso de emergência.

As contas de acesso de emergência ajudam a restringir o acesso privilegiado em uma organização do Azure AD. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a emergências em cenários de "interrupção" em que as contas administrativas normais não podem ser usadas. Certifique-se de controlar e reduzir o uso da conta de emergência somente para o tempo necessário.

Avalie as contas que são atribuídas ou qualificadas para a função de administrador global. Se você não vir nenhuma conta somente de nuvem usando o \*domínio. onmicrosoft.com (para acesso de emergência de "interrupção"), crie-as. Para obter mais informações, consulte [Gerenciamento de contas administrativas de acesso de emergência no Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ativar a autenticação multifator e registrar todas as outras contas de administrador não federadas de usuário único altamente privilegiado

Exija o Azure Multi-Factor Authentication (MFA) na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do Azure AD: Administrador Global, administrador de funções com privilégios, administrador do Exchange Online e administrador SharePoint Online. Use o guia para habilitar [Multi-factor Authentication (MFA) para suas contas de administrador](../authentication/howto-mfa-userstates.md) e certifique-se de que todos os usuários se registraram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Mais informações podem ser encontradas na etapa 2 e 3 do guia [Proteger o acesso a dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Etapa 2: mitigar ataques usados com frequência

![Estágio 2 mitigar ataques usados com frequência](./media/directory-admin-roles-secure/stage-two.png)

O estágio 2 do roteiro se concentra na redução das técnicas de ataque e roubo de credenciais usadas com mais frequência e pode ser implementado em aproximadamente 2 a 4 semanas. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Realizar um inventário de serviços, proprietários e administradores

O aumento no "Traga seu próprio dispositivo" e o trabalho de políticas domésticas e o crescimento da conectividade sem fio tornam essencial monitorar quem está se conectando à sua rede. Uma auditoria de segurança pode revelar dispositivos, aplicativos e programas em sua rede para os quais sua organização não dá suporte e que representam alto risco. Para obter mais informações, veja [visão geral de monitoramento e gerenciamento de segurança do Azure](../../security/fundamentals/management-monitoring-overview.md). Certifique-se de incluir todas as tarefas a seguir em seu processo de inventário.

* Identifique os usuários que têm os serviços e funções administrativas, onde eles podem gerenciar.
* Use o Azure AD PIM para descobrir quais usuários em sua organização têm acesso de administrador ao Azure AD.
* Além das funções definidas no Azure Ad, o Office 365 vem com um conjunto de funções de administrador que você pode atribuir a usuários em sua organização. Cada função de administrador é mapeada para funções comerciais comuns e concede às pessoas em sua organização permissões para realizar tarefas específicas no [centro de administração Microsoft 365](https://admin.microsoft.com). Use o centro de administração Microsoft 365 para descobrir quais usuários em sua organização têm acesso de administrador ao Office 365, incluindo por meio de funções não gerenciadas no Azure AD. Para obter mais informações, consulte sobre as funções de administrador e as [práticas de segurança do](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) [Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) para o Office 365.
* Faça o inventário nos serviços de que sua organização depende, como Azure, Intune ou Dynamics 365.
* Verifique se suas contas são usadas para fins de administração:

  * Ter endereços de email de trabalho anexados a eles
  * Ter se registrado para a autenticação multifator do Azure ou usar MFA local
* Pergunte aos usuários sua justificativa de negócios para acesso administrativo.
* Remova o acesso de administrador para as pessoas e serviços que não são necessários.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifique as contas da Microsoft em funções administrativas que precisam ser alternadas para contas de trabalho ou escolares

Se os administradores globais iniciais reutilizarem suas credenciais de conta Microsoft existentes quando começarem a usar o Azure AD, substitua as contas da Microsoft por contas individuais baseadas em nuvem ou sincronizadas.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Garanta contas de usuário separadas e emails de encaminhamento para as contas de administrador global

As contas de email pessoais são regularmente analisadas por invasores cibernéticos, um risco que torna os endereços de email pessoais inaceitáveis para contas de administrador global. Para ajudar a separar os riscos da Internet de privilégios administrativos, crie contas dedicadas para cada usuário com privilégios administrativos.

* Certifique-se de criar contas separadas para os usuários realizarem tarefas de administração globais
* Certifique-se de que seus administradores globais não abram emails acidentalmente ou executem programas com suas contas de administrador
* Certifique-se de que essas contas tenham seus emails encaminhados para uma caixa de correio de trabalho

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Certifique-se de que as senhas de contas administrativas foram alteradas recentemente

Verifique se todos os usuários entraram em suas contas administrativas e alteraram suas senhas pelo menos uma vez nos últimos 90 dias. Além disso, verifique se todas as contas compartilhadas tiveram suas senhas alteradas recentemente.

#### <a name="turn-on-password-hash-synchronization"></a>Ativar a sincronização de hash de senha

Azure AD Connect sincroniza um hash do hash da senha de um usuário do Active Directory local para uma organização do Azure AD baseada em nuvem. Você pode usar a sincronização de hash de senha como um backup se usar Federação com Serviços de Federação do Active Directory (AD FS) (AD FS). Esse backup poderá ser útil se seus servidores locais Active Directory ou AD FS estiverem temporariamente indisponíveis.

A sincronização de hash de senha permite que os usuários entrem em um serviço usando a mesma senha que eles usam para entrar em sua instância de Active Directory local. A sincronização de hash de senha permite que a proteção de identidade detecte credenciais comprometidas comparando hashes de senha com senhas conhecidas para serem comprometidas. Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Exigir autenticação multifator para usuários em funções privilegiadas e usuários expostos

O Azure AD recomenda que você exija a autenticação multifator (MFA) para todos os seus usuários. Certifique-se de considerar os usuários que teriam um impacto significativo se sua conta fosse comprometida (por exemplo, gerentes financeiros). A MFA reduz o risco de um ataque devido a uma senha comprometida.

Ativar:

* [MFA usando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) para todos os usuários em sua organização.

Se você usar o Windows Hello para empresas, o requisito de MFA poderá ser atendido usando a experiência de entrada do Windows Hello. Para obter mais informações, consulte [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Configurar o Identity Protection

Azure AD Identity Protection é uma ferramenta de monitoramento e relatório baseada em algoritmos que detecta possíveis vulnerabilidades que afetam as identidades da sua organização. Você pode configurar as respostas automatizadas a essas atividades suspeitas detectadas e tomar as devidas providências para resolvê-los. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obter o Office 365 Secure Score (se estiver usando o Office 365)

A pontuação segura examina suas configurações e atividades para os serviços do Office 365 que você está usando e os compara a uma linha de base estabelecida pela Microsoft. Você receberá uma pontuação com base em quão alinhado com as práticas de segurança. Qualquer pessoa que tenha as permissões de administrador para uma assinatura do Office 365 Business Premium ou Enterprise pode acessar a [https://securescore.office.com](https://securescore.office.com/)Pontuação segura em.

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Verifique as diretrizes de segurança e conformidade do Office 365 (se estiver usando o Office 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem de um cliente do Office 365 para configurar o Office 365 e habilitar outros recursos do EMS. Em seguida, analise as etapas de 3 a 6 de como [proteger o acesso a dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e o guia de como [monitorar segurança e conformidade no Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configure o Office 365 Activity Monitoring (se estiver usando o Office 365)

Monitore sua organização para usuários que estão usando o Office 365 para identificar a equipe que tem uma conta de administrador, mas pode não precisar de acesso ao Office 365 porque elas não entram nesses portais. Para obter mais informações, consulte [relatórios de atividade no centro de administração do Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer os proprietários de plano de resposta de incidente/emergência

O estabelecimento de uma funcionalidade de resposta a incidentes bem-sucedida requer planejamento e recursos consideráveis. Você deve monitorar continuamente os ataques cibernéticos e estabelecer prioridades para a manipulação de incidentes. Colete, analise e relate dados de incidentes para criar relações e estabelecer comunicação com outros grupos internos e planejar os proprietários. Para obter mais informações, consulte [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteja as contas as contas administrativas locais, se ainda não tiver feito isso

Se sua organização Azure Active Directory estiver sincronizada com o Active Directory local, siga as orientações em [roteiro de acesso privilegiado à segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): este estágio inclui:

* Criando contas de administrador separadas para usuários que precisam realizar tarefas administrativas locais
* Implantando estações de trabalho com acesso privilegiado para administradores de Active Directory
* Criando senhas de administrador local exclusivas para estações de trabalho e servidores

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário de assinaturas

Use o portal da Enterprise e o portal do Azure para identificar as assinaturas em sua organização que hospedam aplicativos de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover as contas da Microsoft de funções de administrador

As contas da Microsoft de outros programas, como Xbox, Live e Outlook, não devem ser usadas como contas de administrador para as assinaturas da sua organização. Remova o status do administrador de todas as contas da Microsoft e substitua pelo Azure AD ( chris@contoso.compor exemplo,) contas corporativas ou de estudante. Para fins de administração, dependa de contas que são autenticadas no Azure AD e não em outros serviços.

#### <a name="monitor-azure-activity"></a>Monitorar a atividade do Azure

O Log de Atividades do Azure fornece um histórico de eventos no nível da assinatura no Azure. Oferece informações sobre quem criou, atualizou ou excluiu quais recursos e quando fez isso. Para obter mais informações, consulte [Auditar e receber notificações sobre ações importantes em sua assinatura do Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="configure-conditional-access-policies"></a>Configurar políticas de acesso condicional

Prepare políticas de acesso condicional para aplicativos locais e hospedados na nuvem. Se você tiver usuários ingressados no local de trabalho, obtenha mais informações em [Configurando o acesso condicional local usando Azure Active Directory registro de dispositivo](../active-directory-device-registration-on-premises-setup.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Etapa 3: assumir o controle da atividade do administrador

![Etapa 3: assumir o controle da atividade do administrador](./media/directory-admin-roles-secure/stage-three.png)

O estágio 3 se baseia nas mitigações do estágio 2 e deve ser implementado em aproximadamente 1-3 meses. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Concluir uma análise de acesso de usuários em funções de administrador

Mais usuários corporativos estão ganhando acesso privilegiado por meio de serviços de nuvem, o que pode levar a acesso não gerenciado. Atualmente, os usuários podem se tornar administradores globais para o Office 365, administradores de assinatura do Azure ou ter acesso de administrador a VMs ou por meio de aplicativos SaaS.

Sua organização deve ter todos os funcionários manipular transações de negócios comuns como usuários sem privilégios e conceder direitos de administrador somente quando necessário. Conclua as revisões de acesso para identificar e confirmar os usuários que estão qualificados para ativar os privilégios de administrador.

É recomendável que você:

1. Determine quais usuários são administradores do Azure AD, habilite sob demanda, acesso de administração just-in-time e controles de segurança baseada em função.
2. Converter os usuários que têm sem justificativa clara para acesso de administrador com privilégios para uma função diferente (se não houver função qualificada, remova-o).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continuar a distribuição de autenticação mais forte para todos os usuários

Exigir que usuários altamente expostos tenham autenticação moderna e forte, como o Azure MFA ou o Windows Hello. Exemplos de usuários altamente expostos incluem:

* Executivos do C-Suite
* Gerentes de alto nível
* Equipe de ti e segurança crítica

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Use estações de trabalho dedicadas para a administração do Azure AD

Os invasores podem tentar direcionar contas com privilégios para que possam interromper a integridade e a autenticidade dos dados. Geralmente, eles usam código mal-intencionado que altera a lógica do programa ou espiona o administrador inserindo uma credencial. As PAWs (Privileged Access Workstations, Estações de Trabalho com Acesso Privilegiado) fornecem um sistema operacional dedicado para tarefas confidenciais, que é protegido contra ataques da Internet e vetores de ameaça. Separar essas tarefas e contas confidenciais das estações de trabalho e dispositivos de uso diário fornece proteção forte de:

* Ataques de phishing
* Vulnerabilidades do aplicativo e do sistema operacional
* Ataques de representação
* Ataques de roubo de credenciais, como registro de pressionamentos de tecla, passagem de hash e passagem de tíquete

Ao implantar estações de trabalho com acesso privilegiado, você pode reduzir o risco de que os administradores insiram suas credenciais em um ambiente de desktop que não tenha sido protegido. Para saber mais, confira [Privileged Identity Management](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Analise as recomendações do Instituto Nacional de padrões e tecnologia para lidar com incidentes

O Instituto Nacional de padrões e tecnologia (NIST) fornece diretrizes para tratamento de incidentes, particularmente para analisar dados relacionados ao incidente e determinar a resposta apropriada a cada incidente. Para obter mais informações, consulte [(NIST) o Computer Security Incident Handling Guide (SP 61 800, Revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Privileged Identity Management (PIM) para JIT a funções administrativas adicionais

Para o Azure Active Directory, use o recurso [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). Ativação de tempo limitado de funções privilegiadas funciona, permitindo que você:

* Ativar privilégios de administrador para realizar uma tarefa específica
* Imponha o MFA durante o processo de ativação
* Use alertas para informar os administradores sobre alterações fora de banda
* Permitir que os usuários mantenham seu acesso privilegiado por um período pré-configurado
* Permitir que os administradores de segurança:

  * Descobrir todas as identidades com privilégios
  * Exibir relatórios de auditoria
  * Criar revisões de acesso para identificar cada usuário que está qualificado para ativar privilégios de administrador

Se você já estiver usando o Azure AD Privileged Identity Management, ajuste intervalos de tempo para privilégios de tempo especificado conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determine a exposição a protocolos com senha (se estiver usando o Exchange Online)

Recomendamos que você identifique todos os usuários potenciais que podem ser catastróficos para a organização se suas credenciais foram comprometidas. Para esses usuários, coloque em vigor os requisitos de autenticação fortes e use o acesso condicional do Azure AD para mantê-los de entrar em seus emails usando o nome de usuário e a senha. Você pode bloquear a [autenticação herdada usando o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)e pode [bloquear a autenticação básica](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) por meio do Exchange Online.

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Conclua uma avaliação de revisão de funções para as funções do Office 365 (se estiver usando o Office 365)

Avalie se todos os usuários administradores estão nas funções corretas (exclua e reatribua de acordo com essa avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Examine a abordagem de gerenciamento de incidentes de segurança usada no Office 365 e compare com sua própria organização

Você pode fazer o download desse relatório de [gerenciamento de incidentes de segurança no Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a proteger as contas administrativas privilegiadas locais

Se o seu locatário do Azure Active Directory estiver sincronizado ao Active Directory no local, em seguida, siga as orientações em [Roteiro de Acesso Privilegiado à Segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Etapa 2. Neste estágio, você:

* Implantar estações de trabalho com acesso privilegiado para todos os administradores
* Exigir MFA
* Use apenas o administrador suficiente para a manutenção do controlador de domínio, reduzindo a superfície de ataque de domínios
* Implantar a avaliação avançada de ameaças para detecção de ataque

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitoramento integrado

A [central de segurança do Azure](../../security-center/security-center-intro.md):

* Fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure
* Ajuda a detectar ameaças que, de outra forma, podem passar despercebidas
* Funciona com uma ampla gama de soluções de segurança

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Faça o inventário de contas privilegiadas em Máquinas Virtuais hospedadas

Normalmente, você não precisa dar aos usuários permissões irrestritas para todas as suas assinaturas ou recursos do Azure. Use as funções de administrador do Azure AD para conceder somente o acesso que os usuários que precisam para realizar seus trabalhos. Você pode usar as funções de administrador do Azure AD para permitir que um administrador gerencie somente as VMs em uma assinatura, enquanto outra pode gerenciar bancos de dados SQL dentro da mesma assinatura. Para obter mais informações, consulte [Introdução ao Controle de Acesso Baseado em Função no portal do Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar o PIM para funções de administrador do Azure AD

Use o Privileged Identity Management com as funções de administrador do Azure Ad para gerenciar, controlar e monitorar o acesso aos recursos do Azure. O uso do PIM protege diminuindo o tempo de exposição de privilégios e aumentando sua visibilidade em seus relatórios e alertas. Para obter mais informações, consulte [Gerenciar o acesso aos recursos do Azure com o Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Usar integrações do log do Azure para enviar logs relevantes do Azure para seus sistemas SIEM

A integração de log do Azure permite que você integre logs brutos de recursos do Azure aos sistemas Security Information and Event Management (SIEM) da sua organização. A [integração de log do Azure](../../security/fundamentals/azure-log-integration-overview.md) coleta eventos do Windows de logs do Windows visualizador de eventos e recursos do Azure de:

* Logs de atividades do Azure
* Alertas da Central de Segurança do Azure
* Logs de recursos do Azure

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento do usuário para aplicativos conectados

O Azure AD permite automatizar a criação e a manutenção de identidades de usuário em aplicativos de nuvem, como dropbox, Salesforce e ServiceNow. Para saber mais, confira [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar proteção de informações

Microsoft Cloud App Security permite investigar arquivos e definir políticas com base nos rótulos de classificação da proteção de informações do Azure, permitindo maior visibilidade e controle de seus dados na nuvem. Verifique e classifique arquivos na nuvem e aplique rótulos de proteção de informações do Azure. Confira mais informações em [Integração da Proteção de Informações do Azure](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configurar o acesso condicional

Configure o acesso condicional com base em um grupo, local e sensibilidade de aplicativo para [aplicativos SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicativos conectados ao Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorar a atividade de aplicativos de nuvem conectados

É recomendável usar [Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para garantir que o acesso do usuário também seja protegido em aplicativos conectados. Esse recurso protege o acesso corporativo a aplicativos de nuvem e protege suas contas de administrador, permitindo que você:

* Aumentar a visibilidade e controle para aplicativos em nuvem
* Criar políticas de acesso, atividades e compartilhamento de dados
* Identificar automaticamente as atividades arriscadas, comportamentos anormais e ameaças
* Impedir o vazamento de dados
* Minimizar o risco e prevenção de ameaças automatizado e aplicação de políticas

O agente Cloud App Security SIEM integra Cloud App Security integra o Cloud App Security ao seu servidor SIEM para habilitar o monitoramento centralizado de atividades e alertas do Office 365. Ele é executado no servidor e recebe alertas e atividades de segurança do aplicativo de nuvem e transmite-as no servidor SIEM. Para obter mais informações, consulte [Integração SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>Estágio 4: continuar criando defesas

![Estágio 4: adotar uma postura de segurança ativa](./media/directory-admin-roles-secure/stage-four.png)

O estágio 4 do roteiro deve ser implementado em seis meses e além. Conclua seu roteiro para reforçar as proteções de acesso privilegiado de ataques em potencial que são conhecidos hoje. Para as ameaças de segurança do amanhã, é recomendável exibir a segurança como um processo contínuo para aumentar os custos e reduzir a taxa de sucesso dos adversários direcionados ao seu ambiente.

Proteger o acesso privilegiado é importante para estabelecer garantias de segurança para seus ativos de negócios. No entanto, ele deve fazer parte de um programa de segurança completo que fornece garantias de segurança contínuas. Este programa deve incluir elementos como:

* Política
* Operações
* Segurança de informações
* Servidores
* Aplicativos
* Computadores
* Dispositivos
* Malha de nuvem

Recomendamos as seguintes práticas quando você estiver gerenciando contas de acesso privilegiado:

* Garantir que os administradores estejam fazendo seus negócios diários como usuários sem privilégios
* Conceder acesso privilegiado somente quando necessário e removê-lo posteriormente (just-in-time)
* Manter logs de atividade de auditoria relacionados a contas privilegiadas

Para obter mais informações sobre a criação de um roteiro de segurança completa, consulte [recursos de arquitetura de TI de nuvem da Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Para se envolver com os serviços da Microsoft para ajudá-lo a implementar qualquer parte do seu roteiro, entre em contato com seu representante da Microsoft ou veja [criar defesas cibernéticos críticas para proteger sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Essa etapa final do roteiro Secured Privileged Access inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-ad"></a>Examinar funções de administrador no Azure AD

Determine se as funções de administrador internas atuais do Azure AD ainda estão atualizadas e certifique-se de que os usuários estão apenas com as funções de que precisam. Com o Azure AD, você pode atribuir administradores separados para atender a funções diferentes. Para obter mais informações, consulte [atribuindo funções de administrador no Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Analise os usuários que têm administraçõa dos dispositivos unidos do Azure AD

Para obter mais informações, consulte [Como configurar dispositivos híbridos unidos do Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Reveja os membros de [funções internas do administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Ignore esta etapa se você não estiver usando o Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Valiar o plano de resposta a incidentes

Para melhorar o seu plano, a Microsoft recomenda que você valide regularmente seu que está funcionando conforme o esperado:

* Passe pelo seu roteiro existente para ver o que foi perdido
* Com base na análise de postmortem, revisar as novas práticas existentes ou defini-las
* Verifique se o plano e as práticas de resposta a incidentes atualizados estão distribuídos em toda a sua organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure 

Determine se você precisa [transferir a propriedade de uma assinatura do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Vigilância": o que fazer em caso de emergência

![Contas para acesso à interrupção de emergência](./media/directory-admin-roles-secure/emergency.jpeg)

1. Notifique os gerentes-chave e os responsáveis pela segurança com informações sobre o incidente.

2. Analise o guia estratégico de ataque.

3. Acesse a combinação de nome de usuário e senha da conta "interrupção" para entrar no Azure AD.

4. Obtenha ajuda da Microsoft ao [abrir uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Examine os [relatórios de entrada do Azure AD](../reports-monitoring/overview-reports.md). Pode haver algum tempo entre um evento que ocorre e quando ele é incluído no relatório.

6. Para ambientes híbridos, se sua infraestrutura local federada e seu servidor de AD FS não estiverem disponíveis, você poderá alternar temporariamente da autenticação federada para usar a sincronização de hash de senha. Essa opção reverte a Federação de domínio de volta para a autenticação gerenciada até que o servidor de AD FS se torne disponível.

7. Monitorar o email quanto a contas com privilégios.

8. Certifique-se de salvar os backups de logs relevantes para investigação forense e investigação jurídica.

Para obter mais informações sobre como o Microsoft Office 365 trata os incidentes de segurança, consulte [gerenciamento de incidentes de segurança no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Perguntas frequentes: respostas para proteger o acesso privilegiado  

**P:** O que fazer se eu ainda não implementei os componentes de acesso seguro?

**Resposta:** Defina no mínimo duas contas de vigilância, atribua o MFA a suas contas de administrador privilegiado e separe as contas de usuário das contas de administrador Global.

**P:** Após uma violação, qual é o problema superior que precisa ser abordados primeiro?

**Resposta:** Certifique-se de que você está exigindo a autenticação mais forte para indivíduos altamente expostos.

**P:** o que acontece se nosso administradores com privilégios forem desativados?

**Resposta:** Crie uma conta de administrador global que sempre seja mantida atualizada.

**P:** O que acontece se houver apenas um administrador global e eles não puderem ser acessados?

**Resposta:** Use uma de suas contas de vigilância para obter acesso privilegiado imediato.

**P:** Como posso proteger administradores dentro da minha organização?

**Resposta:** Dê acesso aos administradores para seus negócios diários como usuários “não privilegiados” padrão.

**P:** Quais são as práticas recomendadas para a criação de contas de administrador no Azure AD?

**Resposta:** Reserva com privilégios de acesso para as tarefas administrativas específicas.

**P:** quais ferramentas existem para reduzir o acesso de administrador persistentes?

**Resposta:** funções de administrador Privileged Identity Management (PIM) e Azure AD.

**P:** Qual é a posição da Microsoft sobre a sincronização de contas de administrador no Azure AD?

**Resposta:** As contas de administrador da camada 0 são usadas somente para contas do AD local. Essas contas normalmente não são sincronizadas com o Azure AD na nuvem. As contas de administrador da camada 0 incluem contas, grupos e outros ativos que têm controle administrativo direto ou indireto da floresta Active Directory local, domínios, controladores de domínio e ativos.

**P:** Como podemos impedir que os administradores atribuam acesso aleatório de administrador no portal?

**Resposta:** Use contas sem privilégios para todos os usuários e a maioria dos administradores. Inicie desenvolvimento de um volume da organização para determinar quais contas de administrador devem ser privilegiadas. E o monitor para usuários administrativos recém-criados.

## <a name="next-steps"></a>Próximas etapas

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) - Produtos e serviços de nuvem de recursos de segurança da Microsoft

* [Microsoft Trust Center - Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – conjunto abrangente da Microsoft de ofertas de conformidade para serviços de nuvem

* [Orientação sobre como fazer uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -gerenciar requisitos de segurança e conformidade para os serviços de nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros serviços online da Microsoft

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) - O Microsoft Intune oferece recursos de gerenciamento de dispositivo móvel, gerenciamento de aplicativo móvel e gerenciamento de PC na nuvem.

* [Microsoft Dynamics 365 security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – O Dynamics 365 é a solução baseada em nuvem da Microsoft que unifica o gerenciamento de relacionamento com clientes (CRM) e recursos de planejamento de recursos empresariais (ERP).
