---
title: Perguntas frequentes sobre o Serviço do Azure SignalR
description: Veja as respostas para perguntas frequentes sobre o Serviço do Azure SignalR, incluindo solução de problemas e cenários de uso típicos.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489554"
---
# <a name="azure-signalr-service-faq"></a>Perguntas frequentes sobre o Serviço do Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço do Azure SignalR está pronto para uso em produção?

Sim.
Para nosso comunicado de disponibilidade geral, confira [Serviço do Azure SignalR agora em disponibilidade geral](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

O [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) é totalmente compatível.

O suporte para o ASP.NET SignalR ainda está na *versão prévia pública*. [Veja um exemplo de código](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>A conexão de cliente é fechada com a mensagem de erro "Nenhum servidor disponível". O que isso significa?

Esse erro ocorre somente quando os clientes estão enviando mensagens para o Serviço do Azure SignalR.

Se você não tiver nenhum servidor de aplicativos e usar apenas a API REST do Serviço do Azure SignalR, esse comportamento ocorrerá *por padrão*.
Na arquitetura sem servidor, as conexões de cliente estão no modo de *escuta* e não enviam mensagens para o Serviço do Azure SignalR.
Leia [mais sobre a API REST](./signalr-quickstart-rest-api.md).

Se você tem servidores de aplicativos, essa mensagem de erro significa que nenhum servidor de aplicativos está conectado à instância do Serviço do Azure SignalR.

As possíveis causas são:
- Nenhum servidor de aplicativos está conectado ao Serviço do Azure SignalR. Verifique os logs do servidor de aplicativos para erros de conexão possíveis. Esse caso é raro na configuração de alta disponibilidade com mais de um servidor de aplicativos.
- Há problemas de conectividade com instâncias do Serviço do Azure SignalR. Esse problema é temporário e as instâncias serão recuperadas automaticamente.
Se ele persistir por mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou [crie uma solicitação de suporte no Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando há vários servidores de aplicativos, as mensagens de cliente são enviadas a todos os servidores ou apenas a um deles?

Há um mapeamento de um para um entre um cliente e um servidor de aplicativos. Mensagens de um cliente sempre são enviadas para o mesmo servidor de aplicativos.

O mapeamento é mantido até que o cliente ou o servidor de aplicativos desconecte.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicativos estiver inativo, como poderei encontrá-lo e ser notificado?

O Serviço do Azure SignalR monitora pulsações de servidores de aplicativos.
Se as pulsações não forem recebidas por um período de tempo especificado, o servidor de aplicativos será considerado offline. Todas as conexões de cliente mapeadas para esse servidor de aplicativos serão desconectadas.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Por que meu `IUserIdProvider` personalizado lança uma exceção quando eu alterno do SDK do ASP.NET Core SignalR para o SDK do Serviço do Azure SignalR?

O parâmetro `HubConnectionContext context` é diferente entre o SDK do ASP.NET Core SignalR e o SDK do Serviço do Azure SignalR quando `IUserIdProvider` é chamado.

No ASP.NET Core SignalR, `HubConnectionContext context` é o contexto da conexão de cliente física com os valores válidos para todas as propriedades.

No SDK de Serviço do Azure SignalR, `HubConnectionContext context` é o contexto de conexão de cliente lógica. O cliente físico está conectado à instância do Serviço do Azure SignalR, portanto, apenas um número limitado de propriedades é fornecido.

Por enquanto, apenas `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` estão disponíveis para acesso.
Você pode [verificar o código-fonte](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no Serviço do Azure SignalR no lado do servidor, com o ASP.NET Core SignalR? Por exemplo, posso desabilitar o transporte de WebSocket?

Não.

O Serviço do Azure SignalR fornece todos os três transportes compatíveis por padrão com o ASP.NET Core SignalR. Ele não é configurável. O Serviço do Azure SignalR cuidará das conexões e transportes para todas as conexões de cliente.

Você pode configurar os transportes do lado do cliente conforme documentado na [configuração do SignalR para ASP.NET Core](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual é o significado das métricas, como contagem de mensagens ou contagem de conexões, mostradas no portal do Azure? Qual tipo de agregação devo escolher?

Você pode encontrar detalhes sobre como calculamos essas métricas em [Mensagens e conexões no Serviço do Azure SignalR](signalr-concept-messages-and-connections.md).

No painel de visão geral dos recursos do Serviço do Azure SignalR, já escolhemos o tipo de agregação apropriado para você. Se você for até o painel de métricas, poderá usar o tipo de agregação de [Mensagens e conexões no Serviço do Azure SignalR](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) como uma referência.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Qual é o significado dos modos de serviço `Default`, `Serverless` e `Classic`? Como faço para escolher?

Veja as informações sobre os modos:
* O modo `Default` *exige* um servidor de hub. Nesse modo, o Serviço do Azure SignalR roteia o tráfego do cliente para as conexões de servidor de hub conectadas. O Serviço do Azure SignalR verifica se há um servidor de hub conectado. Se o serviço não puder encontrar um servidor de hub conectado, ele rejeitará as conexões de entrada do cliente. Você também pode usar a *API de Gerenciamento* nesse modo para gerenciar os clientes conectados diretamente por meio do Serviço do Azure SignalR.
* O modo `Serverless` *não* permite nenhuma conexão de servidor. Ou seja, ele rejeitará todas as conexões do servidor. Todos os clientes precisam estar no modo sem servidor. Os clientes se conectam ao Serviço do Azure SignalR, e os usuários geralmente usam tecnologias sem servidor, como a *Função do Azure* para manipular as lógicas do hub. [Confira um exemplo simples](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) que usa o modo sem servidor do Serviço do Azure SignalR.
* O modo `Classic` é um status misto. Quando um hub tem uma conexão de servidor, o novo cliente é roteado para um servidor de hub. Caso contrário, o cliente entrará no modo sem servidor. 

  Isso pode causar um problema. Por exemplo, se todas as conexões de servidor forem perdidas por um momento, alguns clientes entrarão no modo sem servidor em vez de rotear para um servidor de hub.

Veja algumas diretrizes para escolher um modo:
- Se não houver um servidor de hub, escolha `Serverless`.
- Se todos os hubs tiverem servidores de hub, escolha `Default`.
- Se alguns hubs tiverem servidores de hub e outros não, você poderá escolher `Classic`, mas isso poderá causar um problema. A melhor maneira é criar duas instâncias: uma é `Serverless` e a outra é `Default`.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Há alguma diferença de recursos ao usar o Serviço do Azure SignalR ou o ASP.NET SignalR?
Quando você usa o Serviço do Azure SignalR, algumas APIs e recursos do ASP.NET SignalR não têm suporte:
- A capacidade de passar um estado arbitrário entre clientes e o hub (geralmente chamada de `HubState`) não terá suporte.
- Não há suporte para classe `PersistentConnection`.
- Não há suporte para o *transporte de Quadro Contínuo*.
- O Serviço do Azure SignalR não repete mais as mensagens enviadas ao cliente quando o cliente está offline.
- Quando você está usando o Serviço do Azure SignalR, o tráfego para uma conexão de cliente sempre é roteado (também chamado de *adesivo*) para uma instância do servidor de aplicativos durante a conexão.

O suporte para o ASP.NET SignalR concentra-se na compatibilidade. Portanto, nem todos os novos recursos do ASP.NET Core SignalR têm suporte. Por exemplo, *MessagePack* e *Streaming* estão disponíveis somente para aplicativos do ASP.NET Core SignalR.

Você pode configurar o Serviço do Azure SignalR para diferentes modos de serviço: `Classic`, `Default` e `Serverless`. O modo `Serverless` não tem suporte para o ASP.NET. A API REST do plano de dados também não tem suporte.

## <a name="where-does-my-data-reside"></a>Onde meus dados residem?

O Serviço do Azure SignalR funciona como um serviço processador de dados. Ele não armazena nenhum conteúdo do cliente e a residência de dados é incluída por design. Se você usar o Serviço do Azure SignalR junto com outros serviços do Azure, como o Armazenamento do Azure para diagnóstico, confira [este white paper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) para obter diretrizes de como manter a residência de dados em regiões do Azure.
