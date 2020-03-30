---
title: Controle de Segurança Do Azure - Configuração segura
description: Configuração segura do controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934288"
---
# <a name="security-control-secure-configuration"></a>Controle de segurança: configuração segura

Estabelecer, implementar e gerenciar ativamente (rastrear, relatar, corrigir) a configuração de segurança dos recursos do Azure, a fim de evitar que os invasores explorem serviços e configurações vulneráveis.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.1 | 5.1 | Cliente |

Use a Diretiva Azure ou o Azure Security Center para manter configurações de segurança para todos os Recursos Do Azure.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.2 | 5.1 | Cliente |

Use a recomendação &quot;do Azure Security Center Para corrigir&quot; vulnerabilidades em configurações de segurança em suas Máquinas Virtuais para manter configurações de segurança em todos os recursos de computação.

Como monitorar as recomendações do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Como remediar as recomendações do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras através de seus recursos do Azure.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entenda os efeitos da política do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Compartilhado |

As imagens básicas do sistema operacional são gerenciadas e mantidas pela Microsoft.

No entanto, você pode aplicar as configurações de segurança exigidas pela sua organização usando modelos do Azure Resource Manager e/ou Configuração de Estado Desejado.

Como criar uma máquina virtual do Azure a partir de um modelo do Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Entenda a configuração de estado desejada para máquinas virtuais do Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.5 | 5,3 | Cliente |

Se usar definições de diretiva personalizadas do Azure, use OZure DevOps ou a Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação de Repos do Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.6 | 5,3 | Cliente |

Se usar imagens personalizadas, use o RBAC para garantir que apenas usuários autorizados possam acessar as imagens. Para imagens de contêiner, armazene-as no Registro de Contêineres do Azure e aproveite o RBAC para garantir que apenas usuários autorizados possam acessar as imagens.

Entenda o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Entenda o RBAC para registro de contêineres:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Como configurar o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração de sistema

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Use a diretiva do Azure para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções políticas.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração de sistema para sistemas operacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

Use extensões de computação do Azure, como PowerShell Desired State Configuration for Windows compute ou Linux Chef Extension for Linux.

Como instalar extensões de máquina virtual no Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitoramento automatizado de configuração para serviços do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Use o Azure Security Center para executar varreduras de linha de base para os recursos do Azure

Como remediar recomendações no Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.1 | 5.5 | Cliente |

Use o Azure Security Center para executar varreduras de linha de base para configurações de SISTEMA E Docker para contêineres.

Entenda as recomendações de contêineres do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança 

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.11 | 13.1 | Cliente |

Use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento secreto para seus aplicativos na nuvem.

Como se integrar com as identidades gerenciadas do Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre de Chaves:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer autenticação do Key Vault com uma identidade gerenciada:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.12 | 4.1 | Cliente |

Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permite que você se autentique em qualquer serviço que suporte a autenticação Azure AD, incluindo O Cofre de Chaves, sem quaisquer credenciais em seu código.

Como configurar identidades gerenciadas:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.13 | 13.3 | Cliente |

Implementar o Scanner de Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o Scanner de Credencial:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Próximas etapas

Veja o próximo controle de segurança: [Malware Defense](security-control-malware-defense.md)
