---
title: Sessões de renderização remota
description: Descreve o que é uma sessão de renderização remota
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681682"
---
# <a name="remote-rendering-sessions"></a>Sessões de renderização remota

No Azure Remote Rendering (ARR), uma *sessão* é um conceito-chave. Este artigo explica exatamente o que é uma sessão.

## <a name="overview"></a>Visão geral

A renderização remota do Azure funciona descarregando tarefas complexas de renderização na nuvem. Essas tarefas de renderização não podem ser cumpridas por qualquer servidor, já que a maioria dos servidores em nuvem não tem GPUs. Devido à quantidade de dados envolvidos e à difícil exigência de produzir resultados a taxas de quadros interativas, a responsabilidade que o servidor lida com qual solicitação do usuário também não pode ser entregue a outra máquina em tempo real, como pode ser possível para tráfego web mais comum.

Isso significa que quando você usa renderização remota do Azure, um servidor em nuvem com os recursos de hardware necessários deve ser reservado exclusivamente para lidar com suas solicitações de renderização. Uma *sessão* refere-se a tudo o que envolve a interação com este servidor. Ele começa com a solicitação inicial de reserva (*locação*) de uma máquina para seu uso, continua com todos os comandos para carregar e manipular modelos, e termina com a liberação da locação no servidor de nuvem.

## <a name="managing-sessions"></a>Gerenciamento de sessões

Existem várias maneiras de gerenciar e interagir com as sessões. A maneira independente de criar, atualizar e encerrar sessões é através [da API REST de gerenciamento](../how-tos/session-rest-api.md)de sessões . Em C# e C++, essas operações `AzureFrontend` `AzureSession`são expostas através das classes e . Para aplicações Unity, existem outras funções `ARRServiceUnity` de utilidade fornecidas pelo componente.

Uma vez *conectado* a uma sessão ativa, operações como [carregar modelos](models.md) `AzureSession` e interagir com a cena são expostas através da classe.

### <a name="managing-multiple-sessions-simultaneously"></a>Gerenciamento de múltiplas sessões simultaneamente

Não é possível *conectar* totalmente a várias sessões de um dispositivo. No entanto, você pode criar, observar e desligar quantas sessões quiser a partir de um único aplicativo. Desde que o aplicativo não seja feito para se conectar a uma sessão, ele também não precisa ser executado em um dispositivo como o HoloLens 2. Um caso de uso para tal implementação pode ser se você quiser controlar sessões através de um mecanismo central. Por exemplo, pode-se construir um aplicativo web, onde vários tablets e HoloLenses podem fazer login. Em seguida, o aplicativo pode exibir opções nos tablets, como qual modelo CAD exibir. Se um usuário fizer uma seleção, essas informações serão comunicadas a todos os HoloLenses para criar uma experiência compartilhada.

## <a name="session-phases"></a>Fases da sessão

Cada sessão passa por múltiplas fases.

### <a name="session-startup"></a>Inicialização de sessão

