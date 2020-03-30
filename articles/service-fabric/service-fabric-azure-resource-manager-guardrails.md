---
title: Guardrails de implantação do Service Fabric Azure Resource Manager
description: Este artigo fornece uma visão geral dos erros comuns cometidos ao implantar um cluster de malha de serviço através do Azure Resource Manager e como evitá-los.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368571"
---
# <a name="service-fabric-guardrails"></a>Guardrails de malha de serviço 
Ao implantar um cluster de malha de serviço, os guardrails são colocados no lugar, o que falhará com uma implantação do Azure Resource Manager no caso de uma configuração de cluster inválida. As seções a seguir fornecem uma visão geral dos problemas comuns de configuração de cluster e das etapas necessárias para mitigar esses problemas. 

## <a name="durability-mismatch"></a>Incompatibilidade de durabilidade
### <a name="overview"></a>Visão geral
O valor de durabilidade para um tipo de nó de malha de serviço é definido em duas seções diferentes de um modelo do Azure Resource Manager. A seção de extensão 'Definir escala de máquina virtual' do recurso Virtual Machine Scale Set' e a seção Tipo de nó do recurso de cluster 'Malha de malha de serviço'. É um requisito que o valor de durabilidade nessas seções corresponda, caso contrário, a implantação de recursos falhará.

A seção a seguir contém um exemplo de uma incompatibilidade de durabilidade entre a configuração de durabilidade do conjunto de escala de máquina virtual e a configuração de durabilidade do tipo de nó de malha de malha de serviço:  

**Configuração de durabilidade do conjunto de escala da máquina virtual**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Configuração de durabilidade do tipo nó de malha de serviço** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Mensagens de erro
* A incompatibilidade de durabilidade do conjunto de escala da máquina virtual não corresponde ao nível de durabilidade atual do tipo de nó de malha de malha de serviço
* A durabilidade do conjunto de escala de máquina virtual não corresponde ao nível de durabilidade do tipo de nó de malha de serviço de destino
* A durabilidade do conjunto de escala de máquina virtual corresponde ao nível de durabilidade atual do fabric de serviço ou ao nível de durabilidade do tipo de nó de malha de serviço de destino 

### <a name="mitigation"></a>Atenuação
Para corrigir uma incompatibilidade de durabilidade, que é indicada por qualquer uma das mensagens de erro acima:
1. Atualize o nível de durabilidade na extensão Virtual Machine Scale Set ou na seção Tipo de nó de malha de serviço do modelo Azure Resource Manager para garantir que os valores correspondam.
2. Reimplante o modelo do Azure Resource Manager com os valores atualizados.


## <a name="seed-node-deletion"></a>Exclusão de nó de sementes 
### <a name="overview"></a>Visão geral
Um cluster Service Fabric tem uma propriedade [de nível](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) de confiabilidade que é usada para determinar o número de réplicas de serviços do sistema que são executados no tipo de nó principal do cluster. O número de réplicas necessárias determinará o número mínimo de nós que devem ser mantidos no tipo de nó primário do cluster. Se o número de nódulos no tipo de nó primário ficar abaixo do mínimo necessário para o nível de confiabilidade, o cluster se tornará instável.  

### <a name="error-messages"></a>Mensagens de erro 
A operação de remoção de nó de sementes foi detectada e será rejeitada. 
* Esta operação resultaria {0} em apenas potenciais nós de sementes {1} para permanecer no cluster, enquanto são necessários como mínimo.
* A {0} remoção de nódulos {1} de sementes resultaria na queda do cluster devido à perda do quórum do nó de sementes. O número máximo de nódulos de sementes {2}que podem ser removidos por vez é .
 
### <a name="mitigation"></a>Atenuação 
Certifique-se de que seu tipo de nó principal tenha máquinas virtuais suficientes para a confiabilidade especificada em seu cluster. Você não será capaz de remover uma Máquina Virtual se ela trouxer o Conjunto de Escala de Máquina Virtual abaixo do número mínimo de nós para o nível de confiabilidade dado.
* Se o nível de confiabilidade estiver corretamente especificado, certifique-se de que você tenha nódulos suficientes no tipo de nó principal necessáriopara o nível de confiabilidade. 
* Se o nível de confiabilidade estiver incorreto, inicie uma alteração no recurso Service Fabric para diminuir o nível de confiabilidade primeiro antes de iniciar qualquer operação do Conjunto de Escala de Máquina Virtual e aguarde que ele seja concluído.
* Se o nível de confiabilidade for Bronze, siga [estas etapas](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) para reduzir graciosamente o cluster.

## <a name="next-steps"></a>Próximas etapas
* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Solucionar problemas tecido de serviço: [guias de solução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
