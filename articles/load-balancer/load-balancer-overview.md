---
title: O que é o Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Visão geral dos recursos do Balanceador de Carga do Azure, arquitetura e implementação. Saiba como o Load Balancer funciona e como aproveitá-lo na nuvem.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2019
ms.author: allensu
ms.openlocfilehash: 4f6a0a2631c3f7458548a2ff435c3e565e744f41
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660198"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Azure Load Balancer?

*Balanceamento de carga* refere-se à distribuição eficiente de carga ou tráfego de rede de entrada em um grupo de recursos ou servidores de back-end. O Azure oferece uma [variedade de opções de balanceamento de carga](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) que você pode escolher conforme suas necessidades. Este documento aborda o Azure Load Balancer.

O Azure Load Balancer opera na camada quatro do modelo OSI (Interconexão de Sistemas Abertos). É o ponto de contato único para clientes. O Load Balancer distribui novos fluxos de entrada que chegam nas instâncias de pool de front-end a back-end do Load Balancer, de acordo com as investigações de integridade e regras de balanceamento de carga especificadas. As instâncias do pool de back-end podem ser Máquinas Virtuais ou instâncias do Azure em um conjunto de dimensionamento de máquinas virtuais. 

Com o Azure Load Balancer, você pode dimensionar seus aplicativos e criar serviços com alta disponibilidade. O Load Balancer é compatível com cenários de entrada e saída, fornece baixa latência e alta taxa de transferência e pode ser escalado verticalmente em milhões de fluxos para aplicativos TCP e UDP.

