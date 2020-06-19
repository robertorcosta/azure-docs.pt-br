---
title: Restringir o tráfego de saída no Serviço de Kubernetes do Azure (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saída no Serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 194e799daf107220c28404001d223e521dceeb3f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870895"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controlar o tráfego de saída dos nós de cluster no Serviço de Kubernetes do Azure (AKS)

Por padrão, os clusters do AKS têm acesso irrestrito de internet de saída. Esse nível de acesso à rede permite que os nós e os serviços que você executa acessem recursos externos, conforme necessário. Se quiser restringir o tráfego de saída, um número limitado de portas e endereços precisar estar acessível para manter a integridade das tarefas de manutenção de cluster. O cluster é configurado por padrão para usar apenas as imagens de contêiner do sistema base do Registro de Contêiner da Microsoft (MCR) ou o Registro de Contêiner do Azure (ACR). Configure o firewall e as regras de segurança de sua preferência para permitir as portas e os endereços necessários.

Este artigo detalha quais portas de rede e nomes de domínio totalmente qualificados (FQDNs) são obrigatórios e opcionais se você restringir o tráfego de saída em um cluster do AKS.

> [!IMPORTANT]
> Este documento aborda apenas como bloquear o tráfego de saída da sub-rede do AKS. O AKS não tem nenhum requisito de entrada.  Não há suporte para o bloqueio de tráfego de sub-rede interna que usam grupos de segurança de rede (NSGs) e firewalls. Para controlar e bloquear o tráfego no cluster, use [Políticas de rede][network-policy].

## <a name="before-you-begin"></a>Antes de começar

Você precisará da CLI do Azure versão 2.0.66 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de saída

Para fins operacionais e de gerenciamento, os nós em um cluster do AKS precisam acessar portas e nomes de domínio totalmente qualificados (FQDNs) específicos. Essas ações podem ser comunicadas com o servidor de API ou para baixar e depois instalar os componentes principais do cluster e atualizações de segurança de nó do Kubernetes. Por padrão, o tráfego de saída da Internet não é restrito para nós em um cluster do AKS. O cluster pode extrair imagens de contêiner do sistema base de repositórios externos.

Para aumentar a segurança do cluster do AKS, pode ser necessário restringir o tráfego de saída. O cluster está configurado para extrair imagens de contêiner do sistema base do MCR ou do ACR. Se você bloquear o tráfego de saída dessa maneira, defina portas e FQDNs específicos para permitir que os nós AKS se comuniquem corretamente com os serviços externos necessários. Sem essas portas e esses FQDNs autorizados, seus nós do AKS não poderão se comunicar com o servidor de API nem instalar componentes principais.

Você pode usar o [Firewall do Azure][azure-firewall] ou um dispositivo de firewall de terceiros para proteger o tráfego de saída e definir as portas e os endereços necessários. O AKS não cria automaticamente essas regras para você. As portas e os endereços a seguir são sua referência ao criar as regras apropriadas no firewall de rede.

> [!IMPORTANT]
> Ao usar o Firewall do Azure para restringir o tráfego de saída e criar uma rota definida pelo usuário (UDR) para forçar todo o tráfego de saída, crie uma regra DNAT apropriada no firewall para permitir corretamente o tráfego de entrada. Usar o Firewall do Azure com uma UDR interrompe a configuração de entrada devido ao roteamento assimétrico. (O problema ocorre se a sub-rede do AKS tiver uma rota padrão para o endereço IP privado do firewall e você estiver usando um balanceador de carga de entrada ou serviço do tipo do Kubernetes: LoadBalancer). Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoramento de estado, ele descarta o pacote de retorno porque o firewall não está ciente de uma sessão estabelecida. Para saber como integrar o Firewall do Azure com o balanceador de carga de serviço ou de entrada, confira [Integrar o Firewall do Azure com o Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Você pode bloquear o tráfego para a porta TCP 9000, a porta TCP 22 e a porta UDP 1194 com uma regra de rede entre os IPs do nó de trabalho de saída e o IP para o servidor de API.

No AKS, há dois conjuntos de portas e endereços:

* As [portas necessárias e o endereço para clusters do AKS](#required-ports-and-addresses-for-aks-clusters) detalham os requisitos mínimos para o tráfego de saída autorizado.
* Os [endereços e portas recomendados opcionais para clusters do AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) não são necessários em todos os cenários, mas a integração com outros serviços, como o Azure Monitor, não funcionará corretamente. Examine esta lista de portas opcionais e FQDNs e autorize todos os serviços e componentes usados no seu cluster do AKS.

> [!NOTE]
> Limitar o tráfego de saída funciona apenas em novos clusters do AKS. Para os clusters existentes, [execute uma operação de atualização de cluster][aks-upgrade] usando o comando `az aks upgrade` antes de limitar o tráfego de saída.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portas e endereços necessários para clusters do AKS

As seguintes regras de rede/portas de saída são necessárias para um cluster do AKS:

* Porta TCP *443*
* TCP [IPAddrOfYourAPIServer]:443 é necessário se você tiver um aplicativo que precise se comunicar com o servidor da API.  Essa alteração pode ser definida depois que o cluster for criado.
* A porta TCP *9000*, a porta TCP *22* e a porta UDP *1194* do pod frontal do túnel para se comunicar com a extremidade do túnel no servidor de API.
    * Para obter mais especificações, consulte os endereços * *.hcp.\<location\>.azmk8s.io* e * *.tun.\<location\>.azmk8s.io* na tabela a seguir.
* A porta UDP *123* para sincronização de tempo do protocolo de tempo de rede (NTP) (nós do Linux).
* A porta UDP *53* para DNS também será necessária se você tiver pods que acessam diretamente o servidor de API.

As seguintes regras de FQDN/aplicativo são obrigatórias:

> [!IMPORTANT]
> * **.blob.core.windows.net e aksrepos.azurecr.io** não são mais regras de FQDN necessárias para bloqueio de saída.  Para os clusters existentes, [execute uma operação de atualização de cluster][aks-upgrade] com o comando `az aks upgrade` para remover essas regras.

> [!IMPORTANT]
> *.cdn.mscr.io foi substituído por *.data.mcr.microsoft.com nas regiões de nuvem pública do Azure. Atualize suas regras de firewall existentes para que as alterações entrem em vigor.

- Azure Global

| FQDN                       | Porta      | Use      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Esse endereço é necessário para a comunicação do nó <-> servidor de API. Substitua *\<location\>* pela região em que o cluster do AKS está implantado. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Esse endereço é necessário para a comunicação do nó <-> servidor de API. Substitua *\<location\>* pela região em que o cluster do AKS está implantado. |
| *.cdn.mscr.io       | HTTPS:443 | Esse endereço é necessário para o armazenamento do MCR apoiado pela Rede de Distribuição de Conteúdo (CDN). |
| mcr.microsoft.com          | HTTPS:443 | Esse endereço é necessário para acessar imagens no Registro de Contêiner do Azure (MCR). Esse registro contém imagens/gráficos próprios (ex.: moby, etc.) necessários para o funcionamento do cluster durante a atualização e dimensionamento do cluster |
| *.data.mcr.microsoft.com             | HTTPS:443 | Esse endereço é necessário para o armazenamento do MCR apoiado pela Rede de Distribuição de Conteúdo (CDN). |
| management.azure.com       | HTTPS:443 | Esse endereço é necessário para operações GET/PUT do Kubernetes. |
| login.microsoftonline.com  | HTTPS:443 | Esse endereço é necessário para a autenticação do Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Esse endereço é necessário para a sincronização de horário do NTP em nós do Linux. |
| packages.microsoft.com     | HTTPS:443 | Esse endereço é o repositório de pacotes da Microsoft usado para as operações *app-get*.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |
| acs-mirror.azureedge.net      | HTTPS:443 | Esse endereço é para o repositório necessário para instalar os binários necessários, como kubenet e CNI do Azure. |

- Azure China 21Vianet

| FQDN                       | Porta      | Use      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Esse endereço é necessário para a comunicação do nó <-> servidor de API. Substitua *\<location\>* pela região em que o cluster do AKS está implantado. |
| *.tun.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Esse endereço é necessário para a comunicação do nó <-> servidor de API. Substitua *\<location\>* pela região em que o cluster do AKS está implantado. |
| *.azk8s.cn        | HTTPS:443 | Esse endereço é necessário para baixar os binários e as imagens necessários|
| mcr.microsoft.com          | HTTPS:443 | Esse endereço é necessário para acessar imagens no Registro de Contêiner do Azure (MCR). Esse registro contém imagens/gráficos próprios (ex.: moby, etc.) necessários para o funcionamento do cluster durante a atualização e dimensionamento do cluster |
| *.cdn.mscr.io       | HTTPS:443 | Esse endereço é necessário para o armazenamento do MCR apoiado pela Rede de Distribuição de Conteúdo (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Esse endereço é necessário para o armazenamento do MCR apoiado pela Rede de Distribuição de Conteúdo (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Esse endereço é necessário para operações GET/PUT do Kubernetes. |
| login.chinacloudapi.cn  | HTTPS:443 | Esse endereço é necessário para a autenticação do Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Esse endereço é necessário para a sincronização de horário do NTP em nós do Linux. |
| packages.microsoft.com     | HTTPS:443 | Esse endereço é o repositório de pacotes da Microsoft usado para as operações *app-get*.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |

- Azure Government

| FQDN                       | Porta      | Use      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Esse endereço é necessário para a comunicação do nó <-> servidor de API. Substitua *\<location\>* pela região em que o cluster do AKS está implantado. |
| *.tun.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Esse endereço é necessário para a comunicação do nó <-> servidor de API. Substitua *\<location\>* pela região em que o cluster do AKS está implantado. |
| mcr.microsoft.com          | HTTPS:443 | Esse endereço é necessário para acessar imagens no Registro de Contêiner do Azure (MCR). Esse registro contém imagens/gráficos próprios (ex.: moby, etc.) necessários para o funcionamento do cluster durante a atualização e dimensionamento do cluster |
|*.cdn.mscr.io              | HTTPS:443 | Esse endereço é necessário para o armazenamento do MCR apoiado pela Rede de Distribuição de Conteúdo (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Esse endereço é necessário para o armazenamento do MCR apoiado pela Rede de Distribuição de Conteúdo (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Esse endereço é necessário para operações GET/PUT do Kubernetes. |
| login.microsoftonline.us  | HTTPS:443 | Esse endereço é necessário para a autenticação do Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Esse endereço é necessário para a sincronização de horário do NTP em nós do Linux. |
| packages.microsoft.com     | HTTPS:443 | Esse endereço é o repositório de pacotes da Microsoft usado para as operações *app-get*.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |
| acs-mirror.azureedge.net      | HTTPS:443 | Esse endereço é para o repositório necessário para instalar os binários necessários, como kubenet e CNI do Azure. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Endereços e portas recomendados opcionais para clusters do AKS

As seguintes regras de rede/portas de saída são opcionais para um cluster do AKS:

As seguintes regras de FQDN/aplicativo são recomendadas para que os clusters do AKS funcionem corretamente:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Esse endereço permite que os nós de cluster do Linux baixem as atualizações e os patches de segurança necessários. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Endereços e portas necessários para clusters do AKS habilitados para GPU

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS com GPU habilitada:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Esse endereço é usado para a instalação e a operação corretas do driver nos nós baseados em GPU. |
| us.download.nvidia.com | HTTPS:443 | Esse endereço é usado para a instalação e a operação corretas do driver nos nós baseados em GPU. |
| apt.dockerproject.org | HTTPS:443 | Esse endereço é usado para a instalação e a operação corretas do driver nos nós baseados em GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Endereços e portas necessários com o Azure Monitor para contêineres habilitados

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Monitor para contêineres habilitado:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Telemetria de monitoramento e métricas corretas usando o Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Usada pelo Azure Monitor para ingerir dados de análise de logs. |
| *.oms.opinsights.azure.com | HTTPS:443 | Esse endereço é usado pelo omsagent para autenticar o serviço de análise de logs. |
|*.microsoftonline.com | HTTPS:443 | Usada para autenticar e enviar métricas ao Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Usada para enviar dados de métricas ao Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Endereços e portas necessários com o Azure Dev Spaces habilitado

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Dev Spaces para contêineres habilitado:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Este endereço é usado para extrair imagens do Linux Alpine e outras do Azure Dev Spaces |
| gcr.io | HTTPS:443 | Esse endereço é usado para efetuar pull de imagens helm/tiller |
| storage.googleapis.com | HTTPS:443 | Esse endereço é usado para efetuar pull de imagens helm/tiller |
| azds-\<guid\>.\<location\>.azds.io | HTTPS:443 | Para se comunicar com os serviços de back-end do Azure Dev Spaces para seu controlador. O FQDN exato pode ser encontrado em "dataplaneFqdn" em %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Endereços e portas necessários para clusters do AKS com Azure Policy (em visualização pública) habilitados

> [!CAUTION]
> Alguns dos recursos abaixo estão em versão prévia.  As sugestões neste artigo poderão sofrer alterações à medida que o recurso passar para visualização pública e fases de versão futuras.

As seguintes regras de FQDN/aplicativo são necessárias para clusters do AKS que têm o Azure Policy habilitado.

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Esse endereço é usado para que o Azure Policy funcione corretamente. (atualmente em versão prévia no AKS) |
| raw.githubusercontent.com | HTTPS:443 | Esse endereço é usado para efetuar pull das políticas internas do GitHub para garantir que o Azure Policy funcione corretamente. (atualmente em versão prévia no AKS) |
| *.gk.\<location\>.azmk8s.io | HTTPS:443    | Complemento do Azure Policy que se comunica com o ponto de extremidade de auditoria do Gatekeeper em execução no servidor mestre para obter os resultados da auditoria. |
| dc.services.visualstudio.com | HTTPS:443 | Complemento do Azure Policy que envia dados telemétricos para pontos de extremidade de insights de aplicativo. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Exigido por nós habilitados baseados no Windows Server

As seguintes regras de FQDN/aplicativo são necessárias para usar pools de nós baseados no Windows Server:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Para instalar binários relacionados ao Windows |
| mp.microsoft.com, www<span></span>.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Para instalar binários relacionados ao Windows |
| kms.core.windows.net | TCP:1688 | Para instalar binários relacionados ao Windows |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu quais portas e endereços permitir ao restringir o tráfego de saída para o cluster. Você também pode definir como os pods, por si só, podem se comunicar e quais são as restrições deles dentro do cluster. Para mais informações, consulte [Proteger o tráfego entre os pods usando as políticas de rede no AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
