---
title: Protegendo zonas e registros privados de DNS - Azure DNS
description: Neste caminho de aprendizado, comece a proteger zonas privadas de DNS e conjuntos de registros no Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473058"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Como proteger zonas e registros privados de DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zonas e registros privados de DNS são recursos críticos. Excluir uma região DeDNS ou um único registro de DNS pode resultar em uma paralisação do serviço. É importante que as zonas e registros de DNS estejam protegidos contra alterações não autorizadas ou acidentais.

Este artigo explica como o Azure DNS permite que você proteja suas zonas e registros privados de DNS contra tais alterações.  Aplicamos dois poderosos recursos de valores mobiliários fornecidos pelo Azure Resource Manager: [controle de acesso baseado em função](../role-based-access-control/overview.md) e [bloqueios de recursos](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o usuários, grupos e recursos do Azure. Com o RBAC, você pode conceder o nível de acesso que os usuários precisam. Para obter mais informações sobre como o RBAC ajuda você a gerenciar o acesso, veja [O que é Controle de Acesso Baseado em Função](../role-based-access-control/overview.md).

### <a name="the-private-dns-zone-contributor-role"></a>A função de contribuinte da zona de DNS privada

A função de contribuinte privado da zona de DNS é uma função incorporada para gerenciar recursos privados de DNS. Essa função aplicada a um usuário ou grupo permite que eles gerenciem recursos privados de DNS.

O grupo de recursos *myPrivateDNS* contém cinco zonas para contoso Corporation. A concessão do administrador De DNS, as permissões privadas de contribuinte da zona de DNS para esse grupo de recursos, permite o controle total sobre essas regiões De DNS. Evita conceder permissões desnecessárias. O administrador de DNS não pode criar ou parar máquinas virtuais.

A maneira mais simples de atribuir permissões de RBAC é [por meio do Portal do Azure](../role-based-access-control/role-assignments-portal.md).  

Controle **de acesso aberto (IAM)** para o grupo de recursos, selecione **Adicionar**e selecione a função Contribuinte de zona **de DNS** privada. Selecione os usuários ou grupos necessários para conceder permissões.

![RBAC de nível do grupo de recursos por meio do Portal do Azure](./media/dns-protect-private-zones-recordsets/rbac1.png)

As permissões também podem ser [concedidas usando o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

O comando equivalente também está [disponível por meio da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>RBAC nível de zona privada

É possível aplicar regras de RBAC do Azure a uma assinatura, grupo de recursos ou a um recurso individual. Esse recurso pode ser uma zona DNS individual, ou um conjunto de registros individuais.

Por exemplo, o grupo de recursos *myPrivateDNS* contém a *private.contoso.com* de região e um *customers.private.contoso.com*de subzona . Os registros CNAME são criados para cada conta do cliente. A conta de administrador usada para gerenciar registros CNAME é atribuída permissões para criar registros na *região de customers.private.contoso.com.* A conta só pode gerenciar *customers.private.contoso.com.*

É possível conceder permissões de nível da zona RBAC por meio do Portal do Azure.  Controle **de acesso aberto (IAM)** para a região, selecione **Adicionar**e selecione a função Contribuinte de zona **de DNS** privada. Selecione os usuários ou grupos necessários para conceder permissões.

![RBAC de nível da Zona DNS por meio do Portal do Azure](./media/dns-protect-private-zones-recordsets/rbac2.png)

As permissões também podem ser [concedidas usando o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

O comando equivalente também está [disponível por meio da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>RBAC de nível do conjunto de registros

As permissões são aplicadas no nível de registro definido.  O usuário tem controle para as entradas de que precisa e não pode fazer outras alterações.

As permissões RBAC de nível de registro podem ser configuradas através do portal Azure, usando o botão **Access Control (IAM)** na página do conjunto de registros:

![RBAC de nível do conjunto de registros por meio do Portal do Azure](./media/dns-protect-private-zones-recordsets/rbac3.png)

![RBAC de nível do conjunto de registros por meio do Portal do Azure](./media/dns-protect-private-zones-recordsets/rbac4.png)

As permissões RBAC de nível do conjunto de registros também podem ser [concedidas usando o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

O comando equivalente também está [disponível por meio da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função de contribuinte de zona privada privada incorporada permite o controle total sobre um recurso DNS. É possível construir suas próprias funções personalizadas do Azure para fornecer um controle mais fino.

A conta usada para gerenciar CNAMEs é concedida permissão para gerenciar apenas registros CNAME. A conta não pode modificar registros de outros tipos. A conta não pode fazer operações de nível de zona, como exclusão de região.

O exemplo a seguir mostra uma definição de função personalizada para gerenciar apenas registros CNAME:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade Actions define as seguintes permissões específicas do DNS:

* `Microsoft.Network/privateDnsZones/CNAME/*` concede controle total sobre os registros CNAME
* `Microsoft.Network/privateDNSZones/read`concede permissão para ler regiões privadas de DNS, mas não para modificá-las, permitindo que você veja a região em que o CNAME está sendo criado.

> [!NOTE]
> Usando uma função RBAC personalizada para evitar a exclusão de conjuntos de registros permitindo simultaneamente que eles sejam atualizados não é um controle efetivo. Isso impede que os conjuntos de registro sejam excluídos, mas não impede que sejam modificados.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros vazio. Do ponto de vista de resolução de DNS, isso tem o mesmo efeito de excluir o registro.

As definições de função personalizadas não podem ser definidas atualmente através do portal Azure. Uma função personalizada com base nessa definição de função pode ser criada usando o Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Ela também pode ser criada por meio da CLI do Azure:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

A função pode então ser atribuída da mesma forma que as funções internas, conforme descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerenciar e atribuir funções personalizadas, veja [Funções personalizadas no RBAC do Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recurso

O Azure Resource Manager suporta outro tipo de controle de segurança, a capacidade de bloquear recursos. Os bloqueios de recursos são aplicados ao recurso e são eficazes em todos os usuários e funções. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Existem dois tipos de bloqueio de recursos: **CanNotDelete** e **ReadOnly**. Esses tipos de bloqueio podem ser aplicados em uma região de DNS privada ou a um conjunto de registros individuais.  As seções a seguir descrevem vários cenários comuns e como dar suporte a eles usando bloqueios de recurso.

### <a name="protecting-against-all-changes"></a>Proteção contra todas as alterações

Para evitar que alterações sejam feitas, aplique um bloqueio ReadOnly na região. Esse bloqueio impede que novos conjuntos de discos sejam criados e os conjuntos de registros existentes sejam modificados ou excluídos.

Bloqueios de recurso em nível de zona podem ser criados via Portal do Azure.  Na página da zona DNS, selecione **Bloqueios** e, em seguida, selecione **+Adicionar**:

![Bloqueios de recurso em nível de zona via Portal do Azure](./media/dns-protect-private-zones-recordsets/locks1.png)

Os bloqueios de recursos em nível de zona também podem ser criados através [do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest):

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

O comando equivalente também está [disponível por meio da CLI do Azure](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create):

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Protegendo registros individuais

Para impedir que um conjunto de registros DNS existente sofra modificações, aplique um bloqueio ReadOnly ao conjunto de registros.

> [!NOTE]
> Aplicar um bloqueio CanNotDelete a um conjunto de registros não é um controle efetivo. Ele impede que os conjuntos de registro sejam excluídos, mas não impede que sejam modificados.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros vazio. Do ponto de vista de resolução de DNS, isso tem o mesmo efeito de excluir o registro.

Atualmente, os bloqueios de recurso em nível de conjunto de registros só podem ser configurados pelo uso do Azure PowerShell.  Eles não são suportados no portal Azure ou no Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Proteção contra a exclusão de zona

Quando uma região é excluída no DNS do Azure, todos os conjuntos de registros na região são excluídos.  Esta operação não pode ser desfeita. A exclusão acidental de uma zona crítica tem o potencial para ter um impacto significativo nos negócios.  É importante proteger contra a exclusão acidental da zona.

Aplicar um bloqueio CanNotDelete a uma zona impede que a zona seja excluída. As fechaduras são herdadas por recursos infantis. Um bloqueio impede que qualquer conjunto de registros na região seja excluído. Como descrito na nota acima, é ineficaz, uma vez que os registros ainda podem ser removidos dos conjuntos de registros existentes.

Como alternativa, aplique um bloqueio CanNotDelete a um registro definido na região, como o conjunto de registros SOA. A região não é excluída sem também excluir os conjuntos de registros. Esse bloqueio protege contra a exclusão de região, ao mesmo tempo em que permite que os conjuntos de registros dentro da região sejam modificados livremente. Se for feita uma tentativa de excluir a região, o Azure Resource Manager detectará essa remoção. A remoção também excluiria o conjunto de registros SOA, o Azure Resource Manager bloqueia a chamada porque o SOA está bloqueado.  Nenhum conjunto de registros é excluído.

O seguinte comando do PowerShell cria um bloqueio CanNotDelete no registro SOA da zona especificada:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Outra opção para evitar a exclusão acidental da zona é usando uma função personalizada. Essa função garante que as contas usadas para gerenciar suas regiões não tenham permissões de exclusão de região. 

Quando você precisa excluir uma região, você pode impor uma exclusão em duas etapas:

 - Primeiro, as permissões de exclusão da zona de concessão
 - Em segundo lugar, conceder permissões para excluir a zona.

A função personalizada funciona para todas as zonas acessadas por essas contas. Contas com permissões de exclusão de região, como o proprietário da assinatura, ainda podem excluir acidentalmente uma região.

É possível usar ambas as abordagens - bloqueios de recursos e funções personalizadas - ao mesmo tempo, como uma abordagem de defesa em profundidade para a proteção da zona DNS.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como trabalhar com o RBAC, veja [Introdução ao gerenciamento de acesso no Portal do Azure](../role-based-access-control/overview.md).
* Para obter mais informações sobre trabalho com bloqueios de recurso, confira [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
