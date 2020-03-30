---
title: Como funciona a execução do seu código com o Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos de execução do seu código no Azure Kubernetes Service com o Azure Dev Spaces
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241355"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Como funciona a execução do seu código com o Azure Dev Spaces

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente os aplicativos kubernetes e colaborar com sua equipe em um cluster Azure Kubernetes Service (AKS). Uma vez que seu [projeto esteja preparado para ser executado em um espaço de dev,][how-it-works-prep]você pode usar o Dev Spaces para construir e executar seu projeto em seu cluster AKS.

Este artigo descreve o que acontece executar seu código em AKS com Dev Spaces.

## <a name="run-your-code"></a>Execute seu código

Para executar seu código em um `up` espaço de dev, `azds.yaml` emita o comando no mesmo diretório do seu arquivo:

```cmd
azds up
```

O `up` comando carrega os arquivos de origem do aplicativo e outros artefatos necessários para construir e executar seu projeto para o espaço de desenvolvimento. A partir daí, o controlador em seu espaço de vév:

1. Cria os objetos Kubernetes para implantar seu aplicativo.
1. Constrói o recipiente para sua aplicação.
1. Implanta seu aplicativo no espaço de desenvolvimento.
1. Cria um nome DNS acessível ao público para o ponto final do aplicativo, se configurado.
1. Usa *o avanço da porta* para fornecer http://localhostacesso ao ponto final do aplicativo usando .
1. Encaminha stdout e stderr para a ferramenta do lado do cliente.


## <a name="starting-a-service"></a>Começando um serviço

Quando você inicia um serviço em um espaço de desenvolvimento, a ferramenta e o controlador do lado do cliente trabalham em coordenação para sincronizar seus arquivos de origem, criar objetos de contêiner e Kubernetes e executar seu aplicativo.

Em um nível mais granular, aqui `azds up`está o que acontece quando você corre:

1. [Os arquivos são sincronizados][sync-section] do computador do usuário para um armazenamento de arquivos Azure exclusivo para o cluster AKS do usuário. O código-fonte, o gráfico Helm e os arquivos de configuração são carregados.
1. O controlador cria uma solicitação para iniciar uma nova sessão. Esta solicitação contém várias propriedades, incluindo um ID exclusivo, nome do espaço, caminho para código-fonte e um sinalizador de depuração.
1. O controlador substitui o espaço reservado *$(tag)* no gráfico Helm pelo ID de sessão exclusivo e instala o gráfico Helm para o seu serviço. Adicionar uma referência ao ID de sessão exclusivo ao gráfico Helm permite que o contêiner implantado no cluster AKS para esta sessão específica seja vinculado à solicitação de sessão e às informações associadas.
1. Durante a instalação do gráfico Helm, o servidor de admissão do webhook Kubernetes adiciona recipientes adicionais ao pod do aplicativo para instrumentação e acesso ao código-fonte do seu projeto. Os devspaces-proxy e os devspaces-proxy-init são adicionados para fornecer rastreamento HTTP e roteamento espacial. O contêiner de construção de devspaces é adicionado para fornecer ao pod acesso à instância do Docker e ao código-fonte do projeto para a construção do contêiner do aplicativo.
1. Quando o pod do aplicativo é iniciado, o contêiner de devspaces-build e o contêiner devspaces-proxy-init são usados para construir o contêiner de aplicativo. Em seguida, são iniciados os contêineres de contêiner de aplicativos e os devspaces-proxy.
1. Depois que o contêiner de aplicativo foi iniciado, a funcionalidade do lado do cliente usa a http://localhostfuncionalidade de encaminhamento da *porta* Kubernetes para fornecer acesso HTTP ao seu aplicativo . Este encaminhamento de porta conecta seu computador de desenvolvimento ao serviço em seu espaço de desenvolvimento.
1. Quando todos os contêineres da cápsula tiverem começado, o serviço está funcionando. Neste ponto, a funcionalidade do lado do cliente começa a transmitir os traços HTTP, stdout e stderr. Essas informações são exibidas pela funcionalidade do lado do cliente para o desenvolvedor.

## <a name="updating-a-running-service"></a>Atualizando um serviço em execução

