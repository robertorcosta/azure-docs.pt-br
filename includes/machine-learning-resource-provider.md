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
Ao criar um espaço de trabalho Azure Machine Learning ou um recurso usado pelo espaço de trabalho, você pode receber um erro semelhante às seguintes mensagens:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A maioria dos provedores de recursos são automaticamente registrados, mas não todos. Se você receber essa mensagem, precisará registrar o provedor mencionado.

Para obter informações sobre como registrar provedores de recursos, consulte [resolver erros de registro do provedor de recursos](../articles/azure-resource-manager/templates/error-register-resource-provider.md).