---
title: Controle de Segurança do Azure - Controle de Identidade e Acesso
description: Controle de Identidade e Controle de Acesso de Controle de Segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934423"
---
# <a name="security-control-identity-and-access-control"></a>Controle de Segurança: Controle de Identidade e Acesso

As recomendações de gerenciamento de identidade e acesso se concentram em abordar questões relacionadas ao controle de acesso baseado em identidade, bloquear o acesso administrativo, alertar sobre eventos relacionados à identidade, comportamento anormal da conta e controle de acesso baseado em papéis.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.1 | 4.1 | Cliente |

O Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo Azure AD PowerShell para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Como obter um papel de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.2 | 4.2 | Cliente |

O Azure AD não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, o que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços de marketplace que podem usar senhas padrão.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use contas administrativas dedicadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.3 | 4.3 | Cliente |

Crie procedimentos operacionais padrão em torno do uso de contas administrativas dedicadas. Use o Azure Security Center Identity and Access Management para monitorar o número de contas administrativas.

Você também pode habilitar um Just-In-Time / Just-Enough-Access usando funções privilegiadas de gerenciamento de identidade privilegiada do Azure AD para serviços Microsoft e gerenciador de recursos do Azure. 

Saiba Mais:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use o single sign-on (SSO) com o Azure Active Directory

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.4 | 4.4 | Cliente |

Sempre que possível, use o SSO do Azure Active Directory em vez de configurar credenciais individuais por serviço. Use as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Entenda o SSO com o Azure AD:

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Use autenticação multifatorial para todos os acessos baseados no Azure Active Directory

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Cliente |

Habilite o Azure AD MFA e siga as recomendações de gerenciamento de identidade e acesso do Azure Security Center.

Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (Estações de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Cliente |

Use PAWs (estações de trabalho de acesso privilegiado) com MFA configurado para fazer login e configurar recursos do Azure.

Saiba mais sobre estações de trabalho de acesso privilegiado:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como ativar o MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registre e alerte sobre atividades suspeitas de contas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.7 | 4.8, 4.9 | Cliente |

Use relatórios de segurança do Azure Active Directory para geração de registros e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Use o Azure Security Center para monitorar a atividade de identidade e acesso.

Como identificar usuários do Azure AD sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a identidade e a atividade de acesso dos usuários no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerencie os recursos do Azure apenas em locais aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.8 | 11.7 | Cliente |

Use locais nomeados de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de faixas de endereços IP ou países/regiões.

Como configurar locais nomeados no Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9: Use o Diretório Ativo do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Cliente |

Use o Azure Active Directory (AAD) como sistema central de autenticação e autorização. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também armazena sais, hashes e armazena com segurança as credenciais do usuário.

Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e conciliar regularmente o acesso do usuário

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3,10 | 16.9, 16.10 | Cliente |

O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use o Azure Identity Access Reviews para gerenciar eficientemente membros de grupo, acesso a aplicativos corporativos e atribuições de função. O acesso do usuário pode ser revisto regularmente para garantir que apenas os Usuários certos tenham acesso contínuo. 

Relatório sadia do Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as avaliações de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitortenta acessar contas desativadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.11 | 16.12 | Cliente |

Você tem acesso às fontes de registro de atividade, auditoria e evento de risco do Azure AD, que permitem que você se integre a qualquer ferramenta De monitoramento/SIEM.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuários do Azure Active Directory e enviando os logs de auditoria e logs de login para um Espaço de Trabalho do Log Analytics. Você pode configurar os alertas desejados no Espaço de Trabalho do Log Analytics.

Como integrar os Logs de Atividades do Azure no Monitor do Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login da conta

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.12 | 16.13 | Cliente |

Use os recursos de proteção de risco e identidade do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas às identidades dos usuários. Você também pode ingerr dados no Azure Sentinel para mais investigações.

Como ver os logins arriscados do Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Forneça à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.13 | 16 | Cliente |

Em cenários de suporte onde a Microsoft precisa acessar os dados do cliente, o Customer Lockbox fornece uma interface para você revisar e aprovar ou rejeitar solicitações de acesso a dados do cliente.

Entenda o Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>Próximas etapas

Veja o próximo controle de segurança: [Proteção de dados](security-control-data-protection.md)
