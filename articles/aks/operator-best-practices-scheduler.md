---
title: Práticas recomendadas do operador – recursos do agendador básico no Serviço de Kubernetes do Azure (AKS)
description: Conheça as práticas recomendadas de operador do cluster para usar os recursos básicos do Agendador, como cotas de recursos e orçamentos de interrupção de pod no Serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126589"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para recursos do Agendador básico no serviço de Kubernetes do Azure (AKS)

À medida que você gerencia clusters no Serviço de Kubernetes do Azure (AKS), geralmente é necessário isolar equipes e cargas de trabalho. O Agendador Kubernetes fornece recursos que permitem controlar a distribuição de recursos de computação ou limitar o impacto de eventos de manutenção.

Este artigo sobre práticas recomendadas se concentra em recursos de agendamento de Kubernetes básicos para os operadores do cluster. Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Cotas de uso de recursos para fornecer uma quantidade fixa de recursos para as equipes ou cargas de trabalho
> * Limitar o impacto da manutenção agendada usando os orçamentos de interrupção de pod
> * Verificação de solicitações de recurso de pod ausente e limites usando a `kube-advisor` ferramenta

## <a name="enforce-resource-quotas"></a>Enforce resource quotas

**Diretrizes de práticas recomendadas** - planejar e aplicar as cotas de recursos no nível de namespace. Se os pods não definem limites e solicitações de recursos, rejeite a implantação. Monitorare o uso de recursos e ajuste as cotas conforme necessário.

Solicitações de recursos e limites são colocados na especificação de pod. Esses limites são usados pelo Agendador Kubernetes no momento da implantação para encontrar um nó disponível no cluster. Esses limites e solicitações funcionam no nível do pod individual. Para obter mais informações sobre como definir esses valores, consulte [definir limites e solicitações de recursos Pod][resource-limits]

Para fornecer uma maneira para reservar e limitar os recursos em uma equipe de desenvolvimento ou um projeto, você deve usar *as cotas de recursos*. Essas cotas são definidas em um namespace e podem ser usadas para definir cotas nas seguinte base:

* **Recursos de computação**, como CPU e memória ou GPUs.
* **Recursos de armazenamento**, inclui o número total de volumes ou quantidade de espaço em disco para uma classe de armazenamento específica.
* **Contagem de objetos**, como o número máximo de segredos, serviços ou trabalhos podem ser criados.

Kubernetes não sobrecarregam os recursos. Depois do total acumulado de solicitações de recursos ou limites passa a cota atribuída, não há outras implantações bem-sucedidas.

Quando você define as cotas de recursos, todos os pods criados no namespace devem fornecer limites ou solicitações em suas especificações de pod. Se eles não fornecerem esses valores, você pode rejeitar a implantação. Em vez disso, você pode [configurar as solicitações e os limites padrão para um namespace][configure-default-quotas].

O manifesto YAML do exemplo a seguir chamado *dev-app-team-quotas.yaml* define um limite rígido de um total de *10* CPUs, *Gi 20* de memória, e *10*pods:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Essa cota de recursos pode ser aplicada, especificando o namespace, tal como *aplicativos de desenvolvimento*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Trabalhe com seus desenvolvedores de aplicativos e proprietários para entender suas necessidades e aplicar as cotas de recurso apropriado.

