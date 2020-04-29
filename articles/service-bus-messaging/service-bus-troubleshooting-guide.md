---
title: Guia de solução de problemas do barramento de serviço do Azure | Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens do barramento de serviço do Azure e ações sugeridas a serem executadas quando a exceção ocorre.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887769"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guia de solução de problemas do barramento de serviço do Azure
Este artigo fornece dicas de solução de problemas e recomendações para alguns problemas que você pode ver ao usar o barramento de serviço do Azure. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou tempo limite
As etapas a seguir podem ajudá-lo a solucionar problemas de conectividade/certificado/tempo limite para todos os serviços em *. servicebus.windows.net. 

- Navegue até ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Ele ajuda a verificar se você tem problemas de filtragem de IP ou de rede virtual ou de cadeia de certificados (mais comuns ao usar o SDK do Java).

    Um exemplo de mensagem bem-sucedida:
    
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
- Execute o comando a seguir para verificar se alguma porta está bloqueada no firewall. As portas usadas são 443 (HTTPS), 5671 (AMQP) e 9354 (net Messaging/SBMP). Dependendo da biblioteca que você usa, outras portas também são usadas. Aqui está o comando de exemplo que verifica se a porta 5671 está bloqueada. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    No Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Esse comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço. Em seguida, você pode verificar quantos deles foram bem-sucedidos/com falha e também ver a latência de conexão TCP. Você pode baixar a `psping` ferramenta [aqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tnc`, `ping`e assim por diante. 
- Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e a analisarem usando ferramentas como o [Wireshark](https://www.wireshark.org/). Entre em contato com [suporte da Microsoft](https://support.microsoft.com/) se necessário. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemas que podem ocorrer com atualizações/reinicializações de serviço
As atualizações e reinicializações do serviço de back-end podem causar o seguinte impacto em seus aplicativos:

- As solicitações podem estar momentaneamente limitadas.
- Pode haver uma queda nas mensagens/solicitações de entrada.
- O arquivo de log pode conter mensagens de erro.
- Os aplicativos podem ser desconectados do serviço por alguns segundos.

Se o código do aplicativo utilizar o SDK, a política de repetição já estará interna e ativa. O aplicativo será reconectado sem um impacto significativo no aplicativo/fluxo de trabalho.

## <a name="unauthorized-access-send-claims-are-required"></a>Acesso não autorizado: as declarações de envio são necessárias
Você pode ver esse erro ao tentar acessar um tópico do barramento de serviço do Visual Studio em um computador local usando uma identidade gerenciada atribuída pelo usuário com permissões de envio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Para resolver esse erro, instale a biblioteca [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  Para obter mais informações, consulte [autenticação de desenvolvimento local](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Para saber como atribuir permissões a funções, consulte [autenticar uma identidade gerenciada com Azure Active Directory para acessar os recursos do barramento de serviço do Azure](service-bus-managed-service-identity.md).

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Azure Resource Manager exceções](service-bus-resource-manager-exceptions.md). Exceções de lista de ti geradas ao interagir com o barramento de serviço do Azure usando Azure Resource Manager (por meio de modelos ou chamadas diretas).
- [Exceções de mensagens](service-bus-messaging-exceptions.md). Ele fornece uma lista de exceções geradas pelo .NET Framework para o barramento de serviço do Azure. 

