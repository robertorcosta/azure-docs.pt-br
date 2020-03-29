---
title: Ajuste de desempenho TCP/IP para VMs Azure | Microsoft Docs
description: Aprenda várias técnicas comuns de ajuste de desempenho TCP/IP e sua relação com as VMs do Azure.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297780"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Ajuste de desempenho de TCP/IP em VMs do Azure

Este artigo discute técnicas comuns de ajuste de desempenho TCP/IP e algumas coisas a considerar quando você as usa para máquinas virtuais em execução no Azure. Ele fornecerá uma visão geral básica das técnicas e explorará como elas podem ser sintonizadas.

## <a name="common-tcpip-tuning-techniques"></a>Técnicas comuns de ajuste TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentação e grande descarregamento de envio

#### <a name="mtu"></a>MTU

A unidade de transmissão máxima (MTU) é o quadro de maior tamanho (pacote), especificado em bytes, que pode ser enviado através de uma interface de rede. O MTU é uma configuração configurável. O MTU padrão usado em VMs do Azure e a configuração padrão na maioria dos dispositivos de rede globalmente, é de 1.500 bytes.

#### <a name="fragmentation"></a>Fragmentação

A fragmentação ocorre quando um pacote é enviado que excede o MTU de uma interface de rede. A pilha TCP/IP quebrará o pacote em pedaços menores (fragmentos) que estejam em conformidade com o MTU da interface. A fragmentação ocorre na camada IP e é independente do protocolo subjacente (como tcp). Quando um pacote de 2.000 bytes é enviado por uma interface de rede com um MTU de 1.500, o pacote será dividido em um pacote de 1.500 bytes e um pacote de 500 bytes.

Os dispositivos de rede no caminho entre uma fonte e destino podem soltar pacotes que excedam o MTU ou fragmentar o pacote em pedaços menores.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>O bit Não Fragmentar em um pacote IP

O bit Don't Fragment (DF) é um sinalizador no cabeçalho do protocolo IP. O bit DF indica que os dispositivos de rede no caminho entre o remetente e o receptor não devem fragmentar o pacote. Esta parte pode ser definida por muitas razões. (Veja a seção "Path MTU Discovery" deste artigo, por exemplo.) Quando um dispositivo de rede recebe um pacote com o conjunto de bits Não Fragmentar, e esse pacote excede a interface do dispositivo MTU, o comportamento padrão é que o dispositivo solte o pacote. O dispositivo envia uma mensagem ICMP Fragmentation Needed de volta à fonte original do pacote.

#### <a name="performance-implications-of-fragmentation"></a>Implicações de desempenho da fragmentação

A fragmentação pode ter implicações negativas de desempenho. Uma das principais razões para o efeito sobre o desempenho é o impacto da CPU/memória da fragmentação e remontagem de pacotes. Quando um dispositivo de rede precisa fragmentar um pacote, ele terá que alocar recursos de CPU/memória para executar a fragmentação.

A mesma coisa acontece quando o pacote é remontado. O dispositivo de rede tem que armazenar todos os fragmentos até que sejam recebidos para que possa remontá-los no pacote original. Esse processo de fragmentação e remontagem também pode causar latência.

A outra possível implicação negativa de desempenho da fragmentação é que pacotes fragmentados podem chegar fora de ordem. Quando os pacotes são recebidos fora de ordem, alguns tipos de dispositivos de rede podem solhá-los. Quando isso acontece, todo o pacote tem que ser retransmitido.

Os fragmentos são normalmente descartados por dispositivos de segurança, como firewalls de rede ou quando os buffers de recebimento de um dispositivo de rede estão esgotados. Quando os buffers de recebimento de um dispositivo de rede são esgotados, um dispositivo de rede está tentando remontar um pacote fragmentado, mas não tem os recursos para armazenar e reassumir o pacote.

A fragmentação pode ser vista como uma operação negativa, mas o suporte à fragmentação é necessário quando você está conectando diversas redes pela internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Benefícios e consequências da modificação do MTU

De um modo geral, você pode criar uma rede mais eficiente aumentando o MTU. Cada pacote transmitido tem informações de cabeçalho adicionadas ao pacote original. Quando a fragmentação cria mais pacotes, há mais sobrecarga de cabeçalho, e isso torna a rede menos eficiente.

