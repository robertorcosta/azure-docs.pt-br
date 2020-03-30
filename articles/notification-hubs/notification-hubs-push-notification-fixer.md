---
title: Diagnosticar notificações descartadas nos Hubs de Notificação do Azure
description: Saiba como diagnosticar problemas comuns com notificações removidas nos Hubs de Notificações do Microsoft Azure.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657479"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnosticar notificações descartadas nos Hubs de Notificação do Azure

Uma pergunta comum sobre o Azure Notification Hubs é como solucionar problemas quando notificações de um aplicativo não aparecem em dispositivos clientes. Os clientes querem saber onde e por que as notificações foram retiradas e como corrigir o problema. Este artigo identifica o motivo pelo qual as notificações podem ser removidas ou não recebidas pelos dispositivos. Também explica como determinar a causa raiz.

É fundamental compreender primeiro como os Hubs de Notificação efetua push das notificações para um dispositivo.

![Arquitetura dos Hubs de Notificação][0]

Em um fluxo de envio de notificação típico, a mensagem é enviada do *back-end do aplicativo* para os Hubs de Notificação. O Notification Hubs processa todos os registros. Ele leva em conta as tags configuradas e as expressões de marcação para determinar os alvos. Os alvos são os registros que precisam receber a notificação push. Esses registros podem abranger qualquer uma de nossas plataformas suportadas: Android, Baidu (dispositivos Android na China), Fire OS (Amazon) iOS, Windows e Windows Phone.

Com os destinos estabelecidos, os Hubs de Notificação efetuam push nas notificações para o *serviço de notificação por push* para a plataforma do dispositivo. Exemplos incluem o serviço de notificação do Push (APNs) da Apple para iOS e macOS, e o Firebase Cloud Messaging (FCM) para dispositivos Android. Os Hubs de Notificação efetuam push de notificações divididas em diversos lotes de registros. Ele autentica-se com o respectivo serviço de notificação push, com base nas credenciais definidas no portal Azure, em **Configure Notification Hub**. O serviço de notificação por push encaminha as notificações para os respectivos *dispositivos cliente*.

A última etapa da entrega de notificação é entre o serviço de notificação push da plataforma e o dispositivo. A entrega de notificação pode falhar em qualquer uma das quatro etapas do processo de notificação push (cliente, back-end de aplicativo, Hubs de Notificação e serviço de notificação push da plataforma). Para obter mais informações sobre a arquitetura dos Hubs de Notificação, consulte [Visão geral dos Hubs de Notificação].

Uma falha no cumprimento das notificações pode ocorrer durante a fase inicial de teste/estadiamento. Notificações removidas neste estágio podem indicar um problema de configuração. Se ocorrer uma falha na entrega das notificações na produção, algumas ou todas as notificações poderão ser descartadas. Neste caso, um problema mais profundo de padrão de aplicativos ou mensagens é indicado.

A próxima seção analisa cenários em que as notificações podem ser descartadas, variando de comuns a raras.

## <a name="notification-hubs-misconfiguration"></a>Configuração incorreta dos Hubs de Notificação

Para enviar notificações ao respectivo serviço de notificação push, os Hubs de Notificação devem autenticar-se no contexto do seu aplicativo. Você deve criar uma conta de desenvolvedor com o serviço de notificação da plataforma-alvo (Microsoft, Apple, Google, etc.). Em seguida, você deve registrar seu aplicativo com o SO onde você recebe um token ou chave que você usa para trabalhar com o PNS de destino.

Você precisa adicionar as credenciais da plataforma para o Portal do Azure. Se nenhuma notificações estiver chegando ao dispositivo, o primeiro passo é garantir que as credenciais corretas estejam configuradas nos Hubs de Notificação. As credenciais devem corresponder ao aplicativo criado sob uma conta de desenvolvedor específica da plataforma.

Para ver instruções passo a passo para concluir esse processo, consulte [Introdução aos Hubs de Notificações do Microsoft Azure].

Aqui estão algumas configurações incorretas comuns para observar:

### <a name="notification-hub-name-location"></a>Localização do nome do hub de notificação

Verifique se o nome do seu hub de notificação (sem erros de grafia) é o mesmo em todos esses locais:

* Onde você se registra do cliente
* Para onde você envia notificações do back-end
* Onde você configurou as credenciais de serviço de notificação push

Certifique-se de usar as strings corretas de configuração de assinatura de acesso compartilhado no cliente e no back-end do aplicativo. Geralmente, você deve usar **DefaultListenSharedAccessSignature** no cliente e **DefaultFullSharedAccessSignature** no back-end do aplicativo. Isso concede permissões para enviar notificações aos Hubs de Notificação.

### <a name="apn-configuration"></a>Configuração APN

