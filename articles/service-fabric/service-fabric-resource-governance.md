---
title: Governança de recursos para contêineres e serviços
description: O Azure Service Fabric permite especificar os limites de recurso para os serviços executados dentro ou fora de contêineres.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 85520876d7f0c89450b572d28dee6cb66ed2231d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772373"
---
# <a name="resource-governance"></a>Governança de recursos

Ao executar vários serviços no mesmo nó ou cluster, é possível que um serviço consuma mais recursos, privando outros serviços no processo. Esse problema é conhecido como o problema do “vizinho barulhento”. O Service Fabric do Azure permite ao desenvolvedor especificar reservas e limites por serviço para assegurar recursos e limitar o uso de recursos.

> Antes de continuar lendo este artigo, recomendamos que você se familiarize com o [Modelo de aplicativo do Service Fabric](service-fabric-application-model.md) e o [Modelo de hospedagem do Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de governança de recursos

Há suporte para a governança de recursos no Service Fabric de acordo com o [pacote de serviço](service-fabric-application-model.md). Os recursos que são atribuídos ao pacote de serviço podem ser subdivididos entre pacotes de códigos. Os limites de recurso especificados também indicam a reserva dos recursos. O Service Fabric dá suporte à especificação de CPU e memória por pacote de serviço, com duas [métricas](service-fabric-cluster-resource-manager-metrics.md) internas:

* *CPU* (nome da métrica `servicefabric:/_CpuCores`): um núcleo lógico que está disponível no computador host. Todos os núcleos em todos os nós têm o mesmo peso.

* *Memória* (nome `servicefabric:/_MemoryInMB`métrico ): A memória é expressa em megabytes, e mapeia a memória física disponível na máquina.

Para essas duas métricas, o [Gerenciador de Recursos de Cluster](service-fabric-cluster-resource-manager-cluster-description.md) controla a capacidade total do cluster, a carga em cada nó do cluster e os recursos restantes no cluster. Essas duas métricas são equivalentes a qualquer outro usuário ou métrica personalizada. Todos os recursos existentes podem ser usados com elas:

* O cluster pode ser [equilibrado](service-fabric-cluster-resource-manager-balancing.md) de acordo com essas duas métricas (comportamento padrão).
* O cluster pode ser [desfragmentado](service-fabric-cluster-resource-manager-defragmentation-metrics.md) de acordo com essas duas métricas.
* Ao [descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md), a capacidade armazenada em buffer pode ser definida para essas duas métricas.

Não há suporte para [relatórios de cargas dinâmicas](service-fabric-cluster-resource-manager-metrics.md) nessas métricas e as cargas dessas métricas são definidas no momento da criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governança de recursos

Atualmente, o runtime do Service Fabric não fornece uma reserva de recursos. Quando um processo ou um contêiner é aberto, o runtime define os limites de recursos para as cargas que foram definidas no momento da criação. Além disso, o runtime rejeita a abertura de novos pacotes de serviço disponíveis quando os recursos são excedidos. Para entender melhor como o processo funciona, vamos usar um exemplo de um nó com dois núcleos de CPU (o mecanismo de governança de memória é equivalente):

1. Primeiro, um contêiner é colocado no nó, solicitando um núcleo de CPU. O runtime abre o contêiner e define o limite de CPU para um núcleo. O contêiner não poderá usar mais de um núcleo.

2. Em seguida, uma réplica do serviço é colocada no nó e o pacote de serviço correspondente especifica um limite de um núcleo de CPU. O runtime abre o pacote de códigos e define o limite de CPU para um núcleo.

Neste ponto, a soma dos limites é igual à capacidade do nó. Um processo e um contêiner estão em execução com um núcleo cada e não interferem um no outro. O Service Fabric não coloca mais nenhum contêiner ou réplica quando eles estão especificando o limite de CPU.

No entanto, há duas situações em que outros processos podem brigar pela CPU. Nessas situações, um processo e um contêiner do nosso exemplo poderá ter o problema do vizinho barulhento:

* *Combinação de serviços e contêineres controlados e não controlados*: se um usuário criar um serviço sem nenhuma governança de recursos especificada, o runtime considerará que ele não está consumindo nenhum recurso e poderá colocá-lo no nó de nosso exemplo. Nesse caso, esse novo processo efetivamente consume alguns recursos da CPU à custa dos serviços que já estão sendo executados no nó. Há duas soluções para este problema. Não misturar serviços controlados e não controlado no mesmo cluster ou usar [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que esses dois tipos de serviços não terminem no mesmo conjunto de nós.

* *Quando outro processo for iniciado no nó, fora do Service Fabric (por exemplo, um serviço do SO)*: nessa situação, o processo fora do Service Fabric também competirá pela CPU com os serviços existentes. A solução para esse problema é configurar as capacidades de nó corretamente para a conta para a sobrecarga do sistema operacional, conforme mostrado na próxima seção.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração de cluster para habilitar a governança de recursos

Quando o nó for iniciado e ingressado no cluster, o Service Fabric detectará a quantidade de memória e o número de núcleos disponíveis e então definirá as capacidades de nó para esses dois recursos.

Para deixar algum espaço do buffer para o sistema operacional e para outros processos que possam estar em execução no nó, o Service Fabric usará somente 80% dos recursos disponíveis no nó. Esse percentual é configurável e pode ser alterado no manifesto do cluster.

Este é um exemplo de como instruir o Service Fabric a usar 50% da CPU disponível e 70% da memória disponível:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Para a maioria dos clientes e cenários, a detecção automática de capacidades de nó para a CPU e a memória é a configuração recomendada (a detecção automática é ligada por padrão). No entanto, se você precisar de configuração manual completa das capacidades de nó, você pode configurá-los por tipo de nó usando o mecanismo para descrever os nódulos no cluster. Aqui está um exemplo de como configurar o tipo de nó com quatro núcleos e 2 GB de memória:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a detecção automática de recursos disponíveis está habilitada e as capacidades de nó são definidas manualmente no manifesto do cluster, o Service Fabric verifica se o nó tem recursos suficientes para dar suporte à capacidade definida pelo usuário:

* Se as capacidades de nó definidas no manifesto forem menores ou iguais aos recursos disponíveis no nó, o Service Fabric usará as capacidades especificadas no manifesto.

* Se as capacidades do nó definidas no manifesto forem maiores que os recursos disponíveis, o Service Fabric usará os recursos disponíveis como as capacidades do nó.

A detecção automática de recursos disponíveis poderá ser desativada se não for necessária. Para desativá-la, altere a seguinte configuração:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para obter o desempenho ideal, a seguinte configuração também deve ser ativada no manifesto do cluster:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> A partir da versão 7.0 do Service Fabric, atualizamos a regra de como as capacidades de recurso do nó são calculadas nos casos em que o usuário fornece manualmente os valores para capacidades de recurso de nó. Vamos considerar o seguinte cenário:
>
> * Há 10 núcleos de cpu no total no nó
> * O SF está configurado para usar 80% dos recursos totais para os serviços do usuário (configuração padrão), o que deixa um buffer de 20% para os outros serviços em execução no nó (serviços do sistema incl. Service Fabric)
> * O usuário decide substituir manualmente a capacidade de recurso do nó para a métrica dos núcleos da CPU e define-a em 5 núcleos
>
> Alteramos a regra sobre como a capacidade disponível para serviços de usuário da Malha de Serviço é calculada da seguinte forma:
>
> * Antes do Service Fabric 7.0, a capacidade disponível para serviços do usuário seria calculada para **5 núcleos** (buffer de capacidade de 20% é ignorado)
> * A partir do Service Fabric 7.0, a capacidade disponível para serviços de usuário seria calculada para **4 núcleos** (buffer de capacidade de 20% não é ignorado)

## <a name="specify-resource-governance"></a>Especificar a governança de recurso

Os limites da governança de recursos são especificados no manifesto do aplicativo (seção ServiceManifestImport), conforme mostrado no seguinte exemplo:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, o pacote de serviço chamado **ServicePackageA** obtém um núcleo nos nós em que ele é colocado. Este pacote de serviço contém dois pacotes de código **(CodeA1** e **CodeA2),** e ambos especificam o `CpuShares` parâmetro. A proporção de CpuShares 512:256 divide o núcleo entre os dois pacotes de códigos.

Portanto, neste exemplo, CodeA1 obtém dois terços de um núcleo e CodeA2 obtém um terço de um núcleo (e uma reserva de garantia flexível dele). Se CpuShares não forem especificados para pacotes de códigos, o Service Fabric dividirá os núcleos igualmente entre eles.

Os limites de memória são absolutos e, portanto, os dois pacotes de códigos são limitados a 1.024 MB de memória (e a uma reserva de garantia reversível da mesma). Os pacotes de códigos (contêineres ou processos) não podem alocar mais memória do que esse limite e tentar fazer isso resultará em uma exceção de memória insuficiente. Para que a imposição do limite de recursos funcione, todos os pacotes de códigos em um pacote de serviço devem ter limites de memória especificados.

### <a name="using-application-parameters"></a>Usando parâmetros de aplicativo

Ao especificar as configurações de governança de recursos, é possível usar [parâmetros de aplicativos](service-fabric-manage-multiple-environment-app-configuration.md) para gerenciar várias configurações de aplicativos. O exemplo a seguir mostra o uso de parâmetros do aplicativo:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

Neste exemplo, os valores de parâmetro padrão são definidos para o ambiente de produção, onde cada pacote de serviço obteria 4 núcleos e 2 GB de memória. É possível alterar os valores padrão com arquivos de parâmetro do aplicativo. Neste exemplo, um arquivo de parâmetro pode ser usado para testar o aplicativo localmente, onde receberia menos recursos do que em produção:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Especificar a governança de recursos com os parâmetros do aplicativo está disponível a partir do Service Fabric versão 6.1.<br>
>
> Quando são usados parâmetros de aplicativo para especificar a governança dos recursos, o Service Fabric não pode ser rebaixado para uma versão anterior à versão 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Aplicando os limites de recursos para serviços de usuário

Embora a aplicação de governança de recursos aos serviços da Malha de Serviços garanta que esses serviços controlados por recursos não possam exceder sua cota de recursos, muitos usuários ainda precisam executar alguns de seus serviços de Malha de Serviço em modo não governado. Ao usar serviços de malha de serviço não regidos, é possível encontrar situações em que serviços "descontrolados" desgovernados consomem todos os recursos disponíveis nos nós de malha de serviço, o que pode levar a problemas sérios como:

* Fome de recursos de outros serviços em execução nos nós (incluindo serviços do sistema Service Fabric)
* Nódulos terminando em um estado insalubre
* APIs de gerenciamento de cluster de malha de serviço sem resposta

Para evitar que essas situações ocorram, a Service Fabric permite que você *imponha os limites de recursos para todos os serviços de usuário da Service Fabric em execução no nó* (tanto governados quanto não governados) para garantir que os serviços do usuário nunca usem mais do que a quantidade especificada de recursos. Isso é alcançado definindo o valor para a configuração EnforceUserServiceMetricCapacidades na seção PlacementAndLoadBalanceing do ClusterManifest para true. Esta configuração é desatada por padrão.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Observações adicionais:

* A aplicação do limite `servicefabric:/_CpuCores` de `servicefabric:/_MemoryInMB` recursos só se aplica às métricas e aos recursos
* A aplicação do limite de recursos só funciona se as capacidades de nó para as métricas de recursos estiverem disponíveis para o Service Fabric, seja através de mecanismo de detecção automática, ou através de usuários especificando manualmente as capacidades do nó (conforme explicado na [configuração do Cluster para habilitar](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) a seção de controle de recursos).Se as capacidades de nó não estiverem configuradas, o recurso de aplicação do limite de recursos não poderá ser usado, uma vez que o Service Fabric não pode saber quantos recursos reservar para serviços do usuário.A Service Fabric emitirá um aviso de saúde se "EnforceUserServiceMetricCapacidades" for verdadeira, mas as capacidades de nó não estiverem configuradas.

## <a name="other-resources-for-containers"></a>Outros recursos para contêineres

Além da CPU e da memória, é possível especificar outros limites de recursos para contêineres. Esses limites são especificados no nível do pacote de códigos e aplicados quando o contêiner é iniciado. Ao contrário da CPU e da memória, o Gerenciador de Recursos de Cluster não reconhece esses recursos e não faz verificações de capacidade nem balanceamento de carga para eles.

* *MemorySwapInMB*: a quantidade de memória de troca que pode ser usada por um contêiner.
* *MemoryReservationInMB*: o limite dinâmico para governança de memória que é imposto apenas quando a contenção de memória é detectada no nó.
* *CpuPercent*: o percentual de CPU que pode ser usado pelo contêiner. Se os limites de CPU forem especificados para o pacote de serviço, esse parâmetro será efetivamente ignorado.
* *MaximumIOps*: máximo de IOPS que um contêiner pode usar (leitura e gravação).
* *MaximumIOBytesps*: máximo de E/S (bytes por segundo) que um contêiner pode usar (leitura e gravação).
* *BlockIOWeight*: o peso de E/S do bloco em relação a outros contêineres.

Esses recursos podem ser combinados com a CPU e a memória. Este é um exemplo de como especificar recursos adicionais para contêineres:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Gerenciador de Recursos de Cluster, leia [Introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Para saber mais sobre o modelo de aplicativo, pacotes de serviço e pacotes de códigos e como as réplicas são mapeadas para eles, leia [Modelar um aplicativo no Service Fabric](service-fabric-application-model.md).
