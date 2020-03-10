---
title: Práticas recomendadas para acesso de administrador seguro – Azure AD | Microsoft Docs
description: Certifique-se de que as contas administrativas de acesso e administração da sua organização estão seguras. Para arquitetos de sistema e profissionais de TI que configurarem o Azure AD, Azure e serviços Online da Microsoft.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c46facb2d43137175730bf04fea0efec9c1ecbd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389933"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Proteger o acesso privilegiado para implantações de nuvem híbrida no Azure AD

A segurança da maioria ou todos os ativos de negócios da organização moderna depende a integridade das contas com privilégios que administram e gerenciam os sistemas de TI. Atores mal-intencionados, incluindo ataques cibernéticos geralmente são destinados contas de administrador e a outros elementos de acesso privilegiado ao tentar obter rapidamente o acesso a dados confidenciais e sistemas usando ataques de roubo de credenciais. Para serviços de nuvem, prevenção e resposta são as responsabilidades conjuntas do provedor de serviços de nuvem e do cliente. Para obter mais informações sobre as ameaças mais recentes aos pontos de extremidade e à nuvem, consulte o [Relatório de Inteligência de Segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report). Este artigo pode ajudá-lo a desenvolver um roteiro para fechar as lacunas entre os planos atuais e as diretrizes descritas aqui.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com os padrões e conformidade normativa. Saiba mais sobre como a equipe de resposta a incidentes globais da Microsoft atenua os efeitos de ataques contra serviços de nuvem e como a segurança é criada em produtos comerciais da Microsoft e serviços de nuvem no [Microsoft Trust Center - Segurança](https://www.microsoft.com/trustcenter/security)e destinos de conformidade da Microsoft em [Microsoft Trust Center - Conformidade](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Para a maioria das organizações, a segurança dos ativos de negócios da organização moderna depende da integridade das contas com privilégios que administram e gerenciam os sistemas de TI. Invasores virtuais se concentram em acesso privilegiado aos sistemas de infraestrutura (como o Active Directory e Azure Active Directory) para obter acesso a dados confidenciais da empresa. 

Abordagens tradicionais se concentram em proteger os pontos de entrada e saída de uma rede de perímetro de segurança principal são menos eficazes devido ao aumento no uso de aplicativos SaaS e dispositivos pessoais na internet. A substituição natural para o perímetro de segurança de rede em uma empresa moderna complexa são os controles de autenticação e autorização na camada de identidade da organização.

Contas com privilégios administrativos estão efetivamente no controle desse novo "perímetro de segurança." É fundamental para proteger o acesso privilegiado, independentemente se o ambiente está no local, de nuvem ou híbrido no local e serviços hospedados na nuvem. Proteger o acesso administrativo contra determinados adversários exige que você adote uma abordagem completa e ponderada para isolar os sistemas de sua organização contra riscos. 

Proteger acesso privilegiado requer alterações a

* Processos, práticas administrativas e gerenciamento de conhecimento
* Componentes técnicos, como proteção de host, proteções de conta e gerenciamento de identidade

Este documento se concentra principalmente na criação de um roteiro para proteger as identidades e acesso que são gerenciados ou relatado no Azure AD, Microsoft Azure, Office 365 e outros serviços de nuvem. Para organizações que têm contas administrativas locai, consulte as diretrizes para acesso no local e híbrido gerenciado a partir do Active Directory em [Proteção de Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> As diretrizes neste artigo referem-se principalmente a recursos do Azure Active Directory que são incluídos em planos de Azure Active Directory Premium P1 e P2. O Azure Active Directory Premium P2 está incluído no conjunto de EMS E5 e Microsoft 365 E5. Este guia pressupõe que sua organização já tem as licenças do Azure AD Premium P2 adquiridas para os usuários. Se você não tiver essas licenças, algumas das orientações podem não se aplicar à sua organização. Além disso, ao longo deste artigo, o administrador global do termo é sinônimo de "administrador da empresa" ou "administrador locatário".

## <a name="develop-a-roadmap"></a>Desenvolver um roteiro 

A Microsoft recomenda que você crie e execute um roteiro para proteger o acesso privilegiado contra invasores virtuais. Você sempre pode ajustar seu roteiro para acomodar seus recursos existentes e os requisitos específicos dentro da sua organização. Cada estágio de nossos planos deverá aumentar o custo e a dificuldade de adversários de atacar o acesso privilegiado ao seu local, nuvem e ativos híbridos. A Microsoft recomenda os seguintes quatro estágios de roteiro: este roteiro recomendado programa primeiro as implementações mais eficazes e mais rápidas, com base nas experiências da Microsoft contra incidente e resposta de a ataques cibernéticos. Essas linhas do tempo para este roteiro são aproximadas.

![Etapas do roteiro com linhas do tempo](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Etapa 1 (24 a 48 horas): itens críticos, recomendamos que você faça imediatamente

* Etapa 2 (2 a 4 semanas): reduzir as técnicas de ataque usados com mais frequência

* Etapa 3 (1 a 3 meses): criar visibilidade e controle total da atividade do administrador

* Etapa 4 (seis meses adiante): continuar criando defesas para proteger ainda mais a plataforma de segurança

Essa estrutura de roteiro foi projetada para maximizar o uso de tecnologias da Microsoft que você já tiver implantado. Você também pode tirar proveito das tecnologias de segurança atuais e futuras e integrar ferramentas de segurança de outros fornecedores que você já tenha implantado ou que planeja implantar. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Etapa 1: itens críticos, recomendamos que você faça imediatamente

![Etapa 1 itens críticos para fazer primeiro](./media/directory-admin-roles-secure/stage-one.png)

A etapa 1 do roteiro do destina-se as tarefas críticas que são rápidas e fáceis de implementar. É recomendável que você faça alguns desses itens imediatamente dentro de 24 a 48 horas primeiro para garantir um nível básico de proteção ao acesso privilegiado. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir:

### <a name="general-preparation"></a>Preparação geral

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ative o Azure AD Privileged Identity Management

Se você ainda não tiver ativado o Azure AD Privileged Identity Management (PIM), você deve fazer isso em seu locatário de produção. Depois de ativar o Privileged Identity Management, você receberá mensagens de notificação por email para alterações da função de acesso privilegiado. Essas notificações fornecem aviso antecipado quando usuários adicionais são adicionados às funções altamente privilegiadas no seu diretório.

O Azure AD Privileged Identity Management está incluído no Azure AD Premium P2 ou EMS E5. Essas soluções ajudam a proteger o acesso a aplicativos e recursos no ambiente local e na nuvem. Se você ainda não tiver o Azure AD Premium P2 ou o EMS E5 e desejar avaliar mais os recursos mencionados neste roteiro, inscreva-se na [avaliação gratuita de 90 dias do Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Use essas licenças de avaliação para testar o Azure AD Privileged Identity Management e o Azure AD Identity Protection para monitorar a atividade usando o Azure AD avançados relatórios de segurança, auditoria e alertas.

Depois de ter ativado o Azure AD Privileged Identity Management:

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta que seja um administrador global do seu locatário de produção.

2. Para selecionar o locatário em que você deseja usar o Privileged Identity Management, selecione seu nome de usuário no canto superior direito do portal do Azure.

3. No menu portal do Azure, selecione **todos os serviços** e filtre a lista para **Azure ad Privileged Identity Management**.

4. Abra o Privileged Identity Management a partir de **Todos os serviços** liste e fixá-o ao seu painel.

A primeira pessoa a usar o Azure AD Privileged Identity Management em seu diretório, receberá automaticamente as funções **Administrador de segurança** e **Administrador com privilégios de função** no diretório. Somente os administradores com privilégios de função podem gerenciar atribuições de função de usuários do diretório do Azure AD. Além disso, depois de adicionar o Azure AD Privileged Identity Management, será exibido o assistente de segurança que orienta a experiência inicial de detecção e atribuição. Você pode sair do assistente sem fazer alterações adicionais no momento. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifique e categorize as contas que estão em funções altamente privilegiadas 

Depois de ativar o Azure AD Privileged Identity Management, exiba os usuários que estão nas funções de diretório administrador Global administrator, administrador de função Priveleged, administrador de Exchange Online e administrador SharePoint Online. Se você não tiver o Azure AD PIM em seu locatário, você pode usar o [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comece com a função de administrador global, pois essa função é genérica: um usuário ao qual é atribuída essa função de administrador tem as mesmas permissões em todos os serviços de nuvem para os quais sua organização assinou, independentemente de ter sido atribuída a essa função no Microsoft 365 Centro de administração, o portal do Azure ou usando o módulo do Azure AD para o Microsoft PowerShell. 

Remova todas as contas que não são mais necessárias nessas funções. Em seguida, categorize as contas restantes atribuídas às funções administrativas:

* Atribuídas individualmente a usuários administrativos e também podem ser usadas para fins não administrativos (por exemplo, email pessoal)
* Individualmente atribuídas para usuários administrativos e designadas para fins administrativos apenas
* Compartilhada por vários usuários
* Para cenários de acesso de emergência em situação crítica
* Para scripts automatizados
* Para usuários externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Defina pelo menos duas contas de acesso de emergência 

Certifique-se de não entrar em uma situação em que eles possam ser bloqueados inadvertidamente da administração do locatário do Azure AD devido à incapacidade de entrar ou ativar uma conta de usuário individual existente como administrador. Por exemplo, se a organização for federada a um provedor de identidade local, esse provedor de identidade pode estar indisponível para que os usuários não possam entrar no local. Você pode reduzir o impacto da falta acidental de acesso administrativo ao armazenar duas ou mais contas de acesso de emergência em seu locatário.

As contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado em um ambiente existente do Azure Active Directory. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas. As organizações devem garantir a meta de controlar e reduzir o uso da conta de emergência para somente esse momento durante o qual é necessário.

Avalie as contas que são atribuídas ou qualificadas para a função de administrador global. Se você não vir nenhuma conta somente em nuvem usando o domínio *.onmicrosoft.com (usado para acesso de emergência), crie-as. Para obter mais informações, consulte [Gerenciamento de contas administrativas de acesso de emergência no Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ative a autenticação multifator e registre todas as outras contas altamente privilegiadas de usuário único federado não administrador

Exija o Azure Multi-Factor Authentication (MFA) na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do Azure AD: Administrador Global, administrador de funções com privilégios, administrador do Exchange Online e administrador SharePoint Online. Use o guia para habilitar [Multi-factor Authentication (MFA) para suas contas de administrador](../authentication/howto-mfa-userstates.md) e certifique-se de que todos os usuários se registraram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Mais informações podem ser encontradas na etapa 2 e 3 do guia [Proteger o acesso a dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Etapa 2: reduzir as técnicas de ataque usados com mais frequência

![Estágio 2 mitigar ataques usados com frequência](./media/directory-admin-roles-secure/stage-two.png)

O estágio 2 do roteiro se concentra na redução das técnicas de ataque e roubo de credenciais usadas com mais frequência e pode ser implementado em aproximadamente 2 a 4 semanas. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Realizar um inventário de serviços, proprietários e administradores

Com o aumento de bring-your-own-device (BYOD) e políticas de trabalho de casa e o crescimento da conectividade sem fio em empresas, é importante monitorar quem está se conectando à sua rede. Uma auditoria de segurança efetiva geral revela dispositivos, aplicativos e programas em execução em sua rede que não são suportados pela equipe de TI e, portanto, potencialmente não seguros. Para obter mais informações, veja [visão geral de monitoramento e gerenciamento de segurança do Azure](../../security/fundamentals/management-monitoring-overview.md). Certifique-se de incluir todas as tarefas a seguir em seu processo de inventário. 

* Identifique os usuários que têm os serviços e funções administrativas, onde eles podem gerenciar.
* Use o Azure AD PIM para descobrir quais usuários em sua organização têm acesso de administrador no Azure AD, incluindo funções adicionais além daqueles listados na Etapa 1.
* Além das funções definidas no Azure Ad, o Office 365 vem com um conjunto de funções de administrador que você pode atribuir a usuários em sua organização. Cada função de administrador é mapeada para funções comerciais comuns e concede às pessoas em sua organização permissões para realizar tarefas específicas no [centro de administração Microsoft 365](https://admin.microsoft.com). Use o centro de administração Microsoft 365 para descobrir quais usuários em sua organização têm acesso de administrador ao Office 365, incluindo por meio de funções não gerenciadas no Azure AD. Para obter mais informações, consulte [Funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e [práticas recomendadas de segurança para o Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Execute o inventário em outros serviços de que sua organização depende, como Azure, Intune, ou Dynamics 365.
* Certifique-se que suas contas de administrador (contas que são usadas para fins de administração, não apenas contas de uso cotidiano dos usuários) tenham endereços de email anexados a elas e registraram para o Azure MFA ou usam a MFA no local.
* Pergunte aos usuários sua justificativa de negócios para acesso administrativo.
* Remova o acesso de administrador para as pessoas e serviços que não são necessários.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifique as contas da Microsoft em funções administrativas que precisam ser alternadas para contas de trabalho ou escolares

Às vezes, os administradores globais inicias para uma organização reutilizam suas credenciais de conta Microsoft existentes quando começaram usando o Azure AD. Essas contas da Microsoft devem ser substituídas por contas individuais baseadas em nuvem ou sincronizadas. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Garanta contas de usuário separadas e emails de encaminhamento para as contas de administrador global 

Contas de administrador global não devem ter endereços de email pessoais, uma vez que contas de email pessoais regularmente são capturadas por invasores virtuais. Para ajudar a separar os riscos de internet (ataques de phishing, navegação na web não intencional) de privilégios administrativos, criar contas dedicadas para cada usuário com privilégios administrativos. 

Se você ainda não tiver feito isso, crie contas separadas para que os usuários executem tarefas de administrador global, para certificar-se de que eles não abram emails inadvertidamente ou executem programas associados com suas contas de administrador. Certifique-se de que essas contas têm seu email encaminhado para uma caixa de correio do trabalho.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Certifique-se de que as senhas de contas administrativas foram alteradas recentemente

Certifique-se de que todos os usuários entraram em suas contas administrativas e suas senhas pelo menos uma vez nos últimos 90 dias. Além disso, certifique-se de que as contas compartilhadas na qual vários usuários sabem a senha tiveram suas senhas alteradas recentemente.

#### <a name="turn-on-password-hash-synchronization"></a>Ativar a sincronização de hash de senha

A sincronização de hash de senha é um recurso usado para sincronizar hashes de hashes da senha do usuário de uma instância do Active Directory local para uma instância do Microsoft Azure AD baseada em nuvem. Mesmo que você decida usar a federação com os Serviços de Federação do Active Directory (AD FS) ou outros provedores de identidade, é possível configurar a sincronização de hash de senha como um backup, caso a infraestrutura local, como ADFS Server ou AD, falhe ou torne-se temporariamente indisponível. Isso possibilita que usuários entrar no serviço usando a mesma senha que usam para entrar em sua instância do AD local. Além disso, permite a proteção de identidade para detectar credenciais comprometidas, comparando os hashes de senha com senhas conhecidas como compormetidas, se um usuário tiver utilizado o mesmo endereço de email e senha em outros serviços não estiverem conectados ao Azure AD.  Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Exigir autenticação multifator (MFA) para usuários em todas as funções privilegiadas, bem como os usuários expostos

O Azure AD recomenda que você exija autenticação multifator (MFA) para todos os usuários, incluindo administradores e todos os outros usuários que tivrem um impacto significativo se sua conta foi comprometida (por exemplo, gerentes financeiros). Isso reduz o risco de um ataque devido a uma senha comprometida.

Ativar:

* [MFA usando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) para todos os usuários em sua organização.

Se você usar o Windows Hello for Business, o requisito de MFA pode ser atendido usando o logon do Windows Hello. Para obter mais informações, consulte [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configurar o Identity Protection 

O Azure AD Identity Protection é um algoritmo de monitoramento baseado e relatórios de ferramenta que você pode usar para detectar possíveis vulnerabilidades que afetam as identidades da organização. Você pode configurar as respostas automatizadas a essas atividades suspeitas detectadas e tomar as devidas providências para resolvê-los. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obter o Office 365 Secure Score (se estiver usando o Office 365)

Proteger a pontuação mostra quais serviços do Office 365, você está usando (como OneDrive, SharePoint e Exchange) examina as configurações e as atividades e compara-as com à uma linha de base estabelecida pela Microsoft. Você obterá uma pontuação com base em como está alinhado às práticas recomendadas de segurança. Qualquer pessoa que tenha permissões de administrador (administrador global ou uma função de administrador personalizada) para uma assinatura do Office 365 Business Premium ou Enterprise pode acessar a Pontuação Segura em [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Verifique as diretrizes de segurança e conformidade do Office 365 (se estiver usando o Office 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem sobre como um cliente do Office 365 deve configurar o Office 365 e utilizar outros recursos do EMS. Em seguida, analise as etapas de 3 a 6 de como [proteger o acesso a dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e o guia de como [monitorar segurança e conformidade no Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configure o Office 365 Activity Monitoring (se estiver usando o Office 365)

Você pode monitorar como as pessoas na sua organização estão usando os serviços do Office 365, possibilitando que você identifique usuários que têm uma conta administrativa que podem precisar do acesso ao Office 365 devido a não entrar nesses portais. Para obter mais informações, consulte [relatórios de atividade no centro de administração do Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer os proprietários de plano de resposta de incidente/emergência

A execução de resposta a incidentes efetivamente é uma tarefa complexa. Portanto, o estabelecimento de uma capacidade de resposta a incidentes bem-sucedida requer planejamento substancial e recursos. É essencial que você continuamente a monitorar ataques cibernéticos e a estabelecer procedimentos para a prioridade de tratamento de incidentes. Métodos eficientes de coletar, analisar e relatar dados são vitais para criar relações e para estabelecer comunicação com outros grupos internos e planejar os proprietários. Para obter mais informações, consulte [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteja as contas as contas administrativas locais, se ainda não tiver feito isso

Se o seu locatário do Active Directory do Azure estiver sincronizado ao Active Directory no local, em seguida, siga as orientações em [Roteiro de Acesso Privilegiado à Segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Etapa 1. Isso inclui a criação de contas de administrador separada para usuários que precisam realizar tarefas administrativas do local, implantando estações de trabalho de acesso privilegiado para administradores do Active Directory e criar senhas de administrador local exclusivo para estações de trabalho e servidores.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário de assinaturas

Use o portal da Enterprise e o portal do Azure para identificar as assinaturas em sua organização que hospedam aplicativos de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover as contas da Microsoft de funções de administrador

As contas da Microsoft de outros programas, como o Xbox Live e Outlook não devem ser usadas como contas de administrador para assinaturas organizacionais. Remova o status do administrador de todas as contas da Microsoft e substitua por contas corporativas ou de estudante de Azure Active Directory (por exemplo, chris@contoso.com).

#### <a name="monitor-azure-activity"></a>Monitorar a atividade do Azure

O Log de Atividades do Azure fornece um histórico de eventos no nível da assinatura no Azure. Oferece informações sobre quem criou, atualizou ou excluiu quais recursos e quando fez isso. Para obter mais informações, consulte [Auditar e receber notificações sobre ações importantes em sua assinatura do Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="configure-conditional-access-policies"></a>Configurar políticas de acesso condicional

Prepare políticas de acesso condicional para aplicativos locais e hospedados na nuvem. Se você tiver usuários ingressados no local de trabalho, obtenha mais informações em [Configurando o acesso condicional local usando Azure Active Directory registro de dispositivo](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Etapa 3: criar visibilidade e controle total da atividade do administrador

![Estágio 3 assumir o controle da atividade do administrador](./media/directory-admin-roles-secure/stage-three.png)

A Etapa 3 amplia as atenuações da Etapa 2 e foi projetada para ser implementada em aproximadamente 1 a 3 meses. Essa etapa do roteiro de Acesso Privilegiado Seguro inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Concluir uma análise de acesso de usuários em funções de administrador

Mais usuários corporativos estão obtendo acesso privilegiado por meio de serviços de nuvem, que pode levar a uma plataforma não gerenciada cada vez maior. Isso pode incluir os usuários que se tornaram administradores globais do Office365, administradores de assinatura do Azure e os usuários que têm acesso administrativo para VMs ou via aplicativos SaaS. Como alternativa, as organizações fazer com que todos os funcionários, principalmente a administradores, lidem com transações de negócios diárias como usuários sem privilégios e só terem direitos de administrador conforme necessário. Uma vez que o número de usuários em funções de administrador pode crescer desde a adoção inicial, o acesso completo analisa para identificar e confirmar se cada usuário que é elegível para ativar os privilégios de administrador. 

Faça o seguinte:

* Determine quais usuários são administradores do Azure AD, habilite sob demanda, acesso de administração just-in-time e controles de segurança baseada em função.
* Converter os usuários que têm sem justificativa clara para acesso de administrador com privilégios para uma função diferente (se não houver função qualificada, remova-o).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continuar a distribuição de autenticação mais forte para todos os usuários 

Exija os executivos de pacote C, gerenciadores de alto nível, IT crítico e a equipe de segurança e outros usuários altamente expostos para autenticação moderna, forte, como Azure MFA ou o Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Use estações de trabalho dedicadas para a administração do Azure AD

Os invasores podem tentar contas com privilégios para obter acesso a dados e sistemas de uma organização para que possam interromper a integridade e a autenticidade dos dados, por meio de um código mal-intencionado que altera a lógica do programa ou espiona o administrador inserindo uma credencial. As Estações de Trabalho com Acesso Privilegiado (PAWs) fornecem um sistema operacional dedicado para as tarefas confidenciais protegidas contra ataques da Internet e vetores de ameaça. Separar essas tarefas confidenciais e contas das estações de trabalho de uso diário e dispositivos fornece uma ótima proteção contra ataques de phishing, aplicativo e vulnerabilidades do SO, vários ataques de representação e ataques de roubo de credenciais, como o registro de teclas pressionadas, Passagem de Hash e Passagem de Tíquete. Implantar estações de trabalho de acesso privilegiado, você pode reduzir o risco dos administradores de inserir credenciais de administrador, exceto em um ambiente de área de trabalho que foi protegido. Para saber mais, confira [Privileged Identity Management](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Analise as recomendações do Instituto Nacional de padrões e tecnologia para lidar com incidentes 

O Instituto Nacional de padrões e tecnologia (NIST) fornece diretrizes para tratamento de incidentes, particularmente para analisar dados relacionados ao incidente e determinar a resposta apropriada a cada incidente. Para obter mais informações, consulte [(NIST) o Computer Security Incident Handling Guide (SP 61 800, Revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Privileged Identity Management (PIM) para JIT a funções administrativas adicionais

Para o Azure Active Directory, use o recurso [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). Ativação de tempo limitado de funções privilegiadas funciona, permitindo que você:

* Ative os privilégios de administrador para executar uma tarefa específica
* Imponha o MFA durante o processo de ativação
* Use alertas para informar os administradores sobre alterações fora de banda
* Permita que os usuários manter certos privilégios, por um período de tempo configurado previamente
* Permita que os administradores de segurança descubram todas as identidades com privilégios, exiba relatórios de auditoria e crie análises de acesso para identificar cada usuário que é elegível para ativar os privilégios de administrador

Se você já estiver usando o Azure AD Privileged Identity Management, ajuste intervalos de tempo para privilégios de tempo especificado conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determine a exposição a protocolos com senha (se estiver usando o Exchange Online)

No passado, protocolos pressupõe-se presumiam que as combinações de nome de usuário/senha foram inseridas em dispositivos, contas de email, telefones e assim por diante. Mas, agora, com o risco de ataques cibernéticos na nuvem, recomendamos que você identifique todos os usuários potenciais que, se suas credenciais foram comprometidos, possam ser catastróficos para a organização e excluí-los da capacidade de entrar em seu email por meio de nome de usuário/senha implementando requisitos de autenticação fortes e acesso condicional. Você pode bloquear a [autenticação herdada usando o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Consulte os detalhes sobre [como bloquear a autenticação básica](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) por meio do Exchange Online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Conclua uma avaliação de revisão de funções para as funções do Office 365 (se estiver usando o Office 365)

Avalie se todos os usuários administradores estão nas funções corretas (exclua e reatribua de acordo com essa avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Examine a abordagem de gerenciamento de incidentes de segurança usada no Office 365 e compare com sua própria organização

Você pode fazer o download desse relatório de [gerenciamento de incidentes de segurança no Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a proteger as contas administrativas privilegiadas locais

Se o seu locatário do Azure Active Directory estiver sincronizado ao Active Directory no local, em seguida, siga as orientações em [Roteiro de Acesso Privilegiado à Segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Etapa 2. Isso inclui implantar Estações de trabalho de acesso privilegiado para todos os administradores, exigir MFA, usando apenas Just Enough Admin para manutenção do controlador de domínio, reduzindo a superfície de ataque de domínios, implantar o ATA para detecção de ataque.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitoramento integrado

A [Central de Segurança do Azure](../../security-center/security-center-intro.md) permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Faça o inventário de contas privilegiadas em Máquinas Virtuais hospedadas

Na maioria dos casos, você não precisa fornecer aos usuários permissões irrestritas a todos os recursos ou assinaturas do Azure. Você pode usar funções de administrador do Azure AD para separar as tarefas dentro de sua organização e conceder somente a quantidade de acesso a usuários que precisam para executar trabalhos específicos. Por exemplo, use as funções do Azure AD para permitir que um administrador gerencie apenas máquinas virtuais em uma assinatura, enquanto outro pode gerenciar banco de dados SQL dentro da mesma assinatura. Para obter mais informações, consulte [Introdução ao Controle de Acesso Baseado em Função no portal do Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar o PIM para funções de administrador do Azure AD

Use o Privileged Identity Management com as funções de administrador do Azure Ad para gerenciar, controlar e monitorar o acesso aos recursos do Azure. Use o PIM protege contas privilegiadas contra ataques cibernéticos, reduzindo o tempo de exposição de privilégios e aumente sua visibilidade sobre seu uso por meio de alertas e relatórios. Para obter mais informações, consulte [Gerenciar o acesso aos recursos do Azure com o Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Usar integrações do log do Azure para enviar logs relevantes do Azure para seus sistemas SIEM 

A integração de log do Azure permite que você integre logs brutos de recursos do Azure aos sistemas Security Information and Event Management (SIEM) da sua organização. [A Integração do Log do Azure](../../security/fundamentals/azure-log-integration-overview.md) coleta eventos do Windows de logs do Visualizador de Eventos do Windows e recursos do Azure de Logs de Atividades do Azure, Alertas da Central de Segurança do Azure e Logs de Diagnóstico do Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento do usuário para aplicativos conectados

O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem (SaaS) como Dropbox, Salesforce, ServiceNow e muito mais. Para saber mais, confira [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar proteção de informações

O MCAS permite que você investigue arquivos e defina políticas com base em rótulos de classificação da Proteção de Informações do Microsoft Azure permitindo maior visibilidade e controle de seus dados na nuvem. Verifique e classifique arquivos na nuvem e aplique rótulos de proteção de informações do Azure. Para obter mais informações, consulte [integração da Proteção de Informações do Microsoft Azure](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configurar o acesso condicional

Configure o acesso condicional com base em um grupo, local e sensibilidade de aplicativo para [aplicativos SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicativos conectados ao Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorar a atividade de aplicativos de nuvem conectados

Para garantir que o acesso dos usuários está protegido nos aplicativos conectados, recomendamos que você use o [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Ele protege o acesso corporativo a aplicativos, além de proteger suas contas de administrador, possibilitando:

* Aumentar a visibilidade e controle para aplicativos em nuvem
* Criar políticas de acesso, atividades e compartilhamento de dados
* Identificar automaticamente as atividades arriscadas, comportamentos anormais e ameaças
* Impedir o vazamento de dados
* Minimizar o risco e prevenção de ameaças automatizado e aplicação de políticas

O agente Cloud App Security SIEM integra Cloud App Security integra o Cloud App Security ao seu servidor SIEM para habilitar o monitoramento centralizado de atividades e alertas do Office 365. Ele é executado no servidor e recebe alertas e atividades de segurança do aplicativo de nuvem e transmite-as no servidor SIEM. Para obter mais informações, consulte [Integração SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Etapa 4: Continue criando defesas para uma postura de segurança mais proativa

![O estágio 4 adota uma postura de segurança proativa](./media/directory-admin-roles-secure/stage-four.png)

A Etapa 4 do roteiro do amplia a visibilidade de Etapa 3 e foi projetada para ser implementada em seis meses e posterior. Concluir roteiro ajuda a desenvolver proteções de fortes de acesso privilegiado contra ataques potenciais que atualmente são conhecidos e estão disponíveis. Infelizmente, as ameaças de segurança constantemente evoluem e mudam, portanto, é recomendável que você veja a segurança como um processo contínuo que é voltado para aumentar o custo e reduzir a taxa de sucesso de adversários direcionando o seu ambiente.

Proteger o acesso privilegiado é a primeira etapa crítica para estabelecer as garantias de segurança para ativos de negócios em uma organização moderna, mas não é a única parte de um programa de segurança completo que inclui elementos, como política, operações, informações segurança, servidores, aplicativos, computadores, dispositivos, malha da nuvem e outros componentes que fornecem garantias de segurança em andamento. 

Além de gerenciar suas contas de acesso privilegiado, recomendamos que você revise o seguinte em uma base contínua:

* Certifique-se de que os administradores estão fazendo seus negócios diários, como usuários sem privilégios.
* Apenas conceda acesso privilegiado quando necessário e removê-o posteriormente (just-in-time).
* Mantenha e analise as atividades de auditoria relacionadas a contas privilegiadas.

Para obter mais informações sobre a criação de um roteiro de segurança completa, consulte [recursos de arquitetura de TI de nuvem da Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Para obter mais informações sobre atrair os serviços da Microsoft para ajudá-lo em qualquer um desses tópicos, entre em contato com seu representante da Microsoft ou visite [ Construir defesas cibernéticas essenciais para proteger a sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Essa etapa final do roteiro Secured Privileged Access inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-active-directory"></a>Funções de administrador no Azure Active Directory 

Determine se as funções de administrador atuais internas do Azure AD ainda estarão atualizadas e certifique-se de que os usuários estão apenas em funções e delegações que precisam de permissões correspondentes. Usando o Azure AD, você pode designar administradores separados para atender a diferentes funções. Para saber mais informações, consulte [Atribuindo funções de administrador no Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Analise os usuários que têm administraçõa dos dispositivos unidos do Azure AD

Para obter mais informações, consulte [Como configurar dispositivos híbridos unidos do Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Reveja os membros de [funções internas do administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Se você estiver usando o Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Valiar o plano de resposta a incidentes

Para melhorar o seu plano, a Microsoft recomenda que você valide regularmente seu que está funcionando conforme o esperado:

* Passe pelo seu roteiro existente para ver o que foi perdido
* Com base na análise post mortem, revise ou defina as melhores práticas existentes
* Certifique-se de que seu plano de resposta a incidentes está atualizado e que as práticas recomendadas são distribuídas por toda a organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para as organizações a gerenciar o acesso do Azure 

Determine se você precisa [transferir a propriedade de uma assinatura do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Vigilância": o que fazer em caso de emergência

![Contas para acesso à interrupção de emergência](./media/directory-admin-roles-secure/emergency.jpeg)

1. Notifique os principais gerentes e executivos de segurança com informações pertinentes sobre o incidente.

2. Analise o guia estratégico de ataque. 

3. Acesse sua combinação de nome de usuário/senha de “vigilância” para entrar no Azure AD. 

4. Obtenha ajuda da Microsoft ao [abrir uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Examine os [relatórios de entrada do Azure AD](../reports-monitoring/overview-reports.md). Pode haver um atraso entre um evento que ocorrer e quando ele estiver incluído no relatório.

6. Para ambientes híbridos, se federado e seu servidor de AD FS não estiverem disponíveis, talvez seja necessário alternar temporariamente da autenticação federada para usar a sincronização de hash de senha. Isso reverte a Federação de domínio de volta para a autenticação gerenciada até que o servidor de AD FS se torne disponível.

7. Monitorar o email quanto a contas com privilégios.

8. Certifique-se de salvar os backups de logs relevantes para investigação forense e investigação jurídica.

Para obter mais informações sobre como o Microsoft Office 365 trata os incidentes de segurança, consulte [gerenciamento de incidentes de segurança no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Perguntas Frequentes: perguntas mais comuns que recebemos referente à proteção do acesso privilegiado  

**P:** O que fazer se eu ainda não implementei os componentes de acesso seguro?

**Resposta:** Defina no mínimo duas contas de vigilância, atribua o MFA a suas contas de administrador privilegiado e separe as contas de usuário das contas de administrador Global.

**P:** Após uma violação, qual é o problema superior que precisa ser abordados primeiro?

**Resposta:** Certifique-se de que você está exigindo a autenticação mais forte para indivíduos altamente expostos.

**P:** o que acontece se nosso administradores com privilégios forem desativados?

**Resposta:** Crie uma conta de admin Global que está sempre atualizada.

**P:** O que acontece se houver apenas um administrador global e não puder ser alcançado? 

**Resposta:** Use uma de suas contas de vigilância para obter acesso privilegiado imediato.

**P:** Como posso proteger administradores dentro da minha organização?

**Resposta:** Dê acesso aos administradores para seus negócios diários como usuários “não privilegiados” padrão.

**P:** Quais são as práticas recomendadas para a criação de contas de administrador no Azure AD?

**Resposta:** Reserva com privilégios de acesso para as tarefas administrativas específicas.

**P:** quais ferramentas existem para reduzir o acesso de administrador persistentes?

**Resposta:** funções de administrador Privileged Identity Management (PIM) e Azure AD.

**P:** Qual é a posição da Microsoft sobre a sincronização de contas de administrador no Azure AD?

**Resposta:** Contas de administrador de Camada 0 (incluindo contas, grupos e outros recursos que têm controle administrativo direto ou indireto da floresta do AD, domínios ou controladores de domínio e todos os ativos) de contas de administrador são usadas somente para contas locais do AD e são normalmente não são sincronizadas para Azure AD para a nuvem.

**P:** Como podemos impedir que os administradores atribuam acesso aleatório de administrador no portal?

**Resposta:** Use contas sem privilégios para todos os usuários e a maioria dos administradores. Inicie desenvolvimento de um volume da organização para determinar quais contas de administrador devem ser privilegiadas. E monitorar usuários administrativos recém-criados.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) - Produtos e serviços de nuvem de recursos de segurança da Microsoft

* [Microsoft Trust Center - Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – conjunto abrangente da Microsoft de ofertas de conformidade para serviços de nuvem

* [Orientação sobre como realizar uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - gerenciar requisitos de conformidade e segurança para serviços de nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros serviços online da Microsoft

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) - O Microsoft Intune oferece recursos de gerenciamento de dispositivo móvel, gerenciamento de aplicativo móvel e gerenciamento de PC na nuvem.

* [Microsoft Dynamics 365 security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – O Dynamics 365 é a solução baseada em nuvem da Microsoft que unifica o gerenciamento de relacionamento com clientes (CRM) e recursos de planejamento de recursos empresariais (ERP).
