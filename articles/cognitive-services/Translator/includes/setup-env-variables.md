---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144228"
---
## <a name="set-up"></a>Configuração

### <a name="create-a-translator-resource"></a>Criar um recurso Tradutor

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso Tradutor usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) em seu computador local. Também é possível:

* Exiba um recurso existente no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave do recurso ou da assinatura de avaliação, crie duas [variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - Chave de assinatura do seu recurso Tradutor.
* `TRANSLATOR_TEXT_ENDPOINT` - Ponto de extremidade global para o Tradutor. Use `https://api.cognitive.microsofttranslator.com/`.
