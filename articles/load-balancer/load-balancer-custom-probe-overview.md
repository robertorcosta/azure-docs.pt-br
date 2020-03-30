---
title: Sondas de saúde para dimensionar e fornecer HA para o seu serviço
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a usar testes de saúde para monitorar instâncias por trás do Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: ec1507e09a183f8d466a456b70151861f5f0e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159431"
---
# <a name="load-balancer-health-probes"></a>Investigações de integridade do Load Balancer

Ao usar as regras de balanceamento de carga com o Azure Load Balancer, você precisa especificar testes de saúde para permitir que o Balanceador de carga detecte o status de ponto final de backend.  A configuração das respostas do teste de saúde e do teste determinam quais instâncias de pool backend receberão novos fluxos. Você pode usar testes de saúde para detectar a falha de um aplicativo em um ponto final de backend. Você também pode gerar uma resposta personalizada para uma investigação de integridade e usar a investigação de integridade para controle de fluxo, a fim de gerenciar o tempo de inatividade planejado ou de carga. Quando um teste de saúde falha, o Balanceador de Carga deixará de enviar novos fluxos para a respectiva instância insalubre. A conectividade de saída não é impactada, apenas a conectividade de entrada é impactada.

As investigações de integridade dão suporte a vários protocolos. A disponibilidade de um protocolo específico de teste de saúde varia de acordo com o Load Balancer SKU.  Além disso, o comportamento do serviço varia de acordo com o Load Balancer SKU, conforme mostrado nesta tabela:

