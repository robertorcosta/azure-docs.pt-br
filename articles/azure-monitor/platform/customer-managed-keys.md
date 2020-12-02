---
title: Chave do Azure Monitor gerenciada pelo cliente
description: Informações e etapas para configurar Customer-Managed chave para criptografar dados em seus espaços de trabalho do Log Analytics usando uma chave de Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/18/2020
ms.openlocfilehash: 17648b9bc973285764bb0bd6242506122a043780
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454261"
---
# <a name="azure-monitor-customer-managed-key"></a>Chave do Azure Monitor gerenciada pelo cliente 

Este artigo fornece informações básicas e etapas para configurar chaves gerenciadas pelo cliente para seus espaços de trabalho do Log Analytics. Uma vez configurada, todos os dados enviados para seus workspaces são criptografados com sua chave do Azure Key Vault.

Recomendamos revisar [Limitações e restrições](#limitationsandconstraints) abaixo antes da configuração.

## <a name="customer-managed-key-overview"></a>Visão geral da chave gerenciada pelo cliente

[A criptografia em repouso](../../security/fundamentals/encryption-atrest.md) é um requisito comum de privacidade e segurança nas organizações. Você pode permitir que o Azure gerencie completamente a criptografia em repouso, enquanto você tem várias opções para gerenciar de forma rigorosa as chaves de criptografia e criptografia.

Azure Monitor garante que todos os dados e consultas salvas sejam criptografadas em repouso usando chaves gerenciadas pela Microsoft (MMK). O Azure Monitor também fornece uma opção de criptografia usando sua própria chave armazenada em seu [Azure Key Vault](../../key-vault/general/overview.md) e lhe dá o controle para revogar o acesso aos seus dados a qualquer momento. Azure Monitor uso da criptografia é idêntico ao modo como a [criptografia de armazenamento do Azure](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) Opera.

Customer-Managed chave é entregue em clusters de Log Analytics dedicados, fornecendo maior nível de proteção e controle. Os dados ingeridos em clusters dedicados estão sendo criptografados duas vezes — uma vez no nível de serviço usando chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente e uma vez no nível de infraestrutura usando dois algoritmos de criptografia diferentes e duas chaves diferentes. A [criptografia dupla](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) protege contra um cenário em que um dos algoritmos ou chaves de criptografia pode ser comprometido. Nesse caso, a camada adicional de criptografia continua a proteger seus dados. O cluster dedicado também permite que você proteja seus dados com o controle de [Lockbox](#customer-lockbox-preview) .

Os dados ingeridos nos últimos 14 dias também são mantidos no cache de acesso frequente (com suporte de SSD) para uma operação de mecanismo de consulta eficiente. Esses dados permanecem criptografados com chaves da Microsoft independentemente da configuração de chave gerenciada pelo cliente, mas seu controle sobre os dados SSD adere à [revogação de chave](#key-revocation). Estamos trabalhando para ter dados SSD criptografados com Customer-Managed chave no primeiro semestre de 2021.

O [modelo de preços de clusters log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters) usa reservas de capacidade a partir de um nível de 1000 GB/dia.

> [!IMPORTANT]
> Devido a restrições de capacidade temporária, exigimos que você registre previamente antes de criar um cluster. Use seus contatos na Microsoft ou abra a solicitação de suporte para registrar suas IDs de assinaturas.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Como Customer-Managed chave funciona em Azure Monitor

O Azure Monitor utiliza a identidade gerenciada atribuída pelo sistema para conceder acesso ao seu Azure Key Vault. A identidade gerenciada atribuída pelo sistema só pode ser associada a um único recurso do Azure, enquanto a identidade do cluster de Log Analytics tem suporte no nível do cluster – isso determina que a funcionalidade é fornecida em um cluster de Log Analytics dedicado. Para dar suporte à chave de Customer-Managed em vários espaços de trabalho, um novo recurso de *Cluster* log Analytics é executado como uma conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho do log Analytics. O armazenamento de cluster do Log Analytics usa a identidade gerenciada que \'está associada ao recurso de *cluster* para autenticar no Azure Key Vault via Azure Active Directory. 

Após a configuração, todos os dados ingeridos em espaços de trabalho vinculados ao seu cluster dedicado são criptografados com sua chave em Key Vault. Você pode desvincular espaços de trabalho do cluster a qualquer momento. Os novos dados são ingeridos para Log Analytics armazenamento e criptografados com a chave da Microsoft, enquanto você pode consultar os dados novos e antigos diretamente.


![Visão geral da chave de Customer-Managed](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Recurso de *cluster* do Log Analytics com identidade gerenciada com permissões para Key Vault -- A identidade é propagada para o armazenamento de cluster do Log Analytics dedicado subjacente
3. Cluster dedicado de Log Analytics
4. Espaços de trabalho vinculados ao recurso de *cluster* 

## <a name="encryption-keys-operation"></a>Operação de chaves de criptografia

Há três tipos de chaves envolvidas na criptografia de dados de Armazenamento:

- Chave de criptografia de chave de **Kek** (sua chave de Customer-Managed)
- **AEK** - Chave de Criptografia da Conta
- **DEK** - Chave de Criptografia dos Dados

As seguintes regras se aplicam:

- As contas de armazenamento de cluster Log Analytics geram uma chave de criptografia exclusiva para cada conta de armazenamento, que é conhecida como AEK.
- O AEK é usado para derivar DEKs, que são as chaves que são usadas para criptografar cada bloco de dados gravados no disco.
- Quando você configura sua chave no Key Vault e faz referência a ela no cluster, o armazenamento do Azure envia solicitações ao seu Azure Key Vault para encapsular e desencapsular o AEK para executar as operações de criptografia e descriptografia de dados.
- Seu KEK nunca deixa seu Key Vault e, no caso de uma chave HSM, ele nunca deixa o hardware.
- O armazenamento do Azure usa a identidade gerenciada associada ao recurso de *cluster* para autenticar e acessar o Azure Key Vault via Azure Active Directory.

## <a name="customer-managed-key-provisioning-procedure"></a>Procedimento de provisionamento de chave Customer-Managed

1. Registrar sua assinatura para permitir a criação do cluster
1. Criar o Azure Key Vault e armazenar a chave
1. Criando cluster
1. Conceder permissões ao Key Vault
1. Vinculando espaços de trabalho Log Analytics

Não há suporte para a configuração de chave Customer-Managed no portal do Azure e o provisionamento é executado por meio do [PowerShell](/powershell/module/az.operationalinsights/), da [CLI](/cli/azure/monitor/log-analytics) ou de solicitações [REST](/rest/api/loganalytics/) .

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de status

Algumas das etapas de configuração são executadas de forma assíncrona porque não podem ser concluídas rapidamente. O `status` em resposta contém pode ser um dos seguintes: ' InProgress ', ' atualizando ', ' excluindo ', ' Succeeded ' ou ' Failed ', incluindo o código de erro.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="rest"></a>[REST](#tab/rest)

Ao usar REST, a resposta inicialmente retorna um código de status HTTP 200 (OK) e o cabeçalho com a propriedade *Azure-AsyncOperation* quando aceita:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Você pode verificar o status da operação assíncrona enviando uma solicitação GET ao valor do cabeçalho *Azure-AsyncOperation*:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

### <a name="allowing-subscription"></a>Permitindo assinatura

> [!IMPORTANT]
> Customer-Managed recurso principal é regional. Seus Azure Key Vault, cluster e espaços de trabalho vinculados Log Analytics devem estar na mesma região, mas podem estar em assinaturas diferentes.

### <a name="storing-encryption-key-kek"></a>Armazenamento da chave de criptografia (KEK)

Crie ou use um Azure Key Vault que você já tem para gerar ou importe uma chave a ser usada para criptografia de dados. O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos dados no Azure Monitor. Você pode verificar essa configuração em propriedades no seu Key Vault, a *Exclusão temporária* e a *Proteção contra limpeza* devem ser habilitadas.

![Configurações de exclusão temporária e proteção contra limpeza](media/customer-managed-keys/soft-purge-protection.png)

Essas configurações podem ser atualizadas no Key Vault por meio da CLI e do PowerShell:

- [Exclusão Reversível](../../key-vault/general/soft-delete-overview.md)
- A [proteção contra limpeza](../../key-vault/general/soft-delete-overview.md#purge-protection) protege contra a exclusão forçada do segredo/cofre mesmo após a exclusão temporária

### <a name="create-cluster"></a>Criar cluster

Siga o artigo procedimento ilustrado no [clusters dedicados](../log-query/logs-dedicated-clusters.md#creating-a-cluster). 

> [!IMPORTANT]
> Copie e salve a resposta, pois você precisará das informações nas próximas etapas.

### <a name="grant-key-vault-permissions"></a>Conceder permissões do Key Vault

Criar política de acesso no Key Vault para conceder permissões ao seu cluster. Essas permissões são usadas pelo Armazenamento do Azure Monitor subjacente para criptografia de dados. Abra o Key Vault no portal do Azure e clique em “Políticas de acesso” e, em seguida, em “+ Adicionar política de acesso” para criar uma política com estas configurações:

- Permissões da chave: selecione as permissões “Obter”, “Encapsular Chave” e “Desencapsular chave”.
- Selecionar entidade de segurança: Insira o nome do cluster ou o valor da ID principal que foi retornado na resposta na etapa anterior.

![conceder permissões do Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Obter* é necessária para verificar se o Key Vault está configurado como recuperável para proteger sua chave e o acesso aos seus dados do Azure Monitor.

### <a name="update-cluster-with-key-identifier-details"></a>Atualizar cluster com detalhes do identificador de chave

Todas as operações no cluster exigem a permissão Microsoft. OperationalInsights/clusters/Write Action. Essa permissão pode ser concedida por meio do proprietário ou colaborador que contém a *ação/Write ou por meio da função colaborador de log Analytics que contém a ação Microsoft. OperationalInsights/* .

Esta etapa atualiza Azure Monitor armazenamento com a chave e a versão a serem usadas para criptografia de dados. Quando atualizado, sua nova chave está sendo usada para encapsular e desencapsular a chave de armazenamento (AEK).

Selecione a versão atual da sua chave em Azure Key Vault para obter os detalhes do identificador de chave.

![Conceder permissões do Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Atualize keyvaultproperties no cluster com detalhes do identificador de chave.

A operação é assíncrona e pode demorar um pouco para ser concluída.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Resposta**

A propagação do identificador de chave demora alguns minutos para ser concluída. Você pode verificar o status de atualização de duas maneiras:
1. Copie o valor da URL de Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no cluster e examine as propriedades *Keyvaultproperties* . Os detalhes do identificador de chave atualizados recentemente devem retornar na resposta.

Uma resposta à solicitação GET deve ser parecida com esta quando a atualização do identificador de chave for concluída: 200 OK e cabeçalho
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

---

### <a name="link-workspace-to-cluster"></a>Vincular espaço de trabalho ao cluster

Você precisa ter permissões de ' gravação ' no seu espaço de trabalho e no cluster para executar essa operação, o que inclui estas ações:

- No workspace: Microsoft.OperationalInsights/workspaces/write
- Em cluster: Microsoft. OperationalInsights/clusters/Write

> [!IMPORTANT]
> Esta etapa deve ser executada somente após a conclusão do provisionamento de cluster do Log Analytics. Se você vincular espaços de trabalho e ingerir dados antes do provisionamento, os dados ingeridos serão descartados e não poderão ser recuperados.

Esta operação é assíncrona e pode um tempo para ser concluída.

Siga o artigo procedimento ilustrado no [clusters dedicados](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-the-cluster).

## <a name="key-revocation"></a>Revogação de chave

Você pode revogar o acesso aos dados desabilitando sua chave ou excluindo a política de acesso do cluster em seu Key Vault. O armazenamento de cluster do Log Analytics sempre respeitará as alterações nas permissões de chave feita a mais de uma hora e o Armazenamento ficará indisponível. Todos os novos dados ingeridos em espaços de trabalho vinculados ao cluster são ignorados e não são recuperáveis, os dados ficam inacessíveis e as consultas a esses espaços de trabalho falham. Os dados anteriormente ingeridos permanecem no armazenamento, desde que seu cluster e seus espaços de trabalho não sejam excluídos. Os dados inacessíveis são administrados pela política de retenção de dados e serão limpos quando a retenção for alcançada. 

Os dados ingeridos nos últimos 14 dias também são mantidos no cache frequente (com suporte de SSD) para uma operação de mecanismo de consulta eficiente. Ele é excluído na operação de revogação de chave e se torna inacessível também.

O armazenamento pesquisa periodicamente seu Key Vault para tentar desencapsular a chave de criptografia e, depois de acessado, a ingestão de dados e a consulta são retomados em até 30 minutos.

## <a name="key-rotation"></a>Alteração de chaves

Customer-Managed a rotação de chave requer uma atualização explícita para o cluster com a nova versão de chave no Azure Key Vault. Siga as instruções na etapa "atualizar cluster com detalhes do identificador de chave". Se você não atualizar os novos detalhes do identificador de chave no cluster, o armazenamento de cluster Log Analytics continuará usando sua chave anterior para criptografia. Se você desabilitar ou excluir sua chave antiga antes de atualizar a nova chave no cluster, você entrará no estado de [revogação de chave](#key-revocation) .

Todos os seus dados continuam acessíveis após a operação de revezamento de chaves, já que os dados são sempre criptografados com a AEK (Chave de Criptografia de Conta), embora a AEK seja agora criptografada com sua nova versão de KEK (Chave de Criptografia de Chave) no Key Vault.

## <a name="customer-managed-key-for-queries"></a>Chave de Customer-Managed para consultas

A linguagem de consulta usada em Log Analytics é expressiva e pode conter informações confidenciais em comentários que você adiciona a consultas ou na sintaxe de consulta. Algumas organizações exigem que essas informações sejam mantidas protegidas em Customer-Managed política de chave e você precisa salvar suas consultas criptografadas com sua chave. Azure Monitor permite armazenar *pesquisas salvas* e consultas *de alertas de log* criptografadas com sua chave em sua própria conta de armazenamento quando conectada ao seu espaço de trabalho. 

> [!NOTE]
> Log Analytics consultas podem ser salvas em vários repositórios, dependendo do cenário usado. As consultas permanecem criptografadas com a chave da Microsoft (MMK) nos cenários a seguir, independentemente de Customer-Managed configuração de chave: pastas de trabalho no Azure Monitor, painéis do Azure, aplicativo lógico do Azure, Runbooks de Azure Notebooks e de automação.

Quando você coloca seu próprio armazenamento (BYOS) e o vincula ao seu espaço de trabalho, o serviço carrega as consultas *salvas* e *alertas de log* para sua conta de armazenamento. Isso significa que você controla a conta de armazenamento e a [política de criptografia em repouso](../../storage/common/customer-managed-keys-overview.md) usando a mesma chave usada para criptografar dados no cluster log Analytics ou em uma chave diferente. No entanto, você será responsável pelos custos associados a essa conta de armazenamento. 

**Considerações antes de definir Customer-Managed chave para consultas**
* Você precisa ter permissões de ' gravação ' no espaço de trabalho e na conta de armazenamento
* Certifique-se de criar sua conta de armazenamento na mesma região que o espaço de trabalho Log Analytics está localizado
* As *pesquisas salvas* no armazenamento são consideradas como artefatos de serviço e seu formato pode ser alterado
* As *pesquisas de gravações* existentes são removidas do seu espaço de trabalho. Copie e *salve as pesquisas* necessárias antes da configuração. Você pode exibir as *pesquisas salvas* usando o  [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* Não há suporte para o histórico de consulta e você não poderá ver as consultas que executou
* Você pode vincular uma única conta de armazenamento ao espaço de trabalho com a finalidade de salvar consultas, mas pode ser usado partir as *pesquisas salvas* e consultas de *alertas de log*
* Não há suporte para fixar no painel

**Configurar BYOS para consultas de pesquisas salvas**

Vincular uma conta de armazenamento para *consulta* ao seu espaço de trabalho-- *salvas* as consultas de pesquisas são salvas em sua conta de armazenamento. 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Após a configuração, qualquer nova consulta de *pesquisa salva* será salva no armazenamento.

**Configurar o BYOS para consultas de alertas de log**

Vincular uma conta de armazenamento para *alertas* ao seu espaço de trabalho-as consultas de *alertas de log* são salvas em sua conta de armazenamento. 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Após a configuração, qualquer consulta de alerta nova será salva no armazenamento.

## <a name="customer-lockbox-preview"></a>Sistema de Proteção de Dados do Cliente (versão prévia)

O Lockbox fornece a você o controle para aprovar ou rejeitar a solicitação de engenheiro da Microsoft para acessar seus dados durante uma solicitação de suporte.

No Azure Monitor, você tem esse controle sobre os dados em espaços de trabalho vinculados ao seu Log Analytics cluster dedicado. O controle de lockbox se aplica aos dados armazenados em um cluster Log Analytics dedicado, em que é mantido isolado nas contas de armazenamento do cluster em sua assinatura protegida por lockbox.  

Saiba mais sobre [sistema de proteção de dados do cliente para Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Operações de Customer-Managed chave

- **Obter todos os clusters em um grupo de recursos**
  
  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Resposta**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

  ---

- **Obter todos os clusters em uma assinatura**

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Resposta**
    
  A mesma resposta que para ' cluster em um grupo de recursos ', mas no escopo da assinatura.

  ---

- **Atualizar *reserva de capacidade* no cluster**

  Quando o volume de dados para seus espaços de trabalho vinculados é alterado ao longo do tempo e você deseja atualizar o nível de reserva de capacidade adequadamente. Siga o [cluster de atualização](#update-cluster-with-key-identifier-details) e forneça seu novo valor de capacidade. Ele pode estar no intervalo de 1000 a 3000 GB por dia e em etapas de 100. Para um nível superior a 3000 GB por dia, acesse o contato da Microsoft para habilitá-lo. Observe que você não precisa fornecer o corpo da solicitação REST completa, mas deve incluir o SKU:

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

  ---

- **Atualizar *billtype* no cluster**

  A propriedade *billtype* determina a atribuição de cobrança para o cluster e seus dados:
  - *cluster* (padrão) -- A cobrança é atribuída à assinatura que hospeda seu recurso de cluster
  - *workspaces* -- A cobrança é atribuída às assinaturas que hospedam seus workspaces proporcionalmente
  
  Siga o [cluster de atualização](#update-cluster-with-key-identifier-details) e forneça o novo valor de billtype. Observe que você não precisa fornecer o corpo da solicitação REST completa e deve incluir o *billingType*:

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  N/D

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  N/D

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

  ---

- **Desvincular workspace**

  Você precisa de permissões de ' gravação ' no espaço de trabalho e no cluster para executar esta operação. Você pode desvincular um espaço de trabalho do cluster a qualquer momento. Novos dados ingeridos após a operação de desvinculação são armazenados em Log Analytics armazenamento e criptografados com a chave da Microsoft. Você pode consultar os dados que foram ingeridos em seu espaço de trabalho antes e depois do desvincular sem vínculo, desde que o cluster seja provisionado e configurado com uma chave de Key Vault válida.

  Esta operação é assíncrona e pode um tempo para ser concluída.

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Verificar status do link do espaço de trabalho**
  
  Execute a operação get no espaço de trabalho e observe se a propriedade *clusterResourceId* está presente na resposta em *recursos*. Um espaço de trabalho vinculado terá a propriedade *clusterResourceId* .

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

   ```rest
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Excluir o cluster**

  Você precisa de permissões de ' gravação ' no cluster para executar esta operação. Uma operação de exclusão reversível é executada para permitir a recuperação de seu cluster, incluindo seus dados dentro de 14 dias, se a exclusão foi acidental ou intencional. O nome do cluster permanece reservado durante o período de exclusão reversível e você não pode criar um novo cluster com esse nome. Após o período de exclusão reversível, o nome do cluster é liberado e o cluster e seus dados são excluídos permanentemente e não são recuperáveis. Qualquer espaço de trabalho vinculado é desvinculado do cluster na operação de exclusão. Novos dados ingeridos são armazenados no armazenamento do Log Analytics e criptografados com a chave da Microsoft. 
  
  A operação de desvinculação é assíncrona e pode levar até 90 minutos para ser concluída.

  # <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

  N/D

  # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---
  
- **Recuperar o cluster e seus dados** 
  
  Um cluster que foi excluído nos últimos 14 dias está no estado de exclusão reversível e pode ser recuperado com seus dados. Como todos os espaços de trabalho foram desvinculados da exclusão do cluster, você precisa vincular novamente os espaços de trabalho após a recuperação do cluster. A operação de recuperação é atualmente executada manualmente pelo grupo de produtos. Use o Microsoft Channel ou abra a solicitação de suporte para recuperação do cluster excluído.

## <a name="limitations-and-constraints"></a>Limitações e restrições

- Customer-Managed chave tem suporte no cluster de Log Analytics dedicado e adequada para clientes que enviam 1 TB por dia ou mais.

- O número máximo de clusters por região e assinatura é 2

- O máximo de espaços de trabalho vinculados ao cluster é 1000

- Você pode vincular um espaço de trabalho ao cluster e, em seguida, desvinculá-lo. O número de operações de link de espaço de trabalho no espaço de trabalho específico é limitado a 2 em um período de 30 dias.

- O link do espaço de trabalho para o cluster deve ser executado somente depois que você tiver verificado que o provisionamento do cluster Log Analytics foi concluído.  Os dados enviados ao seu workspace antes da conclusão serão removidos e não poderão ser recuperados.

- Customer-Managed criptografia de chave se aplica a dados recentemente ingeridos após o tempo de configuração. Os dados que foram ingeridos antes da configuração, permanecem criptografados com a chave da Microsoft. Você pode consultar dados ingeridos antes e depois da configuração da chave de Customer-Managed de forma direta.

- O Azure Key Vault deve ser configurado como recuperável. Essas propriedades não são habilitadas por padrão e devem ser configuradas usando a CLI ou o PowerShell:<br>
  - [Exclusão Reversível](../../key-vault/general/soft-delete-overview.md)
  - A [proteção de limpeza](../../key-vault/general/soft-delete-overview.md#purge-protection) deve ser ativada para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível.

- A movimentação de cluster para outro grupo de recursos ou assinatura não tem suporte no momento.

- Seus Azure Key Vault, cluster e espaços de trabalho vinculados devem estar na mesma região e no mesmo locatário do Azure Active Directory (AD do Azure), mas podem estar em assinaturas diferentes.

- O link do espaço de trabalho para o cluster falhará se ele estiver vinculado a outro cluster.

- A Lockbox não está disponível atualmente na China. 

- A [criptografia dupla](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) é configurada automaticamente para clusters criados a partir de outubro de 2020 em regiões com suporte. Você pode verificar se o cluster está configurado para criptografia dupla por uma solicitação GET no cluster e observar o valor da `"isDoubleEncryptionEnabled"` propriedade – é `true` para clusters com criptografia dupla habilitada. 
  - Se você criar um cluster e receber um erro "<Region-Name> não dá suporte à criptografia dupla para clusters.", você ainda poderá criar o cluster sem criptografia dupla. Adicione `"properties": {"isDoubleEncryptionEnabled": false}` a propriedade no corpo da solicitação REST.
  - A configuração de criptografia dupla não pode ser alterada após a criação do cluster.

## <a name="troubleshooting"></a>Solução de problemas

- Comportamento com disponibilidade do Key Vault
  - Em operação normal -- O armazenamento faz caches de AEK por curtos períodos e volta para Key Vault para desencapsular periodicamente.
    
  - Erros de conexão transitórios -- O armazenamento trata erros transitórios (tempos limite, falhas de conexão, problemas de DNS), permitindo que as chaves permaneçam no cache um pouco mais tempo e isso supera os pequenos problemas de disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
  - Site ativo -- a indisponibilidade de aproximadamente 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso a Key Vault é restaurado, a consulta fica disponível e os dados temporários armazenados em cache são ingeridos no armazenamento de dados e criptografados com Customer-Managed chave.

  - Taxa de acesso do Key Vault -- A frequência com que o Armazenamento do Azure Monitor acessa o Key Vault para operações de encapsulamento e desencapsulamento é entre 6 e 60 segundos.

- Se você criar um cluster e especificar o keyvaultproperties imediatamente, a operação poderá falhar, pois a política de acesso não poderá ser definida até que a identidade do sistema seja atribuída ao cluster.

- Se você atualizar o cluster existente com keyvaultproperties e a política de acesso à chave ' Get ' estiver ausente no Key Vault, a operação falhará.

- Se você receber um erro de conflito ao criar um cluster, pode ser que você tenha excluído o cluster nos últimos 14 dias e esteja em um período de exclusão reversível. O nome do cluster permanece reservado durante o período de exclusão reversível e você não pode criar um novo cluster com esse nome. O nome é liberado após o período de exclusão reversível quando o cluster é excluído permanentemente.

- Se você atualizar o cluster enquanto uma operação estiver em andamento, a operação falhará.

- Se você não implantar o cluster, verifique se seus Azure Key Vault, cluster e espaços de trabalho vinculados Log Analytics estão na mesma região. Eles podem estar em assinaturas diferentes.

- Se você atualizar a versão de chave no Key Vault e não atualizar os novos detalhes do identificador de chave no cluster, o cluster de Log Analytics continuará usando sua chave anterior e seus dados ficarão inacessíveis. Atualize os novos detalhes do identificador de chave no cluster para retomar a ingestão de dados e a capacidade de consultar dados.

- Algumas operações são longas e podem demorar um pouco para serem concluídas--são elas: criação de cluster, atualização de chave de cluster e exclusão de cluster. Você pode verificar o status da operação de duas maneiras:
  1. ao usar o REST, copie o Azure-AsyncOperation valor da URL da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
  2. Enviar solicitação GET para cluster ou espaço de trabalho e observar a resposta. Por exemplo, espaço de trabalho desvinculado não terá o *clusterResourceId* em *recursos*.

- Mensagens de erro
  
  Criação de cluster:
  -  400--o nome do cluster não é válido. O nome do cluster pode conter caracteres de a-z, A-Z, 0-9 e comprimento de 3-63.
  -  400--o corpo da solicitação é nulo ou está em formato inadequado.
  -  400--o nome do SKU é inválido. Defina o nome do SKU como capacityReservation.
  -  400--a capacidade foi fornecida, mas o SKU não é capacityReservation. Defina o nome do SKU como capacityReservation.
  -  400--capacidade ausente no SKU. Defina o valor de capacidade para 1000 ou superior em etapas de 100 (GB).
  -  400--a capacidade no SKU não está no intervalo. Deve ser no mínimo 1000 e até a capacidade máxima permitida, que está disponível em ' uso e custo estimado ' em seu espaço de trabalho.
  -  400--a capacidade está bloqueada por 30 dias. A redução da capacidade é permitida 30 dias após a atualização.
  -  400--nenhum SKU foi definido. Defina o nome do SKU como capacityReservation e o valor da capacidade como 1000 ou superior em etapas de 100 (GB).
  -  400--a identidade é nula ou está vazia. Defina a identidade com o tipo systemAssigned.
  -  400--keyvaultproperties está definido na criação. Atualize keyvaultproperties após a criação do cluster.
  -  400--a operação não pode ser executada agora. A operação assíncrona está em um estado diferente de Succeeded. O cluster deve concluir sua operação antes que qualquer operação de atualização seja executada.

  Atualização do cluster
  -  400--o cluster está em estado de exclusão. A operação assíncrona está em andamento. O cluster deve concluir sua operação antes que qualquer operação de atualização seja executada.
  -  400--keyvaultproperties não está vazio, mas tem um formato inválido. Consulte [atualização do identificador de chave](#update-cluster-with-key-identifier-details).
  -  400--falha ao validar a chave no Key Vault. Pode ser devido à falta de permissões ou quando a chave não existe. Verifique se você [definiu a política de acesso e chave](#grant-key-vault-permissions) no Key Vault.
  -  400--a chave não é recuperável. Key Vault deve ser definido como exclusão reversível e proteção de limpeza. Consulte a [documentação do Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400--a operação não pode ser executada agora. Aguarde a conclusão da operação assíncrona e tente novamente.
  -  400--o cluster está em estado de exclusão. Aguarde a conclusão da operação assíncrona e tente novamente.

  Get do cluster:
    -  404--cluster não encontrado, o cluster pode ter sido excluído. Se você tentar criar um cluster com esse nome e entrar em conflito, o cluster estará em exclusão reversível por 14 dias. Você pode contatar o suporte para recuperá-lo ou usar outro nome para criar um novo cluster. 

  Exclusão de cluster
    -  409--não é possível excluir um cluster no estado de provisionamento. Aguarde a conclusão da operação assíncrona e tente novamente.

  Link do espaço de trabalho:
  -  404--espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi excluído.
  -  409--operação de desvinculação ou link do espaço de trabalho no processo.
  -  400--cluster não encontrado, o cluster especificado não existe ou foi excluído. Se você tentar criar um cluster com esse nome e entrar em conflito, o cluster estará em exclusão reversível por 14 dias. Você pode contatar o suporte para recuperá-lo.

  Desvincular espaço de trabalho:
  -  404--espaço de trabalho não encontrado. O espaço de trabalho especificado não existe ou foi excluído.
  -  409--operação de desvinculação ou link do espaço de trabalho no processo.