Para obter mais informações sobre objetos de recursos, escopos e prioridades disponíveis, consulte [cotas de recursos em kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planejar a disponibilidade usando os orçamentos de interrupção de pod

**Diretrizes de práticas recomendadas** - para manter a disponibilidade de aplicativos, defina os orçamentos de interrupção de Pod (PDBs) para certificar-se de que um número mínimo de pods está disponível no cluster.

Há dois eventos de interrupção que fazem com que os pods sejam removidos:

* *Interrupções involuntárias* são eventos além do controle típico do operador de cluster ou o proprietário do aplicativo.
  * Essas interrupções involuntárias incluem uma falha de hardware no computador físico, um pânico de kernel ou a exclusão de uma VM do nó
* *Interrupções involuntárias* são eventos solicitados além do controle típico do operador de cluster ou o proprietário do aplicativo.
  * Essas interrupções voluntárias incluem atualizações de cluster, um modelo de implantação atualizada ou exclusão acidental de um pod.

As interrupções involuntárias podem ser reduzidas usando várias réplicas de seus pods em uma implantação. Executar vários nós no cluster AKS também ajuda com essas interrupções involuntárias. Em caso de interrupção voluntária, o Kubernetes fornece *orçamentos de interrupção de pod* que permitem que o operador de cluster defina uma contagem mínima disponível ou máximo disponível de recursos. Esses orçamentos de interrupção de pod permitem que você planeje como implantações ou conjuntos de réplicas respondem quando ocorre um evento de interrupção voluntária.

Se um cluster é a ser atualizado ou um modelo de implantação atualizado, o Agendador Kubernetes torna claro que os pods adicionais estão programados nos outros nós antes que os eventos de interrupção voluntário podem continuar. O Agendador espera antes que um nó seja reinicializado até que o número definido de pods é agendado com êxito em outros nós no cluster.

Vamos examinar um exemplo de um conjunto de réplicas com cinco compartimentos que executam o NGINX. O pods no conjunto de réplicas recebe o rótulo `app: nginx-frontend`. Durante um evento de interrupção voluntária, como uma atualização de cluster, você deseja certificar-se de que pelo menos três pods continuam em execução. O manifesto YAML a seguir para o objeto para um *PodDisruptionBudget* define esses requisitos:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Você também pode definir uma porcentagem, tal como *60%* , que permite que você compense a réplica automaticamente definido com o aumento do número de pods.

Você pode definir um número máximo de instâncias que não está disponível em um conjunto de réplicas. Novamente, uma porcentagem para os máximo pods indisponíveis também pode ser definida. O manifesto YAML do orçamento de interrupção pod a seguir define não mais do que dois pods na réplica de conjunto não disponível:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Depois que seu orçamento de interrupção do pod é definido, você o cria no cluster do AKS assim como acontece com qualquer outro objeto de Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Trabalhe com seus desenvolvedores de aplicativos e proprietários para entender suas necessidades e aplicar os orçamentos de interrupção de pod.

Para obter mais informações sobre como usar orçamentos de interrupção de Pod, consulte [especificar um orçamento de interrupção para seu aplicativo][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Verificar regularmente os problemas de aplicativos com o kube-advisor

**Diretrizes de práticas recomendadas** -execute regularmente a versão mais recente do `kube-advisor` ferramenta de software livre para detectar problemas no cluster. Se você aplicar cotas de recursos em um cluster AKS existente, execute `kube-advisor` primeiro para localizar os pods que não têm solicitações de recursos e limites definidos.

A ferramenta [Kube-Advisor][kube-advisor] é um projeto de código-fonte aberto AKs associado que examina um cluster kubernetes e relata os problemas encontrados. Uma verificação útil é identificar os pods que não têm limites e solicitações de recurso em vigor.

A ferramenta Kube-Advisor pode relatar a solicitação de recursos e os limites ausentes no PodSpecs para aplicativos do Windows, bem como aplicativos do Linux, mas a ferramenta Kube-Advisor em si deve ser agendada em um pod do Linux. Você pode agendar um pod para ser executado em um pool de nós com um sistema operacional específico usando um [seletor de nó][k8s-node-selector] na configuração do pod.

Em um cluster AKS que hospeda várias equipes de desenvolvimento e aplicativos, pode ser difícil de controlar os pods sem solicitações desses recursos e limita o conjunto. Como prática recomendada, executar regularmente `kube-advisor` em seus clusters AKS, especialmente se você não atribuir as cotas de recursos para namespaces.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Este artigo se concentra nos recursos básicos de agendador Kubernetes. Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Multilocação e isolamento de cluster][aks-best-practices-cluster-isolation]
* [Recursos do Agendador do kubernetes avançado][aks-best-practices-advanced-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
