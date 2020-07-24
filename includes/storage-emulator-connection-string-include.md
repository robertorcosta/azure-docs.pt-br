---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070565"
---
O azurite dá suporte a uma única conta fixa e uma chave de autenticação conhecida para autenticação de chave compartilhada. Essa conta e a chave são as únicas credenciais de chave compartilhada permitidas para uso com azurite. Eles são:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação com suporte do azurite é destinada apenas para testar a funcionalidade do seu código de autenticação de cliente. Ela não serve para fins de segurança. Você não pode usar sua conta de armazenamento de produção e a chave com azurite. Você não deve usar a conta de desenvolvimento com dados de produção.
> 
> O azurite dá suporte à conexão somente via HTTP. No entanto, o HTTPS é o protocolo recomendado para acessar os recursos em uma conta de armazenamento de produção do Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Conectar-se à conta do emulador usando um atalho
A maneira mais fácil de se conectar ao azurite de seu aplicativo é configurar uma cadeia de conexão no arquivo de configuração do aplicativo que faz referência ao atalho `UseDevelopmentStorage=true` . Aqui está um exemplo de uma cadeia de conexão para azurite em um arquivo *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Conectar-se à conta do emulador usando o nome de conta e a chave conhecidos
Para criar uma cadeia de conexão que referencia o nome da conta do emulador e a chave, você deve especificar os pontos de extremidade para cada um dos serviços do emulador na cadeia de conexão que você deseja usar. Isso é necessário para que a cadeia de conexão faça referência aos pontos de extremidade do emulador, que são diferentes daqueles para uma conta de armazenamento de produção. Por exemplo, o valor da sua cadeia de conexão terá esta aparência:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Esse valor é idêntico ao atalho mostrado acima, `UseDevelopmentStorage=true`.