Você deve manter dois hubs diferentes: um para produção e outro para testes. Você deve carregar o certificado que você usa em um ambiente de caixa de areia para um hub separado do que o certificado/hub que você usará na produção. Não tente carregar tipos diferentes de certificados para o mesmo hub. Causará falhas na notificação.

Se você enviar inadvertidamente diferentes tipos de certificados para o mesmo hub, você deve excluir o hub e começar de novo com um novo hub. Se por algum motivo você não puder excluir o hub, você deve pelo menos excluir todos os registros existentes do hub.

### <a name="fcm-configuration"></a>Configuração fcm

1. Certifique-se de que a chave de *servidor* obtida no Firebase corresponde à chave de servidor que você registrou no portal Azure.

   ![Chave do servidor Firebase][3]

2. Verifique se você configurou a **ID do projeto** no cliente. Você pode obter o valor da **ID do projeto** do painel do Firebase.

   ![ID do projeto Firebase][1]

## <a name="application-issues"></a>Problemas de aplicativos

### <a name="tags-and-tag-expressions"></a>Tags e expressões de tags

Se você usar tags ou expressões de tags para segmentar seu público, é possível que, ao enviar a notificação, nenhum alvo seja encontrado. Este erro é baseado nas marcas especificadas ou expressões de tag em sua chamada de envio.

Revise seus registros para garantir que as tags correspondam quando você enviar uma notificação. Em seguida, verifique o recibo de notificação somente dos clientes que possuem esses registros.

Por exemplo, suponha que todos os seus registros com hubs de notificação usem a tag "Política". Se você enviar uma notificação com a tag "Esportes", a notificação não será enviada para nenhum dispositivo. Um caso complexo pode envolver expressões de marcação onde você se registrou usando "Tag A" *ou* "Tag B", mas você teve como alvo "Tag A && Tag B". A seção de dicas de autodiagnóstico mais tarde no artigo mostra como revisar seus registros e suas tags.

### <a name="template-issues"></a>Problemas do modelo

Se você usar modelos, siga as diretrizes descritas em [Modelos].

### <a name="invalid-registrations"></a>Registros inválidos

Se o centro de notificação foi configurado corretamente e as marcas ou expressões de tags foram usadas corretamente, alvos válidos serão encontrados. As notificações devem ser enviadas para esses destinos. Os Hubs de Notificação, em seguida, disparam vários lotes de processamento em paralelo. Cada lote envia mensagens para um conjunto de registros.

> [!NOTE]
> Como o Notification Hubs processa lotes em paralelo, a ordem em que as notificações são entregues não é garantida.

O Notification Hubs é otimizado para um modelo de entrega de mensagens "no máximo de uma vez". Tentamos realizar uma eliminação de duplicação para que nenhuma notificação seja entregue mais de uma vez para um dispositivo. Os registros são verificados para garantir que apenas uma mensagem seja enviada por identificador de dispositivo antes de ser enviada ao serviço de notificação push.

Cada lote é enviado para o serviço de notificação push, que por sua vez aceita e valida os registros. Durante esse processo, é possível que o serviço de notificação push detecte um erro com um ou mais registros em um lote. O serviço de notificação push retorna um erro aos Hubs de Notificação e o processo pára. O serviço de notificação por push remove esse lote completamente. Isso é especialmente verdadeiro com APNs, que usa um protocolo de fluxo TCP.

Neste caso, o registro de falha é removido do banco de dados. Em seguida, tentamos entregar a notificação novamente para o restante dos dispositivos nesse lote.

