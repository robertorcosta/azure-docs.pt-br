---
title: Entender o gerenciamento de carga de trabalho kubernetes no dispositivo Azure Stack Edge | Microsoft Docs
description: Descreve como cargas de trabalho do kubernetes podem ser gerenciadas em seu dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 21845b51fdd108221d5e1bce50e953b79084d17d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89082886"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Gerenciamento de carga de trabalho kubernetes em seu dispositivo Azure Stack Edge

No dispositivo Azure Stack Edge, um cluster kubernetes é criado quando você configura a função de computação. Depois que o cluster kubernetes é criado, os aplicativos em contêineres podem ser implantados no cluster kubernetes em pods. Há maneiras distintas de implantar cargas de trabalho no cluster kubernetes. 

Este artigo descreve os vários métodos que podem ser usados para implantar cargas de trabalho no dispositivo Azure Stack Edge.

## <a name="workload-types"></a>Tipos de carga de trabalho

Os dois tipos comuns de cargas de trabalho que você pode implantar em seu Azure Stack dispositivo de borda são aplicativos sem estado ou aplicativos com estado.

- **Aplicativos sem estado** não preservam seu estado e não salvam dados no armazenamento persistente. Todos os dados de usuário e de sessão permanecem com o cliente. Alguns exemplos de aplicativos sem estado incluem front-ends da Web como Nginx e outros aplicativos Web.

    Você pode criar uma implantação do kubernetes para implantar um aplicativo sem estado no cluster. 

- **Os aplicativos com estado** exigem que seu estado seja salvo. Os aplicativos com estado usam armazenamento persistente, como volumes persistentes, para salvar dados para uso pelo servidor ou por outros usuários. Exemplos de aplicativos com monitoração de estado incluem bancos de dados como o MongoDB.

    Você pode criar uma implantação do kubernetes para implantar um aplicativo com estado. 

## <a name="namespaces-types"></a>Tipos de namespaces

Recursos de kubernetes, como pods e implantações, são agrupados logicamente em um namespace. Esses agrupamentos fornecem uma maneira de dividir logicamente um cluster kubernetes e restringir o acesso para criar, exibir ou gerenciar recursos. Os usuários podem interagir apenas com recursos dentro de seus namespaces atribuídos.

Os namespaces são destinados ao uso em ambientes com muitos usuários espalhados por várias equipes ou projetos. Para clusters com alguns a dezenas de usuários, não é necessário criar nem pensar em namespaces. Comece a usar namespaces quando precisar dos recursos que eles fornecem.

Para obter mais informações, consulte [Kubernetes namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).


O dispositivo do Azure Stack Edge tem os seguintes namespaces:

- **Namespace do sistema** – esse namespace é onde os principais recursos existem, como recursos de rede, como DNS e proxy, ou o painel do kubernetes. Normalmente, você não implantar seus próprios aplicativos para esse namespace. Use esse namespace para depurar qualquer problema de cluster kubernetes. 

    Há vários namespaces de sistema em seu dispositivo e os nomes correspondentes a esses namespaces de sistema são reservados. Aqui está uma lista dos namespaces de sistema reservados: 
    - Kube-sistema
    - metallb-sistema
    - DBE-namespace
    - default
    - kubernetes-painel
    - default
    - Kube-concessão de nó
    - Kube – público
    - iotedge
    - Azure-arco

    Certifique-se de não usar nenhum nome reservado para namespaces de usuário que você criar. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Namespace de usuário** – esses são os namespaces que você pode criar por meio do **kubectl** para implantar aplicativos localmente.
 
- **IOT Edge namespace** -você se conecta a esse `iotedge` namespace para implantar aplicativos via IOT Edge.

- **Namespace Arc do Azure** -você se conecta a este `azure-arc` namespace para implantar aplicativos por meio do Azure Arc.

 
## <a name="deployment-types"></a>Tipos de implantação

Há três maneiras principais de implantar suas cargas de trabalho. Cada uma dessas metodologias de implantação permite que você se conecte a um namespace distinto no dispositivo e, em seguida, implante aplicativos com ou sem estado.

![Implantação de carga de trabalho kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Implantação local**: essa é por meio da ferramenta de acesso de linha de comando, como a `kubectl` que permite implantar K8 `yamls` . Você se conecta ao cluster K8 em seu Azure Stack Edge que você cria usando o `kubeconfig` arquivo. Para obter mais informações, acesse [acessar um cluster kubernetes por meio de kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Implantação de IOT Edge**: isso ocorre por meio de IOT Edge, que se conecta ao Hub IOT do Azure. Você se conecta ao cluster K8 em seu dispositivo Azure Stack Edge por meio do `iotedge` namespace. Os agentes de IoT Edge implantados nesse namespace são responsáveis pela conectividade com o Azure. Você aplica a `IoT Edge deployment.json` configuração usando o CI/CD do DevOps do Azure. O namespace e o gerenciamento de IoT Edge são feitos por meio do operador de nuvem.

- **Implantação do Azure/Arc**: o arco do Azure é uma ferramenta de gerenciamento híbrido que permitirá que você implante aplicativos em seus clusters K8. Você conecta o cluster K8 em seu dispositivo Azure Stack Edge por meio do `azure-arc namespace` .  Os agentes são implantados neste namespace que são responsáveis pela conectividade com o Azure. Você aplica a configuração de implantação usando o gerenciamento de configuração baseado em GitOps. O arco do Azure também permitirá que você use Azure Monitor para contêineres para exibir e monitorar seus clusters. Para obter mais informações, acesse [o que é o Azure-Arc habilitado kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Escolher o tipo de implantação

Ao implantar aplicativos, considere as seguintes informações:

- **Tipos únicos ou múltiplos**: você pode escolher uma única opção de implantação ou uma combinação de opções de implantação diferentes.
- **Nuvem versus local**: dependendo de seus aplicativos, você pode escolher a implantação local via kubectl ou implantação de nuvem por meio do IOT Edge e do Arc do Azure. 
    - A implantação local é mais adequada para cenários de desenvolvimento. Ao escolher uma implantação local, você é restrito à rede na qual o dispositivo do Azure Stack Edge é implantado.
    - Se você tiver um agente de nuvem que possa implantar, você deve implantar seu operador de nuvem e usar o gerenciamento de nuvem.
- **IOT vs. arc do Azure**: a escolha da implantação também depende da intenção do seu cenário de produto. Se você estiver implantando aplicativos ou contêineres com integração mais profunda com o ecossistema IoT ou IoT, deverá escolher a maneira IoT Edge de implantar aplicativos. Se você tiver implantações kubernetes existentes, o arco do Azure será a escolha preferida.


## <a name="next-steps"></a>Próximas etapas

Para implantar localmente um aplicativo por meio do kubectl, consulte:

- [Implante um aplicativo sem estado em seu Azure Stack Edge por meio do kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Para implantar um aplicativo por meio do IoT Edge, consulte:

- [Implante um módulo de exemplo em seu Azure Stack Edge via IOT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Para implantar um aplicativo por meio do Azure Arc, consulte:

- [Implantar um aplicativo usando o arco do Azure](azure-stack-edge-gpu-deploy-sample-module.md).
