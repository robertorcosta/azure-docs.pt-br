---
title: Guia de solução de problemas para ônibus de serviço azure | Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens do Azure Service Bus e sugestões de ações a serem tomadas quando a exceção ocorrer.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887769"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guia de solução de problemas para ônibus de serviço azure
Este artigo fornece dicas e recomendações de solução de problemas para alguns problemas que você pode ver ao usar o Ônibus de Serviço Do Azure. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou tempo não
As etapas a seguir podem ajudá-lo a solucionar problemas de conectividade/certificado/tempo demora para todos os serviços em *.servicebus.windows.net. 

- Navegue para ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Ele ajuda a verificar se você tem filtragem ip ou problemas de rede virtual ou cadeia de certificados (mais comum ao usar java SDK).

    Um exemplo de mensagem de sucesso:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Um exemplo de mensagem de erro de falha:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Execute o seguinte comando para verificar se alguma porta está bloqueada no firewall. As portas utilizadas são 443 (HTTPS), 5671 (AMQP) e 9354 (Net Messaging/SBMP). Dependendo da biblioteca que você usa, outras portas também são usadas. Aqui está o comando de amostra que verifica se a porta 5671 está bloqueada. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    No Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas de conectividade intermitente, execute o seguinte comando para verificar se há pacotes descartados. Este comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço. Em seguida, você pode verificar quantos deles tiveram sucesso/falharam e também ver a latência de conexão TCP. Você pode `psping` baixar a ferramenta [daqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Você pode usar comandos equivalentes se estiver `tnc` `ping`usando outras ferramentas como , e assim por diante. 
- Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e analisá-lo usando ferramentas como [wireshark](https://www.wireshark.org/). Entre em contato com [o suporte da Microsoft,](https://support.microsoft.com/) se necessário. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemas que podem ocorrer com atualizações/reinicializações de serviço
Atualizações e reinicializações de serviço sinuosas podem causar o seguinte impacto em seus aplicativos:

- Os pedidos podem ser momentaneamente estrangulados.
- Pode haver uma queda nas mensagens/solicitações recebidas.
- O arquivo de registro pode conter mensagens de erro.
- Os aplicativos podem ser desligados do serviço por alguns segundos.

Se o código do aplicativo utilizar o SDK, a política de repetição já está incorporada e ativa. O aplicativo se reconectará sem impacto significativo no fluxo de aplicação/trabalho.

## <a name="unauthorized-access-send-claims-are-required"></a>Acesso não autorizado: Enviar reclamações são necessárias
Você pode ver esse erro ao tentar acessar um tópico de Bus de Serviço do Visual Studio em um computador local usando uma identidade gerenciada atribuída pelo usuário com permissões de envio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Para resolver esse erro, instale a biblioteca [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Para obter mais informações, consulte [autenticação de desenvolvimento local](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Para saber como atribuir permissões às funções, consulte [Authenticate uma identidade gerenciada com o Azure Active Directory para acessar os recursos do Azure Service Bus](service-bus-managed-service-identity.md).

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Exceções do Azure Resource Manager](service-bus-resource-manager-exceptions.md). Ele lista exceções geradas ao interagir com o Azure Service Bus usando o Azure Resource Manager (via modelos ou chamadas diretas).
- [Exceções de mensagens](service-bus-messaging-exceptions.md). Ele fornece uma lista de exceções geradas pelo .NET Framework for Azure Service Bus. 