| | SKU Standard | SKU Básico |
| --- | --- | --- |
| [Tipos de investigações](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação inoperante](#probedown) | Todas as investigações inoperantes, todos os fluxos TCP continuam. | Todas as sondas para baixo, todos os fluxos TCP expiram. | 


>[!IMPORTANT]
>Revise este documento na íntegra, incluindo [importantes orientações de design](#design) abaixo para criar um serviço confiável.

>[!IMPORTANT]
>As investigações de integridade do Load Balancer originam-se no endereço IP 168.63.129.16 e não devem ser bloqueadas para que as investigações marquem a instância como operante.  Revisar o [endereço IP de origem da investigação](#probesource) para obter detalhes.

>[!IMPORTANT]
>Independentemente do limite de tempo configurado, os testes de saúde HTTP(S) Load Balancer farão um teste de saúde automaticamente em uma instância se o servidor retornar qualquer código de status que não seja HTTP 200 OK ou se a conexão for encerrada via reset TCP.

## <a name="probe-configuration"></a><a name="probes"></a>Configuração do teste

A configuração do teste de saúde consiste nos seguintes elementos:

- Duração do intervalo entre sondas individuais
- Número de respostas da sonda que devem ser observadas antes da transição da sonda para um estado diferente
- Protocolo da sonda
- Porta da sonda
- Caminho HTTP para usar para HTTP GET ao usar testes HTTP(S)

>[!NOTE]
>Uma definição de teste não é obrigatória ou verificada ao usar o Azure PowerShell, Azure CLI, Templates ou API. Os testes de validação do teste só são feitos quando se usa o Portal Azure.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Entendendo o sinal do aplicativo, detecção do sinal e reação da plataforma

O número de respostas do teste se aplica a ambos

- o número de sondas bem sucedidas que permitem que uma instância seja marcada como para cima, e
- o número de testes cronometrados que fazem com que uma instância seja marcada como para baixo.

Os valores de tempo e intervalo especificados determinam se uma instância será marcada para cima ou para baixo.  A duração do intervalo multiplicado pelo número de respostas do teste determina a duração durante a qual as respostas do teste devem ser detectadas.  E o serviço reagirá depois que as sondas necessárias tiverem sido alcançadas.

Podemos ilustrar ainda mais o comportamento com um exemplo. Se você tiver definido o número de respostas do teste para 2 e o intervalo para 5 segundos, isso significa que 2 falhas de tempo de intervalo do teste devem ser observadas dentro de um intervalo de 10 segundos.  Como o tempo em que um teste é enviado não é sincronizado quando seu aplicativo pode alterar o estado, podemos limitar o tempo para detectar por dois cenários:

1. Se o aplicativo começar a produzir uma resposta de teste de tempo limite pouco antes da chegada do primeiro teste, a detecção desses eventos levará 10 segundos (intervalos de 2 x 5 segundos) mais a duração do aplicativo começando a sinalizar um tempo limite para quando o primeiro sonda chega.  Você pode assumir que esta detecção levará pouco mais de 10 segundos.
2. Se o aplicativo começar a produzir uma resposta de teste de tempo limite logo após a chegada do primeiro teste, a detecção desses eventos não começará até que a próxima sonda chegue (e aintervalo) mais 10 segundos (intervalos de 2 x 5 segundos).  Você pode assumir que esta detecção leva pouco menos de 15 segundos.

Para este exemplo, uma vez que a detecção tenha ocorrido, a plataforma levará um pequeno tempo para reagir a essa mudança.  Isso significa que, dependendo de 

1. quando a aplicação começa a mudar de estado e
2. quando essa alteração é detectada e atendida os critérios necessários (número de sondas enviadas no intervalo especificado) e
3. quando a detecção foi comunicada através da plataforma 

você pode assumir que a reação a uma resposta de teste de tempo limite levará entre um mínimo de pouco mais de 10 segundos e um máximo de pouco mais de 15 segundos para reagir a uma mudança no sinal do aplicativo.  Este exemplo é fornecido para ilustrar o que está ocorrendo, no entanto, não é possível prever uma duração exata além da orientação áspera acima ilustrada neste exemplo.
 
## <a name="probe-types"></a><a name="types"></a>Tipos de investigações

O protocolo utilizado pela sonda de saúde pode ser configurado para um dos seguintes:

- [Ouvintes TCP](#tcpprobe)
- [Pontos de extremidade HTTP](#httpprobe)
- [Pontos de Extremidade HTTPS](#httpsprobe)

Os protocolos disponíveis dependem do SKU do Balanceador de carga usado:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| SKU Standard |    &#9989; |   &#9989; |   &#9989; |
| SKU Básico |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>Investigação TCP

As investigações TCP iniciam uma conexão executando um handshake TCP aberto de três vias com a porta definida.  As investigações TCP encerram uma conexão com um handshake TCP fechado de quatro vias.

O intervalo mínimo de investigação é de 5 segundos e o número mínimo de respostas não íntegras é 2.  A duração total de todos os intervalos não pode exceder 120 segundos.

Uma investigação TCP falha quando:
* O ouvinte TCP na instância não responde durante o período de tempo limite.  Um teste é marcado com base no número de solicitações de teste com tempo de saída, que foram configuradas para ficar sem resposta antes de marcar a sonda.
* A investigação recebe uma redefinição de TCP da instância.

A seguir ilustra como você pode expressar esse tipo de configuração de teste em um modelo do Gerenciador de recursos:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a> <a name="httpsprobe"></a> Investigação HTTP / HTTPS

>[!NOTE]
>Investigação HTTPS está disponível somente para [Standard Load Balancer](load-balancer-standard-overview.md).

As investigações HTTP e HTTPS se baseiam na investigação TCP e emitem um HTTP GET com o caminho especificado. Ambas essas investigações dão suporte a caminhos relativos para o HTTP GET. Investigações HTTPS são iguais às investigações HTTP com a adição de um wrapper de TLS (Transport Layer Security), anteriormente conhecido como SSL. A investigação de integridade é marcada como operante quando a instância responde com um status HTTP 200 dentro do período de tempo limite.  A investigação de integridade tenta verificar a porta de investigação de integridade configurada a cada 15 segundos, por padrão. O intervalo mínimo de investigação é de 5 segundos. A duração total de todos os intervalos não pode exceder 120 segundos.

Os testes HTTP / HTTPS também podem ser úteis para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga se a porta do teste também for o ouvinte do serviço em si. Por exemplo, é recomendável remover uma instância caso ela esteja usando mais de 90% da CPU e retorne um status HTTP diferente de 200. 

> [!NOTE] 
> A sonda HTTPS requer o uso de certificados baseados que tenham um hash de assinatura mínima de SHA256 em toda a cadeia.

Se você usar o Serviços de Nuvem e tiver funções web que usem w3wp.exe, também é possível obter o monitoramento automático do site. Falhas no código do site retornam um status não 200 para a investigação do balanceador de carga.

Uma investigação HTTP / HTTPS falha quando:
* O ponto de extremidade da investigação retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Isso marcará imediatamente a investigação de integridade como inoperante. 
* O ponto final do teste não responde durante o intervalo mínimo do intervalo do teste e o período de tempo de 30 segundos. Várias solicitações de investigação podem ficar sem resposta antes que a investigação seja marcada como não estando em execução e até que a soma de todos os intervalos de tempo limite seja atingido.
* O ponto de extremidade de investigação fecha a conexão por meio de uma redefinição de TCP.

A seguir ilustra como você pode expressar esse tipo de configuração de teste em um modelo do Gerenciador de recursos:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Investigação de agente convidado (somente clássico)

Por padrão, as funções do serviço de nuvem (funções de trabalho e funções web) usam um agente convidado para o monitoramento de investigação.  Uma investigação de agente convidado é uma configuração de último recurso.  Sempre use uma investigação de integridade explicitamente com uma investigação TCP ou HTTP. Uma investigação de agente convidado não é tão eficiente quanto a investigações definidas explicitamente para a maioria dos cenários de aplicativo.

Uma investigação de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, ele escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto. (Outros estados são Ocupado, Reciclando ou Parando.)

Para obter mais informações, consulte [Configurar o arquivo de definição de serviço (csdef) para investigações de integridade](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [Introdução à criação de um balanceador de carga público para serviços de nuvem](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta. Em seguida, ele para de enviar fluxos a essa instância. O balanceador de carga continua a verificar a instância. 

Se o agente convidado responder com um HTTP 200, o balanceador de carga enviará o tráfego para essa instância novamente.

Quando você usa uma função Web, o código do site geralmente é executado em w3wp.exe, que não é monitorado nem pela malha do Azure nem pelo agente convidado. Falhas em w3wp.exe (por exemplo, respostas HTTP 500) não são relatadas para o agente convidado. Consequentemente, o balanceador de carga não perde essa instância.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Sondar o comportamento

Os testes de saúde TCP, HTTP e HTTPS são considerados saudáveis e marcam o ponto final backend como saudável quando:

* A investigação de integridade é bem-sucedida uma vez após a inicialização da VM.
* O número especificado de sondas necessárias para marcar o ponto final de backend como saudável foi alcançado.

Qualquer ponto final de backend que tenha alcançado um estado saudável é elegível para receber novos fluxos.  

> [!NOTE]
> Se a sonda de saúde flutuar, o balanceador de carga espera mais tempo antes de colocar o ponto final backend de volta no estado saudável. Esse tempo de espera extra protege o usuário e a infraestrutura, e é uma política intencional.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Comportamento de investigação inoperante

### <a name="tcp-connections"></a>Conexões TCP

Novas conexões TCP terão sucesso em manter o ponto final de backend saudável.

Se a sonda de saúde de um backend endpoint falhar, as conexões TCP estabelecidas para este ponto final backend continuarão.

Se todas as investigações para todas as instâncias em um pool de back-end falharem, nenhum novo fluxo será enviado ao pool de back-end. O Load Balancer Standard permitirá a continuação dos fluxos TCP estabelecidos.  O Basic Load Balancer terminará todos os fluxos TCP existentes para o pool de back-end.
 
O Load Balancer é um serviço de passagem (não encerra conexões TCP) e o fluxo ocorre sempre entre o cliente e o sistema operacional convidado e o aplicativo da VM. Uma piscina com todas as sondas para baixo fará com que um frontend não responda às tentativas abertas de conexão TCP (SYN), pois não há um ponto final de backend saudável para receber o fluxo e responder com um SYN-ACK.

### <a name="udp-datagrams"></a>Datagramas UDP

Os datagramas UDP serão entregues em pontos finais de backend saudáveis.

UDP é sem conexão e não há estado de fluxo controlado para UDP. Se o teste de saúde de backend endpoint falhar, os fluxos UDP existentes passarão para outra instância saudável no pool de backend.

Se todas as investigações para todas as instâncias em um pool de back-end falharem, os fluxos UDP existentes serão encerrados para os Basic e Standard Load Balancers.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Endereço IP de origem da investigação

O Load Balancer usa um serviço de investigação distribuído para seu modelo de integridade interno. O serviço de investigação reside em cada host no qual as VMs e podem ser programadas sob demanda para gerar investigações de integridade de acordo com a configuração do cliente. O tráfego da investigação de integridade está diretamente entre o serviço de investigação que gera a investigação de integridade e a VM do cliente. Todas as investigações de integridade do Load Balancer originam-se do endereço IP 168.63.129.16 como sua fonte.  Use o espaço de endereços IP em uma VNET que não seja o espaço RFC1918.  O uso de um endereço IP globalmente reservado e de propriedade da Microsoft reduz a possibilidade de um conflito de endereços IP com o espaço de endereços IP utilizado na VNET.  Esse endereço IP é o mesmo em todas as regiões e não é alterado nem representa um risco à segurança porque apenas o componente interno da plataforma Azure pode obter um pacote desse endereço IP. 

A marca de serviço AzureLoadBalancer identifica esse endereço IP de origem nos [grupos de segurança de rede](../virtual-network/security-overview.md) e permite o tráfego da investigação de integridade por padrão.

Além dos testes de saúde do Load Balancer, as [seguintes operações usam este endereço IP:](../virtual-network/what-is-ip-address-168-63-129-16.md)

- Permite que o Agente de VM se comunique com a plataforma para sinalizar que ele está em um estado "Pronto"
- Permite a comunicação com o servidor virtual de DNS para fornecer resolução de nome filtrado aos clientes que não definem servidores DNS personalizados.  Essa filtragem garante que cada cliente só possa resolver os nomes de host de sua própria implantação.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.

## <a name="design-guidance"></a><a name="design"></a>Orientação de design

As investigações de integridade são usadas para manter seu serviço resiliente e permitir que ele seja dimensionado. Uma configuração incorreta ou um padrão de design inadequado pode afetar a disponibilidade e a escalabilidade do serviço. Leia este documento na íntegra e considere qual é o impacto no seu cenário quando a resposta dessa investigação é marcada como inoperante ou operante e como isso afeta a disponibilidade do cenário de aplicativo.

Ao projetar o modelo de saúde para sua aplicação, você deve sondar uma porta em um ponto final de backend que reflete a saúde dessa instância __e__ o serviço de aplicação que você está fornecendo.  A porta do aplicativo e a porta de investigação não precisam ser as mesmas.  Em alguns cenários, pode ser desejável que a porta de investigação seja diferente da porta na qual o aplicativo fornece o serviço.  

Às vezes, pode ser útil para seu aplicativo gerar uma resposta de investigação de integridade para não apenas detectar a integridade do aplicativo, mas também sinalizar diretamente para o Load Balancer se a instância deve receber ou não novos fluxos.  Você pode manipular a resposta de investigação para permitir que seu aplicativo crie a pressão de retorno e limite a entrega de novos fluxos para uma instância que falhou na investigação de integridade ou preparar a manutenção do aplicativo e iniciar a drenagem do cenário.  Ao usar o Standard Load Balancer, um sinal de [investigação inoperante](#probedown) sempre permitirá a continuidade dos fluxos TCP até o tempo limite ocioso ou o fechamento da conexão. 

Para o balanceamento de carga UDP, você deve gerar um sinal de teste de saúde personalizado a partir do ponto final do backend e usar um teste de saúde TCP, HTTP ou HTTPS direcionado ao ouvinte correspondente para refletir a saúde do seu aplicativo UDP.

Ao usar as [regras de balanceamento de carga das Portas HA](load-balancer-ha-ports-overview.md) com o [Standard Load Balancer](load-balancer-standard-overview.md), as cargas de todas as portas serão balanceadas e uma única resposta da investigação de integridade deverá refletir o status de toda a instância.

Não converta uma investigação de integridade nem use um proxy para ela por meio da instância que recebe a investigação de integridade para outra instância na VNET, pois essa configuração pode resultar em falhas em cascata no cenário.  Considere o seguinte cenário: um conjunto de dispositivos de terceiros é implantado no pool de back-end de um recurso do Load Balancer para fornecer escala e redundância para os dispositivos e a investigação de integridade está configurada para investigar uma porta de investigação que o dispositivo de terceiros usa como proxy ou converte em outras máquinas virtuais por trás do dispositivo.  Se você investigar a mesma porta que está usando para converter as solicitações ou usar um proxy para elas para as outras máquinas virtuais por trás do dispositivo, qualquer resposta de investigação de uma única máquina virtual por trás do dispositivo marcará o dispositivo em si como inativo. Essa configuração pode levar a uma falha em cascata de todo o cenário de aplicação como resultado de um único ponto final de backend atrás do aparelho.  O gatilho pode ser uma falha de investigação intermitente que fará com que o Load Balancer marque o destino original como inoperante (a instância do dispositivo) e, por sua vez, pode desabilitar todo o cenário de aplicativo. Em vez disso, investigue a integridade do dispositivo em si. A seleção da investigação para determinar o sinal de integridade é uma consideração importante para cenários de NVA (soluções de virtualização de rede) e é necessário consultar o fornecedor do aplicativo para descobrir qual é o sinal de integridade apropriado para esses cenários.

Se você não permitir o [IP de origem](#probesource) da investigação nas políticas de firewall, a investigação de integridade falhará, pois não poderá acessar a instância.  Por sua vez, o Load Balancer marcará para a instância como inoperante devido à falha da investigação de integridade.  Essa configuração incorreta poderá causar uma falha no cenário de aplicativo com balanceamento de carga.

Para que a investigação de integridade do Load Balancer marque a instância como operante, é **necessário** permitir esse endereço IP em todos os [grupos de segurança de rede](../virtual-network/security-overview.md) do Azure e nas políticas de firewall local.  Por padrão, cada grupo de segurança de rede inclui a [marca de serviço](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer para permitir o tráfego de investigação de integridade.

Caso deseje testar uma falha de investigação de integridade ou marcar uma instância individual como inoperante, use um [grupo de segurança de rede](../virtual-network/security-overview.md) para bloquear explicitamente a investigação de integridade (porta de destino ou [IP de origem](#probesource)) e simular a falha de uma investigação.

Não configure a VNET com o intervalo de endereços IP de propriedade da Microsoft que contém 168.63.129.16.  Essas configurações entrarão em conflito com o endereço IP da investigação de integridade e poderão resultar na falha do cenário.

Se você tiver várias interfaces na VM, será necessário certificar-se de responder à investigação na interface em que a investigação foi recebida.  Talvez seja necessário obter a conversão do endereço de rede de origem desse endereço na VM por interface.

Não habilite [carimbos de data/hora TCP](https://tools.ietf.org/html/rfc1323).  A habilitação de carimbos de tempo TCP pode fazer com que os testes de saúde falhem devido à queda de pacotes TCP pela pilha TCP do OS convidado da VM, o que resulta na marcação do Balancer de carga no respectivo ponto final.  Os carimbos de data/hora TCP são rotineiramente habilitados por padrão em imagens de VM protegidas pela segurança e precisam ser desabilitados.

## <a name="monitoring"></a>Monitoramento

Tanto o [Balancer de Carga Padrão](load-balancer-standard-overview.md) pública quanto o interno expõem por ponto final e o status do teste de saúde de backendpoint como métricas multidimensionais através do Monitor Azure. Essas métricas podem ser consumidas por outros serviços do Azure ou aplicativos parceiros. 

O Balancer de carga pública básica expõe o status do teste de saúde resumido por pool de backend através de logs do Monitor Do Azure.  Os registros do Monitor do Azure não estão disponíveis para balanceadores de carga básicos internos.  Você pode usar [os registros do Monitor do Azure](load-balancer-monitor-log.md) para verificar o estado de saúde do teste de balanceador de carga pública e a contagem de testes. O registro em log pode ser usado com o Power BI ou com o Azure Operational Insights para fornecer estatísticas sobre o status da integridade do balanceador de carga.

## <a name="limitations"></a>Limitações

- Investigações HTTPS não dão suporte à autenticação mútua com um certificado de cliente.
- Você deve assumir que os testes de saúde falharão quando os carimbos de tempo do TCP estiverem ativados.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Balanceador de Carga Padrão](load-balancer-standard-overview.md)
- [Introdução à criação de um balanceador de carga público no Gerenciador de Recursos usando PowerShell](quickstart-create-standard-load-balancer-powershell.md)
- [API REST para investigações de integridade](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Solicitar novas habilidades de investigação de integridade com [Uservoice do Load Balancer](https://aka.ms/lbuservoice)
