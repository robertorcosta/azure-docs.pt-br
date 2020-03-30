---
title: Automatize o ciclo de vida de seus aplicativos com os serviços Visual Studio App Center e Azure
description: Conheça os serviços como o App Center que ajudam a configurar a construção e integração contínua sustais para seus aplicativos móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240935"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatize o ciclo de vida de seus aplicativos com construção e integração contínuas

Como desenvolvedores, você escreve código e verifica-o no repositório de código, mas os compromissos verificados no repo podem nem sempre ser consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, os problemas podem chegar à integração. As equipes podem se deparar com situações em que as coisas não funcionam, os bugs se acumulam e o desenvolvimento do projeto é adiado. Os desenvolvedores têm que esperar até que todo o código de software seja construído e testado para verificar se há erros, o que torna o processo lento e menos iterativo. 

Com a construção e integração contínuas, os desenvolvedores podem simplificar builds e testar seu código, comprometendo suas alterações no repositório de código-fonte e colocando testes e verificações no ambiente de construção. Desta forma, eles estão sempre fazendo testes contra o código. Todas as alterações feitas no código-fonte são construídas continuamente sempre que há um compromisso feito no repositório. A cada check-in, o servidor de integração contínua (CI) valida e executa qualquer teste que o desenvolvedor criou. Se os testes não passarem, o código é enviado de volta para outras alterações. Desta forma, os desenvolvedores não quebram as compilações criadas. Eles também não têm que executar todos os testes localmente em seus computadores, o que aumenta a produtividade do desenvolvedor. 

## <a name="key-benefits"></a>Principais benefícios
- Automatize suas compilações, testes e implantações para pipelines.
- Detecte bugs e corrija problemas cedo para garantir taxas de liberação mais rápidas.
- Comprometa o código com mais freqüência e crie aplicativos rapidamente.
- Obtenha flexibilidade para alterar o código rapidamente sem problemas.
- Ganhe tempo de mercado mais rápido para que apenas um código de boa qualidade o faça até o fim.
- Faça pequenas mudanças de código de forma mais eficiente porque pequenos pedaços de código são integrados ao mesmo tempo.
- Aumente a transparência da equipe e a prestação de contas para que você receba feedback contínuo de seus clientes e de sua equipe.

Use os seguintes serviços para permitir um pipeline de integração contínua em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Build](/appcenter/build/) ajuda você a criar aplicativos nativos e multiplataforma sustais em que sua equipe está trabalhando usando uma infra-estrutura de nuvem segura. Você pode facilmente conectar seu repo no Visual Studio App Center e começar a construir seu aplicativo na nuvem em cada compromisso. Você não precisa se preocupar em configurar servidores de compilação localmente, configurações complicadas e código que se baseia na máquina de um colega de trabalho, mas não na sua.

Com o poder adicional dos serviços do Visual Studio App Center, você pode automatizar ainda mais seu fluxo de trabalho. Você pode liberar automaticamente builds para testadores e lojas de aplicativos públicas com o App Center Distribute. Você também pode executar testes automatizados de iu de iu em milhares de configurações reais de dispositivos e sO na nuvem com teste do App Center.

**Principais características**
- Configure a integração contínua em minutos e crie aplicativos com mais freqüência e rapidez.
- Integre-se ao GitHub, BitBucket, Azure DevOps e GitLab.
- Crie compilações rápidas e seguras em máquinas gerenciadas e hospedadas na nuvem.
- Habilite suas compilações para iniciar o teste e verifique se o aplicativo é construído em dispositivos iOS e Android do mundo real.
- Obtenha suporte nativo e multiplataforma para iOS, Android, macOS, Windows, Xamarin e React Native.
- Personalize suas compilações adicionando scripts pós-clone, pré-construção e pós-construção.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Comece com o App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [O Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), um serviço no Azure DevOps, é um serviço de integração contínua e entrega contínua (CD) totalmente caracterizado que funciona com o seu provedor Git preferido. Ele pode implantar na maioria dos principais serviços de nuvem, o que inclui o Azure. Você pode começar com seu código no GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Em seguida, você pode automatizar a compilação, o teste e a implantação do seu código no Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais características**
- **Experiência simplificada baseada em tarefas para configurar um servidor CI:** Configure um servidor CI para aplicativos móveis nativos (Android, iOS e Windows) e multiplataforma (Xamarin, Cordova e React Native), além de tecnologias de servidor baseadas na Microsoft e não-Microsoft (Node.js, Java).
- **Qualquer idioma, plataforma e nuvem:** Construa, teste e implante aplicativos Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Execute em paralelo no Linux, macOS e Windows. Implante para provedores de nuvem como Azure, AWS e Google Cloud Platform. Distribua aplicativos móveis através de canais beta e lojas de aplicativos.
- **Suporte a contêineres nativos:** Crie novos contêineres com facilidade e empurre-os para qualquer registro. Implante contêineres para hosts independentes ou Kubernetes.
- **Fluxos de trabalho avançados:** Crie facilmente cadeias de compilação e compilações multifásicas. Obtenha suporte para YAML, integração de teste, portões de liberação, relatórios e muito mais.
- **Extensível:** Use uma série de tarefas de construção, teste e implantação construídas pela comunidade, que inclui centenas de extensões do Slack ao SonarCloud. Você pode até mesmo implantar de outros sistemas de COI, como Jenkins. Ganchos web e APIs REST podem ajudá-lo a se integrar.
- **Compilações gratuitas hospedadas na nuvem:** Essas construções estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação para outros fornecedores de nuvem:** Os fornecedores incluem a AWS e a Google Cloud Platform.

**Referências**
- [Comece com o guia azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Inícios rápidos](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Para ajudá-lo a escolher o serviço certo para as compilações de aplicativos, consulte o artigo que compara [app center build vs. Azure Pipelines](/appcenter/build/choose-between-services).
