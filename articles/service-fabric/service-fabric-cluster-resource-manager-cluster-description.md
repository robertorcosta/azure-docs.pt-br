---
title: Descreva um cluster usando o Cluster Resource Manager
description: Descreva um cluster de malha de serviço especificando domínios de falha, domínios de upgrade, propriedades de nó e capacidades de nó para o Gerenciador de recursos de cluster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258766"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descreva um cluster de malha de serviço usando o Gerenciador de recursos do cluster
O recurso Cluster Resource Manager do Azure Service Fabric fornece vários mecanismos para descrever um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nó

Durante o tempo de execução, o Cluster Resource Manager usa essas informações para garantir a alta disponibilidade dos serviços em execução no cluster. Ao aplicar essas regras importantes, ele também tenta otimizar o consumo de recursos dentro do cluster.

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é qualquer área da falha coordenada. Uma única máquina é um domínio de falha. Ele pode falhar por conta própria por várias razões, desde falhas na fonte de alimentação até falhas na condução de falhas até firmware de NIC ruim. 

As máquinas conectadas ao mesmo switch Ethernet estão no mesmo domínio de falha. Assim como as máquinas que compartilham uma única fonte de energia ou em um único local. 

Como é natural que falhas de hardware se sobreponham, os domínios de falha são inerentemente hierárquicos. Eles são representados como URIs em Malha de Serviço.

É importante que os domínios de falha sejam configurados corretamente porque o Service Fabric usa essas informações para colocar os serviços com segurança. A Service Fabric não quer colocar serviços de tal forma que a perda de um domínio de falha (causada pela falha de algum componente) faça com que um serviço desça. 

No ambiente Azure, o Service Fabric usa as informações de domínio de falha fornecidas pelo ambiente para configurar corretamente os nós no cluster em seu nome. Para instâncias autônomas do Service Fabric, os domínios de falha são definidos no momento em que o cluster está configurado. 

