---
title: Tecnologias de implantação em funções do Azure
description: Aprenda as diferentes maneiras que você pode implantar código em Funções Do Azure.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277122"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação em funções do Azure

Você pode usar algumas tecnologias diferentes para implantar o código do projeto Funções do Azure no Azure. Este artigo fornece uma lista exaustiva dessas tecnologias, descreve quais tecnologias estão disponíveis para quais sabores de Funções, explica o que acontece quando você usa cada método e fornece recomendações para o melhor método para usar em vários cenários . As várias ferramentas que suportam a implantação de funções do Azure estão sintonizadas com a tecnologia certa com base em seu contexto. Em geral, a implantação zip é a tecnologia de implantação recomendada para funções do Azure.

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implantação

O Azure Functions suporta desenvolvimento local multiplataforma e hospedagem no Windows e Linux. Atualmente, três planos de hospedagem estão disponíveis:

+ [Consumo](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicado (Serviço de Aplicativo)](functions-scale.md#app-service-plan)

Cada plano tem comportamentos diferentes. Nem todas as tecnologias de implantação estão disponíveis para cada sabor das funções do Azure. O gráfico a seguir mostra quais tecnologias de implantação são suportadas para cada combinação de sistema operacional e plano de hospedagem:

| Tecnologia de implantação | Consumo de Windows | Windows Premium | Windows Dedicado  | Consumo de Linux | Linux Premium | Linux Dedicado |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL do pacote externo<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip implantar |✔|✔|✔|✔|✔|✔|
| Contêiner do Docker | | | | |✔|✔|
| Implantação da Web |✔|✔|✔| | | |
| Controle do código-fonte |✔|✔|✔| |✔|✔|
| Git<sup>Local 1</sup> |✔|✔|✔| |✔|✔|
| Sincronização de<sup>nuvem 1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edição de portal |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Tecnologia de implantação que requer [sincronização manual do gatilho](#trigger-syncing).  
<sup>2</sup> A edição do portal é habilitada apenas para gatilhos HTTP e Timer para funções no Linux usando planos Premium e Dedicados.

## <a name="key-concepts"></a>Principais conceitos

Alguns conceitos-chave são fundamentais para entender como as implantações funcionam nas funções do Azure.

### <a name="trigger-syncing"></a>Sincronização do gatilho

Quando você altera qualquer um de seus gatilhos, a infra-estrutura Functions deve estar ciente das alterações. A sincronização acontece automaticamente para muitas tecnologias de implantação. No entanto, em alguns casos, você deve sincronizar manualmente seus gatilhos. Quando você implanta suas atualizações fazendo referência a uma URL de pacote externo, Git local, sincronia na nuvem ou FTP, você deve sincronizar manualmente seus gatilhos. Você pode sincronizar gatilhos de três maneiras:

* Reinicie seu aplicativo de função no portal Azure
* Envie uma solicitação `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` HTTP POST para usar a [tecla mestre](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Envie uma solicitação `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`HTTP POST para . Substitua os espaços reservados pelo seu ID de assinatura, nome do grupo de recursos e o nome do seu aplicativo de função.

### <a name="remote-build"></a>Construção remota

As funções do Azure podem executar automaticamente builds no código que recebe após implantações de zip. Essas compilações se comportam ligeiramente diferente, dependendo se o seu aplicativo está sendo executado no Windows ou Linux. As compilações remotas não são executadas quando um aplicativo foi definido anteriormente para ser executado no modo [Executar a partir do pacote.](run-functions-from-deployment-package.md) Para aprender a usar a compilação remota, navegue para [zip deploy](#zip-deploy).

> [!NOTE]
> Se você está tendo problemas com a compilação remota, pode ser porque seu aplicativo foi criado antes do recurso ser disponibilizado (1 de agosto de 2019). Tente criar um novo aplicativo `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` de função ou correr para atualizar seu aplicativo de função. Este comando pode ter duas tentativas de sucesso.

#### <a name="remote-build-on-windows"></a>Compilação remota no Windows

Todos os aplicativos de função em execução no Windows têm um pequeno aplicativo de gerenciamento, o site SCM (ou [Kudu).](https://github.com/projectkudu/kudu) Este site lida com grande parte da implantação e da lógica de construção para funções do Azure.

Quando um aplicativo é implantado no Windows, `dotnet restore` comandos específicos `npm install` do idioma, como (C#) ou (JavaScript) são executados.

#### <a name="remote-build-on-linux"></a>Compilação remota no Linux

Para habilitar a compilação remota no Linux, as [seguintes configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) devem ser definidas:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Por padrão, tanto as [ferramentas principais de funções do Azure](functions-run-local.md) quanto a [extensão de funções do Azure para o Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) executam compilações remotas ao implantar no Linux. Por causa disso, ambas as ferramentas criam automaticamente essas configurações para você no Azure. 

Quando os aplicativos são construídos remotamente no Linux, eles [são executados a partir do pacote de implantação](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Plano de consumo

Os aplicativos de função Linux em execução no plano Consumo não têm um site SCM/Kudu, o que limita as opções de implantação. No entanto, os aplicativos de função no Linux em execução no plano De consumo suportam compilações remotas.

##### <a name="dedicated-and-premium-plans"></a>Planos dedicados e Premium

Os aplicativos de função em execução no Linux no [plano Dedicated (App Service)](functions-scale.md#app-service-plan) e o [plano Premium](functions-scale.md#premium-plan) também têm um site limitado de SCM/Kudu.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implantação

Os seguintes métodos de implantação estão disponíveis em Funções Azure.

### <a name="external-package-url"></a>URL do pacote externo

Você pode usar uma URL de pacote externo para referenciar um arquivo de pacote remoto (.zip) que contém seu aplicativo de função. O arquivo é baixado da URL fornecida, e o aplicativo é executado no modo [Run From Package.](run-functions-from-deployment-package.md)

>__Como usá-lo:__ Adicione `WEBSITE_RUN_FROM_PACKAGE` às configurações do aplicativo. O valor desta configuração deve ser uma URL (a localização do arquivo de pacote específico que você deseja executar). Você pode adicionar configurações [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou usando [o Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se você usar o armazenamento Azure Blob, use um contêiner privado com uma [assinatura de acesso compartilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para dar às Funções acesso ao pacote. Sempre que o aplicativo é reiniciado, ele busca uma cópia do conteúdo. Sua referência deve ser válida durante a vida útil da aplicação.

>__Quando usá-lo:__ A URL do pacote externo é o único método de implantação suportado para funções do Azure em execução no Linux no plano De consumo, se o usuário não quiser que uma [compilação remota](#remote-build) ocorra. Quando você atualiza o arquivo de pacote que um aplicativo de função faz referência, você deve [sincronizar manualmente gatilhos](#trigger-syncing) para dizer ao Azure que seu aplicativo foi alterado.

### <a name="zip-deploy"></a>Zip implantar

Use zip deploy para empurrar um arquivo .zip que contém seu aplicativo de função para o Azure. Opcionalmente, você pode definir seu aplicativo para começar a [ser executado a partir do pacote](run-functions-from-deployment-package.md)ou especificar que uma [compilação remota](#remote-build) ocorre.

>__Como usá-lo:__ Implante usando sua ferramenta de cliente favorita: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio,](functions-develop-vs.md#publish-to-azure)ou da linha de comando usando as [Ferramentas Principais de Funções do Azure](functions-run-local.md#project-file-deployment). Por padrão, essas ferramentas usam a implantação zip e [são executadas a partir do pacote](run-functions-from-deployment-package.md). As Ferramentas Principais e a extensão Visual Studio Code permitem a [compilação remota](#remote-build) ao implantar no Linux. Para implantar manualmente um arquivo .zip no seu aplicativo de função, siga as instruções em [Implantar a partir de um arquivo .zip ou URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Quando você implanta usando zip deploy, você pode definir seu aplicativo para [executar a partir do pacote](run-functions-from-deployment-package.md). Para executar a partir `WEBSITE_RUN_FROM_PACKAGE` do pacote, defina o valor de configuração do aplicativo para `1`. Recomendamos a implantação do zip. Ele produz tempos de carregamento mais rápidos para seus aplicativos, e é o padrão para O VS Code, Visual Studio e o Azure CLI. 

>__Quando usá-lo:__ Zip deploy é a tecnologia de implantação recomendada para funções do Azure.

### <a name="docker-container"></a>Contêiner do Docker

Você pode implantar uma imagem de contêiner Linux que contém seu aplicativo de função.

>__Como usá-lo:__ Crie um aplicativo de função Linux no plano Premium ou Dedicated e especifique qual imagem de contêiner executar. É possível fazer isso de duas formas:
>
>* Crie um aplicativo de função Linux em um plano de serviço de aplicativo do Azure no portal Azure. Para **Publicar,** selecione **'Imagem de Docker'** e configure o recipiente. Digite o local onde a imagem está hospedada.
>* Crie um aplicativo de função Linux em um plano de Serviço de Aplicativo usando o Azure CLI. Para aprender como, consulte [Criar uma função no Linux usando uma imagem personalizada](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Para implantar em um aplicativo existente usando um contêiner personalizado, em [Azure Functions Core Tools,](functions-run-local.md)use o [`func deploy`](functions-run-local.md#publish) comando.

>__Quando usá-lo:__ Use a opção de contêiner Docker quando precisar de mais controle sobre o ambiente Linux onde seu aplicativo de função é executado. Este mecanismo de implantação está disponível apenas para funções em execução no Linux.

### <a name="web-deploy-msdeploy"></a>Implantação da Web (MSDeploy)

Web Implantar pacotes e implanta seus aplicativos Windows em qualquer servidor IIS, incluindo seus aplicativos de função em execução no Windows no Azure.

>__Como usá-lo:__ Use [ferramentas do Visual Studio para funções do Azure](functions-create-your-first-function-visual-studio.md). Limpe a caixa de seleção **do arquivo de pacote (recomendado).**
>
>Você também pode baixar o Web `MSDeploy.exe` Deploy [3.6](https://www.iis.net/downloads/microsoft/web-deploy) e ligar diretamente.

>__Quando usá-lo:__ O Web Deploy é suportado e não tem problemas, mas o mecanismo preferido é [zip deploy com Run From Package ativado](#zip-deploy). Para saber mais, consulte o guia de desenvolvimento do [Visual Studio.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Controle do código-fonte

Use o controle de origem para conectar seu aplicativo de função a um repositório do Git. Uma atualização para código nesse repositório aciona a implantação. Para obter mais informações, consulte o [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Como usá-lo:__ Use o Centro de Implantação na área De Funções do portal para configurar a publicação a partir do controle de origem. Para saber mais, confira [Implantação contínua do Azure Functions](functions-continuous-deployment.md).

>__Quando usá-lo:__ O uso do controle de origem é a melhor prática para equipes que colaboram em seus aplicativos de função. O controle de origem é uma boa opção de implantação que permite gasodutos de implantação mais sofisticados.

### <a name="local-git"></a>Git local

Você pode usar o Git local para empurrar o código de sua máquina local para funções do Azure usando o Git.

>__Como usá-lo:__ Siga as instruções na [implantação do Git local para o Serviço de Aplicativos Do Azure](../app-service/deploy-local-git.md).

>__Quando usá-lo:__ Em geral, recomendamos que você use um método de implantação diferente. Quando você publica no Git local, você deve [sincronizar manualmente os gatilhos](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronização de nuvem

Use sincronização em nuvem para sincronizar seu conteúdo do Dropbox e OneDrive para funções do Azure.

>__Como usá-lo:__ Siga as instruções em [Conteúdo sincronizar de uma pasta na nuvem](../app-service/deploy-content-sync.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando você publica usando sincronização na nuvem, você deve [sincronizar manualmente os gatilhos](#trigger-syncing).

### <a name="ftp"></a>FTP

Você pode usar FTP para transferir diretamente arquivos para funções do Azure.

>__Como usá-lo:__ Siga as instruções em [Implantar conteúdo usando FTP/s](../app-service/deploy-ftp.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando você publica usando FTP, você deve [sincronizar manualmente os gatilhos](#trigger-syncing).

### <a name="portal-editing"></a>Edição de portal

No editor baseado em portal, você pode editar diretamente os arquivos que estão em seu aplicativo de função (essencialmente implantando cada vez que você salvar suas alterações).

>__Como usá-lo:__ Para poder editar suas funções no portal Azure, você deve ter [criado suas funções no portal](functions-create-first-azure-function.md). Para preservar uma única fonte de verdade, o uso de qualquer outro método de implantação torna sua função somente leitura e impede a edição contínua do portal. Para retornar a um estado no qual você pode editar seus arquivos no portal Azure, você pode ativar manualmente o modo de edição de `Read/Write` volta e remover quaisquer configurações de aplicativo relacionadas à implantação (como `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando usá-lo:__ O portal é uma boa maneira de começar com funções do Azure. Para um trabalho de desenvolvimento mais intenso, recomendamos que você use uma das seguintes ferramentas do cliente:
>
>* [Código visual do estúdio](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (linha de comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela a seguir mostra os sistemas operacionais e idiomas que suportam a edição do portal:

| | Consumo de Windows | Windows Premium | Windows Dedicado | Consumo de Linux | Linux Premium | Linux Dedicado |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| Script do C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (versão prévia) | | | | | | |
| PowerShell (versão prévia) |✔|✔|✔| | | |
| Script de série (Node.js) | | | | | | |

<sup>*</sup>A edição do portal é habilitada apenas para gatilhos HTTP e Timer para Funções no Linux usando planos Premium e Dedicados.

## <a name="deployment-slots"></a>Slots de implantação

Quando você implanta seu aplicativo de função no Azure, você pode implantar em um slot de implantação separado em vez de diretamente para a produção. Para obter mais informações sobre os slots de implantação, consulte a documentação [de slots de implantação de funções do Azure](../app-service/deploy-staging-slots.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

Leia esses artigos para saber mais sobre como implantar seus aplicativos de função: 

+ [Implantação contínua para Azure Functions](functions-continuous-deployment.md)
+ [Entrega contínua usando DevOps Azure](functions-how-to-azure-devops.md)
+ [Implantações zip para funções do Azure](deployment-zip-push.md)
+ [Executar o Azure Functions de um arquivo de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implantação de recursos para seu aplicativo de funções do Azure Functions](functions-infrastructure-as-code.md)
