---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240260"
---
1. Abra o projeto do cliente baixado usando o Xcode.

2. Acesse o [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na `Overview` lâmina, procure a URL que é o ponto final público para o seu aplicativo móvel. Exemplo - o nome do site do meu nome https://test123.azurewebsites.netde aplicativo "test123" será .

3. Para o projeto Swift, abra o arquivo `ToDoTableViewController.swift` nesta pasta - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. O nome do app é `ZUMOAPPNAME`.

4. No `viewDidLoad()` método, `ZUMOAPPURL` substitua o parâmetro por ponto final público acima.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    se torna
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Para o projeto Objective-C, abra o arquivo `QSTodoService.m` nesta pasta - ZUMOAPPNAME/ZUMOAPPNAME. O nome do app é `ZUMOAPPNAME`.

6. No `init` método, `ZUMOAPPURL` substitua o parâmetro por ponto final público acima.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    se torna
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no simulador de iOS.

8. No aplicativo, clique no**+** ícone de mais ( ) digite texto significativo, como *Complete o tutorial*, e clique no botão salvar. Isso envia uma solicitação POST para o back-end do Azure implantado anteriormente. O back-end insere dados da solicitação na tabela SQL TodoItem e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista.

   ![Aplicativo de início rápido em execução no iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
