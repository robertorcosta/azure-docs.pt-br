---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582683"
---
O emulador dá suporte a uma única conta fixa e uma chave de autenticação conhecida para autenticação de chave compartilhada. Essa conta e a chave são as únicas credenciais de chave compartilhada permitidas para uso com o emulador. Elas são:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação com suporte pelo emulador destina-se apenas ao teste da funcionalidade do seu código de autenticação de cliente. Ela não serve para fins de segurança. Você não pode usar sua conta de armazenamento de produção e a chave com o emulador. Você não deve usar a conta de desenvolvimento com dados de produção.
> 
> O emulador dá suporte à conexão somente via HTTP. No entanto, o HTTPS é o protocolo recomendado para acessar os recursos em uma conta de armazenamento de produção do Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Conectar-se à conta do emulador usando um atalho
A maneira mais fácil de se conectar ao emulador de seu aplicativo é configurar uma cadeia de conexão no arquivo de configuração do aplicativo que faz referência ao atalho `UseDevelopmentStorage=true` . Aqui está um exemplo de uma cadeia de conexão para o emulador em um arquivo de *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

O é equivalente a especificar totalmente o nome da conta, a chave de conta e os pontos de extremidade para cada um dos serviços de emulador que você deseja usar na cadeia de conexão. Isso é necessário para que a cadeia de conexão faça referência aos pontos de extremidade do emulador, que são diferentes daqueles para uma conta de armazenamento de produção. Por exemplo, o valor da sua cadeia de conexão terá esta aparência:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
