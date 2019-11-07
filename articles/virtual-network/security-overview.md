---
title: Visão geral dos grupos de segurança do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre grupos de segurança de rede e aplicativo. Os grupos de segurança ajudam você a filtrar o tráfego de rede entre recursos do Azure.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 6046ab98e657cd14a2ac883cd32709c9a1b5da57
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721488"
---
# <a name="security-groups"></a>Grupos de segurança
<a name="network-security-groups"></a>

Você pode filtrar o tráfego de rede de e para recursos do Azure em uma [rede virtual](virtual-networks-overview.md) do Azure com um grupo de segurança de rede. Um grupo de segurança de rede contém [regras de segurança](#security-rules) que permitem ou negam o tráfego de rede de entrada ou de saída em relação a vários tipos de recursos do Azure. Para saber mais sobre quais recursos do Azure podem ser implantados em uma rede virtual e ter grupos de segurança de rede associados a eles, consulte [Integração de rede virtual para serviços do Azure](virtual-network-for-azure-services.md). Para cada regra, você pode especificar origem e destino, porta e protocolo.

Este artigo explica conceitos de grupo de segurança de rede para ajudá-lo a usá-los com eficiência. Se você nunca criou um grupo de segurança de rede, pode concluir um [tutorial](tutorial-filter-network-traffic.md) rápido para obter alguma experiência. Se você estiver familiarizado com os grupos de segurança de rede e a necessidade de gerenciá-los, consulte [Gerenciar um grupo de segurança de rede](manage-network-security-group.md). Se você estiver tendo problemas de comunicação e precisa solucionar problemas de grupos de segurança de rede, consulte [Diagnosticar um problema de filtro de tráfego de rede em máquina virtual](diagnose-network-traffic-filter-problem.md). Você pode habilitar [logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para [analisar tráfego de rede](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) entrando e saindo de recursos que tenham um grupo de segurança de rede associado.

## <a name="security-rules"></a>Regras de segurança

Um grupo de segurança de rede pode conter nenhuma ou quantas regras você desejar, dentro dos [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) da assinatura do Azure. Cada regra especifica as seguintes propriedades:

|Propriedade  |Explicação  |
|---------|---------|
|Nome|Um nome exclusivo dentro do grupo de segurança de rede.|
|Prioridade | Um número entre 100 e 4096. As regras são processadas na ordem de prioridade, com números mais baixos processados antes de números mais altos, pois os números mais baixos têm prioridade mais alta. Depois que o tráfego corresponde a uma regra, o processamento é interrompido. Assim, as regras existentes com baixa prioridade (números mais altos) que têm os mesmos atributos das regras com prioridades mais altas não são processadas.|
|Origem ou destino| Qualquer endereço IP ou um endereço IP individual, bloco de CIDR (encaminhamento entre domínios) (10.0.0.0/24, por exemplo), [marca de serviço](service-tags-overview.md) ou [grupo de segurança de aplicativo](#application-security-groups). Se você especificar um endereço para um recurso do Azure, especifique o endereço IP privado atribuído ao recurso. Os grupos de segurança de rede são processados depois que o Azure traduz um endereço IP público em um endereço IP privado para tráfego de entrada e antes que o Azure traduza um endereço IP privado para um endereço IP público para tráfego de saída. Saiba mais sobre os [endereços IP](virtual-network-ip-addresses-overview-arm.md) do Azure. A especificação de um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicativos permite que você crie menos regras de segurança. A capacidade de especificar vários endereços IP individuais e intervalos (você não pode especificar várias marcas de serviço ou grupos de aplicativos) em uma regra é conhecida como [regras de segurança aumentadas](#augmented-security-rules). As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede criados pelo modelo de implantação do Gerenciador de Recursos. Você não pode especificar vários endereços IP e intervalos de endereços IP em grupos de segurança de rede criados pelo modelo de implantação clássica. Saiba mais sobre os [modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocolo     | TCP, UDP, ICMP ou any.|
|Direção| Se a regra se aplica ao tráfego de entrada ou de saída.|
|Intervalo de portas     |Você pode especificar uma porta individual ou um intervalo de portas. Por exemplo, você pode especificar 80 ou 10000-10005. A especificação de intervalos permite que você crie menos regras de segurança. As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede criados pelo modelo de implantação do Gerenciador de Recursos. Você não pode especificar várias portas ou intervalos de porta na mesma regra de segurança em grupos de segurança de rede criados pelo modelo de implantação clássica.   |
|Ação     | Permitir ou negar        |

As regras de segurança do grupo de segurança de rede são avaliadas por prioridade usando as informações de 5 tuplas (origem, porta de origem, destino, porta de destino e protocolo) para permitir ou negar o tráfego. Um registro de fluxo é criado para as conexões existentes. A comunicação é permitida ou negada com base no estado de conexão do registro de fluxo. O registro de fluxo permite que um grupo de segurança de rede seja com estado. Se você especificar uma regra de segurança de saída para algum endereço pela porta 80, por exemplo, não será necessário especificar uma regra de segurança de entrada para a resposta ao tráfego de saída. Você precisa especificar uma regra de segurança de entrada se a comunicação for iniciada externamente. O oposto também é verdadeiro. Se o tráfego de entrada é permitido por uma porta, não é necessário especificar uma regra de segurança de saída para responder ao tráfego pela porta.
As conexões existentes podem não ser interrompidas quando você remove uma regra de segurança que habilitou o fluxo. Os fluxos de tráfego são interrompidos quando as conexões são interrompidas e nenhum tráfego está fluindo em qualquer direção por pelo menos alguns minutos.

Há limites ao número de regras de segurança que você pode criar em um grupo de segurança de rede. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Regras de segurança aumentadas

As regras de segurança aumentada simplificam a definição de segurança para redes virtuais, permitindo que você defina políticas de segurança de rede maiores e mais complexas com menos regras. Você pode combinar várias portas e vários intervalos e endereços IP explícitos em uma única regra de segurança fácil de entender. Use regras aumentadas nos campos de origem, destino e porta de uma regra. Para simplificar a manutenção da sua definição de regra de segurança, combine as regras de segurança aumentadas com [marcas de serviço](service-tags-overview.md) ou [grupos de segurança de aplicativo](#application-security-groups). Há limites para a quantidade de endereços, intervalos e portas que você pode especificar em uma regra. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Marcas de serviço

Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. Ele ajuda a minimizar a complexidade de atualizações frequentes em regras de segurança de rede.

Para obter mais informações, consulte [marcas de serviço do Azure](service-tags-overview.md). 

## <a name="default-security-rules"></a>Regras de segurança padrão

O Azure cria as seguintes regras padrão em cada grupo de segurança de rede que você criar:

### <a name="inbound"></a>Entrada

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioridade|Fonte|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Qualquer|Permitir|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioridade|Fonte|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Qualquer|Permitir|

#### <a name="denyallinbound"></a>DenyAllInBound

|Prioridade|Fonte|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Qualquer|NEGAR|

### <a name="outbound"></a>Saída

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Qualquer | Permitir |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Qualquer | Permitir |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Qualquer | NEGAR |

Nas colunas **Origem** e **Destino**, *VirtualNetwork*, *AzureLoadBalancer* e *Internet* são [marcas de serviço](service-tags-overview.md) em vez de endereços IP. Na coluna protocolo, **qualquer um** abrange TCP, UDP e ICMP. Ao criar uma regra, você pode especificar TCP, UDP, ICMP ou any. *0.0.0.0/0* nas colunas **Origem** e **Destino** representa todos os endereços. Clientes como portal do Azure, CLI do Azure ou PowerShell podem usar * ou qualquer para essa expressão.
 
Não é possível remover as regras padrão, mas você pode substituí-las criando regras com prioridades mais altas.

## <a name="application-security-groups"></a>Grupos de segurança do aplicativo

Os grupos de segurança de aplicativo permitem a você configurar a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Você pode reutilizar sua política de segurança em escala sem precisar manter endereços IP explícitos manualmente. A plataforma lida com a complexidade de endereços IP explícitos e vários conjuntos de regras, permitindo que você se concentre na sua lógica de negócios. Para entender melhor os grupos de segurança de aplicativo, considere o exemplo abaixo:

![Grupos de segurança do aplicativo](./media/security-groups/application-security-groups.png)

Na figura anterior, *NIC1* e *NIC2* são membros do grupo de segurança de aplicativo *AsgWeb*. *NIC3* é um membro do grupo de segurança de aplicativo *AsgLogic*. *NIC4* é um membro do grupo de segurança de aplicativo *AsgDb*. Embora cada adaptador de rede neste exemplo seja membro de apenas um grupo de segurança de aplicativo, um adaptador de rede pode ser membro de vários grupos de segurança de aplicativo, dentro dos [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Nenhum dos adaptadores de rede tem um grupo de segurança de rede associado. *NSG1* está associado a ambas as sub-redes e contém as seguintes regras:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Essa regra é necessária para permitir o tráfego da Internet para os servidores Web. Como o tráfego de entrada da Internet é negado pela regra de segurança padrão [DenyAllInbound](#denyallinbound), nenhuma regra adicional é necessária para os grupos de segurança do aplicativo *AsgLogic* ou *AsgDb*.

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Permitir |

### <a name="deny-database-all"></a>Deny-Database-All

Já que a regra de segurança padrão [AllowVNetInBound](#allowvnetinbound) permite toda a comunicação entre recursos na mesma rede virtual, essa regra é necessária para negar o tráfego de todos os recursos.

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Qualquer | NEGAR |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Essa regra permite o tráfego do grupo de segurança de aplicativo *AsgLogic* para o grupo de segurança de aplicativo *AsgDb*. A prioridade para essa regra é mais alta do que a prioridade para a regra *Deny-Database-All*. Como resultado, essa regra é processada antes da regra *Deny-Database-All* e, portanto, o tráfego do grupo de segurança de aplicativo *AsgLogic* é permitido enquanto todos os outros tráfegos são bloqueados.

|Prioridade|Fonte|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Permitir |

As regras que especificam um grupo de segurança de aplicativo como origem ou destino são aplicadas apenas aos adaptadores de rede que são membros do grupo de segurança de aplicativo. Se o adaptador de rede não for membro de um grupo de segurança de aplicativo, a regra não será aplicada ao adaptador de rede, mesmo que o grupo de segurança de rede esteja associado à sub-rede.

Os grupos de segurança do aplicativo têm as seguintes restrições:

-   Há limites ao número de grupos de segurança de aplicativo que você pode ter em uma assinatura, bem como outros limites relacionados aos grupos de segurança de aplicativo. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Você pode especificar um grupo de segurança do aplicativo como a origem e o destino em uma regra de segurança. Você não pode especificar vários grupos de segurança de aplicativos na origem ou no destino.
- Todas as interfaces de rede atribuídas a um grupo de segurança do aplicativo precisam existir na mesma rede virtual que a primeira interface de rede atribuída ao grupo de segurança que o aplicativo está. Por exemplo, se o primeiro adaptador de rede atribuído a um grupo de segurança de aplicativo chamado *AsgWeb* estiver na rede virtual denominada *VNet1*, todos os adaptadores de rede subsequentes atribuídos a *ASGWeb* deverão existir na *VNet1*. Você não pode adicionar interfaces de rede de redes virtuais diferentes ao mesmo grupo de segurança do aplicativo.
- Se você especificar um grupo de segurança do aplicativo como a origem e o destino em uma regra de segurança, as interfaces de rede em ambos os grupos de segurança do aplicativo deverão existir na mesma rede virtual. Por exemplo, se *AsgLogic* contiver adaptadores de rede da *VNet1* e *AsgDb* contiver adaptadores de rede da *VNet2*, você não poderá atribuir *AsgLogic* como a origem e o *AsgDb* como o destino em uma regra. Todos os adaptadores de rede dos grupos de segurança de aplicativo de origem e de destino precisam existir na mesma rede virtual.

> [!TIP]
> Para minimizar o número de regras de segurança necessárias e a necessidade de alterar as regras, planeje os grupos de segurança do aplicativo que serão necessários e crie regras usando marcas de serviço ou grupos de segurança de aplicativo em vez de endereços IP individuais ou intervalos de endereços IP, sempre que possível.

## <a name="how-traffic-is-evaluated"></a>Como o tráfego é avaliado

Você pode implantar recursos de vários serviços do Azure em uma rede virtual do Azure. Para obter uma lista completa, consulte [Serviços que podem ser implantados em uma rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Você pode associar um, ou nenhum, grupo de segurança de rede a cada [sub-rede](virtual-network-manage-subnet.md#change-subnet-settings) e [adaptador de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) de uma rede virtual em uma máquina virtual. O mesmo grupo de segurança de rede pode ser associado ao número de sub-redes e adaptadores de rede desejado.

A imagem abaixo ilustra os diferentes cenários de como os grupos de segurança de rede podem ser implantados para permitir o tráfego de rede da Internet pela porta TCP 80:

![Processamento de NSG](./media/security-groups/nsg-interaction.png)

Consulte a imagem anterior com o texto a seguir para entender como o Azure processa as regras de entrada e saída para os grupos de segurança de rede:

### <a name="inbound-traffic"></a>Tráfego de entrada

Em relação ao tráfego de entrada, o Azure processa as regras em um grupo de segurança de rede associado a uma sub-rede em primeiro lugar, se houver uma, e, em seguida, as regras em um grupo de segurança de rede associado ao adaptador de rede, se houver um.

- **VM1**: as regras de segurança no *NSG1* são processadas, já que estão associadas à *Subnet1* e a *VM1* está na *Subnet1*. A menos que você tenha criado uma regra que permite a entrada pela porta 80, o tráfego será negado pela regra de segurança padrão [DenyAllInbound](#denyallinbound) e nunca será avaliado pelo *NSG2*, já que o *NSG2* está associado ao adaptador de rede. Se *NSG1* tem uma regra de segurança que permite a porta 80, o tráfego é processado por *NSG2*. Para permitir a porta 80 para a máquina virtual, tanto *NSG1* quanto *NSG2* devem ter uma regra que permite a porta 80 da Internet.
- **VM2**: as regras no *NSG1* são processadas porque a *VM2* também está na *Subnet1*. Já que a *VM2* têm um grupo de segurança de rede associado a seu adaptador de rede, ela recebe todo o tráfego permitido pelo *NSG1* ou tem todo o tráfego negado por *NSG1* também negado. O tráfego é permitido ou negado para todos os recursos na mesma sub-rede quando um grupo de segurança de rede está associado a uma sub-rede.
- **VM3**: já que não há nenhum grupo de segurança de rede associado à *Subnet2*, o tráfego é permitido para a sub-rede e processado pelo *NSG2*, pois o *NSG2* está associado ao adaptador de rede anexado à *VM3*.
- **VM4**: o tráfego é permitido para a *VM4* porque um grupo de segurança de rede não está associado à *Subnet3* ou ao adaptador de rede na máquina virtual. Todo o tráfego será permitido por meio de um adaptador de rede e sub-rede se não tiver um grupo de segurança de rede associado a eles.

### <a name="outbound-traffic"></a>Tráfego de saída

Em relação ao tráfego de saída, o Azure processa as regras em um grupo de segurança de rede associado a um adaptador de rede em primeiro lugar, se houver um, e, em seguida, as regras em um grupo de segurança de rede associado à sub-rede, se houver uma.

- **VM1**: as regras de segurança no *NSG2* são processadas. A menos que você crie uma regra de segurança que nega a porta 80 de saída para a Internet, o tráfego será permitido pela regra de segurança padrão [AllowInternetOutbound](#allowinternetoutbound) tanto no *NSG1* quanto no *NSG2*. Se *NSG2* tem uma regra de segurança que nega a porta 80, o tráfego é negado e nunca é avaliado pelo *NSG1*. Para negar a porta 80 na máquina virtual, um ou ambos os grupos de segurança da rede devem ter uma regra que nega a porta 80 para a Internet.
- **VM2**: todo o tráfego é enviado por meio do adaptador de rede na sub-rede, uma vez que o adaptador de rede conectado à *VM2* não tem um grupo de segurança de rede associado a ele. As regras no *NSG1* são processadas.
- **VM3**: se *NSG2* tem uma regra de segurança que nega a porta 80, o tráfego é negado. Se *NSG2* tem uma regra de segurança que permite a porta 80, está tem permissão de saída para a Internet, uma vez que um grupo de segurança de rede não está associado à *Subnet2*.
- **VM4**: todo o tráfego é permitido da *VM4*, pois um grupo de segurança de rede não está associado ao adaptador de rede anexado à máquina virtual, ou para a *Subnet3*.


### <a name="intra-subnet-traffic"></a>Tráfego de intra-subnet

É importante observar que as regras de segurança em um NSG associado a uma sub-rede podem afetar a conectividade entre as VMs dentro dela. Por exemplo, se uma regra for adicionada a *NSG1* que nega todo o tráfego de entrada e de saída, *VM1* e *VM2* não poderão se comunicar entre si. Outra regra teria que ser adicionada especificamente para permitir isso. 



É possível exibir facilmente as regras de agregação aplicadas a um adaptador de rede exibindo as [regras de segurança efetiva](virtual-network-network-interface.md#view-effective-security-rules) para determinado adaptador de rede. Você também pode usar o recurso [Verificar o fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) no Observador de Rede do Azure para determinar se a comunicação é permitida na entrada ou saída de um adaptador de rede. O fluxo de IP informa se a comunicação é permitida ou negada e qual regra de segurança de rede permite ou nega o tráfego.

> [!NOTE]
> Os grupos de segurança de rede são associados a sub-redes ou a máquinas virtuais e serviços de nuvem implantados no modelo de implantação clássico e a sub-redes ou interfaces de rede no modelo de implantação do Resource Manager. Para saber mais sobre os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> A menos que você tenha um motivo específico, recomendamos que você associe um grupo de segurança de rede a uma sub-rede ou a um adaptador de rede, mas não a ambos. Como as regras em um grupo de segurança de rede associado a uma sub-rede podem entrar em conflito com as regras em um grupo de segurança de rede associado a um adaptador de rede, você pode ter problemas de comunicação inesperados que exigem a solução de problemas.

## <a name="azure-platform-considerations"></a>Considerações sobre a plataforma do Azure

- **IP virtual do nó de host**: serviços básicos de infraestrutura, como DHCP, DNS, IMDS e monitoramento de integridade, são fornecidos por meio dos endereços IP de host virtualizados 168.63.129.16 e 169.254.169.254. Esses endereços IP pertencem à Microsoft e são os únicos endereços IP virtualizados usado em todas as regiões para essa finalidade.
- **Licenciamento (Serviço de Gerenciamento de Chaves)** : as imagens do Windows em execução nas máquinas virtuais devem ser licenciadas. Para garantir o licenciamento, uma solicitação de licenciamento é enviada para os servidores de host do serviço de gerenciamento de chaves que lidar com essas consultas. A solicitação é feita para a saída pela porta 1688. Para implantações usando configuração [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), esta regra de plataforma será desabilitada.
- **Máquinas virtuais em pools de carga balanceada**: o intervalo de porta e endereço de origem aplicado é do computador de origem, não do balanceador de carga. Os intervalos de porta e endereço de destino são para o computador de destino, não o balanceador de carga.
- **Instâncias de serviço do Azure**: instâncias de vários serviços do Azure, como o HDInsight, Ambientes de Serviço de Aplicativo e Conjuntos de Dimensionamento de Máquinas Virtuais são implantadas em sub-redes de rede virtual. Para obter uma lista completa de serviços que podem ser implantados em uma rede virtual, confira a [Rede virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Familiarize-se com os requisitos de porta para cada serviço antes da aplicação de um grupo de segurança de rede à sub-rede em que o recurso está implantado. Se você negar portas exigidas pelo serviço, o serviço não funcionará corretamente.
- **Enviar um email**: a Microsoft recomenda que você utilize os serviços de retransmissão de SMTP autenticados (normalmente conectados via porta TCP 587, mas geralmente outras, também) para enviar email de máquinas virtuais do Azure. Os serviços de retransmissão de SMTP são especializados em reputação do remetente, para minimizar a possibilidade dos provedores de email de terceiros rejeitarem mensagens. Esses serviços de retransmissão de SMTP incluem, mas não estão limitados à proteção do Exchange Online e do SendGrid. O uso de serviços de retransmissão de SMTP não é de modo algum restrito no Azure, independentemente de seu tipo de assinatura. 

  Se você criou sua assinatura do Azure antes de 15 de novembro de 2017, além de poder usar os serviços de retransmissão de SMTP, você pode enviar um email diretamente pela porta TCP 25. Se você criou sua assinatura depois do dia 15 de novembro de 2017, não poderá enviar emails diretamente pela porta 25. O comportamento de comunicação de saída pela porta 25 depende do tipo de assinatura que você tem, da seguinte maneira:

     - **Contrato Enterprise**: a comunicação de saída da porta 25 é permitida. É possível enviar emails de saída diretamente a partir de máquinas virtuais para provedores de email externos, sem restrições da plataforma do Azure. 
     - **Pré-pago:** a comunicação de saída da porta 25 está bloqueada de todos os recursos. Se você precisar enviar emails de uma máquina virtual diretamente para os provedores de email externos (não usando uma retransmissão SMTP autenticada), pode fazer uma solicitação para remover a restrição. As solicitações são examinadas e aprovadas a critério da Microsoft e somente são concedidas após a realização de verificações antifraude. Para fazer uma solicitação, abra um caso de suporte com o tipo de problema *Técnico*, *Conectividade de rede virtual*, *Não é possível enviar email (SMTP/Porta 25)* . No seu caso de suporte, isso inclui detalhes sobre por que você precisa enviar emails diretamente aos provedores de email, em vez de passar por um retransmissor SMTP autenticado. Se sua assinatura for isenta, somente as máquinas virtuais criadas após a data de isenção serão capazes de se comunicar pela porta 25.
     - **MSDN, Azure Pass, Azure via Open, Educação, BizSpark, e Avaliação gratuita**: a comunicação de saída da porta 25 está bloqueada para todos os recursos. Nenhuma solicitação para remover a restrição pode ser feita, pois as solicitações não foram concedidas. Se você tiver que enviar um email de sua máquina virtual, deverá usar um serviço de retransmissão de SMTP.
     - **Provedor de serviços de nuvem**: os clientes que estão consumindo recursos do Azure por meio de um provedor de serviços de nuvem podem criar um caso de suporte com seu provedor de serviços de nuvem e solicitar que o provedor de criar um caso de desbloqueio em seu nome, se uma retransmissão SMTP segura não puder ser usada.

  Se o Azure permitir que você envie emails pela porta 25, a Microsoft não garante que os provedores de email aceitarão emails de entrada provenientes de sua máquina virtual. Se um provedor específico rejeitar um email de sua máquina virtual, trabalhe diretamente com o provedor para resolver entregas de mensagens ou problemas de filtragem de spam, ou use um serviço de retransmissão de SMTP autenticado.

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [Criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
