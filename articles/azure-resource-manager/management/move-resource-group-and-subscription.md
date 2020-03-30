---
title: Mova recursos para um novo grupo de assinatura ou recursos
description: Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248847"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou assinatura

Este artigo mostra como mover recursos do Azure para outra assinatura do Azure ou outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST para mover recursos.

O grupo de origem e o grupo de destino ficam bloqueados durante a operação de movimentação. As operações de gravação e exclusão são bloqueadas nos grupos de recursos até que a migração seja concluída. Esse bloqueio significa que você não pode adicionar, atualizar ou excluir recursos nos grupos de recursos. Não significa que os recursos estão congelados. Por exemplo, se você mover um SQL Server e seu banco de dados para um novo grupo de recursos, um aplicativo que usa o banco de dados não terá nenhuma inatividade. Ele ainda poderá ler e gravar no banco de dados. O bloqueio pode durar no máximo quatro horas, mas a maioria dos movimentos se completaem em muito menos tempo.

Mover um recurso só o move para um novo grupo de recursos ou assinatura. Não altera o local do recurso.

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Há algumas etapas importantes a serem executadas antes de mover um recurso. Ao verificar essas condições, é possível evitar erros.

1. Os recursos que você deseja mover devem apoiar a operação de mudança. Para obter uma lista de quais recursos de suporte a movimentação, consulte [O suporte à operação Mover para recursos](move-support-resources.md).

1. Alguns serviços têm limitações ou requisitos específicos ao mover recursos. Se você estiver movendo qualquer um dos seguintes serviços, verifique essa orientação antes de se mover.

   * [Os serviços de aplicativos movem orientações](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services movem orientação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Orientação de movimento de modelo de implantação clássica](./move-limitations/classic-model-move-limitations.md) - Classic Compute, Classic Storage, Classic Virtual Networks e Cloud Services
   * [Orientação de movimentação de rede](./move-limitations/networking-move-limitations.md)
   * [Serviços de recuperação movem orientação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Máquinas Virtuais movem orientação](./move-limitations/virtual-machines-move-limitations.md)

