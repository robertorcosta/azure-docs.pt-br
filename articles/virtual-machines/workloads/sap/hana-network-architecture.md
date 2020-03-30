---
title: Arquitetura de rede do SAP HANA do Azure (Instâncias Grandes) | Microsoft Docs
description: arquitetura de rede para implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33684a6292d7e51c04f6bacc7c49ee5986dbec10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502407"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitetura de rede do SAP HANA (Instâncias Grandes)

A arquitetura dos serviços de rede do Azure é um componente essencial da implantação com êxito dos aplicativos do SAP HANA em Instâncias Grandes. Normalmente, as implantações do SAP HANA no Azure (Instâncias Grandes) têm uma estrutura SAP maior com diversas soluções SAP com vários tamanhos de bancos de dados, consumo de recursos de CPU e a utilização de memória. É provável que nem todos os sistemas de TI já estejam localizados no Azure. Seu cenário SAP é muitas vezes híbrido, bem como de um ponto de vista de aplicação DBMS e SAP usando uma mistura de NetWeaver, e S/4HANA e SAP HANA e outros DBMS. O Azure oferece diferentes serviços que permitem executar os diferentes sistemas DBMS, NetWeaver e S/4HANA no Azure. O Azure também oferece tecnologia de rede para fazer o Azure parecer um data center virtual para suas implantações de software no local

A menos que seus sistemas completos de TI estejam hospedados no Azure. A funcionalidade de rede do Azure é usada para conectar o mundo local com seus ativos do Azure para fazer o Azure parecer um data center virtual seu. A funcionalidade de rede do Azure utilizada é: 

