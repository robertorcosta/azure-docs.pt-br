---
title: Planeje uma implantação de cluster de malha de serviço do Azure
description: Saiba mais sobre o planejamento e a preparação para uma implantação de cluster de malha de malha de serviço de produção no Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1762a6975448301957579b3437a8af5c89b3accd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193469"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planeje e prepare-se para uma implantação de cluster

Planejar e preparar para a implantação de um cluster de produção é muito importante.  Há muitos fatores a considerar.  Este artigo orienta você através das etapas de preparação de sua implantação de cluster.

## <a name="read-the-best-practices-information"></a>Leia as informações sobre as melhores práticas
Para gerenciar com sucesso aplicativos e clusters do Azure Service Fabric, existem operações que recomendamos que você realize para otimizar a confiabilidade do seu ambiente de produção.  Para obter mais informações, leia [o aplicativo Service Fabric e as práticas recomendadas do cluster.](service-fabric-best-practices-overview.md)

## <a name="select-the-os-for-the-cluster"></a>Selecione o Sistema Operacional para o cluster
O Service Fabric permite a criação de clusters do Service Fabric em qualquer VM ou computador que esteja executando o Windows Server ou o Linux.  Antes de implantar seu cluster, você deve escolher o SISTEMA OPERACIONAL: Windows ou Linux.  Cada nó (máquina virtual) no cluster executa o mesmo SISTEMA OPERACIONAL, você não pode misturar VMs Windows e Linux no mesmo cluster.

## <a name="capacity-planning"></a>planejamento de capacidade
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns pontos a serem considerados como parte desse processo.

* O número inicial de tipos de nó para o seu cluster 
* As propriedades de cada tipo de nó (tamanho, número de instâncias, principal, internet, número de VMs, etc.)
* As características de confiabilidade e durabilidade do cluster

