---
title: Entender o controle de acesso baseado em função do kubernetes no dispositivo Azure Stack Edge | Microsoft Docs
description: Descreve como o controle de acesso baseado em função do kubernetes ocorre em um dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 703e67b4829413776dc8d98843888fbd67906baa
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182183"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-device"></a>Controle de acesso baseado em função do kubernetes em seu dispositivo Azure Stack Edge


No dispositivo Azure Stack Edge, quando você configura a função de computação, um cluster kubernetes é criado. Você pode usar o RBAC (controle de acesso baseado em função) kubernetes para limitar o acesso aos recursos de cluster em seu dispositivo.

Este artigo fornece uma visão geral do sistema RBAC fornecido pelo kubernetes e como o kubernetes RBAC é implementado em seu dispositivo Azure Stack Edge. 

## <a name="rbac-for-kubernetes"></a>RBAC para kubernetes

O RBAC kubernetes permite atribuir usuários ou grupos de usuários, permissão para tarefas como criar ou modificar recursos ou exibir logs da execução de cargas de trabalho do aplicativo. Essas permissões podem ser delimitadas para um único namespace ou concedidas em todo o cluster. 

Quando você configura o cluster kubernetes, um único usuário é criado correspondente a esse cluster e é chamado de usuário administrador do cluster.  Um `kubeconfig` arquivo é associado ao usuário administrador do cluster. O `kubeconfig` arquivo é um arquivo de texto que contém todas as informações de configuração necessárias para se conectar ao cluster para autenticar o usuário.

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

## <a name="namespaces-and-users"></a>Namespaces e usuários

No mundo real, é importante dividir o cluster em vários namespaces. 

- **Vários usuários**: se você tiver vários usuários, vários namespaces permitirão que esses usuários implantem seus aplicativos e serviços em seus namespaces específicos isoladamente uns dos outros. 
- **Usuário único**: mesmo que haja um único usuário, vários namespaces permitirão que o usuário executasse várias versões dos aplicativos no mesmo cluster kubernetes.

### <a name="roles-and-rolebindings"></a>Funções e RoleBindings

Kubernetes tem o conceito de função e Associação de função que permite conceder permissões a usuários ou recursos em um nível de namespace e em um nível de cluster. 

- **Funções**: você pode definir permissões para usuários como uma **função** e, em seguida, usar **funções** para conceder permissões em um namespace. 
- **RoleBindings**: depois de definir as funções, você pode usar **RoleBindings** para atribuir funções para um namespace específico. 

Essa abordagem permite separar logicamente um único cluster kubernetes, com os usuários capazes de acessar somente os recursos do aplicativo em seu namespace atribuído. 

## <a name="rbac-on-azure-stack-edge"></a>RBAC na borda Azure Stack

Na implementação atual do RBAC, Azure Stack Edge permite que você execute as seguintes ações de um runspace do PowerShell restrito:

- Criar namespaces.  
- Crie usuários adicionais.
- Conceda acesso de administrador aos namespaces que você criou. Tenha em mente que você não terá acesso à função de administrador de cluster ou a uma exibição dos recursos de todo o cluster.
- Obtenha `kubeconfig` o arquivo com informações para acessar o cluster kubernetes.


O dispositivo de borda Azure Stack tem vários namespaces de sistema e você pode criar namespaces de usuário com `kubeconfig` arquivos para acessar esses namespaces. Os usuários têm controle total sobre esses namespaces e podem criar ou modificar usuários ou conceder acesso a usuários. Somente o administrador do cluster tem acesso completo aos namespaces do sistema e aos recursos de todo o cluster. Um `aseuser` tem acesso somente leitura aos namespaces do sistema.

Aqui está um diagrama que descreve a implementação do RBAC no dispositivo Azure Stack Edge.

![RBAC no dispositivo Azure Stack Edge](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

Neste diagrama, Alice, Bob e Chuck têm acesso somente a namespaces de usuário atribuídos, que nesse caso são `ns1` , e, `ns2` `ns3` respectivamente. Nesses namespaces, eles têm acesso de administrador. O administrador de cluster, por outro lado, tem acesso de administrador aos namespaces do sistema e aos recursos de todo o cluster.

Você pode usar `kubectl` comandos para criar namespaces, atribuir usuários, atribuir usuários ou baixar `kubeconfig` arquivos. Este é um fluxo de trabalho de alto nível:

1. Crie um namespace e um usuário.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Crie um usuário.  

    `New-HcsKubernetesUser -UserName`  

3. Associe o namespace ao usuário que você criou.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Salve a configuração do usuário no `C:\Users\<username>\.kube` .  

5. Instale `kubectl` e inicie a implantação de aplicativos no `kubectl` . 

Para obter instruções passo a passo detalhadas, acesse [kubernetes cluster via kuebctl em seu Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Ao trabalhar com namespaces e usuários em seus dispositivos Azure Stack Edge, as seguintes advertências se aplicam:

- Você não tem permissão para executar nenhuma operação, como criar usuários, conceder ou revogar acesso de namespace ao usuário, para qualquer um dos namespaces do sistema. Exemplos de namespaces do sistema incluem,,,, `kube-system` `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` , `kube-public` . Os namespaces do sistema também incluem os namespaces reservados para tipos de implantação, como `iotedge` (IOT Edge namespace) e `azure-arc` (namespace Arc do Azure).
- Você pode criar namespaces de usuário e dentro desses namespaces criar usuários adicionais e conceder ou revogar o acesso de namespace a esses usuários.
- Você não tem permissão para criar namespaces com nomes idênticos aos de qualquer namespace do sistema. Os nomes dos namespaces do sistema são reservados.  
- Você não tem permissão para criar nenhum namespace de usuário com nomes que já estão em uso por outros namespaces de usuário. Por exemplo, se você tiver um `test-ns` que você criou, não poderá criar outro `test-ns` namespace.
- Você não tem permissão para criar usuários com nomes que já estão reservados. Por exemplo, `aseuser` é um administrador de cluster reservado e não pode ser usado.


## <a name="next-steps"></a>Próximas etapas

Para entender como você pode criar um usuário, criar um namespace e conceder acesso de usuário ao namespace, consulte [acessar um cluster kubernetes por meio de kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

