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
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779956"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Use o Azure Policy para aplicar configurações de cluster em escala (versão prévia)

## <a name="overview"></a>Visão geral

Use o Azure Policy para impor cada `Microsoft.Kubernetes/connectedclusters` ou o recurso `Microsoft.ContainerService/managedClusters` habilitado para Git-Ops tem `Microsoft.KubernetesConfiguration/sourceControlConfigurations` específico aplicado.  Para usar o Azure Policy, selecione uma definição de política existente e crie uma atribuição de política.  Ao criar a atribuição de política, você define o escopo para a atribuição: será um grupo de recursos ou uma assinatura do Azure.  Também é possível definir os parâmetros para o `sourceControlConfiguration` que será criado.  Depois que a atribuição for criada, o mecanismo de política identificará todos os recursos `connectedCluster` ou `managedCluster` que estão localizados dentro do escopo e aplicará o `sourceControlConfiguration` a cada um.

Se estiver usando vários repositórios Git como as fontes de verdade para cada cluster (por exemplo, um repositório para o operador central de TI/cluster e outros repositórios para equipes de aplicativos), você pode habilitar isso usando várias atribuições de política, cada atribuição de política configurada para usar um repositório Git diferente.

## <a name="create-a-custom-policy-definition"></a>Criar uma definição de política personalizada

1. No portal do Azure, vá até a Política e na seção **Criação** da barra lateral, selecione **Definições**.
2. Selecione **+ Definição de política**.
3. Defina o **Local da definição** como suas assinatura ou grupo de gerenciamento.  Isso determinará o escopo mais amplo onde a definição da política pode ser usada.
4. Atribua à política um **Nome** e uma **Descrição**.
5. Em categoria, escolha **Criar novo** e grave o *Cluster do Kubernetes – Azure Arc*
6. Na caixa de edição **Regra da política**, copie/cole o conteúdo dessa [definição de política de exemplo](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json).
7. **Salvar**.

Esta etapa para criar uma definição de política personalizada não será necessária depois que o trabalho for concluído para torná-la uma política interna.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

1. No portal do Azure, vá até a Política e na seção **Criação** da barra lateral, selecione **Definições**.
2. Encontre a definição que você acabou de criar e selecione-a.
3. Na página de ações, selecione **Atribuir**.
4. Defina o **Escopo** do grupo de gerenciamento, a assinatura ou o grupo de recursos ao qual a atribuição de política será aplicada.
5. Se você quiser excluir algum recurso do escopo da política, defina as **Exclusões**.
6. Dê um **Nome** e uma **Descrição** à atribuição de política que você possa usar para identificá-la facilmente.
7. Verifique se **Imposição de política** está definida como *Habilitado*.
8. Selecione **Avançar**.
9. Defina os valores do parâmetro que serão usados durante a criação do `sourceControlConfiguration`.
10. Selecione **Avançar**.
11. Habilitar **Criar uma tarefa de correção**.
12. Verifique se **Criar uma identidade gerenciada** está marcada e se a identidade terá permissões de **Colaborador**.  Consulte [este doc](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) e [o comentário neste doc](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) para obter mais informações sobre as permissões necessárias.
13. Selecione **Examinar + criar**.

Depois que a atribuição de política é criada, para qualquer novo recurso `connectedCluster` (ou recurso `managedCluster` com os agentes do GitOps instalados) que está localizado no escopo da atribuição, o `sourceControlConfiguration` será aplicado.  Para clusters existentes, será necessário executar manualmente uma tarefa de correção.  Normalmente leva de 10 a 20 minutos para que a atribuição de política entre em vigor.

## <a name="verify-a-policy-assignment"></a>Verificar uma atribuição de política

1. No portal do Azure, vá até um dos recursos `connectedCluster` e, na seção **Configurações**  da barra lateral, selecione **Políticas**. (A UX para o cluster gerenciado AKS ainda não está implementada, mas está chegando.)
2. Na lista, você deve ver a atribuição de política que criou acima e o **Estado de conformidade** deve ser *Em conformidade*.
3. Na seção **Configurações** da barra lateral, selecione **Configurações**.
4. Na lista, você deve ver o `sourceControlConfiguration` que a atribuição de política criou.
5. Use **kubectl** para interrogar o cluster: você verá o namespace e os artefatos que foram criados pelo `sourceControlConfiguration`.
6. Em 5 minutos, você deve ver no cluster os artefatos descritos nos manifestos no repositório Git configurado.

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Azure Monitor para Contêineres com clusters Kubernetes habilitados para Arc](./deploy-azure-monitor-for-containers.md)
