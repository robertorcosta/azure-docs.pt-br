---
title: Solução de problemas
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Aprenda a solucionar problemas e resolver problemas comuns ao ativar e usar espaços de dev do Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: c12dfd385962d8dd7de8239a0d4ecd46746499c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239775"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Solução de problemas do Azure Dev Spaces

Este guia contém informações sobre problemas comuns que você pode ter ao usar o Azure Dev Spaces.

Se você tiver um problema ao usar o Azure Dev Spaces, crie um [problema no repositório Azure Dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Antes de começar

Para solucionar problemas de maneira mais eficaz, pode ser útil criar logs mais detalhados para revisão.

Para ver a extensão do Visual Studio, defina a variável de ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` como 1. Certifique-se de reiniciar o Visual Studio para que a variável de ambiente tenha efeito. Uma vez habilitado, os logs detalhados são gravados no seu diretório `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Na CLI, você pode gerar mais informações durante a execução do comando usando o switch `--verbose`. Também é possível pesquisar logs mais detalhados em `%TEMP%\Azure Dev Spaces`. Em um Mac, o diretório *TEMP* `echo $TMPDIR` pode ser encontrado correndo de uma janela de terminal. Em um computador Linux, o diretório `/tmp` *TEMP* é geralmente . Além disso, verifique se o registro está ativado no [arquivo de configuração do Azure CLI](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

O Azure Dev Spaces também funciona melhor ao depurar uma única instância ou pod. O `azds.yaml` arquivo contém uma configuração, *replicaCount,* que indica o número de pods que o Kubernetes executa para o seu serviço. Se você alterar a *réplicaContagem* para configurar seu aplicativo para executar vários pods para um determinado serviço, o depurador será anexado ao primeiro pod, quando listado em ordem alfabética. O depurador será anexado a um pod diferente quando o pod original for reciclado, provavelmente causando um comportamento inesperado.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Problemas comuns ao habilitar espaços de dev do Azure

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falha ao criar o controlador Azure Dev Spaces"

Você pode ver esse erro quando algo der errado com a criação do controlador. Se esse for um erro transitório, exclua e recrie o controlador para corrigi-lo.

Você também pode tentar excluir o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Use o Azure Dev Spaces CLI para excluir um controlador. Não é possível excluir um controlador do Visual Studio. Você também não pode instalar o Azure Dev Spaces CLI no Azure Cloud Shell para que você não possa excluir um controlador do Azure Cloud Shell.

Se você não tiver o Azure Dev Spaces CLI instalado, você pode primeiro instalá-lo usando o seguinte comando e excluir o controlador:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Recriar o controlador pode ser feito a partir do CLI ou Visual Studio. Consulte o [desenvolvimento da equipe](quickstart-team-development.md) ou desenvolva com o [.NET Core](quickstart-netcore-visualstudio.md) quickstarts para exemplos.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Controlador criar falha por causa do comprimento do nome do controlador

O nome de um controlador do Azure Dev Spaces não pode ter mais de 31 caracteres. Se o nome do seu controlador exceder 31 caracteres quando você habilitar Espaços de Dev em um cluster AKS ou criar um controlador, você receberá um erro. Por exemplo: 

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Para corrigir esse problema, crie um controlador com um nome alternativo. Por exemplo: 

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Ativando espaços de dev falhando quando os pools de nós do Windows são adicionados a um cluster AKS

Atualmente, o Azure Dev Spaces destina-se a ser executado apenas em pods e nós Linux. Quando você tem um cluster AKS com um pool de nós do Windows, você deve garantir que os pods do Azure Dev Spaces sejam programados apenas em nós Linux. Se um pod Do Azure Dev Spaces estiver programado para ser executado em um nó do Windows, esse pod não será inicializado e a ativação do Dev Spaces falhará.

Para corrigir esse problema, [adicione uma mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) ao seu cluster AKS para garantir que os pods Linux não estejam programados para serem executados em um nó do Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "Não encontrei nós Linux não contaminados no estado Pronto no cluster. Precisa haver pelo menos um nó Linux não contaminado no estado Pronto para implantar pods no namespace 'azds'."

O Azure Dev Spaces não conseguiu criar um controlador no seu cluster AKS porque não conseguiu encontrar um nó não contaminado em um estado *pronto* para agendar pods. O Azure Dev Spaces requer pelo menos um nó Linux em um estado *Pronto* que permite agendar pods sem especificar tolerâncias.

Para corrigir esse problema, [atualize sua configuração de mancha](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) no cluster AKS para garantir que pelo menos um nó Linux permita agendar pods sem especificar tolerâncias. Além disso, certifique-se de que pelo menos um nó Linux que permita o agendamento de pods sem especificar tolerâncias está no estado *Pronto.* Se seu nó está demorando muito para chegar ao estado *pronto,* você pode tentar reiniciar seu nó.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "Azure Dev Spaces CLI não instalado corretamente" ao executar aks use-dev-spaces

Uma atualização para o Azure Dev Spaces CLI alterou seu caminho de instalação. Se você estiver usando uma versão do Azure CLI antes de 2.0.63, você pode ver esse erro. Para exibir sua versão do Azure CLI, use `az --version`.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao ser executado `az aks use-dev-spaces` com uma versão do Azure CLI antes de 2.0.63, a instalação é bem sucedida. Você pode continuar `azds` a usar sem problemas.

Para corrigir esse problema, atualize sua instalação do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) para 2.0.63 ou posterior. Esta atualização resolverá a mensagem `az aks use-dev-spaces`de erro que você recebe ao ser executado . Alternativamente, você pode continuar a usar a versão atual do Azure CLI e do Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Erro "Não é possível acessar kube-apiserver"

Você pode ver esse erro quando o Azure Dev Spaces não puder se conectar ao servidor aPI do cluster AKS. 

Se o acesso ao servidor AKS cluster API estiver bloqueado ou se você tiver [os intervalos de endereços IP autorizados pelo servidor API](../aks/api-server-authorized-ip-ranges.md) habilitados para o cluster AKS, você também deve [criar](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) ou [atualizar](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) seu cluster para [permitir intervalos adicionais com base na sua região](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Certifique-se de que o servidor API está disponível executando comandos kubectl. Se o servidor de API não estiver disponível, entre em contato com o suporte da AKS e tente novamente quando o servidor de API estiver funcionando.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problemas comuns ao preparar seu projeto para o Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso "O arquivo Docker não pôde ser gerado devido a uma linguagem sem suporte"
O Azure Dev Spaces fornece suporte nativo para C# e Node.js. Quando você `azds prep` é executado em um diretório com código escrito em um desses idiomas, o Azure Dev Spaces cria automaticamente um Arquivo Docker apropriado para você.

Você ainda pode usar o Azure Dev Spaces com código escrito em outros idiomas, mas você precisa criar manualmente o Arquivo Docker antes de ser executado `azds up` pela primeira vez.

Se o aplicativo estiver escrito em um idioma que o Azure Dev Spaces não suporta nativamente, você precisará fornecer um Arquivo Docker apropriado para construir uma imagem de contêiner executando seu código. O Docker fornece uma lista de [melhores práticas para gravar Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/), bem como uma [referência Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a gravar um Dockerfile que se ajuste às suas necessidades.

Uma vez que você tenha um `azds up` Arquivo Docker apropriado no lugar, você executa para executar sua aplicação no Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problemas comuns ao iniciar ou interromper serviços com o Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Erro "Arquivo de configuração não encontrado:"

Ao `azds up`correr, você pode ver este erro. Ambos `azds up` `azds prep` devem ser executados a partir do diretório raiz do projeto que você deseja executar em seu espaço de dev.

Para corrigir esse problema:
1. Altere o diretório atual para a pasta raiz que contém o código de serviço. 
1. Se você não tiver um arquivo _azds.yaml_ na `azds prep` pasta de código, execute para gerar ativos Docker, Kubernetes e Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Tempo em "Esperando a construção da imagem do contêiner..." passo com os ádenos virtuais AKS

Esse tempo de tempo ocorre quando você tenta usar o Dev Spaces para executar um serviço configurado para ser executado em um [nó virtual AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Atualmente, o Dev Spaces não suporta serviços de construção ou depuração em nomes virtuais.

Se você executar `azds up` com a opção `--verbose` ou habilitar o log detalhado no Visual Studio, você verá detalhes adicionais:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o pod do serviço foi atribuído ao *virtual-node-aci-linux*, que é um nó virtual.

Para corrigir esse problema, atualize o gráfico Helm para que o serviço remova quaisquer valores *de nodeSelector* ou *tolerâncias* que permitam que o serviço seja executado em um nó virtual. Normalmente, esses valores são definidos no arquivo `values.yaml` do gráfico.

Você ainda pode usar um cluster AKS que tem o recurso de nós virtuais ativado, se o serviço que você deseja construir ou depurar via Dev Spaces é executado em um nó VM. Executar um serviço com Espaços de Dev em um nó VM é a configuração padrão.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Erro "não foi possível encontrar um pod de leme pronto" ao lançar o Dev Spaces

Esse erro ocorrerá se o cliente do Helm não puder mais se comunicar com o pod Tiller em execução no cluster.

Para corrigir esse problema, reinicie os nós do agente no seu cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Erro "release\<azds-\>-\<identificador spacename\>-\<\> servicename failed: services '\<servicename\> \<'\>já existe" ou "Pull access negado para servicename, repositório não existe ou pode exigir 'login docker'"

Esses erros podem ocorrer se você misturar comandos `helm upgrade`de `helm delete`comando saqueado direto `azds up` `azds down`(como `helm install`, ou ) com comandos Dev Spaces (como e ) dentro do mesmo espaço de v. Eles ocorrem porque o Dev Spaces tem sua própria instância tiller, que entra em conflito com sua própria instância tiller funcionando no mesmo espaço de vérmédio.

É bom usar comandos Helm e Dev Spaces contra o mesmo cluster AKS, mas cada espaço de nome habilitado para Dev Spaces deve usar um ou outro.

Por exemplo, suponha que você use um comando Helm para executar toda a sua aplicação em um espaço de desenvolvimento pai. Você pode criar espaços de desenvolvimento infantil fora desse pai, usar o Dev Spaces para executar serviços individuais dentro dos espaços de desenvolvimento infantil e testar os serviços juntos. Quando estiver pronto para verificar suas alterações, use um comando Helm para implantar o código atualizado no espaço de dev pai. Não use `azds up` para executar o serviço atualizado no espaço de dev pai, porque ele entrará em conflito com o serviço inicialmente executado usando o Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Arquivo Docker existente não usado para construir um contêiner

O Azure Dev Spaces pode ser configurado para apontar para um _Dockerfile_ específico no projeto. Se aparecer que o Azure Dev Spaces não está usando o _Dockerfile_ que você espera compilar seus contêineres, talvez seja necessário informar explicitamente qual Dockerfile usar ao Azure Dev Spaces. 

Para corrigir esse problema, abra o arquivo _azds.yaml_ que o Azure Dev Spaces gerou em seu projeto. Configurações *de atualização: desenvolver: build: dockerfile* to point to the Dockerfile you want to use. Por exemplo: 

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erro "não autorizado: autenticação necessária" ao tentar usar uma imagem do Docker de um registro privado

Você está usando uma imagem do Docker de um registro privado que requer autenticação.

Para corrigir esse problema, você pode permitir que o Dev Spaces autentique e puxe imagens deste registro privado usando [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Para usar o imagePullSecrets, [crie um segredo kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) no namespace onde você está usando a imagem. Em seguida, forneça o segredo `azds.yaml`como uma imagemPullSecret em .

Abaixo está um exemplo de uma `azds.yaml`imagem especificandoPullSecrets em .

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Configuração de `azds.yaml` imagemPullSecrets in substituirá a `values.yaml`imagemPullSecrets especificada no .

### <a name="error-service-cannot-be-started"></a>Erro "O serviço não pode ser iniciado."

Você pode ver esse erro quando seu código de serviço não pode ser iniciado. Frequentemente, a causa está no código do usuário. Para obter mais informações de diagnóstico, habilite o registro mais detalhado ao iniciar seu serviço.

A partir da linha `--verbose` de comando, use o para permitir um registro mais detalhado. Você também pode especificar `--output`um formato de saída usando . Por exemplo: 

```cmd
azds up --verbose --output json
```

No Visual Studio:

1. Abra **Ferramentas > Opções** e, em **Projetos e Soluções**, escolha **Compilar e executar**.
2. Altere as configurações de **Detalhamento da saída de compilação do projeto no MSBuild** para **Detalhado** ou **Diagnóstico**.

    ![Captura de tela da caixa de diálogo Opções de ferramentas](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Executar novamente um serviço após a recriação do controlador

Você recebe um erro *O serviço não pode ser iniciado* ao tentar executar novamente um serviço após ter removido e, em seguida, recriado o controlador do Azure Dev Spaces associado a esse cluster. Nessa situação, a saída detalhada contém o texto a seguir:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Esse erro ocorre porque a remoção do controlador Dev Spaces não remove serviços previamente instalados por esse controlador. Recriar o controlador e, em seguida, tentar executar os serviços usando o novo controlador falha porque os serviços antigos ainda estão implantados.

Para corrigir esse problema, use o comando `kubectl delete` para remover manualmente os serviços antigos do cluster e, em seguida, execute novamente os Dev Spaces para instalar os novos serviços.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Erro "O serviço não pode ser iniciado." ao usar arquivos Docker de vários estágios

Você recebe um erro *Não é possível iniciar o serviço* durante o uso de um Dockerfile de vários estágios. Nessa situação, a saída detalhada contém o texto a seguir:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Esse erro ocorre porque o Azure Dev Spaces não suporta atualmente compilações de vários estágios. Reescreva seu Dockerfile para evitar builds de vários estágios.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>O tráfego de rede não é encaminhado para o cluster AKS ao conectar sua máquina de desenvolvimento

Ao usar [o Azure Dev Spaces para conectar seu cluster AKS à sua máquina de desenvolvimento,](how-to/connect.md)você pode encontrar um problema no qual o tráfego de rede não é encaminhado entre sua máquina de desenvolvimento e seu cluster AKS.

Ao conectar sua máquina de desenvolvimento ao seu cluster AKS, o Azure Dev Spaces encaminha o tráfego `hosts` de rede entre o cluster AKS e a máquina de desenvolvimento modificando o arquivo da máquina de desenvolvimento. O Azure Dev Spaces `hosts` cria uma entrada no com o endereço do serviço Kubernetes que você está substituindo como um nome de host. Esta entrada é usada com o encaminhamento da porta para tráfego direto de rede entre sua máquina de desenvolvimento e o cluster AKS. Se um serviço em sua máquina de desenvolvimento entra em conflito com a porta do serviço Kubernetes que você está substituindo, o Azure Dev Spaces não poderá encaminhar o tráfego de rede para o serviço Kubernetes. Por exemplo, o serviço *Windows BranchCache* geralmente está vinculado a *0.0.0.0:80*, o que causará um conflito para a porta 80 em todos os IPs locais.

Para corrigir esse problema, você precisa parar quaisquer serviços ou processos que entrem em conflito com a porta do serviço Kubernetes que você está tentando substituir. Você pode usar ferramentas, como *netstat,* para inspecionar quais serviços ou processos em sua máquina de desenvolvimento estão em conflito.

Por exemplo, para parar e desativar o serviço *Windows BranchCache:*
* Corra `services.msc` do prompt de comando.
* Clique com o botão direito do mouse no *BranchCache* e selecione *Propriedades*.
* Clique em *Parar*.
* Opcionalmente, você pode desativá-lo definindo *Tipo de Inicialização* para *Desativado*.
* Clique em *OK*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Problemas comuns usando visual studio e visual studio code com Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Erro "Faltam ferramentas e configurações necessárias"

Este erro pode ocorrer ao iniciar o VS Code: "Ferramentas e configurações do [Azure Dev Spaces] necessárias para compilar e depurar '[nome do projeto]' estão ausentes."
O erro significa que azds.exe não está na variável de ambiente PATH, como visto no VS Code.

Tente iniciar o código VS a partir de um prompt de comando onde a variável de ambiente PATH está definida corretamente.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "As ferramentas necessárias para compilar e depurar 'projectname' estão desatualizadas."

Esse erro será exibido no Visual Studio Code se você tiver uma versão mais recente da extensão do VS Code para o Azure Dev Spaces, mas uma versão anterior da CLI do Azure Dev Spaces.

Tente baixar e instalar a versão mais recente do Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Erro: "Falha ao encontrar extensão de depurador para tipo:coreclr"

Você pode ver esse erro ao executar o depurador visual studio code. Você pode não ter a extensão VS Code para C# instalada em sua máquina de desenvolvimento. A extensão C# inclui suporte de depuração para .NET Core (CoreCLR).

Para corrigir esse problema, instale a [extensão VS Code para C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Erro "O 'coreclr' do tipo de depuração configurado não é suportado"

Você pode ver esse erro ao executar o depurador visual studio code. Você pode não ter a extensão VS Code para Azure Dev Spaces instalada em sua máquina de desenvolvimento.

Para corrigir esse problema, instale a [extensão VS Code para Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro "Valor 'cwd' inválido 'cwd'. O sistema não pode localizar o arquivo especificado." ou "launch: o programa '/src/[caminho para o projeto binário] 'não existe "

Você pode ver esse erro ao executar o depurador visual studio code. Por padrão, a extensão do VS Code usa `src` como o diretório de trabalho do projeto no contêiner. Se você atualizou `Dockerfile` para especificar um diretório de trabalho diferente, poderá visualizar esse erro.

Para corrigir esse problema, atualize o `launch.json` arquivo sob o `.vscode` subdiretório da pasta do projeto. Altere a diretiva `configurations->cwd` para apontar para o mesmo diretório que `WORKDIR` definiu no `Dockerfile` do projeto. Além disso, talvez seja necessário atualizar a diretiva `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro "O programa de tubos 'azds' saiu inesperadamente com o código 126."

Você pode ver esse erro ao executar o depurador visual studio code.

Para corrigir esse problema, feche e reabra o Visual Studio Code. Reinicie o depurador.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "Falha do relógio interno: assista ENOSPC" ao anexar depuração a um aplicativo Node.js

Esse erro ocorre quando o nó que executa o pod com o aplicativo Node.js que você está tentando anexar com um depurador excedeu o valor *fs.inotify.max_user_watches.* Em alguns casos, [o valor padrão de *fs.inotify.max_user_watches* pode ser muito pequeno para lidar com a anexação de um depurador diretamente a um pod](https://github.com/Azure/AKS/issues/772).

Uma solução temporária para este problema é aumentar o valor do *fs.inotify.max_user_watches* em cada nó no cluster e reiniciar esse nó para que as alterações entrem em vigor.

## <a name="other-common-issues"></a>Outros problemas comuns

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>O erro "azds" não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lote

Esse erro pode `azds.exe` acontecer se não estiver instalado ou configurado corretamente.

Para corrigir esse problema:

1. Verifique a localização %ProgramFiles%/Microsoft SDKs\Azure\Azure `azds.exe`Dev Spaces CLI for . Se estiver lá, adicione o local à variável de ambiente PATH.
2. Se `azds.exe` não estiver instalado, execute o seguinte comando:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Erro de autorização "Microsoft.DevSpaces/register/action"

Você precisa de acesso de *Proprietário* ou *Colaborador* em sua assinatura do Azure para gerenciar o Azure Dev Spaces. Se você está tentando gerenciar dev spaces e não tem acesso *proprietário* ou *contribuinte* à assinatura azure associada, você pode ver um erro de autorização. Por exemplo: 

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Para corrigir esse problema, usando uma conta com o *acesso do Proprietário* ou *Contribuinte* à assinatura do Azure, registre manualmente o `Microsoft.DevSpaces` namespace:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Novas cápsulas não estão começando

O inicializador Kubernetes não pode aplicar o PodSpec para novos pods devido a alterações de permissão do RBAC na função *de administrador de cluster* no cluster. O novo pod também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada ao pod não existe mais. Para ver as cápsulas *Pending* que estão em um estado pendente `kubectl get pods` devido ao problema inicializador, use o comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esse problema pode impactar pods em *todos os namespaces* no cluster, incluindo namespaces onde o Azure Dev Spaces não está habilitado.

Para corrigir esse problema, [atualize o CLI do Dev Spaces para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, exclua o *initializer azdsConfiguração* do controlador Azure Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Depois de remover o *initializer azdsConfiguração* do controlador Azure Dev Spaces, use `kubectl delete` para remover quaisquer pods em um estado *pendente.* Depois que todas as cápsulas pendentes forem removidas, reimplante suas cápsulas.

Se as novas cápsulas *Pending* ainda estiverem presas em `kubectl delete` um estado pendente após uma reimplantação, use para remover quaisquer cápsulas em um estado *pendente.* Depois que todos os pods pendentes tiverem sido removidos, exclua o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois que o controlador for reinstalado, reimplante suas cápsulas.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões RBAC incorretas para chamar controlador e APIs do Dev Spaces

O usuário que acessar o controlador Azure Dev Spaces deve ter acesso para ler o *kubeconfig* do administrador no cluster AKS. Por exemplo, essa permissão está disponível na função de administrador de cluster de [serviço azure Kubernetes incorporada](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O usuário que acessar o controlador Azure Dev Spaces também deve ter a função *Contribuinte* ou *Proprietário* RBAC para o controlador. Mais detalhes sobre a atualização das permissões de um usuário para um cluster AKS estão disponíveis [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função RBAC do usuário para o controlador:

1. Entre no Portal do Azure em https://portal.azure.com.
1. Navegue até o Grupo de recursos que contém o controlador, que geralmente é o mesmo do seu cluster AKS.
1. Habilite a caixa de seleção *Mostrar tipos ocultos.*
1. Clique no controlador.
1. Abra o painel *de Controle de Acesso (IAM).*
1. Clique na guia *Atribuições de* função.
1. Clique *em Adicionar* e adicionar *atribuição de função .*
    * Para *Função,* selecione *Contribuinte* ou *Proprietário.*
    * Para *Atribuir acesso a*, selecione *Usuário, grupo ou entidade de serviço do Azure AD*.
    * Para *Selecionar,* procure o usuário que deseja dar permissões.
1. Clique em *Salvar*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>A resolução de nomes DNS falha para uma URL pública associada a um serviço do Azure Dev Spaces

Você pode configurar um ponto de extremidade de URL público para seu serviço, especificando a opção `--enable-ingress` para o comando `azds prep` ou selecionando a caixa de seleção `Publicly Accessible` no Visual Studio. O nome DNS público é registrado automaticamente quando você executa o serviço no Dev Spaces. Se esse nome DNS não estiver registrado, você verá um erro *A página não pode ser exibida* ou *O site não pode ser alcançado* no navegador da Web durante a conexão com a URL pública.

Para corrigir esse problema:

* Verifique o status de todas as URLs associadas aos seus serviços de Espaços de Dev:

  ```console
  azds list-uris
  ```

* Se uma URL estiver no estado *pendente,* o Dev Spaces ainda aguarda a conclusão do registro do DNS. Às vezes, leva alguns minutos para que esse registro seja concluído. O Azure Dev Spaces também abre um túnel de localhost para cada serviço, que pode ser usado enquanto aguarda o registro DNS.
* Se uma URL permanecer no estado *Pendente* por mais de 5 minutos, isso pode indicar um problema com o pod DNS externo que cria o ponto de extremidade público ou o pod controlador de entrada nginx que adquire o ponto de extremidade público. Use os seguintes comandos para excluir esses pods e permitir que a AKS os recrie automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro "Erro de conexão a montante ou desconectar/redefinir antes de cabeçalhos"

É possível ver esse erro ao tentar acessar o serviço. Por exemplo, ao acessa a URL do serviço em um navegador. Este erro significa que a porta do contêiner não está disponível. Isso pode pelas seguintes razões:

* O contêiner ainda está em processo de ser compilado e implantado. Esse problema poderá ocorrer se você executar `azds up` ou iniciar o depurador e, em seguida, tentar acessar o contêiner antes dele ser implantado com êxito.
* A configuração de porta não é uniforme em todo o _Dockerfile_, Helm Chart e em qualquer código de servidor que abra uma porta.

Para corrigir esse problema:

1. Se o contêiner estiver sendo compilado/implantado, você poderá aguardar de 2 a 3 segundos e tentar acessar o serviço novamente. 
1. Verifique a configuração da porta nos seguintes ativos:
    * **[Gráfico de leme](https://docs.helm.sh):** Especificado pelo `service.port` andaime `deployment.containerPort` e `azds prep` em valores.yaml andaime por comando.
    * Quaisquer portas sendo abertas no código do aplicativo, por exemplo, no Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>O nome tipo ou namespace "MyLibrary" não pôde ser encontrado

Um projeto de biblioteca que você está usando não pode ser encontrado. Com o Dev Spaces, o contexto de construção está no nível de projeto/serviço por padrão.  

Para corrigir esse problema:

1. Modifique `azds.yaml` o arquivo para definir o contexto de compilação para o nível da solução.
2. Modifique `Dockerfile` `Dockerfile.develop` os arquivos e os arquivos `.csproj`para consultar os arquivos do projeto, por exemplo, corretamente em relação ao novo contexto de compilação.
3. Adicione `.dockerignore` a no mesmo diretório `.sln` que o arquivo.
4. Atualize `.dockerignore` o com entradas adicionais conforme necessário.

Você pode encontrar um exemplo [aqui.](https://github.com/sgreenmsft/buildcontextsample)

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Autoscaling de pod horizontal não funciona em um espaço de dev

Quando você executa um serviço em um espaço de desenvolvimento, o pod desse serviço é [injetado com recipientes adicionais para instrumentação](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) e todos os recipientes em um pod precisam ter limites de recursos e solicitações definidos para O Pod Horizontal Autoscaling.

Para corrigir esse problema, aplique uma solicitação de recurso e limite aos recipientes de Espaço de Desenvolvimento injetados. Solicitações e limites de recursos podem ser aplicados para o contêiner `azds.io/proxy-resources` injetado (devspaces-proxy) adicionando a anotação à especificação do pod. O valor deve ser definido como um objeto JSON representando a seção de recursos da especificação de contêiner para o proxy.

Abaixo está um exemplo de uma anotação de recursos proxy que deve ser aplicada à sua especificação de pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Habilite os espaços de dev do Azure em um namespace existente com pods em execução

Você pode ter um cluster AKS existente e um namespace com pods em execução onde deseja ativar o Azure Dev Spaces.

Para habilitar o Azure Dev Spaces em um namespace `use-dev-spaces` existente `kubectl` em um cluster AKS, execute e use para reiniciar todos os pods nesse namespace.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Depois que seus pods forem reiniciados, você pode começar a usar seu namespace existente com o Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Habilite os espaços de dev do Azure no cluster AKS com tráfego de saída restrito para nós de cluster

Para habilitar o Azure Dev Spaces em um cluster AKS para o qual o tráfego de saída dos nós de cluster é restrito, você terá que permitir as seguintes FQDNs:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Para puxar imagens do Linux Alpine e outras imagens do Azure Dev Spaces |
| gcr.io | HTTP:443 | Para puxar imagens de leme/leme|
| storage.googleapis.com | HTTP:443 | Para puxar imagens de leme/leme|
| azds-<guid>. <location>Azds.io | HTTPS:443 | Para se comunicar com os serviços de back-end do Azure Dev Spaces para o seu controlador. O FQDN exato pode ser encontrado no "dataplaneFqdn" em %USERPROFILE%\.azds\settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Erro "Não foi \<possível\> encontrar \<o\>cluster no Subscription SubscriptionId"

Você pode ver esse erro se o seu arquivo kubeconfig estiver mirando um cluster ou assinatura diferente do que você está tentando usar com as ferramentas do lado do cliente do Azure Dev Spaces. A ferramenta do lado do cliente Do Zure Dev Spaces replica o comportamento do *kubectl*, que usa [um ou mais arquivos kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) para selecionar e se comunicar com o cluster.

Para corrigir esse problema:

* Use `az aks use-dev-spaces -g <resource group name> -n <cluster name>` para atualizar o contexto atual. Este comando também permite espaços de dev do Azure no cluster AKS se ainda não estiver habilitado. Alternativamente, você `kubectl config use-context <cluster name>` pode usar para atualizar o contexto atual.
* Use `az account show` para mostrar a assinatura atual do Azure que você está mirando e verifique se isso está correto. Você pode alterar a assinatura `az account set`que você está mirando usando .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Erro ao usar espaços de dev após rodar certificados AKS

Depois [de rodar os certificados em seu cluster AKS,](../aks/certificate-rotation.md)certas operações, como `azds space list` e `azds up` falharão. Você também precisa atualizar os certificados no controlador Azure Dev Spaces depois de rodar os certificados em seu cluster.

Para corrigir esse problema, certifique-se de que `az aks get-credentials` seu *kubeconfig* tenha os certificados atualizados usando e execute o `azds controller refresh-credentials` comando. Por exemplo: 

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
