---
title: Erro InvalidNetworkSecurityGroupSecurityRules-Azure HDInsight
description: A criação do cluster falha com o ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289096"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Cenário: InvalidNetworkSecurityGroupSecurityRules-a criação de cluster falha no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe o código de erro `InvalidNetworkSecurityGroupSecurityRules` com uma descrição semelhante a "as regras de segurança no grupo de segurança de rede configuradas com a sub-rede não permitem a conectividade de entrada e/ou saída necessária".

## <a name="cause"></a>Causa

Provavelmente um problema com as regras de [grupo de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) de entrada configuradas para o cluster.

## <a name="resolution"></a>Resolução

Vá para a portal do Azure e identifique o NSG associado à sub-rede em que o cluster está sendo implantado. Na seção **regras de segurança de entrada** , verifique se as regras permitem o acesso de entrada à porta 443 para os endereços IP mencionados [aqui](../control-network-traffic.md).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]