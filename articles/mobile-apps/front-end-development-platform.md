---
title: Escolha a plataforma de desenvolvimento front-end para criar aplicativos clientes com os serviços Visual Studio e Azure
description: Conheça os idiomas nativos e multiplataformas suportados para criar aplicativos clientes.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240855"
---
# <a name="choose-mobile-development-frameworks"></a>Escolha estruturas de desenvolvimento móvel
Os desenvolvedores podem usar tecnologias do lado do cliente para criar aplicativos móveis usando estruturas e padrões específicos para uma abordagem multiplataforma. Com base em seus fatores de decisão, os desenvolvedores podem construir:
- Aplicativos nativos de plataforma única usando linguagens como Objective C e Java
- Aplicativos multiplataforma usando Xamarin, .NET e C #
- Aplicações híbridas usando Cordova e suas variantes

## <a name="native-platforms"></a>Plataformas nativas
A criação de um aplicativo nativo requer linguagens de programação específicas da plataforma, SDKs, ambientes de desenvolvimento e outras ferramentas fornecidas por fornecedores de SO.

### <a name="ios"></a>iOS
Criado e desenvolvido pela Apple, o iOS é usado para criar aplicativos em dispositivos Apple, ou seja, o iPhone e o iPad.

- **Linguagens de programação**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Projetado pelo Google e pelo SO mais popular do mundo, o Android é usado para construir aplicativos que podem ser executados em uma variedade de smartphones e tablets.

- **Linguagem de programação**: Java, Kotlin 
- **IDE**: Ferramentas para desenvolvedores Android Studio e Android 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Linguagem de programação**: C #
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: Windows SDK

#### <a name="pros"></a>Vantagens
- Boa experiência do usuário
- Aplicativos responsivos com alto desempenho e capacidade de interface com bibliotecas nativas
- Aplicações altamente seguras

#### <a name="cons"></a>Desvantagens
- Aplicativo é executado em apenas uma plataforma
- Mais recursos do desenvolvedor intensivo e caro para construir um aplicativo
- Reutilização de código inferior

## <a name="cross-platforms-and-hybrid-applications"></a>Multiplataformas e aplicações híbridas
Aplicativos multiplataforma dão-lhe o poder de escrever aplicativos móveis nativos uma vez, compartilhar código e executá-los no iOS, Android e Windows.

### <a name="xamarin"></a>Xamarin
De propriedade da Microsoft, [o Xamarin](https://visualstudio.microsoft.com/xamarin/) é usado para construir aplicativos móveis robustos e multiplataforma sinuosos em C#. Xamarin tem uma biblioteca de classes e tempo de execução que funciona em muitas plataformas, como iOS, Android e Windows. Ele também compila aplicativos nativos (não interpretados) que oferecem alto desempenho. Xamarin combina todas as habilidades das plataformas nativas e adiciona uma série de recursos poderosos próprios.

- **Linguagem de programação**: C #
- **IDE**: Visual Studio no Windows ou Mac

### <a name="react-native"></a>React Native
Lançado pelo Facebook em 2015, [React Native](https://facebook.github.io/react-native/) é uma estrutura JavaScript [de código aberto](https://github.com/facebook/react-native) para escrever aplicativos móveis reais e nativos para iOS e Android. É baseado no React, a biblioteca JavaScript do Facebook para construir interfaces de usuário. Em vez de mirar o navegador, ele tem como alvo plataformas móveis. React Native usa componentes nativos em vez de componentes da Web como blocos de construção.
 
- **Linguagem de programação**: JavaScript
- **IDE**: Código visual do estúdio

### <a name="unity"></a>Unity
 Unity é um motor otimizado para criar jogos. Você pode usá-lo para criar aplicativos 2D ou 3D de alta qualidade com C# para plataformas como Windows, iOS, Android e Xbox.

### <a name="cordova"></a>Cordova
Cordova permite que você construa aplicativos híbridos usando ferramentas visuais studio para Apache Cordova ou Visual Studio Code com extensões para Cordova. Com a abordagem híbrida, você pode compartilhar componentes com sites e reutilizar aplicativos baseados em servidor web com abordagens de aplicativos web hospedados com base em Cordova.

#### <a name="pros"></a>Vantagens
- Maior usabilidade de código criando uma base de código para várias plataformas
- Atender a um público mais amplo em muitas plataformas
- Redução dramática no tempo de desenvolvimento
- Fácil de lançar e atualizar

#### <a name="cons"></a>Desvantagens
- Menor desempenho
- Falta de flexibilidade
- Cada plataforma tem um conjunto único de recursos e funcionalidades para tornar o aplicativo nativo mais criativo
- Aumento do tempo de design da UI
- Limitação da ferramenta
