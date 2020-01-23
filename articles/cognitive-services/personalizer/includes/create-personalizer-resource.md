---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122749"
---
## <a name="create-a-personalizer-azure-resource"></a>Criar um recurso do Azure do Personalizador

Crie um recurso para o Personalizador usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) em seu computador local. Também é possível:

* Obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias gratuitamente. Depois de se inscrever, ela estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Exibir esse recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave do recurso ou da assinatura de avaliação, crie duas [variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY` para a chave do recurso.
* `PERSONALIZER_RESOURCE_ENDPOINT` para o ponto de extremidade do recurso.

No portal do Azure, os valores da chave e do ponto de extremidade estão disponíveis na página **início rápido**.
