---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369063"
---
>[!NOTE]
> Pontos de extremidade para recursos criados após 1º de julho de 2019, use o formato de subdomínio personalizado mostrado abaixo. Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](../../cognitive-services-custom-subdomains.md). 

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o Reconhecimento de Tinta Digital usando o [portal do Azure](../../cognitive-services-apis-create-account.md).

Depois de criar um recurso, obtenha o ponto de extremidade e a chave abrindo o recurso no [portal do Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e clicando em **Início rápido**.

Criar duas [variáveis de ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – A chave de assinatura para autenticar suas solicitações. 

* `INK_RECOGNITION_ENDPOINT` – O ponto de extremidade para seu recurso. Ele terá esta aparência: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`