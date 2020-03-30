---
title: incluir arquivo
description: incluir arquivo
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564773"
---
## <a name="register-extensions"></a>Extensões de registro

Com exceção dos gatilhos HTTP e temporizador, as vinculações de funções na versão 2.x em tempo de execução e superior são implementadas como pacotes de extensão. Na versão 2.x e além do tempo de execução das funções do Azure, você tem que registrar explicitamente as extensões para os tipos de vinculação usados em suas funções. As exceções são as ligações HTTP e os gatilhos do temporizador, que não exigem extensões.

Você pode optar por instalar extensões de vinculação individualmente ou adicionar uma referência de pacote de extensão ao arquivo do projeto host.json. Os pacotes de extensão eliminam a chance de ter problemas de compatibilidade de pacotes ao usar vários tipos de vinculação. É a abordagem recomendada para o registro de extensões vinculantes. Os pacotes de extensão também removem a necessidade de instalação do .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Pacotes de extensão

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Para saber mais, consulte [Registre as extensões de vinculação funções do Azure](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Você deve adicionar pacotes de extensão ao host.json antes de adicionar vinculações ao arquivo function.json.

### <a name="register-individual-extensions"></a>Registre extensões individuais

Se você precisar instalar extensões que não estão em um pacote, você pode registrar manualmente pacotes de extensão individuais para vinculações específicas. 

> [!NOTE]
> Para registrar manualmente as `func extensions install`extensões usando, você deve ter o .NET Core 2.x SDK instalado.

Depois de atualizar o arquivo *function.json* para incluir todas as associações que a função precisa, execute o comando a seguir na pasta do projeto.

```bash
func extensions install
```

O comando lê o arquivo *function.json* para saber quais são os pacotes necessários, instala-os e recria o projeto de extensão. Ele adiciona as novas associações na versão atual, mas não atualiza as associações existentes. Use a opção `--force` para atualizar as associações existentes para a versão mais recente nas novas instalações.
