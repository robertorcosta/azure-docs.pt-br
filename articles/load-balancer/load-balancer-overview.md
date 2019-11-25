---
title: O que é o Azure Load Balancer?
titlesuffix: Azure Load Balancer
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
ms.date: 11/08/2019
ms.author: allensu
ms.openlocfilehash: be293a925e507468d96be4c9f6b47f30eea5f025
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888703"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Azure Load Balancer?

Com o Azure Load Balancer, você pode dimensionar seus aplicativos e criar alta disponibilidade para seus serviços. O Load Balancer é compatível com cenários de entrada e saída, fornece baixa latência e alta taxa de produtividade e pode ser escalado verticalmente em milhões de fluxos para aplicativos TCP e UDP.

O Load Balancer distribui novos fluxos de entrada que chegam nas instâncias de pool de front-end a back-end do Load Balancer, de acordo com as investigações de integridade e regras especificadas.

Um Load Balancer público pode fornecer conexões de saída para VMs (máquinas virtuais) em sua rede virtual convertendo endereços IP privados em públicos.

O Azure Load Balancer está disponível em dois tipos de preço ou *SKUs*: Básico e Standard. Há diferenças em dimensionamento, recursos e preço. Qualquer cenário possível com o Basic Load Balancer também pode ser criado com o Standard Load Balancer, embora as abordagens sejam um pouco diferentes. À medida que você aprende sobre o Load Balancer, familiarize-se com os conceitos básicos e as diferenças específicas de SKU.

## <a name="why-use-load-balancer"></a>Por que usar o Load Balancer?

Use o Azure Load Balancer para:

