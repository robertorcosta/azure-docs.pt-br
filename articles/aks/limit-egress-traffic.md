---
title: Restringir o tráfego de saída no Azure Kubernetes Service (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saída no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080183"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controle o tráfego de saída para os nós de cluster no Azure Kubernetes Service (AKS)

Por padrão, os clusters AKS têm acesso à internet de saída irrestrita (saída). Esse nível de acesso à rede permite que os nós e serviços executados para acessar recursos externos, conforme necessário. Se você deseja restringir o tráfego de saída, um número limitado de portas e endereços deve ser acessível para manter tarefas saudáveis de manutenção de cluster. Seu cluster é configurado por padrão para usar apenas imagens de contêiner do sistema base do Microsoft Container Registry (MCR) ou do Acr (ACR) do Azure Container Registry (ACR). Configure suas regras de firewall e segurança preferidas para permitir essas portas e endereços necessários.

Este artigo detalha quais portas de rede e nomes de domínio totalmente qualificados (FQDNs) são necessários e opcionais se você restringir o tráfego de saída em um cluster AKS.

> [!IMPORTANT]
> Este documento abrange apenas como bloquear o tráfego saindo da sub-rede AKS. A AKS não tem requisitos de ingestão.  O bloqueio do tráfego interno de sub-redes usando NSGs (Network Security Groups, grupos de segurança de rede) e firewalls não é suportado. Para controlar e bloquear o tráfego dentro do cluster, use [Políticas de rede][network-policy].

## <a name="before-you-begin"></a>Antes de começar

Você precisa da versão 2.0.66 do Azure CLI ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de saída

Para fins de gestão e operação, os nodos em um cluster AKS precisam acessar determinadas portas e nomes de domínio totalmente qualificados (FQDNs). Essas ações podem ser para se comunicar com o servidor de API, ou para baixar e, em seguida, instalar componentes de cluster kubernetes principais e atualizações de segurança de nós. Por padrão, o tráfego de internet de saída não é restrito para nomes em um cluster AKS. O cluster pode puxar imagens de recipiente do sistema básico de repositórios externos.

Para aumentar a segurança do seu cluster AKS, você pode querer restringir o tráfego de saída. O cluster é configurado para puxar imagens de contêiner do sistema base de MCR ou ACR. Se você bloquear o tráfego de saída desta maneira, defina portas específicas e FQDNs para permitir que os álos AKS se comuniquem corretamente com os serviços externos necessários. Sem essas portas autorizadas e FQDNs, seus nós AKS não podem se comunicar com o servidor aPI ou instalar componentes principais.

Você pode usar [o Firewall Azure][azure-firewall] ou um aparelho de firewall de terceiros para proteger o tráfego de saída e definir essas portas e endereços necessários. O AKS não cria automaticamente essas regras para você. As seguintes portas e endereços são para referência à medida que você cria as regras apropriadas em seu firewall de rede.

> [!IMPORTANT]
> Quando você usar o Azure Firewall para restringir o tráfego de saída e criar uma rota definida pelo usuário (UDR) para forçar todo o tráfego de saída, certifique-se de criar uma regra de DNAT apropriada no Firewall para permitir corretamente o tráfego de entrada. O uso do Firewall Azure com um UDR interrompe a configuração de ingestão devido ao roteamento assimétrico. (O problema ocorre se a sub-rede AKS tiver uma rota padrão que vai para o endereço IP privado do firewall, mas você está usando um balanceador de carga pública - entrada ou serviço Kubernetes do tipo: LoadBalancer). Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall é estadual, ele descarta o pacote de retorno porque o firewall não está ciente de uma sessão estabelecida. Para saber como integrar o Azure Firewall com o seu balanceador de carga de serviço ou de serviço, consulte [Integrar o Firewall Azure com o Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Você pode bloquear o tráfego para a porta TCP 9000, a porta TCP 22 e a porta UDP 1194 usando uma regra de rede entre os IP(s) do nó do trabalhador egresso e o IP para o servidor aPI.

No AKS, existem dois conjuntos de portas e endereços:

* As [portas e endereços necessários para clusters AKS](#required-ports-and-addresses-for-aks-clusters) detalham os requisitos mínimos para o tráfego autorizado de saída.
* Os [endereços e portas recomendados opcionais para clusters AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) não são necessários para todos os cenários, mas a integração com outros serviços, como o Azure Monitor, não funcionará corretamente. Revise esta lista de portas opcionais e FQDNs e autorize qualquer um dos serviços e componentes usados em seu cluster AKS.

> [!NOTE]
> Limitar o tráfego de saída só funciona em novos clusters AKS. Para clusters existentes, [execute uma operação de upgrade de cluster][aks-upgrade] usando o `az aks upgrade` comando antes de limitar o tráfego de saída.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portas e endereços necessários para clusters do AKS

As seguintes portas de saída / regras de rede são necessárias para um cluster AKS:

* Porta TCP *443*
* TCP [IPAddrOfYourAPIServer]:443 é necessário se você tiver um aplicativo que precisa falar com o servidor de API.  Essa alteração pode ser definida após a criação do cluster.
* Porta TCP *9000,* porta TCP *22* e porta UDP *1194* para que a cápsula frontal do túnel se comunique com o fim do túnel no servidor api.
    * Para ser mais específico, consulte o **.hcp.\< localização\>.azmk8s.io* e **\< .tun. localização\>.azmk8s.io* endereços na tabela a seguir.
* Porta UDP *123* para sincronização de tempo do Protocolo de Tempo de Rede (NTP) (nós Linux).
* A porta UDP *53* para DNS também é necessária se você tiver pods acessando diretamente o servidor API.

As seguintes regras fqdn / aplicativo são necessárias:

> [!IMPORTANT]
> ***.blob.core.windows.net e aksrepos.azurecr.io** não são mais necessárias regras fqdn para bloqueio de saída.  Para clusters existentes, [execute uma operação de upgrade de cluster][aks-upgrade] usando o `az aks upgrade` comando para remover essas regras.

> [!IMPORTANT]
> *.cdn.mscr.io foi substituído por *.data.mcr.microsoft.com para as regiões de nuvem pública do Azure. Atualize as regras de firewall existentes para que as alterações entrem em vigor.

- Azure Global

| FQDN                       | Porta      | Use      |
|----------------------------|-----------|----------|
| *.hcp. \<localização\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API > no node <. Substitua * \<\> * a localização pela região onde o cluster AKS é implantado. |
| *.tun. \<localização\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API > no node <. Substitua * \<\> * a localização pela região onde o cluster AKS é implantado. |
| *.cdn.mscr.io       | HTTPS:443 | Este endereço é necessário para o armazenamento MCR apoiado pelo CDN (Azure Content Delivery Network, rede de entrega de conteúdo do Azure). |
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para acessar imagens no Microsoft Container Registry (MCR). Este registro contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para o armazenamento MCR apoiado pela Rede de Entrega de Conteúdo (CDN) do Azure. |
| management.azure.com       | HTTPS:443 | Este endereço é necessário para as operações Kubernetes GET/PUT. |
| login.microsoftonline.com  | HTTPS:443 | Este endereço é necessário para a autenticação do Diretório Ativo do Azure. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para sincronização de tempo NTP em nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes microsoft usado para operações *de apt-get em* cache.  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Este endereço é para o repositório necessário para instalar binários necessários como kubenet e Azure CNI. |

- Azure China 21Vianet

| FQDN                       | Porta      | Use      |
|----------------------------|-----------|----------|
| *.hcp. \<localização\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API > no node <. Substitua * \<\> * a localização pela região onde o cluster AKS é implantado. |
| *.tun. \<localização\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API > no node <. Substitua * \<\> * a localização pela região onde o cluster AKS é implantado. |
| *.azk8s.cn        | HTTPS:443 | Este endereço é necessário para baixar binários e imagens necessários|
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para acessar imagens no Microsoft Container Registry (MCR). Este registro contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
| *.cdn.mscr.io       | HTTPS:443 | Este endereço é necessário para o armazenamento MCR apoiado pelo CDN (Azure Content Delivery Network, rede de entrega de conteúdo do Azure). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para o armazenamento MCR apoiado pela Rede de Entrega de Conteúdo (CDN) do Azure. |
| management.chinacloudapi.cn       | HTTPS:443 | Este endereço é necessário para as operações Kubernetes GET/PUT. |
| login.chinacloudapi.cn  | HTTPS:443 | Este endereço é necessário para a autenticação do Diretório Ativo do Azure. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para sincronização de tempo NTP em nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes microsoft usado para operações *de apt-get em* cache.  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |

- Azure Government

| FQDN                       | Porta      | Use      |
|----------------------------|-----------|----------|
| *.hcp. \<localização\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API > no node <. Substitua * \<\> * a localização pela região onde o cluster AKS é implantado. |
| *.tun. \<localização\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API > no node <. Substitua * \<\> * a localização pela região onde o cluster AKS é implantado. |
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para acessar imagens no Microsoft Container Registry (MCR). Este registro contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
|*.cdn.mscr.io              | HTTPS:443 | Este endereço é necessário para o armazenamento MCR apoiado pelo CDN (Azure Content Delivery Network, rede de entrega de conteúdo do Azure). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para o armazenamento MCR apoiado pela Rede de Entrega de Conteúdo (CDN) do Azure. |
| management.usgovcloudapi.net       | HTTPS:443 | Este endereço é necessário para as operações Kubernetes GET/PUT. |
| login.microsoftonline.us  | HTTPS:443 | Este endereço é necessário para a autenticação do Diretório Ativo do Azure. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para sincronização de tempo NTP em nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes microsoft usado para operações *de apt-get em* cache.  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Este endereço é para o repositório necessário para instalar binários necessários como kubenet e Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Endereços e portas recomendadas opcionais para clusters AKS

As seguintes portas de saída / regras de rede são opcionais para um cluster AKS:

As seguintes regras fqdn / aplicativo são recomendadas para que os clusters AKS funcionem corretamente:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com azure.archive.ubuntu.com, azure.archive.ubuntu.com changelogs.ubuntu.com. | HTTP:80   | Este endereço permite que os nós de cluster do Linux baixem os patches e atualizações de segurança necessários. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Endereços e portas necessários para gpu habilitados clusters AKS

As seguintes regras fqdn / aplicativo são necessárias para clusters AKS que tenham GPU ativado:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Este endereço é usado para a instalação e operação corretas do driver em nomes baseados em GPU. |
| us.download.nvidia.com | HTTPS:443 | Este endereço é usado para a instalação e operação corretas do driver em nomes baseados em GPU. |
| apt.dockerproject.org | HTTPS:443 | Este endereço é usado para a instalação e operação corretas do driver em nomes baseados em GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Endereços e portas necessários com o Monitor Azure para contêineres habilitados

As seguintes regras fqdn / aplicativo são necessárias para clusters AKS que têm o Monitor Azure para contêineres ativado:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Isto é para métricas corretas e telemetria de monitoramento usando o Monitor Azure. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Isso é usado pelo Azure Monitor para ingerir dados de análise de log. |
| *.oms.opinsights.azure.com | HTTPS:443 | Este endereço é usado pelo omsagent, que é usado para autenticar o serviço de análise de log. |
|*.microsoftonline.com | HTTPS:443 | Isso é usado para autenticar e enviar métricas para o Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Isso é usado para enviar dados métricos para o Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Endereços e portas necessários com espaços de dev do Azure ativados

As seguintes regras fqdn / aplicativo são necessárias para clusters AKS que têm os Espaços de Desenvolvimento do Azure ativados:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Este endereço é usado para puxar imagens do Linux Alpine e outras imagens do Azure Dev Spaces |
| gcr.io | HTTP:443 | Este endereço é usado para puxar imagens de leme/leme |
| storage.googleapis.com | HTTP:443 | Este endereço é usado para puxar imagens de leme/leme |
| \<azds-guid\>. \<localização\>.azds.io | HTTPS:443 | Para se comunicar com os serviços de back-end do Azure Dev Spaces para o seu controlador. O FQDN exato pode ser encontrado no "dataplaneFqdn" em %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Endereços e portas necessários para clusters AKS com a Política do Azure (em visualização pública) ativado

> [!CAUTION]
> Alguns dos recursos abaixo estão na pré-visualização.  As sugestões deste artigo estão sujeitas a alterações à medida que o recurso passa para estágios de pré-visualização pública e de lançamento futuro.

As seguintes regras fqdn / aplicativo são necessárias para clusters AKS que têm a Diretiva Azure ativada.

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Este endereço é usado para o funcionamento correto da Política Do Azure. (atualmente em pré-visualização em AKS) |
| raw.githubusercontent.com | HTTPS:443 | Este endereço é usado para puxar as políticas incorporadas do GitHub para garantir o funcionamento correto da Política Do Azure. (atualmente em pré-visualização em AKS) |
| *.gk. \<localização\>.azmk8s.io | HTTPS:443    | Complemento de política do Azure que conversa com o ponto final da auditoria gatekeeper em execução no servidor mestre para obter os resultados da auditoria. |
| dc.services.visualstudio.com | HTTPS:443 | Complemento de política do Azure que envia dados de telemetria para os insights dos aplicativos. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Exigido por vocês baseados no Windows Server (em visualização pública) ativados

> [!CAUTION]
> Alguns dos recursos abaixo estão na pré-visualização.  As sugestões deste artigo estão sujeitas a alterações à medida que o recurso passa para estágios de pré-visualização pública e de lançamento futuro.

As seguintes regras fqdn / aplicativos são necessárias para clusters AKS baseados no Windows Server:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Para instalar binários relacionados ao windows |
| mp.microsoft.com,<span></span>www.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Para instalar binários relacionados ao windows |
| kms.core.windows.net | TCP:1688 | Para instalar binários relacionados ao windows |


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu quais portas e endereços permitem se restringir o tráfego de saída para o cluster. Você também pode definir como os pods podem se comunicar e quais restrições eles têm dentro do cluster. Para obter mais informações, consulte [Tráfego seguro entre pods usando políticas de rede no AKS][network-policy].

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
