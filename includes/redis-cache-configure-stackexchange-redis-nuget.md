---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68286221"
---
Os aplicativos .NET podem usar o cliente de cache **StackExchange.Redis** , que pode ser configurado no Visual Studio usando um pacote NuGet que simplifica a configuração dos aplicativos do cliente de cache. 

> [!NOTE]
> Para obter mais informações, consulte a página do GitHub [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) e a [documentação do cliente de cache StackExchange.Azure](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet do StackExchange.Redis, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Gerenciar Pacotes NuGet**. 

![Gerenciar pacotes NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Digite **StackExchange.Redis** ou **StackExchange.Redis.StrongName** na caixa de texto de pesquisa, selecione a versão desejada nos resultados e clique em **Instalar**.

> [!NOTE]
> Se você preferir usar uma versão de nome forte da biblioteca de cliente **StackExchange.Redis**, escolha **StackExchange.Redis.StrongName**; caso contrário, escolha **StackExchange.Redis**.
>
>

![Pacote NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o Cache do Azure para Redis com o cliente de Cache StackExchange.Azure para Redis.

> [!NOTE]
> Se você já tiver configurado seu projeto para usar o StackExchange.Redis, poderá verificar atualizações ao pacote por meio do **Gerenciador de Pacotes NuGet**. Para verificar e instalar as versões atualizadas do pacote NuGet StackExchange.Redis, clique em **Atualizações** na janela **Gerenciador de Pacotes NuGet**. Se uma atualização ao pacote NuGet StackExchange.Redis estiver disponível, você poderá atualizar seu projeto para usar a versão atualizada.
>
>

Você também pode instalar o pacote do NuGet StackExchange.Redis clicando no **Gerenciador de Pacotes NuGet**, **Console do Gerenciador de Pacotes** no menu **Ferramentas** e executando o seguinte comando na janela **Console do Gerenciador de Pacotes**.

```
Install-Package StackExchange.Redis
```
