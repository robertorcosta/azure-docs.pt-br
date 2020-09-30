---
title: Etapas de importação de aplicativo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/22/2020
ms.openlocfilehash: aa0ba1de63cbcf1d9485c71b6bec7a29afb5140f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544907"
---
1. No [portal do LUIS](https://www.luis.ai), na página **Meus aplicativos**, selecione **+ Novo aplicativo para conversa**, em seguida, **Importar como JSON**. Localize o arquivo JSON salvo na etapa anterior. Você não precisa alterar o nome do aplicativo. Selecione **Concluído**

1. Na seção **Gerenciar**, na guia **Versões**, escolha a versão `0.1`, em seguida, selecione **Clonar** para clonar a versão e dê um nome de `ml-entity`. Depois, escolha **Concluído** para terminar o processo de clonagem. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

    > [!TIP]
    > É uma prática recomendada clonar para uma nova versão antes de modificar o aplicativo. Quando concluir a alteração para uma versão, exporte a versão (como um arquivo .json ou .lu) e faça check-in do arquivo no controle do código-fonte.

1. Selecione **Compilar** e, em seguida, **Intenções** para ver as intenções, que são os principais blocos de construção de um aplicativo LUIS.

    ![Altere da página Versões para a página Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