* Balancear a carga do tráfego de Internet de entrada para suas VMs. Essa configuração é conhecida como [Load Balancer público](#publicloadbalancer).
* Balancear a carga do tráfego entre VMs em uma rede virtual. Também é possível acessar o front-end de um Load Balancer de uma rede local em um cenário híbrido. Ambos os cenários usam uma configuração que é conhecida como um [Load Balancer interno](#internalloadbalancer).
* Encaminhar o tráfego para uma porta específica em VMs específicas com regras de NAT (conversão de endereços de rede) de entrada.
* Forneça [conectividade de saída](load-balancer-outbound-connections.md) para VMs dentro de sua rede virtual usando um Load Balancer público.

>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas para seus cenários. Se estiver em busca de informações sobre o encerramento do protocolo TLS ("descarregamento de SSL") ou sobre o processamento de camada de aplicativo por solicitação HTTP/HTTPS, confira [O que é o Gateway de Aplicativo do Azure](../application-gateway/application-gateway-introduction.md). Se você estiver procurando por balanceamento de carga DNS global, confira [O que é o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md). Os cenários de ponta a ponta podem se beneficiar da combinação dessas soluções.

## <a name="what-are-load-balancer-resources"></a>O que são recursos de Load Balancer?

Os recursos do Load Balancer são objetos que especificam como o Azure deve programar sua infraestrutura de multilocatário para alcançar o cenário que você deseja criar. Não há relação direta entre os recursos do Load Balancer e a infraestrutura real. A criação de um Load Balancer não cria uma instância e a capacidade sempre está disponível.

Um recurso do Load Balancer pode ser um Load Balancer público ou um Load Balancer interno. As funções do recurso do Load Balancer são definidas por um front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end. Você coloca VMs no pool de back-end especificando o pool de back-end na VM.

## <a name="fundamental-load-balancer-features"></a>Recursos do Azure Load Balancer fundamental

O Azure Load Balancer fornece os seguintes recursos fundamentais para aplicativos de TCP e UDP:

* **Balanceamento de carga**

  Com o Azure Load Balancer, é possível criar uma regra de balanceamento de carga para distribuir o tráfego que chega em um front-end para instâncias do pool de back-end. O Load Balancer usa um algoritmo baseado em hash para distribuição de fluxos de entrada e regenera os cabeçalhos dos fluxos para instâncias do pool de back-end. Um servidor fica disponível para receber novos fluxos quando a investigação de integridade indica que o ponto de extremidade de back-end é íntegro.

  Por padrão, o Load Balancer usa um hash de 5 tuplas. O hash inclui endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e número do protocolo IP para mapear fluxos para servidores disponíveis. Você pode criar afinidade com um endereço IP de origem usando um hash de 2 ou 3 tuplas para uma determinada regra. Todos os pacotes do mesmo fluxo de pacote chegam na mesma instância por trás do front-end com balanceamento de carga. Quando o cliente inicia um novo fluxo do mesmo IP de origem, a porta de origem é alterada. Dessa forma, o hash de 5 tuplas podem fazer com que o tráfego vá para um ponto de extremidade de back-end diferente.

  Para saber mais, confira [Configurar o modo de distribuição para o Azure Load Balancer](load-balancer-distribution-mode.md). A imagem a seguir mostra a distribuição baseada em hash:

  ![Distribuição baseada em hash](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: distribuição baseada em hash*

* **Encaminhamento de porta**

  Com Load Balancer, você pode criar uma regra NAT de entrada. Essa regra NAT encaminha o tráfego de uma porta específica de um endereço IP de front-end específico para uma porta específica de uma instância de back-end específica dentro da rede virtual. Esse encaminhamento é realizado pela mesma distribuição baseada em hash que o balanceamento de carga. Cenários comuns dessa funcionalidade são sessões de protocolo RDP ou SSH (Secure Shell) para instâncias de VM individuais em uma Rede Virtual do Microsoft Azure.
  
  É possível mapear vários pontos de extremidade internos para portas no mesmo endereço IP de front-end. Você pode usar os endereços IP de front-end para administrar remotamente suas VMs sem um jumpbox adicional.

* **Independência e transparência de aplicativo**

  O Load Balancer não interage diretamente com o TCP ou UDP, nem com a camada de aplicativo. Qualquer cenário de aplicativo TCP ou UDP pode ser compatível. O Load Balancer não encerra nem origina fluxos, não interage com o conteúdo do fluxo nem fornece qualquer função de gateway de camada de aplicativo. Os handshakes de protocolo sempre ocorrem diretamente entre o cliente e a instância do pool de back-end. Uma resposta para um fluxo de entrada sempre é uma resposta de uma máquina virtual. Quando o fluxo chega na máquina virtual, o endereço IP de origem original também é preservado.

  * Cada ponto de extremidade é respondido somente por uma VM. Por exemplo, um handshake TCP sempre ocorre entre o cliente e a VM de back-end selecionada. Uma resposta a uma solicitação feita a um front-end é uma resposta gerada por uma VM de back-end. Ao validar com êxito a conectividade com um front-end, você está validando a conectividade de ponta a ponta com pelo menos uma máquina virtual de back-end.
  * O conteúdo do aplicativo é transparente para o Load Balancer. Qualquer aplicativo UDP ou TCP pode ser compatível. Para cargas de trabalho que exigem processamento por solicitação HTTP ou manipulação de conteúdo da camada de aplicativo, como análise de URLs HTTP, você deve usar um balanceador de carga de camada 7, como o [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway).
  * Como Load Balancer não interage com o conteúdo de TCP e fornece o descarregamento de TLS, você pode criar cenários criptografados de ponta a ponta. O uso do Load Balancer ganha grande expansão para aplicativos TLS encerrando a conexão TLS na VM em si. Por exemplo, a capacidade de chaveamento de sua sessão TLS é limitada apenas pelo tipo e pelo número de VMs adicionadas ao pool de back-end. Se você exigir descarregamento de SSL, tratamento de camada de aplicativo, ou quiser delegar o gerenciamento de certificados ao Azure, use o [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway) do balanceador de carga de camada 7 do Azure.

* **Reconfiguração automática**

  O Load Balancer reconfigura-se instantaneamente quando você escala ou reduz verticalmente as instâncias. Adicionar ou remover VMs do pool de back-end reconfigura o Load Balancer sem operações adicionais no recurso do Load Balancer.

* **Investigações de integridade**

  Para determinar a integridade das instâncias no pool de back-end, o Load Balancer usa investigações de integridade definidas por você. Quando uma investigação não responde, o Load Balancer interrompe o envio de novas conexões para as instâncias não íntegras. Uma falha de investigação não afeta as conexões existentes. A conexão continua até que o aplicativo encerre o fluxo, transcorra um tempo limite de ociosidade ou a VM seja desligada.

  O Load Balancer fornece diferentes tipos de investigação de integridade para pontos de extremidade TCP, HTTP e HTTPS. Para saber mais, confira [Tipos de investigação](load-balancer-custom-probe-overview.md#types).

  Ao usar os serviços de nuvem Clássicos, um tipo adicional é permitido: [Agente convidado](load-balancer-custom-probe-overview.md#guestagent). Um agente convidado deve ser considerado uma investigação de integridade do último recurso. A Microsoft não os recomenda se houver outras opções disponíveis.

* **Conexões de saída (SNAT)**

  Todos os fluxos de saída dos endereços IP privados em sua rede virtual para endereços IP públicos na Internet podem ser movidos para um endereço IP de front-end do Load Balancer. Quando um front-end público é vinculado a uma VM de back-end por meio de uma regra de balanceamento de carga, o Azure move conexões de saída para o endereço IP de front-end público. Essa configuração tem as seguintes vantagens:

  * Facilidade de upgrade e recuperação de desastre dos serviços, uma vez que o front-end pode ser mapeado dinamicamente para outra instância do serviço.
  * Gerenciamento mais fácil da ACL (lista de controle de acesso). As ACLs expressas como IPs de front-end não são alteradas à medida que os serviços são escalados verticalmente para cima ou para baixo, ou quando são reimplantados. Mover conexões de saída para um número menor de endereços IP do que computadores reduz a carga de implementar listas seguras de destinatários.

  Para saber mais, confira [Conexões de saída no Azure](load-balancer-outbound-connections.md).

O Standard Load Balancer tem funcionalidades adicionais específicas do SKU além dessas básicas, conforme descrito abaixo.

## <a name="skus"></a> Comparação de SKU do Azure Load Balancer

O Load Balancer dá suporte a SKUs Basic e Standard. Há diferenças de SKU na escala, nos recursos e no preço do cenário. Qualquer cenário possível com o Basic Load Balancer pode ser criado com o Standard Load Balancer. As APIs para ambos os SKUs são semelhantes e invocadas pela especificação de um SKU. A API para dar suporte a SKUs do Load Balancer e ao IP público está disponível começando pela API `2017-08-01`. Ambos os SKUs compartilham a mesma API geral e estrutura.

A configuração do cenário completo pode ser um pouco diferente dependendo do SKU. A documentação do Load Balancer indica quando um artigo se aplica somente a um SKU específico. Veja a tabela a seguir para comparar e entender as diferenças. Para saber mais, confira [Visão geral do Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Novos designs devem adotar o Standard Load Balancer.

VMs autônomas, conjuntos de disponibilidade e conjuntos de dimensionamento de máquina virtual podem estar conectados apenas a um SKU, nunca a ambos. Os SKUs do Load Balancer e do endereço IP público devem ser correspondentes quando você usá-los com endereços IP públicos. Os SKUs do Load Balancer e do IP público não são mutáveis.

A melhor prática é especificar explicitamente os SKUs. Neste momento, as alterações necessárias estão sendo mantidas em um mínimo. Se um SKU não for especificado, o padrão será a versão de API `2017-08-01` do SKU Básico.

>[!IMPORTANT]
>O Standard Load Balancer é um novo produto do Load Balancer. Trata-se basicamente de um superconjunto do Basic Load Balancer, mas há diferenças importantes entre os dois produtos. Qualquer cenário de ponta a ponta possível com o Load Balancer Básico também pode ser criado com o Load Balancer Standard. Se você já está acostumado com o Basic Load Balancer, compare-o com o Standard Load Balancer para entender as alterações mais recentes em seu comportamento.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para saber mais, confira [Limites do balanceador de carga](https://aka.ms/lblimits). Para obter detalhes sobre o Load Balancer Standard, confira [visão geral](load-balancer-standard-overview.md), [preços](https://aka.ms/lbpricing) e [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Conceitos

### <a name = "publicloadbalancer"></a>Azure Load Balancer Público

O Load Balancer público mapeia o endereço IP público e a porta do tráfego de entrada para o endereço IP privado e a porta da VM. O Load Balancer mapeia o tráfego ao contrário para o tráfego de resposta da VM. Você pode distribuir tipos específicos de tráfego entre vários serviços ou VMs aplicando regras de balanceamento de carga. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web.

>[!NOTE]
>É possível implementar apenas um Load Balancer público e um Load Balancer interno por conjunto de disponibilidade.

A figura a seguir mostra um ponto de extremidade com balanceamento de carga para tráfego da Web que é compartilhado entre três VMs para a porta TCP 80 pública. Essas três VMs estão em um conjunto com balanceamento de carga.

![Exemplo de Load Balancer público](./media/load-balancer-overview/IC727496.png)

*Figura: Balanceando o tráfego da Web usando um Load Balancer público*

Os clientes da Internet enviam solicitações da página da Web para o endereço IP público de um aplicativo Web na porta TCP 80. O Azure Load Balancer distribui as solicitações entre as três VMs no conjunto com balanceamento de carga. Para saber mais sobre algoritmos do Load Balancer, confira [Recursos essenciais do Load Balancer](load-balancer-overview.md##fundamental-load-balancer-features).

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

Confira [Criar um Basic Load Balancer](quickstart-create-basic-load-balancer-portal.md) para começar a usar um Load Balancer: crie VMs com uma extensão de IIS personalizada instalada e faça o balanceamento de carga do aplicativo Web entre as VMs.
