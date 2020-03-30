---
title: Personalizar CoreDNS para a aknetes (AKS)
description: Aprenda a personalizar o CoreDNS para adicionar subdomínios ou estender pontos finais de DNS personalizados usando o AkS Service (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595817"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizar o CoreDNS com o Serviço de Kubernetes do Azure

O Azure Kubernetes Service (AKS) usa o projeto [CoreDNS][coredns] para gerenciamento e resolução de DNS de cluster com todos os *clusters 1.12.x* e superiores. Anteriormente, o projeto kube-dns foi usado. Este projeto kube-dns está agora preterido. Para obter mais informações sobre a personalização do CoreDNS e os Kubernetes, consulte a [documentação oficial upstream][corednsk8s].

Como o AKS é um serviço gerenciado, não é possível modificar a configuração principal do CoreDNS (um *CoreFile*). Em vez disso, você usa um Kubernetes *ConfigMap* para substituir as configurações padrão. Para ver o AKS CoreDNS ConfigMaps padrão, use o `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando.

Este artigo mostra como usar o ConfigMaps para opções básicas de personalização do CoreDNS em AKS. Essa abordagem difere da configuração do CoreDNS em outros contextos, como o uso do CoreFile. Verifique a versão do CoreDNS que você está executando, pois os valores de configuração podem mudar entre as versões.

> [!NOTE]
> `kube-dns`ofereceu diferentes [opções de personalização][kubednsblog] através de um mapa de configuração Kubernetes. O CoreDNS **não** é retrocompatível com kube-dns. Quaisquer personalizações que você usou anteriormente devem ser atualizadas para uso com CoreDNS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>O que é suportado/sem suporte

Todos os plugins CoreDNS incorporados são suportados. Não são suportados plugins adicionais/terceiros.

## <a name="rewrite-dns"></a>Reescrever DNS

Um cenário que você tem é executar regravações de nomes DNS em tempo real. No exemplo a `<domain to be written>` seguir, substitua por seu próprio nome de domínio totalmente qualificado. Crie um `corednsms.yaml` arquivo nomeado e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Crie o ConfigMap usando o comando [kubectl apply configmap][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f corednsms.yaml
```

Para verificar se as personalizações foram aplicadas, use o [kubectl get configmaps][kubectl-get] e especifique seus *coredns-custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Agora force o CoreDNS a recarregar o ConfigMap. O comando [kubectl delete pod][kubectl delete] não é destrutivo e não causa tempo de inatividade. Os `kube-dns` pods são excluídos, e o Agendador Kubernetes os recria. Estes novos pods contêm a alteração no valor TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> O comando acima está correto. Enquanto estamos `coredns`mudando, a implantação está o nome **kube-dns.**

## <a name="custom-forward-server"></a>Servidor avançado personalizado

Se você precisar especificar um servidor avançado para o tráfego de rede, você pode criar um ConfigMap para personalizar o DNS. No exemplo a seguir, atualize o nome e o `forward` endereço com os valores para o seu próprio ambiente. Crie um `corednsms.yaml` arquivo nomeado e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl apply configmap][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap usando o [pod kubectl delete][kubectl delete] para o Agendador Kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Use domínios personalizados

Você pode querer configurar domínios personalizados que só podem ser resolvidos internamente. Por exemplo, você pode querer resolver o domínio personalizado *puglife.local*, que não é um domínio de nível superior válido. Sem um domínio personalizado ConfigMap, o cluster AKS não pode resolver o endereço.

No exemplo a seguir, atualize o domínio personalizado e o endereço IP para direcionar o tráfego com os valores para o seu próprio ambiente. Crie um `corednsms.yaml` arquivo nomeado e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl apply configmap][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap usando o [pod kubectl delete][kubectl delete] para o Agendador Kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domínios stub

O CoreDNS também pode ser usado para configurar domínios de stub. No exemplo a seguir, atualize os domínios personalizados e endereços IP com os valores para o seu próprio ambiente. Crie um `corednsms.yaml` arquivo nomeado e cole a seguinte configuração de exemplo:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Como nos exemplos anteriores, crie o ConfigMap usando o comando [kubectl apply configmap][kubectl-apply] e especifique o nome do seu manifesto YAML. Em seguida, force o CoreDNS a recarregar o ConfigMap usando o [pod kubectl delete][kubectl delete] para o Agendador Kubernetes para recriá-los:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plugin hosts

Como todos os plugins incorporados são suportados, isso significa que o plugin CoreDNS [Hosts][coredns hosts] também está disponível para personalizar:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Habilite o registro para depuração de consulta de DNS 

Para habilitar o registro de consulta de DNS, aplique a seguinte configuração em seu ConfigMap personalizado de coredns:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou alguns exemplos de cenários para personalização do CoreDNS. Para obter informações sobre o projeto CoreDNS, consulte [a página do projeto upstream CoreDNS][coredns].

Para saber mais sobre os conceitos principais de rede, consulte [conceitos de rede para aplicações em AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