Quando você pede ao ARR para [criar uma nova sessão,](../how-tos/session-rest-api.md#create-a-session)a primeira coisa que ele faz é retornar uma sessão [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Este UUID permite que você consulta informações sobre a sessão. O UUID e algumas informações básicas sobre a sessão são persistidos por 30 dias, para que você possa consultar essas informações mesmo após a sessão ter sido interrompida. Neste ponto, o estado da **sessão** será relatado como **Início**.

Em seguida, a renderização remota do Azure tenta encontrar um servidor que possa hospedar sua sessão. Há dois parâmetros para esta busca. Primeiro, ele só reservará servidores em sua [região.](../reference/regions.md) Isso porque a latência da rede em todas as regiões pode ser muito alta para garantir uma experiência decente. O segundo fator é o *tamanho* desejado que você especificou. Em cada região, há um número limitado de servidores que podem atender à solicitação de tamanho *Padrão* ou *Premium.* Consequentemente, se todos os servidores do tamanho solicitado estiverem atualmente em uso na sua região, a criação da sessão falhará. A razão do fracasso [pode ser questionada.](../how-tos/session-rest-api.md#get-sessions-properties)

> [!IMPORTANT]
> Se você solicitar um tamanho *VM padrão* e a solicitação falhar devido à alta demanda, isso não implica que a solicitação de um servidor *Premium* também falhe. Então, se é uma opção para você, você pode tentar voltar para uma VM *Premium.*

Quando o serviço encontra um servidor adequado, ele tem que copiar a máquina virtual (VM) adequada nele para transformá-lo em um host de renderização remota Do Zure. Esse processo leva vários minutos. Posteriormente, o VM é inicializado e o estado de **sessão** passa para **Pronto**.

Neste momento, o servidor está exclusivamente esperando sua entrada. Este também é o ponto a partir do qual você é cobrado pelo serviço.

### <a name="connecting-to-a-session"></a>Conectando-se a uma sessão

Uma vez que a sessão esteja *pronta,* você pode *se conectar* a ela. Enquanto conectado, o dispositivo pode enviar comandos para carregar e modificar modelos. Cada host ARR só serve um dispositivo cliente por vez, então quando um cliente se conecta a uma sessão, ele tem controle exclusivo sobre o conteúdo renderizado. Isso também significa que o desempenho de renderização nunca vai variar por razões fora do seu controle.

> [!IMPORTANT]
> Embora apenas um cliente possa *se conectar* a uma sessão, informações básicas sobre sessões, como seu estado atual, podem ser consultadas sem se conectar.

Enquanto um dispositivo estiver conectado a uma sessão, as tentativas de outros dispositivos de conexão falharão. No entanto, uma vez que o dispositivo conectado se desconecta, voluntariamente ou devido a algum tipo de falha, a sessão aceitará outra solicitação de conexão. Todos os estados anteriores (modelos carregados e tal) são descartados de tal forma que o próximo dispositivo de conexão receba uma lousa limpa. Assim, as sessões podem ser reutilizadas muitas vezes, por diferentes dispositivos e pode ser possível ocultar a sobrecarga de inicialização da sessão do usuário final na maioria dos casos.

> [!IMPORTANT]
> O servidor remoto nunca altera o estado dos dados do lado do cliente. Todas as mutações de dados (como atualizações de transformação e solicitações de carga) devem ser realizadas pelo aplicativo cliente. Todas as ações atualizam imediatamente o estado do cliente.

### <a name="session-end"></a>Fim da sessão

Quando você solicita uma nova sessão, você especifica um *tempo máximo de locação,* normalmente no intervalo de uma a oito horas. Esta é a duração durante a qual o host aceitará sua entrada.

Há duas razões regulares para uma sessão terminar. Ou você solicita manualmente que a sessão seja interrompida ou o tempo máximo de locação expira. Em ambos os casos, qualquer conexão ativa com o host é encerrada imediatamente, e o serviço é desligado nesse servidor. O servidor é então dado de volta ao pool Azure e pode ser requisitado para outros fins. Impedir uma sessão não pode ser desfeita ou cancelada. Consultar o **estado** da sessão em uma sessão parada retornará **Parado** ou **Expirado,** dependendo se foi desligado manualmente ou porque o tempo máximo de locação foi atingido.

Uma sessão também pode ser interrompida por causa de alguma falha.

Em todos os casos, você não será cobrado mais uma vez que uma sessão é interrompida.

> [!WARNING]
> Se você se conectar a uma sessão, e por quanto tempo, não afeta o faturamento. O que você paga pelo serviço depende da duração da *sessão,* ou seja, o tempo que um servidor é exclusivamente reservado para você e os recursos de hardware solicitados (o tamanho da VM). Se você iniciar uma sessão, conecte-se por cinco minutos e, em seguida, não pare a sessão, de tal forma que ela continue funcionando até que seu contrato de locação expire, você será cobrado pelo tempo de locação da sessão completa. Por outro lado, o *tempo máximo de locação* é principalmente uma rede de segurança. Não importa se você solicita uma sessão com um tempo de locação de oito horas, em seguida, use-a apenas por cinco minutos, se você parar manualmente a sessão depois.

#### <a name="extend-a-sessions-lease-time"></a>Estender o tempo de locação de uma sessão

Você pode estender o tempo de [locação](../how-tos/session-rest-api.md#update-a-session) de uma sessão ativa, se você precisar mais tempo.

## <a name="example-code"></a>Código de exemplo

O código abaixo mostra uma implementação simples de iniciar uma sessão, esperando o estado *pronto,* conectando e, em seguida, desconectando e desligando novamente.

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

`AzureFrontend` Várias `AzureSession` instâncias podem ser mantidas, manipuladas e consultadas a partir de código. Mas apenas um único dispositivo `AzureSession` pode se conectar a um de cada vez.

A vida útil de uma máquina `AzureFrontend` virtual não `AzureSession` está vinculada à instância ou à instância. `AzureSession.StopAsync`deve ser chamado para parar uma sessão.

O ID de sessão persistente `AzureSession.SessionUUID()` pode ser consultado via e armazenado em cache localmente. Com este ID, um `AzureFrontend.OpenSession` aplicativo pode ligar para vincular a essa sessão.

Quando `AzureSession.IsConnected` é `AzureSession.Actions` verdade, retorna `RemoteManager`uma instância de , que contém as funções para [carregar modelos,](models.md)manipular [entidades](entities.md)e [consultar informações](../overview/features/spatial-queries.md) sobre a cena renderizada.

## <a name="next-steps"></a>Próximas etapas

* [Entities](entities.md)
* [Modelos](models.md)
