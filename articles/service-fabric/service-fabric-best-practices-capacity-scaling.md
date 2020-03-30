---
title: Planejamento de capacidade e dimensionamento para o Azure Service Fabric
description: Melhores práticas para planejamento e dimensionamento de clusters do Service Fabric e aplicativos.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258987"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planejamento de capacidade e dimensionamento para o Azure Service Fabric

Antes de criar qualquer cluster de malha de serviço do Azure ou dimensionar recursos de computação que hospedem seu cluster, é importante planejar a capacidade. Para obter mais informações sobre o planejamento de capacidade, confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Para obter mais orientações de práticas recomendadas para escalabilidade de cluster, consulte [considerações sobre escalabilidade do Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Além de considerar as características do tipo de nó e do cluster, você deve esperar que as operações de dimensionamento levem mais de uma hora para serem concluídas para um ambiente de produção. Essa consideração é verdadeira, independentemente do número de VMs que você está adicionando.

## <a name="autoscaling"></a>Dimensionamento automático
Você deve executar operações de dimensionamento através de modelos do Azure Resource Manager, porque é a melhor prática para tratar [configurações]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)de recursos como código . 

O uso de dimensionamento automático através de conjuntos de escala de máquinavirtual fará com que o modelo do Gerenciador de recursos versão defina incorretamente a contagem de instâncias para conjuntos de escala de máquinas virtuais. A definição imprecisa aumenta o risco de que implantações futuras causem operações de dimensionamento não intencionais. Em geral, você deve usar autoscaling se:

* Implantar modelos do Resource Manager com capacidade apropriada declarada não dá suporte a seu caso de uso.
     
   Além do dimensionamento manual, você pode configurar um [pipeline de integração e entrega contínua no Azure DevOps Services usando projetos de implantação de grupo de recursos do Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Este pipeline é comumente acionado por um aplicativo lógico que usa métricas de desempenho de máquinas virtuais consultadas a partir da [API do Azure Monitor REST](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). O pipeline efetivamente faz escalas automáticas com base nas métricas que você deseja, ao mesmo tempo em que otimiza para modelos do Gerenciador de Recursos.
* Você precisa dimensionar horizontalmente apenas um nó de conjunto de escala de máquina virtual de cada vez.
   
   Para dimensionar por três ou mais nós de cada vez, você deve [dimensionar um cluster de malha de serviço adicionando um conjunto de escala de máquina virtual](virtual-machine-scale-set-scale-node-type-scale-out.md). É mais seguro escalar e dimensionar conjuntos de escala de máquinas virtuais horizontalmente, um nó de cada vez.
* Você tem confiabilidade silver ou superior para o seu cluster Service Fabric, e durabilidade silver ou superior em qualquer escala onde você configura regras de autoscaling.
  
   A capacidade mínima para regras de autodimensionamento deve ser igual ou superior a cinco instâncias de máquinavirtual. Ele também deve ser igual ou maior do que o mínimo de Nível de Confiabilidade para o seu tipo de nó primário.

> [!NOTE]
> O tecido de serviço stateful do Service Fabric:/System/InfastructureService/<NODE_TYPE_NAME>> é executado em todos os tipos de nó que têm Prata ou maior durabilidade. É o único serviço de sistema que é suportado para ser executado no Azure em qualquer um dos tipos de nó de clusters.

## <a name="vertical-scaling-considerations"></a>Considerações de dimensionamento vertical

[Dimensionamento vertical](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) de um tipo de nó no Tecido de Serviço Azure requer uma série de passos e considerações. Por exemplo: 

* O cluster deve estar íntegro antes do dimensionamento. Caso contrário, você vai desestabilizar o aglomerado ainda mais.
* O nível de durabilidade de prata ou superior é necessário para todos os tipos de nós de cluster service fabric que hospedam serviços estaduais.

> [!NOTE]
> Seu tipo de nó primário que hospeda serviços de sistema de malha de serviço séis deve ser nível de durabilidade de prata ou maior. Depois de habilitar a durabilidade silver, as operações de cluster, como upgrades, adição ou remoção de nós, e assim por diante serão mais lentas porque o sistema otimiza para a segurança dos dados sobre a velocidade das operações.

Dimensionamento vertical de um conjunto de escala de máquina virtual é uma operação destrutiva. Em vez disso, dimensione horizontalmente seu cluster adicionando um novo conjunto de escala com o SKU desejado. Em seguida, migre seus serviços para o SKU desejado para concluir uma operação de dimensionamento vertical segura. Alterando um recurso de conjunto de escala de máquina virtual O SKU é uma operação destrutiva porque refaz as imagens dos hosts, o que remove todo o estado localmente persistido.

Seu cluster usa propriedades de nó de malha de serviço [e restrições de colocação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) para decidir onde hospedar os serviços do seu aplicativo. Quando estiver dimensionando verticalmente seu tipo de nó `"nodeTypeRef"`primário, declare valores de propriedade idênticos para . Você pode encontrar esses valores na extensão Service Fabric para conjuntos de escala de máquinavirtual. 

O trecho a seguir de um modelo do Gerenciador de recursos mostra as propriedades que você declarará. Ele tem o mesmo valor para os conjuntos de escala recém-provisionados para os que você está dimensionando, e é suportado apenas como um serviço temporário de estado para o seu cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe seu cluster funcionando com vários conjuntos de escala que usam o mesmo `nodeTypeRef` valor de propriedade por mais tempo do que o necessário para concluir uma operação de dimensionamento vertical bem-sucedida.
>
> Valide sempre as operações em ambientes de teste antes de tentar alterações no ambiente de produção. Por padrão, os serviços do sistema de cluster Service Fabric têm uma restrição de colocação apenas para o tipo de nó principal de destino.

Com as propriedades de nó e as restrições de posicionamento declaradas, siga estas etapas uma instância de VM por vez. Isso permite que os serviços do sistema (e seus serviços estaduais) sejam desligados graciosamente na instância de VM que você está removendo à medida que novas réplicas são criadas em outros lugares.

1. A partir do `Disable-ServiceFabricNode` PowerShell, execute com a intenção `RemoveNode` de desativar o nó que você vai remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis narantes, remova a instância da máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminuir o número de VMs por um nesse tipo de nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.
5. Uma vez que todas as VMs se foram (representadas como "Para baixo"), o tecido:/System/InfrastructureService/[nome do nó] mostrará um estado de erro. Em seguida, você pode atualizar o recurso de cluster para remover o tipo de nó. Você pode usar a implantação do modelo ARM ou editar o recurso de cluster através do [gerenciador de recursos do Azure](https://resources.azure.com). Isso iniciará uma atualização de cluster que removerá o serviço fabric:/System/InfrastructureService/[tipo nó] que está em estado de erro.
 6. Depois disso, você pode excluir opcionalmente o VMScaleSet, você ainda verá os nós como "Para baixo" da exibição do Service Fabric Explorer. O último passo seria limpá-los com `Remove-ServiceFabricNodeState` o comando.

## <a name="horizontal-scaling"></a>Dimensionamento em escala horizontal

Você pode fazer o dimensionamento horizontal [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) ou [programáticamente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se você estiver escalando um tipo de nó que tenha durabilidade de Prata ou Ouro, o dimensionamento será lento.

### <a name="scaling-out"></a>Expansão

Dimensione um cluster de malha de serviço aumentando a contagem de instâncias para um conjunto de escala de máquina virtual em particular. Você pode dimensionar programáticamente usando `AzureClient` o ID para a escala desejada definida para aumentar a capacidade.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para dimensionar manualmente, atualize a capacidade na propriedade SKU do recurso de conjunto de [escala de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desejado.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Redução horizontal

Escalar requer mais consideração do que escalar. Por exemplo:

* ServiçoS de sistema de malha são executados no tipo de nó primário em seu cluster. Nunca desligue nem reduza verticalmente o número de instâncias desse tipo de nó para que você tenha menos instâncias do que o que a camada de confiabilidade requer. 
* Para um serviço estadual, você precisa de um certo número de nós que estão sempre à disposição para manter a disponibilidade e preservar o estado do seu serviço. No mínimo, você precisa de um número de nós iguais à contagem de conjuntos de réplicas de destino da partição ou serviço.

Para reduzir manualmente, siga estas etapas:

1. A partir do `Disable-ServiceFabricNode` PowerShell, execute com a intenção `RemoveNode` de desativar o nó que você vai remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis narantes, remova a instância da máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminuir o número de VMs por um nesse tipo de nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3 conforme necessário até que você prover a capacidade desejada. Não reduza o número de instâncias nos tipos de nó primários para menos do que aquilo que a camada de confiabilidade requer. Confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

Para dimensionar manualmente, atualize a capacidade na propriedade SKU do recurso de conjunto de [escala de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desejado.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Você deve preparar o nó para desligamento para escalar em programação. Encontre o nó a ser removido (o nó de instância mais alta). Por exemplo: 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Desativar e remover o nó usando `FabricClient` a`client` mesma instância (neste caso) e a instância do nó (neste`instanceIdString` caso) que você usou no código anterior:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Quando você reduz um cluster, você verá a ocorrência de nó removido/VM exibida em um estado insalubre no Service Fabric Explorer. Para obter uma explicação sobre esse comportamento, consulte [Comportamentos que você pode observar no Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Você pode:
> * Ligue para o [comando Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) com o nome de nó apropriado.
> * Implante o [aplicativo de ajuda de autoescala service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) em seu cluster. Este aplicativo garante que os nós dimensionados sejam eliminados do Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de confiabilidade

O [nível de confiabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) é uma propriedade do recurso de cluster Service Fabric. Não pode ser configurado de forma diferente para tipos de nó individuais. Ele controla o fator de replicação dos serviços do sistema para o cluster e é uma configuração no nível do recurso de cluster. 

O nível de confiabilidade determinará o número mínimo de nós que o tipo de nó primário deve ter. A camada de confiabilidade pode ter os valores a seguir:

* Platinum: Executa os serviços do sistema com uma contagem de conjunto de réplicas de sete e nove nações.
* Ouro: Executa os serviços do sistema com uma contagem de conjunto de réplicas de sete e sete nações.
* Silver: Executa os serviços do sistema com uma contagem de conjunto de réplicas de cinco e cinco nações.
* Bronze: Executa os serviços do sistema com uma contagem de réplicas de três e três nações.

O nível mínimo recomendado de confiabilidade é Prata.

O nível de confiabilidade é definido na seção de propriedades do [recurso Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), semelhante a isto:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Tipos de nó executados com durabilidade Bronze não têm _nenhum privilégio_. Os trabalhos de infra-estrutura que afetam suas cargas de trabalho apátridas não serão interrompidos ou atrasados, o que pode afetar suas cargas de trabalho. 
>
> Use a durabilidade Bronze somente para tipos de nós que executam cargas de trabalho sem estado. Para cargas de trabalho de produção, execute Silver ou superior para garantir a consistência do estado. Escolha a confiabilidade correta com base nas diretrizes na [documentação de planejamento de capacidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

O nível de durabilidade deve ser definido em dois recursos. Um deles é o perfil de extensão do recurso de [conjunto de escala de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

O outro recurso `nodeTypes` está em desenvolvimento no [recurso Microsoft.ServiceFabric/clusters:](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

* Crie um cluster em VMs ou computadores executando o Windows Server: [Criação de cluster de malha de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Crie um cluster em VMs ou computadores executando o Linux: [Crie um cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
