---
title: Operações de Gerenciamento
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a duração das operações de gerenciamento do Azure SQL Instância Gerenciada e as práticas recomendadas.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530910"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Visão geral das operações de gerenciamento de Instância Gerenciada do SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>O que são operações de gerenciamento?
O Azure SQL Instância Gerenciada fornece operações de gerenciamento que você pode usar para implantar automaticamente novas instâncias gerenciadas, atualizar propriedades de instância e excluir instâncias quando não for mais necessária.

Para dar suporte a [implantações em redes virtuais do Azure](../../virtual-network/virtual-network-for-azure-services.md) e fornecer isolamento e segurança para clientes, o SQL instância gerenciada se baseia em [clusters virtuais](connectivity-architecture-overview.md#high-level-connectivity-architecture). O cluster virtual representa um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede da rede virtual do cliente. Essencialmente, cada implantação de instância gerenciada em uma sub-rede vazia resulta em um novo build-out de cluster virtual.

As operações subsequentes em instâncias gerenciadas implantadas também podem ter efeitos sobre o cluster virtual subjacente. Essas operações afetam a duração das operações de gerenciamento, pois a implantação de máquinas virtuais adicionais vem com uma sobrecarga que precisa ser considerada quando você planeja novas implantações ou atualizações em instâncias gerenciadas existentes.

Todas as operações de gerenciamento podem ser categorizadas da seguinte maneira:

- Implantação de instância (nova criação de instância).
- Atualização de instância (alterando Propriedades de instância, como vCores ou armazenamento reservado).
- Exclusão da instância.

## <a name="management-operations-duration"></a>Duração das operações de gerenciamento
Normalmente, as operações em clusters virtuais são mais longas. A duração das operações em clusters virtuais varia – abaixo estão os valores que você normalmente pode esperar, com base em dados de telemetria de serviço existentes:

- **Criação de cluster virtual**: a criação é uma etapa síncrona em operações de gerenciamento de instância. **90% das operações são concluídas em 4 horas**.
- **Redimensionamento do cluster virtual (expansão ou redução)**: a expansão é uma etapa síncrona, enquanto a redução é executada de forma assíncrona (sem afetar a duração das operações de gerenciamento de instâncias). **90% das expansões de cluster são concluídas em menos de 2,5 horas**.
- **Exclusão de cluster virtual**: a exclusão é uma etapa assíncrona, mas também pode ser [iniciada manualmente](virtual-cluster-delete.md) em um cluster virtual vazio; nesse caso, ela é executada de forma síncrona. **90% das exclusões do cluster virtual são concluídas em 1,5 horas**.

Além disso, o gerenciamento de instâncias também pode incluir uma das operações em bancos de dados hospedados, o que resulta em durações mais longas:

- **Anexando arquivos de banco de dados do armazenamento do Azure**: uma etapa síncrona, como computação (vCores) ou expansão de armazenamento na camada de serviço uso geral. **90% dessas operações são concluídas em 5 minutos**.
- **Always on a propagação do grupo de disponibilidade**: uma etapa síncrona, como computação (vCores) ou dimensionamento de armazenamento na camada de serviço de comercialmente crítico, bem como a alteração da camada de serviço de Uso Geral para comercialmente crítico (ou vice-versa). A duração dessa operação é proporcional ao tamanho total do banco de dados, bem como à atividade atual do banco de dados (número de transações ativas). A atividade do banco de dados ao atualizar uma instância pode introduzir uma variação significativa na duração total. **90% dessas operações são executadas em 220 GB/hora ou superior**.

A tabela a seguir resume as operações e as durações gerais típicas:

|Categoria  |Operação  |Segmento de execução longa  |Duração estimada  |
|---------|---------|---------|---------|
|**Implantação** |Primeira instância em uma sub-rede vazia|Criação de cluster virtual|90% das operações são concluídas em 4 horas.|
|Implantação |Primeira instância de outra geração de hardware em uma sub-rede não vazia (por exemplo, primeira instância de Gen 5 em uma sub-rede com instâncias de Gen 4)|Criação de cluster virtual *|90% das operações são concluídas em 4 horas.|
|Implantação |Primeira instância de criação de 4 vCores em uma sub-rede vazia ou não vazia|Criação de cluster virtual * *|90% das operações são concluídas em 4 horas.|
|Implantação |Criação de instância subsequente dentro da sub-rede não vazia (2ª, 3ª, etc. instância)|Redimensionamento de cluster virtual|90% das operações são concluídas em 2,5 horas.|
|**Atualização** |Alteração da propriedade de instância (senha de administrador, logon do Azure AD, Benefício Híbrido do Azure Flag)|N/D|Até 1 minuto.|
|Atualização |Expansão/redução do armazenamento de instância (Uso Geral camada de serviço)|Anexando arquivos de banco de dados|90% das operações são concluídas em 5 minutos.|
|Atualização |Expansão/redução do armazenamento de instância (Comercialmente Crítico camada de serviço)|-Redimensionamento de cluster virtual<br>-Always On propagação do grupo de disponibilidade|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora).|
|Atualização |Expansão e redução da vCores (computação de instância) (Uso Geral)|-Redimensionamento de cluster virtual<br>-Anexando arquivos de banco de dados|90% das operações são concluídas em 2,5 horas.|
|Atualização |Expansão e redução da vCores (computação de instância) (Comercialmente Crítico)|-Redimensionamento de cluster virtual<br>-Always On propagação do grupo de disponibilidade|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora).|
|Atualização |Alteração da camada de serviço de instância (Uso Geral para Comercialmente Crítico e vice-versa)|-Redimensionamento de cluster virtual<br>-Always On propagação do grupo de disponibilidade|90% das operações são concluídas em 2,5 horas + tempo para propagar todos os bancos de dados (220 GB/hora).|
|**Exclusão**|Exclusão da instância|Backup da parte final do log para todos os bancos de dados|90% de operações concluídas em até 1 minuto.<br>Observação: se a última instância na sub-rede for excluída, esta operação agendará a exclusão do cluster virtual após 12 horas. * *|
|Exclusão|Exclusão de cluster virtual (como operação iniciada pelo usuário)|Exclusão do cluster virtual|90% das operações são concluídas em até 1,5 horas.|