- As redes virtuais do Azure estão conectadas ao circuito [ExpressRoute](https://azure.microsoft.com/services/expressroute/) que se conecta aos ativos da rede local.
- Um circuito ExpressRoute que conecta no local ao Azure deve ter uma largura de banda mínima de [1 Gbps ou mais](https://azure.microsoft.com/pricing/details/expressroute/). Essa largura de banda mínima permite largura de banda adequada para a transferência de dados entre sistemas locais e sistemas em execução nas VMs. Também permite largura de banda adequada para conexão a sistemas do Azure a partir de usuários locais.
- Todos os sistemas SAP no Azure são configurados em redes virtuais para se comunicarem entre si.
- O Active Directory e o DNS hospedados no local são estendidos para o Azure através do ExpressRoute a partir do local ou estão sendo executados completos no Azure.

Para o caso específico de integração hana grandes instâncias na malha de rede do data center do Azure, a tecnologia Azure ExpressRoute também é usada


> [!NOTE] 
> Apenas uma assinatura do Azure pode ser vinculada a apenas um inquilino em um selo HANA Large Instance em uma região específica do Azure. Por outro lado, um único inquilino de carimbo HANA Large Instance pode ser vinculado a apenas uma assinatura do Azure. Esse requisito é consistente com outros objetos cobráveis no Azure.

Se o SAP HANA no Azure (Instâncias Grandes) for implantado em várias regiões diferentes do Azure, um inquilino separado será implantado no selo HANA Large Instance. É possível executar ambos sob a mesma assinatura do Azure, desde que essas instâncias façam parte da mesma paisagem do SAP. 

> [!IMPORTANT] 
> Apenas o método de implantação do Azure Resource Manager é suportado com o SAP HANA no Azure (Instâncias Grandes).

 

## <a name="additional-virtual-network-information"></a>Informações adicionais sobre rede virtual

Para conectar uma rede virtual ao ExpressRoute, um gateway Azure ExpressRoute deve ser criado. Para obter mais informações, consulte [os gateways sobre o Expressroute para ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Um gateway Azure ExpressRoute é usado com o ExpressRoute para uma infra-estrutura fora do Azure ou para um selo Azure Large Instance. Você pode conectar o gateway Azure ExpressRoute a um máximo de quatro circuitos ExpressRoute diferentes, desde que essas conexões venham de diferentes roteadores de borda corporativa da Microsoft. Para obter mais informações, consulte [a infra-estrutura e a conectividade SAP HANA (Large Instances) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> O máximo de throughput que você pode obter com um gateway ExpressRoute é de 10 Gbps usando uma conexão ExpressRoute. A cópia de arquivos entre uma VM que reside em uma rede virtual e um sistema local (como um único fluxo de cópia) não alcança a taxa de transferência total as diferentes SKUs do gateway. Para aproveitar a largura de banda completa do gateway ExpressRoute, use vários fluxos. Ou, será necessário copiar arquivos diferentes em fluxos paralelos de um único arquivo.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de rede para SAP HANA em Instâncias Grandes
A arquitetura de rede para o SAP HANA em Instâncias Grandes pode ser separada em quatro partes diferentes:

- Rede local e conexão do ExpressRoute para o Azure. Essa parte é o domínio do cliente e é conectada ao Azure por meio do ExpressRoute. Este circuito Expressroute é totalmente pago por você como cliente. A largura de banda deve ser grande o suficiente para lidar com o tráfego de rede entre seus ativos no local e a região do Azure contra a sua conexão. Observe o canto inferior direito na figura a seguir.
- Os serviços de rede do Azure, como discutido anteriormente, com redes virtuais, que novamente precisam de gateways ExpressRoute adicionados. Essa parte é uma área na qual você precisa localizar os projetos apropriados para os requisitos do aplicativo, segurança e requisitos de conformidade. Se você usa o SAP HANA em Instâncias Grandes, é outro ponto a ser considerado em termos do número de redes virtuais e SKUs do gateway do Azure a escolher. Observe a parte superior direita na figura.
- Conectividade do SAP HANA em Instâncias Grandes através da tecnologia do ExpressRoute no Azure. Essa parte é implantada e feita pela Microsoft. Tudo o que você precisa fazer é fornecer alguns intervalos de endereço IP após a implantação dos ativos no SAP HANA em Instâncias Grandes conectar o circuito do ExpressRoute às redes virtuais. Para obter mais informações, consulte [a infra-estrutura e a conectividade SAP HANA (Large Instances) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Não há uma taxa adicional para você como cliente pela conectividade entre a malha de rede do data center Azure e as unidades HANA Large Instance.
- Rede dentro do selo HANA Large Instance, que é principalmente transparente para você.

![Rede virtual conectada ao SAP HANA do Azure (Instâncias Grandes) e no local](./media/hana-overview-architecture/image1-architecture.png)

O requisito de que os ativos locais devem conectar-se por meio do ExpressRoute para o Azure não altera porque você utiliza o SAP HANA em Instâncias Grandes. O requisito de ter uma ou várias redes virtuais executando as VMs, que hospedam a camada de aplicativo que conecta-se às instâncias do HANA hospedadas nas unidades do SAP HANA em Instâncias Grandes, também não é alterado. 

As diferenças para implantações do SAP no Azure são:

- As unidades do SAP HANA em Instâncias Grandes do locatário do cliente são conectadas por meio de outro circuito do ExpressRoute às redes virtuais. Para separar as condições de carga, os circuitos de rede virtual do Azure ExpressRoute e os circuitos entre redes virtuais Azure e HANA Large Instances não compartilham os mesmos roteadores.
- O perfil de carga de trabalho entre a camada de aplicativo do SAP e o SAP HANA em Instâncias Grandes é de natureza diferente, com muitas solicitações e intermitências pequenas, como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicativo.
- A arquitetura de aplicativos do SAP é mais sensível à latência de rede que os cenários típicos em que os dados são trocados entre o local e o Azure.
- O gateway Azure ExpressRoute tem pelo menos duas conexões ExpressRoute. Um circuito conectado a partir do local e outro conectado a partir de HANA Large Instances. Isso deixa apenas espaço para outros dois circuitos adicionais de MSEEs diferentes para se conectar no ExpressRoute Gateway. Esta restrição é independente do uso do Caminho Rápido ExpressRoute. Todos os circuitos conectados compartilham a largura de banda máxima para dados de entrada do gateway ExpressRoute.

Com a Revisão 3 dos selos HANA Large Instance, a latência de rede experimentada entre as unidades VMs e HANA Large Instance pode ser maior do que uma latência típica de ida e volta da rede VM-para-VM. Dependendo da região do Azure, os valores medidos podem exceder a latência de viagem de ida e volta de 0,7 ms, classificada como abaixo da média na [Nota SAP 1100926 – Perguntas frequentes: desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). Dependendo da ferramenta e da região do Azure para medir a latência da viagem de ida e volta da rede entre uma VM do Azure e a unidade de Instância Grande do HANA, a latência medida pode ser até cerca de 2 milissegundos. No entanto, os clientes implantam aplicativos do SAP de produção baseados no SAP HANA com êxito no SAP HANA em Instâncias Grandes. Certifique-se de testar os processos empresariais completamente no SAP HANA em Instâncias Grandes do Azure. Uma nova funcionalidade, chamada ExpressRoute Fast Path, é capaz de reduzir substancialmente a latência da rede entre hana grandes instâncias e VMs de camada de aplicativo no Azure (veja abaixo). 

Com a Revisão 4 dos selos HANA Large Instance, a latência de rede entre VMs Azure que são implantadas em proximidade com o selo HANA Large Instance, é experimentada para atender à classificação média ou melhor do que a média, conforme documentado no [SAP Note #1100926 - FAQ: Desempenho da rede](https://launchpad.support.sap.com/#/notes/1100926/E) se o Azure ExpressRoute Fast Path estiver configurado (veja abaixo). Para implantar VMs Azure nas proximidades das unidades HANA Large Instance da Revisão 4, você precisa aproveitar os Grupos de [Colocação de Proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). A forma como grupos de colocação de proximidade podem ser usados para localizar a camada de aplicativo SAP no mesmo datacenter do Azure como as unidades HANA Large Instance hospedadas da Revisão 4 é descrita em [Grupos de Colocação de Proximidade do Azure para obter latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).

Para fornecer latência de rede determinística entre VMs e HANA Large Instance, a escolha do Gateway ExpressRoute SKU é essencial. Diferentemente dos padrões de tráfego entre VMs locais e VMs, o padrão de tráfego entre as VMs e o SAP HANA em Instâncias Grandes pode desenvolver pequenas, mas elevadas intermitências de solicitações e volumes de dados a serem transmitidos. Para lidar bem com essas intermitências, é recomendável usar a SKU do gateway UltraPerformance. Para a classe Tipo II de Hana Large Instance SKUs, o uso do Gateway UltraPerformance SKU como gateway ExpressRoute é obrigatório.

> [!IMPORTANT] 
> Dado o tráfego geral entre o aplicativo do SAP e as camadas do banco de dados, somente as SKUs do gateway HighPerformance ou UltraPerformance para redes virtuais têm suporte para conexão com o SAP HANA do Azure (Instâncias Grandes). Para Hana Large Instance Type II SKUs, apenas o Gateway SKU ultraperformance é suportado como um gateway ExpressRoute. Exceções se aplicam ao usar o Caminho Rápido ExpressRoute (veja abaixo)

### <a name="expressroute-fast-path"></a>Caminho rápido expressroute
Para diminuir a latência, o ExpressRoute Fast Path foi introduzido e lançado em maio de 2019 para a conectividade específica de HANA Large Instances para redes virtuais Azure que hospedam as VMs do aplicativo SAP. A grande diferença para a solução implementada até agora, é que os fluxos de dados entre VMs e HANA Large Instances não são mais roteados através do gateway ExpressRoute. Em vez disso, as VMs atribuídas nas sub-redes da rede virtual Do Zure estão se comunicando diretamente com o roteador de borda corporativa dedicado. 

> [!IMPORTANT] 
> A funcionalidade ExpressRoute Fast Path requer que as sub-redes que executam as VMs do aplicativo SAP estejam na mesma rede virtual do Azure que foi conectada às Grandes Instâncias do HANA. As VMs localizadas em redes virtuais Do Zure que são acompanhadas pela rede virtual Azure conectada diretamente às unidades HANA Large Instance não estão se beneficiando do Caminho Rápido ExpressRoute. Como resultado, os projetos típicos de hub e rede virtual, onde os circuitos ExpressRoute estão se conectando contra uma rede virtual hub e redes virtuais contendo a camada de aplicativos SAP (raios) estão sendo pesquisados, a otimização pelo ExpressRoute Fast O caminho não vai funcionar. Em adição, o ExpressRoute Fast Path não suporta regras de roteamento definidas pelo usuário (UDR) hoje. Para obter mais informações, consulte [o gateway de rede virtual ExpressRoute e o FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Para obter mais detalhes sobre como configurar o Caminho Rápido expressroute, leia o documento [Conecte uma rede virtual a instâncias hana grandes](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Um gateway UltraPerformance ExpressRoute é necessário para ter o ExpressRoute Fast Path funcionando


## <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura local mostrada anteriormente é conectada por meio do ExpressRoute no Azure. O circuito ExpressRoute se conecta a um roteador de borda corporativa da Microsoft (MSEE). Para obter mais informações, consulte [Visão geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Depois que a rota é estabelecida, ela se conecta à espinha dorsal do Azure.

> [!NOTE] 
> Para executar paisagens do SAP no Azure, conecte o roteador de borda corporativa mais próximo da região do Azure na paisagem do SAP. Os selos HANA Large Instance são conectados por meio de dispositivos de roteador de borda corporativa dedicados para minimizar a latência da rede entre VMs nos selos Azure IaaS e HANA Large Instance.

O gateway ExpressRoute para as VMs que hospedam instâncias de aplicativos SAP está conectado a um circuito ExpressRoute que se conecta ao local. A mesma rede virtual é conectada a um roteador de borda corporativa separado dedicado para conectar os carimbos da Instância Grande. Usando o ExpressRoute Fast Path, o fluxo de dados de HANA Large Instances para as VMs de camada de aplicativo SAP não são mais roteados através do gateway ExpressRoute e com isso reduzem a latência de viagem de ida e volta da rede.

Esse sistema é um exemplo direto de um único sistema SAP. A camada de aplicativo do SAP é hospedada no Azure. O banco de dados do SAP HANA é executado no SAP HANA do Azure (Instâncias Grandes). A suposição é que a largura de banda do gateway ExpressRoute de 2 Gbps ou 10 Gbps de throughput não representa um gargalo.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou grandes sistemas SAP

Se vários sistemas SAP ou grandes sistemas SAP forem implantados para se conectar ao SAP HANA no Azure (Instâncias Grandes), o throughput do gateway ExpressRoute pode se tornar um gargalo. Ou você quer isolar sistemas de produção e não-produção em diferentes redes virtuais do Azure. Nesse caso, divida as camadas do aplicativo em várias redes virtuais. Também é possível criar uma rede virtual especial que conecta-se ao SAP HANA em Instâncias Grandes para casos como:

- Execução de backups diretamente das instâncias do HANA na Instância Grande do HANA para uma VM no Azure que hospeda compartilhamentos NFS.
- Cópia de backups grandes ou de outros arquivos de unidades de Instância Grande do HANA com o espaço em disco gerenciado no Azure.

Use uma rede virtual separada para hospedar VMs que gerenciam o armazenamento para transferência em massa de dados entre HANA Large Instances e Azure. Esse arranjo evita os efeitos da transferência de arquivos ou dados grandes do HANA Large Instance para o Azure no gateway ExpressRoute que atende às VMs que executam a camada de aplicativos SAP. 

Uma arquitetura de rede mais escalonável:

- Aproveite várias redes virtuais para uma única camada de aplicativo do SAP maior.
- Implante uma rede virtual separada para cada sistema SAP implantado, em comparação à combinação desses sistemas SAP em sub-redes separadas sob a mesma rede virtual.

  Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (Instâncias Grandes):

![Implantar camada de aplicativo do SAP em várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependendo das regras e restrições, você deseja aplicar entre as diferentes redes virtuais que hospedam VMs de diferentes sistemas SAP, você deve observar essas redes virtuais. Para obter mais informações sobre o emparelhamento de rede virtual, consulte [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Roteamento no Azure

Por implantação padrão, três considerações de roteamento de rede são importantes para o SAP HANA no Azure (Instâncias grandes):

* O SAP HANA no Azure (Instâncias Grandes) só pode ser acessado através de VMs do Azure e da conexão dedicada ExpressRoute, não diretamente do local. O acesso direto do local para as unidades do SAP HANA em Instâncias Grandes, conforme fornecido pela Microsoft a você, não é possível imediatamente. As restrições de roteamento transitivas devem-se à arquitetura de rede atual do Azure usada para o SAP HANA em Instâncias Grandes. Alguns clientes de administração e quaisquer aplicativos que precisam de acesso direto, como o SAP Solution Manager em execução no local, não podem conectar ao banco de dados do SAP HANA. Para exceções, verifique a seção 'Roteamento direto para instâncias grandes do HANA'.

* Se você tiver unidades HANA Large Instance implantadas em duas regiões diferentes do Azure para recuperação de desastres, as mesmas restrições transitórias de roteamento aplicadas no passado. Em outras palavras, os endereços IP de uma unidade HANA Large Instance em uma região (por exemplo, US West) não foram encaminhados para uma unidade HANA Large Instance implantada em outra região (por exemplo, US East). Essa restrição foi independente do uso da rede Azure espiando entre regiões ou conectando os circuitos ExpressRoute que conectam unidades hana large instance a redes virtuais. Para uma representação gráfica, consulte a figura na seção "Usar unidades do SAP HANA em Instâncias Grandes em várias regiões". Essa restrição, que veio com a arquitetura implantada, proibiu o uso imediato da replicação do sistema HANA como funcionalidade de recuperação de desastres. Para alterações recentes, consulte a seção 'Use unidades HANA Large Instance em várias regiões'. 

* As unidades SAP HANA on Azure (Instâncias Grandes) têm um endereço IP atribuído a partir da faixa de endereço do pool de IP do servidor que você enviou ao solicitar a implantação hana grande instância. Para obter mais informações, consulte [a infra-estrutura e a conectividade SAP HANA (Large Instances) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este endereço IP é acessível através das assinaturas e circuito do Azure que conecta redes virtuais do Azure a instâncias hana grandes. O endereço IP atribuído fora desse intervalo de endereços do pool de IP do servidor é atribuído diretamente à unidade de hardware. Ele*não* é atribuído pelo NAT, como foi o caso nas primeiras implantações dessa solução. 

### <a name="direct-routing-to-hana-large-instances"></a>Roteamento direto para hana instâncias grandes

Por padrão, o roteamento transitivo não funciona nesses cenários:

* Entre as unidades HANA Large Instance e uma implantação no local.

* Entre hana grande instância roteamento que são implantados em duas regiões diferentes.

Existem três maneiras de permitir o roteamento transitivo nesses cenários:

- Um proxy reverso para rotear dados, de e para. Por exemplo, F5 BIG-IP, NGINX com Gerenciador de Tráfego implantado na rede virtual Azure que se conecta a INSTÂNCIAs Grandes hana e a locais como uma solução de roteamento de firewall/tráfego virtual.
- Usar [regras IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) em uma VM Linux para habilitar o roteamento entre localidades locais e as unidades do SAP HANA em Instâncias Grandes ou entre unidades do SAP HANA em Instâncias Grandes em regiões diferentes. O VM executando IPTables precisa ser implantado na rede virtual Azure que se conecta a HANA Large Instances e a on-premises. A VM precisa ser dimensionada nesse concomção, de modo que o throughput de rede da VM seja suficiente para o tráfego de rede esperado. Para obter detalhes sobre a largura de banda da rede VM, verifique o artigo [Tamanhos de máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [O Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) seria outra solução para permitir tráfego direto entre unidades de ocorrência on-premises e HANA Large. 

Todo o tráfego dessas soluções seria roteado através de uma rede virtual Do Zure e, como tal, o tráfego poderia ser adicionalmente restrito pelos aparelhos macios usados ou pelos Grupos de Segurança da Rede Azure, de modo que certos endereços IP ou endereço IP variam de no local, os locais podem ser bloqueados ou explicitamente permitidos acessando instâncias grandes do HANA. 

> [!NOTE]  
> Esteja ciente de que a implementação e o suporte para soluções personalizadas envolvendo IPTables ou dispositivos de rede de terceiros não são fornecidos pela Microsoft. O suporte deve ser fornecido pelo fornecedor do componente usado ou pelo integrador. 

#### <a name="express-route-global-reach"></a>Alcance global da rota expressa
A Microsoft introduziu uma nova funcionalidade chamada [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). O Global Reach pode ser usado para HANA Large Instances em dois cenários:

- Habilite o acesso direto do local às unidades HANA Large Instance implantadas em diferentes regiões
- Habilite a comunicação direta entre suas unidades HANA Large Instance implantadas em diferentes regiões


##### <a name="direct-access-from-on-premises"></a>Acesso Direto a partir do local
Nas regiões do Azure onde o Global Reach é oferecido, você pode solicitar a ativação da funcionalidade Global Reach para o seu circuito ExpressRoute que conecta sua rede local à rede virtual Azure que se conecta às suas unidades HANA Large Instance também. Existem algumas implicações de custo para o lado local do seu circuito ExpressRoute. Para preços, verifique os preços do [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Não há custos adicionais para você relacionados ao circuito que conecta as unidades HANA Large Instance ao Azure. 

> [!IMPORTANT]  
> No caso de usar o Global Reach para permitir o acesso direto entre suas unidades HANA Large Instance e ativos locais, o fluxo de dados e controle da rede não é **roteado através de redes virtuais Azure,** mas diretamente entre os roteadores de câmbio corporativo da Microsoft. Como resultado, quaisquer regras de NSG ou ASG, ou qualquer tipo de firewall, NVA ou proxy que você implantou em uma rede virtual Do Zure, não estão sendo tocados. **Se você usar o ExpressRoute Global Reach para habilitar o acesso direto do local ao HANA, as restrições e permissões de unidades de grande instância para acessar unidades hana grandes instâncias precisam ser definidas em firewalls no lado local** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Conectando HANA Grandes Instâncias em diferentes regiões do Azure
Da mesma forma, como o ExpressRoute Global Reach pode ser usado para conectar no local a unidades hana large instance, ele pode ser usado para conectar dois inquilinos HANA Large Instance que são implantados para você em duas regiões diferentes. O isolamento são os circuitos ExpressRoute que seus inquilinos hana grande instância estão usando para se conectar ao Azure em ambas as regiões. Não há taxas adicionais para conectar dois inquilinos hana grande instância que são implantados em duas regiões diferentes. 

> [!IMPORTANT]  
> O fluxo de dados e o fluxo de controle do tráfego de rede entre os diferentes inquilinos de instância hana não serão roteados através de redes azure. Como resultado, você não pode usar a funcionalidade do Azure ou NVAs para impor restrições de comunicação entre seus dois inquilinos hana grandes instâncias. 

Para obter mais detalhes sobre como habilitar o ExpressRoute Global Reach, leia o documento [Conecte uma rede virtual a instâncias hana grandes](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade com a Internet do SAP HANA em Instâncias Grandes
O SAP HANA em Instâncias Grandes *não* tem conectividade direta com a Internet. Por exemplo, essa limitação pode restringir a capacidade de registrar a imagem do SO diretamente com o fornecedor do SO. Talvez seja necessário trabalhar com o servidor da Ferramenta de Gerenciamento de Assinaturas do SUSE Linux Enterprise Server ou com o Gerenciador de Assinaturas do Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Criptografia de dados entre VMs e o SAP HANA em Instâncias Grandes
Os dados transferidos entre o SAP HANA em Instâncias Grandes e as VMs não são criptografados. No entanto, apenas para a troca entre o lado do DBMS do HANA e aplicativos baseados em JDBC/ODBC, é possível habilitar criptografia do tráfego. Para obter mais informações, consulta [esta documentação pelo SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usar unidades do SAP HANA em Instâncias Grandes em várias regiões

Para realizar configurações de recuperação de desastres, você precisa ter unidades SHANA Large Instance em várias regiões do Azure. Mesmo usando o Azure [Global Vnet Peering], o roteamento transitivo por padrão não está funcionando entre inquilinos hana grande instância em duas regiões diferentes. No entanto, a Global Reach abre o caminho de comunicação entre as unidades HANA Large Instance que você provisionou em duas regiões diferentes. Este cenário de uso do ExpressRoute Global Reach permite:

 - Replicação do sistema HANA sem proxies ou firewalls adicionais
 - Copiando backups entre unidades HANA Large Instance em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema


![Rede virtual conectada a carimbos da Instância Grande do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura mostra como as diferentes redes virtuais em ambas as regiões estão conectadas a dois circuitos ExpressRoute diferentes que são usados para se conectar ao SAP HANA no Azure (Instâncias Grandes) em ambas as regiões do Azure (linhas cinzas). A razão para estas duas conexões cruzadas é proteger-se de uma paralisação dos MSEEs em ambos os lados. O fluxo de comunicação entre as duas redes virtuais nas duas regiões do Azure deve ser tratado sobre o [peering global](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) das duas redes virtuais nas duas regiões diferentes (linha pontilhada azul). A linha vermelha grossa descreve a conexão ExpressRoute Global Reach, que permite que as unidades HANA Large Instance de seus inquilinos em duas regiões diferentes se comuniquem entre si. 

> [!IMPORTANT] 
> Se foram utilizados vários circuitos do ExpressRoute, as configurações de prefixação do AS Path e de BGP de Preferência Local deverão ser usadas para garantir o roteamento apropriado do tráfego.

**Próximas etapas**
- Confira [Arquitetura de armazenamento do SAP HANA (Instâncias Grandes)](hana-storage-architecture.md)
