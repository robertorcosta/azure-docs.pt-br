---
title: Segurança
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as várias considerações de segurança para o uso de serviços cognitivas.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131530"
---
# <a name="azure-cognitive-services-security"></a>Segurança de serviços cognitivas do Azure

A segurança deve ser considerada uma prioridade principal ao desenvolver qualquer e todos os aplicativos. Com o início de aplicativos habilitados para inteligência artificial, a segurança é ainda mais importante. Neste artigo, vários aspectos da segurança dos serviços cognitivas do Azure são descritos, como o uso da segurança da camada de transporte, a autenticação e a configuração segura de dados confidenciais.

## <a name="transport-layer-security-tls"></a>Protocolo TLS

Todos os pontos de extremidade de serviços cognitivas expostos por HTTP impõem o TLS 1,2. Com um protocolo de segurança imposto, os consumidores que tentam chamar um ponto de extremidade de serviços cognitivas devem aderir a estas diretrizes:

* O sistema operacional do cliente (SO) precisa dar suporte a TLS 1,2
* A linguagem (e a plataforma) usada para fazer a chamada HTTP precisar especificar o TLS 1,2 como parte da solicitação
  * Dependendo do idioma e da plataforma, a especificação do TLS é feita implicitamente ou explicitamente

Para usuários do .NET, considere as <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">práticas <span class="docon docon-navigate-external x-hidden-focus"> </span>recomendadas de segurança da camada de transporte </a>.

## <a name="authentication"></a>Autenticação

Ao discutir a autenticação, há várias concepções comuns. A autenticação e a autorização geralmente são confusas entre si. A identidade também é um componente importante na segurança. Uma identidade é uma coleção de informações sobre uma <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">entidade <span class="docon docon-navigate-external x-hidden-focus"> </span>de segurança </a>. Os IdP (provedores de identidade) fornecem identidades aos serviços de autenticação. A autenticação é o ato de verificar a identidade de um usuário. Autorização é a especificação de direitos de acesso e privilégios para recursos para uma determinada identidade. Várias ofertas de serviços cognitivas incluem o RBAC (controle de acesso baseado em função). O RBAC poderia ser usado para simplificar parte da cerimônia envolvida com o gerenciamento manual de entidades. Para obter mais detalhes, consulte [controle de acesso baseado em função para recursos do Azure](../role-based-access-control/overview.md).

Para obter mais informações sobre autenticação com chaves de assinatura, tokens de acesso e Azure Active Directory (AAD), consulte <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">autenticar<span class="docon docon-navigate-external x-hidden-focus"></span>solicitações para serviços cognitivas do Azure</a>.

## <a name="environment-variables-and-application-configuration"></a>Variáveis de ambiente e configuração de aplicativo

As variáveis de ambiente são pares de nome-valor, armazenadas em um ambiente específico. Uma alternativa mais segura ao uso de valores codificados para dados confidenciais é usar variáveis de ambiente. Os valores codificados são inseguros e devem ser evitados.

> [!CAUTION]
> Não **use valores** codificados para dados confidenciais, fazendo isso é uma grande vulnerabilidade de segurança.

> [!NOTE]
> Embora as variáveis de ambiente sejam armazenadas em texto sem formatação, elas são isoladas em um ambiente. Se um ambiente for comprometido, também serão as variáveis com o ambiente.

### <a name="set-environment-variable"></a>Definir variável de ambiente

Para definir variáveis de ambiente, use um dos comandos a seguir, `ENVIRONMENT_VARIABLE_KEY` em que o é a `value` chave nomeada e é o valor armazenado na variável de ambiente.

# <a name="command-line"></a>[Linha de comando](#tab/command-line)

Crie e atribua a variável de ambiente persistente, dado o valor.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Em uma nova instância do **prompt de comando**, leia a variável de ambiente.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Crie e atribua a variável de ambiente persistente, dado o valor.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Em uma nova instância do **Windows PowerShell**, leia a variável de ambiente.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Crie e atribua a variável de ambiente persistente, dado o valor.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

Em uma nova instância do **bash**, leia a variável de ambiente.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Depois de definir uma variável de ambiente, reinicie o IDE (ambiente de desenvolvimento integrado) para garantir que as variáveis de ambiente adicionadas recentemente estejam disponíveis.

### <a name="get-environment-variable"></a>Obter variável de ambiente

Para obter uma variável de ambiente, ele deve ser lido na memória. Dependendo do idioma que você estiver usando, considere os trechos de código a seguir. Esses trechos de código demonstram como obter a variável de `ENVIRONMENT_VARIABLE_KEY` ambiente dada ao e atribuir a `value`uma variável chamada.

# <a name="c"></a>[C#](#tab/csharp)

Para obter mais informações, <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>consulte.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C](#tab/cpp)

Para obter mais informações, <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>consulte.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Para obter mais informações, <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>consulte.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Para obter mais informações, <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>consulte.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Para obter mais informações, <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>consulte.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Para obter mais informações, <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>consulte.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Próximas etapas

* Explore os vários [Serviços cognitivas](welcome.md)
* Saiba mais sobre [redes virtuais de serviços cognitivas](cognitive-services-virtual-networks.md)
