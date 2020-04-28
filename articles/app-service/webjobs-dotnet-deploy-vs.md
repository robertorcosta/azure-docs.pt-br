---
title: Desenvolver e implantar WebJobs usando VS
description: Saiba como desenvolver o Azure WebJobs no Visual Studio e implantá-los no Azure App Service, incluindo a criação de uma tarefa programada.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d73c858bdbfee7c5b7c7e31b62b1f601b7b6838a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866447"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Desenvolver e implantar o WebJobs usando o Visual Studio – Serviço de Aplicativo do Azure

Este artigo explica como usar o Visual Studio para implantar um projeto de aplicativo de console em um aplicativo web no [App Service](overview.md) como [um WebJob do Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implantar o WebJobs usando o [portal do Azure](https://portal.azure.com), consulte [Executar tarefas em segundo plano com o WebJobs](webjobs-create.md).

Você pode publicar vários WebJobs em um único aplicativo web. Certifique-se de que cada WebJob em um aplicativo web tenha um nome único.

A versão 3.x do [Azure WebJobs SDK](webjobs-sdk-how-to.md) permite que você desenvolva WebJobs que sejam executados como aplicativos .NET Core ou .NET Framework, enquanto a versão 2.x suporta apenas o .NET Framework. A maneira como você implanta um projeto WebJobs é diferente para projetos .NET Core versus .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs como aplicativos de console .NET Core

Ao usar a versão 3.x do WebJobs, você pode criar e publicar WebJobs como aplicativos de console .NET Core. Para obter instruções passo-a-passo para criar e publicar um aplicativo de console .NET Core no Azure como WebJob, consulte [Comece com o Azure WebJobs SDK para processamento de fundo orientado a eventos](webjobs-sdk-get-started.md).

> [!NOTE]
> O .NET Core WebJobs não pode ser vinculado a projetos web. Se você precisar implantar seu WebJob com um aplicativo web, você deve [criar seu WebJob como um aplicativo de console .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implantar no Serviço de Aplicativo do Azure

Publicar um WebJob .NET Core para app service do Visual Studio usa a mesma ferramenta que publicar um aplicativo ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipos de WebJob

Por padrão, um WebJob publicado a partir de um projeto de console .NET Core é executado apenas quando acionado ou sob demanda. Você também pode atualizar o projeto para [executar em um cronograma](#scheduled-execution) ou executar continuamente.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Execução programada

Quando você publica um aplicativo de console .NET Core no Azure, um novo arquivo *settings.job* é adicionado ao projeto. Use este arquivo para definir um cronograma de execução para o seu WebJob. Para obter mais informações, consulte [Agendar um WebJob acionado](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Execução contínua

Você pode usar o Visual Studio para alterar o WebJob para ser executado continuamente quando Always On estiver ativado no Azure.

1. Se você ainda não fez isso, [publique o projeto para o Azure](#deploy-to-azure-app-service).

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. Na guia **Publicar,** escolha **Configurações**. 

1. Na caixa de diálogo **Configurações** de perfil, escolha **'Continuar** para **Tipo de WebJob'** e escolha **Salvar**.

    ![Publicar diálogo Configurações para um WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **Publicar** para republicar o WebJob com as configurações atualizadas.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs como aplicativos de console .NET Framework  

Quando o Visual Studio implanta um projeto de aplicativo de console .NET framework habilitado para WebJobs, ele copia arquivos em tempo de execução para a pasta apropriada no aplicativo web (*App_Data/empregos/contínuos* para WebJobs contínuos e *App_Data/empregos/acionados* para WebJobs agendados ou sob demanda).

Um projeto habilitado para Trabalhos Web tem os seguintes itens adicionados:

* O pacote NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Um arquivo [webjob-publish-settings.json](#publishsettings) que contém configurações de implantação e agendador. 

![Diagrama que mostra o que é adicionado a um Aplicativo de Console para habilitar implantação como um Trabalho Web](./media/webjobs-dotnet-deploy-vs/convert.png)

É possível adicionar esses itens a um projeto do Aplicativo de Console existente ou usar um modelo para criar um novo projeto do Aplicativo de Console habilitado para Trabalhos Web. 

É possível implantar um projeto como um Trabalho Web propriamente dito ou vinculá-lo a um projeto Web de forma que ele seja implantado automaticamente sempre que você implanta o projeto Web. Para vincular projetos, o Visual Studio inclui o nome do projeto habilitado para Trabalhos Web em um arquivo [webjobs-list.json](#webjobslist) no projeto Web.

![Diagrama que mostra o projeto de Trabalho Web sendo vinculado ao projeto Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Se estiver usando o Visual Studio 2015, instale o [SDK do Azure para .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se estiver usando o Visual Studio 2017, instale a [carga de trabalho de desenvolvimento do Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a> Habilitar a implantação do WebJobs em um projeto existente do Aplicativo de Console

Você tem duas opções:

* [Habilitar implantação automática com um projeto Web](#convertlink).

  Configure um projeto do Aplicativo de Console existente de forma que ele seja implantado automaticamente como um Trabalho Web quando você implanta o projeto Web. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.

* [Habilitar implantação sem um projeto Web](#convertnolink).

  Configure um projeto do Aplicativo de Console existente a ser implantado como um Trabalho Web propriamente dito, sem link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Habilitar a implantação de Trabalhos Web automática com um projeto Web

1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** > **Projeto Existente como Trabalho Web do Azure**.
   
    ![Projeto Existente como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Na lista suspensa **Nome do projeto** , selecione o projeto do Aplicativo de Console a ser adicionado como um Trabalho Web.
   
    ![Selecionando projeto na caixa de diálogo Adicionar Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Habilitar a implantação de Trabalhos Web sem um projeto Web
1. Clique com o botão direito do mouse no projeto do Aplicativo de Console no **Gerenciador de Soluções** e, depois, clique em **Publicar como Azure WebJob...**. 
   
    ![Publicar como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida, com o projeto selecionado na caixa **Nome do projeto** .
2. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**.
   
   O assistente **Publicar Web** é exibido.  Se você não desejar publicar imediatamente, feche o assistente. As configurações inseridas por você são salvas para quando quiser [implantar o projeto](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Criar um novo projeto habilitado para Trabalhos Web
Para criar um projeto habilitado para Trabalhos Web, é possível usar o modelo de projeto do Aplicativo de Console e habilitar a implantação de Trabalhos Web conforme explicado na [seção anterior](#convert). Também é possível usar o modelo do novo projeto de Trabalhos Web:

* [Use o modelo de novo projeto do WebJobs para um WebJob independente](#createnolink)
  
    Crie um projeto e o configure para ser implantado propriamente dito como um Trabalho Web, sem link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.
* [Use o modelo de novo projeto do WebJobs para um WebJob vinculado a um projeto web](#createlink)
  
    Crie um projeto que seja configurado para ser implantado automaticamente como um Trabalho Web quando um projeto Web na mesma solução for implantado. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.

> [!NOTE]
> O modelo new-project do WebJobs instala automaticamente pacotes NuGet e inclui o código em *Program.cs* para o [SDK do WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se você não desejar usar o SDK do WebJobs, remova ou altere a instrução `host.RunAndBlock` em *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente
1. Clique **em Arquivo** > **novo projeto**e, em seguida, na caixa de diálogo Novo **Projeto** clique em **Cloud** > **Azure WebJob (.NET Framework)**.
   
    ![Caixa de diálogo Novo Projeto mostrando o modelo de Trabalho Web](./media/webjobs-dotnet-deploy-vs/np.png)
2. Siga as direções mostradas anteriormente para [tornar o projeto do Aplicativo de Console um projeto de Trabalhos Web independente](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web
1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** > **Novo Projeto de Trabalho Web do Azure**.
   
    ![Entrada de menu de Novo Projeto de Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>A caixa de diálogo Adicionar WebJob do Azure
A caixa de diálogo **Adicionar Azure WebJob** permite inserir o nome do WebJob e executar a configuração de modo do WebJob. 

![Caixa de diálogo Adicionar Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Os campos dessa caixa de diálogo correspondem aos campos da caixa de diálogo **Adicionar WebJob** do portal do Azure. Para obter mais informações, consulte [Executar tarefas em segundo plano com o WebJobs](webjobs-create.md).

> [!NOTE]
> * Para obter informações sobre a implantação de linha de comando, consulte [Habilitando a entrega de linha de comando ou contínua de Trabalhos Web do Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se você implantar um WebJob e, em seguida, decidir que deseja alterar o tipo de WebJob e implantá-lo novamente, precisará excluir o arquivo *webjobs-publish-settings.json*. Isso fará com que o Visual Studio exiba novamente as opções de publicação para que você possa alterar o tipo de Trabalho Web.
> * Se você implantar um Trabalho Web e depois alterar o modo de execução de contínuo para não contínuo ou vice-versa, o Visual Studio criará um novo Trabalho Web no Azure quando você o reimplantar. Se você alterar outras configurações de agendamento, mas deixar o modo de execução igual ou alternar Agendado e Sob Demanda, o Visual Studio atualizará o trabalho existente, em vez de criar um novo.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
Quando você configura um Aplicativo de Console para implantação de Trabalhos Web, o Visual Studio instala o pacote NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e armazena informações de agendamento em um arquivo *webjob-publish-settings.json* na pasta *Propriedades* do projeto dos Trabalhos Web. Aqui está um exemplo desse arquivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) é armazenado e pode ser visto lá.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
Quando você vincula um projeto habilitado para Trabalhos Web a um projeto Web, o Visual Studio armazena o nome do projeto de Trabalhos Web em um arquivo *webjobs-list.json* na pasta *Propriedades* do projeto Web. A lista pode conter vários projetos do WebJobs, conforme mostrado no seguinte exemplo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) é armazenado e pode ser visto lá.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Implantar um projeto de Trabalhos Web
Um projeto de Trabalhos Web vinculado a um projeto Web é implantado automaticamente com o projeto Web. Para obter informações sobre a implantação do projeto web, consulte **guias** > de como**fazer implantar** o aplicativo na navegação à esquerda.

Para implantar um projeto do WebJobs sozinho, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Publicar como Azure WebJob...**. 

![Publicar como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um Trabalho Web independente, o mesmo assistente **Publicar Web** usado em projetos Web é exibido, mas com menos configurações disponíveis para serem alteradas.

## <a name="scheduling-a-triggered-webjob"></a>Agendando um WebJob disparado

O WebJobs usa um arquivo *settings.job* para determinar quando um WebJob é executado. Use este arquivo para definir um cronograma de execução para o seu WebJob. O exemplo a seguir é executado a cada hora das 9:00 às 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Este arquivo deve estar localizado na raiz da pasta WebJobs, juntamente com `wwwroot\app_data\jobs\triggered\{job name}` `wwwroot\app_data\jobs\continuous\{job name}`o script do WebJob, como ou . Ao implantar um WebJob por meio do Visual Studio, marque as propriedades do arquivo `settings.job` como **Copiar se mais recente**. 

Quando você [cria um WebJob a partir do portal Azure,](webjobs-create.md)o arquivo settings.job é criado para você.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Expressões CRON

O WebJobs usa as mesmas expressões CRON para agendamento que o gatilho do temporizador em Funções Azure. Para saber mais sobre o suporte do CRON, consulte o artigo de referência do gatilho do [temporizador](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>configurações.referência de trabalho

As seguintes configurações são suportadas pelo WebJobs:

| **Configuração** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Todos | Permite que o trabalho seja executado no lugar sem ser primeiro copiado para uma pasta temporária. Para saber mais, consulte [o diretório de trabalho do WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Contínuo | Só execute o WebJobs em uma única instância quando for dimensionado. Para saber mais, consulte [Definir um trabalho contínuo como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Disparado | Execute o WebJob em um cronograma baseado em CRON. Para saber mais, consulte o artigo de referência do gatilho do [temporizador](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Todos | Permite o controle do comportamento de desligamento. Para saber mais, consulte [O desligamento de Graceful](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK do WebJobs](webjobs-sdk-how-to.md)
