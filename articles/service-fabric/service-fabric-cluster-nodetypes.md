---
title: Tipos de nó e conjuntos de dimensionamento de máquinas virtuais
description: Saiba como os tipos de nó de malha de serviço do Azure se relacionam com conjuntos de escala de máquinas virtuais e como se conectar remotamente a uma instância de conjunto de escala ou nó de cluster.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199709"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nó do Service Fabric e os conjuntos de dimensionamento da máquina virtual

[Conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets) são um recurso de computação do Azure. Você pode usar os conjuntos de dimensionamento para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que você define em um cluster Azure Service Fabric configura exatamente um conjunto de escala: vários tipos de nó não podem ser apoiados pelo mesmo conjunto de escalas e um tipo de nó não deve (na maioria dos casos) ser apoiado por vários conjuntos de escala. Uma exceção a isso está na rara situação de [dimensionamento vertical](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) de um `nodeTypeRef` tipo de nó, quando você tem temporariamente dois conjuntos de escala com o mesmo valor enquanto as réplicas são migradas do original para o conjunto de escala atualizado.

O tempo de execução do Service Fabric é instalado em cada máquina virtual na escala definida pela extensão *Microsoft.Azure.ServiceFabric* Virtual Machine. Cada tipo de nó pode ser escalado vertical ou horizontalmente de forma independente, ter a SKU de sistema operacional em execução em cada nó de cluster, ter conjuntos diferentes de portas abertas e usar métricas de capacidade diferentes.

A figura a seguir mostra um cluster que tem dois tipos de nó, chamados *FrontEnd* e *BackEnd*. Cada tipo de nó tem cinco nós.

![Um cluster com dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapeando instâncias de conjuntos de dimensionamento de máquinas virtuais para nós

Conforme mostrado na figura anterior, as instâncias do conjunto de dimensionamento começam na instância 0 e, em seguida, aumentam em 1. A numeração é refletida nos nomes do nó. Por exemplo, o nó BackEnd_0 é a instância 0 do conjunto de dimensionamento de BackEnd. Esse conjunto de dimensionamento específico tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você escala um conjunto de dimensionamento verticalmente, uma nova instância é criada. O novo nome da instância do conjunto de dimensionamento geralmente será o nome do conjunto de dimensionamento mais o número de instância seguinte. Em nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapear balanceadores de carga do conjunto de dimensionamento para tipos de nó e conjuntos de dimensionamento

Se você tiver implantado o cluster no portal do Azure ou usado o modelo do exemplo do Azure Resource Manager, todos os recursos em um grupo de recursos serão listados. Você pode ver os balanceadores de carga para cada conjunto de dimensionamento ou tipo de nó. O nome do balanceador de carga usa o seguinte formato: **LB-&lt;nome do tipo de nó&gt;**. Um exemplo é LB-sfcluster4doc-0, conforme mostrado na figura a seguir:

![Recursos][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Extensão da máquina virtual do tecido de serviço

Service Fabric Virtual Machine Extension é usado para inicializar o fabric de serviço para máquinas virtuais do Azure e configurar a segurança do nó.

A seguir está um trecho da extensão Service Fabric Virtual Machine:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

A seguir estão as descrições da propriedade:

| **Nome** | **Valores permitidos** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
| name | string | Nome único para extensão |
| type | "ServiceFabricLinuxNode" ou "ServiceFabricWindowsNode" | Identifica o tecido de serviço do SISTEMA OPERACIONAL é bootstrapping to |
| upgrade automáticoMinorVersion | true ou false | Habilite a atualização automática das versões menores do SF Runtime |
| publicador | Microsoft.Azure.ServiceFabric | Nome do editor de extensão Service Fabric |
| clusterEndpont | string | Ponto final uri:PORT para gerenciamento |
| nodeTypeRef | string | Nome do nóType |
| durabilidadeNível | bronze, prata, ouro, platina | Tempo permitido para pausar a infra-estrutura imutável do Azure |
| habilitarParallelJobs | true ou false | Habilitar Computação ParalelaTrabalhos como remover VM e reiniciar VM na mesma escala definida em paralelo |
| nicPrefixOverride | string | Prefixo de sub-rede como "10.0.0.0/24" |
| commonNames | string[] | Nomes comuns de certificados de cluster instalados |
| x509StoreName | string | Nome da loja onde o certificado de cluster instalado está localizado |
| typeHandlerVersion | 1,1 | Versão de Extensão. 1.0 versão clássica de extensão são recomendadas para atualizar para 1.1 |
| Datapath | string | Caminho para a unidade usada para salvar o estado para serviços do sistema Service Fabric e dados de aplicativos.

## <a name="next-steps"></a>Próximas etapas

* Consulte [visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [segurança de clusters](service-fabric-cluster-security.md).
* [Conectar-se remotamente](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) uma instância específica do conjunto de dimensionamento
* [Atualizar os valores de intervalo de porta RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nas VMs de cluster após a implantação
* [Alterar o nome de usuário administrador e a senha](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para as VMs do cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
