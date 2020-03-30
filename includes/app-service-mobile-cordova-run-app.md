---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240251"
---
1. Navegue até o arquivo de solução no projeto cliente (.sln) e abra-o usando o Visual Studio.

2. No Visual Studio, escolha a plataforma de solução (Android, iOS ou Windows) no menu suspenso ao lado da seta de início. Selecione um emulador ou dispositivo de implantação específico clicando na lista suspensa na seta verde. Você pode usar a plataforma Android padrão e o emulador Ripple. Tutoriais mais avançados (por exemplo, notificações por push) exigirão que você selecione um emulador ou dispositivo com suporte.

3. Abra o `ToDoActivity.java` arquivo nesta pasta - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. O nome do app é `ZUMOAPPNAME`.

4. Acesse o [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na `Overview` lâmina, procure a URL que é o ponto final público para o seu aplicativo móvel. Exemplo - o nome do site do meu nome https://test123.azurewebsites.netde aplicativo "test123" será .

5. Vá para `index.js` o arquivo em ZUMOAPPNAME/www/js/index.js e no `onDeviceReady()` método, substitua `ZUMOAPPURL` o parâmetro por ponto final público acima.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    se torna
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Pressione F5 ou clique na seta verde para compilar e executar o aplicativo Cordova. Se for mostrada uma caixa de diálogo de segurança no emulador que solicita acesso à rede, aceite-a.

7. Depois que o aplicativo for iniciado no dispositivo ou emulador, digite texto significativo em **Enter novo texto,** como *Complete o tutorial* e clique no botão **Adicionar.**

Os back-end insere dados da solicitação na tabela TodoItem no Banco de Dados SQL e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista.

Você pode repetir as etapas 3 a 5 para outras plataformas.