> [!WARNING]
> É importante que as informações de domínio de falha fornecidas ao Service Fabric seja precisa. Por exemplo, digamos que os nós do cluster service fabric estão sendo executados dentro de 10 máquinas virtuais, rodando em 5 hosts físicos. Nesse caso, mesmo que haja dez máquinas virtuais, há apenas cinco domínios de falha (de nível superior) diferentes. O compartilhamento do mesmo host físico faz com que as VMs compartilhem o mesmo domínio de falha raiz, porque as VMs experimentam falha coordenada se seu host físico falhar.  
>
> A Service Fabric espera que o domínio de falha de um nó não mude. Outros mecanismos de garantia de alta disponibilidade das VMs, como [HA-VMs,](https://technet.microsoft.com/library/cc967323.aspx)podem causar conflitos com o Service Fabric. Esses mecanismos utilizam migração transparente de VMs de um host para outro. Eles não reconfiguram ou notificam o código de execução dentro da VM. Como tal, eles não são *suportados* como ambientes para executar clusters de malha de serviço. 
>
> O Service Fabric deve ser a única tecnologia de alta disponibilidade empregada. Mecanismos como migração de VM ao vivo e SANs não são necessários. Se esses mecanismos forem usados em conjunto com o Service Fabric, eles _reduzem_ a disponibilidade e a confiabilidade do aplicativo. A razão é que eles introduzem complexidade adicional, adicionam fontes centralizadas de falha e usam estratégias de confiabilidade e disponibilidade que entram em conflito com as da Service Fabric. 
>
>

No gráfico a seguir, colorimos todas as entidades que contribuem para domínios de falhas e listamos todos os diferentes domínios de falhas que resultam. Neste exemplo, temos datacenters (“DC”), racks (“R”) e folhas (“B”). Se cada lâmina contém mais de uma máquina virtual, pode haver outra camada na hierarquia de domínio de falha.

<center>

![Nódulos organizados através de domínios de falhas][Image1]
</center>

Durante o tempo de execução, o Service Fabric Cluster Resource Manager considera os domínios de falha nos layouts de cluster e planos. As réplicas estaduais ou instâncias apátridas de um serviço são distribuídas para que estejam em domínios de falhas separados. A distribuição do serviço entre domínios de falhas garante que a disponibilidade do serviço não seja comprometida quando um domínio de falha falha em qualquer nível da hierarquia.

O Cluster Resource Manager não se importa com quantas camadas existem na hierarquia de domínio de falha. Ele tenta garantir que a perda de qualquer parte da hierarquia não afete os serviços que nele são executados. 

É melhor que o mesmo número de nódulos esteja em cada nível de profundidade na hierarquia do domínio da falha. Se a "árvore" de domínios de falha estiver desequilibrada em seu cluster, será mais difícil para o Cluster Resource Manager descobrir a melhor alocação de serviços. Layouts de domínio de falhas desequilibradas significam que a perda de alguns domínios afeta mais a disponibilidade de serviços do que outros domínios. Como resultado, o Cluster Resource Manager é dividido entre dois objetivos: 

* Ele quer usar as máquinas nesse domínio "pesado", colocando serviços nelas. 
* Ele quer colocar serviços em outros domínios para que a perda de um domínio não cause problemas. 

Qual é a aparência de um domínio desequilibrado? O diagrama a seguir mostra dois layouts de cluster diferentes. No primeiro exemplo, os nós são distribuídos uniformemente entre os domínios de falha. No segundo exemplo, um domínio de falha tem muito mais nós do que os outros domínios de falha. 

<center>

![Dois layouts de cluster diferentes][Image2]
</center>

No Azure, a escolha de qual domínio de falha contém um nó é gerenciada para você. Mas dependendo do número de nós que você provida, você ainda pode acabar com domínios de falha que têm mais nós neles do que em outros. 

Por exemplo, digamos que você tem cinco domínios de falha no cluster, mas provisão sete nós para um tipo de nó **(NodeType).** Neste caso, os dois primeiros domínios de falha acabam com mais nós. Se você continuar a implantar mais instâncias **do NodeType** com apenas algumas instâncias, o problema fica pior. Por esta razão, recomendamos que o número de nós em cada tipo de nó seja um múltiplo do número de domínios de falha.

## <a name="upgrade-domains"></a>Domínios de atualização
Os domínios de upgrade são outro recurso que ajuda o Service Fabric Cluster Resource Manager a entender o layout do cluster. Os domínios de upgrade definem conjuntos de nós que são atualizados ao mesmo tempo. Os domínios de upgrade ajudam o Cluster Resource Manager a entender e orquestrar operações de gerenciamento como upgrades.

Os domínios de upgrade são muito parecidos com domínios de falha, mas com algumas diferenças principais. Primeiro, áreas de falhas coordenadas de hardware definem domínios de falha. Os domínios de upgrade, por outro lado, são definidos por política. Você decide quantos você quer, em vez de deixar o ambiente ditar o número. Você pode ter tantos domínios de upgrade quanto você não. Outra diferença entre domínios de falha e domínios de upgrade é que os domínios de upgrade não são hierárquicos. Em vez disso, eles são mais como uma etiqueta simples. 

O diagrama a seguir mostra três domínios de upgrade listrados em três domínios de falha. Ele também mostra uma possível colocação para três réplicas diferentes de um serviço de estado, onde cada um acaba em diferentes domínios de falha e upgrade. Essa colocação permite a perda de um domínio de falha enquanto está no meio de uma atualização de serviço e ainda tem uma cópia do código e dados.  

<center>

![Posicionamento com domínios de falha e upgrade][Image3]
</center>

Existem prós e contras para ter um grande número de domínios de upgrade. Mais domínios de upgrade significam que cada etapa da atualização é mais granular e afeta um número menor de nós ou serviços. Menos serviços têm que se mover de cada vez, introduzindo menos agitação no sistema. Isso tende a melhorar a confiabilidade, pois menos do serviço é afetado por qualquer problema introduzido durante a atualização. Mais domínios de upgrade também significam que você precisa de buffer menos disponível em outros nós para lidar com o impacto da atualização. 

Por exemplo, se você tiver cinco domínios de upgrade, os nós em cada um estão lidando com cerca de 20% do seu tráfego. Se você precisa derrubar esse domínio de upgrade para um upgrade, essa carga geralmente precisa ir a algum lugar. Como você tem quatro domínios de upgrade restantes, cada um deve ter espaço para cerca de 5% do tráfego total. Mais domínios de upgrade significam que você precisa de menos buffer nos nós do cluster. 

Considere se você tinha 10 domínios de upgrade em vez disso. Nesse caso, cada domínio de upgrade estaria lidando com apenas cerca de 10% do tráfego total. Quando um upgrade passa pelo cluster, cada domínio precisaria ter espaço para apenas cerca de 1,1% do tráfego total. Mais domínios de upgrade geralmente permitem que você execute seus nós em maior utilização, porque você precisa de menos capacidade reservada. O mesmo vale para domínios de falhas.  

A desvantagem de ter muitos domínios de upgrade é que os upgrades tendem a demorar mais. O Service Fabric espera um curto período depois que um domínio de upgrade é concluído e realiza verificações antes de começar a atualizar o próximo. Esses atrasos permitem detectar problemas introduzidos pela atualização antes que ela continue. Essa compensação é aceitável, pois isso evita que alterações incorretas afetem grande parte do serviço de uma só vez.

A presença de poucos domínios de upgrade tem muitos efeitos colaterais negativos. Enquanto cada domínio de upgrade está baixo e sendo atualizado, uma grande parte de sua capacidade total não está disponível. Por exemplo, se você tiver apenas três domínios de upgrade, você está anotando cerca de um terço da sua capacidade total de serviço ou cluster por vez. Ter muito do seu serviço para baixo de uma vez não é desejável porque você precisa de capacidade suficiente no resto do seu cluster para lidar com a carga de trabalho. Manter esse buffer significa que durante a operação normal, esses nós estão menos carregados do que seriam de outra forma. Isso aumenta o custo de execução de seu serviço.

Não há um limite real para o número total de domínios de falha ou de atualização em um ambiente nem restrições sobre como eles se sobrepõem. Mas há padrões comuns:

- Domínios de falhas e domínios de upgrade mapeados 1:1
- Um domínio de upgrade por nó (instância física ou virtual do SO)
- Um modelo "listrado" ou "matrix" onde os domínios de falha e upgrade formam uma matriz com máquinas geralmente executando as diagonais

<center>

![Layouts de domínios de falha e atualização][Image4]
</center>

Não há melhor resposta para qual layout escolher. Cada um tem prós e contras. Por exemplo, o modelo de 1FD:1UD é simples de configurar. O modelo de um domínio de upgrade por modelo de nó é mais parecido com o que as pessoas estão acostumadas. Durante as atualizações, cada nó é atualizado independentemente. Isso é semelhante à forma como pequenos conjuntos de computadores eram atualizados manualmente no passado.

O modelo mais comum é a matriz FD/UD, onde os domínios de falha e os domínios de upgrade formam uma tabela e nós são colocados a partir da diagonal. Esse é o modelo usado por padrão nos clusters do Service Fabric no Azure. Para clusters com muitos nós, tudo acaba parecendo um padrão dematriz denso.

> [!NOTE]
> Os clusters de malha de serviço hospedados no Azure não suportam alterar a estratégia padrão. Apenas clusters autônomos oferecem essa personalização.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de domínio de falha e de atualização e o comportamento resultante
### <a name="default-approach"></a>Abordagem padrão
Por padrão, o Cluster Resource Manager mantém os serviços equilibrados entre os domínios de falha e upgrade. Isso é modelado como uma [restrição](service-fabric-cluster-resource-manager-management-integration.md). A restrição para domínios de falha e atualização afirma: "Para uma determinada partição de serviço, nunca deve haver uma diferença maior do que uma no número de objetos de serviço (instâncias de serviço apátridas ou réplicas de serviço sem estado) entre quaisquer dois domínios no mesmo nível de hierarquia.

Digamos que essa restrição forneça uma garantia de "diferença máxima". A restrição para domínios de falha e atualização impede certos movimentos ou arranjos que violam a regra.

Por exemplo, digamos que temos um cluster com seis nós, configurado com cinco domínios de falha e cinco domínios de upgrade.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora vamos dizer que criamos um serviço com um valor **targetReplicaSetSize** (ou, para um serviço apátrida, **InstanceCount**) de cinco. As réplicas recaem sobre N1 a N5. Na verdade, N6 nunca será usado, independentemente de quantos serviços como este você criar. Mas por quê? Vamos observar a diferença entre o layout atual e o que aconteceria se N6 fosse escolhido.

Aqui está o layout que temos e o número total de réplicas por falha e domínio de upgrade:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este layout é equilibrado em termos de nós por domínio de falha e domínio de upgrade. Também é equilibrado em termos de número de réplicas por falha e domínio de upgrade. Cada domínio possui o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como as réplicas seriam distribuídas?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Este layout viola nossa definição da garantia de "diferença máxima" para a restrição de domínio de falha. FD0 tem duas réplicas, enquanto FD1 tem zero. A diferença entre FD0 e FD1 é um total de dois, que é maior do que a diferença máxima de um. Como a restrição é violada, o Cluster Resource Manager não permite esse arranjo. Da mesma forma, se pegássemos N2 e N6 (em vez de N1 e N2), teríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este layout é equilibrado em termos de domínios de falha. Mas agora está violando a restrição de domínio de upgrade, porque ud0 tem zero réplicas e UD1 tem duas. Este layout também é inválido e não será escolhido pelo Cluster Resource Manager.

Essa abordagem de distribuição de réplicas com estado ou de instâncias sem estado fornece a melhor tolerância a falhas possível. Se um domínio cair, o número mínimo de réplicas/instâncias será perdido. 

Por outro lado, essa abordagem pode ser muito restrita e não permitir que o cluster utilize todos os recursos. Para determinadas configurações de cluster, alguns nós não podem ser usados. Isso pode fazer com que a Service Fabric não coloque seus serviços, resultando em mensagens de aviso. No exemplo anterior, alguns dos nós de cluster não podem ser usados (N6 no exemplo). Mesmo se você adicionasse nós a esse cluster (N7-N10), as réplicas/instâncias seriam colocadas apenas em N1–N5 devido às restrições nos domínios de falha e upgrade. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Abordagem alternativa

O Cluster Resource Manager suporta outra versão da restrição para domínios de falha e upgrade. Permite a colocação, ao mesmo tempo em que garante um nível mínimo de segurança. A restrição alternativa pode ser declarada da seguinte forma: "Para uma determinada partição de serviço, a distribuição de réplicas entre os domínios deve garantir que a partição não sofra uma perda de quórum." Digamos que essa restrição forneça uma garantia de "garantia de quórum seguro". 

> [!NOTE]
> Para um serviço com estado, definimos *perda de quorum* em uma situação em que a maioria das réplicas de partição estão inativas ao mesmo tempo. Por exemplo, se **TargetReplicaSetSize** for cinco, um conjunto de três réplicas representa quórum. Da mesma forma, se **TargetReplicaSetSize** for seis, quatro réplicas são necessárias para quórum. Em ambos os casos, não mais do que duas réplicas podem ser desligadas ao mesmo tempo se a partição quiser continuar funcionando normalmente. 
>
> Para um serviço apátrida, não existe perda de *quórum.* Os serviços apátridas continuam funcionando normalmente, mesmo que a maioria das instâncias desçam ao mesmo tempo. Então, vamos nos concentrar em serviços estaduais no resto deste artigo.
>

Voltar para o exemplo anterior. Com a versão "cofre de quórum" da restrição, todos os três layouts seriam válidos. Mesmo que fd0 falhou no segundo layout ou UD1 falhou no terceiro layout, a partição ainda teria quórum. (A maioria das réplicas ainda estaria em cima.) Com esta versão da restrição, N6 quase sempre pode ser utilizado.

A abordagem "seguro de quórum" fornece mais flexibilidade do que a abordagem "diferença máxima". A razão é que é mais fácil encontrar distribuições de réplicas que são válidas em quase qualquer topologia de cluster. No entanto, essa abordagem não garante a melhor característica de tolerância a falhas, pois algumas falhas são piores que outras. 

Na pior das hipóteses, a maioria das réplicas pode ser perdida com a falha de um domínio e uma réplica adicional. Por exemplo, em vez de três falhas serem necessárias para perder quórum com cinco réplicas ou instâncias, agora você pode perder uma maioria com apenas duas falhas. 

### <a name="adaptive-approach"></a>Abordagem adaptável
Como ambas as abordagens têm pontos fortes e fracos, introduzimos uma abordagem adaptativa que combina essas duas estratégias.

> [!NOTE]
> Este é o comportamento padrão começando com a versão 6.2 do Service Fabric. 
> 
> A abordagem adaptável usa a lógica “diferença máxima” por padrão e muda para a lógica “segurança de quorum” somente quando necessário. O Cluster Resource Manager descobre automaticamente qual estratégia é necessária olhando como o cluster e os serviços estão configurados.
> 
> O Gerenciador de recursos do Cluster deve usar a lógica "baseada em quórum" para um serviço, ambas as condições são verdadeiras:
>
> * **TargetReplicaSetSize** para o serviço é igualmente divisível pelo número de domínios de falha e pelo número de domínios de upgrade.
> * O número de nós é menor ou igual ao número de domínios de falha multiplicado pelo número de domínios de upgrade.
>
> Tenha em mente que o Cluster Resource Manager usará essa abordagem para serviços apátridas e estaduais, embora a perda de quórum não seja relevante para serviços apátridas.

Vamos voltar ao exemplo anterior e assumir que um cluster agora tem oito nós. O cluster ainda está configurado com cinco domínios de falha e cinco domínios de upgrade, e o valor **TargetReplicaSetSize** de um serviço hospedado nesse cluster permanece cinco. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Como todas as condições necessárias estão satisfeitas, o Cluster Resource Manager usará a lógica "baseada em quórum" na distribuição do serviço. Isso permite o uso de N6-N8. Uma possível distribuição de serviços neste caso pode ser assim:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se o valor **targetReplicaSetSize** do seu serviço for reduzido a quatro (por exemplo), o Cluster Resource Manager notará essa alteração. Ele será retomado usando a lógica de "diferença máxima" porque **targetReplicaSetSize** não é dividível pelo número de domínios de falha e domínios de upgrade mais. Como resultado, certos movimentos de réplica ocorrerão para distribuir as quatro réplicas restantes nos notes N1-N5. Dessa forma, a versão "diferença máxima" do domínio de falha e da lógica de domínio de upgrade não é violada. 

No layout anterior, se o valor **TargetReplicaSetSize** for cinco e N1 for removido do cluster, o número de domínios de upgrade se tornará igual a quatro. Novamente, o Cluster Resource Manager começa a usar a lógica de "diferença máxima" porque o número de domínios de upgrade não divide mais o valor **targetreplicaSetSize** do serviço. Como resultado, a réplica R1, quando construída novamente, tem que pousar no N4 para que a restrição para o domínio de falha e atualização não seja violada.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurando domínios de falha e atualização
Nas implantações de malha de serviço hospedadas pelo Azure, os domínios de falha e os domínios de upgrade são definidos automaticamente. O Service Fabric seleciona e usa as informações de ambiente do Azure.

Se você estiver criando seu próprio cluster (ou quiser executar uma topologia específica em desenvolvimento), você pode fornecer o domínio de falha e atualizar as informações de domínio você mesmo. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três data centers (cada um com três racks). Este cluster também tem três domínios de upgrade listrados nesses três data centers. Aqui está um exemplo da configuração em ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

Este exemplo usa clusterConfig.json para implantações autônomas:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Quando você está definindo clusters via Azure Resource Manager, o Azure atribui domínios de falha e domínios de upgrade. Assim, a definição de seus tipos de nó e conjuntos de escala de máquina virtual no modelo do Azure Resource Manager não inclui informações sobre domínio de falha ou domínio de upgrade.
>

## <a name="node-properties-and-placement-constraints"></a>Restrições de posicionamento e propriedades do nó
Às vezes (na verdade, na maioria das vezes) você vai querer garantir que certas cargas de trabalho são executadas apenas em certos tipos de nódulos no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs, e outras podem não. 

Um grande exemplo de direcionamento de hardware para cargas de trabalho específicas é quase todas as arquiteturas n-tier. Certas máquinas servem como front-end ou lado de serviço de API do aplicativo e são expostas aos clientes ou à internet. Diferentes computadores, normalmente com recursos de hardware diferentes, lidam com o trabalho das camadas de computação ou armazenamento. Normalmente, eles _não_ são expostos diretamente a clientes ou à Internet. 

A Service Fabric espera que, em alguns casos, cargas de trabalho específicas possam precisar ser executadas em configurações de hardware específicas. Por exemplo: 

* Um aplicativo n-tier existente foi "levantado e deslocado" para um ambiente de Malha de Serviço.
* Uma carga de trabalho deve ser executada em hardware específico para razões de isolamento de desempenho, escala ou segurança.
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por razões de política ou consumo de recursos.

Para suportar esses tipos de configurações, o Service Fabric inclui tags que você pode aplicar a nós. Essas marcas são chamadas de *propriedades de nó*. *Restrições de colocação* são as instruções anexadas aos serviços individuais que você seleciona para uma ou mais propriedades de nó. Restrições de posicionamento definem onde os serviços devem ser executados. O conjunto de restrições é extensível. Qualquer par de tecla/valor pode funcionar. 

<center>

![Diferentes cargas de trabalho para um layout de cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Propriedades de nó incorporadas
Service Fabric define algumas propriedades de nó padrão que podem ser usadas automaticamente para que você não precise defini-las. As propriedades padrão definidas em cada nó são **NodeType** e **NodeName**. 

Por exemplo, você pode escrever `"(NodeType == NodeType03)"`uma restrição de colocação como . **NodeType** é uma propriedade comumente usada. É útil porque corresponde 1:1 com um tipo de máquina. Cada tipo de computador corresponde a um tipo de carga de trabalho em um aplicativo de n camadas tradicional.

<center>

![Restrições de colocação e propriedades do nó][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Restrições de colocação e sintaxe de propriedade de nó 
O valor especificado na propriedade do nó pode ser uma string, booleana ou assinada longamente. A declaração no serviço é chamada de *restrição* de colocação porque restringe onde o serviço pode ser executado no cluster. A restrição pode ser qualquer declaração booleana que opera nas propriedades do nó no cluster. Os seletores válidos nestas instruções booleanas são:

* Verificações condicionais para criar declarações específicas:

  | de | Sintaxe |
  | --- |:---:|
  | "igual a" | "==" |
  | "diferente de" | "!=" |
  | "maior que" | ">" |
  | "maior ou igual a" | ">=" |
  | "menor que" | "<" |
  | "menor ou igual a" | "<=" |

* Declarações booleanas para agrupamento e operações lógicas:

  | de | Sintaxe |
  | --- |:---:|
  | "e" | "&&" |
  | "ou" | "&#124;&#124;" |
  | "não" | "!" |
  | "agrupar como instrução única" | "()" |

Aqui estão alguns exemplos de declarações básicas de restrição:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

O serviço pode ser posicionado somente em nós em que a instrução de restrição de posicionamento geral é avaliada como "True". Os nós que não têm uma propriedade definida não correspondem a nenhuma restrição de colocação que contenha a propriedade.

Digamos que as seguintes propriedades do nó foram definidas para um tipo de nó em ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

O exemplo a seguir mostra propriedades de nó definidas via ClusterConfig.json para implantações autônomas ou Template.json para clusters hospedados no Azure. 

> [!NOTE]
> No modelo do Azure Resource Manager, o tipo de nó geralmente é parametrizado. Pareceria `"[parameters('vmNodeType1Name')]"` ao invés de NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Você pode criar *restrições* de colocação de serviço para um serviço da seguinte forma:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se todos os nós do NodeType01 forem válidos, você `"(NodeType == NodeType01)"`também poderá selecionar esse tipo de nó com a restrição .

As restrições de colocação de um serviço podem ser atualizadas dinamicamente durante o tempo de execução. Se você precisar, você pode mover um serviço no cluster, adicionar e remover requisitos, e assim por diante. Service Fabric garante que o serviço permaneça em dia e esteja disponível mesmo quando esses tipos de alterações forem feitas.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

As restrições de colocação são especificadas para cada instância de serviço nomeada. As atualizações sempre assumem o lugar (substituem) do que foi especificado anteriormente.

A definição do cluster define as propriedades em um nó. Alterar as propriedades de um nó requer um upgrade para a configuração do cluster. Atualizar as propriedades de um nó requer que cada nó afetado seja reiniciado para informar suas novas propriedades. A Service Fabric gerencia essas atualizações de rolamento.

## <a name="describing-and-managing-cluster-resources"></a>Descrevendo e gerenciando recursos de cluster
Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerenciar o consumo de recursos no cluster. Gerenciar recursos de cluster pode significar algumas coisas diferentes. 

Primeiro, é necessário garantir que os computadores não sejam sobrecarregados. Isso significa garantir que eles não executem mais serviços do que conseguem tratar. 

Em segundo lugar, há o equilíbrio e a otimização, que são fundamentais para a execução eficiente dos serviços. Ofertas de serviços econômicas ou sensíveis ao desempenho não podem permitir que alguns nós sejam quentes enquanto outros estão frios. Os nódulos quentes levam à contenção de recursos e ao baixo desempenho. Os nódulos frios representam recursos desperdiçados e aumento de custos. 

O Service Fabric representa os recursos como *métricas*. As métricas são qualquer recurso lógico ou físico que você queira descrever para o Service Fabric. Exemplos de métricas são "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre os recursos físicos que a Service Fabric pode reger em nós, consulte [a governança de recursos](service-fabric-resource-governance.md). Para obter informações sobre as métricas padrão usadas pelo Gerenciador de recursos do Cluster e como configurar métricas personalizadas, consulte [este artigo](service-fabric-cluster-resource-manager-metrics.md).

As métricas são diferentes das restrições de colocação e propriedades do nó. Propriedades do nó são descritores estáticos dos nós propriamente ditos. As métricas descrevem os recursos que os nódulos têm e que os serviços consomem quando são executados em um nó. Uma propriedade de nó pode ser **HasSSD** e pode ser definida como verdadeira ou falsa. A quantidade de espaço disponível nesse SSD e quanto é consumido pelos serviços seria uma métrica como "DriveSpaceInMb". 

Assim como para restrições de colocação e propriedades de nó, o Service Fabric Cluster Resource Manager não entende o que os nomes das métricas significam. Os nomes de métrica são apenas cadeias de caracteres. É uma boa prática declarar unidades como parte dos nomes métricos que você cria quando eles podem ser ambíguos.

## <a name="capacity"></a>Capacity
Se você desligou todo *o balanceamento de*recursos, o Service Fabric Cluster Resource Manager ainda garantiria que nenhum nó ultrapassasse sua capacidade. É possível gerenciar saturações de capacidade, a menos que o cluster esteja muito cheio ou que a carga de trabalho seja maior do que qualquer nó. A capacidade é outra *restrição* que o Cluster Resource Manager usa para entender o quanto de um recurso um nó tem. A capacidade restante também é rastreada para o cluster como um todo. 

A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Por exemplo, a métrica pode ser "ClientConnections" e um nó pode ter uma capacidade para "ClientConnections" de 32.768. Outros nódulos podem ter outros limites. Um serviço em execução nesse nó pode dizer que está consumindo atualmente 32.256 da métrica "ClientConnections".

Durante o tempo de execução, o Cluster Resource Manager rastreia a capacidade restante no cluster e nos nós. Para rastrear a capacidade, o Cluster Resource Manager subtrai o uso de cada serviço da capacidade de um nó onde o serviço é executado. Com essas informações, o Cluster Resource Manager pode descobrir onde colocar ou mover réplicas para que os nós não ultrapassem a capacidade.

<center>

![Capacidade e nós de cluster][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Você pode ver capacidades definidas no manifesto do cluster. Aqui está um exemplo para ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Aqui está um exemplo de capacidades definidas via ClusterConfig.json para implantações autônomas ou Template.json para clusters hospedados no Azure: 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

A carga de um serviço muitas vezes muda dinamicamente. Digamos que a carga de uma réplica de "ClientConnections" mudou de 1.024 para 2.048. O nó em que ele estava sendo executado então tinha uma capacidade de apenas 512 restantes para essa métrica. Agora que a colocação da réplica ou instância é inválida, porque não há espaço suficiente nesse nó. O Gerenciador de recursos do cluster tem que recuperar o nó abaixo da capacidade. Reduz a carga no nó que está acima da capacidade movendo uma ou mais das réplicas ou instâncias desse nó para outros nós. 

O Cluster Resource Manager tenta minimizar o custo de movimentação de réplicas. Você pode aprender mais sobre [o custo do movimento](service-fabric-cluster-resource-manager-movement-cost.md) e sobre o [reequilíbrio de estratégias e regras.](service-fabric-cluster-resource-manager-metrics.md)

## <a name="cluster-capacity"></a>Capacidade do cluster
Como o Service Fabric Cluster Resource Manager impede que o cluster geral fique muito cheio? Com carga dinâmica, não há muito que possa fazer. Os serviços podem ter seu pico de carga independentemente das ações que o Cluster Resource Manager toma. Como resultado, seu grupo com muito espaço para a cabeça hoje pode ser desalimentado se houver um pico amanhã. 

Os controles no Cluster Resource Manager ajudam a evitar problemas. A primeira coisa que você pode fazer é impedir a criação de novas cargas de trabalho que fariam com que o cluster ficasse cheio.

Digamos que você crie um serviço apátrida, e ele tem alguma carga associada a ele. O serviço se preocupa com a métrica "DiskSpaceInMb". O serviço consumirá cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Você deseja criar três instâncias do serviço. Isso significa que você precisa de 15 unidades de "DiskSpaceInMb" para estar presente no cluster para que você até crie essas instâncias de serviço.

O Cluster Resource Manager calcula continuamente a capacidade e o consumo de cada métrica para que possa determinar a capacidade restante no cluster. Se não houver espaço suficiente, o Cluster Resource Manager rejeita a chamada para criar um serviço.

Como a exigência é apenas que 15 unidades estejam disponíveis, você pode alocar esse espaço de muitas maneiras diferentes. Por exemplo, pode haver uma unidade de capacidade restante em 15 nódulos diferentes, ou três unidades restantes de capacidade em cinco lupas diferentes. Se o Cluster Resource Manager pode reorganizar as coisas para que haja cinco unidades disponíveis em três nós, ele coloca o serviço. Normalmente, é possível reorganizar o cluster, a menos que ele esteja quase cheio ou que os serviços existentes não possam ser consolidados por alguma razão.

## <a name="buffered-capacity"></a>Capacidade tampão
A capacidade tamponada é outra característica do Cluster Resource Manager. Ela permite reservar uma parte da capacidade total do nó. Este buffer de capacidade é usado apenas para colocar serviços durante atualizações e falhas de nó. 

A capacidade tamponada é especificada globalmente por métrica para todos os nós. O valor que você escolhe para a capacidade reservada é uma função do número de domínios de falha e upgrade que você tem no cluster. Mais domínios de falha e upgrade significam que você pode escolher um número menor para sua capacidade tamponada. Se você tiver mais domínios, poderá esperar que quantidades menores de cluster fiquem indisponíveis durante atualizações e falhas. Especificar a capacidade tamponada só faz sentido se você também tiver especificado a capacidade do nó para uma métrica.

Aqui está um exemplo de como especificar a capacidade tamponada em ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Aqui está um exemplo de como especificar a capacidade tamponada via ClusterConfig.json para implantações autônomas ou Template.json para clusters hospedados no Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

A criação de novos serviços falha quando o cluster está sem capacidade em buffer para uma métrica. Impedir a criação de novos serviços para preservar o buffer garante que atualizações e falhas não façam com que os nós ultrapassem a capacidade. A capacidade tamponada é opcional, mas recomendamos em qualquer cluster que defina uma capacidade para uma métrica.

O Cluster Resource Manager expõe essas informações de carga. Para cada métrica, as informações incluem: 
- As configurações de capacidade tamponadas.
- A capacidade total.
- O consumo atual.
- Se cada métrica é considerada equilibrada ou não.
- Estatísticas sobre o desvio padrão.
- Os nódulos que têm mais e menos carga.  
  
O código a seguir mostra um exemplo dessa saída:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre a arquitetura e o fluxo de informações no Cluster Resource Manager, consulte visão [geral da arquitetura do Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md).
* Definir métricas de desfragmentação é uma maneira de consolidar a carga em números em vez de espalhá-la. Para saber como configurar a desfragmentação, consulte [Desfragmentação de métricas e carga em Malha de Serviço](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Comece desde o início e [obtenha uma introdução ao Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Para saber como o Cluster Resource Manager gerencia e equilibra a carga no cluster, consulte [Equilibrando o cluster de malha de serviço](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
