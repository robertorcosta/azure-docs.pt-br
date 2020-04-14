---
title: Remova o TLS 1.0 e o 1.1 do uso com o Cache Azure para Redis
description: Saiba como remover o TLS 1.0 e o 1.1 do seu aplicativo ao se comunicar com o Cache Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 809fbe85a9783777d5dbef86357bd5a386bd6f81
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261206"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Remova o TLS 1.0 e o 1.1 do uso com o Cache Azure para Redis

Há um empurrão em todo o setor para o uso exclusivo da versão 1.2 (Transport Layer Security) (Transport Layer Security) 1.2 ou posterior. As versões TLS 1.0 e 1.1 são conhecidas por serem suscetíveis a ataques como BEAST e POODLE, e ter outras fraquezas de Vulnerabilidades e Exposições Comuns (CVE). Eles também não suportam os métodos modernos de criptografia e suítes de cifras recomendadas pelos padrões de conformidade da Indústria de Cartões de Pagamento (PCI). Este [blog de segurança TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explica algumas dessas vulnerabilidades com mais detalhes.

Como parte deste esforço, faremos as seguintes alterações no Cache do Azure para Redis:

* **Fase 1:** Configuraremos a versão TLS mínima padrão para ser 1.2 para instâncias de cache recém-criadas. (Este costumava ser TLS 1.0.) As instâncias de cache existentes não serão atualizadas neste momento. Você poderá alterar [a versão TLS mínima](cache-configure.md#access-ports) de volta para 1.0 ou 1.1 para compatibilidade retrógrada, se necessário. Essa alteração pode ser feita através do portal Azure ou de outras APIs de gerenciamento.
* **Fase 2:** Vamos parar de suportar as versões TLS 1.0 e 1.1. Após essa alteração, seu aplicativo será obrigado a usar o TLS 1.2 ou posterior para se comunicar com seu cache.

Além disso, como parte desta mudança, estaremos removendo o suporte para suítes cypher mais antigas e inseguras.  Nossas suítes cypher suportadas serão restritas ao seguinte quando o cache for configurado com uma versão TLS mínima de 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Este artigo fornece orientações gerais sobre como detectar dependências dessas versões TLS anteriores e removê-las de seu aplicativo.

As datas em que essas alterações entrarem em vigor são:

| Nuvem               | Data de início da fase 1 | Data de início da fase 2      |
|---------------------|--------------------|-------------------------|
| Azure (global)      |  13 de janeiro de 2020  | 11 de maio de 2020 (prorrogado) |
| Azure Government    |  13 de março de 2020    | 11 de maio de 2020            |
| Azure Alemanha       |  13 de março de 2020    | 11 de maio de 2020            |
| Azure China         |  13 de março de 2020    | 11 de maio de 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Verifique se sua aplicação já está em conformidade

A maneira mais fácil de descobrir se seu aplicativo funcionará com o TLS 1.2 é definir o valor mínimo da **versão TLS** para TLS 1.2 em um teste ou cache de estágio que ele usa. A configuração **da versão TLS mínima** está nas [configurações avançadas](cache-configure.md#advanced-settings) da instância de cache no portal Azure. Se o aplicativo continuar funcionando como esperado após essa mudança, provavelmente será compatível. Você pode precisar configurar algumas bibliotecas de clientes Redis usadas pelo seu aplicativo especificamente para habilitar o TLS 1.2, para que eles possam se conectar ao Cache do Azure para Redis sobre esse protocolo de segurança.

## <a name="configure-your-application-to-use-tls-12"></a>Configure seu aplicativo para usar o TLS 1.2

A maioria dos aplicativos usa bibliotecas de clientes Redis para lidar com a comunicação com seus caches. Aqui estão instruções para configurar algumas das bibliotecas de clientes populares, em várias linguagens e frameworks de programação, para usar o TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Os clientes Redis .NET usam a versão TLS mais antiga por padrão no .NET Framework 4.5.2 ou anterior, e usam a versão TLS mais recente no .NET Framework 4.6 ou posterior. Se você estiver usando uma versão mais antiga do .NET Framework, você pode ativar o TLS 1.2 manualmente:

* **StackExchange.Redis:** Definir `ssl=true` `sslprotocols=tls12` e na seqüência de conexão.
* **ServiceStack.Redis:** Siga as instruções [serviceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) e requer ServiceStack.Redis v5.6 no mínimo.

### <a name="net-core"></a>.NET Core

Os clientes Redis .NET Core usam a versão TLS mais recente por padrão.

### <a name="java"></a>Java

Os clientes Redis Java usam O TLS 1.0 na versão Java 6 ou anterior. Jedis, Alface e Redisson não podem se conectar ao Cache Azure para Redis se o TLS 1.0 estiver desativado no cache. Atualize sua estrutura Java para usar novas versões TLS.

Para Java 7, os clientes Redis não usam O TLS 1.2 por padrão, mas podem ser configurados para ele. Jedis permite que você especifique as configurações TLS subjacentes com o seguinte trecho de código:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Os clientes Alface e Redisson ainda não suportam especificar a versão TLS, então eles quebrarão se o cache aceitar apenas conexões TLS 1.2. Correções para esses clientes estão sendo revisadas, então verifique com esses pacotes se há uma versão atualizada com este suporte.

No Java 8, o TLS 1.2 é usado por padrão e não deve exigir atualizações para a configuração do cliente na maioria dos casos. Por segurança, teste sua aplicação.

### <a name="nodejs"></a>Node.js

Node Redis e IORedis usam TLS 1.2 por padrão.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versões anteriores ao PHP 7: O Predis suporta apenas o TLS 1.0. Essas versões não funcionam com o TLS 1.2; você deve atualizar para usar o TLS 1.2.
 
* PHP 7.0 a PHP 7.2.1: O Predis usa apenas O TLS 1.0 ou 1.1 por padrão. Você pode usar a seguinte solução para usar o TLS 1.2. Especifique o TLS 1.2 ao criar a instância do cliente:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 e versões posteriores: Predis usa a versão TLS mais recente.

#### <a name="phpredis"></a>PhpRedis

PhpRedis não suporta TLS em nenhuma versão PHP.

### <a name="python"></a>Python

Redis-py usa TLS 1.2 por padrão.

### <a name="go"></a>GO

O Redigo usa o TLS 1.2 por padrão.

## <a name="additional-information"></a>Informações adicionais

- [Como configurar o Cache do Azure para Redis](cache-configure.md)
