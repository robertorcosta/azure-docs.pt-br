---
author: baanders
description: incluir arquivo para informações de início na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 3f19674c0e25ebf5cddc2dfb45580a15d04fad46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095707"
---
>[!NOTE]
>Essas operações devem ser concluídas por um usuário com uma função de *proprietário* na assinatura do Azure. Embora algumas partes possam ser concluídas sem essa permissão elevada, a cooperação de um proprietário será necessária para configurar completamente uma instância utilizável. Veja mais informações sobre isso na seção [*pré-requisitos: permissões necessárias*](#prerequisites-permission-requirements) abaixo.

A configuração completa para uma nova instância de gêmeos digital do Azure consiste em três partes:
1. **Criando a instância**
2. **Configurando as permissões de acesso do usuário**: o usuário do Azure precisa ter a função de *proprietário do Azure digital gêmeos (versão prévia)* na instância para executar atividades de gerenciamento. Nesta etapa, você atribuirá a si mesmo essa função (se você for um proprietário na assinatura do Azure) ou obterá um proprietário em sua assinatura para atribuí-la a você.
3. **Configurando permissões de acesso para aplicativos cliente**: é comum escrever um aplicativo cliente que você usa para interagir com sua instância. Para que o aplicativo cliente acesse o gêmeos digital do Azure, você precisa configurar um registro de *aplicativo* no [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) que o aplicativo cliente usará para autenticar a instância.

Para continuar, você precisará de uma assinatura do Azure. Você pode configurar um gratuitamente [aqui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: requisitos de permissão

Para poder concluir todas as etapas neste artigo, você precisa ser classificado como um proprietário em sua assinatura do Azure. 

Você pode verificar seu nível de permissão executando este comando no Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Se você for um proprietário, o `roleDefinitionName` valor na saída será *proprietário*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Janela Cloud Shell mostrando a saída do comando AZ role Assignment List":::

Se você achar que o valor é *colaborador* ou algo diferente do *proprietário*, você pode entrar em contato com o proprietário da assinatura e prosseguir de uma das seguintes maneiras:
* Solicitar que o proprietário conclua as etapas neste artigo em seu nome
* Solicitar que o proprietário eleve você ao proprietário na assinatura também, para que você tenha as permissões para continuar. Se isso é apropriado depende de sua organização e sua função dentro dela.
