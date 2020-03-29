---
title: Remover um tipo de nó no Azure Service Fabric | Microsoft Docs
description: Saiba como remover um tipo de nó de um cluster do Service Fabric em execução no Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969397"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Como remover um tipo de nó de malha de serviço
Este artigo descreve como dimensionar um cluster do Azure Service Fabric removendo um tipo de nó existente de um cluster. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente. Depois de criar um cluster do Service Fabric, você pode dimensionar um cluster horizontalmente removendo um tipo de nó (conjunto de dimensionamento de máquinas virtuais) e todos os seus nós.  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

> [!WARNING]
> Utilizando esta abordagem para remover um tipo de nó de um cluster de produção não é recomendado ser usado com freqüência. Esse é um comando muito perigoso, pois ele exclui o recurso de conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó. 

## <a name="durability-characteristics"></a>Características de durabilidade
A segurança é priorizada sobre a velocidade ao usar remove-AzServiceFabricNodeType. O tipo de nó deve ser do [nível de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Prata ou Ouro, porque:
- Bronze não dá nenhuma garantia sobre como salvar informações de estado.
- A durabilidade Prata ou Ouro intercepta todas as alterações para o conjunto de dimensionamento.
- Ouro também dá controle sobre as atualizações do Azure sob o conjunto de dimensionamento.

O Service Fabric "orquestra" alterações subjacentes e atualiza de forma que os dados não sejam perdidos. No entanto, quando você remove um tipo de nó com durabilidade bronze, você pode perder informações do estado. Se você está removendo um tipo de nó primário e sua aplicação é apátrida, Bronze é aceitável. Quando você executar cargas de trabalho com monitoramento de estado na produção, a configuração mínima deve ser Prata. Da mesma forma, cenários de produção do tipo de nó primário devem ser sempre Prata ou Ouro.

### <a name="more-about-bronze-durability"></a>Mais informações sobre durabilidade de Bronze

Ao remover um tipo de nó que é Bronze, todos os nós no tipo de nó diminuem imediatamente. O Service Fabric não intercepta todas as atualizações do conjunto de dimensionamento de nós de Bronze, assim, todas as VMs ficam inativas imediatamente. Se você tiver qualquer coisa com o monitoamento de estado em nós, os dados são perdidos. Agora, mesmo se você estivesse sem estado, todos os nós no Service Fabric participam do anel, portanto, uma vizinhança inteira pode ser perdida, o que pode desestabilizar o cluster em si.

## <a name="remove-a-node-type"></a>Remover um tipo de nó

1. Por favor, cuide deste pré-requisito antes de iniciar o processo.

    - O aglomerado é saudável.
    - Ainda haverá capacidade suficiente depois que o tipo de nó for removido, por exemplo. número de nós para colocar a contagem de réplicas necessárias.

2. Mova todos os serviços que tenham restrições de colocação para usar o tipo de nó fora do tipo de nó.

    - Modificar o Manifesto de Aplicação/Serviço para não fazer mais referência ao tipo de nó.
    - Implantar a mudança.

    Em seguida, valide isso:
    - Todos os serviços modificados acima não estão mais em execução no nó pertencente ao tipo nó.
    - Todos os serviços são saudáveis.

3. Não marque o tipo de nó como não-primário (Pular para tipos de nó não primários)

    - Localize o modelo do Azure Resource Manager usado para implantação.
    - Encontre a seção relacionada ao tipo de nó na seção Malha de serviço.
    - A alteração épropriedade primária para falsa. ** Não remova a seção relacionada ao tipo de nó nesta tarefa.
    - Implante o modelo de gerenciador de recursos do Azure modificado. ** Dependendo da configuração do cluster, esta etapa pode demorar um pouco.
    
    Em seguida, valide isso:
    - A seção de malha de serviço no Portal indica que o cluster está pronto.
    - Cluster é saudável.
    - Nenhum dos nós pertencentes ao tipo de nó está marcado como Nó de Semente.

4. Desativar dados para o tipo de nó.

    Conecte-se ao cluster usando o PowerShell e execute a etapa seguinte.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Para a durabilidade do bronze, espere que todos os nós para chegar ao estado desativado
    - Para durabilidade de prata e ouro, alguns nódulos entrarão para deficientes e o resto estará em estado de desativação. Verifique a guia de detalhes dos nós em estado de desabilitação, se todos eles estão presos na garantia de quórum para partições de serviço de infra-estrutura, então é seguro continuar.

5. Pare os dados do tipo nó.

    Conecte-se ao cluster usando o PowerShell e execute a etapa seguinte.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Espere até que todos os nós para o tipo de nó estejam marcados para baixo.
    
6. Remova os dados do tipo nó.

    Conecte-se ao cluster usando o PowerShell e execute a etapa seguinte.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Espere até que todos os nós sejam removidos do cluster. Os nós não devem ser exibidos no SFX.

7. Remova o tipo de nó da seção Malha de Serviço.

    - Localize o modelo do Azure Resource Manager usado para implantação.
    - Encontre a seção relacionada ao tipo de nó na seção Malha de serviço.
    - Remova a seção correspondente ao tipo de nó.
    - Somente para clusters silver e de maior durabilidade, atualize o recurso de cluster no modelo `applicationDeltaHealthPolicies` e `properties` configure políticas de saúde para ignorar a saúde do aplicativo de malha:/Sistema adicionando recurso de cluster, conforme dado abaixo. A política abaixo deve ignorar os erros existentes, mas não permitir novos erros de saúde. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Implante o modelo de gerenciador de recursos do Azure modificado. ** Este passo levará um tempo, geralmente até duas horas. Essa atualização mudará as configurações para o InfrastructureService, portanto, é necessária uma reinicialização de nó. Neste caso `forceRestart` é ignorado. 
    O parâmetro `upgradeReplicaSetCheckTimeout` especifica o tempo máximo que o Service Fabric espera que uma partição esteja em um estado seguro, se não já em um estado seguro. Uma vez que as verificações de segurança passem por todas as partições em um nó, o Service Fabric prossegue com a atualização nesse nó.
    O valor do `upgradeTimeout` parâmetro pode ser reduzido para 6 horas, mas para a máxima segurança 12 horas devem ser usados.

    Em seguida, valide isso:
    - Recurso de malha de serviço no portal mostra-se pronto.

8. Remova todas as referências aos recursos relacionados ao tipo de nó.

    - Localize o modelo do Azure Resource Manager usado para implantação.
    - Remova o conjunto de escala da máquina virtual e outros recursos relacionados ao tipo de nó do modelo.
    - Implante as alterações.

    Em seguida:
    - Aguarde a implantação ser concluída.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o cluster [características de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Saiba mais sobre [tipos de nós e Conjuntos de Dimensionamento de Máquinas Virtuais](service-fabric-cluster-nodetypes.md).
- Saiba mais sobre [Dimensionamento do cluster do Service Fabric](service-fabric-cluster-scaling.md).
