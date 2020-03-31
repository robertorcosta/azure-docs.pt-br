---
title: Teste a latência da rede de máquinas virtuais Azure em uma rede virtual Azure | Microsoft Docs
description: Saiba como testar a latência de rede entre máquinas virtuais Do Zure em uma rede virtual
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896368"
---
# <a name="test-vm-network-latency"></a>Testar latência da rede de VMs

Para obter os resultados mais precisos, meça a latência da rede da máquina virtual Azure (VM) com uma ferramenta projetada para a tarefa. Ferramentas disponíveis publicamente, como SockPerf (para Linux) e latte.exe (para Windows) podem isolar e medir a latência da rede, excluindo outros tipos de latência, como latência de aplicativos. Essas ferramentas se concentram no tipo de tráfego de rede que afeta o desempenho do aplicativo (ou seja, o protocolo de controle de transmissão [TCP] e o tráfego do Protocolo de Datagrama do Usuário [UDP]). 

Outras ferramentas comuns de conectividade, como o Ping, podem medir a latência, mas seus resultados podem não representar o tráfego de rede usado em cargas de trabalho reais. Isso porque a maioria dessas ferramentas empregam o Protocolo de Mensagens de Controle da Internet (ICMP), que pode ser tratado de forma diferente do tráfego de aplicativos e cujos resultados podem não se aplicar a cargas de trabalho que usam TCP e UDP. 

Para testes precisos de latência de rede dos protocolos usados pela maioria dos aplicativos, sockperf (para Linux) e latte.exe (para Windows) produzem os resultados mais relevantes. Este artigo abrange ambas as ferramentas.

## <a name="overview"></a>Visão geral

Usando duas VMs, uma como remetente e outra como receptor, você cria um canal de comunicação bidirecional. Com esta abordagem, você pode enviar e receber pacotes em ambas as direções e medir o tempo de ida e volta (RTT).

Você pode usar essa abordagem para medir a latência da rede entre duas VMs ou até mesmo entre dois computadores físicos. As medidas de latência podem ser úteis para os seguintes cenários:

- Estabeleça um benchmark de latência de rede entre as VMs implantadas.
- Compare os efeitos das alterações na latência da rede após alterações relacionadas serem feitas para:
  - Sistema operacional (SISTEMA OPERACIONAL) ou software de pilha de rede, incluindo alterações de configuração.
  - Um método de implantação de VM, como a implantação em uma zona de disponibilidade ou grupo de colocação de proximidade (PPG).
  - Propriedades vm, como rede acelerada ou alterações de tamanho.
  - Uma rede virtual, como roteamento ou alterações de filtragem.

### <a name="tools-for-testing"></a>Ferramentas para testes
Para medir a latência, você tem duas opções de ferramentas diferentes:

* Para sistemas baseados no Windows: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Para sistemas baseados em Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Ao usar essas ferramentas, você ajuda a garantir que apenas os tempos de entrega de carga TCP ou UDP sejam medidos e não ICMP (Ping) ou outros tipos de pacotes que não sejam usados por aplicativos e não afetem seu desempenho.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Dicas para criar uma configuração ótima de VM

Ao criar sua configuração de VM, tenha em mente as seguintes recomendações:
- Use a versão mais recente do Windows ou Linux.
- Habilite a rede acelerada para obter melhores resultados.
- Implantar VMs com [um grupo de colocação de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
- VMs maiores geralmente têm um desempenho melhor do que as VMs menores.

### <a name="tips-for-analysis"></a>Dicas para análise

Ao analisar os resultados dos testes, tenha em mente as seguintes recomendações:

- Estabeleça uma linha de base mais cedo, assim que a implantação, configuração e otimizações estiverem concluídas.
- Compare sempre novos resultados com uma linha de base ou, caso contrário, de um teste para outro com alterações controladas.
- Repita os testes sempre que as alterações forem observadas ou planejadas.


## <a name="test-vms-that-are-running-windows"></a>Teste VMs que estão executando o Windows

### <a name="get-latteexe-onto-the-vms"></a>Obter latte.exe para as VMs

Baixe a [versão mais recente do latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Considere colocar latte.exe em pasta separada, como *c:\ferramentas*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Permita latte.exe através do Firewall do Windows Defender

No *receptor,* crie uma regra Permitir no Firewall do Windows Defender para permitir que o tráfego latte.exe chegue. É mais fácil permitir todo o programa latte.exe pelo nome em vez de permitir a entrada de portas TCP específicas.

Permita latte.exe através do Firewall do Windows Defender executando o seguinte comando:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Por exemplo, se você copiou latte.exe para a pasta *c:\tools,* este seria o comando:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Executar testes de latência

* No *receptor,* inicie latte.exe (execute-o a partir da janela CMD, não do PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Cerca de 65.000 iterações são tempo suficiente para retornar resultados representativos.

    Qualquer número de porta disponível é bom.

    Se a VM tiver um endereço IP de 10.0.0.4, o comando ficaria assim:

    `latte -a 10.0.0.4:5005 -i 65100`

* No *remetente,* inicie latte.exe (execute-o a partir da janela CMD, não do PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    O comando resultante é o mesmo do receptor,&nbsp;exceto com a adição de *-c* para indicar que este é o *cliente*, ou *remetente*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Aguarde os resultados. Dependendo da distância das VMs, o teste pode levar alguns minutos para ser concluído. Considere começar com menos iterações para testar o sucesso antes de realizar testes mais longos.

## <a name="test-vms-that-are-running-linux"></a>Teste VMs que estão executando o Linux

Para testar VMs que estão executando o Linux, use [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Instale sockperf nas VMs

Nos VMs Linux, tanto *o remetente* quanto o *receptor,* execute os seguintes comandos para preparar SockPerf nas VMs. Comandos são fornecidos para as principais distros.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Para Red Hat Enterprise Linux (RHEL)/CentOS

Execute os seguintes comandos:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Para Ubuntu

Execute os seguintes comandos:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Para todas as distros

Copie, compile e instale o SockPerf de acordo com as seguintes etapas:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Executar SockPerf nas VMs

Depois que a instalação do SockPerf estiver concluída, as VMs estão prontas para executar os testes de latência. 

Primeiro, inicie SockPerf no *receptor.*

Qualquer número de porta disponível é bom. Neste exemplo, usamos a porta 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Agora que o servidor está ouvindo, o cliente pode começar a enviar pacotes para o servidor na porta em que está ouvindo (neste caso, 12345).

Cerca de 100 segundos é tempo suficiente para retornar resultados representativos, como mostrado no exemplo a seguir:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Aguarde os resultados. Dependendo da distância entre as VMs, o número de iterações vai variar. Para testar o sucesso antes de realizar testes mais longos, considere começar com testes mais curtos de cerca de 5 segundos.

Este exemplo sockPerf usa um tamanho de mensagem de 350 bytes, que é típico para um pacote médio. Você pode ajustar o tamanho maior ou menor para obter resultados que representem com mais precisão a carga de trabalho que está sendo executado em suas VMs.


## <a name="next-steps"></a>Próximas etapas
* Melhore a latência com [um grupo de colocação de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
* Aprenda a otimizar a [rede para VMs](../virtual-network/virtual-network-optimize-network-bandwidth.md) para o seu cenário.
* Leia sobre [como a largura de banda é alocada em máquinas virtuais](../virtual-network/virtual-machine-network-throughput.md).
* Para obter mais informações, consulte [Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md).