### <a name="select-the-initial-number-of-node-types"></a>Selecione o número inicial de tipos de nó
Primeiro, é necessário descobrir para que o cluster que você está criando será utilizado. Que tipos de aplicativos você planeja implantar nesse cluster? O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet? Os serviços (que compõem o aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Um cluster de malha de serviço pode consistir em mais de um tipo de nó: um tipo de nó primário e um ou mais tipos de nó não-primário. Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquinas virtuais. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. [As propriedades do nó e as restrições de colocação][placementconstraints] podem ser configuradas para restringir serviços específicos a tipos específicos de nó.  Para obter mais informações, leia [o número de tipos de nós que o cluster precisa para começar](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Selecione propriedades de nó para cada tipo de nó
Os tipos de nó definem o VM SKU, número e propriedades das VMs no conjunto de escalas associados.

O tamanho mínimo de VMs para cada tipo de nó é determinado pelo [nível de durabilidade][durability] escolhido para o tipo de nó.

O número mínimo de VMs para o tipo de nó primário é determinado pela [camada de confiabilidade][reliability] que você escolhe.

Consulte as recomendações mínimas para [tipos de nó primário,](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance) [cargas de trabalho estaduais em tipos de nó não primários](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)e [cargas de trabalho sem estado em tipos de nó não primários](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads).

Mais do que o número mínimo de nós deve ser baseado no número de réplicas do aplicativo/serviços que você deseja executar neste tipo de nó.  [O planejamento de capacidade para aplicativos de malha de serviço ajuda](service-fabric-capacity-planning.md) a estimar os recursos necessários para executar seus aplicativos. Você sempre pode escalar o cluster para cima ou para baixo mais tarde para ajustar para alterar a carga de trabalho do aplicativo. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Use discos efêmeros do Sistema Operacional para conjuntos de escala de máquinas virtuais

*Os discos do Sistema Operacional Efêmero* são armazenamento criado na máquina virtual local (VM) e não salvos no azure storage remoto. Eles são recomendados para todos os tipos de nó de malha de serviço (principal e secundário), porque em comparação com discos tradicionais persistentes do sistema operacional, discos efêmeros do sistema operacional:

* Reduza a latência de leitura/gravação para o disco DO SO
* Habilite operações mais rápidas de gerenciamento de nó de reset/reimagem
* Reduza os custos gerais (os discos são gratuitos e não incorrem em custo adicional de armazenamento)

Os discos do Sistema Operacional Efêmero não são um recurso específico do Service Fabric, mas sim uma característica dos *conjuntos* de escala de máquina virtual do Azure que são mapeados para os tipos de nó service fabric. Usá-los com o Service Fabric requer o seguinte no modelo do Cluster Azure Resource Manager:

1. Certifique-se de que seus tipos de nó especifiquem [tamanhos de VM suportados](../virtual-machines/windows/ephemeral-os-disks.md) para discos efêmeros do Sistema Operacional Efêmero e que o tamanho da VM tenha tamanho de cache suficiente para suportar o tamanho do disco do SISTEMA OPERACIONAL (veja *Nota* abaixo).) Por exemplo:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Certifique-se de selecionar um tamanho de VM com um tamanho de cache igual ou maior do que o tamanho do disco do SISTEMA OPERACIONAL da própria VM, caso contrário, sua implantação do Azure pode resultar em erro (mesmo que seja inicialmente aceito).

2. Especifique uma versão`vmssApiVersion`de `2018-06-01` conjunto de escala de máquina virtual () de ou posterior:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Na seção de conjunto de escala `Local` de `diffDiskSettings`máquina virtual do seu modelo de implantação, especifique a opção para:

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

Para obter mais informações e outras opções de configuração, consulte [discos efêmeros do Sistema Operacional para VMs Azure](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecione os níveis de durabilidade e confiabilidade para o cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que as VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (por exemplo, reinicialização de VM, recriação de imagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e os serviços com estado. Nos tipos de nó não primários, esse privilégio permite que o Service Fabric pause quaisquer solicitações de infraestrutura de nível de VM (como reinicialização de VM, reinicialização de VM e migração de VM) que afetem os requisitos de quorum para os serviços com estado.  Para obter vantagens dos diferentes níveis e recomendações sobre qual nível usar e quando, consulte [As características de durabilidade do cluster][durability].

A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar nesse cluster no tipo de nó primário. Quanto maior for o número de réplicas, mais confiáveis os serviços do sistema serão no cluster.  Para obter vantagens dos diferentes níveis e recomendações sobre qual nível usar e quando, consulte [As características de confiabilidade do cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Habilitar proxy reverso e/ou DNS
Geralmente, os serviços que se conectam entre si em um cluster podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós de um cluster estão na mesma rede local. Para facilitar a conexão entre os serviços, a Service Fabric fornece serviços adicionais: um [serviço De DNS](service-fabric-dnsservice.md) e um [serviço proxy reverso.](service-fabric-reverseproxy.md)  Ambos os serviços podem ser habilitados ao implantar um cluster.

Uma vez que muitos serviços, especialmente serviços contêineres, podem ter um nome de URL existente, ser capaz de resolvê-los usando o protocolo DNS padrão (em vez do protocolo de Serviço de Nomeação) é conveniente, especialmente em cenários de "elevador e mudança" de aplicativos. Isso é exatamente o que o serviço DNS faz. Ele permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade.

O proxy reverso aborda os serviços no cluster que expõem pontos finais HTTP (incluindo HTTPS). O proxy reverso simplifica muito a chamada de outros serviços, fornecendo um formato URI específico.  O proxy reverso também lida com as etapas de resolução, conexão e repetição necessárias para que um serviço se comunique com outro.

## <a name="prepare-for-disaster-recovery"></a>Preparar para a recuperação de desastre
Uma parte crítica da entrega de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos diferentes de falhas. Isso é especialmente importante para falhas não planejadas e fora de seu controle. [Prepare-se para a recuperação de desastres](service-fabric-disaster-recovery.md) descreve alguns modos de falha comuns que podem ser desastres se não forem modelados e gerenciados corretamente. Também discute mitigações e ações a serem tomadas se um desastre acontecer de qualquer maneira.

## <a name="production-readiness-checklist"></a>Lista de verificação de preparação para produção
O aplicativo e o cluster estão prontos para receber tráfego de produção? Antes de implantar seu cluster para produção, execute a [lista de verificação de prontidão de produção](service-fabric-production-readiness-checklist.md). Mantenha seu aplicativo e cluster funcionando sem problemas, trabalhando os itens nesta lista de verificação. Recomendamos fortemente que todos esses itens sejam retirados antes de entrar em produção.

## <a name="next-steps"></a>Próximas etapas
* [Crie um cluster de malha de serviço executando o Windows](service-fabric-best-practices-overview.md)
* [Crie um cluster de malha de serviço executando o Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster