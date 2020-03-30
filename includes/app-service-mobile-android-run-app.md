---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240333"
---
1. Abra o projeto usando o **Android Studio**, usando **Importar projeto (Eclipse ADT, Gradle, etc.)**. Certifique-se de fazer essa seleção de importação para evitar erros de JDK.

2. Abra o `ToDoActivity.java` arquivo nesta pasta - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. O nome do app é `ZUMOAPPNAME`.

3. Acesse o [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na `Overview` lâmina, procure a URL que é o ponto final público para o seu aplicativo móvel. Exemplo - o nome do site do meu nome https://test123.azurewebsites.netde aplicativo "test123" será .

4. No `onCreate()` método, `ZUMOAPPURL` substitua o parâmetro por ponto final público acima.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    se torna
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Pressione o botão **Executar “aplicativo”** para compilar o projeto e iniciar o aplicativo no simulador de Android.

4. No aplicativo, digite texto significativo, como *Complete o tutorial* e clique no botão 'Adicionar'. Isso envia uma solicitação POST para o back-end do Azure implantado anteriormente. O back-end insere dados da solicitação na tabela SQL TodoItem e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista.
    ![Quickstart Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)