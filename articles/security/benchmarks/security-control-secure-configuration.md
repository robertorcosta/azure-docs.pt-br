---
title: Controle de Segurança Do Azure - Configuração segura
description: Configuração segura do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bfa16f59155e420209ab6370056a7c612b5327e4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759048"
---
# <a name="security-control-secure-configuration"></a>Controle de segurança: configuração segura

Estabelecer, implementar e gerenciar ativamente (rastrear, relatar, corrigir) a configuração de segurança dos recursos do Azure, a fim de evitar que os invasores explorem serviços e configurações vulneráveis.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabeleça configurações seguras para todos os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.1 | 5.1 | Cliente |

Use aliases de diretiva do Azure para criar políticas personalizadas para auditar ou impor a configuração de seus recursos do Azure. Você também pode usar definições de diretiva azure incorporadas.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam / excedam os requisitos de segurança para sua organização.

Você também pode usar recomendações do Azure Security Center como uma linha de base de configuração segura para seus recursos do Azure.

- [Como visualizar aliases disponíveis da Política Do Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Crie e gerencie políticas para impor a conformidade](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Exportação única e multi-recursos para um modelo no portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Recomendações de segurança - um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.2 | 5.1 | Cliente |

Use as recomendações do Azure Security Center para manter configurações de segurança em todos os recursos de computação.  Além disso, você pode usar imagens personalizadas do sistema operacional ou configuração do Estado de Automação do Azure para estabelecer a configuração de segurança do sistema operacional exigida pela sua organização.

- [Como monitorar as recomendações do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Recomendações de segurança - um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Visão geral da configuração do estado de automação do Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Carregue um VHD e use-o para criar novas VMs windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Criar uma VM Linux usando um disco personalizado com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras através de seus recursos do Azure.  Além disso, você pode usar os modelos do Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos pela sua organização. 

- [Compreender os efeitos do Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Criar e gerenciar políticas para impor a conformidade](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Visão geral dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Compartilhado |

Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos recursos de computação do Azure.  Além disso, você pode usar modelos do Azure Resource Manager, imagens personalizadas do sistema operacional ou configuração do Estado de Automação do Azure para manter a configuração de segurança do sistema operacional exigida pela sua organização.   Os modelos de máquinas virtuais da Microsoft combinados com a Configuração de Estado Desejado da Automação do Azure podem ajudar a atender e manter os requisitos de segurança. 

Além disso, observe que as Imagens de MáquinaVirtual do Azure Marketplace publicadas pela Microsoft são gerenciadas e mantidas pela Microsoft. 

- [Como implementar as recomendações de avaliação de vulnerabilidades do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Como criar uma máquina virtual do Azure a partir de um modelo do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Visão geral da configuração do estado de automação do Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Crie uma máquina virtual do Windows no portal Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informações sobre como baixar o modelo vm](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar com segurança a configuração dos recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.5 | 5,3 | Cliente |

Use o Azure DevOps para armazenar e gerenciar seu código com segurança, como políticas personalizadas do Azure, modelos do Azure Resource Manager e scripts de configuração de estado desejado. Para acessar os recursos gerenciados no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança incorporados ou grupos definidos no Azure Active Directory (Azure AD) se integrado com OZure DevOps ou Active Directory se integrado ao TFS.

- [Como armazenar código no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar com segurança imagens personalizadas do sistema operacional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.6 | 5,3 | Cliente |

Se usar imagens personalizadas, use o RBAC (Role-Based Access Control, controle de acesso baseado em função) para garantir que apenas usuários autorizados possam acessar as imagens. Usando uma Galeria de Imagens Compartilhadas, é possível compartilhar suas imagens com diferentes usuários, entidades de serviço ou grupos do AD dentro de sua organização.  Para imagens de contêiner, armazene-as no Registro de Contêineres do Azure e aproveite o RBAC para garantir que apenas usuários autorizados possam acessar as imagens.  

- [Entenda o RBAC no Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Entenda o RBAC para Registro de Contêineres](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Como configurar o RBAC no Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Visão geral da galeria de imagens compartilhadas](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Defina e implemente configurações de segurança padrão para os recursos do Azure usando a Diretiva Do Azure. Use aliases de diretiva do Azure para criar políticas personalizadas para auditar ou impor a configuração de rede de seus recursos do Azure. Você também pode fazer uso de definições de políticas incorporadas relacionadas aos seus recursos específicos.  Além disso, você pode usar o Azure Automation para implantar alterações de configuração.

- [Como configurar e gerenciar a Política do Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como usar aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração para sistemas operacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

A Configuração do Estado de Automação do Azure é um serviço de gerenciamento de configuração para os álos dsc (Desired State Configuration, configuração do estado desejado) em qualquer datacenter na nuvem ou no local. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado. 

- [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitoramento automatizado de configuração para recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Use o Azure Security Center para executar varreduras de linha de base para os recursos do Azure.  Além disso, use a Política do Azure para alertar e auditar as configurações de recursos do Azure.

- [Como remediar recomendações no Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.10 | 5.5 | Cliente |

Use o Azure Security Center para executar varreduras de linha de base para configurações de SISTEMA E Docker para contêineres.

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gerencie segredos do Azure com segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.11 | 13.1 | Cliente |

Use a Identidade de Serviço Gerenciado em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento secreto para seus aplicativos na nuvem.

- [Como se integrar com as identidades gerenciadas do Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Como criar um Cofre de Chaves](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Como fornecer autenticação do Key Vault com uma identidade gerenciada](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerencie identidades com segurança e automaticamente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.12 | 4.1 | Cliente |

Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permite que você se autentique em qualquer serviço que suporte a autenticação Azure AD, incluindo O Cofre de Chaves, sem quaisquer credenciais em seu código.

- [Como configurar identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição de credenciais não intencionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.13 | 18.1, 18.7 | Cliente |

Implementar o Scanner de Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o Scanner de Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Próximas etapas

- Veja o próximo Controle de Segurança: [Malware Defense](security-control-malware-defense.md)