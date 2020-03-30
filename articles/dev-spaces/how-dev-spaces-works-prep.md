---
title: Como funciona a preparação de um projeto para o Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como funciona a preparação do seu projeto com o Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241628"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Como funciona a preparação de um projeto para o Azure Dev Spaces

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente os aplicativos kubernetes e colaborar com sua equipe em um cluster Azure Kubernetes Service (AKS). O Dev Spaces pode gerar gráficos Dockerfiles e Helm para o seu projeto. O Dev Spaces também cria e usa um arquivo de configuração para implantar, executar e depurar seus aplicativos Kubernetes em AKS. Todos esses arquivos residem com o código do seu aplicativo e podem ser adicionados ao seu sistema de controle de versão.

Este artigo descreve o que acontece você prepara seu projeto para ser executado em AKS com Dev Spaces.

## <a name="prepare-your-code"></a>Prepare seu código

Para executar sua aplicação em um espaço de desenvolvimento, ele precisa ser contêiner, e você precisa definir como ele deve ser implantado no Kubernetes. Para contêiner sua aplicação, você precisa de um arquivo Docker. Para definir como sua aplicação é implantada no Kubernetes, você precisa de um [gráfico Helm](https://docs.helm.sh/). Para ajudar a criar o gráfico Dockerfile e Helm para sua `prep` aplicação, as ferramentas do lado do cliente fornecem o comando:

```cmd
azds prep --enable-ingress
```

O `prep` comando analisará os arquivos do seu projeto e tentará criar o gráfico Dockerfile e Helm para executar seu aplicativo em Kubernetes. Atualmente, `prep` o comando gerará um gráfico Dockerfile e Helm com os seguintes idiomas:

* Java
* Node.js
* .NET Core

Você *deve* `prep` executar o comando de um diretório que contenha código fonte. A `prep` execução do comando a partir do diretório correto permite que a ferramenta do lado do cliente identifique o idioma e crie um arquivo Docker apropriado para contêiner seu aplicativo. Você também pode `prep` executar o comando a partir de um diretório que contém um arquivo *pom.xml* para projetos Java.

Se você `prep` executar o comando do diretório que não contém código-fonte, a ferramenta do lado do cliente não gerará um arquivo Docker. Ele também exibirá um erro dizendo: *O Arquivo Docker não pôde ser gerado devido a uma linguagem não suportada*. Esse erro também ocorre se a ferramenta do lado do cliente não reconhecer o tipo de projeto.

Quando você `prep` executa o comando, você `--enable-ingress` tem a opção de especificar a bandeira. Este sinalizador diz ao controlador para criar um ponto final acessível à Internet para este serviço. Se você não especificar este sinalizador, o serviço só será acessível dentro do cluster ou usando o túnel localhost criado pela ferramenta do lado do cliente. Você pode ativar ou desativar esse `prep` comportamento depois de executar o comando atualizando o gráfico Helm gerado.

O `prep` comando não substituirá nenhum dockerfiles ou gráficos Helm existentes que você tenha em seu projeto. Se um gráfico Dockerfile ou Helm existente usar a mesma `prep` convenção `prep` de nomeação que os arquivos gerados pelo comando, o comando pulará a geração desses arquivos. Caso contrário, `prep` o comando gerará seu próprio gráfico Dockerfile ou Helm junto com os arquivos existentes.

> [!IMPORTANT]
> O Azure Dev Spaces usa o gráfico Dockerfile e Helm para que seu projeto construa e execute seu código, mas você pode modificar esses arquivos se quiser alterar a forma como o projeto é construído e executado.

O `prep` comando também `azds.yaml` gerará um arquivo na raiz do seu projeto. O Azure Dev Spaces usa esse arquivo para construir, instalar, configurar e executar seu aplicativo. Este arquivo de configuração lista a localização do gráfico Dockerfile e Helm e também fornece configuração adicional em cima desses artefatos.

Aqui está um exemplo de arquivo azds.yaml criado usando [o aplicativo de amostra .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

O `azds.yaml` arquivo gerado `prep` pelo comando destina-se a trabalhar para um cenário simples e único de desenvolvimento de projetos. Se o seu projeto específico tiver aumentado a complexidade, `prep` talvez seja necessário atualizar este arquivo depois de executar o comando. Por exemplo, seu projeto pode exigir algumas alterações no seu processo de construção ou lançamento com base nas suas necessidades de desenvolvimento ou depuração. Você também pode ter vários aplicativos em seu projeto, que requerem vários processos de compilação ou um conteúdo de compilação diferente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como executar seu código em seu espaço de dev, consulte [Como funciona a execução do seu código com o Azure Dev Spaces][how-it-works-up].

Para começar a usar o Azure Dev Spaces para preparar seu projeto para o Azure Dev Space, consulte as seguintes partidas rápidas:

* [Rapidamente iterado e depurado com Visual Studio Code e Java][quickstart-java]
* [Iterar rapidamente e depurar com Visual Studio Code e .NET][quickstart-netcore]
* [Rapidamente iterado e depurado com Visual Studio Code e Node.js][quickstart-node]
* [Iterar rapidamente e depurar com Visual Studio e .NET Core][quickstart-vs]
* [Usando a CLI para desenvolver um aplicativo no Kubernetes][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md