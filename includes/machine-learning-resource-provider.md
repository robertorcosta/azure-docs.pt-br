---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893968"
---
Ao criar um espaço de trabalho de aprendizado de máquina do Azure ou um recurso usado pelo espaço de trabalho, você pode receber um erro semelhante às seguintes mensagens:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A maioria dos provedores de recursos são automaticamente registrados, mas não todos. Se você receber esta mensagem, você precisa registrar o provedor mencionado.

Para obter informações sobre o registro de provedores de recursos, consulte [Resolver erros para registro de provedor esporas](../articles/azure-resource-manager/templates/error-register-resource-provider.md).