Veja um exemplo. O tamanho do cabeçalho Ethernet é de 14 bytes mais uma seqüência de verificação de quadro de 4 bytes para garantir a consistência do quadro. Se um pacote de 2.000 bytes for enviado, 18 bytes de sobrecarga Ethernet são adicionados na rede. Se o pacote for fragmentado em um pacote de 1.500 bytes e um pacote de 500 bytes, cada pacote terá 18 bytes de cabeçalho Ethernet, um total de 36 bytes.

Tenha em mente que o aumento do MTU não necessariamente criará uma rede mais eficiente. Se um aplicativo enviar apenas pacotes de 500 bytes, a mesma sobrecarga de cabeçalho existirá se o MTU for de 1.500 bytes ou 9.000 bytes. A rede só se tornará mais eficiente se usar tamanhos de pacotemaiores que são afetados pelo MTU.

#### <a name="azure-and-vm-mtu"></a>Azure e VM MTU

O MTU padrão para VMs Azure é de 1.500 bytes. A pilha de rede virtual do Azure tentará fragmentar um pacote a 1.400 bytes.

Observe que a pilha de rede virtual não é inerentemente ineficiente porque fragmenta pacotes a 1.400 bytes, embora as VMs tenham um MTU de 1.500. Uma grande porcentagem de pacotes de rede são muito menores do que 1.400 ou 1.500 bytes.

#### <a name="azure-and-fragmentation"></a>Azul e fragmentação

A pilha de rede virtual é configurada para soltar "fragmentos fora de ordem", ou seja, pacotes fragmentados que não chegam em sua ordem fragmentada original. Esses pacotes são descartados principalmente por causa de uma vulnerabilidade de segurança de rede anunciada em novembro de 2018 chamada FragmentSmack.

FragmentSmack é um defeito na forma como o kernel Linux lidou com a remontagem de pacotes Fragmentados IPv4 e IPv6. Um invasor remoto poderia usar essa falha para desencadear operações caras de remontagem de fragmentos, o que poderia levar a um aumento da CPU e uma negação de serviço no sistema de destino.

#### <a name="tune-the-mtu"></a>Sintonize o MTU

Você pode configurar um Azure VM MTU, como você pode em qualquer outro sistema operacional. Mas você deve considerar a fragmentação que ocorre no Azure, descrita acima, quando você está configurando um MTU.

Não encorajamos os clientes a aumentar as MTUs VM. Esta discussão visa explicar os detalhes de como o Azure implementa o MTU e realiza a fragmentação.

> [!IMPORTANT]
>O aumento do MTU não é conhecido por melhorar o desempenho e pode ter um efeito negativo no desempenho do aplicativo.
>
>

#### <a name="large-send-offload"></a>Grande descarregamento de envio

Uma grande descarregamento de envio (LSO) pode melhorar o desempenho da rede descarregando a segmentação de pacotes para o adaptador Ethernet. Quando o LSO está ativado, a pilha TCP/IP cria um grande pacote TCP e envia-o para o adaptador Ethernet para segmentação antes de encaminhá-lo. O benefício do LSO é que ele pode liberar a CPU de segmentar pacotes em tamanhos que estejam de acordo com o MTU e descarregar esse processamento para a interface Ethernet onde é realizado em hardware. Para saber mais sobre os benefícios do LSO, consulte [Suporte de grande descarregamento](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)de envio .

Quando o LSO estiver ativado, os clientes do Azure podem ver grandes tamanhos de quadros quando realizam capturas de pacotes. Esses grandes tamanhos de quadros podem levar alguns clientes a pensar que a fragmentação está ocorrendo ou que um Grande MTU está sendo usado quando não está. Com o LSO, o adaptador Ethernet pode anunciar um tamanho máximo maior do segmento (MSS) para a pilha TCP/IP para criar um pacote TCP maior. Todo esse quadro não segmentado é então encaminhado para o adaptador Ethernet e seria visível em uma captura de pacote realizada na VM. Mas o pacote será dividido em muitos quadros menores pelo adaptador Ethernet, de acordo com o MTU do adaptador Ethernet.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Dimensionamento da janela TCP MSS e PMTUD

