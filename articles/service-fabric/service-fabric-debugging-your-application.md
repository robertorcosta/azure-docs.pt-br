---
title: Depurar sua aplicação no Visual Studio
description: Melhore a confiabilidade e o desempenho dos seus serviços desenvolvendo e depurando-os no Visual Studio em um cluster de desenvolvimento local.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624133"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar seu aplicativo do Service Fabric usando o Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Depurar um aplicativo local do Service Fabric
Você pode economizar tempo e dinheiro implantando e depurando seu aplicativo do Service Fabric do Azure no cluster de desenvolvimento de um computador local. O Visual Studio 2019 ou 2015 pode implantar o aplicativo no cluster local e conectar automaticamente o depurador a todas as instâncias do seu aplicativo. O Visual Studio deve ser executado como administrador para conectar o depurador.

1. Inicie um cluster de desenvolvimento local seguindo as etapas em [Configurando o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started.md).
2. Pressione **F5** ou clique em **Depurar** > **Iniciar Depuração**.
   
    ![Iniciar depuração de um aplicativo][startdebugging]
3. Defina os pontos de interrupção em seu código e explore o aplicativo clicando nos comandos do menu **Depurar** .
   
   > [!NOTE]
   > O Visual Studio se conecta a todas as instâncias do seu aplicativo. Ao percorrer o código, os pontos de interrupção podem ser atingidos por vários processos, resultando em sessões simultâneas. Tente desabilitar os pontos de interrupção depois que eles forem atingidos, tornando o ponto de interrupção condicional na ID do thread, ou usando eventos de diagnóstico.
   > 
   > 
4. A janela **Eventos de Diagnóstico** abrirá automaticamente para exibir eventos de diagnóstico em tempo real.
   
    ![Exibir eventos de diagnóstico em tempo real][diagnosticevents]
5. Você também pode abrir a janela **Eventos de Diagnóstico** no Gerenciador de Nuvem.  Em **Service Fabric**, clique com o botão direito do mouse em qualquer nó e escolha **Exibir Rastreamentos de Streaming**.
   
    ![Abrir a janela de eventos de diagnóstico][viewdiagnosticevents]
   
    Se você quiser filtrar seus rastreamentos para um serviço ou aplicativo específico, habilite rastreamentos nesse serviço ou aplicativo específico.
6. Os eventos de diagnóstico podem ser vistos no arquivo **ServiceEventSource.cs** gerado automaticamente e são chamados do código do aplicativo.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A janela **Eventos de Diagnóstico** permite filtragem, pausa e eventos de inspeção em tempo real.  O filtro é uma pesquisa simples de cadeia de caracteres da mensagem do evento, incluindo seu conteúdo.
   
    ![Filtrar, pausar e retomar ou inspecionar eventos em tempo real][diagnosticeventsactions]
