---
title: Configure a rede para espaços azure dev em diferentes topoologias de rede
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Descreve os requisitos de rede para executar espaços azure dev no Azure Kubernetes Services
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, CNI, kubenet, SDN, rede
ms.openlocfilehash: 3e344576caf276ae7cb5fe00395c84810a4e7d32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262036"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configure a rede para espaços azure dev em diferentes topoologias de rede

O Azure Dev Spaces é executado em clusters Azure Kubernetes Service (AKS) com a configuração padrão de rede. Se você quiser alterar a configuração de rede do seu cluster AKS, como colocar o cluster atrás de um firewall, usar grupos de segurança de rede ou usar políticas de rede, você terá que incorporar considerações adicionais para executar o Azure Dev Spaces.

![Configuração de rede virtual](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurações de rede virtual ou sub-rede

Seu cluster AKS pode ter uma configuração diferente de rede virtual ou sub-rede para restringir o tráfego de ingestão ou saída para o cluster AKS. Por exemplo, seu cluster pode estar por trás de um firewall, como o Azure Firewall, ou você pode usar Grupos de Segurança de Rede ou funções personalizadas para restringir o tráfego de rede. Você pode encontrar um exemplo de configuração de rede no [repositório de amostra Azure Dev Spaces no GitHub][sample-repo].

O Azure Dev Spaces tem certos requisitos para o tráfego de rede *Ingress e Egress,* bem como apenas tráfego *de Ingress.* Se você estiver usando o Azure Dev Spaces em um cluster AKS com uma configuração de rede virtual ou sub-rede que restringe o tráfego para o cluster AKS, você deve seguir apenas os seguintes requisitos de tráfego de ingestão e saída para que os espaços de desenvolvimento do Azure funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de ingresss e egress

O Azure Dev Spaces precisa de tráfego de inserção e saída para os seguintes FQDNs:

| FQDN                       | Porta       | Use      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Para puxar imagens docker para Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Para puxar imagens de leme para a azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Para puxar imagens de leme para a azure Dev Spaces |
| azds-*.azds.io             | HTTPS: 443 | Para se comunicar com os serviços de back-end do Azure Dev Spaces para o controlador Azure Dev Spaces. O FQDN exato pode ser encontrado em *dataplaneFqdn* em`USERPROFILE\.azds\settings.json` |

Atualize sua configuração de firewall ou segurança para permitir o tráfego de rede de e para todos os FQDNs acima. Por exemplo, se você estiver usando um firewall para proteger sua rede, os FQDNs acima devem ser adicionados à regra de aplicação do firewall para permitir o tráfego de e para esses domínios.

### <a name="ingress-only-network-traffic-requirements"></a>Ingress apenas requisitos de tráfego de rede

O Azure Dev Spaces fornece roteamento em nível de namespace do Kubernetes, bem como acesso público a serviços usando seu próprio FQDN. Para que ambos os recursos funcionem, atualize seu firewall ou configuração de rede para permitir a ingestão pública no endereço IP externo do controlador de ingestão Do Azure Dev Spaces em seu cluster. Alternativamente, você pode criar um [balanceador de carga interna][aks-internal-lb] e adicionar uma regra NAT em seu firewall para traduzir o IP público do seu firewall para o IP do seu balanceador de carga interna. Você também pode usar [traefik][traefik-ingress] ou [NGINX][nginx-ingress] para criar um controlador de ingestão personalizado.

## <a name="aks-cluster-network-requirements"></a>Requisitos de rede de cluster AKS

O AKS permite que você use políticas de [rede][aks-network-policies] para controlar o tráfego de ingestão e saída entre pods em um cluster, bem como o tráfego de saída de um pod. O Azure Dev Spaces tem certos requisitos para o tráfego de rede *Ingress e Egress,* bem como apenas tráfego *de Ingress.* Se você estiver usando o Azure Dev Spaces em um cluster AKS com políticas de rede AKS, você deve seguir os seguintes requisitos de tráfego de ingestão e saída para que os espaços de desenvolvimento do Azure funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de ingresss e egress

O Azure Dev Spaces permite que você se comunique diretamente com um pod em um espaço de vv em seu cluster para depuração. Para que esse recurso funcione, adicione uma política de rede que permita a ingestão e a comunicação de saída aos endereços IP da infra-estrutura Azure Dev Spaces, que [variam de acordo com a região.][dev-spaces-ip-auth-range-regions]

### <a name="ingress-only-network-traffic-requirements"></a>Ingress apenas requisitos de tráfego de rede

O Azure Dev Spaces fornece roteamento entre pods através de namespaces. Por exemplo, os namespaces com o Azure Dev Spaces ativado podem ter uma relação pai/filho, o que permite que o tráfego de rede seja roteado entre pods nos espaços de nome dos pais e dos filhos. O Azure Dev Spaces também expõe pontos finais de serviço usando seu próprio FQDN. Para configurar diferentes formas de expor serviços e como ele afeta o roteamento do nível de [namespace, consulte usando diferentes opções de ponto final][endpoint-options].

## <a name="using-azure-cni"></a>Usando a CNI do Azure

Por padrão, os clusters AKS são configurados para usar [kubenet][aks-kubenet] para rede, que funciona com o Azure Dev Spaces. Você também pode configurar seu cluster AKS para usar [a CNI (Azure Container Networking Interface, interface de rede de contêineres do Azure).][aks-cni] Para usar o Azure Dev Spaces com o Azure CNI no cluster AKS, permita que sua rede virtual e espaços de endereços de sub-rede até 10 endereços IP privados para pods implantados pelo Azure Dev Spaces. Mais detalhes sobre a autorização de endereços IP privados estão disponíveis na [documentação da CNI AKS Azure][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Usando faixas IP autorizadas pelo servidor API

Os clusters AKS permitem configurar segurança adicional que limita qual endereço IP pode interagir com seus clusters, por exemplo, usando redes virtuais personalizadas ou [garantindo acesso ao servidor de API usando faixas IP autorizadas][aks-ip-auth-ranges]. Para usar o Azure Dev Spaces ao usar essa segurança adicional ao [criar][aks-ip-auth-range-create] seu cluster, você deve [permitir faixas adicionais com base na sua região][dev-spaces-ip-auth-range-regions]. Você também pode [atualizar][aks-ip-auth-range-update] um cluster existente para permitir essas faixas adicionais. Você também precisa permitir que o endereço IP de quaisquer máquinas de desenvolvimento que se conectem ao seu cluster AKS para depuração se conecte ao seu servidor de API.

## <a name="using-aks-private-clusters"></a>Usando clusters privados AKS

Neste momento, o Azure Dev Spaces não é suportado com [clusters privados AKS][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Usando diferentes opções de ponto final

O Azure Dev Spaces tem a opção de expor pontos finais para seus serviços em execução em AKS. Ao ativar o Azure Dev Spaces em seu cluster, você tem as seguintes opções para configurar o tipo de ponto final para o cluster:

* Um ponto final *público,* que é o padrão, implanta um controlador de ingestão com um endereço IP público. O endereço IP público está registrado no DNS do cluster, permitindo o acesso público aos seus serviços usando uma URL. Você pode visualizar `azds list-uris`esta URL usando .
* Um ponto final *privado* implanta um controlador de ingestão com um endereço IP privado. Com um endereço IP privado, o balanceador de carga para o cluster só é acessível a partir de dentro da rede virtual do cluster. O endereço IP privado do balanceador de carga é registrado no DNS do cluster para que os serviços dentro da rede virtual do cluster possam ser acessados usando uma URL. Você pode visualizar `azds list-uris`esta URL usando .
* A *definição de nenhum* para a opção de ponto final faz com que nenhum controlador de ingestão seja implantado. Sem o controlador de ingress implantado, os [recursos de roteamento do Azure Dev Spaces][dev-spaces-routing] não funcionarão. Opcionalmente, você pode implementar sua própria solução de controlador de ingress usando [traefik][traefik-ingress] ou [NGINX,][nginx-ingress]o que permitirá que os recursos de roteamento funcionem novamente.

Para configurar sua opção de ponto final, use *-e* ou *--ponto final* ao ativar espaços de dev do Azure em seu cluster. Por exemplo:

> [!NOTE]
> A opção de ponto final requer que você esteja executando a versão 2.2.0 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Requisitos do cliente

O Azure Dev Spaces usa ferramentas do lado do cliente, como a extensão CLI do Azure Dev Spaces, a extensão Visual Studio Code e a extensão Visual Studio, para se comunicar com o cluster AKS para depuração. Para usar as ferramentas do lado do cliente do Azure Dev Spaces, permita o tráfego das máquinas de desenvolvimento para o domínio *azds-\*.azds.io.* Consulte *dataplaneFqdn* para `USERPROFILE\.azds\settings.json` obter o Exato FQDN. Se usar [faixas IP autorizadas pelo servidor API,][auth-range-section]você também precisa permitir que o endereço IP de quaisquer máquinas de desenvolvimento que se conectem ao seu cluster AKS para depuração se conecte ao servidor de API.

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[team-quickstart]: quickstart-team-development.md