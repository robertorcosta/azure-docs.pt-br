---
title: Exemplos e modelos do Azure Lighthouse
description: Esses exemplos e os modelos do Azure Resource Manager mostram como integrar clientes para gerenciamento de recursos delegados do Azure e dão suporte a cenários do Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553675"
---
# <a name="azure-lighthouse-samples"></a>Exemplos do Azure Lighthouse

A tabela a seguir inclui links para os principais modelos do Azure Resource Manager para o Azure Lighthouse. Esses arquivos e muito mais também podem ser encontrados na [repositório de exemplos do Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Integrar clientes ao gerenciamento de recursos delegados do Azure

Fornecemos diferentes modelos para abordar diferentes cenários de integração. Escolha a opção que funciona melhor e modifique o arquivo de parâmetro para refletir seu ambiente. Para obter mais informações sobre como usar esses arquivos em sua implantação, confira [Integrar um cliente ao gerenciamento de recursos delegados do Azure](../how-to/onboard-customer.md).

| **Modelo** | **Descrição** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Integra uma assinatura de cliente ao gerenciamento de recursos delegados do Azure. Uma implantação separada deve ser executada para cada assinatura. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Integra um ou mais dos grupos de recursos do cliente ao gerenciamento de recursos delegados do Azure. Use **rgDelegatedResourceManagement** para um único grupo de recursos ou **multipleRgDelegatedResourceManagement** para integrar vários grupos de recursos na mesma assinatura. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Se você [publicou uma oferta de serviços gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md), pode opcionalmente usar esse modelo para integrar recursos para clientes que aceitaram a oferta. Os valores de marketplace no arquivo de parâmetros devem corresponder aos valores que você usou ao publicar sua oferta. |

Normalmente, uma implantação separada é necessária para cada assinatura que está sendo integrada, mas você também pode implantar modelos em várias assinaturas.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Implanta modelos do Azure Resource Manager entre várias assinaturas. |

## <a name="azure-policy"></a>Azure Policy

Estes exemplos mostram como usar o Azure Policy com assinaturas que foram integradas para o gerenciamento de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma marca (usando o efeito modificar) a uma assinatura delegada. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria de atribuições de delegação. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que habilita o diagnóstico nos recursos do Azure Key Vault em assinaturas delegadas (usando o efeito deployIfNotExists). Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para habilitar o diagnóstico em uma assinatura delegada e conecta todas as VMs Windows e Linux ao workspace do Log Analytics criado pela política. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma assinatura delegada. |

## <a name="azure-monitor"></a>Azure Monitor

Esses exemplos mostram como usar o Azure Monitor para criar alertas para assinaturas que foram integradas para o gerenciamento de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Este modelo cria um alerta do Azure e se conecta a um Grupo de Ações existente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Cria vários alertas de log com base em consultas Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Implanta um alerta em um locatário quando um usuário delega uma assinatura a um locatário gerenciador.|

## <a name="additional-cross-tenant-scenarios"></a>Cenários entre locatários adicionais

Esses exemplos ilustram várias tarefas que podem ser executadas em cenários de gerenciamento entre locatários.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Implanta contas de armazenamento em dois grupos de recursos diferentes.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Cria serviços de gerenciamento do Azure, vincula-os e implanta soluções adicionais. Para uma implantação de ponta a ponta, use o modelo **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Habilita e configura a Central de Segurança do Azure na assinatura de destino do Azure. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Implanta e habilita o Azure Sentinel em um workspace do Log Analytics existente em uma assinatura delegada. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Esses modelos permitem que você implante extensões de VM do Log Analytics em suas VMs Windows e Linux, conectando-as ao workspace do Log Analytics desejado |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md).
- Explore o [repositório de exemplos do Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).