Para obter mais informações de erro sobre a tentativa de entrega falha contra um registro, você pode usar as APIs DE NOTIFICAÇÃO HUBS REST [Por Telemetria de Mensagem: Obtenha telemetria de mensagem de notificação](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry) e feedback [PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Para obter o código de exemplo, consulte o [Exemplo de envio de REST](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemas do serviço de notificação por push

Após o serviço de notificação push receber a notificação, ele entrega a notificação ao dispositivo. Neste momento, o Notification Hubs não tem controle sobre a entrega da notificação ao dispositivo.

Como os serviços de notificação da plataforma são robustos, as notificações tendem a chegar aos dispositivos em poucos segundos. Se o serviço de notificação push estiver estrangulando, o Notification Hubs aplicará uma estratégia de back-off exponencial. Se o serviço de notificação push permanecer inalcançável por 30 minutos, há uma política em vigor para expirar e soltar as mensagens permanentemente.

Se um serviço de notificação push tentar entregar uma notificação, mas o dispositivo estiver off-line, a notificação será armazenada pelo serviço de notificação push. É armazenado por um período limitado de tempo. A notificação é entregue ao dispositivo quando ele estiver disponível.

Cada aplicativo armazena apenas uma notificação recente. Se várias notificações forem enviadas enquanto um dispositivo estiver offline, cada nova notificação fará com que a última seja descartada. Manter apenas a mais nova notificação é chamado *de coalescção* em APNs e *colapso* no FCM. (A FCM usa uma chave em colapso.) Quando o dispositivo permanece offline por um longo tempo, as notificações armazenadas para o dispositivo são descartadas. Para obter mais informações, consulte [visão geral das APNs] e [sobre as mensagens FCM].

Com os Hubs de Notificação, você pode passar uma chave de coalizão através de um cabeçalho HTTP usando a API genérica SendNotification. Por exemplo, para o SDK do .NET, você usaria `SendNotificationAsync`. A API SendNotification também leva cabeçalhos HTTP que são passados como é para o respectivo serviço de notificação push.

## <a name="self-diagnosis-tips"></a>Dicas de autodiagnóstico

Aqui estão os caminhos para diagnosticar a causa raiz das notificações descartadas nos Hubs de Notificação.

### <a name="verify-credentials"></a>Verifique as credenciais

#### <a name="push-notification-service-developer-portal"></a>Portal de desenvolvedor do serviço de notificação push

Verifique as credenciais no respectivo portal do desenvolvedor do serviço de notificação por push (APNs, FCM, Serviço de Notificação do Windows e assim por diante). Para obter mais informações, consulte [Tutorial: Envie notificações para aplicativos da Universal Windows Platform usando os Hubs de Notificação do Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Portal do Azure

Para revisar e combinar as credenciais com as obtidas no portal do desenvolvedor do serviço de notificação push, acesse a guia **Políticas de Acesso** no portal Azure.

![Políticas de acesso do Portal do Azure][4]

### <a name="verify-registrations"></a>Verificar registros

#### <a name="visual-studio"></a>Visual Studio

No Visual Studio, você pode se conectar ao Azure através do Server Explorer para visualizar e gerenciar vários serviços do Azure, incluindo hubs de notificação. Este atalho é principalmente útil para o seu ambiente de desenvolvimento/teste.

![Gerenciador de Servidores do Visual Studio][9]

![Gerenciador de Servidores](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Você pode visualizar e gerenciar todos os registros em seu hub. Os registros podem ser categorizados por plataforma, registro nativo ou modelo, tag, identificador de serviço de notificação push, ID de registro e data de validade. Também é possível editar um registro nesta página. É especialmente útil para editar tags.

Clique com o botão direito do mouse no hub de notificação no **Server Explorer**e selecione **Diagnosticar**. 

![Visual Studio Server Explorer: Menu de diagnóstico](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Veja a seguinte página:

![Visual Studio: Página de diagnóstico](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Mude para a página **Registros de dispositivos:**

![Visual Studio: Registros de dispositivos](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Você pode usar a página **Enviar testes** para enviar uma mensagem de notificação de teste:

![Visual Studio: Test Send](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Use o Visual Studio para editar registros somente durante o desenvolvimento/teste e com um número limitado de inscrições. Se você precisar editar seus registros em massa, considere usar a funcionalidade de registro de exportação e importação descrita em [Como Exportar e Modificar Registros em Massa](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Muitos clientes usam [o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) para visualizar e gerenciar seus hubs de notificação. O Gerenciador de Barramento de Serviço é um projeto de software livre. 

### <a name="verify-message-notifications"></a>Verificar as notificações de mensagem

#### <a name="azure-portal"></a>Portal do Azure

Para enviar uma notificação de teste para seus clientes sem precisar de um serviço de back-end em funcionamento, em **SUPORTE + SOLUÇÃO DE PROBLEMAS**, selecione **Envio de Teste**.

![Funcionalidade de Envio de Teste no Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Você também pode enviar notificações de teste do Visual Studio.

![Funcionalidade de Envio de Teste no Visual Studio][10]

Para obter mais informações sobre como usar os Hubs de Notificação com o Gerenciador de Servidores do Visual Studio, consulte estes artigos:

* [Como visualizar registros de dispositivos para hubs de notificação](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Análise aprofundada: Visual Studio 2013 Atualização 2 RC e SDK do Azure 2.3]
* [Anunciando o lançamento do Visual Studio 2013 Atualização 3 e SDK do Azure 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Depurar notificações com falha e examinar o resultado da notificação

#### <a name="enabletestsend-property"></a>Propriedade EnableTestSend

Quando você envia uma notificação via Notification Hubs, a notificação é inicialmente enfileirada. Os Hubs de Notificação determinam os destinos corretos e, em seguida, enviam a notificação para o serviço de notificação por push. Se você estiver usando a API REST ou qualquer um dos SDKs do cliente, o retorno da sua chamada de envio significa apenas que a mensagem está enfileirada com hubs de notificação. Ele não fornece informações sobre o que aconteceu quando o Notification Hubs eventualmente enviou a notificação para o serviço de notificação push.

Se sua notificação não chegar ao dispositivo cliente, um erro pode ter ocorrido quando os Notification Hubs tentaram entregá-lo ao serviço de notificação push. Por exemplo, o tamanho do conteúdo poderá exceder o máximo permitido pelo serviço de notificação por push, ou as credenciais configuradas em Hubs de Notificação podem ser inválidas.

Para obter informações sobre erros do serviço de notificação por push, você pode usar a propriedade [EnableTestSend]. Essa propriedade é habilitada automaticamente quando você envia mensagens de teste do portal ou do cliente do Visual Studio. Você pode usar esta propriedade para ver informações detalhadas de depuração e também via APIs. No momento, é possível usá-la no SDK do .NET. Ele será adicionado a todos os SDKs clientes eventualmente.

Para usar a propriedade `EnableTestSend` com a chamada REST, acrescente um parâmetro de cadeia de consulta chamado *test* ao final da sua chamada de envio. Por exemplo: 

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Exemplo .NET SDK

Veja este exemplo de como usar o SDK do .NET para enviar uma notificação de pop-up nativo (notificação do sistema):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

No final da execução, `result.State` simplesmente declara `Enqueued`. Os resultados não fornecem nenhuma visão sobre o que aconteceu com sua notificação push.

Em seguida, você pode usar a propriedade booliana `EnableTestSend`. Use a propriedade `EnableTestSend` ao iniciar `NotificationHubClient` para obter um status detalhado sobre os erros do serviço de notificação por push que ocorrem quando a notificação é enviada. A chamada de envio leva mais tempo para retornar porque primeiro precisa de Hubs de Notificação para entregar a notificação ao serviço de notificação push.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Saída de exemplo

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Esta mensagem indica que as credenciais configuradas nos Hubs de Notificação são inválidas ou que há um problema com os registros no hub. Exclua este registro e deixe o cliente recriar o registro antes de enviar a mensagem.

> [!NOTE]
> O uso da propriedade `EnableTestSend` é extremamente limitado. Use esta opção apenas em um ambiente de desenvolvimento/teste e com um conjunto limitado de inscrições. As notificações de depuração são enviadas para apenas 10 dispositivos. Há também um limite para o processamento de depuração envia, a 10 por minuto.

### <a name="review-telemetry"></a>Telemetria de revisão

#### <a name="azure-portal"></a>Portal do Azure

No portal, é possível obter uma visão geral rápida de todas as atividades no seu hub de notificação.

1. Na guia **Visão geral**, é possível ver uma exibição agregada de registros, notificações e erros por plataforma.

   ![Painel Visão geral dos Hubs de Notificação][5]

2. Na guia **Monitor**, você pode adicionar várias outras métricas específicas da plataforma para uma análise mais profunda. Você pode olhar especificamente para os erros que são retornados quando o Notification Hubs tenta enviar a notificação para o serviço de notificação push.

   ![Log de atividades do Portal do Azure][6]

3. Comece revisando as **Mensagens de entrada**, **Operações de registro** e **Notificações com êxito**. Em seguida, acesse a guia por plataforma para examinar os erros que são específicos para o serviço de notificação por push.

4. Se as configurações de autenticação para o hub de notificação estiverem incorretas, a mensagem **Erro de autenticação do PNS** será exibida. É uma boa indicação para verificar as credenciais de serviço de notificação push.

#### <a name="programmatic-access"></a>Acesso Programático

Para obter mais informações sobre acesso programático, consulte [Programmatic access](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Vários recursos relacionados à telemetria, como exportar e importar os registros e acesso à telemetria por meio de APIs, estão disponíveis apenas na camada de serviço Standard. Se você tentar usar esses recursos do nível de serviço Gratuito ou Básico, você receberá uma mensagem de exceção se você usar o SDK. Você receberá um erro HTTP 403 (Proibido) se usar os recursos diretamente das APIs REST.
>
> Para usar recursos relacionados à telemetria, primeiro certifique-se no portal Azure que você está usando o nível de serviço Padrão.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Visão geral dos Hubs de Notificação]: notification-hubs-push-notification-overview.md
[Introdução aos Hubs de Notificações do Microsoft Azure]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modelos]: https://msdn.microsoft.com/library/dn530748.aspx
[Visão geral de APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Sobre mensagens de FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Análise aprofundada: Visual Studio 2013 Atualização 2 RC e SDK do Azure 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Anunciando o lançamento do Visual Studio 2013 Atualização 3 e SDK do Azure 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
