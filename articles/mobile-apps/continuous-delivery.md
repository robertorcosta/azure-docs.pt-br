---
title: Automatize a implantação e o lançamento de seus aplicativos móveis com os serviços Visual Studio App Center e Azure
description: Conheça os serviços como o App Center que ajudam a configurar o pipeline de entrega contínua para seus aplicativos móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235347"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatize a implantação e o lançamento de seus aplicativos móveis com serviços de entrega contínua

Como desenvolvedores, você escreve código e verifica-o no repositório de código, mas os compromissos verificados no repo podem nem sempre ser consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, os problemas podem chegar à integração. As equipes podem se deparar com situações em que as coisas não funcionam, os bugs se acumulam e o desenvolvimento do projeto é adiado. Os desenvolvedores têm que esperar até que todo o código de software seja construído e testado para verificar se há erros, o que torna o processo lento e menos iterativo.

Com a entrega contínua, você automatiza a implantação e liberação de seus aplicativos móveis. Não importa se você está distribuindo o aplicativo para um grupo de testadores ou funcionários da empresa (para testes beta) ou para uma loja de aplicativos (para produção). A entrega contínua torna as implantações menos arriscadas e incentiva iterações rápidas. Você também pode liberar novas alterações para seus clientes de forma contínua.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribua binários de aplicativos para testadores beta
Testar beta seu aplicativo móvel é uma das etapas críticas durante o processo de desenvolvimento do aplicativo. Ele ajuda a encontrar bugs e problemas em sua aplicação no início. O feedback melhora a qualidade do seu aplicativo quando você está preparando-o para o uso da produção.

Use os seguintes serviços para habilitar um pipeline de entrega contínua em seus aplicativos móveis.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Distribute](/appcenter/distribution/) é uma ferramenta para desenvolvedores liberarem rapidamente builds para dispositivos. Com uma experiência completa de portal de instalação, o App Center Distribute é uma solução poderosa para distribuição beta de testador de aplicativos. Também é uma alternativa conveniente para a distribuição através de lojas de aplicativos públicas. Os desenvolvedores podem automatizar ainda mais seu fluxo de trabalho de distribuição com o App Center Build e integrações de lojas de aplicativos públicos.

**Principais características**
- Distribua seu aplicativo para testadores e usuários beta e certifique-se de que todos os seus testadores estejam na versão mais recente do seu aplicativo.
- Notifique os testadores de novas versões sem que os testadores passem pelo fluxo de download novamente.
- Gerenciar grupos de distribuição para diferentes versões do seu aplicativo.
- Distribua para as lojas: 
    - [Maçã](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Ganhe suporte à plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.
- Registre automaticamente os dispositivos iOS no seu perfil de provisionamento.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Comece com o App Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[O Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um serviço de integração contínua (CI) e entrega contínua (CD) totalmente caracterizado que funciona com o seu provedor Git preferido. O Azure Pipelines pode implantar na maioria dos principais serviços de nuvem, como os serviços do Azure. Você pode começar com seu código no GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Em seguida, você pode automatizar a compilação, o teste e a implantação do seu código no Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais características**
- **Experiência simplificada baseada em tarefas para configurar um servidor CI:** Configure um servidor CI para aplicativos móveis nativos (Android, iOS e Windows) e multiplataforma (Xamarin, Cordova e React Native).
- **Qualquer idioma, plataforma e nuvem:** Construa, teste e implante aplicativos Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Execute em paralelo no Linux, macOS e Windows. Implante para provedores de nuvem como Azure, AWS e Google Cloud Platform. Distribua aplicativos móveis através de canais beta e lojas de aplicativos.
- **Suporte a contêineres nativos:** Crie novos contêineres com facilidade e empurre-os para qualquer registro. Implante contêineres para hosts independentes ou Kubernetes.
- **Fluxos de trabalho e recursos avançados:** Crie facilmente cadeias de compilação e compilações multifásicas. Obtenha suporte para YAML, integração de teste, portões de liberação, relatórios e muito mais.
- **Extensível:** Use uma série de tarefas de construção, teste e implantação construídas pela comunidade, que inclui centenas de extensões do Slack ao SonarCloud. Você pode até mesmo implantar de outros sistemas de COI, como Jenkins. Ganchos web e APIs REST podem ajudá-lo a se integrar.
- **Compilações gratuitas hospedadas na nuvem:** Essas construções estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação para outros fornecedores de nuvem:** Os fornecedores incluem a AWS e a Google Cloud Platform.

**Referências**
- [Comece com o guia azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribua seu aplicativo diretamente para app stores
Depois que seu aplicativo estiver pronto para uso de produção e você quiser que ele seja usado publicamente, ele precisa ser submetido a lojas de aplicativos onde pode ser baixado pelos clientes. Existem várias maneiras de distribuir seu aplicativo diretamente para lojas de aplicativos. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Com [o App Center Distribute,](/appcenter/distribution/stores/)você pode publicar seus aplicativos móveis diretamente nas lojas de aplicativos. Depois que seu aplicativo estiver pronto para ser baixado pelos usuários, você pode publicar seus binários de aplicativos diretamente do portal visual studio app center. 

Você pode distribuir diretamente para:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Na loja de aplicativos desenvolvida e mantida pela Apple, os usuários podem navegar e baixar aplicativos desenvolvidos para dispositivos iOS, MacOS, WatchOS e tvOS. Os desenvolvedores precisam enviar seus aplicativos para iOS para a Apple App Store para uso público.

### <a name="google-play"></a>Google Play

O Google Play é a loja oficial de aplicativos do Sistema Operacional Android, onde os usuários podem navegar e baixar aplicativos desenvolvidos para dispositivos Android que são publicados através do Google.

### <a name="intune"></a>Intune

[O Microsoft Intune](/intune/app-management) é um serviço baseado em nuvem no espaço de gerenciamento de mobilidade corporativa que ajuda a permitir que sua força de trabalho seja produtiva, mantendo seus dados corporativos protegidos. Com o Intune, você pode:
- gerenciar os dispositivos móveis e PCs que sua força de trabalho usa para acessar dados da empresa.
- Gerencie os aplicativos móveis que sua força de trabalho usa.
- Proteja as informações da sua empresa controlando a forma como sua força de trabalho acessa e as compartilha.
- Certifique-se de que os dispositivos e aplicativos estejam em conformidade com os requisitos de segurança da empresa.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Implantar atualizações diretamente nos dispositivos dos usuários

### <a name="codepush"></a>CodePush
Com [o CodePush](/appcenter/distribution/codepush/) no App Center, os desenvolvedores Apache Cordova e React Native podem implantar atualizações de aplicativos móveis diretamente nos dispositivos de seus usuários. Ele funciona como um repositório central para o qual os desenvolvedores podem publicar certas atualizações, como JavaScript, HTML, CSS e alterações de imagem. Em seguida, os aplicativos podem consultar atualizações do repositório usando os SDKs fornecidos pelo cliente. Desta forma, você pode ter um modelo de engajamento mais determinista e direto com seus usuários enquanto você endereça bugs ou adiciona pequenos recursos. Você não é obrigado a reconstruir um binário ou redistribuí-lo através de qualquer loja de aplicativos públicas.

**Principais características**
- Os desenvolvedores Cordova e React Native podem implantar atualizações de aplicativos móveis diretamente nos dispositivos de seus usuários sem liberar em uma loja.
- Útil para corrigir bugs ou adicionar e remover pequenos recursos que não exigem que você reconstrua binários e redistribua-os através de suas respectivas lojas.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Comece com codepush no App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)