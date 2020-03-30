---
title: Guia de desempenho para Serviço do Azure SignalR
description: Uma visão geral do desempenho e do benchmark do Azure SignalR Service. Principais métricas a serem consideradas ao planejar a capacidade.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157675"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho para Serviço do Azure SignalR

Um dos principais benefícios do uso do Azure SignalR Service é a facilidade de dimensionar aplicativos SignalR. Em um cenário em larga escala, o desempenho é um fator importante. 

Neste guia, apresentaremos os fatores que afetam o desempenho do aplicativo SignalR. Descreveremos o desempenho típico em diferentes cenários de casos de uso. No final, apresentaremos o ambiente e as ferramentas que você pode usar para gerar um relatório de desempenho.

## <a name="term-definitions"></a>Definições de termos

*Entrada*: A mensagem recebida para o Serviço de SignalR do Azure.

*Saída*: A mensagem de saída do Azure SignalR Service.

*Largura de banda*: O tamanho total de todas as mensagens em 1 segundo.

*Modo padrão*: O modo de trabalho padrão quando uma ocorrência de serviço do Azure SignalR foi criada. O Azure SignalR Service espera que o servidor do aplicativo estabeleça uma conexão com ele antes de aceitar quaisquer conexões com o cliente.

*Modo sem servidor*: Um modo no qual o Azure SignalR Service aceita apenas conexões com clientes. Nenhuma conexão de servidor é permitida.

## <a name="overview"></a>Visão geral

O Azure SignalR Service define sete níveis Padrão para diferentes capacidades de desempenho. Este guia responde às seguintes perguntas:

-   Qual é o desempenho típico do Serviço Azure SignalR para cada nível?

-   O Azure SignalR Service atende aos meus requisitos de throughput de mensagens (por exemplo, enviar 100.000 mensagens por segundo)?

-   Para o meu cenário específico, qual nível é adequado para mim? Ou como posso selecionar o nível adequado?

-   Que tipo de servidor de aplicativo (tamanho VM) é adequado para mim? Quantos deles devo implantar?

Para responder a essas perguntas, este guia primeiro dá uma explicação de alto nível dos fatores que afetam o desempenho. Em seguida, ilustra as mensagens máximas de entrada e saída para cada nível para casos típicos de uso: **eco**, **transmissão,** **envio para grupo**e envio para **conexão** (conversa entre pares).

Este guia não pode cobrir todos os cenários (e diferentes casos de uso, tamanhos de mensagens, padrões de envio de mensagens e assim por diante). Mas ele fornece alguns métodos para ajudá-lo:

- Avalie sua exigência aproximada para as mensagens de entrada ou de saída.
- Encontre as camadas adequadas verificando a tabela de desempenho.

## <a name="performance-insight"></a>Insight de desempenho

Esta seção descreve as metodologias de avaliação de desempenho e, em seguida, lista todos os fatores que afetam o desempenho. No final, ele fornece métodos para ajudá-lo a avaliar os requisitos de desempenho.

### <a name="methodology"></a>Metodologia

*Rendimento* e *latência* são dois aspectos típicos da verificação de desempenho. Para o Azure SignalR Service, cada nível SKU tem sua própria política de estrangulamento de throughput. A diretiva define *o throughput máximo permitido (largura de banda de entrada e saída)* como o throughput máximo alcançado quando 99% das mensagens têm latência inferior a 1 segundo.

Latência é o período de tempo desde a conexão que envia a mensagem até o recebimento da mensagem de resposta do Azure SignalR Service. Vamos tomar **o echo** como um exemplo. Cada conexão com o cliente adiciona um carimbo de hora na mensagem. O hub do servidor de aplicativos envia a mensagem original de volta ao cliente. Assim, o atraso de propagação é facilmente calculado por cada conexão do cliente. O carimbo de hora é anexado para cada mensagem na **transmissão,** **enviar para o grupo**e enviar para **conexão**.

Para simular milhares de conexões simultâneas de clientes, várias VMs são criadas em uma rede virtual privada no Azure. Todas essas VMs se conectam à mesma instância do Azure SignalR Service.

