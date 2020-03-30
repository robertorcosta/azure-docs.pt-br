---
title: Como gerenciar segredos ao trabalhar com um Azure Dev Space
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Aprenda a usar segredos kubernetes em execução ou tempo de construção ao desenvolver aplicativos com o Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438472"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerenciar segredos ao trabalhar com um Azure Dev Space

Os serviços podem exigir determinadas senhas, cadeias de conexão e outros segredos, como bancos de dados, ou outros serviços seguros do Azure. Ao definir os valores desses segredos nos arquivos de configuração, será possível disponibilizá-los no código como variáveis de ambiente.  Esses arquivos de configuração devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

## <a name="storing-and-using-runtime-secrets"></a>Armazenamento e uso de segredos de tempo de execução

O Azure Dev Spaces oferece duas opções recomendadas e simplificadas para armazenar segredos em gráficos `values.dev.yaml` Helm gerados pela `azds.yaml`ferramenta de cliente Azure Dev Spaces: no arquivo e inline diretamente em . Não é recomendável guardar segredos `values.yaml`em .

> [!NOTE]
> As abordagens a seguir mostram como armazenar e usar segredos para gráficos Helm gerados pela ferramenta do cliente. Se você criar seu próprio gráfico Helm, você pode usar o gráfico Helm diretamente para gerenciar e armazenar segredos.

### <a name="using-valuesdevyaml"></a>Usando valores.dev.yaml

Em um projeto que você já preparou com o `values.dev.yaml` Azure Dev `azds.yaml` Spaces, crie um arquivo na mesma pasta para definir suas chaves e valores secretos. Por exemplo: 

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verifique `azds.yaml` as referências `values.dev.yaml` do `?`arquivo como opcionais usando um . Por exemplo: 

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Se você tem arquivos secretos adicionais, você pode adicioná-los aqui também.

Atualize ou verifique se seu serviço faz referência aos seus segredos como variáveis de ambiente. Por exemplo: 

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute seus serviços atualizados usando `azds up`.

```console
azds up
```
 
Use `kubectl` para verificar se seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Não é recomendável guardar segredos no controle de fontes. Se usar o `values.dev.yaml` Git, adicione ao `.gitignore` arquivo para evitar cometer segredos no controle de origem.

### <a name="using-azdsyaml"></a>Usando azds.yaml

Em um projeto que você já preparou com o Azure Dev Spaces, adicione chaves secretas e `azds.yaml`valor usando *$PLACEHOLDER* sintaxe em *configurações.develop.install.set* in . Por exemplo: 

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> Você pode inserir valores *$PLACEHOLDER* secretos diretamente `azds.yaml`sem usar $PLACEHOLDER sintaxe em . No entanto, essa abordagem não é recomendada, uma vez que `azds.yaml` é armazenada no controle de origem.
     
Crie `.env` um arquivo na `azds.yaml` mesma pasta para definir seus valores *$PLACEHOLDER.* Por exemplo: 

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Não é recomendável guardar segredos no controle de fontes. Se usar o `.env` Git, adicione ao `.gitignore` arquivo para evitar cometer segredos no controle de origem.

Atualize ou verifique se seu serviço faz referência aos seus segredos como variáveis de ambiente. Por exemplo: 

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute seus serviços atualizados usando `azds up`.

```console
azds up
```
 
Use `kubectl` para verificar se seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Usando segredos como argumentos de construção

A seção anterior mostrava como armazenar e usar segredos para usar no tempo de execução do contêiner. Você também pode usar qualquer segredo no tempo de construção de `azds.yaml`contêineres, como uma senha para um NuGet privado, usando .

Em `azds.yaml`, definir os segredos de tempo de compilação `<variable name>: ${secret.<secret name>.<secret key>}` em *configurações.develop.build.args* usando a sintaxe. Por exemplo: 

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

No exemplo acima, *mynugetsecret* é um segredo existente e *pattoken* é uma chave existente.

>[!NOTE]
> Nomes e chaves secretas `.` podem conter o personagem. Use `\` para `.` escapar ao passar segredos como argumentos de construção. Por exemplo, passar um segredo chamado *foo.bar* com `MYTOKEN: ${secret.foo\.bar.token}`a chave do *token*: . Além disso, os segredos podem ser avaliados com prefixo e texto pós-fixado. Por exemplo, `MYURL: eus-${secret.foo\.bar.token}-version1`. Além disso, segredos disponíveis nos espaços dos pais e avós podem ser passados como argumentos de construção.

Em seu Arquivo Docker, use a diretiva *ARG* para consumir o segredo e use essa mesma variável mais tarde no Arquivo Docker. Por exemplo: 

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

```
azds up
```

## <a name="next-steps"></a>Próximas etapas

Com esses métodos, é possível se conectar com segurança a um banco de dados, um Cache do Azure para Redis ou acessar serviços seguros do Azure.
 
