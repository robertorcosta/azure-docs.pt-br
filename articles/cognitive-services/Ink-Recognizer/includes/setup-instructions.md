---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c202ba1d7363af9791daa801f0c447c49a80859b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378563"
---
>[!NOTE]
> Pontos de extremidade para recursos criados após 1º de julho de 2019, use o formato de subdomínio personalizado mostrado abaixo. Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o Reconhecimento de Tinta Digital usando o [portal do Azure](../../cognitive-services-apis-create-account.md).

Depois de criar um recurso, obtenha o ponto de extremidade e a chave abrindo o recurso no [portal do Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e clicando em **Início rápido**.

Criar duas [variáveis de ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – O ponto de extremidade para seu recurso. Ele terá esta aparência: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` – A chave de assinatura para autenticar suas solicitações.   
