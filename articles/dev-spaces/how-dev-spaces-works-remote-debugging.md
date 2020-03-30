---
title: Como funciona a depuração remota do seu código com o Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos de depuração remota no Azure Kubernetes Service com o Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241394"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Como funciona a depuração remota do seu código com o Azure Dev Spaces

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente os aplicativos kubernetes e colaborar com sua equipe em um cluster Azure Kubernetes Service (AKS). Uma vez que seu projeto esteja sendo executado em um espaço de desenvolvimento, o Azure Dev Spaces fornece uma maneira de anexar e depurar um aplicativo em execução no AKS.

Este artigo descreve como funciona a depuração remota com o Dev Spaces.

## <a name="debug-your-code"></a>Depurar seu código

Para aplicações Java, .NET Core e Node.js, você pode depurar seu aplicativo em execução diretamente em seu espaço de desenvolvimento usando Visual Studio Code ou Visual Studio. Visual Studio Code e Visual Studio fornecem ferramentas para se conectar ao seu espaço de desenvolvimento, iniciar seu aplicativo e anexar um depurador. Depois `azds prep`de executado, você pode abrir seu projeto no Visual Studio Code ou Visual Studio. Visual Studio Code ou Visual Studio gerarão seus próprios `azds prep`arquivos de configuração para conexão que é separado da execução . A partir de Visual Studio Code ou Visual Studio, você pode definir pontos de interrupção e iniciar seu aplicativo para o seu espaço de desenvolvimento.

![Depurando seu código](media/get-started-node/debug-configuration-nodejs2.png)

Quando você inicia seu aplicativo usando visual studio code ou visual studio para depuração, eles lidam `azds up`com o lançamento e conexão ao seu espaço de desenvolvimento da mesma forma que a execução . Além disso, as ferramentas do lado do cliente no Visual Studio Code e visual Studio fornecem um parâmetro adicional com informações específicas para depuração. O parâmetro contém o nome da imagem de depurador, a localização do depurador dentro da imagem do depurador e o local de destino dentro do contêiner do aplicativo para montar a pasta de depurador.

A imagem do depurador é automaticamente determinada pela ferramenta do lado do cliente. Ele usa um método semelhante ao usado durante o `azds prep`gráfico Dockerfile e Helm gerar ao executar . Depois que o depurador é montado na imagem do `azds exec`aplicativo, ele é executado usando .

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure Dev Spaces para depurar remotamente seu projeto, consulte as seguintes partidas rápidas:

* [Rapidamente iterado e depurado com Visual Studio Code e Java][quickstart-java]
* [Iterar rapidamente e depurar com Visual Studio Code e .NET][quickstart-netcore]
* [Rapidamente iterado e depurado com Visual Studio Code e Node.js][quickstart-node]
* [Iterar rapidamente e depurar com Visual Studio e .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
