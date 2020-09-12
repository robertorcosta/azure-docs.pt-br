---
title: Controle de segurança do Azure – configuração segura
description: Configuração segura do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 347a63cc77c565d800328c19d1d543c2c9efafc0
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400085"
---
# <a name="security-control-secure-configuration"></a>Controle de segurança: configuração segura

Estabeleça, implemente e gerencie ativamente (acompanhe, relate e corrija) a configuração de segurança dos recursos do Azure para impedir que os invasores explorem serviços e configurações vulneráveis.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.1 | 5,1 | Cliente |

Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração dos recursos do Azure. Você também pode usar definições de Azure Policy internas.

Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua organização.

Você também pode usar recomendações da central de segurança do Azure como uma linha de base de configuração segura para seus recursos do Azure.

- [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Exportação única e de vários recursos para um modelo no portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Recomendações de segurança – um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.2 | 5,1 | Cliente |

Use as recomendações da central de segurança do Azure para manter as configurações de segurança em todos os recursos de computação.  Além disso, você pode usar imagens personalizadas do sistema operacional ou a configuração de estado da automação do Azure para estabelecer a configuração de segurança do sistema operacional exigido por sua organização.

- [Como monitorar as recomendações da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Recomendações de segurança – um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Visão geral da configuração do estado de automação do Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Carregar um VHD e usá-lo para criar novas VMs do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Criar uma VM Linux usando um disco personalizado com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.  Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização. 

- [Entender Azure Policy efeitos](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Criar e gerenciar políticas para impor a conformidade](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Visão geral de modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Compartilhado |

Siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em seus recursos de computação do Azure.  Além disso, você pode usar modelos de Azure Resource Manager, imagens do sistema operacional personalizado ou configuração de estado da automação do Azure para manter a configuração de segurança do sistema operacional exigido pela sua organização.   Os modelos de máquina virtual da Microsoft combinados com a configuração de estado desejado da automação do Azure podem ajudar a atender e a manter os requisitos de segurança. 

Além disso, observe que as imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são gerenciadas e mantidas pela Microsoft. 

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Como criar uma máquina virtual do Azure com base em um modelo de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Visão geral da configuração do estado de automação do Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Criar uma máquina virtual do Windows no portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informações sobre como baixar o modelo de VM](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.5 | 5,3 | Cliente |

Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos de Azure Resource Manager e scripts de configuração de estado desejado. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps, ou Active Directory se integrado ao TFS.

- [Como armazenar código no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.6 | 5,3 | Cliente |

Se usar imagens personalizadas, use o controle de acesso baseado em função do Azure (RBAC do Azure) para garantir que somente usuários autorizados possam acessar as imagens. Usando uma Galeria de Imagens Compartilhadas, é possível compartilhar suas imagens com diferentes usuários, entidades de serviço ou grupos do AD dentro de sua organização.  Para imagens de contêiner, armazene-as no registro de contêiner do Azure e aproveite o RBAC do Azure para garantir que somente usuários autorizados possam acessar as imagens.  

- [Entender o RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Entender o RBAC do Azure para registro de contêiner](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Como configurar o RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Visão geral da galeria de imagens compartilhadas](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Defina e implemente configurações de segurança padrão para recursos do Azure usando Azure Policy. Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos do Azure. Você também pode fazer uso de definições de política internas relacionadas aos seus recursos específicos.  Além disso, você pode usar a automação do Azure para implantar alterações de configuração.

- [Como configurar e gerenciar o Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como usar aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

A configuração de estado de automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado. 

- [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Use a central de segurança do Azure para executar verificações de linha de base para os recursos do Azure.  Além disso, use Azure Policy para alertar e auditar configurações de recursos do Azure.

- [Como corrigir recomendações na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.10 | 5.5 | Cliente |

Use a central de segurança do Azure para executar verificações de linha de base para configurações do sistema operacional e do Docker para contêineres.

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.11 | 13.1 | Cliente |

Use Identidade de Serviço Gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

- [Como integrar com identidades gerenciadas do Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Como criar um Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Como autenticar-se no Key Vault](../../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso de Key Vault](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.12 | 4.1 | Cliente |

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

- [Como configurar identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7,13 | 18,1, 18,7 | Cliente |

Implemente o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Próximas etapas

- Consulte o próximo controle de segurança:  [defesa contra malware](security-control-malware-defense.md)
