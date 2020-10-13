---
title: Azure Monitor os clusters dedicados
description: Os clientes que ingerirem mais de 1 TB um dia de dados de monitoramento poderão usar clusters dedicados em vez de compartilhados
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 714a43ec197ac150488d4443c1eb6fe1be1da232
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575513"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor os clusters dedicados

Azure Monitor os clusters dedicados de logs são uma opção de implantação que está disponível para atender melhor aos clientes de alto volume. Os clientes que ingerirem mais de 4 TB de dados por dia usarão clusters dedicados. Os clientes com clusters dedicados podem escolher os espaços de trabalho a serem hospedados nesses clusters.

Além do suporte para alto volume, há outros benefícios do uso de clusters dedicados:

- **Limite de taxa** – um cliente pode ter mais [limites de taxa de ingestão](../service-limits.md#data-ingestion-volume-rate) apenas no cluster dedicado.
- **Recursos** – determinados recursos da empresa estão disponíveis apenas em clusters dedicados, especificamente CMK (chaves gerenciadas pelo cliente) e suporte de lockbox. 
- **Consistência** – os clientes têm seus próprios recursos dedicados e, portanto, não há nenhuma influência de outros clientes em execução na mesma infraestrutura compartilhada.
- **Eficiência de custo** – pode ser mais econômico usar o cluster dedicado, já que as camadas de reserva de capacidade atribuídas levam em conta toda a ingestão de cluster e se aplicam a todos os seus espaços de trabalho, mesmo que algumas delas sejam pequenas e não elegíveis para o desconto de reserva de capacidade.
- As consultas **entre espaços de trabalho** são executadas mais rapidamente se todos os espaços de trabalho estiverem no mesmo cluster.

Os clusters dedicados exigem que os clientes sejam confirmados usando uma capacidade de pelo menos 1 TB de ingestão de dados por dia. A migração para um cluster dedicado é simples. Não há perda de dados ou interrupção de serviço. 

> [!IMPORTANT]
> Os clusters dedicados são aprovados e têm suporte total para implantações de produção. No entanto, devido a restrições de capacidade temporárias, exigimos o registro prévio para usar o recurso. Use seus contatos na Microsoft para fornecer suas IDs de assinaturas.

## <a name="management"></a>Gerenciamento 

Os clusters dedicados são gerenciados por meio de um recurso do Azure que representa Azure Monitor clusters de log. Todas as operações são feitas neste recurso usando o PowerShell ou a API REST.

Depois que o cluster é criado, ele pode ser configurado e espaços de trabalho vinculados a ele. Quando um espaço de trabalho é vinculado a um cluster, novos dados enviados ao espaço de trabalho residem no cluster. Somente espaços de trabalho que estão na mesma região que o cluster podem ser vinculados ao cluster. Os espaços de trabalho podem não ser semelhantes a um cluster com algumas limitações. Mais detalhes sobre essas limitações estão incluídos neste artigo. 

Todas as operações no nível do cluster exigem a `Microsoft.OperationalInsights/clusters/write` permissão Action no cluster. Essa permissão pode ser concedida por meio do proprietário ou colaborador que contém a `*/write` ação ou por meio da função colaborador de log Analytics que contém a `Microsoft.OperationalInsights/*` ação. Para obter mais informações sobre Log Analytics permissões, consulte [gerenciar o acesso a dados de log e espaços de trabalho no Azure monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Modelo de preços do cluster

Log Analytics clusters dedicados usam um modelo de preços de reserva de capacidade, que de pelo menos 1000 GB/dia. Qualquer uso acima do nível de reserva será cobrado com a taxa de Pagamento Conforme o Uso.  As informações de preços de reserva de capacidade estão disponíveis na [página de preços Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

O nível de reserva de capacidade do cluster é configurado por meio de programação com Azure Resource Manager usando o `Capacity` parâmetro em `Sku` . A `Capacity` é especificada em unidades de GB e pode ter valores de 1.000 GB/dia ou mais em incrementos de 100 GB/dia.

Há dois modos de cobrança para uso em um cluster. Eles podem ser especificados pelo `billingType` parâmetro ao configurar o cluster. 

1. **Cluster**: nesse caso (que é o padrão), a cobrança de dados ingeridos é feita no nível do cluster. As quantidades de dados ingeridos de cada workspace associado a um cluster são agregadas para calcular a fatura diária do cluster. 

2. **Espaços de trabalho**: os custos de reserva de capacidade para o cluster são atribuídos proporcionalmente aos espaços de trabalho no cluster (após a contabilização de alocações por nó da [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/) para cada espaço de trabalho).

Observe que, se o seu espaço de trabalho estiver usando o tipo de preço herdado por nó, quando ele estiver vinculado a um cluster, ele será cobrado com base nos dados ingeridos em relação à reserva de capacidade do cluster e não mais por nó. As alocações de dados por nó da central de segurança do Azure continuarão a ser aplicadas.

Mais detalhes são cobrados para Log Analytics clusters dedicados estão disponíveis [aqui]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).


## <a name="creating-a-cluster"></a>Criar um cluster

Primeiro, você cria recursos de cluster para começar a criar um cluster dedicado.

As propriedades a seguir devem ser especificadas:

- **ClusterName**: usado para fins administrativos. Os usuários não são expostos a esse nome.
- **ResourceGroupName**: como para qualquer recurso do Azure, os clusters pertencem a um grupo de recursos. Recomendamos que você use um grupo de recursos de ti central porque os clusters geralmente são compartilhados por muitas equipes na organização. Para obter mais considerações de design, examine [criando sua implantação de logs de Azure monitor](../platform/design-logs-deployment.md)
- **Local**: um cluster está localizado em uma região específica do Azure. Somente os espaços de trabalho localizados nessa região podem ser vinculados a esse cluster.
- **SkuCapacity**: você deve especificar o nível de *reserva de capacidade* (SKU) ao criar um recurso de *cluster* . O nível de *reserva de capacidade* pode estar no intervalo de 1.000 gb a 3.000 GB por dia. Você pode atualizá-lo em etapas de 100 mais tarde, se necessário. Se você precisar de um nível de reserva de capacidade superior a 3.000 GB por dia, entre em contato conosco em LAIngestionRate@microsoft.com . Para obter mais informações sobre os custos de cluster, consulte [gerenciar custos para clusters de log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

Depois de criar o recurso de *cluster* , você pode editar propriedades adicionais, como *SKU*, * keyvaultproperties ou *billtype*. Veja mais detalhes abaixo.

> [!WARNING]
> A criação do cluster dispara a alocação de recursos e o provisionamento. Esta operação pode levar até uma hora para ser concluída. É recomendável executá-lo de forma assíncrona.

A conta de usuário que cria os clusters deve ter a permissão de criação de recurso do Azure padrão: `Microsoft.Resources/deployments/*` e a permissão de gravação de cluster `(Microsoft.OperationalInsights/clusters/write)` .

### <a name="create"></a>Criar 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Chamar* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Resposta*

Deve ser 200 OK e um cabeçalho.

### <a name="check-provisioning-status"></a>Verificar status do provisionamento

O provisionamento do cluster de Log Analytics demora um pouco para ser concluído. Você pode verificar o estado de provisionamento de várias maneiras:

- Execute Get-AzOperationalInsightsCluster comando do PowerShell com o nome do grupo de recursos e verifique a propriedade ProvisioningState. O valor é *ProvisioningAccount* durante o provisionamento e com *êxito* quando concluído.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Copie o valor da URL de Azure-AsyncOperation da resposta e siga a verificação de status de operações assíncronas.

- Envie uma solicitação GET no recurso de *cluster* e examine o valor *provisioningState*. O valor é *ProvisioningAccount* durante o provisionamento e com *êxito* quando concluído.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

   **Resposta**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

O GUID *PrincipalId* é gerado pelo serviço de identidade gerenciada para o recurso de *cluster* .

## <a name="change-cluster-properties"></a>Alterar propriedades do cluster

Depois de criar o recurso de *cluster* e ele ser totalmente provisionado, você pode editar propriedades adicionais no nível de cluster usando o PowerShell ou a API REST. Além das propriedades que estão disponíveis durante a criação do cluster, as propriedades adicionais só podem ser definidas depois que o cluster tiver sido provisionado:

- **Keyvaultproperties**: usado para configurar o Azure Key Vault usado para provisionar um [Azure monitor chave gerenciada pelo cliente](../platform/customer-managed-keys.md#cmk-provisioning-procedure). Ele contém os seguintes parâmetros:  *KeyVaultUri*, *KeyName*, *keyversion*. 
- **billtype** – a propriedade *billtype* determina a atribuição de cobrança para o recurso de *cluster* e seus dados:
  - **Cluster** (padrão)-os custos de reserva de capacidade para o cluster são atribuídos ao recurso de *cluster* .
  - **Espaços de trabalho** -os custos de reserva de capacidade para o cluster são atribuídos proporcionalmente aos espaços de trabalho no cluster, com o recurso de *cluster* sendo cobrado por parte do uso se o total de dados ingeridos para o dia estiver sob a reserva de capacidade. Consulte [log Analytics clusters dedicados](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) para saber mais sobre o modelo de preços do cluster. 

> [!NOTE]
> Não há suporte para a propriedade *billtype* no PowerShell.
> As atualizações de Propriedade do cluster podem ser executadas assíncronas e podem demorar um pouco para serem concluídas.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> Você pode atualizar o *SKU*do recurso de *cluster* , *keyvaultproperties* ou *billtype* usando patch.

Por exemplo: 

*Chamar*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" contém os detalhes do identificador de chave do Key Vault.

*Resposta*

200 OK e cabeçalho

### <a name="check-cluster-update-status"></a>Verificar status de atualização do cluster

A propagação do identificador de chave leva alguns minutos para ser concluída. Você pode verificar o status de atualização de duas maneiras:

- Copie o valor da URL de Azure-AsyncOperation da resposta e siga a verificação de status de operações assíncronas. 

   OU

- Envie uma solicitação GET no recurso de *cluster* e examine as propriedades de *KeyVaultProperties*. Os detalhes do identificador de chave atualizados recentemente devem retornar na resposta.

   Uma resposta à solicitação GET no recurso de *cluster* deve ser semelhante a esta quando a atualização do identificador de chave for concluída:

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principle-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "keyVaultProperties": {
         "keyVaultUri": "https://key-vault-name.vault.azure.net",
         "kyName": "key-name",
         "keyVersion": "current-version"
         },
        "provisioningState": "Succeeded",
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>Vincular um espaço de trabalho ao cluster

Quando um espaço de trabalho é vinculado a um cluster dedicado, novos dados que são ingeridos no espaço de trabalho são roteados para o novo cluster enquanto os dados existentes permanecem no cluster existente. Se o cluster dedicado for criptografado usando chaves gerenciadas pelo cliente (CMK), somente os novos dados serão criptografados com a chave. O sistema está abstraindo essa diferença dos usuários e os usuários apenas consultam o espaço de trabalho como de costume enquanto o sistema executa consultas entre clusters no back-end.

Um cluster pode ser vinculado a até 100 espaços de trabalho. Os espaços de trabalho vinculados estão localizados na mesma região que o cluster. Para proteger o back-end do sistema e evitar a fragmentação de dados, um espaço de trabalho não pode ser vinculado a um cluster mais de duas vezes por mês.

Para executar a operação de vínculo, você precisa ter permissões de ' gravação ' para o espaço de trabalho e o recurso de *cluster* :

- No espaço de trabalho: *Microsoft. OperationalInsights/Workspaces/Write*
- No recurso de *cluster* : *Microsoft. OperationalInsights/clusters/Write*

Além dos aspectos de cobrança, o espaço de trabalho vinculado mantém suas próprias configurações, como o comprimento da retenção de dados.
O espaço de trabalho e o cluster podem estar em assinaturas diferentes. É possível que o espaço de trabalho e o cluster estejam em locatários diferentes se o Azure Lighthouse for usado para mapear ambos para um único locatário.

Como qualquer operação de cluster, a vinculação de um espaço de trabalho pode ser executada somente após a conclusão do provisionamento de cluster Log Analytics.

> [!WARNING]
> Vincular um espaço de trabalho a um cluster requer a sincronização de vários componentes de back-end e a garantia de hidratação de cache. Esta operação pode levar até duas horas para ser concluída. Recomendamos executá-lo de forma assíncrona.


**PowerShell**

Use o seguinte comando do PowerShell para vincular a um cluster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Use a seguinte chamada REST para vincular a um cluster:

*Enviar*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Resposta*

200 OK e cabeçalho.

### <a name="using-customer-managed-keys-with-linking"></a>Usando chaves gerenciadas pelo cliente com vinculação

Se você usar chaves gerenciadas pelo cliente, os dados ingeridos serão armazenados criptografados com a chave gerenciada após a operação de associação, o que pode levar até 90 minutos para ser concluído. 

Você pode verificar o estado de associação do workspace de duas maneiras:

- Copie o valor da URL de Azure-AsyncOperation da resposta e siga a verificação de status de operações assíncronas.

- Enviar [espaços de trabalho – obter](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) solicitação e observar a resposta. O espaço de trabalho associado tem um clusterResourceId em "recursos".

Uma solicitação de envio é semelhante ao seguinte:

*Enviar*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Resposta*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Desvincular um espaço de trabalho de um cluster dedicado

Você pode desvincular um espaço de trabalho de um cluster. Depois de desvincular um espaço de trabalho do cluster, os novos dados associados a esse espaço de trabalho não são enviados para o cluster dedicado. Além disso, a cobrança do espaço de trabalho não é mais feita por meio do cluster. Os dados antigos do espaço de trabalho desvinculado podem ser deixados no cluster. Se esses dados forem criptografados usando chaves gerenciadas pelo cliente (CMK), os segredos de Key Vault serão mantidos. O sistema é abstrair essa alteração de Log Analytics usuários. Os usuários podem apenas consultar o espaço de trabalho como de costume. O sistema executa consultas entre clusters no back-end, conforme necessário, sem nenhuma indicação para os usuários.  

> [!WARNING] 
> Há um limite de duas operações de vinculação por espaço de trabalho em um mês. Reserve um tempo para considerar e planeje desvincular ações de forma adequada. 

## <a name="delete-a-dedicated-cluster"></a>Excluir um cluster dedicado

Um recurso de cluster dedicado pode ser excluído. Você deve desvincular todos os espaços de trabalho do cluster antes de excluí-lo. Você precisa de permissões de “gravação” no recurso de *cluster* para executar essa operação. 

Depois que o recurso de cluster é excluído, o cluster físico entra em um processo de limpeza e exclusão. A exclusão de um cluster exclui todos os dados que foram armazenados no cluster. Os dados podem ser de espaços de trabalho que foram vinculados ao cluster no passado.

Um recurso de *cluster* que foi excluído nos últimos 14 dias está no estado de exclusão temporária e pode ser recuperado com seus dados. Como todos os espaços de trabalho foram desassociados do recurso de *cluster* com a exclusão de recursos de *cluster* , você precisa associar novamente os espaços de trabalho após a recuperação. A operação de recuperação não pode ser executada pelo usuário entre em contato com seu canal da Microsoft ou suporte para solicitações de recuperação.

Dentro dos 14 dias após a exclusão, o nome do recurso de cluster é reservado e não pode ser usado por outros recursos.

**PowerShell**

Use o seguinte comando do PowerShell para excluir um cluster:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Use a seguinte chamada REST para excluir um cluster:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [log Analytics cobrança de cluster dedicado](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Saiba mais sobre o [design adequado de espaços de trabalho de log Analytics](../platform/design-logs-deployment.md)
