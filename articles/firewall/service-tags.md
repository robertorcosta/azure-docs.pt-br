---
title: Visão geral das marcas de serviço do Firewall do Azure
description: Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74168680"
---
# <a name="azure-firewall-service-tags"></a>Marcas de serviço do Firewall do Azure

Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança. Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

As marcas de serviço de Firewall do Azure podem ser usadas no campo de destino de regras de rede. Você pode usá-los no lugar de endereços IP específicos.

## <a name="supported-service-tags"></a>Marcas de serviço com suporte

Consulte [grupos de segurança](../virtual-network/security-overview.md#service-tags) para obter uma lista de marcas de serviço que estão disponíveis para uso em regras de rede do firewall do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as regras do Firewall do Azure, confira [Lógica de processamento de regra do Firewall do Azure](rule-processing.md).