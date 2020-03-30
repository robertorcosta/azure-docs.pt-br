---
title: Dimensione um cluster de malha de serviço para dentro ou para fora
description: Dimensione um cluster de malha de serviço para corresponder à demanda, definindo regras de escala automática para cada conjunto de escala de tipo de nó/máquina virtual. Adicionar ou remover nós de um cluster do Service Fabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258727"
---
# <a name="scale-a-cluster-in-or-out"></a>Reduzir ou escalar um cluster horizontalmente

> [!WARNING]
> Leia esta seção antes de escalar

O dimensionamento de recursos de computação para originar a carga de trabalho de seu aplicativo requer planejamento intencional, quase sempre levará mais de uma hora para ser concluído em um ambiente de produção e exige que você entenda sua carga de trabalho e contexto de negócios; Na verdade, se você nunca fez essa atividade antes, é recomendável começar lendo e compreendendo [considerações sobre o planejamento de capacidade de cluster do Service Fabric](service-fabric-cluster-capacity.md), antes de continuar com o restante deste documento. Esta recomendação é para evitar problemas não intencionais do LiveSite, e também é recomendado testar com sucesso as operações que você decidir executar em um ambiente que não seja de produção. A qualquer momento você pode [relatar problemas de produção ou solicitar suporte pago para o Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Para engenheiros alocados para executar essas operações que possuam contexto apropriado, este artigo descreverá as operações de dimensionamento, mas você deve decidir e entender quais operações são apropriadas para seu caso de uso; como os recursos a serem dimensionados (CPU, Armazenamento, Memória), a direção a ser dimensionada (vertical ou horizontalmente) e as operações a serem executadas (implantação de modelo de recurso, portal, PowerShell / CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Reduzir horizontalmente ou escalar horizontalmente um cluster do Service Fabric usando regra de dimensionamento automático ou manualmente
Os conjuntos de escala de Máquina Virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um conjunto de dimensionamento de máquinas virtuais separado. Cada tipo de nó pode ser escalado ou reduzido horizontalmente de forma independente, ter conjuntos diferentes de portas abertas e métricas de capacidade diferentes. Leia mais sobre isso no documento de tipos de [nós de malha de serviço.](service-fabric-cluster-nodetypes.md) Uma vez que os tipos de nó de malha de serviço em seu cluster são feitos de conjuntos de escala de máquina virtual no backend, você precisa configurar regras de escala automática para cada conjunto de escala de tipo de nó/máquina virtual.

> [!NOTE]
> Sua assinatura precisa ter uma quantidade suficiente de núcleos para adicionar as novas VMs que compõe esse cluster. Atualmente, não há nenhuma validação de modelo. Logo, você receberá uma falha de tempo de implantação se qualquer um dos limites de cota for atingido. Além disso, um único tipo de nó não pode simplesmente exceder 100 nós por VMSS. Pode ser necessário adicionar VMSS para atingir a escala desejada, e o escalonamento automático não pode adicionar automaticamente VMSS. Adicionar in-loco do VMSS a um cluster ativo é uma tarefa desafiadora e, geralmente, isso resulta em usuários provisionando novos clusters com os tipos de nó apropriados provisionados no momento da criação; [planeje a capacidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) de acordo. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolher o tipo de nó/conjunto de dimensionamento de máquinas virtuais a ser dimensionado
No momento, não é possível especificar as regras de dimensionamento automático para conjuntos de dimensionamento de máquinas virtuais usando o portal, portanto para criar um Cluster do Service Fabric. Portanto, vamos utilizar o Microsoft Azure PowerShell (1.0+) para listar os tipos de nó e, em seguida, adicionar regras de dimensionamento automático a eles.

Para obter a lista de conjuntos de dimensionamento de máquinas virtuais que compõem o cluster, execute os cmdlets a seguir:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Defina regras de escala automática para o conjunto de escala de tipo de nó/máquina virtual
Se o cluster tiver vários tipos de nó, repita isso para cada tipo de nó/conjuntos de escala de máquina virtual que você deseja dimensionar (dentro ou fora). Leve em conta o número de nós que você precisa ter antes de configurar o dimensionamento automático. O número mínimo de nós necessários para o tipo de nó primário é controlado pelo nível de confiabilidade escolhido. Leia mais sobre os [níveis de confiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> A redução vertical do tipo de nó primário para um número inferior ao mínimo causará a instabilidade ou indisponibilidade do cluster. Isso poderá resultar em perda de dados de seus aplicativos e dos serviços do sistema.
> 
> 

Atualmente, o recurso de escala automática não é controlado pelas cargas que os aplicativos podem relatar ao Service Fabric. Portanto, nesse momento, o dimensionamento automático obtido é meramente controlado pelos contadores de desempenho que são emitidos por cada uma das instâncias do conjunto de dimensionamento de máquinas virtuais.  

Siga estas instruções [para configurar a escala automática para cada conjunto de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Em um cenário de redução de escala, a menos que seu tipo de nó tenha um nível de [durabilidade][durability] de Ouro ou Prata, você precisa chamar o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome de nó apropriado. Para a durabilidade bronze, não é recomendado reduzir mais de um nó de cada vez.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adicione manualmente VMs a um conjunto de escala de tipo de nó/máquina virtual

Ao aumentar, você adicionar mais instâncias de máquina virtual ao conjunto de dimensionamento. Essas instâncias tornam-se os nós que o Service Fabric usa. O Service Fabric sabe quando o conjunto de dimensionamento tem mais instâncias adicionadas (aumentando) e reage automaticamente. 

> [!NOTE]
> Adicionar VMs leva tempo, então não espere que as adições sejam instantâneas. Então planeje adicionar capacidade com bastante antecedência, para permitir por mais de 10 minutos antes que a capacidade do VM esteja disponível para que as réplicas/instâncias de serviço estejam colocadas.
> 

### <a name="add-vms-using-a-template"></a>Adicionar VMs usando um modelo
Siga a amostra/instruções na [galeria de modelos quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada tipo de nó. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Adicionar VMs usando comandos PowerShell ou CLI
O código a seguir obtém um conjunto de dimensionamento por nome e aumenta a **capacidade** do conjunto de dimensionamento em 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Esse código define a capacidade para 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Remova manualmente as VMs de um conjunto de escala de tipo de nó/máquina virtual
Quando você escala em um tipo de nó, você remove as instâncias vm do conjunto de escalas. Se o tipo de nó é nível de durabilidade bronze, a Service Fabric não sabe o que aconteceu e informa que um nó desapareceu. Service Fabric então relata um estado não íntegro para o cluster. Para evitar esse estado ruim, você deve remover explicitamente o nó do cluster e remover o estado do nó.

Os serviços do sistema de malha de serviço são executados no tipo de nó primário em seu cluster. Ao reduzir o tipo de nó primário, nunca reduza o número de instâncias para menos do que o nível de [confiabilidade](service-fabric-cluster-capacity.md) garante. 
 
Para um serviço com estado, você precisa de um determinado número de nós que devem estar sempre ativos para manter a disponibilidade e preservar o estado do serviço. No mínimo, é necessário que o número de nós seja igual à contagem de conjunto de réplicas de destino do serviço/partição.

### <a name="remove-the-service-fabric-node"></a>Remover o nó do Service Fabric

As etapas para remover manualmente o estado do nó aplicam-se apenas aos tipos de nó com um nível de durabilidade *bronze.*  Para o nível de durabilidade *Silver* e *Gold,* essas etapas são feitas automaticamente pela plataforma. Para obter mais informações sobre durabilidade, consulte [Planejamento de capacidade do cluster do Service Fabric][durability].

Para manter os nós do cluster distribuídos uniformemente entre domínios de falha e atualização e, portanto, habilitar sua utilização uniforme, o nó criado mais recentemente deve ser removido primeiro. Em outras palavras, os nós devem ser removidos na ordem inversa à de sua criação. O nó criado mais recentemente é aquele com o maior valor da propriedade `virtual machine scale set InstanceId`. Os exemplos de código a seguir retornam o nó criado mais recentemente.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

O cluster do Service Fabric precisa saber que este nó será removido. Você precisa executar três etapas:

1. Desabilite o nó para que ele não seja mais uma replicação de dados.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Pare o nó de modo que o runtime do Service Fabric seja desligado corretamente e o aplicativo obtenha uma solicitação de encerramento.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Remova o nó do cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Depois que essas três etapas tiverem sido aplicadas ao nó, ele poderá ser removido do conjunto de dimensionamento. Se você estiver usando qualquer camada de durabilidade além [bronze][durability], estas etapas serão executadas para você quando a instância do conjunto de dimensionamento for removida.

O bloco de código a seguir obtém o último nó criado, desabilita, para e remove o nó do cluster.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

No código **sfctl** abaixo, o comando a seguir é usado para obter o valor **node-name** do último nó criado: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Use as seguintes consultas **sfctl** para verificar o status de cada etapa
>
> **Verifique o status de desativação**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Verifique o status de parada**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Reduzir horizontalmente o conjunto de dimensionamento

Agora que o nó do Service Fabric foi removido do cluster, o conjunto de dimensionamento de máquinas virtuais pode ser reduzido horizontalmente. No exemplo a seguir, a capacidade do conjunto de dimensionamento é reduzida em 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Esse código define a capacidade para 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos que podem ser observados no Service Fabric Explorer
Ao escalar verticalmente um cluster, o Service Fabric Explorer refletirá o número de nós (instâncias de conjunto de dimensionamento de máquinas virtuais) que fazem parte do cluster.  No entanto, ao reduzir verticalmente um cluster, você verá o nó removido/instância VM exibida em um estado não íntegro, a menos que você chame o cmdlet [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) pelo nome de nó apropriado.   

Veja a seguir a explicação para esse comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços do sistema do Service Fabric (especificamente, FM) conhecem sobre o número de nós que o cluster tinha/tem. Ao reduzir verticalmente o conjunto de dimensionamento de máquinas virtuais, observe que a VM foi excluída, mas o serviço de sistema do FM ainda admitirá que o nó (mapeado para a VM que foi excluída) retornará. Portanto, o Service Fabric Explorer continua exibindo o nó (embora o estado de integridade possa ser erro ou desconhecido).

Para certificar-se de que um nó será removido quando uma VM for removida, você tem duas opções:

1. Escolha um nível de durabilidade de Gold ou Silver para os tipos de nó no seu cluster, o que fornece a integração de infraestrutura. Isso, em seguida, removerá automaticamente os nós do estado (FM) dos serviços do sistema ao reduzir verticalmente.
Confira [os detalhes sobre os níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

2. Depois que a instância VM tiver sido reduzida verticalmente, você precisará chamar o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Os clusters de Service Fabric exigem um determinado número de nós que devem estar ativos o tempo todo para manter a disponibilidade e preservar o estado – conhecido como "manter o quórum". Desta forma, em geral, não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos para saber também sobre como planejar a capacidade do cluster, atualizar um cluster e particionar os serviços:

* [Planejar a capacidade do cluster](service-fabric-cluster-capacity.md)
* [Atualizações de cluster](service-fabric-cluster-upgrade.md)
* [Serviços com estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
