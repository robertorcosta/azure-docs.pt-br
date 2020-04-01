---
title: Determinar as causas de não conformidade
description: Quando um recurso não está em conformidade, existem muitas razões possíveis. Aprenda a descobrir o que causou o não cumprimento.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "79264629"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas de não conformidade

Quando um recurso do Azure é determinado como incompatível com uma regra de política, é útil entender qual parte da regra o recurso não está em conformidade. Também é útil entender qual mudança alterou um recurso anteriormente compatível para torná-lo incompatível. Há duas maneiras de encontrar essa informação:

> [!div class="checklist"]
> - [Detalhes de conformidade](#compliance-details)
> - [Alterar histórico (Versão prévia)](#change-history)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso não está em conformidade, os detalhes de conformidade desse recurso estão disponíveis na página **de conformidade de diretivas.** O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes de recursos, como nome, tipo, localização e ID de recurso
- Estado de conformidade e carimbo de data e hora da última avaliação para a atribuição da política atual
- Uma lista de _razões_ para o não cumprimento de recursos

> [!IMPORTANT]
> Como os detalhes de conformidade de um recurso _não compatível_ mostram o valor atual das propriedades nesse recurso, o usuário deve ter **lido** a operação para o **tipo** de recurso. Por exemplo, se o recurso _não compatível_ for **Microsoft.Compute/virtualMachines,** então o usuário deve ter a operação **Microsoft.Compute/virtualMachines/read.** Se o usuário não tiver a operação necessária, um erro de acesso será exibido.

Para visualizar os detalhes de conformidade, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Na **página Visão geral** ou **conformidade,** selecione uma diretiva em um estado de **conformidade** que não esteja _em conformidade_.

1. Na guia **De conformidade de recursos** da página de conformidade **diretiva,** clique com o botão direito do mouse ou selecione a elipse de um recurso em um estado de **conformidade** que não esteja _em conformidade_. Em seguida, **selecione Exibir detalhes de conformidade**.

   ![Ver opção detalhes de conformidade](../media/determine-non-compliance/view-compliance-details.png)

1. O painel **de detalhes de Conformidade** exibe informações desde a última avaliação do recurso até a atribuição de políticas atuais. Neste exemplo, o campo **Microsoft.Sql/servers/version** é considerado _12.0_ enquanto a definição de diretiva esperava _14.0_. Se o recurso não estiver em conformidade por várias razões, cada um deles será listado neste painel.

   ![Detalhes de conformidade e razões para não conformidade](../media/determine-non-compliance/compliance-details-pane.png)

   Para uma **auditoriaIfNotExist** ou **deployIfNotExist** definição, os detalhes incluem a propriedade **details.type** e quaisquer propriedades opcionais. Para uma lista, consulte [auditIfNotExist](../concepts/effects.md#auditifnotexists-properties) e [deployIfNotExist (propriedades IfNotExist](../concepts/effects.md#deployifnotexists-properties)). **O último recurso avaliado** é um recurso relacionado da seção de **detalhes** da definição.

   Implantação parcial **por exemploA definiçãoNãoExiste:**

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Painel de detalhes de conformidade - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade é um _segredo,_ o valor atual exibe asteriscos.

Esses detalhes explicam por que um recurso está atualmente incompatível, mas não mostram quando a alteração foi feita para o recurso que fez com que ele se tornasse incompatível. Para obter essa informação, consulte [Alterar histórico (Visualizar)](#change-history) abaixo.

### <a name="compliance-reasons"></a>Razões de conformidade

A matriz a seguir mapeia cada _razão_ possível para a [condição](../concepts/definition-structure.md#conditions) responsável na definição da política:

|Motivo | Condição |
|-|-|
|O valor atual deve conter o valor-alvo como chave. |contémchave ou **nãoContémChave** |
|O valor atual deve conter o valor-alvo. |contém ou **nãoContém** |
|O valor atual deve ser igual ao valor-alvo. |igual ou **nãoEquals** |
|O valor atual deve ser menor do que o valor-alvo. |menos ou **não** maiorouigual |
|O valor atual deve ser maior ou igual ao valor-alvo. |maioresOrEquals ou **não** menos |
|O valor atual deve ser maior do que o valor-alvo. |maior ou **não** lessOrEquals |
|O valor atual deve ser menor ou igual ao valor-alvo. |lessOrEquals ou **não** maior |
|O valor atual deve existir. |exists |
|O valor atual deve estar no valor-alvo. |em ou **nãoIn** |
|O valor atual deve ser como o valor-alvo. |como ou **não** Como |
|O valor atual deve corresponder ao valor-alvo. |jogo ou **nãoMatch** |
|O valor atual deve corresponder insensível ao valor-alvo. |matchInsensamente ou nãoMatchInsensitively **not** |
|O valor atual não deve conter o valor-alvo como chave. |nãoContémChave ou **não** contémChave|
|O valor atual não deve conter o valor-alvo. |nãoContém ou **não** contém |
|O valor atual não deve ser igual ao valor-alvo. |nãoIguala ou **não** é igual |
|O valor atual não deve existir. |**não** existe  |
|O valor atual não deve estar no valor-alvo. |nãoEm ou **não** em |
|O valor atual não deve ser como o valor-alvo. |não como ou **não** como |
|O valor atual não deve corresponder ao valor-alvo. |nãoMatch ou **não** corresponder |
|O valor atual não deve corresponder ao valor-alvo. |notMatchInsensitively ou **not** matchInsensitively |
|Nenhum recurso relacionado corresponde aos detalhes de efeito na definição da diretiva. |Um recurso do tipo definido em **então.details.type** e relacionado ao recurso definido na parte **se** a regra da diretiva não existir. |

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade para configuração do hóspede

Para _auditIfNotExist_ na categoria _Configuração de hóspedes,_ pode haver várias configurações avaliadas dentro da VM e você precisará visualizar detalhes por configuração. Por exemplo, se você está auditando uma lista de políticas de senha e apenas uma delas tem status _Não conforme,_ você precisará saber quais políticas específicas de senha estão fora de conformidade e por quê.

Você também pode não ter acesso para entrar diretamente na VM, mas você precisa informar por que a VM não está _em conformidade_.

### <a name="azure-portal"></a>Portal do Azure

Comece seguindo as mesmas etapas na seção acima para visualizar detalhes de conformidade de políticas.

No **painel Detalhes** de conformidade clique no link **Último recurso avaliado**.

   ![Exibir detalhes de definição auditIfNotExist](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

A **página Atribuição de convidados** exibe todos os detalhes de conformidade disponíveis. Cada linha na vista representa uma avaliação que foi realizada dentro da máquina. Na coluna **Razão,** uma frase que descreve por que a Atribuição _de Convidados não_ é compatível é mostrada. Por exemplo, se você estiver auditando políticas de senha, a coluna **Razão** exibirá texto, incluindo o valor atual para cada configuração.

![Ver detalhes de conformidade](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Você também pode visualizar detalhes de conformidade do Azure PowerShell. Primeiro, certifique-se de que o módulo de configuração do convidado está instalado.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Você pode visualizar o status atual de todas as atribuições de hóspedes de uma VM usando o seguinte comando:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Para ver apenas a _frase de razão_ que descreve por que a VM não é _compatível,_ devolva apenas a propriedade da criança Razão.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Você também pode produzir um histórico de conformidade para atribuições de hóspedes no escopo da máquina. A saída deste comando inclui os detalhes de cada relatório para a VM.

> [!NOTE]
> A saída pode retornar um grande volume de dados. Recomenda-se armazenar a saída em uma variável.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Para simplificar essa visualização, use o parâmetro **ShowChanged.** A saída deste comando inclui apenas os relatórios que se seguiram a uma mudança no status de conformidade.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"/>Alterar histórico (Versão prévia)

Como parte de uma nova **pré-visualização pública,** os últimos 14 dias de histórico de alterações estão disponíveis para todos os recursos do Azure que suportam [a exclusão completa do modo](../../../azure-resource-manager/templates/complete-mode-deletion.md). O histórico de alterações fornece detalhes sobre quando uma alteração foi detectada e uma _comparação visual_ para cada alteração. Uma detecção de alteração é acionada quando as propriedades do Gerenciador de recursos são adicionadas, removidas ou alteradas.

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Na **página Visão geral** ou **conformidade,** selecione uma diretiva em qualquer **estado de conformidade**.

1. Na guia **De conformidade de recursos** da página de conformidade **política,** selecione um recurso.

1. Escolha a guia **Histórico de Alterações (versão prévia)** na página **Conformidade do Recurso**. Se houver uma lista de alterações detectadas, ela será exibida.

   ![Guia histórico de alteração de diretiva do Azure na página de conformidade de recursos](../media/determine-non-compliance/change-history-tab.png)

1. Escolha uma das alterações detectadas. O _diferencial visual_ para o recurso é apresentado na página Histórico de **Alteração.**

   ![Azure Policy Change History Visual Diff on Change history](../media/determine-non-compliance/change-history-visual-diff.png)

A _comparação visual_ ajuda a identificar alterações em um recurso. As alterações detectadas podem não estar relacionadas ao estado atual de conformidade do recurso.

Os dados do histórico de alterações são fornecidos pelo [Azure Resource Graph](../../resource-graph/overview.md). Para consultar essas informações fora do portal Azure, consulte [Obter alterações de recursos](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Próximas etapas

- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [obter dados de conformidade](get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)
