---
title: Perguntas frequentes sobre a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Encontre respostas para algumas das perguntas comuns sobre a Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: b5a380f20640b9bc328aa30289ff7f915cc0b73c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414304"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Perguntas frequentes sobre a Azure Dev Spaces

Isso aborda perguntas frequentes sobre o Azure Dev Spaces.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Quais versões do Kubernetes são suportadas para a Zure Dev Spaces?

O Azure Dev Spaces suporta todas as [versões de disponibilidade geral (GA) atualmente suportadas do Kubernetes em AKS][aks-supported-k8s].

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quais regiões do Azure atualmente fornecem espaços Azure Dev?

Consulte [as regiões suportadas][supported-regions] para obter uma lista completa das regiões disponíveis.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Posso migrar meu cluster AKS com o Azure Dev Spaces para outra região?

Sim, se você quiser mover seu cluster AKS com o Azure Dev Spaces para outra [região suportada,][supported-regions]recomendamos que você crie um novo cluster na outra região, em seguida, instale e configure espaços de desenvolvimento do Azure e implante seus recursos e aplicativos para o seu novo cluster. Para obter mais informações sobre a migração de AKS, consulte [Migrar para o Azure Kubernetes Service (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Posso usar o Azure Dev Spaces com arquivos Docker ou gráficos Helm existentes?

Sim, se o seu projeto já tiver um Dockerfile ou um gráfico Helm, você pode usar esses arquivos com o Azure Dev Spaces. Ao executar, `azds prep`use `--chart` o parâmetro e especifique a localização do gráfico. O Azure Dev Spaces ainda gerará um arquivo *azds.yaml* e *Dockerfile.develop,* mas não substituirá ou modificará um arquivo Docker existente ou um gráfico Helm. Você pode precisar modificar os *arquivos azds.yaml* e *Dockerfile.develop* para que tudo funcione corretamente com o aplicativo existente ao executar `azds up`.

Ao usar seu próprio gráfico Dockerfile ou Helm, existem as seguintes limitações:
* Se usar apenas um Arquivo Docker, ele deve incluir tudo o que você precisa para habilitar cenários de desenvolvimento, como o SDK de idioma não apenas o tempo de execução. Se usar um arquivo Docker separado para espaços de desenvolvimento do Azure, como um Dockerfile.develop, tudo o que você precisa para habilitar cenários de desenvolvimento deve ser incluído nesse Arquivo Docker.
* O gráfico Helm deve suportar a passagem de parte ou de toda a tag de imagem como um valor de *values.yaml*.
* Se você estiver modificando qualquer coisa com o ingresss, você também pode atualizar seu gráfico Helm para usar a solução de ingressa fornecida pelo Azure Dev Spaces.
* Se você quiser usar os [recursos de roteamento fornecidos pelo Azure Dev Spaces,][dev-spaces-routing]todos os serviços para um projeto individual devem caber em um único espaço de nome Kubernetes e devem ser implantados com nomeação simples, por *exemplo, service-a*. Nos gráficos padrão do Helm, essa atualização de nomeação pode ser feita especificando um valor para a propriedade *fullnameOverride.*

Para comparar seu próprio gráfico Dockerfile ou Helm com uma versão existente que funciona com o Azure Dev Spaces, revise os arquivos gerados no [quickstart][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Posso modificar os arquivos gerados pelo Azure Dev Spaces?

Sim, você pode modificar o arquivo *azds.yaml,* dockerfile e helm [chart gerado pelo Azure Dev Spaces ao preparar seu projeto][dev-spaces-prep]. Modificar esses arquivos muda a forma como o projeto é construído e executado.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Posso usar o Azure Dev Spaces sem um endereço IP público?

Não, você não pode provisionar espaços azure dev em um cluster AKS sem um IP público. Um IP público é [necessário pelo Azure Dev Spaces para roteamento][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Posso usar minha própria ingestão com a Azure Dev Spaces?

Sim, você pode configurar sua própria ingressa junto com a ingestão que o Azure Dev Spaces cria. Por exemplo, você pode usar [traefik][ingress-traefik] ou [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Posso usar HTTPS com Espaços Dev Do Zure?

Sim, você pode configurar sua própria ingresscom HTTPS usando [traefik][ingress-https-traefik] ou [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Posso usar o Azure Dev Spaces em um cluster que usa CNI em vez de kubenet? 

Sim, você pode usar o Azure Dev Spaces em um cluster AKS que usa a CNI para rede. Por exemplo, você pode usar o Azure Dev Spaces em um cluster AKS com [contêineres Windows existentes,][windows-containers]que usa CNI para rede. Mais informações sobre o uso da CNI para networking com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Posso usar o Azure Dev Spaces com contêineres windows?

Atualmente, o Azure Dev Spaces destina-se a ser executado apenas em pods e nós Linux, mas você pode executar o Azure Dev Spaces em um cluster AKS com [contêineres Windows existentes][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Posso usar o Azure Dev Spaces em clusters AKS com intervalos de endereços IP autorizados do servidor API ativados?

Sim, você pode usar espaços de dev do Azure em clusters AKS com [intervalos de endereços IP autorizados pelo servidor API ativados.][aks-auth-range] Mais informações sobre o uso de clusters AKS com faixas de endereçoIP autorizadas pelo servidor API habilitadas com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Posso usar espaços azure dev em clusters AKS com tráfego de saída restrito para nós de cluster?

Sim, você pode usar espaços de dev do Azure em clusters AKS com tráfego de saída restrito para nós de cluster habilitados uma vez que os FQDNs corretos tenham sido [permitidos.][aks-restrict-egress-traffic] Mais informações sobre o uso de clusters AKS com tráfego de saída restrito para nós de cluster habilitados com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Posso usar espaços Azure Dev em clusters AKS habilitados para RBAC?

Sim, você pode usar espaços azure dev em clusters AKS com ou sem RBAC ativado.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>O que acontece quando eu habilitar a ingestão para projeto no Visual Studio?

Ao usar o Visual Studio para preparar seu projeto, você tem a opção de habilitar a ingestão para o seu serviço. A ativação cria um ponto final público para acessar seu serviço ao ser executado no cluster AKS, que é opcional. Se você não habilitar a ingestão, seu serviço só será acessível dentro do seu cluster AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Posso usar identidades gerenciadas por pod com o Azure Dev Spaces?

Sim, você pode usar [identidades gerenciadas de pod][aks-pod-managed-id] em clusters AKS com o Azure Dev Spaces ativado, mas existem [etapas adicionais de configuração][dev-spaces-pod-managed-id-steps] depois de habilitar o Azure Dev Spaces em seu cluster com identidades gerenciadas por pod. Se você tiver identidades gerenciadas de pod instaladas e quiser desinstalá-la, você poderá encontrar mais detalhes nas [notas de desinstalação][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Posso usar o Azure Dev Spaces com vários microsserviços em um aplicativo?

Sim, você pode usar o Azure Dev Spaces em um aplicativo com vários microsserviços, mas você deve preparar e executar os microsserviços individuais em sua raiz. A cli do Azure Dev Spaces, a extensão Azure Dev Spaces VS Code e a carga de trabalho do Visual Studio Azure Development esperam que o arquivo *azds.yaml* esteja na raiz do microserviço para ser executado e depurar. Consulte o aplicativo de exemplo da amostra de compartilhamento de [bicicletas][bike-sharing] para obter um exemplo de vários microserviços em um único aplicativo.

No Visual Studio Code, é possível [abrir projetos separados em um único espaço][vs-code-multi-root-workspaces] de trabalho e depura-los separadamente através do Azure Dev Spaces. Cada um dos projetos deve ser independente e preparado para o Azure Dev Spaces.

No Visual Studio, é possível configurar soluções .NET Core para depuração através do Azure Dev Spaces.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Posso usar o Azure Dev Spaces com uma malha de serviço?

Neste momento, você não pode usar a Azure Dev Spaces com coordenadas de serviço como [Istio][istio] ou [Linkerd][linkerd]. Você pode executar o Azure Dev Spaces e uma tela de serviço no mesmo cluster AKS, mas você não pode ter tanto o Azure Dev Spaces quanto uma malha de serviço ativada no mesmo namespace.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#list-currently-supported-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md