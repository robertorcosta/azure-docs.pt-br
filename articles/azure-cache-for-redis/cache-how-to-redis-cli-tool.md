---
title: Como usar redis-cli com Cache Azure para Redis
description: Aprenda a usar *redis-cli.exe* como uma ferramenta de linha de comando para interagir com um Cache Azure para Redis como cliente.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: bd2da798cae92a7e47bd879b69dd108618463402
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010759"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Como usar a ferramenta de linha de comando Redis com Cache do Azure para Redis

O *redis-cli.exe* é uma ferramenta de linha de comando popular para interagir com um Cache do Azure para Redis como um cliente. Essa ferramenta também está disponível para uso com Cache do Azure para Redis.

A ferramenta está disponível para as plataformas Windows baixando as [Ferramentas de linha de comando Redis para Windows](https://github.com/MSOpenTech/redis/releases/). 

Se você quiser executar a ferramenta de linha de comando em outra [https://redis.io/download](https://redis.io/download)plataforma, baixe Azure Cache for Redis de .

## <a name="gather-cache-access-information"></a>Coletar informações de acesso do cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode coletar as informações necessárias para acessar o cache usando três métodos:

1. Usando a CLI do Azure [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Com o Azure PowerShell usando [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Usando o Portal do Azure.

Nesta seção, você vai recuperar as chaves do Portal do Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Habilitar o acesso para redis-cli.exe

Com o Cache Azure para Redis, apenas a porta TLS (6380) está habilitada por padrão. A `redis-cli.exe` ferramenta de linha de comando não suporta TLS. Você tem duas opções de configuração para usá-lo:

1. [Habilite a porta não-TLS (6379)](cache-configure.md#access-ports) - **Essa configuração não é recomendada** porque, nesta configuração, as chaves de acesso são enviadas via TCP em texto claro. Essa alteração pode comprometer o acesso ao seu cache. O único cenário em que você pode considerar essa configuração é quando você está acessando apenas um cache de teste.

2. Baixar e instalar o [stunnel](https://www.stunnel.org/downloads.html).

    Execute **stunnel GUI Start** para iniciar o servidor.

    Clique com o botão direito no ícone da barra de tarefas para o servidor stunnel e clique em **Mostrar janela de log**.

    No menu stunnel Log Window, clique em **Configuração** > **Editar configuração** para abrir o arquivo de configuração atual.

    Adicione a seguinte entrada para *redis-cli.exe* sob a seção **Definições de serviço**. Insira o nome real do cache em vez de `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Salve e feche o arquivo de configuração. 
  
    No menu stunnel Log Window, clique **em Configuração** > **de recarga**.


## <a name="connect-using-the-redis-command-line-tool"></a>Conecte-se usando a ferramentas de linha de comando do Redis.

Ao usar stunnel, execute *redis-cli.exe* e passe apenas sua *porta* e *chave de acesso* (primário ou secundário) para conectar-se ao cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se você estiver usando um cache de teste com a `redis-cli.exe` porta não-TLS **não segura,** execute e passe o nome do *host,* *porta*e *tecla de acesso* (principal ou secundário) para se conectar ao cache de teste.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o [Console Redis](cache-configure.md#redis-console) para emitir comandos.

