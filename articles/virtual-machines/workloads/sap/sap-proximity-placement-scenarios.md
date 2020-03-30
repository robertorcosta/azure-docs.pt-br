---
title: Grupos de colocação de proximidade do Azure para aplicações SAP | Microsoft Docs
description: Descreve cenários de implantação do SAP com grupos de colocação de proximidade do Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277592"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupos de colocação de proximidade do Azure para latência de rede ideal com aplicativos SAP
Os aplicativos SAP baseados na arquitetura SAP NetWeaver ou SAP S/4HANA são sensíveis à latência de rede entre o nível de aplicativo SAP e o nível de banco de dados SAP. Essa sensibilidade é o resultado da maior parte da lógica de negócios em execução na camada de aplicativos. Como a camada de aplicativo SAP executa a lógica de negócios, ela emite consultas para o nível de banco de dados em alta frequência, a uma taxa de milhares ou dezenas de milhares por segundo. Na maioria dos casos, a natureza dessas consultas é simples. Eles podem ser executados no nível do banco de dados em 500 microssegundos ou menos.

O tempo gasto na rede para enviar essa consulta do nível do aplicativo para o nível de banco de dados e receber o retorno do resultado tem um grande impacto no tempo necessário para executar processos de negócios. Essa sensibilidade à latência da rede é a razão pela qual você precisa alcançar a latência de rede ideal em projetos de implantação SAP. Consulte [SAP Note #1100926 - FAQ: Desempenho da rede](https://launchpad.support.sap.com/#/notes/1100926/E) para orientações sobre como classificar a latência da rede.

Em muitas regiões do Azure, o número de data centers tem crescido. Esse crescimento também foi desencadeado pela introdução de Zonas de Disponibilidade. Ao mesmo tempo, os clientes, especialmente para sistemas SAP high-end, estão usando SKUs VM mais especiais na família M-Series, ou HANA Large Instances. Esses tipos de máquinas virtuais do Azure não estão disponíveis em todos os data centers em uma região específica do Azure. Por causa dessas duas tendências, os clientes experimentaram latência de rede que não está na faixa ideal. Em alguns casos, essa latência resulta em desempenho abaixo do ideal de seus sistemas SAP.

Para evitar esses problemas, o Azure oferece [grupos de colocação de proximidade.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Esta nova funcionalidade já foi usada para implantar vários sistemas SAP. Para restrições aos grupos de colocação de proximidade, consulte o artigo referido no início deste parágrafo. Este artigo abrange os cenários SAP nos quais grupos de colocação de proximidade do Azure podem ou devem ser usados.

## <a name="what-are-proximity-placement-groups"></a>O que são grupos de colocação de proximidade? 
Um grupo de colocação de proximidade do Azure é uma construção lógica. Quando se é definido, ele está vinculado a uma região azure e a um grupo de recursos do Azure. Quando as VMs são implantadas, um grupo de colocação de proximidade é referenciado por:

- A primeira VM Azure implantada no data center. Você pode pensar na primeira máquina virtual como uma "VM de escopo" que é implantada em um data center baseado em algoritmos de alocação do Azure que são eventualmente combinados com definições de usuário para uma Zona de Disponibilidade específica.
- Todas as VMs subseqüentes foram implantadas que fazem referência ao grupo de colocação de proximidade, para colocar todas as VMs Azure implantadas posteriormente no mesmo data center da primeira máquina virtual.

> [!NOTE]
> Se não houver nenhum hardware host implantado que possa executar um tipo de VM específico no datacenter onde a primeira VM foi colocada, a implantação do tipo vm solicitado não será bem sucedida. Você receberá uma mensagem de falha.

Um único [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) pode ter vários grupos de colocação de proximidade atribuídos a ele. Mas um grupo de colocação de proximidade pode ser atribuído a apenas um grupo de recursos do Azure.

Quando você usar grupos de colocação de proximidade, tenha essas considerações em mente:

- Quando você visa o desempenho ideal para o seu sistema SAP e limita-se a um único datacenter Azure para o sistema usando grupos de colocação de proximidade, você pode não ser capaz de combinar todos os tipos de famílias de VM dentro do grupo de colocação. Essas limitações ocorrem porque o hardware do host necessário para executar um determinado tipo de VM pode não estar presente no datacenter para o qual a "VM escopo" do grupo de colocação foi implantada.
- Durante o ciclo de vida de um sistema SAP, você pode ser forçado a mover o sistema para outro data center. Esse movimento pode ser necessário se você decidir que sua camada HANA DBMS de escala deve, por exemplo, passar de quatro nós para 16 nós, e não há capacidade suficiente para obter 12 VMs adicionais do tipo que você usou no datacenter.
- Devido ao descomissionamento de hardware, a Microsoft pode aumentar as capacidades para um tipo de VM que você usou em um data center diferente, em vez do que você usou inicialmente. Nesse cenário, você pode precisar mover todas as VMs do grupo de colocação de proximidade para outro data center.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Grupos de colocação de proximidade com sistemas SAP que usam apenas VMs Azure
A maioria das implantações do sistema SAP NetWeaver e S/4HANA no Azure não usa [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Para implantações que não usam hana instâncias grandes, é importante fornecer um desempenho ideal entre a camada de aplicativo SAP e o nível DBMS. Para isso, defina um grupo de colocação de proximidade do Azure apenas para o sistema.

Na maioria das implantações de clientes, os clientes constroem um único [grupo de recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) para sistemas SAP. Nesse caso, há uma relação um-para-um entre, por exemplo, o grupo de recursos do sistema ERP de produção e seu grupo de colocação de proximidade. Em outros casos, os clientes organizam seus grupos de recursos horizontalmente e coletam todos os sistemas de produção em um único grupo de recursos. Neste caso, você teria uma relação de um a muitos entre seu grupo de recursos para sistemas SAP de produção e vários grupos de colocação de proximidade para sua produção SAP ERP, SAP BW, e assim por diante.

Evite agrupar vários sistemas de produção SAP ou não-produção em um único grupo de colocação de proximidade. Quando um pequeno número de sistemas SAP ou um sistema SAP e alguns aplicativos circundantes precisam ter comunicação de rede de baixa latência, você pode considerar mover esses sistemas para um grupo de colocação de proximidade. Você deve evitar pacotes de sistemas porque quanto mais sistemas você agrupar em um grupo de colocação de proximidade, maiores são as chances:

- Que você precisa de um tipo de VM que não pode ser executado no datacenter específico no qual o grupo de colocação de proximidade foi escopo.
- Que os recursos de VMs não convencionais, como VMs da série M, eventualmente não serão cumpridos quando você precisa de mais, porque você está adicionando software a um grupo de colocação de proximidade ao longo do tempo.

Aqui está como a configuração ideal, como descrito, se parece:

![Grupos de colocação de proximidade com apenas VMs Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Neste caso, os sistemas SAP únicos são agrupados em um grupo de recursos cada um, com um grupo de colocação de proximidade cada. Não há dependência se você usa configurações de scale-out HANA ou DBMS.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Grupos de colocação de proximidade e HANA Grandes Instâncias
Se alguns de seus sistemas SAP dependerem de [hana grandes instâncias](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para a camada de aplicativos, você poderá experimentar melhorias significativas na latência da rede entre a unidade HANA Large Instances e as VMs Azure quando estiver usando unidades HANA Large Instances que são implantadas em [linhas de revisão 4 ou selos.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) Uma melhoria é que as unidades HANA Large Instances, à medida que são implantadas, implantam com um grupo de colocação de proximidade. Você pode usar esse grupo de colocação de proximidade para implantar suas VMs de camada de aplicativo. Como resultado, essas VMs serão implantadas no mesmo data center que hospeda sua unidade HANA Large Instances.

Para determinar se a unidade HANA Large Instances está implantada em um carimbo ou linha de revisão 4, verifique o artigo [Azure HANA Large Instances control through Azure portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). Na visão geral dos atributos da unidade HANA Large Instances, você também pode determinar o nome do grupo de colocação de proximidade porque ele foi criado quando a unidade HANA Large Instances foi implantada. O nome que aparece na visão geral dos atributos é o nome do grupo de colocação de proximidade em que você deve implantar suas VMs de camada de aplicativo.

Em comparação com sistemas SAP que usam apenas máquinas virtuais Azure, quando você usa HANA Large Instances, você tem menos flexibilidade para decidir quantos [grupos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) usar. Todas as unidades HANA Large Instances de um [inquilino HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) são agrupadas em um único grupo de recursos, conforme descrito neste [artigo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). A menos que você implante em diferentes inquilinos para separar, por exemplo, sistemas de produção e não-produção ou outros sistemas, todas as suas unidades HANA Large Instances serão implantadas em um inquilino HANA Large Instances. Este inquilino tem uma relação de um para um com um grupo de recursos. Mas um grupo separado de colocação de proximidade será definido para cada uma das unidades.

Como resultado, as relações entre os grupos de recursos do Azure e os grupos de colocação de proximidade para um único inquilino serão como mostrado aqui:

![Grupos de colocação de proximidade e HANA Grandes Instâncias](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Exemplo de implantação com grupos de colocação de proximidade
A seguir estão alguns comandos PowerShell que você pode usar para implantar suas VMs com grupos de colocação de proximidade do Azure.

O primeiro passo, depois de fazer login no [Azure Cloud Shell,](https://azure.microsoft.com/features/cloud-shell/)é verificar se você está na assinatura do Azure que deseja usar para a implantação:

<pre><code>
Get-AzureRmContext
</code></pre>

Se você precisar mudar para uma assinatura diferente, você pode fazê-lo executando este comando:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Crie um novo grupo de recursos do Azure executando este comando:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Crie o novo grupo de colocação de proximidade executando este comando:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Implante sua primeira VM no grupo de colocação de proximidade usando um comando como este:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O comando anterior implanta uma VM baseada no Windows. Após o sucesso dessa implantação de VM, o escopo do datacenter do grupo de colocação de proximidade é definido dentro da região do Azure. Todas as implantações subseqüentes de VM que fazem referência ao grupo de colocação de proximidade, como mostrado no comando anterior, serão implantadas no mesmo datacenter do Azure, desde que o tipo VM possa ser hospedado no hardware colocado nesse datacenter e capacidade para esse tipo de VM é Disponível.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combine conjuntos de disponibilidade e Zonas de Disponibilidade com grupos de colocação de proximidade
Uma das desvantagens de usar zonas de disponibilidade para implantações de sistema SAP é que você não pode implantar a camada de aplicativo SAP usando conjuntos de disponibilidade dentro da região específica. Você deseja que a camada de aplicativo SAP seja implantada nas mesmas regiões da camada DBMS. Não é suportado um conjunto de disponibilidade sem uma única VM. Então, anteriormente, você foi forçado a implantar sua camada de aplicativo fazendo referência a uma região. Você perdeu a capacidade de garantir que as VMs da camada de aplicativo fossem espalhadas por diferentes domínios de atualização e falha.

Usando grupos de colocação de proximidade, você pode contornar essa restrição. Aqui está a seqüência de implantação:

- Crie um grupo de colocação de proximidade.
- Implante sua VM âncora, geralmente o servidor DBMS, fazendo referência a uma Zona de Disponibilidade.
- Crie um conjunto de disponibilidade que faz referência ao grupo de proximidade do Azure. (Veja o comando mais tarde neste artigo.)
- Implante as VMs da camada de aplicativo fazendo referência ao conjunto de disponibilidade e ao grupo de colocação de proximidade.

Em vez de implantar a primeira VM como demonstrado na seção anterior, você faz referência a uma Zona de Disponibilidade e ao grupo de colocação de proximidade quando você implanta a VM:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Uma implantação bem-sucedida desta máquina virtual hospedaria a instância de banco de dados do sistema SAP em uma Zona de Disponibilidade. O escopo do grupo de colocação de proximidade é fixado em um dos data centers que representam a Zona de Disponibilidade definida.

Suponha que você implante as VMs de Serviços Centrais da mesma forma que as VMs DBMS, fazendo referência à mesma região ou zonas e aos mesmos grupos de colocação de proximidade. Na próxima etapa, você precisa criar os conjuntos de disponibilidade que deseja usar para a camada de aplicativo do seu sistema SAP.

Você precisa definir e criar o grupo de colocação de proximidade. O comando para criar o conjunto de disponibilidade requer uma referência adicional ao ID do grupo de colocação de proximidade (não o nome). Você pode obter o ID do grupo de colocação de proximidade usando este comando:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Ao criar o conjunto de disponibilidade, você precisa considerar parâmetros adicionais quando estiver usando discos gerenciados (padrão, a menos que especificado em contrário) e grupos de colocação de proximidade:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealmente, você deve usar três domínios de falha. Mas o número de domínios de falha suportados pode variar de região para região. Neste caso, o número máximo de domínios de falha possíveis para as regiões específicas é de dois. Para implantar suas VMs de camada de aplicativo, você precisa adicionar uma referência ao nome do seu conjunto de disponibilidade e ao nome do grupo de colocação de proximidade, conforme mostrado aqui:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

O resultado desta implantação é:
- Uma camada DBMS e serviços centrais para o seu sistema SAP localizado em uma Zona de Disponibilidade ou Zonas de Disponibilidade específicas.
- Uma camada de aplicativo SAP localizada através de conjuntos de disponibilidade nos mesmos data centers do Azure que os VMs ou VMs DBMS.

> [!NOTE]
> Como você implanta um VM DBMS em uma região e o segundo DBMS VM em outra região para criar uma configuração de alta disponibilidade, você precisará de um grupo de colocação de proximidade diferente para cada uma das regiões. O mesmo vale para qualquer conjunto de disponibilidade que você use.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Mova um sistema existente para grupos de colocação de proximidade
Se você já tiver sistemas SAP implantados, você pode querer otimizar a latência de rede de alguns de seus sistemas críticos e localizar a camada de aplicativo e a camada DBMS no mesmo datacenter. Para mover as VMs de uma disponibilidade completa do Azure definida para um grupo de colocação de proximidade existente que já está escopo, você precisa desligar todas as VMs do conjunto de disponibilidade e atribuir o conjunto de disponibilidade ao grupo de colocação de proximidade existente através do portal Azure, PowerShell ou CLI. Se você quiser mover uma VM que não faz parte de um conjunto de disponibilidade em um grupo de colocação de proximidade existente, você só precisa desligar a VM e atribuí-la a um grupo de colocação de proximidade existente. 


## <a name="next-steps"></a>Próximas etapas
Confira a documentação:

- [Cargas de trabalho SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Visualização: Implante VMs para grupos de colocação de proximidade usando o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Visualização: Implante VMs para grupos de colocação de proximidade usando o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Considerações para a implantação do Azure Virtual Machines DBMS para cargas de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

