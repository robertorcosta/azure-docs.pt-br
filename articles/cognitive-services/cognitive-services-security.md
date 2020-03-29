---
title: Segurança
titleSuffix: Azure Cognitive Services
description: Conheça as várias considerações de segurança para o uso de Serviços Cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131530"
---
# <a name="azure-cognitive-services-security"></a>Segurança dos Serviços Cognitivos do Azure

A segurança deve ser considerada uma prioridade máxima no desenvolvimento de toda e qualquer aplicação. Com o início das aplicações habilitadas para inteligência artificial, a segurança é ainda mais importante. Neste artigo, vários aspectos da segurança do Azure Cognitive Services são descritos, como o uso da segurança da camada de transporte, autenticação e configuração segura de dados confidenciais.

## <a name="transport-layer-security-tls"></a>Protocolo TLS

Todos os pontos finais dos Serviços Cognitivos expostos sobre http aplicam o TLS 1.2. Com um protocolo de segurança reforçado, os consumidores que tentam chamar um ponto final dos Serviços Cognitivos devem seguir essas diretrizes:

* O sistema operacional cliente (OS) precisa suportar o TLS 1.2
* O idioma (e a plataforma) usados para fazer a chamada HTTP precisam especificar o TLS 1.2 como parte da solicitação
  * Dependendo do idioma e da plataforma, especificar o TLS é feito de forma implícita ou explícita

Para usuários .NET, considere as <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">práticas <span class="docon docon-navigate-external x-hidden-focus"> </span>recomendadas de segurança de camada de transporte </a>.

## <a name="authentication"></a>Autenticação

Quando se discute autenticação, há vários equívocos comuns. Autenticação e autorização são muitas vezes confundidas umas com as outras. A identidade também é um componente importante na segurança. Uma identidade é uma coleção de informações sobre um <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">diretor. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> Os provedores de identidade (IdP) fornecem identidades para serviços de autenticação. Autenticação é o ato de verificar a identidade de um usuário. Autorização é a especificação de direitos de acesso e privilégios aos recursos para uma determinada identidade. Várias das ofertas de Serviços Cognitivos incluem o Controle de Acesso Baseado em Papéis (RBAC). O RBAC poderia ser usado para simplificar parte da cerimônia envolvida com o gerenciamento manual dos diretores. Para obter mais detalhes, consulte [o controle de acesso baseado em função para os recursos do Azure](../role-based-access-control/overview.md).

Para obter mais informações sobre autenticação com chaves de assinatura, tokens de acesso e AAD (AAD) de acesso, consulte <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">solicitações autenticadas para<span class="docon docon-navigate-external x-hidden-focus"></span>os Serviços Cognitivos do Azure</a>.

## <a name="environment-variables-and-application-configuration"></a>Variáveis de ambiente e configuração de aplicativos

As variáveis de ambiente são pares de valor de nome, armazenados em um ambiente específico. Uma alternativa mais segura para usar valores codificados para dados confidenciais é usar variáveis de ambiente. Os valores codificados são inseguros e devem ser evitados.

> [!CAUTION]
> **Não** use valores codificados para dados confidenciais, fazendo isso é uma grande vulnerabilidade de segurança.

> [!NOTE]
> Enquanto as variáveis de ambiente são armazenadas em texto simples, elas são isoladas a um ambiente. Se um ambiente está comprometido, também são as variáveis com o ambiente.

### <a name="set-environment-variable"></a>Definir variável de ambiente

Para definir variáveis de ambiente, use um `ENVIRONMENT_VARIABLE_KEY` dos seguintes `value` comandos - onde o é a chave nomeada e é o valor armazenado na variável ambiente.

# <a name="command-line"></a>[Linha de Comando](#tab/command-line)

Criar e atribuir variável de ambiente persistente, dado o valor.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Em uma nova instância do **Prompt de comando,** leia a variável ambiente.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Criar e atribuir variável de ambiente persistente, dado o valor.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Em uma nova instância do **Windows PowerShell,** leia a variável ambiente.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Criar e atribuir variável de ambiente persistente, dado o valor.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

Em uma nova instância do **Bash,** leia a variável ambiente.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Depois de definir uma variável de ambiente, reinicie seu ambiente de desenvolvimento integrado (IDE) para garantir que as variáveis de ambiente recém-adicionadas estejam disponíveis.

### <a name="get-environment-variable"></a>Obter variável de ambiente

Para obter uma variável de ambiente, ele deve ser lido na memória. Dependendo do idioma que você está usando, considere os seguintes trechos de código. Esses trechos de código demonstram como `ENVIRONMENT_VARIABLE_KEY` obter a variável ambiente `value`dada a atribuição e atribuir a uma variável chamada .

# <a name="c"></a>[C #](#tab/csharp)

Para obter mais <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

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

# <a name="c"></a>[C++](#tab/cpp)

Para obter mais <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

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

Para obter mais <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

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

Para obter mais <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Para obter mais <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Para obter mais <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Próximas etapas

* Explore os vários [Serviços Cognitivos](welcome.md)
* Saiba mais sobre [redes virtuais de serviços cognitivos](cognitive-services-virtual-networks.md)
