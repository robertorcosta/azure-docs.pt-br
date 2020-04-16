---
title: Controle de Segurança do Azure - Gestão de Estoques e Ativos
description: Inventário de controle de segurança do Azure e gerenciamento de ativos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408373"
---
# <a name="security-control-inventory-and-asset-management"></a>Controle de Segurança: Gestão de Estoques e Ativos

As recomendações de inventário e gerenciamento de ativos se concentram em abordar questões relacionadas ao gerenciamento ativo (inventário, faixa e correção) de todos os recursos do Azure para que apenas os recursos autorizados sejam acessados e os recursos não autorizados e não gerenciados sejam identificados e removidos.

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: Use a solução automatizada de detecção de ativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Cliente |

Use o Gráfico de Recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos etc.) dentro de sua assinatura(s).  Garanta permissões apropriadas (leia) em seu inquilino e enumeratodas as assinaturas do Azure, bem como recursos dentro de suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos via Resource Graph, é altamente recomendável criar e usar os recursos do Azure Resource Manager daqui para frente.

- [Como criar consultas com o Gráfico de Recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Como ver suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entenda o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.2 | 1.5 | Cliente |

Aplique tags aos recursos do Azure que dão metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos não autorizados do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.3 | 1.6 | Cliente |

Use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.4 | 2.1 | Cliente |

Crie um inventário de recursos aprovados do Azure e software aprovado para recursos computacionais de acordo com nossas necessidades organizacionais.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos não aprovados do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.5 | 2.3, 2.4 | Cliente |

Use a Política do Azure para colocar restrições sobre o tipo de recursos que podem ser criados em sua assinatura(s).

Use o Gráfico de Recursos do Azure para consultar/descobrir recursos dentro de sua assinatura(s).  Certifique-se de que todos os recursos do Azure presentes no ambiente sejam aprovados.

- [Como configurar e gerenciar a Política do Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitore aplicativos de software não aprovados em recursos computacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.6 | 2.3, 2.4 | Cliente |

Use o inventário da máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em Máquinas Virtuais. Nome do software, versão, publisher e tempo de atualização estão disponíveis no portal Azure. Para ter acesso à data de instalação e outras informações, habilite os diagnósticos em nível de hóspedes e traga os Logs de Eventos do Windows em um Espaço de Trabalho do Log Analytics.

- [Como ativar o inventário da máquina virtual do Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.7 | 2.5 | Cliente |

Use o Monitoramento de Integridade de Arquivos do Azure Security Center (Rastreamento de alterações) e o inventário de máquinas virtuais para identificar todos os softwares instalados em Máquinas Virtuais. Você pode implementar seu próprio processo para remover software não autorizado. Você também pode usar uma solução de terceiros para identificar software não aprovado.

- [Como usar o Monitoramento de Integridade de Arquivos](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Entenda o rastreamento de alterações do Azure](https://docs.microsoft.com/azure/automation/change-tracking)

- [Como ativar o inventário de máquinas virtuais do Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8: Use apenas aplicativos aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6,8 | 2.6 | Cliente |

Use controles de aplicativos adaptativos do Azure Security Center para garantir que apenas softwares autorizados seja executado e que todo o software não autorizado seja impedido de ser executado em Máquinas Virtuais do Azure.

- [Como usar os controles de aplicativos adaptativos do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9: Use apenas serviços azure aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.9 | 2.6 | Cliente |

Use a Política do Azure para restringir quais serviços você pode prover em seu ambiente.

- [Como configurar e gerenciar a Política do Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como negar um tipo de recurso específico com a Política do Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.10 | 2.7 | Cliente |

Use controles de aplicativos adaptativos do Azure Security Center para especificar a quais tipos de arquivo uma regra pode ou não se aplicar.

Implementar solução de terceiros se isso não atender ao requisito.

- [Como usar os controles de aplicativos adaptativos do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos usuários de interagir com o Azure Resource Manager

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.11 | 2,9 | Cliente |

Use o Azure Conditional Access para limitar a capacidade dos usuários de interagir com o Azure Resources Manager, configurando o "Bloquear acesso" para o aplicativo "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Azure Resources Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos usuários de executar scripts dentro de recursos computacionais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.12 | 2,9 | Cliente |

Dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts dentro dos recursos de computação do Azure.  Você também pode aproveitar os controles de aplicativos adaptativos do Azure Security Center para garantir que apenas softwares autorizados executem e todo o software não autorizado seja impedido de ser executado em Máquinas Virtuais do Azure.

- [Como controlar a execução de script susceptino do PowerShell em ambientes Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Como usar os controles de aplicativos adaptativos do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco física ou logicamente segregadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 6.13 | 2,9 | Cliente |

O software necessário para operações de negócios, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro de sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Firewall Azure ou Network Security Group.

- [Como criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com uma configuração de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>Próximas etapas

- Veja o próximo controle de [segurança: configuração segura](security-control-secure-configuration.md)