\*O cluster virtual é criado por geração de hardware.

\*\*12 horas é a configuração atual, mas isso pode mudar no futuro, portanto, não faça uma dependência difícil. Se você precisar excluir um cluster virtual anteriormente (para liberar a sub-rede, por exemplo), consulte [excluir uma sub-rede depois de excluir uma instância gerenciada](virtual-cluster-delete.md).

## <a name="instance-availability-during-management-operations"></a>Disponibilidade da instância durante operações de gerenciamento

O SQL Instância Gerenciada **está disponível durante as operações de atualização**, exceto um breve tempo de inatividade causado pelo failover que ocorre no final da atualização. Normalmente, ele dura até 10 segundos mesmo no caso de transações de longa execução interrompidas, graças à [recuperação acelerada do banco de dados](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Não é recomendável dimensionar a computação ou o armazenamento do Azure SQL Instância Gerenciada ou alterar a camada de serviço ao mesmo tempo com as transações de longa execução (importação de dados, trabalhos de processamento de dados, recompilação de índice, etc.). O failover de banco de dados que será executado no final da operação cancelará todas as transações em andamento.

O SQL Instância Gerenciada não está disponível para aplicativos cliente durante operações de implantação e exclusão.

## <a name="management-operations-cross-impact"></a>Operações de gerenciamento com impacto cruzado

As operações de gerenciamento em uma instância gerenciada podem afetar outras operações de gerenciamento das instâncias colocadas dentro do mesmo cluster virtual:

- **As operações de restauração de execução longa** em um cluster virtual colocarão em espera outras operações de criação de instância ou de dimensionamento na mesma sub-rede.<br/>**Exemplo:** Se houver uma operação de restauração de execução longa e houver uma solicitação de criação ou escala na mesma sub-rede, essa solicitação levará mais tempo para ser concluída, pois ela aguardará a conclusão da operação de restauração antes de continuar.
    
- **Uma operação de criação ou dimensionamento de instância subsequente** é colocada em espera por uma criação de instância ou escala de instância iniciada anteriormente que iniciou o redimensionamento do cluster virtual.<br/>**Exemplo:** Se houver várias solicitações de criação e/ou escala na mesma sub-rede no mesmo cluster virtual e uma delas iniciar um redimensionamento de cluster virtual, todas as solicitações que foram enviadas 5 + minutos após a que exigiu o redimensionamento do cluster virtual durarão mais do que o esperado, pois essas solicitações precisarão aguardar a conclusão do redimensionamento antes de continuar.

- **As operações de criação/dimensionamento enviadas em uma janela de 5 minutos** serão colocadas em lote e executadas em paralelo.<br/>**Exemplo:** Somente um redimensionamento de cluster virtual será executado para todas as operações enviadas em uma janela de cinco minutos (medindo desde o momento da execução da primeira solicitação de operação). Se outra solicitação for enviada mais de 5 minutos depois que o primeiro for enviado, ele aguardará que o redimensionamento do cluster virtual seja concluído antes do início da execução.

> [!IMPORTANT]
> As operações de gerenciamento que são colocadas em espera devido a outra operação em andamento serão automaticamente retomadas uma vez que as condições para continuar forem atendidas. Não há nenhuma ação do usuário necessária para retomar as operações de gerenciamento em pausa temporariamente.

## <a name="canceling-management-operations"></a>Cancelando operações de gerenciamento

A tabela a seguir resume a capacidade de cancelar operações de gerenciamento específicas e as durações gerais típicas:

Categoria  |Operação  |Cancelável  |Duração estimada de cancelamento  |
|---------|---------|---------|---------|
|Implantação |Criação de instância |Não |  |
|Atualização |Expansão/redução do armazenamento de instância (Uso Geral) |Não |  |
|Atualização |Expansão/redução do armazenamento de instância (Comercialmente Crítico) |Sim |90% das operações são concluídas em 5 minutos. |
|Atualização |Expansão e redução da vCores (computação de instância) (Uso Geral) |Sim |90% das operações são concluídas em 5 minutos. |
|Atualização |Expansão e redução da vCores (computação de instância) (Comercialmente Crítico) |Sim |90% das operações são concluídas em 5 minutos. |
|Atualização |Alteração da camada de serviço de instância (Uso Geral para Comercialmente Crítico e vice-versa) |Sim |90% das operações são concluídas em 5 minutos. |
|Excluir |Exclusão da instância |Não |  |
|Excluir |Exclusão de cluster virtual (como operação iniciada pelo usuário) |Não |  |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para cancelar a operação de gerenciamento, vá para a folha visão geral e clique na caixa de notificação da operação em andamento. Do lado direito, uma tela com a operação em andamento será exibida e haverá um botão para a operação de cancelamento. Após o primeiro clique, você será solicitado a clicar novamente e confirmar que deseja cancelar a operação.

[![Cancelar operação](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

Depois que uma solicitação de cancelamento for enviada e processada, você receberá uma notificação se o envio de cancelamento tiver sido bem-sucedido ou não.

Se a solicitação de cancelamento enviado for bem-sucedida, a operação de gerenciamento será cancelada em alguns minutos, resultando em uma falha.

![Cancelando resultado da operação](./media/management-operations-overview/canceling-operation-result.png)

Se a solicitação de cancelamento falhar ou o botão Cancelar não estiver ativo, significa que a operação de gerenciamento entrou em estado não cancelável e que será concluída em alguns minutos. A operação de gerenciamento continuará sua execução até que seja concluída.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se você ainda não tiver Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para cancelar a operação de gerenciamento, você precisa especificar o nome da operação de gerenciamento. Portanto, primeiro use o comando Get para recuperar a lista de operações e, em seguida, cancele a operação específica.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Para obter uma explicação detalhada de comandos, consulte [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) e [Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você ainda não tiver o CLI do Azure instalado, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para cancelar a operação de gerenciamento, você precisa especificar o nome da operação de gerenciamento. Portanto, primeiro use o comando Get para recuperar a lista de operações e, em seguida, cancele a operação específica.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Para obter uma explicação detalhada de comandos, consulte [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest).

---

## <a name="next-steps"></a>Próximas etapas
- Para saber como criar sua primeira instância gerenciada, consulte [Guia de início rápido](instance-create-quickstart.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração da VNet, consulte [configuração da vnet do SQL instância gerenciada](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, consulte [criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o serviço de migração de banco de dados do Azure para migração, consulte [migração de SQL instância gerenciada usando o serviço de migração de banco](../../dms/tutorial-sql-server-to-managed-instance.md)
