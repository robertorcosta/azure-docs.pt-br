---
title: Sessões do Remote Rendering
description: Descreve o que é uma sessão de renderização remota
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681682"
---
# <a name="remote-rendering-sessions"></a>Sessões do Remote Rendering

Na renderização remota do Azure (ARR), uma *sessão* é um conceito fundamental. Este artigo explica o que é exatamente uma sessão.

## <a name="overview"></a>Visão geral

A renderização remota do Azure funciona descarregando tarefas de renderização complexas na nuvem. Essas tarefas de renderização não podem ser atendidas apenas por qualquer servidor, já que a maioria dos servidores de nuvem não tem GPUs. Devido à quantidade de dados envolvidos e ao requisito rígido de produzir resultados em taxas de quadros interativas, a responsabilidade que o servidor controla qual solicitação de usuário também não pode ser transmitida para outro computador imediatamente, como pode ser possível para o tráfego da Web mais comum.

Isso significa que, quando você usa a renderização remota do Azure, um servidor de nuvem com os recursos de hardware necessários deve ser reservado exclusivamente para lidar com suas solicitações de renderização. Uma *sessão* refere-se a tudo que está envolvido na interação com este servidor. Ele começa com a solicitação inicial para reservar (*conceder*) um computador para seu uso, continua com todos os comandos para carregar e manipular modelos e termina com a liberação da concessão no servidor de nuvem.

## <a name="managing-sessions"></a>Gerenciando sessões

Há várias maneiras de gerenciar e interagir com sessões. A maneira independente de linguagem de criar, atualizar e desligar sessões é por meio [da API REST de gerenciamento de sessão](../how-tos/session-rest-api.md). Em C# e C++, essas operações são expostas por `AzureFrontend` meio `AzureSession`das classes e. Para aplicativos do Unity, há outras funções de `ARRServiceUnity` utilitário fornecidas pelo componente.

Quando você estiver *conectado* a uma sessão ativa, as operações como [carregar modelos](models.md) e interagir com a cena serão expostas por `AzureSession` meio da classe.

### <a name="managing-multiple-sessions-simultaneously"></a>Gerenciando várias sessões simultaneamente

Não é possível *se conectar* totalmente a várias sessões de um dispositivo. No entanto, você pode criar, observar e desligar quantas sessões desejar em um único aplicativo. Desde que o aplicativo não se destinasse a se conectar a uma sessão, ele não precisa ser executado em um dispositivo como o HoloLens 2, seja. Um caso de uso para tal implementação pode ser se você quiser controlar sessões por meio de um mecanismo central. Por exemplo, um pode criar um aplicativo Web, em que vários tablets e HoloLenses podem fazer logon. Em seguida, o aplicativo pode exibir opções nos tablets, como o modelo CAD a ser exibido. Se um usuário fizer uma seleção, essas informações serão comunicadas a todos os HoloLenses para criar uma experiência compartilhada.

## <a name="session-phases"></a>Fases da sessão

Cada sessão passa por várias fases.

### <a name="session-startup"></a>Inicialização da sessão

Quando você pede ao ARR para [criar uma nova sessão](../how-tos/session-rest-api.md#create-a-session), a primeira coisa que ele faz é retornar um [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)de sessão. Esse UUID permite consultar informações sobre a sessão. O UUID e algumas informações básicas sobre a sessão são persistidos por 30 dias, para que você possa consultar essas informações mesmo depois que a sessão for interrompida. Neste ponto, o **estado da sessão** será relatado como **iniciando**.

Em seguida, a renderização remota do Azure tenta encontrar um servidor que pode hospedar sua sessão. Há dois parâmetros para essa pesquisa. Primeiro, ele só reservará servidores em sua [região](../reference/regions.md). Isso ocorre porque a latência de rede entre regiões pode ser muito alta para garantir uma experiência razoável. O segundo fator é o *tamanho* desejado que você especificou. Em cada região, há um número limitado de servidores que podem atender à solicitação de tamanho *Standard* ou *Premium* . Consequentemente, se todos os servidores do tamanho solicitado estiverem atualmente em uso em sua região, a criação da sessão falhará. O motivo da falha [pode ser consultado](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Se você solicitar um tamanho de VM *padrão* e a solicitação falhar devido a alta demanda, isso não implica que a solicitação de um servidor *Premium* também falhará. Portanto, se for uma opção para você, você pode tentar fazer fallback para uma VM *Premium* .

Quando o serviço encontra um servidor adequado, ele precisa copiar a VM (máquina virtual) apropriada para ela para transformá-la em um host de renderização remoto do Azure. Esse processo leva vários minutos. Depois, a VM é inicializada e o **estado da sessão** faz a transição para **pronto**.

Neste ponto, o servidor está esperando exclusivamente pela sua entrada. Esse também é o ponto no qual você recebe a cobrança pelo serviço.

### <a name="connecting-to-a-session"></a>Conectando-se a uma sessão

Quando a sessão estiver *pronta*, você poderá *se conectar* a ela. Enquanto conectado, o dispositivo pode enviar comandos para carregar e modificar modelos. Cada host ARR apenas atende a um dispositivo de cliente por vez, portanto, quando um cliente se conecta a uma sessão, ele tem controle exclusivo sobre o conteúdo renderizado. Isso também significa que o desempenho de renderização nunca irá variar por motivos fora do seu controle.

> [!IMPORTANT]
> Embora apenas um cliente possa *se conectar* a uma sessão, as informações básicas sobre sessões, como seu estado atual, podem ser consultadas sem conexão.

Enquanto um dispositivo estiver conectado a uma sessão, as tentativas de conexão por outros dispositivos falharão. No entanto, quando o dispositivo conectado for desconectado, voluntariamente ou devido a algum tipo de falha, a sessão aceitará outra solicitação de conexão. Todos os Estados anteriores (modelos carregados e assim por diante) são descartados de forma que o próximo dispositivo de conexão obtenha um Tablet limpo. Assim, as sessões podem ser reutilizadas muitas vezes, por diferentes dispositivos e pode ser possível ocultar a sobrecarga de inicialização da sessão do usuário final na maioria dos casos.

> [!IMPORTANT]
> O servidor remoto nunca altera o estado dos dados do lado do cliente. Todas as mutações de dados (como atualizações de transformação e solicitações de carregamento) devem ser executadas pelo aplicativo cliente. Todas as ações atualizam imediatamente o estado do cliente.

### <a name="session-end"></a>Término da sessão

Ao solicitar uma nova sessão, você especifica um *tempo máximo de concessão*, normalmente no intervalo de uma a oito horas. Essa é a duração durante a qual o host aceitará sua entrada.

Há dois motivos regulares para uma sessão terminar. Você solicita manualmente a sessão a ser interrompida ou o tempo máximo de concessão expira. Em ambos os casos, qualquer conexão ativa com o host é fechada imediatamente e o serviço é desligado nesse servidor. Em seguida, o servidor é retornado ao pool do Azure e pode ser revisado para outras finalidades. A interrupção de uma sessão não pode ser desfeita ou cancelada. Consultar o **estado da sessão** em uma sessão interrompida retornará **parado** ou **expirado**, dependendo se ele foi desligado manualmente ou porque o tempo máximo de concessão foi atingido.

Uma sessão também pode ser interrompida devido a alguma falha.

Em todos os casos, você não será cobrado ainda mais depois que uma sessão for interrompida.

> [!WARNING]
> Se você se conectar a uma sessão e por quanto tempo, o não afetará a cobrança. O que você paga pelo serviço depende da *duração da sessão*, o que significa que o tempo que um servidor está reservado exclusivamente para você e os recursos de hardware solicitados (o tamanho da VM). Se você iniciar uma sessão, conecte-se por cinco minutos e, em seguida, não interrompa a sessão, de modo que ela continue em execução até que sua concessão expire, você será cobrado pelo tempo de concessão da sessão completa. Por outro lado, o *tempo máximo de concessão* é principalmente uma rede de segurança. Não importa se você solicitar uma sessão com um tempo de concessão de oito horas e, em seguida, usá-la somente por cinco minutos, se você interromper manualmente a sessão posteriormente.

#### <a name="extend-a-sessions-lease-time"></a>Estender o tempo de concessão de uma sessão

Você pode [estender o tempo de concessão](../how-tos/session-rest-api.md#update-a-session) de uma sessão ativa, se for necessário mais tempo.

## <a name="example-code"></a>Código de exemplo

O código a seguir mostra uma implementação simples de iniciar uma sessão, aguardando o estado *pronto* , conectando e desconectando e desligando novamente.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Várias `AzureFrontend` instâncias `AzureSession` e podem ser mantidas, manipuladas e consultadas do código. Mas apenas um único dispositivo pode se conectar a `AzureSession` um de cada vez.

O tempo de vida de uma máquina virtual não está `AzureFrontend` vinculado à instância `AzureSession` ou à instância. `AzureSession.StopAsync`deve ser chamado para parar uma sessão.

A ID de sessão persistente pode ser consultada `AzureSession.SessionUUID()` por meio de e armazenada em cache localmente. Com essa ID, um aplicativo pode chamar `AzureFrontend.OpenSession` para associar a essa sessão.

Quando `AzureSession.IsConnected` é verdadeiro, `AzureSession.Actions` retorna uma instância do `RemoteManager`, que contém as funções para [carregar modelos](models.md), manipular [entidades](entities.md)e [consultar informações](../overview/features/spatial-queries.md) sobre a cena renderizada.

## <a name="next-steps"></a>Próximas etapas

* [Entities](entities.md)
* [Modelos](models.md)
