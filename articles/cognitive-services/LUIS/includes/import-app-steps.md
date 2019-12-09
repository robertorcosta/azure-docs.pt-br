---
title: Etapas de importação de aplicativo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0253360c66ef6fd995f65e8a83ba5adcdf19543
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806399"
---
1. Na [versão prévia do portal do LUIS](https://preview.luis.ai), na página **Meus Aplicativos**, selecione **Importar** e, em seguida, **Importar como JSON**. Localize o arquivo JSON salvo na etapa anterior. Você não precisa alterar o nome do aplicativo. Selecione **Concluído**

1. Na seção **Gerenciar**, na guia **Versões**, selecione a versão e, em seguida, selecione **Clonar** para clonar a versão e dê a ela um nome com 10 caracteres. Depois, selecione **Concluído** para concluir o processo de clonagem. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

    > [!TIP]
    > É uma prática recomendada clonar uma versão para uma nova versão antes de modificar o aplicativo. Quando terminar com uma versão, exporte-a (como um arquivo .json ou .lu) e faça check-in do arquivo no controle do código-fonte.

1. Selecione **Compilar** e, em seguida, **Intenções** para ver as intenções, que são os principais blocos de construção de um aplicativo LUIS.

    ![Altere da página Versões para a página Intenções.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)