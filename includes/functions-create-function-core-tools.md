---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 21b7226bc82aa8041db6bbd29b4874814e2a47d0
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279533"
---
## <a name="create-a-function"></a>Criar uma função

O comando a seguir cria uma função disparada por HTTP denominada `MyHttpTrigger`.

```command
func new --name MyHttpTrigger --template "HttpTrigger"
```

Quando o comando for executado, você verá algo parecido com a seguinte saída:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