Enquanto um serviço está sendo executado, o Azure Dev Spaces tem a capacidade de atualizar esse serviço se algum dos arquivos de origem do projeto mudar. O Dev Spaces também lida com a atualização do serviço de forma diferente, dependendo do tipo de arquivo que é alterado. Existem três maneiras pelas quais o Dev Spaces pode atualizar um serviço em execução:

* Atualizando diretamente um arquivo
* Reconstrução e reinicialização do processo do aplicativo dentro do contêiner do aplicativo em execução
* Reconstrução e reimplantação do contêiner do aplicativo

![Sincronização de arquivos do Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Certos arquivos de projeto que são ativos estáticos, como arquivos html, css e cshtml, podem ser atualizados diretamente no contêiner do aplicativo sem reiniciar nada. Se um ativo estático for alterado, o novo arquivo será sincronizado com o espaço de dev e, em seguida, usado pelo contêiner em execução.

Alterações em arquivos como código-fonte ou arquivos de configuração de aplicativos podem ser aplicadas reiniciando o processo do aplicativo dentro do contêiner em execução. Uma vez sincronizados esses arquivos, o processo do aplicativo é reiniciado dentro do contêiner em execução usando o processo *de devhostagent.* Ao criar inicialmente o contêiner do aplicativo, o controlador substitui o comando de inicialização do aplicativo por um processo diferente chamado *devhostagent*. O processo real do aplicativo é então executado como um processo filho sob *devhostagent*, e sua saída é canalizada usando a saída do *devhostagent.* O processo *de devhostagent* também faz parte do Dev Spaces e pode executar comandos no contêiner em execução em nome do Dev Spaces. Ao executar uma reinicialização, *devhostagent*:

* Interrompe o processo atual ou processos associados ao aplicativo
* Reconstrói o aplicativo
* Reinicia o processo ou processos associados ao aplicativo

A forma como *o devhostagent* executa as etapas anteriores está [configurada em `azds.yaml` ][azds-yaml-section].

Atualizações para arquivos de projeto, como Arquivos Docker, arquivos csproj ou qualquer parte do gráfico Helm exigem que o contêiner do aplicativo seja reconstruído e reimplantado. Quando um desses arquivos é sincronizado com o espaço de desenvolvimento, o controlador executa o comando [helm upgrade][helm-upgrade] e o contêiner do aplicativo é reconstruído e reimplantado.

## <a name="file-synchronization"></a>Sincronização de arquivos

A primeira vez que um aplicativo é iniciado em um espaço de desenvolvimento, todos os arquivos de origem do aplicativo são carregados. Enquanto o aplicativo está sendo executado e em reinicializações posteriores, apenas os arquivos alterados são carregados. Dois arquivos são usados para coordenar este processo: um arquivo do lado do cliente e um arquivo do lado do controlador.

O arquivo do lado do cliente é armazenado em um diretório temporário e é nomeado com base em um hash do diretório de projeto que você está executando no Dev Spaces. Por exemplo, no Windows você teria um arquivo como *Users\USERNAME\AppData\Local\Temp\1234567890abcdef124567890abcdef124567890abcdef1234567890abcdef1234567890abcdef.synclog* para o seu projeto. No Linux, o arquivo do lado do cliente é armazenado no diretório */tmp.* Você pode encontrar o diretório no `echo $TMPDIR` macOS executando o comando.

Este arquivo está no formato JSON e contém:

* Uma entrada para cada arquivo de projeto sincronizado com o espaço de dev
* Um ID de sincronização
* O carimbo de data e hora da última operação de sincronização

Cada entrada de arquivo do projeto contém um caminho para o arquivo e seu carimbo de tempo.

O arquivo do lado do controlador é armazenado no cluster AKS. Ele contém o ID de sincronização e o carimbo de data e hora da última sincronização.

Uma sincronização acontece quando os carimbos de tempo de sincronização não correspondem entre os arquivos do lado do cliente e do lado do controlador. Durante uma sincronização, as ferramentas do lado do cliente iterates sobre as entradas de arquivo no arquivo do lado do cliente. Se o carimbo de tempo do arquivo estiver após o carimbo de tempo de sincronização, esse arquivo será sincronizado com o espaço de dev. Uma vez que a sincronização esteja concluída, os carimbos de tempo de sincronização são atualizados nos arquivos do lado do cliente e do lado do controlador.

Todos os arquivos do projeto são sincronizados se o arquivo do lado do cliente não estiver presente. Esse comportamento permite que você force uma sincronização completa excluindo o arquivo do lado do cliente.

## <a name="how-running-your-code-is-configured"></a>Como a execução do seu código é configurada

O Azure Dev `azds.yaml` Spaces usa o arquivo para instalar e configurar seu serviço. O controlador `install` usa a `azds.yaml` propriedade no arquivo para instalar o gráfico Helm e criar os objetos Kubernetes:

```yaml
...
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
...
```

Por padrão, `prep` o comando gerará o gráfico Helm. Ele também define a propriedade *install.chart* para o diretório do gráfico Helm. Se você quiser usar um gráfico Helm em um local diferente, você pode atualizar esta propriedade para usar esse local.

Ao instalar os gráficos Helm, o Azure Dev Spaces fornece uma maneira de substituir valores no gráfico Helm. Os valores padrão para `charts/APP_NAME/values.yaml`o gráfico Helm estão em .

Usando a propriedade *install.values,* você pode listar um ou mais arquivos que definem valores que você deseja substituídos no gráfico Helm. Por exemplo, se você queria um nome de host ou configuração de banco de dados especificamente ao executar seu aplicativo em um espaço de desenvolvimento, você pode usar essa funcionalidade de substituição. Você também pode adicionar um *?* no final de qualquer um dos nomes de arquivo para defini-lo como opcional.

A propriedade *install.set* permite configurar um ou mais valores que você deseja substituídos no gráfico Helm. Quaisquer valores configurados no *install.set* substituirão os valores configurados em arquivos listados em *install.values*. As propriedades em *install.set* dependem dos valores no gráfico Helm e podem ser diferentes dependendo do gráfico Helm gerado.

No exemplo acima, a propriedade *install.set.replicaCount* informa ao controlador quantas instâncias do seu aplicativo serão executadas no espaço de desenvolvimento. Dependendo do seu cenário, você pode aumentar esse valor, mas terá um impacto na anexação de um depurador ao pod do seu aplicativo. Para obter mais informações, consulte o [artigo sobre solução de problemas][troubleshooting].

No gráfico Helm gerado, a imagem do contêiner é definida como *{{ . Values.image.repository }}:{{{ . Values.image.tag }}*. O `azds.yaml` arquivo define *install.set.image.tag* property como *$(tag)* por padrão, que é usado como o valor para *{{ . Values.image.tag }}*. Ao definir a propriedade *install.set.image.tag* desta forma, ele permite que a imagem do contêiner para o aplicativo seja marcada de uma maneira distinta ao executar o Azure Dev Spaces. Neste caso específico, a imagem é marcada como * \<valor de image.repository>:$(tag)*. Você deve usar a variável *$(tag)* como o valor de *install.set.image.tag* para que os Espaços de Dev reconheçam e localizem o contêiner no cluster AKS.

No exemplo acima, `azds.yaml` define *install.set.ingress.hosts*. A propriedade *install.set.ingress.hosts* define um formato de nome de host para pontos finais públicos. Esta propriedade também usa *$(spacePrefix)*, *$(rootSpacePrefix)* e *$(hostSufix)*, que são valores fornecidos pelo controlador.

O *$(spacePrefix)* é o nome do espaço de desenvolvimento infantil, que assume a forma de *SPACENAME.s*. O *$(rootSpacePrefix)* é o nome do espaço pai. Por exemplo, se *o azureuser* é um espaço infantil de *padrão,* o valor para *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* é *azureuser.s*. Se o espaço não for um espaço infantil, *$(spacePrefix)* está em branco. Por exemplo, se o espaço *padrão* não tiver espaço para os pais, o valor de *$(rootSpacePrefix)* é *padrão* e o valor de *$(spacePrefix)* está em branco. O *$(hostSufix)* é um sufixo DNS que aponta para o Azure Dev Spaces Ingress Controller que é executado em seu cluster AKS. Este sufixo DNS corresponde a uma entrada DNS curinga, por * \*exemplo. RANDOM_VALUE.eus.azds.io*, que foi criado quando o controlador Azure Dev Spaces foi adicionado ao seu cluster AKS.

No arquivo `azds.yaml` acima, você também pode atualizar *install.set.ingress.hosts* para alterar o nome do host do seu aplicativo. Por exemplo, se você quiser simplificar o nome de host do seu aplicativo de *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSufix)* para *$(spacePrefix)$(rootSpacePrefix)web$(hostSufix)*.

Para construir o contêiner para sua aplicação, o `azds.yaml` controlador usa as seções abaixo do arquivo de configuração:

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

O controlador usa um arquivo Docker para construir e executar seu aplicativo.

A propriedade *build.context* lista o diretório onde os arquivos Docker existem. A propriedade *build.dockerfile* define o nome do Arquivo Docker para construir a versão de produção do aplicativo. A propriedade *configurations.build.dockerfile* configura o nome do Arquivo Docker para a versão de desenvolvimento do aplicativo.

Ter diferentes arquivos Docker para desenvolvimento e produção permite que você habilite certas coisas durante o desenvolvimento e desative esses itens para implantações de produção. Por exemplo, você pode ativar a depuração ou mais registro de verbose durante o desenvolvimento e desativar em um ambiente de produção. Você também pode atualizar essas propriedades se seus arquivos Docker forem nomeados de forma diferente ou estiverem em um local diferente.

Para ajudá-lo a iterar rapidamente durante o desenvolvimento, o Azure Dev Spaces sincronizará alterações do seu projeto local e atualizará gradualmente seu aplicativo. A seção `azds.yaml` abaixo no arquivo de configuração é usada para configurar a sincronização e a atualização:

```yaml
...
configurations:
  develop:
    ...
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
...
```

Os arquivos e diretórios que sincronizarão as alterações estão listados na propriedade *configurations.develop.container.sync.* Esses diretórios são sincronizados inicialmente quando você executa o `up` comando, bem como quando as alterações são detectadas. Se houver diretórios adicionais ou diferentes que você gostaria de sincronizar com o seu espaço de desenvolvimento, você pode alterar esta propriedade.

A propriedade *configurations.development.container.iterate.buildCommands* especifica como construir o aplicativo em um cenário de desenvolvimento. A propriedade *configurations.development.container.command* fornece o comando para executar o aplicativo em um cenário de desenvolvimento. Você pode querer atualizar qualquer uma dessas propriedades se houver sinalizadores ou parâmetros adicionais de compilação ou tempo de execução que você gostaria de usar durante o desenvolvimento.

As *configurações.develop.container.iterate.processesToKill* lista os processos a serem matam para parar o aplicativo. Você pode querer atualizar esta propriedade se quiser alterar o comportamento de reinicialização do seu aplicativo durante o desenvolvimento. Por exemplo, se você atualizou as *configurações.develop.container.iterate.buildCommands* ou *configurations.develop.container.command* properties para alterar a forma como o aplicativo é construído ou iniciado, você pode precisar alterar quais processos estão parados.

Ao preparar seu código `azds prep` usando o comando, você `--enable-ingress` tem a opção de adicionar o sinalizador. A `--enable-ingress` adição do sinalizador cria uma URL acessível ao público para o seu aplicativo. Se você omiti-lo este sinalizador, o aplicativo só será acessível dentro do cluster ou usando o túnel localhost. Depois de `azds prep` executar o comando, você pode alterar essa configuração modificando a propriedade *ativada por ingressem* em `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o networking e como as solicitações são roteadas no Azure Dev Spaces, consulte [como o roteamento funciona com o Azure Dev Spaces][how-it-works-routing].

Para saber mais sobre como usar o Azure Dev Spaces para iterar e desenvolver rapidamente, consulte [Como conectar seu computador de desenvolvimento ao seu espaço de desenvolvimento funciona][how-it-works-connect] e como funciona a [depuração remota do seu código com o Azure Dev Spaces][how-it-works-remote-debugging].

Para começar a usar o Azure Dev Spaces para executar o seu projeto, consulte as seguintes partidas rápidas:

* [Rapidamente iterado e depurado com Visual Studio Code e Java][quickstart-java]
* [Iterar rapidamente e depurar com Visual Studio Code e .NET][quickstart-netcore]
* [Rapidamente iterado e depurado com Visual Studio Code e Node.js][quickstart-node]
* [Iterar rapidamente e depurar com Visual Studio e .NET Core][quickstart-vs]
* [Usando a CLI para desenvolver um aplicativo no Kubernetes][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md