No modo padrão do Azure SignalR Service, as VMs do servidor de aplicativos são implantadas na mesma rede privada virtual que as VMs clientes. Todas as VMs cliente saem na mesma rede da mesma região para evitar latência entre regiões.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, a capacidade de serviço do Azure SignalR é limitada por recursos de computação: CPU, memória e rede. Por exemplo, mais conexões ao Azure SignalR Service fazem com que o serviço use mais memória. Para um tráfego maior de mensagens (por exemplo, cada mensagem é maior que 2.048 bytes), o Azure SignalR Service precisa gastar mais ciclos de CPU para processar o tráfego. Enquanto isso, a largura de banda da rede Azure também impõe um limite para o tráfego máximo.

O tipo de transporte é outro fator que afeta o desempenho. Os três tipos são [WebSocket,](https://en.wikipedia.org/wiki/WebSocket) [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)e [Long-Polling.](https://en.wikipedia.org/wiki/Push_technology) 

WebSocket é um protocolo de comunicação bidirecional e full-duplex sobre uma única conexão TCP. Server-Sent-Event é um protocolo unidirecional para enviar mensagens de servidor para cliente. A pesquisa longa exige que os clientes pesquisem periodicamente as informações do servidor através de uma solicitação HTTP. Para a mesma API as mesmas condições, o WebSocket tem o melhor desempenho, o Server-Sent-Event é mais lento e o Long-Polling é o mais lento. O Azure SignalR Service recomenda o WebSocket por padrão.

O custo de roteamento de mensagens também limita o desempenho. O Azure SignalR Service desempenha um papel como roteador de mensagens, que encaminha a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Um cenário diferente ou API requer uma política de roteamento diferente. 

Para **eco,** o cliente envia uma mensagem para si mesmo, e o destino de roteamento também é ele mesmo. Este padrão tem o menor custo de roteamento. Mas para **transmitir**, **enviar para o grupo**e enviar para **conexão,** o Azure SignalR Service precisa procurar as conexões de destino através da estrutura interna de dados distribuídos. Esse processamento extra usa mais largura de banda de CPU, memória e rede. Como resultado, o desempenho é mais lento.

No modo padrão, o servidor do aplicativo também pode se tornar um gargalo para determinados cenários. O Azure SignalR SDK tem que invocar o hub, enquanto mantém uma conexão ao vivo com cada cliente através de sinais de batimentos cardíacos.

No modo sem servidor, o cliente envia uma mensagem por post HTTP, que não é tão eficiente quanto o WebSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack é menor em tamanho e entregue mais rápido que JSON. O MessagePack pode não melhorar o desempenho. O desempenho do Azure SignalR Service não é sensível aos protocolos porque não decodifica a carga de mensagens durante o encaminhamento de mensagens dos clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores afetam a capacidade de entrada e saída:

-   Nível SKU (CPU/memória)

-   Número de conexões

-   Tamanho da mensagem

-   Taxa de envio de mensagens

-   Tipo de transporte (WebSocket, Server-Sent-Event ou Long-Polling)

-   Cenário do caso de uso (custo de roteamento)

-   Conexões de servidor de aplicativos e serviços (no modo servidor)


### <a name="finding-a-proper-sku"></a>Encontrar um SKU adequado

Como você pode avaliar a capacidade de entrada/saída ou descobrir qual nível é adequado para um caso de uso específico?

Suponha que o servidor de aplicativos seja poderoso o suficiente e não seja o gargalo de desempenho. Em seguida, verifique a largura de banda máxima de entrada e saída para cada camada.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar a avaliação primeiro, assumindo algumas configurações padrão: 

- O tipo de transporte é WebSocket.
- O tamanho da mensagem é de 2.048 bytes.
- Uma mensagem é enviada a cada 1 segundo.
- O Azure SignalR Service está no modo padrão.

Cada camada tem sua própria largura de banda de entrada máxima e largura de banda de saída. Uma experiência de usuário suave não é garantida depois que a conexão de entrada ou saída exceder o limite.

**O Echo** dá a largura de banda máxima de entrada porque tem o menor custo de roteamento. **O Broadcast** define a largura de banda máxima da mensagem de saída.

*Não* exceda os valores destacados nas duas tabelas seguintes.

|       Echo                        | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| **Largura de banda de entrada** | **2 Mbps**    | **4 Mbps**    | **10 Mbps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Largura de banda de saída | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Broadcast             | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2.000 | 5.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Largura de banda de saída** | **4 Mbps**    | **8 Mbps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Largura de banda de entrada* e largura de banda de *saída* são o tamanho total da mensagem por segundo.  Aqui estão as fórmulas para eles:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *conexões de entrada*: O número de conexões que enviam a mensagem.

- *idaConexões*: O número de conexões que recebem a mensagem.

- *mensagemTamanho*: O tamanho de uma única mensagem (valor médio). Uma pequena mensagem com menos de 1.024 bytes tem um impacto de desempenho semelhante a uma mensagem de 1.024 bytes.

- *sendInterval*: O tempo de envio de uma mensagem. Normalmente é 1 segundo por mensagem, o que significa enviar uma mensagem a cada segundo. Um intervalo menor significa enviar mais mensagem em um período de tempo. Por exemplo, 0,5 segundos por mensagem significa enviar duas mensagens a cada segundo.

- *Conexões*: O limite máximo comprometido para o Azure SignalR Service para cada nível. Se o número de conexão for aumentado ainda mais, ele sofrerá de estrangulamento da conexão.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos complexos de uso

##### <a name="bigger-message-size-or-different-sending-rate"></a>Maior tamanho da mensagem ou taxa de envio diferente

O caso de uso real é mais complicado. Ele pode enviar uma mensagem maior que 2.048 bytes, ou a taxa de envio de mensagens não é uma mensagem por segundo. Vamos tomar a transmissão do Unit100 como um exemplo para descobrir como avaliar seu desempenho.

A tabela a seguir mostra um caso real de uso da **transmissão**. Mas o tamanho da mensagem, a contagem de conexões e a taxa de envio de mensagens são diferentes do que assumimos na seção anterior. A questão é como podemos deduzir qualquer um desses itens (tamanho da mensagem, contagem de conexão ou taxa de envio de mensagens) se soubermos apenas dois deles.

| Broadcast  | Tamanho da mensagem | Mensagens de entrada por segundo | conexões | Enviar intervalos |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 segundos                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 segundos                      |

A seguinte fórmula é fácil de inferir com base na fórmula anterior:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para a Unit100, a largura de banda máxima de saída é de 400 MB da tabela anterior. Para um tamanho de mensagem de 20 KB, as \* conexões máximas de saída devem ser de 400 MB 5 / 20 KB = 100.000, que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de uso real tipicamente mistura os quatro casos básicos de uso: **eco**, **transmissão,** **envio para grupo**e envio para **conexão**. A metodologia que você usa para avaliar a capacidade é:

1. Divida os casos de uso misto em quatro casos de uso básico.
1. Calcule a largura de banda máxima de entrada e saída da mensagem usando as fórmulas anteriores separadamente.
1. Soque os cálculos de largura de banda para obter a largura de banda máxima total de entrada/saída. 

Em seguida, pegue o nível apropriado nas tabelas máximas de largura de banda de entrada/saída.

> [!NOTE]
> Para enviar uma mensagem para centenas ou milhares de pequenos grupos, ou para milhares de clientes que enviam uma mensagem uns aos outros, o custo de roteamento se tornará dominante. Leve esse impacto em conta.

Para o caso de uso do envio de uma mensagem aos clientes, certifique-se de que o servidor do aplicativo *não* é o gargalo. A seção "Estudo de caso" a seguir fornece orientações sobre quantos servidores de aplicativos você precisa e quantas conexões de servidor você deve configurar.

## <a name="case-study"></a>Estudo de caso

As seguintes seções passam por quatro casos típicos de uso para transporte de WebSocket: **echo**, **broadcast**, send **to group**e send **to connection**. Para cada cenário, a seção lista a capacidade atual de entrada e saída do Azure SignalR Service. Também explica os principais fatores que afetam o desempenho.

No modo padrão, o servidor do aplicativo cria cinco conexões de servidor com o Azure SignalR Service. O servidor de aplicativos usa o Azure SignalR Service SDK por padrão. Nos resultados seguintes do teste de desempenho, as conexões do servidor são aumentadas para 15 (ou mais para transmitir e enviar uma mensagem para um grande grupo).

Diferentes casos de uso têm requisitos diferentes para servidores de aplicativos. **O Broadcast** precisa de um pequeno número de servidores de aplicativos. **Echo** ou **enviar para conexão** precisa de muitos servidores de aplicativos.

Em todos os casos de uso, o tamanho da mensagem padrão é de 2.048 bytes, e o intervalo de envio de mensagens é de 1 segundo.

### <a name="default-mode"></a>Modo padrão

Clientes, servidores de aplicativos web e o Azure SignalR Service estão envolvidos no modo padrão. Cada cliente representa uma única conexão.

#### <a name="echo"></a>Echo

Primeiro, um aplicativo web se conecta ao Azure SignalR Service. Em segundo lugar, muitos clientes se conectam ao aplicativo web, que redireciona os clientes para o Azure SignalR Service com o token de acesso e o endpoint. Em seguida, os clientes estabelecem conexões WebSocket com o Azure SignalR Service.

Depois que todos os clientes estabelecem conexões, eles começam a enviar uma mensagem que contém um carimbo de tempo para o hub específico a cada segundo. O hub ecoa a mensagem de volta ao seu cliente original. Cada cliente calcula a latência quando recebe a mensagem de eco de volta.

No diagrama a seguir, 5 a 8 (tráfego vermelho destacado) estão em um loop. O loop é executado por uma duração padrão (5 minutos) e obtém a estatística de toda a latência da mensagem.

![Tráfego para o caso de uso de eco](./media/signalr-concept-performance/echo.png)

O comportamento do **eco** determina que a largura de banda máxima de entrada é igual à largura de banda máxima de saída. Para obter detalhes, consulte a tabela a seguir.

|       Echo                        | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Largura de banda de entrada/saída | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Neste caso de uso, cada cliente invoca o hub definido no servidor do aplicativo. O hub apenas chama o método definido no lado cliente original. Este hub é o hub mais leve para **eco**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para este simples hub, a pressão de tráfego no servidor do aplicativo é proeminente à medida que a carga de mensagens de entrada **do eco** aumenta. Essa pressão de tráfego requer muitos servidores de aplicativos para grandes níveis SKU. A tabela a seguir lista a contagem do servidor do aplicativo para cada nível.


|    Echo          | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o nível SKU e a CPU/memória do servidor do aplicativo afetam o desempenho geral do **eco**.

#### <a name="broadcast"></a>Broadcast

Para **transmissão,** quando o aplicativo web recebe a mensagem, ele transmite para todos os clientes. Quanto mais clientes houver para transmitir, mais tráfego de mensagens há para todos os clientes. Confira o diagrama a seguir.

![Tráfego para o caso de uso de transmissão](./media/signalr-concept-performance/broadcast.png)

Um pequeno número de clientes estão transmitindo. A largura de banda da mensagem de entrada é pequena, mas a largura de banda de saída é enorme. A largura de banda de mensagem de saída aumenta à medida que a conexão do cliente ou a taxa de transmissão aumenta.

A tabela a seguir resume as conexões máximas do cliente, a contagem de mensagens de entrada/saída e a largura de banda.

|     Broadcast             | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2.000 | 5.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 20,000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Largura de banda de saída | 4 Mbps   | 8 Mbps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

Os clientes que postam mensagens não têm mais do que quatro. Eles precisam de menos servidores de aplicativos em comparação com o **echo** porque a quantidade de mensagem de entrada é pequena. Dois servidores de aplicativos são suficientes tanto para sla quanto para considerações de desempenho. Mas você deve aumentar as conexões padrão do servidor para evitar desequilíbrio, especialmente para a Unidade50 e unidade100.

|   Broadcast      | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as conexões padrão do servidor de 5 para 40 em cada servidor de aplicativo para evitar possíveis conexões desequilibradas do servidor ao Azure SignalR Service.
>
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens e o nível SKU afetam o desempenho geral da **transmissão**.

#### <a name="send-to-group"></a>Enviar para o grupo

O caso de envio **para uso em grupo** tem um padrão de tráfego semelhante para **transmitir**. A diferença é que, depois que os clientes estabelecerem conexões WebSocket com o Azure SignalR Service, eles devem se juntar a grupos antes que eles possam enviar uma mensagem para um grupo específico. O diagrama a seguir ilustra o fluxo de tráfego.

![Tráfego para o caso de uso de envio para grupo](./media/signalr-concept-performance/sendtogroup.png)

A contagem de membros do grupo e do grupo são dois fatores que afetam o desempenho. Para simplificar a análise, definimos dois tipos de grupos:

- **Grupo pequeno**: Cada grupo tem 10 conexões. O número do grupo é igual a (contagem máxima de conexão) / 10. Por exemplo, para a Unidade1, se houver 1.000 contagens de conexão, então temos 1000 / 10 = 100 grupos.

- **Grupo grande**: O número do grupo é sempre 10. A contagem de membros do grupo é igual a (contagem máxima de conexão) / 10. Por exemplo, para a Unidade1, se houver 1.000 contagens de conexão, então cada grupo tem 1000 / 10 = 100 membros.

**O envio para o grupo** traz um custo de roteamento para o Azure SignalR Service porque ele tem que encontrar as conexões de destino através de uma estrutura de dados distribuída. À medida que as conexões de envio aumentam, o custo aumenta.

##### <a name="small-group"></a>Pequeno grupo

O custo de roteamento é significativo para enviar mensagem para muitos pequenos grupos. Atualmente, a implementação do Azure SignalR Service atinge o limite de custo de roteamento na Unit50. Adicionar mais CPU e memória não ajuda, então o Unit100 não pode melhorar ainda mais pelo design. Se você precisar de mais largura de banda de entrada, entre em contato com o suporte ao cliente.

|   Enviar para um pequeno grupo     | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| conexões               | 1,000 | 2.000 | 5.000  | 10.000 | 20,000 | 50.000 | 100.000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupos               | 100   | 200   | 500    | 1,000  | 2.000  | 5.000  | 10.000 
| Mensagens de entrada por segundo  | 200   | 400   | 1,000  | 2.500  | 4.000  | 7.000  | 7.000   |
| Largura de banda de entrada  | 400 KBps  | 800 KBps  | 2 Mbps     | 5 Mbps     | 8 Mbps     | 14 MBps    | 14 MBps     |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Largura de banda de saída | 4 Mbps    | 8 Mbps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Muitas conexões com clientes estão chamando o hub, então o número do servidor do aplicativo também é fundamental para o desempenho. A tabela a seguir lista as contagens sugeridas do servidor de aplicativos.

|  Enviar para um pequeno grupo   | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de roteamento, o nível SKU e a CPU/memória do servidor do aplicativo afetam o desempenho geral do **envio para um pequeno grupo**.

##### <a name="big-group"></a>Grupo grande

Para **enviar para o grupo grande,** a largura de banda de saída torna-se o gargalo antes de atingir o limite de custo de roteamento. A tabela a seguir lista a largura de banda máxima de saída, que é quase a mesma da **transmissão**.

|    Enviar para o grande grupo      | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2.000 | 5.000  | 10.000 | 20,000 | 50.000  | 100.000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1,000  | 2.000  | 5.000   | 10.000 
| Contagem de grupos               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Mensagens de entrada por segundo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda de entrada  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 20,000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de saída | 8 Mbps    | 8 Mbps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A contagem de conexões de envio não é mais do que 40. A carga no servidor do aplicativo é pequena, então o número sugerido de aplicativos web é pequeno.

|  Enviar para o grande grupo  | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as conexões padrão do servidor de 5 para 40 em cada servidor de aplicativo para evitar possíveis conexões desequilibradas do servidor ao Azure SignalR Service.
> 
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de roteamento e o nível SKU afetam o desempenho geral do **envio para o grupo grande**.

#### <a name="send-to-connection"></a>Enviar para conexão

No caso de envio para uso **de conexão,** quando os clientes estabelecem as conexões ao Azure SignalR Service, cada cliente chama um hub especial para obter seu próprio ID de conexão. O benchmark de desempenho coleta todos os IDs de conexão, embaralha-os e os reatribui a todos os clientes como um alvo de envio. Os clientes continuam enviando a mensagem para a conexão de destino até que o teste de desempenho termine.

![Tráfego para o caso de uso de envio para cliente](./media/signalr-concept-performance/sendtoclient.png)

O custo de roteamento para **envio à conexão** é semelhante ao custo para **envio para pequeno grupo**.

À medida que a contagem de conexões aumenta, o custo de roteamento limita o desempenho geral. A Unidade50 chegou ao limite. Como resultado, a Unidade100 não pode melhorar mais.

A tabela a seguir é um resumo estatístico depois de muitas rodadas de execução do **send to connection** benchmark.

|   Enviar para conexão   | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50          | Unidade100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| conexões                        | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000          | 100.000         |
| Mensagens de entrada/saída por segundo | 1,000 | 2.000 | 5.000 | 8,000  | 9.000  | 20,000 | 20,000 |
| Largura de banda de entrada/saída | 2 Mbps    | 4 Mbps    | 10 Mbps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Este estojo de uso requer alta carga no lado do servidor do aplicativo. Veja a contagem sugerida do servidor de aplicativos na tabela a seguir.

|  Enviar para conexão  | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de roteamento, o nível SKU e a CPU/memória para o servidor do aplicativo afetam o desempenho geral do **envio à conexão**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR eco, transmitir e enviar para pequeno grupo

O Azure SignalR Service fornece a mesma capacidade de desempenho para ASP.NET SignalR. 

O teste de desempenho usa aplicativos Web Azure do [Plano de Serviço Padrão S3](https://azure.microsoft.com/pricing/details/app-service/windows/) para ASP.NET SignalR.

A tabela a seguir fornece a contagem sugerida do aplicativo web para ASP.NET **eco**SignalR .

|   Echo           | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

A tabela a seguir fornece a contagem sugerida do aplicativo web para ASP.NET **transmissão**SignalR .

|  Broadcast       | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

A tabela a seguir fornece a contagem sugerida do aplicativo web para ASP.NET SignalR **enviar para um pequeno grupo**.

|  Enviar para um pequeno grupo     | Unidade1 | Unidade2 | Unidade5 | Unidade10 | Unidade20 | Unidade50 | Unidade100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2.000 | 5.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem de servidores de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modo sem servidor

Os clientes e o Azure SignalR Service estão envolvidos no modo sem servidor. Cada cliente representa uma única conexão. O cliente envia mensagens através da API REST para outro cliente ou mensagens de transmissão para todos.

O envio de mensagens de alta densidade através da API REST não é tão eficiente quanto o uso do WebSocket. Ele exige que você construa uma nova conexão HTTP todas as vezes, e isso é um custo extra no modo sem servidor.

#### <a name="broadcast-through-rest-api"></a>Transmissão através da API REST
Todos os clientes estabelecem conexões WebSocket com o Azure SignalR Service. Em seguida, alguns clientes começam a transmitir através da API REST. O envio de mensagens (entrada) é tudo através do HTTP Post, que não é eficiente em comparação com o WebSocket.

|   Transmissão através da API REST     | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2.000 | 5.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 20,000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de entrada  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Largura de banda de saída | 4 Mbps    | 8 Mbps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Enviar ao usuário através da API REST
O benchmark atribui nomes de usuário a todos os clientes antes que eles comecem a se conectar ao Azure SignalR Service. Depois que os clientes estabelecem conexões WebSocket com o Azure SignalR Service, eles começam a enviar mensagens para outros através do HTTP Post.

|   Enviar ao usuário através da API REST | Unidade1 | Unidade2 | Unidade5  | Unidade10 | Unidade20 | Unidade50  | Unidade100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2.000 | 5.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18.000  |
| Mensagens de saída por segundo | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18.000 |
| Largura de banda de entrada  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |
| Largura de banda de saída | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

Para todos os casos de uso listados anteriormente, realizamos os testes de desempenho em um ambiente Azure. No máximo, usamos 50 VMs clientes e 20 VMs de servidor de aplicativos. Aqui estão alguns detalhes:

- Tamanho do cliente VM: StandardDS2V2 (2 vCPU, memória 7G)

- Tamanho vM do servidor de aplicativos: StandardF4sV2 (4 vCPU, memória 8G)

- Conexões do servidor Azure SignalR SDK: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

Você pode encontrar ferramentas de desempenho para o Azure SignalR Service no [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você tem uma visão geral do desempenho do Azure SignalR Service em cenários típicos de casos de uso.

Para obter detalhes sobre os internos do serviço e dimensionamento para ele, leia os seguintes guias:

* [Componentes internos do Serviço do Azure SignalR](signalr-concept-internals.md)
* [Dimensionamento do serviço Azure SignalR](signalr-howto-scale-multi-instances.md)