#### <a name="tcp-maximum-segment-size"></a>Tamanho máximo do segmento TCP

O tamanho máximo do segmento TCP (MSS) é uma configuração que limita o tamanho dos segmentos TCP, o que evita a fragmentação de pacotes TCP. Os sistemas operacionais normalmente usarão esta fórmula para definir o MSS:

`MSS = MTU - (IP header size + TCP header size)`

O cabeçalho IP e o cabeçalho TCP são 20 bytes cada, ou 40 bytes no total. Assim, uma interface com um MTU de 1.500 terá um MSS de 1.460. Mas o MSS é configurável.

Esta configuração é acordada no aperto de mão de três vias tcp quando uma sessão TCP é configurada entre uma fonte e um destino. Ambos os lados enviam um valor MSS, e o inferior dos dois é usado para a conexão TCP.

Tenha em mente que os MTUs da origem e do destino não são os únicos fatores que determinam o valor do MSS. Dispositivos de rede intermediários, como gateways VPN, incluindo o Azure VPN Gateway, podem ajustar o MTU independentemente da origem e do destino para garantir o melhor desempenho da rede.

#### <a name="path-mtu-discovery"></a>Path MTU Discovery

O MSS é negociado, mas pode não indicar o MSS real que pode ser usado. Isso porque outros dispositivos de rede no caminho entre a origem e o destino podem ter um valor MTU menor do que a origem e o destino. Neste caso, o dispositivo cujo MTU é menor que o pacote soltará o pacote. O dispositivo enviará de volta uma mensagem ICMP Fragmentation Needed (Tipo 3, Código 4) que contém seu MTU. Esta mensagem ICMP permite que o host de origem reduza seu Path MTU adequadamente. O processo é chamado path MTU Discovery (PMTUD).

O processo PMTUD é ineficiente e afeta o desempenho da rede. Quando são enviados pacotes que excedam o MTU de um caminho de rede, os pacotes precisam ser retransmitidos com um MSS mais baixo. Se o remetente não receber a mensagem ICMP Fragmentation Needed, talvez por causa de um firewall de rede no caminho (comumente referido como um *buraco negro PMTUD),* o remetente não sabe que precisa baixar o MSS e retransmitirá continuamente o pacote. É por isso que não recomendamos o aumento do Azure VM MTU.

#### <a name="vpn-and-mtu"></a>VPN e MTU

Se você usar VMs que executam encapsulamento (como VPNs IPsec), existem algumas considerações adicionais sobre o tamanho do pacote e MTU. As VPNs adicionam mais cabeçalhos aos pacotes, o que aumenta o tamanho do pacote e requer um MSS menor.

