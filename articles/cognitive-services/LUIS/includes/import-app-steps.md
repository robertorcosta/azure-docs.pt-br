---
title: Etapas de importação de aplicativo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422747"
---
1. Na [versão prévia do portal do LUIS](https://preview.luis.ai), na página **Meus Aplicativos**, selecione **+ Novo aplicativo para conversa** e, em seguida, **Importar como JSON**. Localize o arquivo JSON salvo na etapa anterior. Você não precisa alterar o nome do aplicativo. Selecione **Concluído**

1. Na seção **Gerenciar**, na guia **Versões**, escolha a versão `0.1` e, em seguida, **Clonar** para clonar a versão e dê a ela o nome de 10 caracteres `ml-entity`. Depois, escolha **Concluído** para concluir o processo de clonagem. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

    > [!TIP]
    > É uma prática recomendada clonar para uma nova versão antes de modificar o aplicativo. Quando concluir a alteração para uma versão, exporte a versão (como um arquivo .json ou .lu) e faça check-in do arquivo no controle do código-fonte.

1. Selecione **Compilar** e, em seguida, **Intenções** para ver as intenções, que são os principais blocos de construção de um aplicativo LUIS.

    ![Altere da página Versões para a página Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)