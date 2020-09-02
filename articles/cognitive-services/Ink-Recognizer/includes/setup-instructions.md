---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303972"
---
>[!NOTE]
> Pontos de extremidade para recursos criados após 1º de julho de 2019, use o formato de subdomínio personalizado mostrado abaixo. Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o Reconhecimento de Tinta Digital usando o [portal do Azure](../../cognitive-services-apis-create-account.md).

Depois de criar um recurso, obtenha o ponto de extremidade e a chave abrindo o recurso no [portal do Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e clicando em **Início rápido**.

Criar duas [variáveis de ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – A chave de assinatura para autenticar suas solicitações. 

* `INK_RECOGNITION_ENDPOINT` – O ponto de extremidade para seu recurso. Ele terá esta aparência: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
