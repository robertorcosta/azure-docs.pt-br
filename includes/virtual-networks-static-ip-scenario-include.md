---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060439"
---
## <a name="scenario"></a>Cenário

Para ilustrar melhor como configurar um endereço IP estático para uma VM, este documento usa este cenário:

![Cenário de rede virtual: sub-redes front-end e back-end, com um endereço IP estático para a sub-rede front-end](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

Neste cenário, você cria uma VM chamada *DNS01* na sub-rede *FrontEnd* e, em seguida, define-a para usar um endereço IP estático de *192.168.1.101*.
