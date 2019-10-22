---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266531"
---
O gateway de rede virtual usa uma sub-rede específica chamada de sub-rede de gateway. A sub-rede de gateway faz parte do intervalo de endereços IP da rede virtual que você especifica ao configurar sua rede virtual. Ele contém os endereços IP que os recursos e os serviços do gateway de rede virtual usam. 


Ao criar a sub-rede de gateway, você especifica o número de endereços IP que a sub-rede contém. O número de endereços IP necessários depende da configuração do gateway de VPN que você deseja criar. Algumas configurações exigem mais endereços IP do que outras. Recomendamos que você crie uma sub-rede de gateway que use um/27 ou/28.


Se você vir um erro que especifica que o espaço de endereço se sobrepõe a uma sub-rede ou que a sub-rede não está contida no espaço de endereço para sua rede virtual, verifique o intervalo de endereços da VNet. Talvez você não tenha endereços IP suficientes disponíveis no intervalo de endereços que você criou para sua rede virtual. Por exemplo, se a sua sub-rede padrão abranger todo o intervalo de endereços, não haverá nenhum endereço IP restante para criar sub-redes adicionais. Você pode ajustar suas sub-redes no espaço de endereço existente para liberar endereços IP ou especificar um intervalo de endereços adicional e criar a sub-rede do gateway.