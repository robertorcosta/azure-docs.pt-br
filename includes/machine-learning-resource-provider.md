---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75893968"
---
Ao criar um workspace do Azure Machine Learning ou um recurso usado pelo workspace, você pode receber um erro semelhante às seguintes mensagens:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A maioria dos provedores de recursos é automaticamente registrada, mas não todos. Se você receber essa mensagem, será necessário registrar o provedor mencionado.

Para obter informações sobre o registro de provedores de recursos, confira [Resolver erros de registro de provedor de recursos](../articles/azure-resource-manager/templates/error-register-resource-provider.md).