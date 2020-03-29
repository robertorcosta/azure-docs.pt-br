---
title: Usar o controle de acesso baseado em função para StorSimple | Microsoft Docs
description: Descreve como usar o controle de acesso baseado em função do Azure (RBAC) no contexto do StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66159092"
---
# <a name="role-based-access-control-for-storsimple"></a>Controle de acesso baseado em função para StorSimple

Este artigo apresenta uma breve descrição de como o RBAC (Controle de Acesso baseado em função) do Azure pode ser usado para seu dispositivo StorSimple. O RBAC oferece gerenciamento de acesso refinado para o Azure. Use o RBAC para conceder a quantidade certa de acesso para os usuários do StorSimple realizarem seus trabalhos, em vez de acesso irrestrito. Para obter mais informações sobre os conceitos básicos do gerenciamento de acesso no Azure, consulte [Introdução ao controle de acesso baseado em função no portal do Azure](../role-based-access-control/overview.md).

Este artigo se aplica a dispositivos da série StorSimple 8000 executando Atualização 3.0 ou posterior no portal do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>Funções RBAC para StorSimple

O RBAC pode ser atribuído com base em funções. As funções garantem determinados níveis de permissão com base nos recursos disponíveis no ambiente. Há dois tipos de funções que os usuários do StorSimple podem escolher: internas ou personalizadas.

* **Funções internas** – funções internas podem ser de proprietário, colaborador, leitor ou administrador de acesso do usuário. Para obter mais informações, consulte [Funções incorporadas para controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md).

* **Funções personalizadas** – se funções internas não atenderem às suas necessidades, você poderá criar funções personalizadas RBAC para StorSimple. Para criar uma função personalizada RBAC, comece com uma função interna, edite-a e, em seguida, importe-a de volta para o ambiente. O download e o upload da função são gerenciados usando o Azure PowerShell ou a CLI do Azure. Para obter mais informações, consulte [Criar funções personalizadas para controle de acesso baseado em função](../role-based-access-control/custom-roles.md).

Para exibir as diferentes funções disponíveis para um usuário do dispositivo StorSimple no portal do Azure, vá para o serviço do Gerenciador de Dispositivos do StorSimple e, em seguida, para **Controle de acesso (IAM) > Funções**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Crie uma função personalizada para o Administrador de Infraestrutura do StorSimple

No exemplo a seguir, vamos começar com a função interna de **Leitor** que permite aos usuários exibir todos os escopos de recursos, mas não editá-los nem criar novos. Em seguida, estendemos essa função para criar uma nova função personalizada do storSimple Infrastructure admin. Essa função é atribuída aos usuários que podem gerenciar a infra-estrutura para os dispositivos StorSimple.

1. Execute o Windows PowerShell como um administrador.

2. Fazer logon no Azure.

    `Connect-AzAccount`

3. Exporte a função de Leitor como um modelo JSON em seu computador.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Abra o arquivo JSON no Visual Studio. Você vê que uma função RBAC típica consiste em três seções principais, **Actions**, **NotActions** e **AssignableScopes**.

    Na seção **Action**, são listadas todas as operações permitidas para essa função. Cada ação é atribuída de um provedor de recursos. Para um administrador de infraestrutura do StorSimple, use o provedor de recursos `Microsoft.StorSimple`.

    Use o PowerShell para ver todos os provedores de recursos disponíveis e registrados em sua assinatura.

    `Get-AzResourceProvider`

    Você também pode verificar os todos os cmdlets do PowerShell disponíveis para gerenciar os provedores de recursos.

    Nas seções **NotActions**, todas as ações restritas para uma função RBAC determinada são listadas. Neste exemplo, nenhuma ação é restrita.
    
    Em **AssignableScopes**, as IDs de assinatura são listadas. Verifique se a função RBAC contém a ID de assinatura explícita na qual ela é usada. Se a ID de assinatura correta não for especificada, você não terá permissão para importar a função para sua assinatura.

    Edite o arquivo tendo em mente as considerações anteriores.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importe a função RBAC personalizada novamente para o ambiente.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Essa função agora deve aparecer na lista de funções na folha **Controle de acesso**.

![Exibir funções RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Para obter mais informações, consulte [Funções personalizadas](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Saída de exemplo para a criação de função personalizada por meio do PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Adicionar usuários à função personalizada

Conceda acesso de dentro do recurso, do grupo de recursos ou da assinatura que é o escopo da atribuição de função. Ao fornecer acesso, tenha em mente que o acesso concedido no nó pai é herdado pelo filho. Para obter mais informações, confira [Controle de acesso baseado em função](../role-based-access-control/overview.md).

1. Vá para **Controle de acesso (IAM)**. Selecione **+ Adicionar** na folha Controle de acesso.

    ![Adicionar acesso à função RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selecione a função que você deseja atribuir, neste caso, **Administrador de infraestrutura do StorSimple**.

3. Selecione o usuário, o grupo ou o aplicativo ao qual você deseja conceder acesso. Você pode pesquisar o diretório por nomes para exibição, endereços de email e identificadores de objeto.

4. Selecione **Salvar** para criar a atribuição.

    ![Adicionar permissões à função RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Uma notificação de **Adicionando usuário** controla o progresso. Depois que o usuário é adicionado com êxito, a lista de usuários no controle de Acesso é atualizada.

## <a name="view-permissions-for-the-custom-role"></a>Exibir permissões para a função personalizada

Quando essa função está criada, você pode exibir as permissões associadas a ela no portal do Azure.

1. Para visualizar as permissões associadas a essa função, vá para **O controle de acesso (IAM) > Funções > StorSimple Infrastructure Admin**. A lista de usuários nesta função é exibida.

2. Selecione um usuário Administrador de Infraestrutura do StorSimple e clique em **Permissões**.

    ![Exibir permissões para a função de Administrador Infraestrutura do StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. As permissões associadas a essa função são exibidas.

    ![Exibir usuários na função de Administrador de Infraestrutura do StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Próximas etapas

Saiba como [Atribuir funções personalizadas para usuários internos e externos](../role-based-access-control/role-assignments-external-users.md).
