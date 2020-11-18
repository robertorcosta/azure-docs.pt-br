---
title: Visão geral das marcas de serviço do Firewall do Azure
description: Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658640"
---
# <a name="azure-firewall-service-tags"></a>Marcas de serviço do Firewall do Azure

Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança. Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

As marcas de serviço de Firewall do Azure podem ser usadas no campo de destino de regras de rede. Você pode usá-los no lugar de endereços IP específicos.

## <a name="supported-service-tags"></a>Marcas de serviço com suporte

Consulte [grupos de segurança](../virtual-network/network-security-groups-overview.md#service-tags) para obter uma lista de marcas de serviço que estão disponíveis para uso em regras de rede do firewall do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as regras do Firewall do Azure, confira [Lógica de processamento de regra do Firewall do Azure](rule-processing.md).