---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a050ce62f745591608249b41ba56992d8fd35204
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935892"
---
## <a name="register-extensions"></a>Extensões de registro

Com exceção dos gatilhos HTTP e Timer, as associações de funções no tempo de execução versão 2. x e superiores são implementadas como pacotes de extensão. Na versão 2. x e além do tempo de execução de Azure Functions, você precisa registrar explicitamente as extensões para os tipos de associação usados em suas funções. As exceções a isso são as associações HTTP e os gatilhos de temporizador que não exigem extensões.

Você pode optar por instalar as extensões de associação individualmente ou pode adicionar uma referência de pacote de extensão ao arquivo de projeto host. JSON. Os pacotes de extensão eliminam a possibilidade de haver problemas de compatibilidade de pacote ao usar vários tipos de associação. É a abordagem recomendada para registrar extensões de associação. Os pacotes de extensão também eliminam a necessidade de instalar o SDK do .NET Core 2. x. 

### <a name="extension-bundles"></a>Pacotes de extensão

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Para saber mais, consulte [registrar Azure Functions extensões de associação](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Você deve adicionar pacotes de extensão ao host. JSON antes de adicionar associações ao arquivo functions. JSON.

### <a name="register-individual-extensions"></a>Registrar extensões individuais

Se precisar instalar extensões que não estejam em um pacote, você poderá registrar manualmente pacotes de extensão individuais para associações específicas. 

> [!NOTE]
> Para registrar manualmente as extensões usando `func extensions install`, você deve ter o SDK do .NET Core 2. x instalado.

Depois de atualizar o arquivo *function.json* para incluir todas as associações que a função precisa, execute o comando a seguir na pasta do projeto.

```bash
func extensions install
```

O comando lê o arquivo *function.json* para saber quais são os pacotes necessários, instala-os e recria o projeto de extensão. Ele adiciona as novas associações na versão atual, mas não atualiza as associações existentes. Use a opção `--force` para atualizar as associações existentes para a versão mais recente nas novas instalações.