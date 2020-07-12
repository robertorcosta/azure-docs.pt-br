---
title: Práticas recomendadas de rede Service Fabric do Azure
description: Práticas recomendadas e considerações de design para gerenciar a conectividade de rede usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 853e53d32f87f81e5db587de2654f83037930da7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261133"
---
# <a name="networking"></a>Rede

Conforme você cria e gerencia clusters do Azure Service Fabric, você fornece conectividade de rede para seus nós e seus aplicativos. Os recursos de rede incluem intervalos de endereços IP, redes virtuais, balanceadores de carga e grupos de segurança de rede. Neste artigo, você aprenderá as melhores práticas para esses recursos.

Examine os [padrões de rede Service Fabric](./service-fabric-patterns-networking.md) do Azure para saber como criar clusters que usam os seguintes recursos: rede virtual ou sub-rede existente, endereço IP público estático, balanceador de carga somente interno ou balanceador de carga interno e externo.

## <a name="infrastructure-networking"></a>Rede de infraestrutura
Maximize o desempenho de sua Máquina Virtual com a Rede Acelerada declarando a propriedade enableAcceleratedNetworking no modelo do Resource Manager. O seguinte snippet refere-se a NetworkInterfaceConfigurations de um conjunto de dimensionamento de máquinas virtuais que habilita a Rede Acelerada:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
O cluster do Service Fabric pode ser provisionado no [Linux com a Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md) e no [Windows com a Rede Acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md).

A rede acelerada tem suporte para SKUs da série de máquinas virtuais do Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e MS/MMS. A Rede Acelerada foi testada com êxito usando o SKU Standard_DS8_v3 em 23/1/2019 em um Cluster do Windows no Service Fabric e usando o Standard_DS12_v2 em 29/01/2019 em um Cluster do Linux no Service Fabric.

Para habilitar a Rede Acelerada em um cluster existente do Service Fabric, primeiro você precisará [Expandir um cluster do Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais](./virtual-machine-scale-set-scale-node-type-scale-out.md) para executar o seguinte:
1. Provisionar um NodeType com a Rede Acelerada habilitada
2. Migrar serviços e seus estados para o NodeType provisionado com a Rede Acelerada habilitada

A expansão da infraestrutura é necessária para habilitar a Rede Acelerada em um cluster existente, pois a habilitação da Rede Acelerada in-loco causará tempo de inatividade, já que ela exige que todas as máquinas virtuais em um conjunto de disponibilidade sejam [paradas e desalocadas antes de habilitar a Rede Acelerada em uma NIC existente](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Rede de cluster

* Os clusters do Service Fabric podem ser implantados em uma rede virtual existente seguindo as etapas descritas em [Padrões de rede do Service Fabric](./service-fabric-patterns-networking.md).

* Os NSGs (grupos de segurança de rede) são recomendados para tipos de nós que restringem o tráfego de entrada e saída para o cluster. Verifique se as portas necessárias estão abertas no NSG. Por exemplo: ![ Service Fabric regras NSG][NSGSetup]

* O tipo de nó primário, que contém os serviços do sistema do Service Fabric, não precisa ser exposto por meio do balanceador de carga externo e pode ser exposto por um [balanceador de carga interno](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Use um [endereço IP público estático](./service-fabric-patterns-networking.md#static-public-ip-address-1) para seu cluster.

## <a name="application-networking"></a>Rede de aplicativo

* Para executar cargas de trabalho de contêiner do Windows, use o [modo de rede aberto](./service-fabric-networking-modes.md#set-up-open-networking-mode) para facilitar a comunicação de serviço a serviço.

* Use um proxy reverso, como o [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) ou o [proxy reverso do Service Fabric](./service-fabric-reverseproxy.md), para expor portas de aplicativo comuns, como 80 ou 443.

* Para contêineres do Windows hospedados em computadores gapped que não podem efetuar pull de camadas base do armazenamento em nuvem do Azure, substitua o comportamento da camada estrangeira usando o sinalizador [--Allow-unredistributable-artefatos](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) no daemon do Docker.

## <a name="next-steps"></a>Próximas etapas

* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
