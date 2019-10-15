---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996841"
---
>[!NOTE]
> Pontos de extremidade para recursos criados após 1º de julho de 2019, use o formato de subdomínio personalizado mostrado abaixo. Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o Reconhecimento de Tinta Digital usando o [portal do Azure](../../cognitive-services-apis-create-account.md). 

* Você também pode obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias, gratuitamente. Depois de se inscrever, ela e um ponto de extremidade estarão disponíveis no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).

Depois de criar um recurso, obtenha o ponto de extremidade e a chave abrindo o recurso no [portal do Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e clicando em **Início rápido**.

Criar duas [variáveis de ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – O ponto de extremidade para seu recurso. Ele terá esta aparência: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` – A chave de assinatura para autenticar suas solicitações.   
