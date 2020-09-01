---
title: Perguntas frequentes sobre o Serviço do Azure SignalR
description: Tenha acesso rápido a perguntas frequentes sobre o Serviço do Azure SignalR, sobre solução de problemas e cenários de uso típicos.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 6d104e41a0cae906c346e81a26617a9d29795fb3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853281"
---
# <a name="azure-signalr-service-faq"></a>Perguntas frequentes sobre o Serviço do Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço do Azure SignalR está pronto para uso em produção?

Sim.
Para nosso comunicado de disponibilidade geral, confira [Serviço do Azure SignalR agora disponível](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

O [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) é totalmente compatível.

O suporte para ASP.NET SignalR ainda está na *versão prévia pública*. Aqui está um [exemplo de código](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>A Conexão de cliente é fechada com a mensagem de erro "Nenhum servidor disponível". O que isso significa?

Esse erro ocorre somente quando os clientes estão enviando mensagens para o Serviço do SignalR.

Se você não tiver nenhum servidor de aplicativos e usar apenas a API REST do Serviço do SignalR, esse comportamento ocorrerá **por padrão**.
Na arquitetura sem servidor, as conexões de cliente estão no modo de **ESCUTA** e não enviarão mensagens para o Serviço do SignalR.
Leia mais sobre a [API REST](./signalr-quickstart-rest-api.md).

Se você tem servidores de aplicativos, essa mensagem de erro significa que nenhum servidor de aplicativos está conectado à instância do Serviço do SignalR.

As possíveis causas são:
- Nenhum servidor de aplicativos está conectado ao Serviço do SignalR. Verifique os logs do servidor de aplicativos para erros de conexão possíveis. Esse caso é raro na configuração de alta disponibilidade com mais de um servidor de aplicativos.
- Há problemas de conectividade com instâncias do Serviço do SignalR. Esse problema é temporário e será recuperado automaticamente.
Se ele persistir por mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou [crie uma solicitação de suporte no Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando há vários servidores de aplicativos, as mensagens de cliente são enviadas a todos os servidores ou apenas a um deles?

Trata-se de um mapeamento de um para um entre cliente e servidor de aplicativos. Mensagens de um cliente sempre são enviadas para o mesmo servidor de aplicativos.

O mapeamento entre cliente e servidor de aplicativos será mantido até que o cliente ou o servidor de aplicativos desconecte.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicativos estiver inativo, como poderei encontrá-lo e ser notificado?

O Serviço do SignalR monitora pulsações de servidores de aplicativos.
Se as pulsações não forem recebidas por um período de tempo especificado, o servidor de aplicativos será considerado offline. Todas as conexões de cliente mapeadas para esse servidor de aplicativos serão desconectadas.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Por que meu `IUserIdProvider` personalizado lança uma exceção ao alternar do SDK do ASP.NET Core SignalR para o SDK do Serviço do Azure SignalR?

O parâmetro `HubConnectionContext context` é diferente entre o SDK do ASP.NET Core SignalR e o SDK do Serviço do Azure SignalR quando `IUserIdProvider` é chamado.

No ASP.NET Core SignalR, `HubConnectionContext context` é o contexto da conexão de cliente física com os valores válidos para todas as propriedades.

No SDK de Serviço do Azure SignalR, `HubConnectionContext context` é o contexto de conexão de cliente lógica. A conexão de cliente física está conectada à instância do Serviço do SignalR, portanto, apenas um número limitado de propriedades é fornecido.

Por enquanto, apenas `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` estão disponíveis para acesso.
Você pode verificar o código-fonte [aqui](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no Serviço do SignalR configurando-o no lado do servidor, com o ASP.NET Core SignalR? Por exemplo, desabilitar o transporte de WebSocket?

Não.

O Serviço do Azure SignalR fornece todos os três transportes compatíveis por padrão com o ASP.NET Core SignalR. Ele não é configurável. O Serviço do SignalR cuidará das conexões e transportes para todas as conexões de cliente.

Você pode configurar os transportes do lado do cliente, conforme documentado [aqui](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual é o significado das métricas como contagem de mensagens ou contagem de conexões mostradas no portal do Azure? Qual tipo de agregação devo escolher?

Você pode encontrar os detalhes de como calculamos essas métricas [aqui](signalr-concept-messages-and-connections.md).

Na folha de visão geral dos recursos do Serviço do Azure SignalR, já escolhemos o tipo de agregação apropriado para você. E se você for para a folha de Métricas, use o tipo de agregação [daqui](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) como uma referência.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Qual é o significado do modo de serviço `Default`/`Serverless`/`Classic`? Como faço para escolher?

Modos:
* O modo `Default` *requer* servidor de hub. Neste módulo, o Azure SignalR roteia o tráfego do cliente para as conexões de servidor de hub conectadas. O Azure SignalR verifica se há um servidor de hub conectado. Se um servidor de hub conectado não for encontrado, o Azure SignalR rejeitará as conexões de entrada do cliente. Você também pode usar a **API de Gerenciamento** nesse modo para gerenciar os clientes conectados diretamente por meio do Azure SignalR.
* O modo `Serverless` *não* permite nenhuma conexão de servidor, por exemplo, rejeitará todas as conexões do servidor. Todos os clientes precisam estar no modo sem servidor. Os clientes se conectam ao Azure SignalR, e os usuários geralmente usam tecnologias sem servidor, como a **Função do Azure** para manipular as lógicas do hub. Confira um [exemplo simples](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) que usa o modo sem servidor do Azure SignalR.
* O modo `Classic` é um status misto. Quando um hub tem conexão com o servidor, o novo cliente será roteado para o servidor de hub, caso contrário, o cliente entrará no modo sem servidor.

  Isso poderá causar algum problema, por exemplo: todas as conexões de servidor serão perdidas por um momento, alguns clientes entrarão no modo sem servidor em vez de rotear para o servidor de hub.

Escolhendo:
1. Sem servidor de hub, escolha `Serverless`.
1. Todos os hubs têm servidores de hub, escolha `Default`.
1. Alguns hubs têm servidores de hub; outros não. Escolha `Classic`, mas isso poderá causar algum problema; a melhor maneira será criar duas instâncias, uma `Serverless` e outra `Default`.

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Há alguma diferença de recurso ao usar o Azure SignalR para o ASP.NET SignalR?
Ao usar o Azure SignalR, algumas APIs e recursos do ASP.NET SignalR ficarão sem suporte:
- A capacidade de passar um estado arbitrário entre clientes e o hub (geralmente chamado de `HubState`) não terá suporte com o uso do Azure SignalR
- A classe `PersistentConnection` ainda não tem suporte ao usar o Azure SignalR
- Não há suporte para o **transporte de quadro contínuo** ao usar o Azure SignalR
- O Azure SignalR não repete mais as mensagens enviadas ao cliente quando o cliente está offline
- Ao usar o Azure SignalR, o tráfego para uma conexão de cliente é sempre roteado (também conhecido como **adesivo**) para uma instância de servidor de aplicativo durante a conexão

O suporte para o ASP.NET SignalR concentra-se na compatibilidade. Portanto, nem todos os novos recursos do ASP.NET Core SignalR têm suporte. Por exemplo, **MessagePack**, **Streaming** etc., estão disponíveis somente para aplicativos do ASP.NET Core SignalR.

O Serviço do SignalR pode ser configurado para modos de serviço diferentes: `Classic`/`Default`/`Serverles`s. Neste suporte do ASP.NET, não há suporte para o modo `Serverless`. A API REST do plano de dados também não tem suporte.

## <a name="where-do-my-data-reside"></a>Onde meus dados residem?

O Serviço do Azure SignalR está funcionando como um serviço processador de dados. Ele não armazenará nenhum conteúdo do cliente e a residência de dados é prometida por design. Se você usar o Serviço do Azure SignalR junto com outros serviços do Azure, como o Armazenamento do Azure para diagnóstico, dê uma olhada [aqui](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) para obter diretrizes de como manter a residência de dados em regiões do Azure.
