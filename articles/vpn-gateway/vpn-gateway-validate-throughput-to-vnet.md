---
title: Validar o throughput de VPN em uma rede virtual do Microsoft Azure
description: A finalidade deste documento é ajudar um usuário a validar a taxa de transferência de rede de seus recursos locais para uma máquina virtual do Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: a88e339e82484c2ec1cd2276f6218fa718b990f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860479"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Como validar a taxa de transferência VPN para uma rede virtual

Uma conexão de gateway de VPN permite que você estabeleça conectividade entre instalações segura entre a sua Rede Virtual do Azure e sua infraestrutura de TI local.

Este artigo mostra como validar a taxa de transferência de rede dos recursos locais para uma máquina virtual (VM) do Azure.

> [!NOTE]
> Este artigo destina-se a ajudar a diagnosticar e corrigir problemas comuns. Se você não conseguir solucionar o problema usando as informações a seguir, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Visão geral

A conexão de gateway de VPN envolve os seguintes componentes:

* Dispositivo VPN no local (Exibir uma lista de [dispositivos VPN validados](vpn-gateway-about-vpn-devices.md#devicetable).)
* Internet pública
* Gateway de VPN do Azure
* VM do Azure

O diagrama a seguir mostra a conectividade lógica de uma rede local para uma rede virtual do Azure por meio de VPN.

![Conectividade Lógica da Rede do Cliente com a Rede MSFT usando VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcule a entrada/saída máxima esperada

1. Determine os requisitos da taxa de transferência de linha de base do aplicativo.
1. Determine os limites de taxa de transferência de gateway de VPN do Azure. Para obter ajuda, consulte a seção "Gateway SKUs" do [About VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Determine as [Diretrizes de taxa de transferência para a VM do Azure](../virtual-machines/virtual-machines-windows-sizes.md) para seu tamanho de VM.
1. Determine a largura de banda do Provedor de Serviços de Internet (ISP).
1. Calcule o seu throughput esperado tomando a menor largura de banda do VM, VPN Gateway ou ISP; que é medido em Megabits por segundo (/) dividido por oito (8).

Se o seu throughput calculado não atender aos requisitos de throughput da linha de base do aplicativo, você deve aumentar a largura de banda do recurso que você identificou como o gargalo. Para redimensionar um Gateway de VPN do Azure, consulte [Alterar um SKU de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para redimensionar uma máquina virtual, consulte [Redimensionar uma VM](../virtual-machines/virtual-machines-windows-resize-vm.md). Se você não estiver experimentando a largura de banda da Internet esperada, você também pode entrar em contato com seu provedor de internet.

> [!NOTE]
> O throughput do VPN Gateway é um agregado de todas as conexões site-to-Site\VNET-to-VNET ou Point-to-Site.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validar a taxa de transferência de rede usando as ferramentas de desempenho

Essa validação deve ser executada durante o horário de pico, já que a saturação de taxa de transferência de túnel VPN durante o teste não apresenta resultados precisos.

A ferramenta que usamos para esse teste é iPerf, que funciona tanto no Windows como no Linux e tem modos de cliente e servidor. É limitado a 3Gbps para VMs Windows.

Essa ferramenta não executa operações de leitura/gravação em disco. A ferramenta somente produz o tráfego TCP gerado automaticamente de uma extremidade à outra. Ele gera estatísticas baseadas em experimentação que mede a largura de banda disponível entre nós cliente e servidor. Ao testar entre dois nós, um nó age como servidor, e o outro atua como cliente. Uma vez que este teste seja concluído, recomendamos que você inverta as funções dos nós para testar o upload e o download de throughput em ambos os nós.

### <a name="download-iperf"></a>Baixar iPerf

Baixar [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para obter detalhes, consulte a [Documentação iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Os produtos de terceiros discutidos neste artigo são fabricados por empresas independentes da Microsoft. A Microsoft não oferece nenhuma garantia, implícita ou não, sobre o desempenho ou a confiabilidade desses produtos.

### <a name="run-iperf-iperf3exe"></a>Executar iPerf (iperf3.exe)

1. Habilite uma regra NSG/ACL permitindo o tráfego (para teste de endereço IP público em VM do Azure).

1. Em ambos os nós, habilite uma exceção de firewall para a porta 5001.

   **Windows:** Execute o seguinte comando como um administrador:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Para remover a regra após a conclusão do teste, execute esse comando:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** As imagens do Azure Linux têm firewalls permissivos. Se houver um aplicativo escutando em uma porta, o tráfego será permitido. Imagens personalizadas que são protegidas podem precisar de portas abertas explicitamente. Os firewalls da camada de OS do Linux comuns incluem `iptables`, `ufw`, ou `firewalld`.

1. No nó de servidor, altere para o diretório onde o iperf3.exe é extraído. Em seguida, execute o iPerf no modo servidor e configure-o para ouvir na porta 5001 como os seguintes comandos:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > A porta 5001 é personalizável para explicar as restrições específicas de firewall em seu ambiente.

1. No nó de cliente, altere para o diretório onde a ferramenta iperf é extraída e, em seguida, execute o seguinte comando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   O cliente está direcionando trinta segundos de tráfego na porta 5001, para o servidor. O sinalizador '-P' indica que estamos fazendo 32 conexões simultâneas ao nó do servidor.

   A tela a seguir mostra a saída a partir desse exemplo:

   ![Saída](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (OPCIONAL) Para preservar os resultados dos testes, execute este comando:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Após concluir as etapas anteriores, execute as mesmas etapas com as funções invertidas, de modo que o nó do servidor agora será o nó do cliente e vice-versa.

> [!Note]
> O Iperf não é a única ferramenta. [NTTTCP é uma solução alternativa para testes.](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)

## <a name="test-vms-running-windows"></a>Teste VMs executando o Windows

### <a name="load-latteexe-onto-the-vms"></a>Carregar Latte.exe nas VMs

Baixe a versão mais recente do [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Considere colocar Latte.exe em pasta separada, como`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Permita latte.exe através do firewall do Windows

No receptor, crie uma regra Permitir no Firewall do Windows para permitir que o tráfego Latte.exe chegue. É mais fácil permitir todo o programa Latte.exe pelo nome em vez de permitir a entrada de portas TCP específicas.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Permita latte.exe através do Firewall do Windows como este

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Por exemplo, se você copiou latte.exe para a pasta "c:\tools", este seria o comando

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

Inicie latte.exe no RECEIVER (executado a partir de CMD, não da PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Cerca de 65 mil iterações é tempo suficiente para retornar resultados representativos.

Qualquer número de porta disponível é bom.

Se a VM tiver um endereço IP de 10.0.0.4, ela ficaria assim

`latte -c -a 10.0.0.4:5005 -i 65100`

Inicie o latte.exe no SENDER (executado a partir de CMD, não da PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

O comando resultante é o mesmo do receptor, exceto com a adição de "-c" para indicar que este é o "cliente" ou remetente

`latte -c -a 10.0.0.4:5005 -i 65100`

Aguarde os resultados. Dependendo da distância das VMs, pode levar alguns minutos para ser concluída. Considere começar com menos iterações para testar o sucesso antes de realizar testes mais longos.

## <a name="test-vms-running-linux"></a>Teste VMs executando Linux

Use [SockPerf](https://github.com/mellanox/sockperf) para testar VMs.

### <a name="install-sockperf-on-the-vms"></a>Instale sockperf nas VMs

Nos VMs Linux (sender e RECEIVER), execute esses comandos para preparar o SockPerf em suas VMs:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Instalar GIT e outras ferramentas úteis

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Instalar O GIT e outras ferramentas úteis

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - todos

Da linha de comando bash (assume que o git está instalado)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Fazer é mais lento, pode levar vários minutos

`make`

Fazer instalação é rápido

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nas VMs

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Comandos de amostra após a instalação. Servidor/Receptor - assume que o IP do servidor é 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Cliente - assume que o IP do servidor é 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Certifique-se de que não há saltos intermediários (por exemplo, Virtual Appliance) durante o teste de throughput entre o VM e o Gateway.
> Se houver resultados ruins (em termos de throughput geral) provenientes dos testes iPERF/NTTTCP acima, consulte o artigo a seguir para entender os principais fatores por trás das possíveis causas básicas do problema:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Em particular, a análise de rastreamentos de captura de pacotes (Wireshark/Network Monitor) coletados em paralelo do cliente e do servidor durante esses testes ajudará nas avaliações de desempenho ruim. Esses traços podem incluir perda de pacotes, alta latência, tamanho de MTU. fragmentação, Janela TCP 0, Fragmentos fora da ordem, e assim por diante.

## <a name="address-slow-file-copy-issues"></a>Solucionar problemas com cópia de arquivo lenta

Mesmo que o throughput geral avaliado com as etapas anteriores (iPERF/NTTTCP/etc.) tenha sido bom, você pode experimentar um enfrentamento lento do arquivo ao usar o Windows Explorer ou arrastar e soltar durante uma sessão RDP. Esse problema normalmente ocorre devido a um ou ambos os seguintes fatores:

* Os aplicativos de cópia de arquivo, como o Windows Explorer e o RDP não usam múltiplos threads ao copiar arquivos. Para obter melhor desempenho, use um aplicativo de cópia de arquivo multi-threaded como o [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) para copiar arquivos usando 16 ou 32 threads. Para alterar o número do segmento para cópia do arquivo em Richcopy, clique em**Opções** >  **de** > arquivo de cópia de arquivo**de cópia de ação**.

   ![Problemas com cópia de arquivo lenta](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nem todos os aplicativos funcionam da mesma forma, e nem todos os aplicativos/processos utilizam todos os segmentos. Se você executar o teste, você pode ver alguns segmentos vazios e não fornecer resultados precisos de throughput.
   > Para verificar o desempenho de transferência de arquivo do aplicativo, use multi-thread aumentando o # de thread em sucessão ou diminua para encontrar o rendimento ideal do aplicativo ou transferência de arquivos.

* Velocidade de leitura/gravação de disco de VM insuficiente. Para obter mais informações, consulte [Solução de Problemas de Armazenamento do Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interface externa do dispositivo local

Mencionou as sub-redes de faixas no local que você gostaria que o Azure alcançasse via VPN no Local Network Gateway. Simultaneamente, defina o espaço de endereço VNET no Azure para o dispositivo no local.

* **Gateway baseado em rota**: A diretiva ou o seletor de tráfego para VPNs baseadas em rota são configurados como qualquer um para qualquer (ou wild cards).

* **Gateway baseado em políticas**: VPNs baseadas em políticas criptografam e direcionam pacotes através de túneis IPsec com base nas combinações de prefixos de endereço entre sua rede on-premises e o Azure VNet. A política (ou o seletor de tráfego) normalmente é definida como uma lista de acesso na configuração de VPN.

* **UsePolicyBasedTrafficSelector** conexões: ("UsePolicyBasedTrafficSelectors" para $True em uma conexão configurarão o gateway Azure VPN para se conectar ao firewall VPN baseado em políticas nas instalações. Se você habilitar PolicyBasedTrafficSelectors, você precisa garantir que seu dispositivo VPN tenha os seletores de tráfego correspondentes definidos com todas as combinações de prefixos de rede local (gateway de rede local) para e a partir dos prefixos de rede virtual do Azure, em vez de any-to-any.

A configuração inadequada pode levar a desconexões frequentes dentro do túnel, quedas de pacotes, mau throughput e latência.

## <a name="check-latency"></a>Verificar latência

Você pode verificar a latência usando as seguintes ferramentas:

* WinMTR
* TCPTraceroute
* `ping`e `psping` (Essas ferramentas podem fornecer uma boa estimativa de RTT, mas não podem ser usadas em todos os casos.)

![Verificar latência](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Se você notar um pico de latência alta em qualquer um dos saltos antes de entrar na espinha dorsal da MS Network, você pode querer prosseguir com investigações adicionais com o seu Provedor de Serviços de Internet.

Se um grande e incomum pico de latência for notado a partir de saltos dentro de "msn.net", entre em contato com o suporte da MS para investigações adicionais.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações ou ajuda, confira o seguinte link:

* [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
