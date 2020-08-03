---
author: baanders
description: incluir arquivo para requisito de função na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407398"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: requisitos de permissão

Para poder concluir todas as etapas neste artigo, você precisa ser classificado como um proprietário em sua assinatura do Azure. 

Você pode verificar seu nível de permissão executando este comando no Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Se você for um proprietário, o `roleDefinitionName` valor na saída será *proprietário*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Janela Cloud Shell mostrando a saída do comando AZ role Assignment List":::

Se você achar que o valor é *colaborador* ou algo diferente do *proprietário*, você pode proceder de uma das seguintes maneiras:
* Entre em contato com o proprietário da assinatura e solicite que o proprietário conclua as etapas neste artigo em seu nome
* Entre em contato com o proprietário da assinatura ou com a função de administrador de acesso do usuário na assinatura e solicite que eles elevem você ao proprietário na assinatura para que você tenha as permissões para continuar. Se isso é apropriado depende de sua organização e sua função dentro dela.