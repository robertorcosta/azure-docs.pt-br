---
title: Usar CI/CD com o Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Saiba como configurar a integração contínua/implantação contínua usando devOps do Azure com o Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: 66ff2080ad44098757a5d9360fd3307e65f7431a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438454"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Usar CI/CD com o Azure Dev Spaces

Este artigo orienta você pela configuração da integração contínua/implantação contínua (CI/CD) ao Serviço de Kubernetes do Azure (AKS) com o Dev Spaces habilitado. A CI/CD para o AKS permite que as atualizações de aplicativo sejam implantadas automaticamente sempre que o código confirmado é enviado para seu repositório de origem. Usar a CI/CD em conjunto com um cluster habilitado para o Dev Spaces é útil porque pode manter uma linha de base do aplicativo atualizada para o trabalho da equipe.

![Exemplo de diagrama CI/CD](../media/common/ci-cd-simple.png)

Embora este artigo oriente você com o Azure DevOps, os mesmos conceitos se aplicariam aos sistemas de CI/CD, como Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Pré-requisitos
* [Cluster do Serviço de Kubernetes do Azure (AKS) com o Azure Dev Spaces habilitado](../get-started-netcore.md)
* [CLI do Azure Dev Spaces instalado](upgrade-tools.md)
* [Organização do Azure DevOps com um projeto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Registro de Contêiner do Azure (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Detalhes disponíveis da [conta do administrador](../../container-registry/container-registry-authentication.md#admin-account) do Registro de Contêiner do Azure
* [Autorizar o cluster do AKS para efetuar pull de seu Registro de Contêiner do Azure](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Baixar código de exemplo
Por uma questão de tempo, vamos criar uma bifurcação do nosso repositório GitHub de código de amostra. Acesse https://github.com/Azure/dev-spaces e selecione **Bifurcação**. Quando o processo de bifurcação estiver completo, **Clone** sua versão com bifurcação do repositório localmente. Por padrão o branch _mestre_ será verificado, mas incluímos algumas alterações de economia de tempo no branch _azds_updates_, que também deve ter sido transferido durante o processo de bifurcação. O branch _azds_updates_ contém atualizações que pedimos que você faça manualmente nas seções do tutorial do Dev Spaces, bem como alguns arquivos YAML e JSON pré-criados para simplificar a implantação do sistema CI/CD. Você pode usar um comando como `git checkout -b azds_updates origin/azds_updates` para verificar o branch _azds_updates_ em seu repositório local.

## <a name="dev-spaces-setup"></a>Configuração do Dev Spaces
Crie um novo espaço chamado _dev_ usando o comando `azds space select`. O espaço _dev_ será usado por seu pipeline de CI/CD para enviar suas alterações de código. Ele também será usado para criar _espaços filhos_ baseados no _dev_.

```cmd
azds space select -n dev
```

Quando solicitado a selecionar um espaço _ \<de\>_ dev pai, selecione nenhum .

Depois que seu espaço de v é criado, você precisa determinar o sufixo do host. Use `azds show-context` o comando para mostrar o sufixo de host do Controlador de Entrada de Espaços Do Azure Dev.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

No exemplo acima, o sufixo host é _fedcba098.eus.azds.io_. Esse valor é usado mais tarde ao criar sua definição de versão.

O espaço _dev_ sempre conterá o estado mais recente do repositório, uma linha de base, para que os desenvolvedores possam criar _espaços filho_ a partir do _dev_ para testar suas alterações isoladas dentro do contexto do aplicativo maior. Esse conceito é discutido com mais detalhes nos tutoriais do Dev Spaces.

## <a name="creating-the-build-definition"></a>Criar a definição de build
Abra seu projeto de equipe do Azure DevOps em um navegador da Web e navegue até a seção _Pipelines_. Primeiro, clique na sua foto de perfil no canto superior direito do site do Azure DevOps, abra o painel de versões prévias do recurso e desative a opção _Nova experiência de criação de pipeline YAML_:

![Abertura do painel de versões prévias do recurso](../media/common/preview-feature-open.png)

A opção para desabilitar:

![Opção Nova experiência de criação de pipeline YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> A versão prévia do recurso do Azure DevOps _Nova experiência de criação de pipeline YAML_ entra em conflito com a criação de pipelines de build pré-definidos no momento. É preciso desabilitá-lo por enquanto para implantar nosso pipeline de build predefinido.

No branch _azds_updates_, incluímos um simples [Azure Pipeline YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) que define as etapas de compilação necessárias para *mywebapi* e *webfrontend*.

Dependendo da linguagem escolhida, o pipeline YAML foi registrado em um caminho semelhante a: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para criar um pipeline desse arquivo:
1. Na página principal do projeto DevOps, navegue até pipelines > Builds.
1. Selecione a opção para criar um **novo** pipeline de compilação.
1. Selecione **o GitHub** como fonte, autorize com sua conta do GitHub, se necessário, e selecione o ramo _azds_updates_ da sua versão bifurcada do repositório de aplicativos de amostra de espaços _de desenvolvimento._
1. Selecione **Configuração como código,** ou **YAML,** como seu modelo.
1. Você agora verá uma página de configuração para o pipeline de build. Como mencionado acima navegue para o caminho específico do idioma para o **caminho do arquivo YAML** usando o **botão ...** Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Vá para a guia **Variáveis.**
1. Adicione manualmente _dockerId_ como uma variável, que é o nome de usuário de sua [conta de administrador do Registro de Contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado nos pré-requisitos do artigo)
1. Adicione manualmente _dockerPassword_ como uma variável, que é a senha da sua conta do administrador do [Registro de Contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de especificar _dockerPassword_ como um Segredo (selecionando o ícone de bloqueio) para fins de segurança.
1. Selecione **Salvar & fila**.

Agora você tem uma solução de IC que irá criar automaticamente os itens *mywebapi* e *webfrontend* para qualquer atualização enviada para o branch _azds_updates_ para a sua bifurcação do GitHub. Você pode verificar se as imagens do Docker foram empurradas navegando até o portal Azure, selecionando seu Registro de Contêineres do Azure e navegando na guia **Repositórios.** Pode levar vários minutos para que as imagens se construam e apareçam no registro do contêiner.

![Repositórios de Registro de Contêiner do Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criar a definição da versão

1. Na página principal do projeto DevOps, navegue até Pipelines > Versões
1. Se você estiver trabalhando em um projeto DevOps totalmente novo que ainda não contenha uma definição de versão, primeiro será necessário criar uma definição de versão vazia antes de continuar. A opção Importar não é exibida na interface do usuário até que você tenha uma definição de versão existente.
1. À esquerda, clique no botão **+ Novo** e clique em Importar **um pipeline**.
1. Clique **em** `samples/release.json` Procurar e selecione a partir de seu projeto.
1. Clique em **OK**. Observe que o painel Pipeline foi carregado com a página de edição da definição de versão. Observe também que há alguns ícones de aviso vermelhos indicando detalhes específicos do cluster que ainda precisam ser configurados.
1. À esquerda do painel Pipeline, clique na bolha **Adicionar um artefato**.
1. Na **gota** de origem, selecione o pipeline de compilação que você criou anteriormente.
1. Para a **versão Padrão,** escolha **Mais recente no ramo padrão do pipeline de compilação com tags**.
1. Deixe **as etiquetas** vazias.
1. Defina o **Alias de Origem** como `drop`. O valor **de alias source** é usado pelas tarefas de versão predefinidas, por isso deve ser definido.
1. Clique em **Adicionar**.
1. Agora clique no ícone de raio na origem de artefatos `drop` recém-criada, conforme mostrado abaixo:

    ![Configuração da implantação contínua de artefato de versão](../media/common/release-artifact-cd-setup.png)
1. Habilite o **gatilho de implantação contínua**.
1. Passar o mouse sobre a guia **Tarefas** ao lado **de Pipeline** e clique _em dev_ para editar as tarefas de fase de _dev._
1. Verifique **se o Gerenciador de recursos do Azure** está selecionado em **Tipo de Conexão.** e você vê os três controles de ![isto destacados em vermelho: Configuração de definição de liberação](../media/common/release-setup-tasks.png)
1. Selecione a assinatura do Azure que você está usando com o Azure Dev Spaces. Você também pode precisar clicar em **Autorizar**.
1. Selecione o grupo de recursos e o cluster que você está usando com o Azure Dev Spaces.
1. Clique no **trabalho de Agente**.
1. Selecione **Ubuntu 1604 hospedado** no **pool Agent**.
1. Passar o mouse sobre o seletor **de tarefas** na parte superior, clique em _prod_ para editar as tarefas de estágio _prod._
1. Verifique **se o Gerenciador de recursos do Azure** está selecionado em **Tipo de Conexão.** e selecione a assinatura, o grupo de recursos e o cluster do Azure que você está usando com o Azure Dev Spaces.
1. Clique no **trabalho de Agente**.
1. Selecione **Ubuntu 1604 hospedado** no **pool Agent**.
1. Clique na guia **Variáveis** para atualizar as variáveis para sua liberação.
1. Atualize o valor do **DevSpacesHostSufix** de **UPDATE_ME** para o sufixo host. O sufixo host é exibido `azds show-context` quando você executou o comando mais cedo.
1. Clique em **Salvar** no canto superior direito e em **OK**.
1. Clique em **+ Versão** (ao lado do botão Salvar) e em **Criar uma versão**.
1. Em **Artefatos,** verifique se a última compilação do seu pipeline de construção está selecionada.
1. Clique em **Criar**.

Agora, um processo de versão automatizado será iniciado, implantando os gráficos *mywebapi* e *webfrontend* em seu cluster do Kubernetes no espaço de nível superior _dev_. Você pode monitorar o progresso de sua versão no portal web Azure DevOps:

1. Navegue até a seção **Lançamentos** em **Pipelines**.
1. Clique no pipeline de liberação para a aplicação da amostra.
1. Clique no nome da versão mais recente.
1. Passar o mouse sobre a caixa **de desenvolvimento** em **Estágios** e clique **em Logs**.

A liberação é feita quando todas as tarefas estão completas.

> [!TIP]
> Se a sua versão falhar com uma mensagem de erro como *FALHA NO UPGRADE: expirou aguardando a condição*, tente inspecionar os pods em seu cluster [usando o painel do Kubernetes](../../aks/kubernetes-dashboard.md). Se você ver que os pods estão falhando em iniciar com mensagens de erro como *Falha em puxar a imagem https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: "azdsexample.azurecr.io/mywebapi:122": erro rpc: código = desc desconhecido = Resposta de erro do daemon: Não ser autorizado: autenticação necessária,* pode ser porque seu cluster não foi autorizado a puxar do seu Registro de Contêiner do Azure. Certifique-se de ter concluído o pré-requisito [Autorizar o cluster do AKS a receber do seu Registro de Contêiner do Azure](../../aks/cluster-container-registry-integration.md).

Agora você tem um pipeline de CI/CD totalmente automatizado para sua bifurcação do GitHub dos aplicativos de amostra do Dev Spaces. Sempre que você confirmar e enviar um código, o pipeline de build criará e enviará as imagens *mywebapi* e *webfrontend* para sua instância de ACR personalizada. Em seguida, o pipeline de versão implantará o gráfico de Helm para cada aplicativo no espaço _dev_ em seu cluster habilitado para Dev Spaces.

## <a name="accessing-your-_dev_-services"></a>Acessar seus serviços _dev_
Após a implantação, a versão _dev_ do *webfrontend* pode ser acessada com uma URL pública como: `http://dev.webfrontend.fedcba098.eus.azds.io`. Você pode encontrar esta `azds list-uri` URL executando o comando: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implantação em Produção

Para promover manualmente uma versão específica para _prod_ usando o sistema CI/CD criado neste tutorial:
1. Navegue até a seção **Lançamentos** em **Pipelines**.
1. Clique no pipeline de liberação para a aplicação da amostra.
1. Clique no nome da versão mais recente.
1. Passar o mouse sobre a caixa **de prod** em **Estágios** e clique **em Implantar**.
    ![Promova para Produção](../media/common/prod-promote.png)
1. Passar o mouse sobre a caixa **de prod** novamente em **Stages** e clique **em Logs**.

A liberação é feita quando todas as tarefas estão completas.

O estágio _prod_ do pipeline CI/CD usa um balanceador de carga em vez do controlador Dev Spaces Ingress para fornecer acesso a serviços _prod._ Os serviços implantados no estágio _prod_ são acessíveis como endereços IP em vez de nomes DNS. Em um ambiente de produção, você pode optar por criar seu próprio controlador Ingress para hospedar seus serviços com base na sua própria configuração de DNS.

Para determinar o IP do serviço webfrontend, clique na etapa **IP pública do Print webfrontend** para expandir a saída de log. Use o IP exibido na saída de log para acessar o aplicativo **webfrontend.**

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentação do Dev Spaces na produção
Embora a instrumentação do Dev Spaces tenha sido projetada para _não_ atrapalhar a operação normal de seu aplicativo, recomendamos executar suas cargas de trabalho de produção em um namespace do Kubernetes que não esteja habilitado com Dev Spaces. Usar esse tipo de namespace do Kubernetes significa que você deve criar seu namespace de produção usando a CLI do `kubectl` ou permitir que seu sistema de CI/CD o crie durante a primeira implantação de Helm. _Selecionar_ ou criar um espaço usando a ferramenta Dev Spaces adicionará instrumentação do Dev Spaces a esse namespace.

Aqui está um exemplo de estrutura de namespace que dá suporte ao desenvolvimento de recursos, o ambiente 'dev', _e_ produção, tudo em um único cluster do Kubernetes:

![Exemplo de estrutura de namespace](../media/common/cicd-namespaces.png)

> [!Tip]
> Se você já tiver criado um espaço `prod` e simplesmente gostaria de excluí-lo da instrumentação do Dev Spaces (sem excluí-lo!), poderá fazê-lo com o seguinte comando da CLI do Dev Spaces:
>
> `azds space remove -n prod --no-delete`
>
> Talvez seja necessário excluir todos os pods no namespace `prod` depois de fazer isso, para que possam ser recriados sem a instrumentação do Dev Spaces.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento da equipe usando o Azure Dev Spaces](../team-development-netcore.md)