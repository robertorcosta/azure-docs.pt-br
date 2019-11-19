---
title: Lógica de processamento de regra do Firewall do Azure
description: O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166783"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regra do Firewall do Azure
O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.


## <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicativos 
As regras de rede são aplicadas primeiro e, seguida, são aplicadas as regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada nas regras de rede, as regras de aplicativo não serão processadas.  Se não houver qualquer correspondência da regra de rede e se o protocolo do pacote for HTTP/HTTPS, o pacote será avaliado pelas regras de aplicativo. Se ainda assim nenhuma correspondência for encontrada, o pacote será avaliado em relação à coleção de regras de infraestrutura. Se ainda não houver nenhuma correspondência, o pacote será negado por padrão.

## <a name="nat-rules"></a>Regras de NAT
A conectividade de entrada pode ser habilitada configurando a DNAT (conversão de endereços de rede de destino) conforme descrito em [Tutorial: Filtrar o tráfego de entrada com DNAT de Firewall do Azure usando o portal do Azure](tutorial-firewall-dnat.md). As regras de DNAT são aplicadas primeiro. Se uma correspondência for encontrada, será adicionada uma regra de rede correspondente implícita para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Não há regras de aplicativo aplicadas para essas conexões.


## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).