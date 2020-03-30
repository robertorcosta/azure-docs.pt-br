---
title: Como o Visual Studio Code funciona com o Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Saiba como o Visual Studio Code e o Azure Dev Spaces ajudam você a depurar e iterar rapidamente seus aplicativos Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240457"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Como o Visual Studio Code funciona com o Azure Dev Spaces

Você pode usar o Visual Studio Code e a [extensão Azure Dev Spaces][azds-extension] para preparar, executar e depurar seus serviços com o Azure Dev Spaces. Com o Visual Studio Code e a extensão Azure Dev Spaces, você pode:

* Gerar ativos para executar e depurar serviços em AKS
* Execute seus serviços Java, Node.js e .NET Core em um espaço de desenvolvimento
* Depurar diretamente seus serviços Java, Node.js e .NET Core em execução em um espaço de desenvolvimento

## <a name="generate-assets"></a>Gerar ativos

Visual Studio Code e a extensão Azure Dev Spaces geram os seguintes ativos para o seu projeto:

* Arquivos docker para aplicativos Java usando aplicativos Maven, Node.js e .NET Core
* Gráficos de leme para quase qualquer idioma com um arquivo Docker
* Um `azds.yaml` arquivo, que é o [arquivo de configuração Do Azure Dev Spaces][azds-yaml] para o seu projeto
* Uma `.vscode` pasta com a configuração de lançamento do Visual Studio Code do seu projeto para aplicativos Java usando aplicativos Maven, Node.js e .NET Core

O arquivo Docker, o `azds.yaml` gráfico Helm e os `azds prep`arquivos são os mesmos ativos gerados ao serem executados . Esses arquivos também podem ser usados fora do código do Visual `azds up`Studio para executar seu projeto em AKS, como a execução . A `.vscode` pasta só é usada pelo código Visual Studio para executar seu projeto em AKS a partir do Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Execute seu serviço em AKS

Depois de gerar os ativos para o seu projeto, você pode executar seus serviços Java, Node.js e .NET Core em um espaço de desenvolvimento existente a partir do Visual Studio Code. Na página *Debug* do Visual Studio Code, você `.vscode` pode invocar a configuração de lançamento do diretório para executar seu projeto.

Você deve criar seu cluster AKS e habilitar espaços azure Dev em seu cluster fora do Visual Studio Code. Por exemplo, você pode usar o Azure CLI ou o portal Azure para fazer essa configuração. Você pode reutilizar os arquivos Docker `azds.yaml` existentes, gráficos helm e arquivos criados fora `azds prep`do Visual Studio Code, como os ativos gerados pela execução . Se você reutilizar ativos gerados fora do Visual Studio `.vscode` Code, você ainda precisa ter um diretório. Este `.vscode` diretório pode ser regenerado pelo código visual studio e pela extensão Azure Dev Spaces e não substituirá seus ativos existentes.

Para projetos .NET Core, você deve ter a [extensão C#][csharp-extension] instalada para executar seu serviço .NET a partir do Visual Studio Code. Também para projetos Java usando o Maven, você deve ter o [Java Debugger for Azure Dev Spaces][java-extension] instalado, bem como [o Maven instalado e configurado][maven] para executar o serviço Java a partir do Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Depurar seu serviço em AKS

Depois de iniciar seu projeto, você pode depurar seus serviços Java, Node.js e .NET Core em execução em um espaço de desenvolvimento diretamente do Visual Studio Code. A configuração `.vscode` de lançamento no diretório fornece as informações adicionais de depuração para executar um serviço com depuração ativada em um espaço de dev. Visual Studio Code também se anexa ao processo de depuração no contêiner em execução em seus espaços de dev, permitindo que você defina pontos de interrupção, inspecione variáveis e realize outras operações de depuração.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Use o Visual Studio Code com espaços Azure Dev

Você pode ver o Visual Studio Code e a extensão Azure Dev Spaces trabalhando com o Azure Dev Spaces nas seguintes partidas rápidas:

* [Rapidamente iterado e depurado com Visual Studio Code e Java][quickstart-java]
* [Iterar rapidamente e depurar com Visual Studio Code e .NET][quickstart-netcore]
* [Rapidamente iterado e depurado com Visual Studio Code e Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
