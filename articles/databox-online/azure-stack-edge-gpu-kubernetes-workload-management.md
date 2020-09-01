---
title: Entender o gerenciamento de carga de trabalho kubernetes no dispositivo Azure Stack Edge | Microsoft Docs
description: Descreve como cargas de trabalho do kubernetes podem ser gerenciadas em seu dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 38c46bdcce64f726b3a7ddf74e0cfd10a14ba663
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268016"
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

## <a name="deployment-flow"></a>Fluxo de implantação

Para implantar aplicativos em um dispositivo Azure Stack Edge, você seguirá estas etapas: 
 
1. **Configurar o acesso**: primeiro, você usará o runspace do PowerShell para criar um usuário, criar um namespace e conceder acesso ao usuário para esse namespace.
2. **Configurar o armazenamento**: em seguida, você usará o Azure Stack recurso do Edge no portal do Azure para criar volumes persistentes usando o provisionamento estático ou dinâmico para os aplicativos com estado que serão implantados.
3. **Configurar a rede**: por fim, você usará os serviços para expor aplicativos externamente e dentro do cluster kubernetes.
 
## <a name="deployment-types"></a>Tipos de implantação

Há três maneiras principais de implantar suas cargas de trabalho. Cada uma dessas metodologias de implantação permite que você se conecte a um namespace distinto no dispositivo e, em seguida, implante aplicativos com ou sem estado.

![Implantação de carga de trabalho kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Implantação local**: essa é por meio da ferramenta de acesso de linha de comando, como a `kubectl` que permite implantar kubernetes `yamls` . Você se conecta ao cluster kubernetes em seu Azure Stack Edge que você cria usando o `kubeconfig` arquivo. Para obter mais informações, acesse [acessar um cluster kubernetes por meio de kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Implantação de IOT Edge**: isso ocorre por meio de IOT Edge, que se conecta ao Hub IOT do Azure. Você se conecta ao cluster kubernetes em seu dispositivo Azure Stack Edge por meio do `iotedge` namespace. Os agentes de IoT Edge implantados nesse namespace são responsáveis pela conectividade com o Azure. Você aplica a `IoT Edge deployment.json` configuração usando o CI/CD do DevOps do Azure. O namespace e o gerenciamento de IoT Edge são feitos por meio do operador de nuvem.

- **Implantação do Azure/Arc**: o arco do Azure é uma ferramenta de gerenciamento híbrido que permitirá que você implante aplicativos em seus clusters kubernetes. Você conecta o cluster kubernetes em seu dispositivo Azure Stack Edge por meio do `azure-arc namespace` . Os agentes são implantados neste namespace que são responsáveis pela conectividade com o Azure. Você aplica a configuração de implantação usando o gerenciamento de configuração baseado em GitOps. O arco do Azure também permitirá que você use Azure Monitor para contêineres para exibir e monitorar seus clusters. Para obter mais informações, acesse [o que é o Azure-Arc habilitado kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

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
