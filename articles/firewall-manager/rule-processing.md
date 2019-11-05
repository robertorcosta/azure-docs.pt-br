---
title: Lógica de processamento de regra do Firewall do Azure
description: Saiba mais sobre a lógica de processamento de regra do Firewall do Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518197"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regra do Firewall do Azure

O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.

## <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicativos

As regras de rede são aplicadas primeiro e, seguida, são aplicadas as regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada nas regras de rede, as regras de aplicativo não serão processadas.  Se não houver qualquer correspondência da regra de rede e se o protocolo do pacote for HTTP/HTTPS, o pacote será avaliado pelas regras de aplicativo. Se ainda assim nenhuma correspondência for encontrada, o pacote será avaliado em relação à coleção de regras de infraestrutura. Se ainda não houver correspondências, o pacote será negado por padrão.

## <a name="nat-rules"></a>Regras de NAT

A conectividade de entrada pode ser habilitada configurando a DNAT (conversão de endereços de rede de destino) conforme descrito em [Tutorial: Filtrar o tráfego de entrada com DNAT de Firewall do Azure usando o portal do Azure](../firewall/tutorial-firewall-dnat.md). As regras de DNAT são aplicadas primeiro. Se uma correspondência for encontrada, será adicionada uma regra de rede correspondente implícita para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Não há regras de aplicativo aplicadas para essas conexões.

## <a name="inherited-rules"></a>Regras herdadas

As coleções de regras de rede herdadas de uma política pai sempre são priorizadas acima das coleções de regras de rede que são definidas como parte de sua nova política. A mesma lógica também se aplica às coleções de regras de aplicativo. No entanto, as coleções de regras de rede são sempre processadas antes das coleções de regras de aplicativo, independentemente da herança.

Por padrão, sua política herda o modo de inteligência contra ameaças da política pai. Você pode substituir isso definindo seu modo de inteligência contra ameaças para um valor diferente na página de configurações de política. Só é possível substituir por um valor mais estrito. Por exemplo, se a política pai estiver definida como *alerta somente*, você poderá configurar essa política local para *alertar e negar*, mas não poderá desativá-la.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre a versão prévia do Gerenciador de firewall do Azure](overview.md)