1. As assinaturas de origem e de destino devem estar ativas. Se você tiver problemas para habilitar uma conta que tenha sido desabilitada [crie uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

1. As assinaturas de origem e de destino devem existir no mesmo [locatário do Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md). Para verificar se as duas assinaturas têm a mesma ID de locatário, use o Azure PowerShell ou a CLI do Azure.

   Para o Azure PowerShell, use:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Para a CLI do Azure, use:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Se as IDs de locatário para as assinaturas de origem e de destino não forem iguais, use os métodos a seguir para reconciliá-las:

   * [Transferir a propriedade de uma assinatura do Azure para outra conta](../../billing/billing-subscription-transfer.md)
   * [Como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A assinatura de destino deve estar registrada para que o provedor de recursos do recurso seja movido. Se não estiver, você receberá um erro afirmando que a **assinatura não está registrada para um tipo de recurso**. Você pode ver esse erro ao mover um recurso para uma nova assinatura que nunca tenha sido usada com esse tipo de recurso.

   Para o PowerShell, use os seguintes comandos para obter o status do registro:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Para registrar um provedor de recursos, use:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Para a CLI do Azure, use os seguintes comandos para obter o status do registro:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Para registrar um provedor de recursos, use:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. A conta de movimentação de recursos deve ter pelo menos as seguintes permissões:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** no grupo de recursos de origem.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** no grupo de recursos de destino.

1. Antes de mover os recursos, verifique as cotas de assinatura da assinatura para a qual você está movendo os recursos. Se mover os recursos significa que a assinatura excederá seus limites, será necessário verificar se você pode solicitar um aumento na cota. Para obter uma lista de limites e como solicitar um aumento, consulte [Limites, cotas e restrições em assinaturas e serviços do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **Para uma movimentação entre as assinaturas, o recurso e seus recursos dependentes devem estar localizados no mesmo grupo de recursos e eles devem ser movidos juntos.** Por exemplo, uma VM com discos gerenciados exigiria que a VM e os discos gerenciados fossem movidos juntos, juntamente com outros recursos dependentes.

   Se você está transferindo um recurso para uma nova assinatura, verifique se o recurso tem algum recurso dependente e se eles estão localizados no mesmo grupo de recursos. Se os recursos não estão no mesmo grupo de recursos, verifique se os recursos podem ser consolidados no mesmo grupo de recursos. Nesse caso, traga todos esses recursos para o mesmo grupo de recursos usando uma operação de movimentação entre grupos de recursos.

   Para obter mais informações, consulte [Cenário para mover através de assinaturas](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Cenário para mover entre assinaturas

Mover recursos de uma assinatura para outra é um processo de três etapas:

![cenário de movimento de assinatura cruzada](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Para efeitos de ilustração, temos apenas um recurso dependente.

* Passo 1: Se os recursos dependentes forem distribuídos em diferentes grupos de recursos, primeiro mova-os para um grupo de recursos.
* Passo 2: Mova os recursos e recursos dependentes juntos da assinatura de origem para a assinatura de destino.
* Passo 3: Opcionalmente, redistribua os recursos dependentes para diferentes grupos de recursos dentro da assinatura-alvo. 

## <a name="validate-move"></a>Validar movimentação

A [operação de validação de movimento](/rest/api/resources/resources/validatemoveresources) permite testar seu cenário de movimento sem realmente mover os recursos. Use esta operação para verificar se a mudança será bem sucedida. A validação é automaticamente chamada quando você envia uma solicitação de movimento. Use esta operação somente quando precisar predeterminar os resultados. Para executar essa operação, você precisa de:

* nome do grupo de recursos de origem
* ID do recurso do grupo de recursos de destino
* ID do recurso de cada recurso para mover
* o [token de acesso](/rest/api/azure/#acquire-an-access-token) para sua conta

Envie a seguinte solicitação:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Com um corpo de solicitação:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se a solicitação estiver formatada corretamente, a operação retornará:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

O código de status 202 indica que a solicitação de validação foi aceita, mas ainda não determinou se a operação de movimentação será bem-sucedida. O valor `location` contém um URL que você usa para verificar o status da operação de longa duração.  

Para verificar o status, envie a seguinte solicitação:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Enquanto a operação ainda está em execução, você continua recebendo o código de status 202. Aguarde o número de segundos indicado no valor `retry-after` antes de tentar novamente. Se a operação de movimentação é validado com êxito, você receberá o código de 204 status. Se a validação da movimentação falhar, você receberá uma mensagem de erro, como:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Usar o portal

Para mover recursos, selecione o grupo de recursos que os contém e, em seguida, selecione o botão **Mover**.

![Mover recursos](./media/move-resource-group-and-subscription/select-move.png)

Selecione se você está movendo os recursos para um novo grupo de recursos ou uma nova assinatura.

Selecione os recursos a serem movidos e o grupo de recursos de destino. Confirme que você precisa atualizar scripts para esses recursos e selecione **OK**. Se tiver selecionado o ícone de edição da assinatura na etapa anterior, você também precisará selecionar a assinatura de destino.

![selecionar destino](./media/move-resource-group-and-subscription/select-destination.png)

Em **Notificações**, você verá que a operação de movimentação está em execução.

![mostrar status da movimentação](./media/move-resource-group-and-subscription/show-status.png)

Quando for concluída, você será notificado sobre o resultado.

![mostrar resultado da movimentação](./media/move-resource-group-and-subscription/show-result.png)

Se você tiver um erro, consulte [Solução de problemas movendo recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [Move-AzResource](/powershell/module/az.resources/move-azresource). O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para mover para uma nova assinatura, inclua um valor para o parâmetro `DestinationSubscriptionId`.

Se você tiver um erro, consulte [Solução de problemas movendo recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Usar a CLI do Azure

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move). Forneça as IDs dos recursos a mover. O exemplo a seguir mostra como mover diversos recursos para um novo grupo de recursos. No parâmetro `--ids`, forneça uma lista separada por espaços das IDs do recurso que deseja mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

Se você tiver um erro, consulte [Solução de problemas movendo recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="use-rest-api"></a>Usar a API REST

Para transferir os recursos existentes para outro grupo de recursos ou assinatura, use a operação [de recursos do Move.](/rest/api/resources/Resources/MoveResources)

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

No corpo da solicitação, especifique o grupo de recursos de destino e os recursos para mover.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se você tiver um erro, consulte [Solução de problemas movendo recursos do Azure para novo grupo de recursos ou assinatura](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Pergunta: Minha operação de movimentação de recursos, que geralmente leva alguns minutos, está em execução há quase uma hora. Há alguma coisa errada?**

Mover um recurso é uma operação complexa que tem diferentes fases. Ele pode envolver mais do que apenas o provedor de recursos do recurso que você está tentando mover. Devido às dependências entre provedores de recursos, o Azure Resource Manager permite 4 horas para que a operação seja concluída. Esse período de tempo dá aos provedores de recursos a chance de se recuperar de problemas transitórios. Se a sua solicitação de mudança estiver dentro do período de 4 horas, a operação continua tentando ser concluída e ainda pode ter sucesso. Os grupos de recursos de origem e destino são bloqueados durante este tempo para evitar problemas de consistência.

**Pergunta: Por que meu grupo de recursos está bloqueado por 4 horas durante a movimentação de recursos?**

A janela de 4 horas é o tempo máximo permitido para a movimentação de recursos. Para evitar modificações nos recursos que estão sendo movidos, os grupos de recursos de origem e destino estão bloqueados durante a movimentação dos recursos.

Há duas fases em um pedido de mudança. Na primeira fase, o recurso é movido. Na segunda fase, as notificações são enviadas a outros provedores de recursos que dependem do recurso que está sendo movido. Um grupo de recursos pode ser bloqueado durante toda a janela de 4 horas quando um provedor de recursos falha em qualquer fase. Durante o tempo permitido, o Gerenciador de recursos tenta a etapa falha.

Se um recurso não puder ser movido dentro da janela de 4 horas, o Resource Manager desbloqueará ambos os grupos de recursos. Os recursos que foram movidos com sucesso estão no grupo de recursos de destino. Os recursos que não se movem são deixados no grupo de recursos de origem.

**Pergunta: Quais são as implicações dos grupos de recursos de origem e destino sendo bloqueados durante a movimentação de recursos?**

O bloqueio impede que você exclua um grupo de recursos, crie um novo recurso em um grupo de recursos ou exclua qualquer um dos recursos envolvidos na movimentação.

A imagem a seguir mostra uma mensagem de erro do portal Azure quando um usuário tenta excluir um grupo de recursos que faz parte de um movimento contínuo.

![Mover mensagem de erro tentando excluir](./media/move-resource-group-and-subscription/move-error-delete.png)

**Pergunta: O que significa o código de erro "MissingMoveDependentResources"?**

Ao mover um recurso, seus recursos dependentes devem existir no grupo de recursos de destino ou assinatura, ou ser incluídos na solicitação de movimentação. Você recebe o código de erro MissingMoveDependentResources quando um recurso dependente não atende a esse requisito. A mensagem de erro tem detalhes sobre o recurso dependente que precisa ser incluído na solicitação de mudança.

Por exemplo, mover uma máquina virtual pode exigir a movimentação de sete tipos de recursos com três provedores de recursos diferentes. Esses provedores e tipos de recursos são:

* Microsoft.Compute
   * virtualMachines
   * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Outro exemplo comum envolve a movimentação de uma rede virtual. Você pode ter que mover vários outros recursos associados a essa rede virtual. A solicitação de mudança pode exigir a movimentação de endereços IP públicos, tabelas de rota, gateways de rede virtuais, grupos de segurança de rede e outros.

**Pergunta: Por que não posso mover alguns recursos no Azure?**

Atualmente, nem todos os recursos no azure apoiam o movimento. Para obter uma lista de recursos que suportam o movimento, consulte [O suporte à operação Mover para recursos](move-support-resources.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de quais recursos de suporte a movimentação, consulte [O suporte à operação Mover para recursos](move-support-resources.md).
