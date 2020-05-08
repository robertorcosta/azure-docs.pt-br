---
title: Solucionar Problemas de erros comuns de implantação
titleSuffix: Azure Load Balancer
description: Descreve como resolver erros comuns ao implantar balanceadores de carga do Azure
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 26b4232832589827a09774eb0106c25e5c446eb1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791078"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Solucionar erros comuns de implantação do Azure com o Azure Load Balancer

Este artigo descreve alguns erros comuns de implantação de Azure Load Balancer e fornece informações para resolver os erros. Se você estiver procurando informações sobre um código de erro e se essas informações não forem fornecidas neste artigo, informe-nos. Na parte inferior desta página, você pode deixar comentários. Os comentários são acompanhados com problemas do GitHub.

## <a name="error-codes"></a>Códigos do Erro

| Código do erro | Detalhes e mitigação |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| O SKU de IP público e Load Balancer SKU devem corresponder. Verifique se os SKUs de IP público e Azure Load Balancer correspondem. O SKU Standard é recomendado para cargas de trabalho de produção. Saiba mais sobre as [diferenças em SKUs](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Os conjuntos de dimensionamento de máquinas virtuais são padrão para balanceadores de carga básicos quando o SKU não é especificado ou implantado sem IPs públicos padrão. Reimplante o conjunto de escala de máquina virtual com IPs públicos padrão nas instâncias individuais para garantir que Standard Load Balancer esteja selecionado ou simplesmente selecione um LB Standard ao implantar o conjunto de dimensionamento de máquinas virtuais da portal do Azure. |
|MaxAvailabilitySetsInLoadBalancerReached | O pool de back-end de um Load Balancer pode conter um máximo de 150 conjuntos de disponibilidade. Se você não tiver conjuntos de disponibilidade explicitamente definidos para suas VMs no pool de back-end, cada VM entrará em seu próprio conjunto de disponibilidade. Assim, a implantação de 150 VMs autônomas significa que ela teria 150 conjuntos de disponibilidade, atingindo assim o limite. Você pode implantar um conjunto de disponibilidade e adicionar outras VMs a ele como uma solução alternativa. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Você não pode ter mais de uma regra em um determinado tipo de balanceador de carga (interno, público) com a mesma porta de back-end e protocolo referenciados pelo mesmo conjunto de dimensionamento de máquinas virtuais. Atualize sua regra para alterar a criação de regra duplicada. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Você não pode ter mais de uma regra em um determinado tipo de balanceador de carga (interno, público) com a mesma porta de back-end e protocolo referenciados pelo mesmo conjunto de dimensionamento de máquinas virtuais. Atualize os parâmetros de regra para alterar essa criação de regra duplicada. |
|AnotherInternalLoadBalancerExists| Você pode ter apenas um Load Balancer do tipo referência interna do mesmo conjunto de VMs/interfaces de rede no back-end do Load Balancer. Atualize sua implantação para garantir que você está criando apenas um Load Balancer do mesmo tipo. |
|CannotUseInactiveHealthProbe| Você não pode ter uma investigação que não seja usada por nenhuma regra configurada para a integridade do conjunto de dimensionamento de máquinas virtuais. Verifique se a investigação configurada está sendo usada ativamente. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Você não pode ter vários balanceadores de carga do mesmo tipo (interno, público). Você pode ter um máximo de um Load Balancer interno e um público. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Não há suporte para Load Balancer básica para conjuntos de dimensionamento de máquinas virtuais de grupos de vários posicionamento ou conjunto de dimensionamento de máquinas virtuais de zona de disponibilidade cruzada. Em vez disso, use Standard Load Balancer. |
|ResourceDeploymentFailure| Se o balanceador de carga estiver em um estado de falha, siga estas etapas para voltar do estado de falha:<ol><li>Vá para https://resources.azure.come entre com suas credenciais de portal do Azure.</li><li>Selecione **leitura/gravação**.</li><li>À esquerda, expanda **assinaturas**e expanda a assinatura com o Load Balancer para atualizar.</li><li>Expanda **ResourceGroups**e, em seguida, expanda o grupo de recursos com o Load Balancer para atualizar.</li><li>Selecione **Microsoft. Network** > **balanceadores**de rede e, em seguida, selecione o Load Balancer a ser atualizado **LoadBalancer_1**.</li><li>Na página Exibir para **LoadBalancer_1**, selecione **obter** > **Editar**.</li><li>A atualização do valor de **ProvisioningState** de **falhou** para **bem-sucedida**.</li><li>Selecione **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Próximas etapas

* Examinar a tabela de [comparação](./skus.md) de Azure Load Balancer SKU
* Saiba mais sobre [os limites de Azure Load Balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
