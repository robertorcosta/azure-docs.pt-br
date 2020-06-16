---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/03/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 8e67a6d0c98a3839922a79e9b452465087da1b69
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417983"
---
1. Selecione [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) para abrir a página do GitHub para o arquivo `pizza-app-for-luis.json`.
1. Clique com o botão direito do mouse (ou toque e segure por alguns segundos) o botão **Bruto** e selecione **Salvar link como** para salvar o `pizza-app-for-luis.json` em seu computador.
1. Entre no [portal do LUIS](https://www.luis.ai).
1. Selecione [Meus Aplicativos](https://www.luis.ai/applications).
1. Na página **Meus aplicativos**, selecione **+ Novo aplicativo de conversa**.
1. Selecione **Importar como JSON**.
1. Na caixa de diálogo **Importar novo aplicativo**, selecione o botão **Escolher arquivo**.
1. Selecione o arquivo `pizza-app-for-luis.json` que você baixou e, em seguida, selecione **Abrir**.
1. No campo **Nome** da caixa de diálogo **Importar novo aplicativo**, insira um nome para o aplicativo Pizza e, em seguida, selecione o botão **Concluído**.

O aplicativo será importado.

Se você vir a caixa de diálogo que diz **Como criar um aplicativo LUIS eficaz**, feche-a.

## <a name="train-and-publish-the-pizza-app"></a>Treinar e publicar o aplicativo Pizza

Você deve ver a página **Intenções** com uma lista de intenções no aplicativo Pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

Agora, o aplicativo Pizza está pronto para o uso.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Registrar a ID do aplicativo, a chave de previsão e o ponto de extremidade de previsão do aplicativo Pizza

Para usar o novo aplicativo Pizza, você precisará da ID do aplicativo, da chave de previsão e do ponto de extremidade de previsão do aplicativo Pizza.

Para encontrar esses valores:

1. Na página **Intenções**, selecione **GERENCIAR**.
1. Na página **Configurações de aplicativo**, registre a **ID do aplicativo**.
1. Selecione **Recursos do Azure**.
1. Na página **Recursos do Azure**, registre a **Chave Primária**. Esse valor é a chave de previsão.
1. Registre a **URL do ponto de extremidade**. Esse valor é o ponto de extremidade de previsão.
