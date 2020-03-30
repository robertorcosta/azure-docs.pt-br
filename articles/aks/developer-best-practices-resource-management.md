---
title: Práticas recomendadas do desenvolvedor – gerenciamento de recursos em serviços de Kubernetes do Azure (AKS)
description: Aprenda as práticas recomendadas do desenvolvedor para o gerenciamento de recursos em serviços de Kubernetes do Azure (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 66676c90e73d1886c86d8afda8cbbecce239a005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259520"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para os desenvolvedores de aplicativos gerenciarem os recursos no serviço de Kubernetes do Azure (AKS)

Ao desenvolver e executar aplicativos no serviço de Kubernetes do Azure (AKS), há algumas áreas principais a serem consideradas. Como você pode gerenciar suas implantações de aplicativo pode afetar negativamente a experiência do usuário final de serviços que você fornece. Para ajudá-lo a ter êxito, tenha em mente algumas práticas recomendadas que você pode seguir ao desenvolver e executar aplicativos no AKS.

Este artigo sobre práticas recomendadas se concentra em como executar suas cargas de trabalho e o cluster da perspectiva do desenvolvedor do aplicativo. Para obter informações sobre as melhores práticas administrativas, consulte [Práticas recomendadas de operador de cluster para isolamento e gerenciamento de recursos no serviço de Kubernetes do Azure (AKS)][operator-best-practices-isolation]. Neste artigo, você aprende:

> [!div class="checklist"]
> * O que são solicitações e limites do recurso de pod
> * Maneiras de desenvolver e implantar aplicativos com espaços de desenvolvimento e o Visual Studio Code
> * Como usar a `kube-advisor` ferramenta para verificar se há problemas com implantações

## <a name="define-pod-resource-requests-and-limits"></a>Defina as solicitações e limites de recurso de pod

**Diretrizes de práticas recomendadas** – defina as solicitações e limites de pod em todos os pods em seus manifestos YAML. Se o cluster AKS usar *cotas de recursos*, sua implantação pode ser rejeitada se você não definir esses valores.

Um meio principal para gerenciar os recursos de computação dentro de um cluster do AKS é usar limites e solicitações de pod. Essas solicitações e limites permitem que o Agendador Kubernetes saiba o que deve ser atribuído a um pod de recursos de computação.

* **As solicitações de CPU/memória** do Pod definem uma quantidade definida de CPU e memória que o pod precisa regularmente.
    * Quando o agendador Kubernetes tenta colocar um pod em um nó, as solicitações de pod são usadas para determinar qual nó tem recursos suficientes disponíveis para agendamento.
    * A não definição de uma solicitação de pod irá configurá-la no limite definido.
    * É muito importante monitorar o desempenho do seu aplicativo para ajustar essas solicitações. Se forem feitas solicitações insuficientes, sua solicitação poderá receber desempenho degradado devido ao agendamento excessivo de um nó. Se as solicitações forem superestimadas, sua solicitação pode ter maior dificuldade para ser agendada.
* **Os limites de CPU/memória** do Pod são a quantidade máxima de CPU e memória que um pod pode usar. Esses limites ajudam a definir quais pods devem ser mortos em caso de instabilidade do nó devido à insuficiência de recursos. Sem os limites adequados, as cápsulas definidas serão mortas até que a pressão dos recursos seja levantada.
    * Os limites do pod ajudam a definir quando um pod perdeu o controle do consumo de recursos. Quando um limite é excedido, o pod é priorizado para matar para manter a saúde do nódulo e minimizar o impacto para as cápsulas que compartilham o nó.
    * Não definir um limite de pod o atribui ao maior valor disponível em um determinado nó.
    * Não defina um limite de pod maior do seus nós podem suportar. Cada nó do AKS reserva uma quantidade definida de CPU e memória para os componentes principais do Kubernetes. Seu aplicativo pode tentar consumir muitos recursos no nó para outros pods para ser executado com êxito.
    * Novamente, é muito importante monitorar o desempenho de sua aplicação em diferentes horários durante o dia ou a semana. Determine quando a demanda máxima é e alinhe os limites do pod aos recursos necessários para atender às necessidades máximas do aplicativo.

Nas especificações do seu pod, é **uma prática recomendada e muito importante** definir essas solicitações e limites com base nas informações acima. Se você não incluir esses valores, o agendador kubernetes não pode levar em conta os recursos que seus aplicativos precisam para ajudar nas decisões de agendamento.

Se o agendador colocar um pod em um nó com recursos insuficientes, o desempenho do aplicativo será degradado. É altamente recomendável que os administradores de cluster *definam cotas de recursos* em um namespace que exija que você defina solicitações e limites de recursos. Para obter mais informações, consulte [clusters cotas de recursos no AKS][resource-quotas].

Ao definir uma solicitação de CPU ou o limite, o valor é medido em unidades de CPU. 
* *1.0* CPU é igual a um núcleo da CPU virtual no nó. 
* A mesma medida é usada para GPUs.
* Você pode definir frações medidas em miliscores. Por exemplo, *100m* é *0,1* de um núcleo vCPU subjacente.

No exemplo a seguir básico para um único pod NGINX, o pod solicita *100m* de tempo de CPU, e *128Mi* de memória. Os limites de recursos para o pod são definidos como *250m* CPU e memória de *256Mi*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Para obter mais informações a respeito de medidas de recursos e atribuições, consulte [Gerenciando os recursos para contêineres de computação][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Desenvolver e depurar aplicativos em relação a um cluster do AKS

**Diretrizes de práticas recomendadas** - as equipes de desenvolvimento devem implantar e depurar em um cluster AKS usando espaços de desenvolvimento. Este modelo de desenvolvimento certifica-se de que as necessidades de armazenamento, rede ou controles de acesso baseado em função sejam implementadas antes que o aplicativo seja implantado para produção.

Com Azure Dev Spaces, você desenvolve, depura e testa os aplicativos diretamente em relação ao cluster AKS. Os desenvolvedores dentro de uma equipe trabalham juntos para compilar e testar em todo o ciclo de vida do aplicativo. Você pode continuar a usar as ferramentas existentes, como o Visual Studio ou Visual Studio Code. Uma extensão é instalada para espaços de desenvolvimento que oferece uma opção para executar e depurar o aplicativo em um cluster AKS:

![Depurar aplicativos em um cluster do AKS com os espaços de desenvolvimento](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Esse processo de desenvolvimento e teste integrado com espaços de desenvolvimento reduz a necessidade de ambientes de teste local, como [minikube][minikube]. Em vez disso, você pode desenvolver e testar em relação a um cluster do AKS. Esse cluster pode ser protegido e isolado, conforme observado na seção anterior sobre o uso de namespaces para isolar logicamente um cluster. Quando seus aplicativos estão prontos para implantar em produção, você pode implantar com confiança conforme seu desenvolvimento foi feito em relação a um cluster AKS real.

O Azure Dev Spaces destina-se a ser usado com aplicativos executados em pods e nós Linux.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Use a extensão Visual Studio Code para Kubernetes

**Diretrizes de práticas recomendadas de** - instalar e usar a extensão do VS Code para o Kubernetes quando você escreve manifestos YAML. Você também pode usar a extensão para solução de implantação integradas, que pode ajudar os proprietários do aplicativo que raramente interagem com o cluster do AKS.

A [extensão do Visual Studio Code para o Kubernetes][vscode-kubernetes] ajuda você a desenvolver e implantar aplicativos no AKS. A extensão fornece intellisense para recursos do Kubernetes e gráficos do Helm e modelos. Você também pode procurar, implantar e editar os recursos do Kubernetes do VS Code. A extensão também fornece uma verificação do intellisense para solicitações de recursos ou limita o que está sendo definido nas especificações de pod:

![Extensão do VS Code para aviso de Kubernetes sobre falta de limites de memória](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verificar regularmente os problemas de aplicativos com o kube-advisor

**Orientação de práticas recomendadas** - `kube-advisor` Execute regularmente a versão mais recente da ferramenta de código aberto para detectar problemas em seu cluster. Se você aplicar cotas de recursos em um cluster AKS existente, execute `kube-advisor` primeiro para localizar os pods que não têm solicitações de recursos e limites definidos.

A ferramenta [kube-advisor][kube-advisor] é um projeto de código aberto associado da AKS que verifica um cluster Kubernetes e relata problemas que ele encontra. Uma verificação útil é identificar os pods que não têm limites e solicitações de recurso em vigor.

A ferramenta kube-advisor pode reportar sobre solicitação de recursos e limites ausentes no PodSpecs para aplicativos Windows, bem como aplicativos Linux, mas a própria ferramenta kube-advisor deve ser agendada em um pod Linux. Você pode agendar um pod para executar em um pool de nó com um sistema operacional específico usando um [seletor][k8s-node-selector] de nó na configuração do pod.

Em um cluster AKS que hospeda várias equipes de desenvolvimento e aplicativos, pode ser difícil de controlar os pods sem solicitações desses recursos e limita o conjunto. Como prática recomendada, executar regularmente `kube-advisor` em seus clusters AKS.

## <a name="next-steps"></a>Próximas etapas

Este artigo sobre práticas recomendadas se concentra em como executar suas cargas de trabalho de uma perspectiva do operador de cluster. Para obter informações sobre as melhores práticas administrativas, consulte [Práticas recomendadas de operador de cluster para isolamento e gerenciamento de recursos no serviço de Kubernetes do Azure (AKS)][operator-best-practices-isolation].

Para implementar algumas dessas práticas recomendadas, consulte os seguintes artigos:

* [Desenvolver com Dev Spaces][dev-spaces]
* [Verifique se há problemas com o kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
