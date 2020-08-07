---
author: baanders
description: incluir arquivo para requisito de função na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832307"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Pré-requisitos: requisitos de permissão

Para poder concluir todas as etapas neste artigo, você precisa ser classificado como um proprietário em sua assinatura do Azure. 

Você pode verificar seu nível de permissão executando este comando no Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Se esse comando retornar um erro dizendo que a CLI **não pode localizar a entidade de serviço ou usuário no banco de dados de grafo**:
>
> Use a ID de *objeto* do usuário em vez de seu email para o restante deste artigo. Isso pode acontecer para usuários em contas pessoais da [Microsoft (MSAS)](https://account.microsoft.com/account). 
>
> Use a [página portal do Azure de Azure Active Directory usuários](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) para selecionar sua conta de usuário e abrir seus detalhes. Copie o *ObjectID*do usuário:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Exibição da página do usuário no portal do Azure realçando o GUID no campo ' ID do objeto '" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Em seguida, repita o comando da lista de atribuição de função usando a *ID de objeto* do usuário no lugar do seu email.

Depois de executar o comando lista de atribuição de função, se você for um proprietário, o `roleDefinitionName` valor na saída será *proprietário*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Janela Cloud Shell mostrando a saída do comando AZ role Assignment List":::

Se você achar que o valor é *colaborador* ou algo diferente do *proprietário*, você pode proceder de uma das seguintes maneiras:
* Entre em contato com o proprietário da assinatura e solicite que o proprietário conclua as etapas neste artigo em seu nome
* Entre em contato com o proprietário da assinatura ou com a função de administrador de acesso do usuário na assinatura e solicite que eles elevem você ao proprietário na assinatura para que você tenha as permissões para continuar. Se isso é apropriado depende de sua organização e sua função dentro dela.