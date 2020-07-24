---
title: Use o Azure Policy para aplicar configurações de cluster em escala (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Use o Azure Policy para aplicar configurações de cluster em escala
keywords: Kubernetes, Arc, Azure, K8s, containers
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050039"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Use o Azure Policy para aplicar configurações de cluster em escala (versão prévia)

## <a name="overview"></a>Visão geral

Use Azure Policy para impor que cada recurso `Microsoft.Kubernetes/connectedclusters` ou recurso habilitado para operações de git `Microsoft.ContainerService/managedClusters` tenha sido `Microsoft.KubernetesConfiguration/sourceControlConfigurations` aplicado especificamente. Para usar o Azure Policy, selecione uma definição de política existente e crie uma atribuição de política. Ao criar a atribuição de política, você define o escopo para a atribuição: será um grupo de recursos ou uma assinatura do Azure. Também é possível definir os parâmetros para o `sourceControlConfiguration` que será criado. Depois que a atribuição for criada, o mecanismo de política identificará todos os recursos `connectedCluster` ou `managedCluster` que estão localizados dentro do escopo e aplicará o `sourceControlConfiguration` a cada um.

Se estiver usando vários repositórios Git como as fontes de verdade para cada cluster (por exemplo, um repositório para o operador central de TI/cluster e outros repositórios para equipes de aplicativos), você pode habilitar isso usando várias atribuições de política, cada atribuição de política configurada para usar um repositório Git diferente.

## <a name="prerequisite"></a>Pré-requisito

Verifique se você tem `Microsoft.Authorization/policyAssignments/write` permissões no escopo (assinatura ou grupo de recursos) em que deseja criar essa atribuição de política.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

1. No portal do Azure, vá até a Política e na seção **Criação** da barra lateral, selecione **Definições**.
2. Escolha a política interna "implantar GitOps no cluster kubernetes" na categoria "kubernetes" e clique em **atribuir**.
3. Defina o **Escopo** do grupo de gerenciamento, a assinatura ou o grupo de recursos ao qual a atribuição de política será aplicada.
4. Se você quiser excluir algum recurso do escopo da política, defina as **Exclusões**.
5. Dê um **Nome** e uma **Descrição** à atribuição de política que você possa usar para identificá-la facilmente.
6. Verifique se **Imposição de política** está definida como *Habilitado*.
7. Selecione **Avançar**.
8. Defina os valores do parâmetro que serão usados durante a criação do `sourceControlConfiguration`.
9. Selecione **Avançar**.
10. Habilitar **Criar uma tarefa de correção**.
11. Verifique se **Criar uma identidade gerenciada** está marcada e se a identidade terá permissões de **Colaborador**. Consulte [este doc](../../governance/policy/assign-policy-portal.md) e [o comentário neste doc](../../governance/policy/how-to/remediate-resources.md) para obter mais informações sobre as permissões necessárias.
12. Selecione **Examinar + criar**.

Depois que a atribuição de política é criada, para qualquer novo recurso `connectedCluster` (ou recurso `managedCluster` com os agentes do GitOps instalados) que está localizado no escopo da atribuição, o `sourceControlConfiguration` será aplicado. Para clusters existentes, será necessário executar manualmente uma tarefa de correção. Normalmente leva de 10 a 20 minutos para que a atribuição de política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verificar uma atribuição de política

1. No portal do Azure, vá até um dos recursos `connectedCluster` e, na seção **Configurações**  da barra lateral, selecione **Políticas**. (A UX para o cluster AKS ainda não está implementada, mas está chegando.)
2. Na lista, você deve ver a atribuição de política que criou acima e o **Estado de conformidade** deve ser *Em conformidade*.
3. Na seção **Configurações** da barra lateral, selecione **Configurações**.
4. Na lista, você deve ver o `sourceControlConfiguration` que a atribuição de política criou.
5. Use **kubectl** para interrogar o cluster: você verá o namespace e os artefatos que foram criados pelo `sourceControlConfiguration`.
6. Em 5 minutos, você deve ver no cluster os artefatos descritos nos manifestos no repositório Git configurado.

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Azure Monitor para Contêineres com clusters Kubernetes habilitados para Arc](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
