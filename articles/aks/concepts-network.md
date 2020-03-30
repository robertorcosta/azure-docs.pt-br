---
title: Conceitos - Rede nos Serviços do Kubernetes do Azure (AKS)
description: Aprenda sobre a rede no AKS (Serviço de Kubernetes do Azure), incluindo as redes da CNI do Azure e kubenet e seus controladores de entrada, balanceadores de carga e endereços IP estáticos.
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253930"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Conceitos de rede para aplicativos no Serviço de Kubernetes do Azure (AKS)

Em uma abordagem de micros serviços baseada em contêiner para o desenvolvimento de aplicativos, os componentes de aplicativo devem trabalhar juntos para processar suas tarefas. O Kubernetes fornece vários recursos que permitem a comunicação desse aplicativo. Você pode se conectar e expor aplicativos internamente ou externamente. Para criar aplicativos altamente disponíveis, você pode balancear a carga de seus aplicativos. Aplicativos mais complexos podem exigir configuração de tráfego de ingresso para terminação SSL / TLS ou roteamento de múltiplos componentes. Por motivos de segurança, você também pode precisar restringir o fluxo de tráfego de rede em ou entre pods e nós.

Este artigo apresenta os principais conceitos que fornecem rede para seus aplicativos no AKS:

- [Serviços](#services)
- [Redes virtuais Azure](#azure-virtual-networks)
- [Controladores de ingress](#ingress-controllers)
- [Políticas de rede](#network-policies)

## <a name="kubernetes-basics"></a>Noções básicas do Kubernetes

Para permitir o acesso aos seus aplicativos, ou para os componentes do aplicativo se comunicarem uns com os outros, o Kubernetes fornece uma camada de abstração para a rede virtual. Os nós do Kubernetes estão conectados a uma rede virtual e podem fornecer conectividade de entrada e saída para pods. O componente *kube-proxy* é executado em cada nó para fornecer esses recursos de rede.

No Kubernetes, os *Serviços* agrupam logicamente os pods para permitir o acesso direto por meio de um endereço IP ou nome DNS e em uma porta específica. Você também pode distribuir tráfego usando um *balanceador de carga*. Um roteamento mais complexo de tráfego de aplicativos também pode ser obtido com os *Controladores de Ingressos*. A segurança e a filtragem do tráfego de rede para pods é possível com as políticas de *rede do Kubernetes*.

A plataforma do Azure também ajuda a simplificar a rede virtual para clusters do AKS. Quando você cria um balanceador de carga do Kubernetes, o recurso do balanceador de carga do Azure subjacente é criado e configurado. À medida que você abre portas de rede para pods, as regras correspondentes do grupo de segurança de rede do Azure são configuradas. Para o roteamento de aplicativos HTTP, o Azure também pode configurar *DNS externo* à medida que novas rotas de ingresso são configuradas.

## <a name="services"></a>Serviços

Para simplificar a configuração de rede para cargas de trabalho de aplicativos, o Kubernetes usa *Serviços* para agrupar logicamente um conjunto de pods e fornecer conectividade de rede. Os seguintes tipos de serviço estão disponíveis:

- **IP do cluster** - Cria um endereço IP interno para uso no cluster do AKS. Bom para aplicativos internos que suportam outras cargas de trabalho no cluster.

    ![Diagrama mostrando o fluxo de tráfego IP do cluster em um cluster AKS][aks-clusterip]

- **NodePort** - Cria um mapeamento de porta no nó subjacente que permite que o aplicativo seja acessado diretamente com o endereço IP e a porta do nó.

    ![Diagrama mostrando o fluxo de tráfego do NodePort em um cluster AKS][aks-nodeport]

- **LoadBalancer** - Cria um recurso do balanceador de carga do Azure, configura um endereço IP externo e conecta os pods solicitados ao pool de back-ends do balanceador de carga. Para permitir que o tráfego dos clientes chegue ao aplicativo, são criadas regras de balanceamento de carga nas portas desejadas. 

    ![Diagrama mostrando o fluxo de tráfego do Balanceador de Carga em um cluster AKS][aks-loadbalancer]

    Para controle adicional e roteamento do tráfego de entrada, você pode usar um [controlador de ingresso](#ingress-controllers).

- **ExternalName** - Cria uma entrada de DNS específica para facilitar o acesso a aplicativos.

O endereço IP para balanceadores de carga e serviços pode ser atribuído dinamicamente ou você pode especificar um endereço IP estático existente para uso. Endereços IP estáticos internos e externos podem ser atribuídos. Esse endereço IP estático existente é geralmente vinculado a uma entrada DNS.

Ambos *interno* e *externo* balanceadores de carga podem ser criados. Os balanceadores de carga interna são atribuídos apenas a um endereço IP privado, de modo que eles não podem ser acessados a partir da Internet.

## <a name="azure-virtual-networks"></a>Redes virtuais do Azure

No AKS, você pode implantar um cluster que usa um dos dois modelos de rede a seguir:

- Rede *Kubenet* – os recursos de rede normalmente são criados e configurados à medida que o cluster AKS é implantado.
- Rede da *CNI (Interface de rede de contêiner) do Azure* – o cluster AKS é conectado a recursos e configurações de rede virtual existentes.

### <a name="kubenet-basic-networking"></a>Rede Kubenet (básica)

A opção de rede *kubenet* é a configuração padrão para a criação do cluster AKS. Com o *kubenet*, os nós obtêm um endereço IP de uma sub-rede da rede virtual do Azure. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente da sub-rede da rede virtual do Azure dos nós. A NAT (conversão de endereços de rede), então, é configurada para que os pods possam acessar recursos na rede virtual do Azure. O endereço IP de origem do tráfego é configurado via NAT como o endereço IP primário do nó.

Os nós usam o plug-in [kubenet][kubenet] do Kubernetes. Você pode permitir que a plataforma do Azure crie e configure as redes virtuais para você ou pode optar por implantar o cluster do AKS em uma sub-rede da rede virtual existente. Novamente, apenas os nós recebem um endereço IP rotável, e os pods usam NAT para se comunicar com outros recursos fora do cluster AKS. Essa abordagem reduz muito o número de endereços IP que você precisa reservar no espaço de rede para uso dos pods.

Para obter mais informações, consulte [Configurar rede kubenet para um cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Rede da CNI do Azure (avançada)

Com a CNI do Azure, cada pod obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o seu espaço de rede e devem ser planejados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods aos quais ele dá suporte. O número equivalente de endereços IP por nó é então reservado com antecedência para esse nó. Essa abordagem requer mais planejamento, pois pode levar à exaustão do endereço IP ou à necessidade de reconstruir clusters em uma sub-rede maior à medida que as demandas de aplicativos crescem.

Nós usam o plug-in de [CNI (Interface de rede de contêiner) do Azure][cni-networking] do Kubernetes.

![Diagrama que mostra dois nós com pontes conectando cada um a uma única VNet do Azure][advanced-networking-diagram]

Para obter mais informações, consulte [Configurar a CNI do Azure para um cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Compare modelos de rede

Tanto a Kubenet quanto a Azure CNI fornecem conectividade de rede para seus clusters AKS. No entanto, há vantagens e desvantagens para cada um. Em alto nível, aplicam-se as seguintes considerações:

* **kubenet**
    * Conserva espaço de endereço IP.
    * Usa o balanceador de carga interno ou externo kubernetes para alcançar pods de fora do cluster.
    * Você deve gerenciar e manter manualmente rotas definidas pelo usuário (UDRs).
    * Máximo de 400 nodes por cluster.
* **Azure CNI**
    * Os pods recebem conectividade de rede virtual completa e podem ser acessados diretamente através de seu endereço IP privado a partir de redes conectadas.
    * Requer mais espaço de endereço IP.

Existem as seguintes diferenças de comportamento entre kubenet e Azure CNI:

| Recurso                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Implantar cluster em rede virtual existente ou nova                                            | Suportado - UDRs aplicados manualmente | Com suporte |
| Conectividade pod-pod                                                                         | Com suporte | Com suporte |
| Conectividade Pod-VM; VM na mesma rede virtual                                          | Funciona quando iniciado por pod | Funciona nos dois sentidos |
| Conectividade Pod-VM; VM em rede virtual peered                                            | Funciona quando iniciado por pod | Funciona nos dois sentidos |
| Acesso no local usando VPN ou Express Route                                                | Funciona quando iniciado por pod | Funciona nos dois sentidos |
| Acesso aos recursos protegidos por pontos finais de serviço                                             | Com suporte | Com suporte |
| Exponha os serviços da Kubernetes usando um serviço de balanceador de carga, App Gateway ou controlador de entrada | Com suporte | Com suporte |
| DNS e zonas privadas padrão do Azure                                                          | Com suporte | Com suporte |

Em relação ao DNS, tanto o DNS da Kubenet quanto do Azure CNI é oferecido pela CoreDNS, um conjunto de daemon saem em execução em AKS. Para obter mais informações sobre o CoreDNS no Kubernetes, consulte [Personalizing DNS Service](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). O CoreDNS é configurado por padrão para encaminhar domínios desconhecidos para os servidores DNS de nó, ou seja, para a funcionalidade DNS da Rede Virtual Azure onde o cluster AKS é implantado. Assim, o Azure DNS e as Zonas Privadas funcionarão para pods em execução em AKS.

### <a name="support-scope-between-network-models"></a>Escopo de suporte entre modelos de rede

Independentemente do modelo de rede que você usa, tanto o Kubenet quanto o Azure CNI podem ser implantados de uma das seguintes maneiras:

* A plataforma Azure pode criar e configurar automaticamente os recursos de rede virtual quando você cria um cluster AKS.
* Você pode criar e configurar manualmente os recursos da rede virtual e anexar a esses recursos quando criar seu cluster AKS.

Embora os recursos como endpoints de serviço ou UDRs sejam suportados com kubenet e Azure CNI, as [políticas de suporte para AKS][support-policies] definem quais mudanças você pode fazer. Por exemplo: 

* Se você criar manualmente os recursos de rede virtual para um cluster AKS, você será suportado ao configurar seus próprios UDRs ou pontos finais de serviço.
* Se a plataforma Azure criar automaticamente os recursos de rede virtual para o cluster AKS, não será suportado alterar manualmente esses recursos gerenciados pela AKS para configurar seus próprios UDRs ou pontos finais de serviço.

## <a name="ingress-controllers"></a>Controladores de entrada

Quando você cria um serviço do tipo LoadBalancer, um recurso de balanceador de carga subjacente do Azure é criado. O balanceador de carga é configurado para distribuir o tráfego para os pods em seu Serviço em uma determinada porta. O LoadBalancer só funciona na camada 4 - o Serviço não está ciente dos aplicativos reais e não pode fazer nenhuma outra consideração de roteamento.

*Os controladores ingressos* funcionam na camada 7 e podem usar regras mais inteligentes para distribuir o tráfego de aplicativos. Um uso comum de um controlador de ingresso é rotear o tráfego HTTP para diferentes aplicativos com base na URL de entrada.

![Diagrama mostrando o fluxo de tráfego de ingresso em um cluster AKS][aks-ingress]

No AKS, você pode criar um recurso do Ingress usando algo como o NGINX ou usar o recurso de roteamento do aplicativo AKS HTTP. Quando você habilita o roteamento de aplicativo HTTP para um cluster AKS, a plataforma Azure cria o controlador Ingress e um controlador *External-DNS*. À medida que novos recursos do Ingress são criados no Kubernetes, os registros necessários do DNS A são criados em uma zona DNS específica do cluster. Para obter mais informações, consulte [implantar o roteamento de aplicativos HTTP][aks-http-routing].

Outro recurso comum do Ingress é o encerramento de SSL / TLS. Em grandes aplicativos da Web acessados via HTTPS, a terminação do TLS pode ser manipulada pelo recurso Ingress, em vez de pelo próprio aplicativo. Para fornecer geração e configuração automática de certificação TLS, você pode configurar o recurso Ingress para usar provedores como o Let's Encrypt. Para obter mais informações sobre como configurar um controlador NGINX Ingress com o Let's Encrypt, consulte [Ingress e TLS][aks-ingress-tls].

Você também pode configurar seu controlador de ingress para preservar o IP de origem do cliente em solicitações para contêineres em seu cluster AKS. Quando a solicitação de um cliente é encaminhada para um contêiner em seu cluster AKS através do controlador de ingress, o IP de origem original dessa solicitação não estará disponível para o contêiner de destino. Quando você habilita a *preservação de IP da fonte do cliente,* o IP de origem para o cliente está disponível no cabeçalho de solicitação em *X-Forwarded-For*. Se você estiver usando a preservação de IP da fonte do cliente no controlador de entrada, você não pode usar o passe do SSL. A preservação de IP da fonte do cliente e o passe do SSL podem ser usados com outros serviços, como o tipo *LoadBalancer.*

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um grupo de segurança de rede filtra o tráfego para VMs, como os nós do AKS. À medida que você cria Serviços, como um LoadBalancer, a plataforma do Azure configura automaticamente as regras do grupo de segurança de rede necessárias. Não configure manualmente as regras do grupo de segurança de rede para filtrar o tráfego de pods em um cluster do AKS. Defina as portas e o encaminhamento necessários como parte dos manifestos do Kubernetes Service e permita que a plataforma do Azure crie ou atualize as regras apropriadas. Você também pode usar políticas de rede, como discutido na próxima seção, para aplicar automaticamente as regras do filtro de tráfego aos pods.

## <a name="network-policies"></a>Políticas de rede

Por padrão, todos os pods em um cluster do AKS podem enviar e receber tráfego sem limitações. Para melhorar a segurança, você pode definir regras que controlam o fluxo de tráfego. Os aplicativos de back-end geralmente só são expostos aos serviços de front-end necessários ou os componentes de banco de dados são acessíveis somente para as camadas de aplicativos que se conectam a eles.

A diretiva de rede é um recurso kubernetes disponível no AKS que permite controlar o fluxo de tráfego entre pods. Você pode optar por permitir ou negar o tráfego com base em configurações, como rótulos atribuídos, namespace ou porta de tráfego. Os grupos de segurança de rede são mais para os nós do AKS, e não para os pods. O uso de políticas de rede é uma maneira mais adequada e nativa da nuvem de controlar o fluxo de tráfego. Como os pods são criados dinamicamente em um cluster AKS, as políticas de rede necessárias podem ser aplicadas automaticamente.

Para saber mais, confira [Proteger o tráfego entre os pods usando as políticas de rede no AKS (Serviço de Kubernetes do Azure)][use-network-policies].

## <a name="next-steps"></a>Próximas etapas

Para obter uma introdução à rede do AKS, crie e configure um cluster do AKS com seus próprios intervalos de endereços IP usando [kubenet][aks-configure-kubenet-networking] ou a [CNI do Azure][aks-configure-advanced-networking].

Para as práticas recomendadas associadas, consulte [As melhores práticas de conectividade e segurança de rede em AKS][operator-best-practices-network].

Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Kubernetes / clusters AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / AKS de acesso e identidade][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / escala de AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