8. A depuração de serviços é semelhante à depuração de qualquer outro aplicativo. Você normalmente definirá Breakpoints através do Visual Studio para facilitar a depuração. Embora as Reliable Collections sejam replicadas em vários nós, elas ainda implementam IEnumerable. Essa implementação significa que você pode usar a exibição de resultados no Visual Studio enquanto você depura para ver o que você guardou dentro. Para isso, defina um ponto de ruptura em qualquer lugar do seu código.
   
    ![Iniciar depuração de um aplicativo][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Executando um script como parte da depuração
Em certos cenários, você pode precisar executar um script como parte de iniciar uma sessão de depuração (por exemplo, quando não estiver usando Serviços Padrão).

No Visual Studio, você pode adicionar um arquivo chamado **Start-Service.ps1** na pasta **Scripts** do projeto Service Fabric Application (.sfproj). Este script será invocado após a criação do aplicativo no cluster local.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar um aplicativo remoto do Service Fabric
Se os aplicativos do Service Fabric estiverem sendo executados em um cluster Service Fabric no Azure, você poderá depurar remotamente esses aplicativos, diretamente do Visual Studio.

> [!NOTE]
> O recurso requer [SDK do Service Fabric 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [SDK do Azure para .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> A depuração remota é destinada a cenários de desenvolvimento/teste e não para uso em ambientes de produção, devido ao impacto nos aplicativos em execução.

1. Navegue até o seu cluster no **Cloud Explorer**. Clique com o botão direito do mouse e escolha **Ativar depuração**
   
    ![Habilitar depuração remota][enableremotedebugging]
   
    Essa ação iniciará o processo de habilitação da extensão de depuração remota em seus nós de cluster e configurações de rede necessárias.
2. Clique com o botão direito do mouse no nó de cluster em **Gerenciador de Nuvem** e escolha **Anexar Depurador**
   
    ![Anexar Depurador][attachdebugger]
3. Na caixa de diálogo **Anexar ao processo**, escolha o processo que deseja depurar e, em seguida, clique em **Anexar**
   
    ![Escolher processo][chooseprocess]
   
    O nome do processo ao qual você quer anexar é igual ao nome do assembly do seu projeto de serviço.
   
    O depurador vai anexar a todos os nós que estão executando o processo.
   
   * No caso em que você está depurando um serviço apátrida, todas as instâncias do serviço em todos os nós fazem parte da sessão de depuração.
   * Se você estiver depurando um serviço de estado, apenas a réplica primária de qualquer partição estará ativa e, portanto, capturada pelo depurador. Se a réplica primária for movida durante a sessão de depuração, o processamento de tal réplica ainda fará parte da sessão de depuração.
   * Para capturar apenas partições relevantes ou instâncias de um determinado serviço, você pode usar pontos de interrupção condicional para apenas quebrar uma partição ou instância específica.
     
     ![Ponto de interrupção condicional][conditionalbreakpoint]
     
     > [!NOTE]
     > Atualmente, não permitimos a depuração de um cluster do Service Fabric com várias instâncias de mesmo nome do executável de serviço.
     > 
     > 
4. Depois de concluir a depuração do aplicativo, você pode desabilitar a extensão de depuração remota clicando com o botão direito do mouse em **Gerenciador de Nuvem** e escolhendo **Desabilitar Depuração**
   
    ![Desabilitar depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Transmitindo rastreamentos de um nó de cluster remoto
Você também é capaz de transmitir traços diretamente de um nó de cluster remoto para o Visual Studio. Esse recurso permite transmitir eventos de rastreamento ETW gerados em um nó de cluster do Service Fabric.

> [!NOTE]
> O recurso requer o [SDK do Service Fabric 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e o [SDK do Azure para .NET 2.9](https://azure.microsoft.com/downloads/).
> Esse recurso só oferece suporte a clusters em execução no Azure.
> 
> 

<!-- -->
> [!WARNING]
> A transmissão de rastreamentos destina-se a cenários de desenvolvimento/teste e não ao uso em ambientes de produção, devido ao impacto nos aplicativos em execução.
> Em um cenário de produção, você deve confiar nos eventos de encaminhamento usando o Diagnóstico do Azure.

1. Navegue até o seu cluster no **Cloud Explorer**. Clique com o botão direito do mouse e escolha **Habilitar rastreamentos de streaming**
   
    ![Habilitar transmissão remota de rastreamentos][enablestreamingtraces]
   
    Essa ação iniciará o processo de habilitação da extensão de rastreamentos de streaming em seus nós de cluster, bem como as configurações de rede necessárias.
2. Expanda o elemento **Nós** no **Gerenciador de Nuvem**, clique com o botão direito do mouse no nó do qual deseja transmitir rastreamentos e escolha **Exibir Transmissão de Rastreamentos**
   
    ![Exibir transmissão remota de rastreamentos][viewremotestreamingtraces]
   
    Repita a etapa 2 para o número de nós dos quais você quer ver os rastreamentos. Cada fluxo de nó será mostrado em uma janela dedicada.
   
    Agora você poderá ver os rastreamentos emitidos pelo Service Fabric e seus serviços. Se quiser filtrar os eventos para mostrar somente um aplicativo específico, basta digitar o nome do aplicativo no filtro.
   
    ![Exibindo transmissão de rastreamentos][viewingstreamingtraces]
3. Depois de terminar a transmissão de rastreamentos do seu cluster, é possível desabilitar a transmissão remota de rastreamentos clicando com o botão direito do mouse no cluster em **Gerenciador de Nuvem** e escolhendo **Desabilitar Rastreamentos de Streaming**
   
    ![Desabilitar transmissão remota de rastreamentos][disablestreamingtraces]

## <a name="next-steps"></a>Próximas etapas
* [Testar um serviço da Malha do Serviço](service-fabric-testability-overview.md).
* [Gerenciar seu aplicativo do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
