---
title: Conformidade usando a Política do Azure
description: Atribua políticas incorporadas na Política Do Azure para auditar a conformidade dos registros de contêineres do Azure
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330729"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Conformidade de auditoria dos registros de contêineres do Azure usando a Política do Azure

[A Azure Policy](../governance/policy/overview.md) é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas. Essas políticas impõem diferentes regras e efeitos sobre os recursos para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço.

Este artigo introduz políticas incorporadas (visualização) para o Registro de Contêineres do Azure. Use essas políticas para auditar registros novos e existentes para conformidade.

Não há cobranças por usar a Política Do Azure.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="built-in-policy-definitions"></a>Definições de políticas incorporadas

As seguintes definições de diretiva incorporadas são específicas do Registro de Contêineres do Azure:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Veja também a definição de diretiva de rede incorporada: [[Visualização] O Registro de Contêineres deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Atribuir políticas

* Atribuir políticas usando o [portal Azure,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md)um [modelo de Gerenciador de Recursos](../governance/policy/assign-policy-template.md)ou os SDKs de diretiva do Azure.
* Escopo de uma atribuição de diretiva para um grupo de recursos, uma assinatura ou [um grupo de gerenciamento do Azure](../governance/management-groups/overview.md). As atribuições da política de registro de contêineres aplicam-se aos registros de contêineres existentes e novos dentro do escopo.
* Habilite ou desative [a aplicação da política a](../governance/policy/concepts/assignment-structure.md#enforcement-mode) qualquer momento.

> [!NOTE]
> Depois de atribuir ou atualizar uma diretiva, leva algum tempo para que a atribuição seja aplicada aos recursos no escopo definido. Veja informações sobre [os gatilhos de avaliação de políticas](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Revisar a conformidade da política

Acessar informações de conformidade geradas por suas atribuições de diretiva usando o portal Azure, as ferramentas de linha de comando do Azure ou os SDKs de política do Azure. Para obter detalhes, consulte [Obter dados de conformidade dos recursos do Azure](../governance/policy/how-to/get-compliance-data.md).

Quando um recurso não está em conformidade, existem muitas razões possíveis. Para determinar o motivo ou encontrar a alteração responsável, consulte [Determinar a não conformidade](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformidade de políticas no portal:

1. Selecione **Todos os serviços**e procure **por Política.**
1. Selecione **Conformidade**.
1. Use os filtros para limitar estados ![de conformidade](./media/container-registry-azure-policy/azure-policy-compliance.png)ou para procurar políticas De conformidade de políticas no portal .
1. Selecione uma política para revisar detalhes e eventos agregados de conformidade. Se desejar, selecione um registro específico para a conformidade com os recursos.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformidade de políticas na CLI do Azure

Você também pode usar o Azure CLI para obter dados de conformidade. Por exemplo, use o comando [az policy assignment list](/cli/azure/policy/assignment#az-policy-assignment-list) na CLI para obter os IDs de diretiva das políticas do Registro de Contêiner do Azure que são aplicadas:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Saída de exemplo:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Em seguida, execute [a lista de estado de política az](/cli/azure/policy/state#az-policy-state-list) para retornar o estado de conformidade formatado pelo JSON para todos os recursos um ID de política específico:

```azurecli
az policy state list \
  --resource <policyID>
```

Ou execute [a lista de estado de política az](/cli/azure/policy/state#az-policy-state-list) para retornar o estado de conformidade formatado pelo JSON de um recurso de registro específico, como o *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [definições](../governance/policy/concepts/definition-structure.md) e [efeitos](../governance/policy/concepts/effects.md) da Política do Azure

* Crie uma [definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md)

* Saiba mais sobre [os recursos de governança](../governance/index.yml) no Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/