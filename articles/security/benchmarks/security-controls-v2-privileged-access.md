---
title: Benchmark de segurança do Azure v2-acesso privilegiado
description: Acesso privilegiado do benchmark de segurança do Azure v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059148"
---
# <a name="security-control-privileged-access"></a>Controle de segurança: acesso privilegiado

O acesso privilegiado abrange controles para proteger o acesso privilegiado ao seu locatário e recursos do Azure. Isso inclui uma variedade de controles para proteger seu modelo administrativo, contas administrativas e estações de trabalho de acesso privilegiado contra riscos deliberados e inadvertidos.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: proteger e limitar os administradores globais

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Os usuários atribuídos à função de Administrador global podem ler e modificar todas as configurações administrativa em sua organização do Azure AD. Limite o número de suas contas de administrador global do Azure para não mais do que duas para cada assinatura. As funções internas mais críticas no Azure AD são o administrador global e o administrador da função com privilégios, pois os usuários atribuídos a essas duas funções podem delegar funções de administrador:
- Administrador global/administração da empresa: os usuários com essa função têm acesso a todos os recursos administrativos do Azure AD, bem como serviços que usam identidades do Azure AD.

- Administrador de função com privilégios: os usuários com essa função podem gerenciar atribuições de função no Azure AD, bem como dentro de Azure AD Privileged Identity Management (PIM). Além disso, essa função permite o gerenciamento de todos os aspectos do PIM e das unidades administrativas.

Observação: você pode ter outras funções críticas que precisam ser governadas se você usar funções personalizadas com determinadas permissões privilegiadas atribuídas. E você também pode querer aplicar controles semelhantes à conta de administrador de ativos de negócios críticos.  

Você pode habilitar o acesso privilegiado JIT (just-in-time) aos recursos do Azure e ao Azure AD usando o Azure AD Privileged Identity Management (PIM). O JIT concede permissões temporárias para executar tarefas privilegiadas somente quando os usuários precisam dela. O PIM também pode gerar alertas de segurança quando há atividade suspeita ou não segura em sua organização do Azure AD.

Observação: alguns serviços do Azure, como o Azure SQL, dão suporte à autenticação de usuário local além da autenticação do Azure AD. Esse tipo de autenticação de usuário local não é gerenciado por meio do Azure AD. Você precisará gerenciar esses usuários separadamente.

- [Permissões de função de administrador no Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md)

- [Usar alertas de segurança do Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Como obter uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Usar a central de segurança do Azure para monitorar a identidade e o acesso](../../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: revisar e reconciliar o acesso do usuário regularmente

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| PA-2 | 4,1, 16,9, 16,10 | AC-2 |

Revise as contas de usuário e acesse os direitos de acesso regularmente para garantir que as contas de usuário e seu acesso sejam válidos. 

Use as revisões de identidade e acesso do Azure AD para gerenciar associações de grupo, acesso a aplicativos empresariais e atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar o fluxo de trabalho de relatório de revisão de acesso para facilitar o processo de revisão.

Para usuários administrativos no nível de carga de trabalho e de serviço do Azure, uma revisão de usuário e acesso mais frequente deve ser realizada. Você também pode usar os alertas de segurança do Azure Privileged Identity Management para detectar quando contas de administrador são criadas e para localizar contas de administrador que estão obsoletas ou configuradas incorretamente. 

Observação: alguns serviços do Azure, como o Azure SQL, dão suporte a usuários locais que não são gerenciados por meio do Azure AD. Você precisará gerenciar esses usuários separadamente.

- [Como obter uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Usar a central de segurança do Azure para monitorar a identidade e o acesso](../../security-center/security-center-identity-access.md)

- [Usar alertas de segurança do Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Alinhar responsabilidades administrativas entre equipes](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Entender os relatórios do Azure AD](/azure/active-directory/reports-monitoring/)

- [Como usar as revisões de identidade e acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management-revisar o acesso às funções do Azure AD](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Central de segurança do Azure-monitorar identidade e acesso](../../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: configurar uma conta de acesso de emergência no Azure AD

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve garantir que as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência sejam mantidas seguras e conhecidas apenas para indivíduos que estão autorizados a usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [SOC (Centro de Operações de Segurança)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: automatizar o fluxo de trabalho de solicitação de acesso e identidade do Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| PA-4 | N/D | AC-2, AC-5, PM-10 |

Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso do Azure, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios.  

- [O que é o gerenciamento de direitos do AD do Azure](../../active-directory/governance/entitlement-management-overview.md)

- [Como configurar o processo de solicitação e aprovação de acesso](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: usar computadores altamente protegidos para tarefas administrativas

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

Estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviço críticos. Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas:
- Use Azure Active Directory, a ATP (proteção avançada contra ameaças) do Microsoft defender e/ou Microsoft Intune para implantar uma estação de trabalho de usuário segura e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas centralmente para impor configuração segura, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede. 

- Use o recurso de bastiões do Azure para um caminho seguro para acessar suas máquinas virtuais por meio de RDP ou SSH. A bastiões do Azure é um serviço PaaS totalmente gerenciado que pode ser provisionado por rede virtual sem criar uma nova rede virtual. 

- [Entenda as estações de trabalho seguras e gerenciadas pelo Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Implantar uma estação de trabalho segura e gerenciada pelo Azure](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Usar o host de bastiões do Azure](../../bastion/bastion-create-host-portal.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [SOC (Centro de Operações de Segurança)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: atribuir privilégios a recursos usando o Azure RBAC

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| PA-6 | 14,6 | AC-2, AC-3 |

O RBAC (controle de acesso baseado em função) do Azure permite que você gerencie privilégios para o acesso a recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como CLI do Azure, Azure PowerShell ou portal do Azure. 

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md)

- [Como configurar o RBAC no Azure](../../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: escolha o processo de aprovação para o suporte da Microsoft

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

Em cenários de suporte em que a Microsoft precisa acessar dados do cliente, Sistema de Proteção de Dados do Cliente fornece um recurso para que você reveja explicitamente e aprove ou rejeite cada solicitação de acesso a dados do cliente.

- [Entender Sistema de Proteção de Dados do Cliente](../fundamentals/customer-lockbox-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

