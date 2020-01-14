---
title: 'Início Rápido: Reconhecer uma fala, intenções e entidades, Python – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660443"
---
Para concluir o início rápido do reconhecimento de intenção, você precisará criar uma conta do LUIS e um projeto usando a versão prévia do portal do LUIS. Este início rápido requer apenas uma assinatura do LUIS. Não é necessário ter uma assinatura do serviço de Fala.

A primeira coisa que você precisará fazer é criar uma conta e um aplicativo do LUIS usando a versão prévia do portal do LUIS. O aplicativo LUIS que você cria usará um domínio predefinido para a automação doméstica, que fornece intenções, entidades e exemplos de declarações. Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem que você poderá chamar usando o SDK de Fala. 

Siga estas instruções para criar seu aplicativo LUIS: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Início Rápido: Criar um aplicativo de domínio predefinido</a>

Quando terminar, você precisará de três itens: 

* Sua chave do LUIS
* Sua região do LUIS
* Sua ID do aplicativo LUIS

Veja em que parte da [versão prévia do portal do LUIS](https://preview.luis.ai/) é possível encontrar essas informações:

1. Na versão prévia do portal do LUIS, selecione **Gerenciar** e, em seguida, selecione **Recursos do Azure**. Nesta página, você encontrará sua chave e local (às vezes chamado de _região_) do LUIS.  

   > [!div class="mx-imgBorder"]
   > ![Chave e local do LUIS](../../../media/luis/luis-key-region.png)

2. Depois de obter a chave e o local, você precisará da ID do aplicativo. Selecione **Configurações do Aplicativo** – a ID do aplicativo está disponível nesta página.

   > [!div class="mx-imgBorder"]
   > ![ID do aplicativo LUIS](../../../media/luis/luis-app-id.png)