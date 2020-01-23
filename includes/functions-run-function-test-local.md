---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 343321b43d219401f0b494ab3f6a54074fbc36f3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279364"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O comando a seguir inicia o aplicativo de funções. O aplicativo é executado usando o mesmo Azure Functions runtime no Azure. O comando start varia, dependendo da linguagem do projeto.

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

Quando o host do Functions é iniciado, ele escreve algo parecido com a seguinte saída, que foi truncada para facilitar a leitura:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copie a URL da função `HttpTrigger` da saída do runtime de função e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. O exemplo a seguir mostra a resposta no navegador à solicitação GET retornada pela função local:

![Testar localmente no navegador](./media/functions-run-function-test-local/functions-test-local-browser.png)

Agora que você executou a função localmente, poderá criar o aplicativo de funções e outros recursos necessários no Azure.
