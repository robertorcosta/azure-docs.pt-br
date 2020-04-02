---
title: Rotativas de certificados no Azure Kubernetes Service (AKS)
description: Saiba como rodar seus certificados em um cluster Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549064"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Rotativas de certificados no Azure Kubernetes Service (AKS)

O Azure Kubernetes Service (AKS) usa certificados para autenticação com muitos de seus componentes. Periodicamente, você pode precisar girar esses certificados por razões de segurança ou política. Por exemplo, você pode ter uma política para rodar todos os seus certificados a cada 90 dias.

Este artigo mostra como girar os certificados em seu cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo exige que você esteja executando a versão 2.0.77 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificados AKS, Autoridades de Certificados e Contas de Serviço

O AKS gera e usa os seguintes certificados, autoridades de certificados e contas de serviço:

* O servidor AKS API cria uma Autoridade de Certificado (CA) chamada CA de cluster.
* O servidor de API possui um Cluster CA, que assina certificados para comunicação unidirecional do servidor de API para kubelets.
* Cada kubelet também cria uma Solicitação de Assinatura de Certificado (CSR), que é assinada pelo Cluster CA, para comunicação do kubelet ao servidor API.
* O armazenamento de valor de chave etcd tem um certificado assinado pelo Cluster CA para comunicação de etcd para o servidor de API.
* O armazenamento de valor de chave etcd cria um CA que assina certificados para autenticar e autorizar a replicação de dados entre réplicas etcd no cluster AKS.
* O agregador de API usa o Cluster CA para emitir certificados para comunicação com outras APIs. O agregador de API também pode ter sua própria CA para emissão desses certificados, mas atualmente usa o Cluster CA.
* Cada nó usa um token de Conta de Serviço (SA), que é assinado pelo Cluster CA.
* O `kubectl` cliente tem um certificado para se comunicar com o cluster AKS.

> [!NOTE]
> Os clusters AKS criados antes de março de 2019 têm certificados que expiram após dois anos. Qualquer cluster criado após março de 2019 ou qualquer cluster que tenha seus certificados rotacionados tem certificados de Ca cluster que expiram após 30 anos. Todos os outros certificados expiram após dois anos. Para verificar quando o cluster `kubectl get nodes` foi criado, use para ver a *Era* de suas piscinas de nó.
> 
> Além disso, você pode verificar a data de validade do certificado do seu cluster. Por exemplo, o comando a seguir exibe os detalhes do certificado para o cluster *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Gire seus certificados de cluster

> [!WARNING]
> Rodar seus certificados `az aks rotate-certs` usando pode causar até 30 minutos de inatividade para o seu cluster AKS.

Use [az aks get-credentials][az-aks-get-credentials] para entrar no seu cluster AKS. Este comando também baixa e `kubectl` configura o certificado de cliente em sua máquina local.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Use `az aks rotate-certs` para girar todos os certificados, CAs e SAs em seu cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Pode levar até 30 `az aks rotate-certs` minutos para ser concluída. Se o comando falhar antes `az aks show` de completar, use para verificar se o status do cluster é *Certificate Rotating*. Se o cluster estiver em um `az aks rotate-certs` estado de falha, execute novamente para girar seus certificados.

Verifique se os certificados antigos não `kubectl` são mais válidos executando um comando. Uma vez que você não `kubectl`atualizou os certificados usados por , você verá um erro.  Por exemplo: 

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Atualize o `kubectl` certificado `az aks get-credentials`usado pela execução .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Verifique se os certificados foram `kubectl` atualizados executando um comando, que agora terá sucesso. Por exemplo: 

```console
kubectl get no
```

> [!NOTE]
> Se você tiver algum serviço que seja executado em cima de AKS, como [o Azure Dev Spaces,][dev-spaces]você também precisará [atualizar certificados relacionados a esses serviços.][dev-spaces-rotate]

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como girar automaticamente os certificados, CAs e SAs do seu cluster. Você pode ver [as práticas recomendadas de segurança de cluster e upgrades no Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] para obter mais informações sobre as práticas recomendadas de segurança da AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