Para o Azure, recomendamos que você ajuste a fixação TCP MSS para 1.350 bytes e interface de túnel MTU para 1.400. Para obter mais informações, consulte os dispositivos VPN e a [página de parâmetros IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latência, tempo de ida e volta e escala de janelas TCP

#### <a name="latency-and-round-trip-time"></a>Latência e ida e volta

A latência da rede é regida pela velocidade da luz sobre uma rede de fibra óptica. O throughput de rede do TCP também é efetivamente regido pelo tempo de ida e volta (RTT) entre dois dispositivos de rede.

| | | | |
|-|-|-|-|
|**Rota**|**Distância**|**Tempo de ida**|**RTT**|
|Nova Iorque para São Francisco|4.148 km|21 ms|42 ms|
|Nova Iorque para Londres|5.585 km|28 ms|56 ms|
|Nova Iorque para Sydney|15.993 km|80 ms|160 ms|

Esta tabela mostra a distância em linha reta entre dois locais. Nas redes, a distância é tipicamente maior do que a distância em linha reta. Aqui está uma fórmula simples para calcular rtt mínimo como regido pela velocidade da luz:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Você pode usar 200 para a velocidade de propagação. Esta é a distância, em metros, que a luz viaja em 1 milissegundo.

Vamos levar Nova York para São Francisco como exemplo. A distância em linha reta é de 4.148 km. Conectando esse valor à equação, oprestamos o seguinte:

`Minimum RTT = 2 * (4,148 / 200)`

A saída da equação é em milissegundos.

Se você quiser obter o melhor desempenho de rede, a opção lógica é selecionar destinos com a menor distância entre eles. Você também deve projetar sua rede virtual para otimizar o caminho do tráfego e reduzir a latência. Para obter mais informações, consulte a seção "Considerações de design de rede" deste artigo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Latência e efeitos de ida e volta no TCP

O tempo de ida e volta tem um efeito direto na entrada máxima do TCP. No protocolo TCP, o tamanho da *janela* é a quantidade máxima de tráfego que pode ser enviada por uma conexão TCP antes que o remetente precise receber o reconhecimento do receptor. Se o TCP MSS estiver definido como 1.460 e o tamanho da janela TCP estiver definido como 65.535, o remetente poderá enviar 45 pacotes antes de receber o reconhecimento do receptor. Se o remetente não receber reconhecimento, ele retransmitirá os dados. Aqui está a fórmula:

`TCP window size / TCP MSS = packets sent`

Neste exemplo, 65.535 / 1.460 é arredondado até 45.

Esse estado de "espera por reconhecimento", um mecanismo para garantir a entrega confiável de dados, é o que faz com que o RTT afete o throughput do TCP. Quanto mais tempo o remetente espera por reconhecimento, mais tempo ele precisa esperar antes de enviar mais dados.

Aqui está a fórmula para calcular o throughput máximo de uma única conexão TCP:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Esta tabela mostra o throughput máximo de megabytes/por segundo de uma única conexão TCP. (Para legibilidade, megabytes são usados para a unidade de medida.)

| | | | |
|-|-|-|-|
|**Tamanho da janela TCP (bytes)**|**Latência RTT (ms)**|**Máximo de megabyte/segundo de throughput**|**Throughput máximo de megabit/segundo**|
|65.535|1|65.54|524.29|
|65.535|30|2.18|17.48|
|65.535|60|1.09|8.74|
|65.535|90|.73|5.83|
|65.535|120|.55|4.37|

Se os pacotes forem perdidos, o throughput máximo de uma conexão TCP será reduzido enquanto o remetente retransmite os dados que já enviou.

#### <a name="tcp-window-scaling"></a>Dimensionamento da janela TCP

O dimensionamento da janela TCP é uma técnica que aumenta dinamicamente o tamanho da janela TCP para permitir que mais dados sejam enviados antes que um reconhecimento seja necessário. No exemplo anterior, 45 pacotes seriam enviados antes da necessidade de um reconhecimento. Se você aumentar o número de pacotes que podem ser enviados antes que um reconhecimento seja necessário, você estará reduzindo o número de vezes que um remetente está esperando por reconhecimento, o que aumenta o throughput máximo do TCP.

Esta tabela ilustra essas relações:

| | | | |
|-|-|-|-|
|**Tamanho da janela TCP (bytes)**|**Latência RTT (ms)**|**Máximo de megabyte/segundo de throughput**|**Throughput máximo de megabit/segundo**|
|65.535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Mas o valor do cabeçalho TCP para o tamanho da janela TCP tem apenas 2 bytes de comprimento, o que significa que o valor máximo para uma janela de recebimento é de 65.535. Para aumentar o tamanho máximo da janela, foi introduzido um fator de escala de janela TCP.

O fator de escala também é uma configuração que você pode configurar em um sistema operacional. Aqui está a fórmula para calcular o tamanho da janela TCP usando fatores de escala:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Aqui está o cálculo para um fator de escala de janela de 3 e um tamanho de janela de 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Um fator de escala de 14 resulta em um tamanho de janela TCP de 14 (o deslocamento máximo permitido). O tamanho da janela TCP será de 1.073.725.440 bytes (8,5 gigabits).

#### <a name="support-for-tcp-window-scaling"></a>Suporte para dimensionamento de janelas TCP

O Windows pode definir diferentes fatores de dimensionamento para diferentes tipos de conexão. (As classes de conexões incluem datacenter, internet e assim por diante.) Você usa `Get-NetTCPConnection` o comando PowerShell para visualizar o tipo de conexão de dimensionamento de janelas:

```powershell
Get-NetTCPConnection
```

Você pode `Get-NetTCPSetting` usar o comando PowerShell para visualizar os valores de cada classe:

```powershell
Get-NetTCPSetting
```

Você pode definir o tamanho inicial da janela TCP `Set-NetTCPSetting` e o fator de dimensionamento TCP no Windows usando o comando PowerShell. Para obter mais informações, consulte [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Estas são as configurações `AutoTuningLevel`eficazes do TCP para:

| | | | |
|-|-|-|-|
|**Autotuninglevel**|**Fator de escala**|**Multiplicador de dimensionamento**|**Fórmula<br/>para calcular o tamanho máximo da janela**|
|Desabilitado|Nenhum|Nenhum|Tamanho da janela|
|Restrito|4|2^4|Tamanho da janela * (2^4)|
|Altamente restrito|2|2^2|Tamanho da janela * (2^2)|
|Normal|8|2^8|Tamanho da janela * (2^8)|
|Experimental|14|2^14|Tamanho da janela * (2^14)|

Essas configurações são as mais propensas a afetar o desempenho do TCP, mas tenha em mente que muitos outros fatores na internet, fora do controle do Azure, também podem afetar o desempenho do TCP.

#### <a name="increase-mtu-size"></a>Aumentar o tamanho do MTU

Como um MTU maior significa um MSS maior, você pode se perguntar se o aumento do MTU pode aumentar o desempenho do TCP. Provavelmente, não. Existem prós e contras para o tamanho do pacote além de apenas tráfego TCP. Como discutido anteriormente, os fatores mais importantes que afetam o desempenho de throughput do TCP são o tamanho da janela TCP, a perda de pacotes e o RTT.

> [!IMPORTANT]
> Não recomendamos que os clientes do Azure alterem o valor padrão de MTU em máquinas virtuais.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Rede acelerada e receber dimensionamento lateral

#### <a name="accelerated-networking"></a>Redes aceleradas

As funções de rede de máquinas virtuais têm sido historicamente intensivas em CPU tanto na VM convidada quanto no hipervisor/host. Cada pacote que transita pelo host é processado em software pela CPU do host, incluindo todo o encapsulamento e descapsolação de rede virtual. Assim, quanto mais tráfego passar pelo host, maior a carga da CPU. E se a CPU do host estiver ocupada com outras operações, isso também afetará o throughput e a latência da rede. O Azure aborda esse problema com rede acelerada.

A rede acelerada fornece latência de rede ultrabaixa consistente através do hardware programável interno do Azure e tecnologias como o SR-IOV. A rede acelerada move grande parte da pilha de rede definida pelo software Azure fora das CPUs e em SmartNICs baseados em FPGA. Essa alteração permite que os aplicativos de usuário final recuperem ciclos de computação, o que coloca menos carga na VM, diminuindo o nervosismo e a inconsistência na latência. Em outras palavras, o desempenho pode ser mais determinista.

A rede acelerada melhora o desempenho, permitindo que a VM convidada contorne o host e estabeleça um datapath diretamente com o SmartNIC de um host. Aqui estão alguns benefícios do networking acelerado:

- **Menor latência / pacotes mais altos por segundo (pps)**: A remoção do switch virtual do datapath elimina o tempo que os pacotes gastam no host para processamento de políticas e aumenta o número de pacotes que podem ser processados na VM.

- **Jitter reduzido**: O processamento de switch virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. A eliminação da aplicação da diretiva no hardware remove essa variabilidade entregando pacotes diretamente para a VM, eliminando a comunicação host-to-VM e todas as interrupções de software e switches de contexto.

- **Diminuição da utilização da CPU**: Ignorar o switch virtual no host leva a menos utilização da CPU para processamento de tráfego de rede.

Para usar rede acelerada, você precisa habilitá-la explicitamente em cada VM aplicável. Consulte [Criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter instruções.

#### <a name="receive-side-scaling"></a>Receba dimensionamento lateral

O RSS (Receive Side Scaling, sistema de driver de rede) é uma tecnologia de driver de rede que distribui o recebimento do tráfego de rede de forma mais eficiente, distribuindo o processamento de recebimento em várias CPUs em um sistema multiprocessador. Em termos simples, o RSS permite que um sistema processe tráfego mais recebido porque usa todas as CPUs disponíveis em vez de apenas uma. Para uma discussão mais técnica do RSS, consulte [Introdução para receber dimensionamento lateral](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Para obter o melhor desempenho quando a rede acelerada estiver ativada em uma VM, você precisa ativar o RSS. O RSS também pode fornecer benefícios em VMs que não usam rede acelerada. Para obter uma visão geral de como determinar se o RSS está habilitado e como habilitá-lo, consulte [Otimizar o throughput de rede para máquinas virtuais Do Zure](https://aka.ms/FastVM).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT e assassinato TIME_WAIT

TCP TIME_WAIT é outra configuração comum que afeta o desempenho da rede e do aplicativo. Em VMs ocupados que estão abrindo e fechando muitas tomadas, seja como clientes ou como servidores (Fonte IP:Source Port + Destination IP:Destination Port), durante a operação normal do TCP, um determinado soquete pode acabar em um estado TIME_WAIT por um longo tempo. O estado TIME_WAIT é destinado a permitir que quaisquer dados adicionais sejam entregues em uma tomada antes de fechá-los. Assim, as pilhas TCP/IP geralmente impedem a reutilização de um soquete, soltando silenciosamente o pacote SYN TCP do cliente.

A quantidade de tempo que um soquete está em TIME_WAIT é configurável. Pode variar de 30 segundos a 240 segundos. Os soquetes são um recurso finito, e o número de soquetes que podem ser usados a qualquer momento é configurável. (O número de soquetes disponíveis é tipicamente de cerca de 30.000.) Se os soquetes disponíveis forem consumidos ou se os clientes e servidores tiverem configurações TIME_WAIT incompatíveis e uma VM tentar reutilizar um soquete em um estado TIME_WAIT, novas conexões falharão à medida que os pacotes TCP SYN forem silenciosamente descartados.

O valor para o intervalo de portas para tomadas de saída geralmente é configurável dentro da pilha TCP/IP de um sistema operacional. A mesma coisa vale para as configurações de TIME_WAIT TCP e reutilização do soquete. Mudar esses números pode potencialmente melhorar a escalabilidade. Mas, dependendo da situação, essas mudanças podem causar problemas de interoperabilidade. Você deve ter cuidado se você mudar esses valores.

Você pode usar TIME_WAIT assassinato para resolver essa limitação de escala. TIME_WAIT assassinato permite que um soquete seja reutilizado em certas situações, como quando o número de seqüência no pacote IP da nova conexão excede o número de seqüência do último pacote da conexão anterior. Neste caso, o sistema operacional permitirá que a nova conexão seja estabelecida (aceitará o novo SYN/ACK) e forçará o fechamento da conexão anterior que estava em estado TIME_WAIT. Esse recurso é suportado em VMs do Windows no Azure. Para saber mais sobre o suporte em outras VMs, consulte o fornecedor do SO.

Para saber mais sobre a configuração das configurações de TIME_WAIT TCP e do intervalo da porta de origem, consulte [Configurações que podem ser modificadas para melhorar o desempenho da rede](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fatores de rede virtual que podem afetar o desempenho

### <a name="vm-maximum-outbound-throughput"></a>Entrada máxima de saída vm

O Azure fornece uma variedade de tamanhos e tipos de VM, cada um com uma mistura diferente de capacidades de desempenho. Um desses recursos é o throughput de rede (ou largura de banda), que é medido em megabits por segundo (Mbps). Como as máquinas virtuais estão hospedadas em hardware compartilhado, a capacidade da rede precisa ser compartilhada de forma justa entre as máquinas virtuais usando o mesmo hardware. Máquinas virtuais maiores são alocadas com mais largura de banda do que máquinas virtuais menores.

A largura de banda de rede alocada a cada máquina virtual é limitada no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que deixa a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1.000 Mbps, esse limite se aplica se o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou uma fora do Azure.

A entrada não é limitada diretamente. Mas há outros fatores, como os limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

A rede acelerada foi projetada para melhorar o desempenho da rede, incluindo latência, throughput e utilização da CPU. A rede acelerada pode melhorar o throughput de uma máquina virtual, mas pode fazer isso apenas até a largura de banda alocada da máquina virtual.

As máquinas virtuais Azure têm pelo menos uma interface de rede conectada a elas. Eles podem ter vários. A largura de banda alocada a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede anexadas à máquina. Em outras palavras, a largura de banda é alocada em uma base de máquina por virtual, independentemente de quantas interfaces de rede estão conectadas à máquina.

O throughput de saída esperado e o número de interfaces de rede suportadas por cada tamanho de VM são detalhados em [Tamanhos para máquinas virtuais Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Para ver o throughput máximo, selecione um tipo, como **propósito geral,** e, em seguida, encontre a seção sobre a série de tamanho na página resultante (por exemplo, "Série Dv2"). Para cada série, há uma tabela que fornece especificações de rede na última coluna, que é intitulada "Max NICs / Largura de banda de rede esperada (Mbps)."

O limite de taxa de transferência se aplica à máquina virtual. O throughput não é afetado por esses fatores:

- **Número de interfaces de rede**: O limite de largura de banda se aplica à soma de todo o tráfego de saída da máquina virtual.

- **Rede acelerada**: Embora esse recurso possa ser útil para atingir o limite publicado, ele não altera o limite.

- **Destino do tráfego**: todos os destinos contam para o limite de saída.

- **Protocolo**: todo o tráfego de saída em todos os protocolos contam para o limite.

Para obter mais informações, consulte [a largura de banda da rede](https://aka.ms/AzureBandwidth)da máquina virtual .

### <a name="internet-performance-considerations"></a>Considerações sobre desempenho na Internet

Como discutido ao longo deste artigo, fatores na internet e fora do controle do Azure podem afetar o desempenho da rede. Aqui estão alguns desses fatores:

- **Latência**: O tempo de ida e volta entre dois destinos pode ser afetado por problemas em redes intermediárias, pelo tráfego que não pega o caminho de distância "mais curto" e por caminhos de peering subótimos.

- **Perda de pacote**: A perda de pacotes pode ser causada por congestionamento de rede, problemas físicos de caminho e dispositivos de rede com baixo desempenho.

- **Tamanho/Fragmentação do MTU**: A fragmentação ao longo do caminho pode levar a atrasos na chegada de dados ou em pacotes que chegam fora de ordem, o que pode afetar a entrega de pacotes.

Traceroute é uma boa ferramenta para medir características de desempenho da rede (como perda de pacotes e latência) ao longo de cada caminho de rede entre um dispositivo de origem e um dispositivo de destino.

### <a name="network-design-considerations"></a>Considerações sobre o projeto da rede

Junto com as considerações discutidas anteriormente neste artigo, a topologia de uma rede virtual pode afetar o desempenho da rede. Por exemplo, um design de hub-and-spoke que retrae o tráfego globalmente para uma rede virtual de um único hub introduzirá latência de rede, o que afetará o desempenho geral da rede.

O número de dispositivos de rede por onde passa o tráfego de rede também pode afetar a latência geral. Por exemplo, em um design hub-and-spoke, se o tráfego passar por um aparelho virtual de rede de fala e um aparelho virtual hub antes de transitar para a internet, os aparelhos virtuais da rede podem introduzir latência.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiões azure, redes virtuais e latência

As regiões azure são compostas por vários data centers que existem dentro de uma área geográfica geral. Esses data centers podem não estar fisicamente próximos um do outro. Em alguns casos, eles estão separados por até 10 quilômetros. A rede virtual é uma sobreposição lógica no topo da rede de datacenter físico do Azure. Uma rede virtual não implica nenhuma topologia de rede específica dentro do data center.

Por exemplo, duas VMs que estão na mesma rede virtual e sub-rede podem estar em racks, linhas ou até mesmo data centers diferentes. Eles podem ser separados por pés de cabo de fibra óptica ou por quilômetros de cabo de fibra óptica. Esta variação poderia introduzir latência variável (uma diferença de alguns milissegundos) entre vms diferentes.

A colocação geográfica das VMs, e a latência potencial resultante entre duas VMs, podem ser influenciadas pela configuração de conjuntos de disponibilidade e Zonas de Disponibilidade. Mas a distância entre data centers em uma região é específica da região e influenciada principalmente pela topologia de datacenters na região.

### <a name="source-nat-port-exhaustion"></a>Fonte de exaustão da porta NAT

Uma implantação no Azure pode se comunicar com endpoints fora do Azure na internet pública e/ou no espaço IP público. Quando uma instância inicia uma conexão de saída, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que o Azure criar esse mapeamento, o tráfego de retorno para o fluxo originado de saída também pode chegar ao endereço IP privado de origem.

Para cada conexão de saída, o Azure Load Balancer precisa manter esse mapeamento por algum período de tempo. Com a natureza multi-inquilina do Azure, manter esse mapeamento para cada fluxo de saída para cada VM pode ser intensivo em recursos. Portanto, existem limites definidos e baseados na configuração da Rede Virtual Azure. Ou, para dizer que, mais precisamente, uma VM Azure só pode fazer um certo número de conexões de saída em um dado momento. Quando esses limites forem atingidos, a VM não será capaz de fazer mais conexões de saída.

Mas esse comportamento é configurável. Para obter mais informações sobre a exaustão da porta SNAT e SNAT, consulte [este artigo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Medir o desempenho da rede no Azure

Alguns dos máximos de desempenho neste artigo estão relacionados à latência da rede/tempo de ida e volta (RTT) entre duas VMs. Esta seção fornece algumas sugestões de como testar a latência/RTT e como testar o desempenho da rede TCP e vm. Você pode ajustar e testar desempenho os valores de TCP/IP e de rede discutidos anteriormente usando as técnicas descritas nesta seção. Você pode conectar valores de latência, MTU, MSS e tamanho da janela nos cálculos fornecidos anteriormente e comparar máximos teóricos com valores reais que você observa durante o teste.

### <a name="measure-round-trip-time-and-packet-loss"></a>Medir o tempo de ida e volta e a perda de pacotes

O desempenho do TCP depende muito do RTT e da perda de pacotes. O utilitário PING disponível no Windows e Linux fornece a maneira mais fácil de medir a perda de RTT e pacotes. A saída de PING mostrará a latência mínima/máxima/média entre uma fonte e destino. Também mostrará perda de pacotes. Ping usa o protocolo ICMP por padrão. Você pode usar o PsPing para testar o TCP RTT. Para obter mais informações, consulte [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Medir o throughput real de uma conexão TCP

NTttcp é uma ferramenta para testar o desempenho TCP de um Linux ou Windows VM. Você pode alterar várias configurações de TCP e, em seguida, testar os benefícios usando NTttcp. Para obter mais informações, consulte estes recursos:

- [Teste de largura de banda/throughput (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [Utilitário NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Medir a largura de banda real de uma máquina virtual

Você pode testar o desempenho de diferentes tipos de VM, rede acelerada e assim por diante, usando uma ferramenta chamada iPerf. O iPerf também está disponível no Linux e Windows. O iPerf pode usar TCP ou UDP para testar o throughput geral da rede. Os testes de throughput do iPerf TCP são influenciados pelos fatores discutidos neste artigo (como latência e TT). Assim, o UDP pode produzir melhores resultados se você apenas quiser testar o rendimento máximo.

Para obter mais informações, consulte estes artigos:

- [Solução de problemas Desempenho da rede Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Como validar a taxa de transferência VPN para uma rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Detectar comportamentos tcp ineficientes

Nas capturas de pacotes, os clientes do Azure podem ver pacotes TCP com sinalizadores TCP (SACK, DUP ACK, RETRANSMIT e FAST RETRANSMIT) que podem indicar problemas de desempenho da rede. Esses pacotes indicam especificamente ineficiências de rede resultantes da perda de pacotes. Mas a perda de pacotes não é necessariamente causada por problemas de desempenho do Azure. Problemas de desempenho podem ser resultado de problemas de aplicação, problemas no sistema operacional ou outros problemas que podem não estar diretamente relacionados à plataforma Azure.

Além disso, tenha em mente que algumas ACKs de retransmissão e duplicação são normais em uma rede. Os protocolos TCP foram construídos para serem confiáveis. Evidências desses pacotes TCP em uma captura de pacotes não indicam necessariamente um problema de rede sistêmica, a menos que sejam excessivos.

Ainda assim, esses tipos de pacotes são indicações de que o throughput tcp não está alcançando seu desempenho máximo, por razões discutidas em outras seções deste artigo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a sintonia de desempenho TCP/IP para VMs Do Zure, você pode querer ler sobre outras considerações para [planejar redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) ou aprender mais sobre como conectar e configurar redes [virtuais.](https://docs.microsoft.com/azure/virtual-network/)