Um **[Load Balancer público](#publicloadbalancer)** pode fornecer conexões de saída para VMs (máquinas virtuais) em sua rede virtual convertendo endereços IP privados em públicos. Os Load Balancers públicos são usados para o tráfego de Internet do balanceador de carga para suas VMs.

Um **[Load Balancer interno (ou privado)](#internalloadbalancer)** pode ser usado para cenários em que apenas endereços IP privados são necessários no front-end. Os balanceadores de carga internos são usados para balancear a carga do tráfego dentro de uma rede virtual. Você também pode acessar um front-end do Azure Load Balancer na rede local em um cenário híbrido.

## <a name="load-balancer-components"></a>Componentes do Load Balancer
* **Configuração do IP de front-end**: o endereço IP do Load Balancer. É o ponto de contato para clientes. Eles podem ser endereços IP públicos ou privados, criando, portanto, Balanceadores de Carga Públicos ou Internos, respectivamente.
* **Pool de back-end**: O grupo de Máquinas Virtuais ou instâncias no Conjunto de Dimensionamento de Máquinas Virtuais que atenderá à solicitação de entrada. Para dimensionar de forma econômica para atender a grandes volumes de computação de tráfego de entrada, as melhores práticas geralmente recomendam adicionar mais instâncias ao pool de back-end. O Load Balancer reconfigura-se instantaneamente por meio da reconfiguração automática quando você dimensiona as instâncias para cima ou para baixo. Adicionar ou remover VMs do pool de back-end reconfigura o Load Balancer sem operações adicionais no recurso de Load Balancer.
* **Investigações de integridade**: uma investigação de integridade é usada para determinar a integridade das instâncias no pool de back-end. Você pode definir o limite não íntegro para suas investigações de integridade. Quando uma investigação não responde, o Load Balancer interrompe o envio de novas conexões para as instâncias não íntegras. Uma falha de investigação não afeta as conexões existentes. A conexão continua até que o aplicativo encerre o fluxo, transcorra um tempo limite de ociosidade ou a VM seja desligada. O Load Balancer fornece diferentes tipos de investigação de integridade para pontos de extremidade TCP, HTTP e HTTPS. Para saber mais, confira [Tipos de investigação](load-balancer-custom-probe-overview.md#types).
* **Regras de balanceamento de carga**: As regras de balanceamento de carga dizem ao Load Balancer o que precisa ser feito e quando. 
* **Regras NAT de entrada**: Uma regra NAT de entrada encaminha o tráfego de uma porta específica de um endereço IP de front-end específico para uma porta específica de uma instância de back-end específica dentro da rede virtual. O encaminhamento de porta é realizado pela mesma distribuição baseada em hash que o balanceamento de carga. Cenários comuns dessa funcionalidade são sessões de protocolo RDP ou SSH (Secure Shell) para instâncias de VM individuais em uma Rede Virtual do Microsoft Azure. É possível mapear vários pontos de extremidade internos para portas no mesmo endereço IP de front-end. Você pode usar os endereços IP de front-end para administrar remotamente suas VMs sem um jumpbox adicional.
* **Regras de saída**: Uma regra de saída configura a NAT (Conversão de Endereços de Rede) de saída para todas as máquinas virtuais identificadas pelo pool de back-end a serem convertidas no front-end.

## <a name="load-balancer-concepts"></a>Conceitos de Load Balancer

O Azure Load Balancer fornece os seguintes recursos fundamentais para aplicativos de TCP e UDP:

* **Algoritmo de balanceamento de carga**: Com o Azure Load Balancer, é possível criar uma regra de balanceamento de carga para distribuir o tráfego que chega em um front-end para instâncias do pool de back-end. O Load Balancer usa um algoritmo baseado em hash para distribuição de fluxos de entrada e regenera os cabeçalhos dos fluxos para instâncias do pool de back-end. Um servidor fica disponível para receber novos fluxos quando a investigação de integridade indica que o ponto de extremidade de back-end é íntegro.
Por padrão, o Load Balancer usa um hash de 5 tuplas. O hash inclui endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e número do protocolo IP para mapear fluxos para servidores disponíveis. Você pode criar afinidade com um endereço IP de origem usando um hash de 2 ou 3 tuplas para uma determinada regra. Todos os pacotes do mesmo fluxo de pacote chegam na mesma instância por trás do front-end com balanceamento de carga. Quando o cliente inicia um novo fluxo do mesmo IP de origem, a porta de origem é alterada. Dessa forma, o hash de 5 tuplas podem fazer com que o tráfego vá para um ponto de extremidade de back-end diferente.
Para saber mais, confira [Configurar o modo de distribuição para o Azure Load Balancer](load-balancer-distribution-mode.md). A imagem a seguir mostra a distribuição baseada em hash:

  ![Distribuição baseada em hash](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: distribuição baseada em hash*

* **Independência e transparência de aplicativo**: O Load Balancer não interage diretamente com o TCP ou UDP, nem com a camada de aplicativo. Qualquer cenário de aplicativo TCP ou UDP pode ser compatível. O Load Balancer não encerra nem origina fluxos, não interage com o conteúdo do fluxo nem fornece qualquer função de gateway de camada de aplicativo. Os handshakes de protocolo sempre ocorrem diretamente entre o cliente e a instância do pool de back-end. Uma resposta para um fluxo de entrada sempre é uma resposta de uma máquina virtual. Quando o fluxo chega na máquina virtual, o endereço IP de origem original também é preservado.
  * Cada ponto de extremidade é respondido somente por uma VM. Por exemplo, um handshake TCP sempre ocorre entre o cliente e a VM de back-end selecionada. Uma resposta a uma solicitação feita a um front-end é uma resposta gerada por uma VM de back-end. Ao validar com êxito a conectividade com um front-end, você está validando a conectividade de ponta a ponta com pelo menos uma máquina virtual de back-end.
  * O conteúdo do aplicativo é transparente para o Load Balancer. Qualquer aplicativo UDP ou TCP pode ser compatível.
  * Como Load Balancer não interage com o conteúdo de TCP e fornece o descarregamento de TLS, você pode criar cenários criptografados de ponta a ponta. O uso do Load Balancer ganha grande expansão para aplicativos TLS encerrando a conexão TLS na VM em si. Por exemplo, a capacidade de chaveamento de sua sessão TLS é limitada apenas pelo tipo e pelo número de VMs adicionadas ao pool de back-end.

* **Conexões de saída (SNAT)** : Todos os fluxos de saída dos endereços IP privados em sua rede virtual para endereços IP públicos na Internet podem ser movidos para um endereço IP de front-end do Load Balancer. Quando um front-end público é vinculado a uma VM de back-end por meio de uma regra de balanceamento de carga, o Azure move conexões de saída para o endereço IP de front-end público. Essa configuração tem as seguintes vantagens:
  * Facilidade de upgrade e recuperação de desastre dos serviços, uma vez que o front-end pode ser mapeado dinamicamente para outra instância do serviço.
  * Gerenciamento mais fácil da ACL (lista de controle de acesso). As ACLs expressas como IPs de front-end não são alteradas à medida que os serviços são escalados verticalmente para cima ou para baixo, ou quando são reimplantados. Mover conexões de saída para um número menor de endereços IP do que computadores reduz a carga de implementar listas seguras de destinatários.
Para saber mais, confira [Conexões de saída no Azure](load-balancer-outbound-connections.md).
O Standard Load Balancer tem funcionalidades adicionais específicas do SKU além dessas básicas, conforme descrito abaixo.

## <a name="skus"></a> Comparação de SKU do Azure Load Balancer

O Load Balancer dá suporte a SKUs Basic e Standard. Há diferenças de SKU na escala, nos recursos e no preço do cenário. Qualquer cenário possível com o Basic Load Balancer pode ser criado com o Standard Load Balancer. As APIs para ambos os SKUs são semelhantes e invocadas pela especificação de um SKU. A API para dar suporte a SKUs do Load Balancer e ao IP público está disponível começando pela API `2017-08-01`. Ambos os SKUs compartilham a mesma API geral e estrutura.

A configuração do cenário completo pode ser um pouco diferente dependendo do SKU. A documentação do Load Balancer indica quando um artigo se aplica somente a um SKU específico. Veja a tabela a seguir para comparar e entender as diferenças. Para saber mais, confira [Visão geral do Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft recomenda o Standard Load Balancer.
VMs autônomas, conjuntos de disponibilidade e conjuntos de dimensionamento de máquina virtual podem estar conectados apenas a um SKU, nunca a ambos. Os SKUs do Load Balancer e do endereço IP público devem ser correspondentes quando você usá-los com endereços IP públicos. Os SKUs do Load Balancer e do IP público não são mutáveis.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para saber mais, confira [Limites do balanceador de carga](https://aka.ms/lblimits). Para obter detalhes sobre o Load Balancer Standard, confira [visão geral](load-balancer-standard-overview.md), [preços](https://aka.ms/lbpricing) e [SLA](https://aka.ms/lbsla).

## <a name="load-balancer-types"></a>Tipos de Load Balancer

### <a name = "publicloadbalancer"></a>Azure Load Balancer Público

O Load Balancer público mapeia o endereço IP público e a porta do tráfego de entrada para o endereço IP privado e a porta da VM. O Load Balancer mapeia o tráfego ao contrário para o tráfego de resposta da VM. Você pode distribuir tipos específicos de tráfego entre vários serviços ou VMs aplicando regras de balanceamento de carga. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web.

>[!NOTE]
>É possível implementar apenas um Load Balancer público e um Load Balancer interno por conjunto de disponibilidade.

A figura a seguir mostra um ponto de extremidade com balanceamento de carga para tráfego da Web que é compartilhado entre três VMs para a porta TCP 80 pública. Essas três VMs estão em um conjunto com balanceamento de carga.

![Exemplo de Load Balancer público](./media/load-balancer-overview/IC727496.png)

*Figura: Balanceando o tráfego da Web usando um Load Balancer público*

Os clientes da Internet enviam solicitações da página da Web para o endereço IP público de um aplicativo Web na porta TCP 80. O Azure Load Balancer distribui as solicitações entre as três VMs no conjunto com balanceamento de carga. Para obter mais informações sobre os algoritmos do balanceador de carga, confira os [Conceitos de Load Balancer](load-balancer-overview.md##load-balancer-concepts).

O Azure Load Balancer distribui o tráfego igualmente entre várias instâncias da VM por padrão. Você também pode configurar a afinidade de sessão. Para saber mais, confira [Configurar o modo de distribuição para o Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a>Azure Load Balancer Interno

Um Load Balancer interno direciona o tráfego apenas para recursos que estão em uma rede virtual ou que usam uma VPN para acessar a infraestrutura do Azure, ao contrário de um Load Balancer público. A infraestrutura do Azure restringe o acesso aos endereços IP de front-end de uma rede virtual que têm balanceamento de carga. Os endereços IP de front-end e as redes virtuais nunca são expostos diretamente a um ponto de extremidade da Internet. Os aplicativos de linha de negócios interno são executados no Azure e acessados de dentro do Azure ou de recursos locais.

Um Load Balancer interno habilita os seguintes tipos de balanceamento de carga:

* **Dentro de uma rede virtual**: balanceamento de carga de VMs na rede virtual para um conjunto de VMs dentro da mesma rede virtual.
* **Para uma rede virtual entre locais**: balanceamento de carga de computadores locais para um conjunto de VMs dentro da mesma rede virtual.
* **Para aplicativos de várias camadas**: balanceamento de carga para aplicativos de várias camadas voltados para a Internet em que as camadas de back-end não são voltadas para a Internet. As camadas de back-end requerem balanceamento de carga de tráfego da camada para a Internet. Veja a próxima figura.
* **Para aplicativos de linha de negócios**: Balanceamento de carga para aplicativos de linha de negócios hospedados no Azure sem hardware ou software do balanceador de carga adicional. Esse cenário inclui servidores locais que estão no conjunto de computadores cujo tráfego tem balanceamento de carga.

![Exemplo de Load Balancer Interno](./media/load-balancer-overview/IC744147.png)

*Figura: Balanceando aplicativos de várias camadas usando o Load Balancer interno e público*

## <a name="pricing"></a>Preços

O uso do Standard Load Balancer é cobrado.

* Número de regras de saída e balanceamento de carga configuradas. As regras NAT de entrada não são contadas no número total de regras.
* A quantidade de dados processados na entrada e na saída não depende das regras.

Para obter informações sobre o preço do Standard Load Balancer, confira [Preço do Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

O Azure Load Balancer Basic é oferecido gratuitamente.

## <a name="sla"></a>Contrato de Nível de Serviço

Para obter informações sobre o SLA do Standard Load Balancer, confira [SLA do Load Balancer](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitações

* O Load Balancer fornece balanceamento de carga e encaminhamento de porta para protocolos TCP e UDP específicos. As regras de balanceamento de carga e as regras NAT de entrada são compatíveis com TCP e UDP, mas não com outros protocolos IP, incluindo ICMP.

  O Load Balancer não encerra, não responde nem interage com o conteúdo de um fluxo UDP ou TCP. Ele não é um proxy. A validação bem-sucedida da conectividade com um front-end deve ocorrer lado a lado com o mesmo protocolo usado em um balanceamento de carga ou em uma regra NAT de entrada. Pelo menos uma de suas máquinas virtuais deve gerar uma resposta para que um cliente veja uma resposta de um front-end.

  Não receber uma resposta do front-end do Load Balancer indica que nenhuma máquina virtual foi capaz de responder. Nada pode interagir com um front-end do Load Balancer sem que uma máquina virtual possa responder. Esse princípio também se aplica a conexões de saída em que o SNAT de representação de porta é compatível apenas com TCP e UDP. Todos os outros protocolos IP, incluindo ICMP, falham. Atribua um endereço IP público no nível da instância para atenuar esse problema. Para saber mais, confira [Reconhecimento de SNAT e PAT](load-balancer-outbound-connections.md#snat).

* Os Load Balancers internos não movem conexões originadas pela saída para o front-end de um Load Balancer interno porque ambos estão no espaço de endereços IP privado. Os Load Balancers fornecem [conexões de saída](load-balancer-outbound-connections.md) de endereços IP privados dentro da rede virtual para os endereços IP públicos. Para Load Balancers internos, essa abordagem evita possível esgotamento da porta SNAT em um espaço de endereços IP interno único, onde a movimentação não é obrigatória.

  O efeito colateral é que, se um fluxo de saída de uma VM no pool de back-end tentar estabelecer um fluxo para o front-end do Load Balancer interno em seu pool _e_ for mapeado de volta para si mesmo, os dois lados do fluxo não serão correspondentes. Como eles não correspondem, o fluxo falha. O fluxo será bem-sucedido se não for mapeado de volta para a mesma VM no pool de back-end que criou o fluxo para o front-end.

  Quando o fluxo é mapeado de volta para si mesmo, o fluxo de saída parece ser originado da VM para o front-end e o fluxo de entrada correspondente parece ser originado da VM para si mesmo. Do ponto de vista do sistema operacional convidado, as partes de entrada e de saída do mesmo fluxo não são correspondentes dentro da máquina virtual. A pilha TCP não reconhece essas metades do mesmo fluxo como parte do mesmo fluxo. A origem e o destino não correspondem. Quando o fluxo é mapeado para qualquer outra VM no pool de back-end, as metades do fluxo são correspondentes e a VM pode responder ao fluxo.

  O sintoma desse cenário é que a conexão intermitente se esgota quando o fluxo retorna para o mesmo back-end que originou o fluxo. Soluções alternativas comuns incluem a inserção de uma camada proxy por trás do Load Balancer interno e o uso de regras de estilo DSR (Retorno de Servidor Direto). Para saber mais, confira [Vários front-ends para o Azure Load Balancer](load-balancer-multivip-overview.md).

  É possível combinar um Load Balancer interno com qualquer proxy de terceiros ou usar o [Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md) interno para cenários de proxy com HTTP/HTTPS. Embora você possa usar um Load Balancer público para atenuar esse problema, o cenário resultante estará propenso ao [esgotamento de SNAT](load-balancer-outbound-connections.md#snat). Evite essa segunda abordagem, a menos que seja cuidadosamente gerenciada.

* Em geral, o encaminhamento de fragmentos de IP não é permitido nas regras de balanceamento de carga. A fragmentação de IP de pacotes UDP e TCP não é permitida em regras de balanceamento de carga. As regras de balanceamento de carga das portas de alta disponibilidade podem ser usadas para encaminhar fragmentos IP existentes. Para saber mais, confira [Visão geral das portas de alta disponibilidade](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Próximas etapas

Confira [Criar um Standard Load Balancer público](quickstart-load-balancer-standard-public-portal.md) para começar a usar um Load Balancer: crie VMs com uma extensão de IIS personalizada instalada e faça o balanceamento de carga do aplicativo Web entre as VMs.
