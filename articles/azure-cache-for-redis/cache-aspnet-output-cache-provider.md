---
title: Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure
description: Saiba como armazenar ASP.NET Saída de Página usando o Cache do Azure para Redis. O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: af003f1f0422c2351bcdf9b0c0010e38785c0344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530318"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure

O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são usados especificamente para respostas HTTP completas (cache de saída de página). O provedor se conecta ao novo ponto de extensibilidade do provedor de cache de saída que foi apresentado no ASP.NET 4.

Para usar o Provedor de Cache de Saída Redis, primeiro configure seu cache e, em seguida, configure seu aplicativo ASP.NET usando o pacote NuGet do Provedor de Cache de Saída do Redis. Este tópico fornece informações sobre como configurar seu aplicativo para usar o Provedor de Cache de Saída Redis. Para saber mais sobre como criar e configurar uma instância do Cache Redis do Azure, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar a saída da página ASP.NET no cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet de Estado de Sessão do Cache do Azure para Redis, clique em **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes** no menu **Ferramentas**.

Execute o comando a seguir na janela `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Para usar o pacote NuGet do Provedor de Cache de Saída Redis, é necessário ter o pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado. Para obter mais informações sobre o pacote NuGet do Provedor de Cache de Saída Redis, consulte a página do NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName com nome forte, também há a versão do StackExchange.Redis sem nome forte. Se o seu projeto estiver usando a versão StackExchange.Redis não-forte, você deve desinstalá-lo; caso contrário, você experimentará conflitos de nomeação em seu projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote do NuGet baixa e adiciona as referências de assembly necessárias e adiciona a seção a seguir ao seu arquivo web.config. Essa seção contém a configuração necessária para que seu aplicativo ASP.NET use o Provedor de Cache de Saída Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configure os atributos usando os valores da sua folha de cache no Portal do Microsoft Azure e defina os demais valores conforme sua preferência. Para obter instruções sobre como acessar as propriedades do cache, consulte [Definir configurações do Cache Redis do Azure](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atributo | Type | Padrão | Descrição |
| --------- | ---- | ------- | ----------- |
| *Host* | string | "Localhost" | O endereço IP do servidor Redis ou o nome do host |
| *Porta* | número inteiro positivo | 6379 (não-SSL)<br/>6380 (SSL) | Porta do servidor Redis |
| *Accesskey* | string | "" | Senha do servidor Redis quando a autorização redis estiver ativada. O valor é string vazio por padrão, o que significa que o provedor de estado de sessão não usará nenhuma senha ao se conectar ao servidor Redis. **Se o servidor Redis estiver em uma rede acessível ao público como o Azure Redis Cache, certifique-se de habilitar a autorização redis para melhorar a segurança e fornecer uma senha segura.** |
| *Ssl* | booleano | **false** | Se deve conectar-se ao servidor Redis via SSL. Esse valor é **falso** por padrão porque o Redis não suporta SSL fora da caixa. **Se você estiver usando o Cache Azure Redis que suporta SSL fora da caixa, certifique-se de configurá-lo como verdadeiro para melhorar a segurança.**<br/><br/>A porta não SSL é desabilitada por padrão para novos caches. Especifique **a verdade** para que esta configuração use a porta SSL. Para mais informações sobre como habilitar a porta não SSL, confira a seção [Portas de acesso](cache-configure.md#access-ports) do tópico [Como configurar um cache](cache-configure.md). |
| *databaseIdNumber* | número inteiro positivo | 0 | *Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Especifique qual banco de dados Redis usar. |
| *conexãoTimeoutInMilliseconds* | número inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *ConnectTimeout* ao criar StackExchange.Redis.ConnectionMultiplexer. |
| *operaçãoTimeoutInMilliseconds* | número inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *SyncTimeout* ao criar StackExchange.Redis.ConnectionMultiplexer. |
| *string de conexão ConnectionString* (seqüência de conexão Valid StackExchange.Redis) | string | *n/a* | Uma referência de parâmetro a AppSettings ou web.config, ou então uma seqüência de conexão StackExchange.Redis válida. Este atributo pode fornecer valores para *host,* *porta,* *accessKey,* *ssl*e outros atributos StackExchange.Redis. Para ver de perto *a conexãoString,* consulte [Configuração de conexãoString](#setting-connectionstring) na seção [Notas de atributo.](#attribute-notes) |
| *configuraçõesNome de classe*<br/>*configuraçõesNome do método* | string<br/>string | *n/a* | *Esses atributos só podem ser especificados através de web.config ou AppSettings.*<br/><br/>Use esses atributos para fornecer uma seqüência de conexões. *configuraçõesClassName* deve ser um nome de classe qualificado para montagem que contenha o método especificado por *configuraçõesMethodName*.<br/><br/>O método especificado pelas *configuraçõesMethodName* deve ser público, estático e vazio (não tomar quaisquer parâmetros), com um tipo de **string**de retorno . Este método retorna a seqüência de conexão real. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Esses atributos só podem ser especificados através de web.config ou AppSettings.*<br/><br/>Use esses atributos para depurar seu aplicativo fornecendo logs do Session State/Output Cache, juntamente com logs do StackExchange.Redis. *loggingClassName* deve ser um nome de classe qualificado para montagem que contenha o método especificado pelo *loggingMethodName*.<br/><br/>O método especificado pelo *loggingMethodName* deve ser público, estático e vazio (não tomar quaisquer parâmetros), com um tipo de retorno de **System.IO.TextWriter**. |
| *Applicationname* | string | O nome do módulo do processo atual ou "/" | *Somente SessionStateProvider*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>O prefixo de nome do aplicativo para usar no cache Redis. O cliente pode usar o mesmo cache Redis para diferentes propósitos. Para garantir que as teclas da sessão não colidem, ela pode ser prefixada com o nome do aplicativo. |
| *Throwonerror* | booleano | true | *Somente SessionStateProvider*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Se para lançar uma exceção quando ocorre um erro.<br/><br/>Para obter mais informações sobre *throwOnError,* consulte [Notas em *lanceOnError* ](#notes-on-throwonerror) na seção [Notas de atributo.](#attribute-notes) |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | número inteiro positivo | 5.000 | *Somente SessionStateProvider*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Quanto tempo para tentar novamente quando uma operação falha. Se esse valor for menor que *o funcionamentoTimeoutInMilliseconds,* o provedor não tentará novamente.<br/><br/>Para obter mais informações sobre *o retryTimeoutInMilliseconds*, consulte [Notas na *repetiçãoTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) na seção [Notas de atributo.](#attribute-notes) |
| *redisSerializerType* | string | *n/a* | Especifica o nome de tipo qualificado de montagem de uma classe que implementa o Microsoft.Web.Redis. ISerializer e que contém a lógica personalizada para serializar e desserializar os valores. Para obter mais informações, consulte [ *RedisSerializerType* ](#about-redisserializertype) na seção Notas de [atributo.](#attribute-notes) |

## <a name="attribute-notes"></a>Notas de atributo

### <a name="setting-connectionstring"></a>Configuração *de conexãoString*

O valor da *conexãoString* é usado como chave para buscar a seqüência de conexão real de AppSettings, se tal string existir em AppSettings. Se não for encontrado dentro de AppSettings, o valor da *conexãoString* será usado como chave para buscar a seqüência de conexão real da seção Web.config **ConnectionString,** se essa seção existir. Se a seqüência de conexões não existir em AppSettings ou na seção Web.config **ConnectionString,** o valor literal da *conexãoString* será usado como a seqüência de conexão ao criar StackExchange.Redis.ConnectionMultiplexer.

Os exemplos a seguir ilustram como *o connectionString* é usado.

#### <a name="example-1"></a>Exemplo 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Em `web.config`, use acima da tecla como valor de parâmetro em vez de valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Exemplo 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

Em `web.config`, use acima da tecla como valor de parâmetro em vez de valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Exemplo 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Notas no *throwOnError*

Atualmente, se ocorrer um erro durante uma operação de sessão, o provedor de estado de sessão lançará uma exceção. Isso desliga a aplicação.

Esse comportamento foi modificado de forma que suporta as expectativas dos usuários existentes do provedor de estado de sessão de ASP.NET, ao mesmo tempo em que fornece a capacidade de agir em exceções, se desejar. O comportamento padrão ainda abre uma exceção quando ocorre um erro, consistente com outros provedores de estado de sessão de ASP.NET; O código existente deve funcionar da mesma forma que antes.

Se você definir *throwOnError* como **falso,** em vez de lançar uma exceção quando ocorre um erro, ele falhará silenciosamente. Para ver se houve um erro e, se for o caso, descobrir qual foi a exceção, verifique a propriedade estática *Microsoft.Web.Redis.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Notas em *retryTimeoutInMilliseconds*

Isso fornece alguma lógica de repetição para simplificar o caso em que alguma operação de sessão deve tentar novamente a falha por causa de coisas como falha de rede, ao mesmo tempo em que permite que você controle o tempo de reavaliação ou opte por não tentar totalmente.

Se você definir *retryTimeoutInMilliseconds* para um número, por exemplo 2000, então quando uma operação de sessão falhar, ele tentará novamente por 2000 milissegundos antes de tratá-lo como um erro. Então, para que o provedor de estado de sessão aplique essa lógica de repetição, basta configurar o tempo extra. A primeira repetição acontecerá após 20 milissegundos, o que é suficiente na maioria dos casos quando uma falha de rede acontece. Depois disso, ele vai tentar novamente a cada segundo até que se esprema. Logo após o intervalo, ele tentará novamente mais uma vez para garantir que ele não cortará o tempo por (no máximo) um segundo.

Se você não acha que precisa de nova tentativa (por exemplo, quando estiver executando o servidor Redis na mesma máquina que seu aplicativo) ou se você quiser lidar com a lógica de repetição você mesmo, defina *TimeoutInMilliseconds* para 0.

### <a name="about-redisserializertype"></a>Sobre *redisSerializerType*

Por padrão, a serialização para armazenar os valores em Redis é feita em um formato binário fornecido pela classe **BinaryFormatter.** Use *redisSerializerType* para especificar o nome de tipo qualificado de montagem de uma classe que implementa **o Microsoft.Web.Redis.ISerializer** e tem a lógica personalizada para serializar e desserializar os valores. Por exemplo, aqui está uma classe de serializer Json usando JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Supondo que esta classe seja definida em um conjunto com o nome **MyCompanyDll,** você pode definir o parâmetro *redisSerializerType* para usá-la:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Diretiva de cache de saída

Adicione uma diretiva OutputCache a cada página da qual você deseja armazenar a saída em cache.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No exemplo anterior, os dados da página em cache permaneceram no cache por 60 segundos, e outra versão da página foi armazenada em cache para cada combinação de parâmetros. Para obter mais informações sobre [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)a diretiva OutputCache, consulte .

Após a conclusão dessas etapas, seu aplicativo será configurado para usar o Provedor de Cache de Saída Redis.

## <a name="third-party-output-cache-providers"></a>Provedores de cache de saída de terceiros

* [Ncache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Ignição Apache](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Próximas etapas

Consulte [Provedor de Estado de Sessão ASP.NET para o Cache Redis do Azure](cache-aspnet-session-state-provider.md).
