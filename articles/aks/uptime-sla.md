---
title: Serviço de Kubernetes do Azure (AKS) com SLA de tempo de atividade
description: Saiba mais sobre a oferta opcional de SLA de tempo de atividade para o servidor de API do Serviço de Kubernetes do Azure (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e0e1399f69640dddfd618ac99637023390f28a92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683212"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA de tempo de atividade do Serviço de Kubernetes do Azure (AKS)

O SLA de tempo de atividade é um recurso opcional para habilitar um SLA com suporte financeiro e superior para um cluster. O SLA de tempo de atividade garante 99,95% de disponibilidade do ponto de extremidade do servidor de API do Kubernetes para clusters que usam [Zona de Disponibilidade][availability-zones] e 99,9% da disponibilidade para clusters que não usam Zonas de Disponibilidade. O AKS usa réplicas de nó mestre em domínios de atualização e de falha para garantir que os requisitos de SLA sejam atendidos.

Os clientes que precisam de um SLA para atender aos requisitos de conformidade ou solicitam a extensão de um SLA para seus usuários finais devem ativar esse recurso. Os clientes com cargas de trabalho críticas que aproveitarão bem um SLA de tempo de atividade mais alto também podem se beneficiar. Usar o recurso de SLA de tempo de atividade com Zonas de Disponibilidade permite uma disponibilidade maior para o tempo de atividade do servidor de API do Kubernetes.  

Os clientes ainda podem criar clusters grátis ilimitados com um objetivo de nível de serviço (SLO) de 99,5% e optar pelo tempo de atividade do SLO ou do SLA que preferirem, conforme necessário.

> [!Important]
> Para clusters com bloqueio de tráfego de saída, consulte [limitar tráfego de saída](limit-egress-traffic.md) para abrir as portas apropriadas.

## <a name="sla-terms-and-conditions"></a>Termos e condições do SLA

O SLA de tempo de atividade é um recurso pago e habilitado por cluster. O preço do SLA de tempo de atividade é determinado pelo número de clusters discretos e não pelo tamanho de clusters individuais. Você pode ver [Detalhes de preços do SLA de tempo de atividade](https://azure.microsoft.com/pricing/details/kubernetes-service/) para mais informações.

## <a name="region-availability"></a>Disponibilidade de região

O SLA de tempo de atividade está disponível nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* Leste dos EUA
* Leste dos EUA 2
* Centro-Sul dos Estados Unidos
* Sudeste da Ásia
* Oeste dos EUA 2

## <a name="before-you-begin"></a>Antes de começar

* A CLI do Azure versão 2.7.0 ou posterior

## <a name="creating-a-cluster-with-uptime-sla"></a>Criar um cluster com SLA de tempo de atividade

Para criar um novo cluster com o SLA de tempo de atividade, use a CLI do Azure.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Use o comando [az aks create][az-aks-create] para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó. O Azure Monitor para contêineres também é habilitado usando o parâmetro *--enable-addons monitoring*.  Essa operação leva vários minutos para ser concluída.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster. O snippet JSON a seguir mostra a camada paga do SKU, indicando que seu cluster está habilitado com SLA de tempo de atividade.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Limitações

* Atualmente, não é possível converter como cluster existente para habilitar o SLA de tempo de atividade.
* Atualmente, não é possível remover o SLA de tempo de atividade de um cluster AKS depois que ele foi criado com esse SLA ativado.  
* Atualmente, não há suporte para clusters particulares.

## <a name="next-steps"></a>Próximas etapas

Use as [Zonas de Disponibilidade][availability-zones] para aumentar a HA com suas cargas de trabalho de cluster do AKS.
Configure o cluster para [limitar o tráfego de saída](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
