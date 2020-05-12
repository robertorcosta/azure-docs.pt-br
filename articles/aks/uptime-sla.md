---
title: Alta disponibilidade do AKS (serviço kubernetes do Azure) com SLA de tempo de atividade
description: Saiba mais sobre a oferta de SLA de tempo de atividade de alta disponibilidade opcional para o servidor de API do AKS (serviço de kubernetes do Azure).
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125716"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA de tempo de atividade do AKS (serviço kubernetes do Azure)

O SLA de tempo de atividade é um recurso opcional para habilitar o SLA mais alto com suporte financeiro para um cluster. O SLA de tempo de atividade garante 99,95% de disponibilidade do ponto de extremidade do servidor de API kubernetes para clusters que usam a [zona de disponibilidade][availability-zones] e 99,9% da disponibilidade para clusters que não usam zonas de disponibilidade. O AKS usa réplicas de nó mestre em domínios de atualização e de falha para garantir que os requisitos de SLA sejam atendidos.

Os clientes que precisam de SLA por motivos de conformidade ou estendendo SLA para seus clientes devem ativar esse recurso. Clientes com cargas de trabalho críticas que precisam de maior disponibilidade com uma opção de SLA beneficiam-se de habilitar esse recurso. Habilite o recurso com Zonas de Disponibilidade para obter maior disponibilidade do servidor de API do kubernetes.  

Os clientes podem criar clusters livres ilimitados com um objetivo de nível de serviço (SLO) de 99,5%.

> [!Important]
> Para clusters com bloqueio de saída, consulte [limitar o tráfego de saída](limit-egress-traffic.md) para abrir as portas apropriadas para o SLA de tempo de atividade.

## <a name="sla-terms-and-conditions"></a>Termos e condições do SLA

O SLA de tempo de atividade é um recurso pago e habilitado por cluster. O preço do SLA de tempo de atividade é determinado pelo número de clusters e não pelo tamanho dos clusters. Você pode exibir [detalhes de preços do SLA de tempo de atividade](https://azure.microsoft.com/pricing/details/kubernetes-service/) para obter mais informações.

## <a name="region-availability"></a>Disponibilidade da região

O SLA de tempo de atividade está disponível nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* Leste dos EUA
* Leste dos EUA 2
* Centro-Sul dos Estados Unidos
* Sudeste da Ásia
* Oeste dos EUA 2

## <a name="before-you-begin"></a>Antes de começar

* O CLI do Azure versão 2.7.0 ou posterior

## <a name="creating-a-cluster-with-uptime-sla"></a>Criando um cluster com SLA de tempo de atividade

Para criar um novo cluster com o SLA de tempo de atividade, use o CLI do Azure.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Use o comando [az aks create][az-aks-create] para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó. O Azure Monitor para contêineres também é habilitado usando o parâmetro *--enable-addons monitoring*.  Essa operação leva vários minutos para ser concluída.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster. O trecho JSON a seguir mostra a camada paga para a SKU, indicando que o cluster está habilitado com SLA de tempo de atividade.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>Limitações

* Atualmente, não é possível adicionar SLA de tempo de atividade a clusters existentes.
* Atualmente, não há nenhuma maneira de remover o SLA de tempo de atividade de um cluster AKS.  

## <a name="next-steps"></a>Próximas etapas

Use [zonas de disponibilidade][availability-zones] para aumentar a alta disponibilidade com suas cargas de trabalho de cluster do AKS.

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
