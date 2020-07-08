---
title: Chave do Azure Monitor gerenciada pelo cliente
description: Informações e etapas para configurar a Chave Gerenciada pelo Cliente (CMK) para criptografar dados em seus workspaces do Log Analytics usando uma chave do Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/05/2020
ms.openlocfilehash: 607f622bc484883ecbeae0552eecc9561cf4c3ef
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85969595"
---
# <a name="azure-monitor-customer-managed-key"></a>Chave do Azure Monitor gerenciada pelo cliente 

Este artigo contém informações básicas e etapas para configurar chaves gerenciadas pelo cliente (CMK) para seus workspaces do Log Analytics. Uma vez configurada, todos os dados enviados para seus workspaces são criptografados com sua chave do Azure Key Vault.

Recomendamos revisar [Limitações e restrições](#limitationsandconstraints) abaixo antes da configuração.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da chave gerenciada pelo cliente (CMK)

[Criptografia em Repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança em organizações. Você pode permitir que o Azure gerencie completamente a Criptografia em Repouso, enquanto você tem várias opções para gerenciar de forma rigorosa a criptografia ou as chaves de criptografia.

O Azure Monitor garante que todos os dados sejam criptografados em repouso usando chaves gerenciadas pelo Azure. O Azure Monitor também fornece uma opção para a criptografia de dados usando sua própria chave armazenada em seu [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) e acessada pelo armazenamento usando a autenticação de [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pelo sistema. Essa chave pode ser de [software ou de hardware protegido por HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

O Azure Monitor usa a criptografia de modo idêntico à [operação da criptografia de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

A CMK permite controlar o acesso aos seus dados e revogá-lo a qualquer momento. O armazenamento do Azure Monitor sempre respeita as alterações nas permissões de chave em uma hora. Os dados ingeridos nos últimos 14 dias também são mantidos no cache de acesso frequente (com suporte de SSD) para uma operação de mecanismo de consulta eficiente. Esses dados permanecem criptografados com chaves da Microsoft, independentemente da configuração da CMK, mas seu controle sobre os dados SSD segue a  [revogação de chave](#cmk-kek-revocation). Estamos trabalhando para ter dados SSD criptografados com a CMK no segundo semestre de 2020.

O recurso CMK é fornecido em clusters dedicados do Log Analytics. Para verificar se temos a capacidade necessária em sua região, exigimos que sua assinatura seja permitida antecipadamente. Use seu contato da Microsoft para obter a assinatura permitida antes de começar a configurar o CMK.

O  [modelo de preços de clusters do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)  usa reservas de capacidade que começam no nível de 1000 GB/dia.

## <a name="how-cmk-works-in-azure-monitor"></a>Como a CMK funciona no Azure Monitor

O Azure Monitor utiliza a identidade gerenciada atribuída pelo sistema para conceder acesso ao seu Azure Key Vault. A identidade gerenciada atribuída pelo sistema só pode ser associada a um único recurso do Azure, enquanto a identidade do cluster do Log Analytics tem suporte no nível do cluster. Isso determina que a capacidade da CMK seja entregue em um cluster dedicado do Log Analytics. Para dar suporte a CMK em vários workspaces, um novo recurso de *Cluster* do Log Analytics funciona como uma conexão de identidade intermediária entre seu Key Vault e seus workspaces do Log Analytics. O armazenamento de cluster do Log Analytics usa a identidade gerenciada que \'está associada ao recurso de *cluster* para autenticar no Azure Key Vault via Azure Active Directory. 

Após a configuração da CMK, todos os dados ingeridos em workspaces associados ao seu recurso de *cluster* são criptografados com sua chave no Key Vault. Você pode desassociar workspaces do recurso de *cluster* a qualquer momento. Novos dados são ingeridos no armazenamento do Log Analytics e criptografados com a chave da Microsoft, enquanto você pode consultar facilmente os dados novos e antigos.


![Visão geral da CMK](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Recurso de *cluster* do Log Analytics com identidade gerenciada com permissões para Key Vault -- A identidade é propagada para o armazenamento de cluster do Log Analytics dedicado subjacente
3. Cluster dedicado de Log Analytics
4. Workspaces associados ao recurso de *cluster* para criptografia de CMK

## <a name="encryption-keys-operation"></a>Operação de chaves de criptografia

Há três tipos de chaves envolvidas na criptografia de dados de Armazenamento:

- **KEK** - Chave de Criptografia da Chave (CMK)
- **AEK** - Chave de Criptografia da Conta
- **DEK** - Chave de Criptografia dos Dados

As seguintes regras se aplicam:

- As contas de armazenamento de cluster do Log Analytics geram uma chave de criptografia exclusiva para cada Conta de Armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves usadas para criptografar cada bloco de dados gravados no disco.

- Ao configurar sua chave no Key Vault e faz referência a ela no recurso de *cluster*, o Armazenamento do Microsoft Azure envia solicitações para seu Azure Key Vault para encapsular e desencapsular o AEK para executar operações de criptografia e descriptografia de dados.

- Seu KEK nunca deixa seu Key Vault e, no caso de uma chave HSM, ele nunca deixa o hardware.

- O Armazenamento do Microsoft Azure usa a identidade gerenciada associada ao recurso de *cluster* para autenticar e acessar o Azure Key Vault por meio do Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento da CMK

1. Permitindo a assinatura – o recurso CMK é fornecido em clusters de Log Analytics dedicados. Para verificar se temos a capacidade necessária em sua região, exigimos que sua assinatura seja permitida antecipadamente. Use seu contato da Microsoft para obter a assinatura permitida.
2. Criar o Azure Key Vault e armazenar a chave
3. Criar um recurso de *cluster*
4. Conceder permissões ao Key Vault
5. Associar workspaces do Log Analytics

O procedimento não tem suporte no portal do Azure e o provisionamento é executado por meio de solicitações do PowerShell ou REST.

> [!IMPORTANT]
> Qualquer solicitação REST deve incluir um token de autorização de portador no cabeçalho da solicitação.

Por exemplo:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Onde *eyJ0eXAiO...* representa o token de autorização completo. 

Você pode adquirir o token usando um destes métodos:

1. Usar método de [Registros de aplicativo](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens).
2. No portal do Azure
    1. Navegue até portal do Azure enquanto estiver na "ferramenta de desenvolvedor" (F12)
    1. Procure a cadeia de caracteres de autorização em "Cabeçalhos de solicitação" em uma das instâncias de "batch?api-version". Ele é semelhante a: "authorization: Bearer eyJ0eXAiO....". 
    1. Copie e adicione-o à sua chamada à API de acordo com os exemplos abaixo.
3. Navegue até o site de documentação REST do Azure. Pressione "Experimente" em qualquer API e copie o token de portador.

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de status

Algumas das operações neste procedimento de configuração são executadas de forma assíncrona porque não podem ser concluídas rapidamente. Ao usar solicitações REST na configuração, a resposta inicialmente retorna um código de status HTTP 200 (OK) e o cabeçalho com a propriedade *Azure-AsyncOperation* quando aceita:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Em seguida, você pode verificar o status da operação assíncrona enviando uma solicitação GET para o valor do cabeçalho *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

A resposta contém informações sobre a operação e seu *status*. Pode ser um dos seguintes:

A operação está em andamento
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

A operação de atualização do identificador de chave está em andamento
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

A exclusão do recurso de *cluster* está em andamento -- Quando você exclui um recurso de *Cluster* que tem workspaces associados, uma operação de desassociação é executada para cada um dos workspaces em operações assíncronas que podem demorar.
Isso não é relevante quando você exclui um *cluster* sem workspace associado -- Nesse caso, o recurso de *cluster* é excluído imediatamente.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operação concluída
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Falha na operação
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Permitindo a assinatura para a implantação do CMK

O recurso CMK é fornecido em clusters dedicados do Log Analytics.Para verificar se temos a capacidade necessária em sua região, exigimos que sua assinatura seja permitida antecipadamente. Use seus contatos na Microsoft para fornecer suas IDs de assinaturas.

> [!IMPORTANT]
> O recurso CMK é regional. Seu Azure Key Vault, recurso de *cluster* e workspaces do Log Analytics associados devem estar na mesma região, mas podem estar em assinaturas diferentes.

### <a name="storing-encryption-key-kek"></a>Armazenamento da chave de criptografia (KEK)

Crie ou use um Azure Key Vault que você já tem para gerar ou importe uma chave a ser usada para criptografia de dados. O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos dados no Azure Monitor. Você pode verificar essa configuração em propriedades no seu Key Vault, a *Exclusão temporária* e a *Proteção contra limpeza* devem ser habilitadas.

![Configurações de exclusão temporária e proteção contra limpeza](media/customer-managed-keys/soft-purge-protection.png)

Essas configurações estão disponíveis por meio da CLI e do PowerShell:
- [Exclusão Reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- A [proteção contra limpeza](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) protege contra a exclusão forçada do segredo/cofre mesmo após a exclusão temporária

### <a name="create-cluster-resource"></a>Criar recurso de *cluster*

Esse recurso é usado como uma conexão de identidade intermediária entre seu Key Vault e seus workspaces do Log Analytics. Depois de receber a confirmação de que suas assinaturas foram permitidas, crie um Log Analytics recurso de *cluster* na região em que os espaços de trabalho estão localizados.

Você deve especificar o nível de *reserva de capacidade* (SKU) ao criar um recurso de *cluster*. O nível de *reserva de capacidade* pode estar no intervalo de 1.000 a 2.000 GB por dia e você pode atualizá-lo em etapas de 100 posteriormente. Se você precisar de um nível de reserva de capacidade maior que 2.000 GB por dia, escreva para LAIngestionRate@microsoft.com. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

A propriedade *billingType* determina atribuição de cobrança para o recurso de *cluster* e seus dados:
- *Cluster* (padrão)--os custos de reserva de capacidade para o cluster são atribuídos ao recurso de *cluster* .
- *Espaços de trabalho* – os custos de reserva de capacidade para o cluster são atribuídos proporcionalmente aos espaços de trabalho no cluster, com o recurso de *cluster* sendo cobrado como parte do uso se o total de dados ingeridos para o dia estiver sob a reserva de capacidade. Consulte [log Analytics clusters dedicados](manage-cost-storage.md#log-analytics-dedicated-clusters) para saber mais sobre o modelo de preços do cluster. 

> [!NOTE]
> * Depois de criar o recurso de *cluster*, você pode atualizá-lo com *SKU*, *keyVaultProperties* ou *billingType* usando a solicitação PATCH REST.
> * Você pode atualizar o *billtype* usando a solicitação REST atualmente, isso não tem suporte no PowerShell

Esta operação é assíncrona e pode um tempo para ser concluída.

> [!IMPORTANT]
> Copie e salve a resposta, pois você precisará das informações nas próximas etapas.
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity "daily-ingestion-gigabyte" 
```

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

A identidade é atribuída ao recurso de *cluster* no momento da criação.

**Resposta**

200 OK e cabeçalho.

Embora o provisionamento do cluster do Log Analytics demore um pouco para ser concluído, você pode verificar o estado de provisionamento de duas maneiras:

1. Copie o valor da URL de Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no recurso de *cluster* e examine o valor *provisioningState*. É *ProvisioningAccount* ao longo do provisionamento e *Succeeded* quando concluído.

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

O GUID "principalId" é gerado pelo serviço de identidade gerenciada para o recurso de *cluster*.

### <a name="grant-key-vault-permissions"></a>Conceder permissões do Key Vault

Atualize seu Key Vault com uma nova política de acesso para conceder permissões para o recurso de *cluster* . Essas permissões são usadas pelo Armazenamento do Azure Monitor subjacente para criptografia de dados. Abra o Key Vault no portal do Azure e clique em “Políticas de acesso” e, em seguida, em “+ Adicionar política de acesso” para criar uma política com estas configurações:

- Permissões da chave: selecione as permissões “Obter”, “Encapsular Chave” e “Desencapsular chave”.
- Selecionar entidade de segurança: Insira o nome do recurso de *cluster* ou o valor da ID principal que foi retornado na resposta na etapa anterior.

![conceder permissões do Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Obter* é necessária para verificar se o Key Vault está configurado como recuperável para proteger sua chave e o acesso aos seus dados do Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar recurso de cluster com detalhes do identificador de chave

Esta etapa é executada durante as atualizações de versão de chave inicial e futura no seu Key Vault. Ela informa o Armazenamento do Azure Monitor sobre a versão da chave a ser usada para criptografia de dados. Quando atualizada, sua nova chave está sendo usada para encapsular e desencapsular para a chave de armazenamento (AEK).

Para atualizar o recurso de *cluster* com detalhes do seu *identificador de chave* do Key Vault, selecione a versão atual da sua chave no Azure Key Vault para obter os detalhes do identificador de chave.

![Conceder permissões do Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Atualize KeyVaultProperties do recurso de *cluster* com os detalhes do identificador de chave.

Esta operação é assíncrona ao atualizar os detalhes do identificador de chave e pode demorar um pouco para ser concluída. É síncrono ao atualizar o valor da capacidade.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> Você pode atualizar o *SKU*do recurso de *cluster* , *keyvaultproperties* ou *billtype* usando patch.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
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

**Resposta**

200 OK e cabeçalho.
A propagação do identificador de chave demora alguns minutos para ser concluída. Você pode verificar o status de atualização de duas maneiras:
1. Copie o valor da URL de Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no recurso de *cluster* e examine as propriedades de *KeyVaultProperties*. Os detalhes do identificador de chave atualizados recentemente devem retornar na resposta.

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

### <a name="workspace-association-to-cluster-resource"></a>Associação de workspace ao recurso de *cluster*

Você precisa ter permissões de “gravação” tanto no workspace, quanto no recurso de *cluster* para executar essa operação, o que inclui estas ações:

- No workspace: Microsoft.OperationalInsights/workspaces/write
- No recurso de *cluster*: Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Esta etapa deve ser executada somente após a conclusão do provisionamento de cluster do Log Analytics. Se você associar workspaces e ingerir dados antes do provisionamento, os dados ingeridos serão descartados e não poderão ser recuperados.

Esta operação é assíncrona e pode um tempo para ser concluída.

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

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

**Resposta**

200 OK e cabeçalho.

Os dados ingeridos são armazenados criptografados com a chave gerenciada após a operação de associação, que pode levar até 90 minutos para ser concluída. Você pode verificar o estado de associação do workspace de duas maneiras:

1. Copie o valor da URL de Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação [Workspaces - Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) e observe a resposta. O workspace associado terá um clusterResourceId em "recursos".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Resposta**

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

## <a name="cmk-kek-revocation"></a>Revogação da CMK (KEK)

Você pode revogar o acesso aos dados se desabilitar sua chave ou excluir a política de acesso ao recurso de *cluster* no seu Key Vault. O armazenamento de cluster do Log Analytics sempre respeitará as alterações nas permissões de chave feita a mais de uma hora e o Armazenamento ficará indisponível. Todos os novos dados ingeridos em workspaces associados ao seu recurso de  *cluster*  são descartados e não poderão ser recuperados, os dados ficarão inacessíveis e as consultas a esses workspaces falharão. Os dados anteriormente ingeridos permanecem no armazenamento, desde que seu recurso de *cluster* e seus workspaces não sejam excluídos. Os dados inacessíveis são administrados pela política de retenção de dados e serão limpos quando a retenção for alcançada. 

Os dados ingeridos nos últimos 14 dias também são mantidos no cache frequente (com suporte de SSD) para uma operação de mecanismo de consulta eficiente. Ele é excluído na operação de revogação de chave e se torna inacessível também.

O armazenamento pesquisa periodicamente seu Key Vault para tentar desencapsular a chave de criptografia e, depois de acessado, a ingestão de dados e a consulta são retomados em até 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação da CMK (KEK)

A rotação da CMK requer atualização explícita para o recurso de *cluster* com a nova versão de chave no Azure Key Vault. Siga as instruções na etapa “Atualizar recurso de *Cluster* com os detalhes do identificador de Chave”. Se você não atualizar os detalhes do novo identificador de chave no recurso de *cluster*, o armazenamento de cluster do Log Analytics continuará usando sua chave anterior para criptografia. Se você desabilitar ou excluir sua chave antiga antes de atualizar a nova chave no recurso de *cluster*, você entrará no estado de [revogação de chave](#cmk-kek-revocation).

Todos os seus dados continuam acessíveis após a operação de revezamento de chaves, já que os dados são sempre criptografados com a AEK (Chave de Criptografia de Conta), embora a AEK seja agora criptografada com sua nova versão de KEK (Chave de Criptografia de Chave) no Key Vault.

## <a name="saving-queries-protected-with-cmk"></a>Salvando consultas protegidas com CMK

A linguagem de consulta usada em Log Analytics é expressiva e pode conter informações confidenciais em comentários que você adiciona a consultas ou na sintaxe de consulta. Algumas organizações exigem que essas informações sejam mantidas protegidas como parte da política de CMK e você precisa salvar suas consultas criptografadas com sua chave. Azure Monitor permite armazenar *pesquisas salvas* e consultas de *alertas de log* em sua própria conta de armazenamento que você conecta ao seu espaço de trabalho. 

> Observação o CMK para consultas usadas em pastas de trabalho e painéis do Azure ainda não tem suporte. Essas consultas permanecem criptografadas com a chave da Microsoft.  

Com o BYOS (Traga seu próprio armazenamento), o serviço carrega consultas na conta de armazenamento que você controla. Isso significa que você controla a [política de criptografia em repouso](https://docs.microsoft.com/azure/storage/common/encryption-customer-managed-keys) usando a mesma chave usada para criptografar dados no cluster log Analytics ou em uma chave diferente. No entanto, você será responsável pelos custos associados a essa conta de armazenamento. 

**Considerações antes de definir CMK para consultas**
* Você precisa ter permissões de ' gravação ' no espaço de trabalho e na conta de armazenamento
* Certifique-se de criar sua conta de armazenamento na mesma região que o espaço de trabalho Log Analytics está localizado
* As *pesquisas salvas* no armazenamento são consideradas como artefatos de serviço e seu formato pode ser alterado
* As *pesquisas de gravações* existentes são removidas do seu espaço de trabalho. Copie e *salve as pesquisas* necessárias antes da configuração. Você pode exibir suas *pesquisas salvas* usando este [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/Get-AzOperationalInsightsSavedSearch?view=azps-4.2.0)
* Não há suporte para o histórico de consulta e você não poderá ver as consultas que executou
* Você pode associar uma única conta de armazenamento ao espaço de trabalho com a finalidade de salvar consultas, mas pode ser usado partir as *pesquisas salvas* e as consultas *de alertas de log*
* Não há suporte para fixar no painel

**Configuração de BYOS para consultas**

Associe uma conta de armazenamento a um DataSourceType de *consulta* ao seu espaço de trabalho. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "resource-group-name"storage-account-name"resource-group-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-03-01-preview
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

Após a configuração, qualquer nova consulta de *pesquisa salva* será salva no armazenamento.

**Configuração de BYOS para alertas de log**

Associe uma conta de armazenamento com *alertas* DataSourceType ao seu espaço de trabalho. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "resource-group-name"storage-account-name"resource-group-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-03-01-preview
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

Após a configuração, qualquer consulta de alerta nova será salva no armazenamento.

## <a name="cmk-management"></a>Gerenciamento de CMK

- **Obter todos os recursos de *cluster* para um grupo de recursos**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
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

- **Obter todos os recursos de *cluster* para uma assinatura**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Resposta**
    
  A mesma resposta para “recursos de *cluster* para um grupo de recursos”, mas no escopo da assinatura.

- **Atualizar *reserva de capacidade* no recurso de *cluster***

  Quando o volume de dados para seus workspaces associados muda ao longo do tempo e você deseja atualizar o nível de reserva de capacidade adequadamente. Siga a [atualização de recurso de *cluster*](#update-cluster-resource-with-key-identifier-details) e forneça o novo valor de capacidade. Ele pode estar no intervalo de 1.000 a 2.000 GB por dia e em etapas de 100. Para um nível superior a 2.000 GB por dia, entre em contato com a Microsoft para habilitá-lo. Observe que você não precisa fornecer o corpo da solicitação REST completa e deve incluir o sku:

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Atualizar *billingType* no recurso de *cluster***

  A propriedade *billingType* determina atribuição de cobrança para o recurso de *cluster* e seus dados:
  - *cluster* (padrão) -- A cobrança é atribuída à assinatura que hospeda seu recurso de cluster
  - *workspaces* -- A cobrança é atribuída às assinaturas que hospedam seus workspaces proporcionalmente
  
  Siga a [atualização do recurso de *cluster*](#update-cluster-resource-with-key-identifier-details) e forneça o novo valor de billingType. Observe que você não precisa fornecer o corpo da solicitação REST completa e deve incluir o *billingType*:

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Desassociar workspace**

  Você precisa de permissões de “gravação” no workspace e no recurso de *cluster* para executar essa operação. Você pode desassociar um workspace do seu recurso de *cluster* quando quiser. Novos dados ingeridos após a operação de desassociação são armazenados no armazenamento do Log Analytics e criptografados com a chave da Microsoft. Você pode consultar os dados que foram ingeridos no seu workspace antes e depois da desassociação sem interrupções, desde que o recurso de *cluster* seja provisionado e configurado com uma chave válida do Key Vault.

  Esta operação é assíncrona e pode um tempo para ser concluída.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK e cabeçalho.

  Dados ingeridos após a operação de desassociação são armazenados no armazenamento do Log Analytics. Isso pode levar 90 minutos para ser concluído. Você pode verificar o estado de desassociação do workspace de duas maneiras:

  1. Copie o valor da URL de Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
  2. Envie uma solicitação [Workspaces - Obter](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) e observe a resposta, o workspace desassociado não terá *clusterResourceId* em *recursos*.

- **Verificar o status da associação do espaço de trabalho**
  
  Execute a operação get no espaço de trabalho e observe se a propriedade *clusterResourceId* está presente na resposta em *recursos*. O espaço de trabalho associado terá a propriedade *clusterResourceId* .

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Excluir seu recurso de *cluster***

  Você precisa de permissões de “gravação” no recurso de *cluster* para executar essa operação. Uma operação de exclusão temporária é executada para permitir a recuperação do seu recurso de *cluster*, incluindo seus dados dentro de 14 dias, seja a exclusão acidental ou intencional. O nome do recurso de *cluster* permanece reservado durante o período de exclusão temporária e você não pode criar um novo cluster com esse nome. Após o período de exclusão temporária, o nome do recurso de *cluster* é liberado, seu recurso de *cluster* e dados são excluídos permanentemente e não podem ser recuperados. Qualquer workspace associado é desassociado do recurso de *cluster* na operação de exclusão. Novos dados ingeridos são armazenados no armazenamento do Log Analytics e criptografados com a chave da Microsoft. 
  
  A operação de desassociação dos workspaces é assíncrona e pode levar até 90 minutos para ser concluída.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK

- **Recuperar o recurso de *cluster* e seus dados** 
  
  Um recurso de *cluster* que foi excluído nos últimos 14 dias está no estado de exclusão temporária e pode ser recuperado com seus dados. Como todos os workspaces foram desassociados do recurso de *cluster* com a exclusão do recurso de *cluster*, você precisará associar novamente os workspaces após a recuperação para criptografia de CMK. A operação de recuperação é executada manualmente pelo grupo de produtos no momento. Use seu canal da Microsoft para solicitações de recuperação.

## <a name="limitationsandconstraints"></a>Limitações e restrições

\- A CMK tem suporte no cluster dedicado do Log Analytics e é adequada para clientes que enviam 1 TB por dia ou mais.

\- O número máximo de recursos de  *cluster*  por região e assinatura é 2

\- Você pode associar um workspace ao seu recurso de  *cluster* e, em seguida, desassociá-lo se a CMK não for necessária para o workspace. O número de associações de workspace em um workspace específico em um período de 30 dias é limitado a 2

\- A associação do workspace ao recurso de  *cluster* deve ser feita SOMENTE depois de verificar se o provisionamento do cluster do Log Analytics foi concluído. Os dados enviados ao seu workspace antes da conclusão serão removidos e não poderão ser recuperados.

\- A criptografia de CMK se aplica a dados recentemente ingeridos após a     configuração de CMK. Os dados que foram ingeridos antes da     configuração de CMK, permanecem criptografados com a chave da Microsoft. Você pode consultar     dados ingeridos antes e depois da configuração do CMK sem problemas.

\- O Azure Key Vault deve ser configurado como recuperável.Essas propriedades não são habilitadas por padrão e devem ser configuradas usando a CLI ou o PowerShell:

  - [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 
 Exclusão      reversível deve ser ativado na    -  [proteção](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)   de limpeza deve ser ativado para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível.

A transferência do recurso de - *cluster* para outro grupo de recursos ou assinatura     não tem suporte no momento.

\- Seu Azure Key Vault, recurso de  *cluster*  e workspaces associados precisam estar na mesma região, no mesmo locatário do Azure Active Directory, mas podem estar em assinaturas diferentes.

\- A associação de workspace a um recurso de  *cluster* falhará se ele estiver     associado a outro recurso de  *cluster* 

## <a name="troubleshooting"></a>Solução de problemas

- Comportamento com disponibilidade do Key Vault
  - Em operação normal -- O armazenamento faz caches de AEK por curtos períodos e volta para Key Vault para desencapsular periodicamente.
    
  - Erros de conexão transitórios -- O armazenamento trata erros transitórios (tempos limite, falhas de conexão, problemas de DNS), permitindo que as chaves permaneçam no cache um pouco mais tempo e isso supera os pequenos problemas de disponibilidade. As capacidades de consulta e ingestão continuam sem interrupção.
    
  - Site ativo -- a indisponibilidade de aproximadamente 30 minutos fará com que a conta de Armazenamento fique indisponível. A capacidade de consulta não está disponível e os dados ingeridos são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso ao Key Vault for restaurado, a consulta fica disponível e os dados temporários armazenados em cache são ingeridos no armazenamento de dados e criptografados com a CMK.

  - Taxa de acesso do Key Vault -- A frequência com que o Armazenamento do Azure Monitor acessa o Key Vault para operações de encapsulamento e desencapsulamento é entre 6 e 60 segundos.

- Se você criar um recurso de *cluster* e especificar KeyVaultProperties imediatamente, a operação poderá falhar, pois a política de acesso não poderá ser definida até que a identidade do sistema seja atribuída ao recurso de *cluster*.

- Se você atualizar o recurso de *cluster* existente com KeyVaultProperties e a Política de Acesso de chave “Get” estiver ausente no Key Vault, a operação falhará.

- Se você receber um erro de conflito ao criar um recurso de *cluster* pode ser que você tenha excluído seu recurso de *cluster* nos últimos 14 dias e ele esteja em um período de exclusão temporária. O nome do recurso de *cluster* permanece reservado durante o período de exclusão temporária e você não pode criar um novo cluster com esse nome. O nome é liberado após o período de exclusão temporária quando o recurso de *cluster* é excluído permanentemente.

- Se você atualizar seu recurso de *cluster* enquanto uma operação estiver em andamento, a operação falhará.

- Se você não implantar o recurso de *cluster*, verifique se seu recurso de  *cluster* do Azure Key Vault e os workspaces do Log Analytics associados estão na mesma região. Eles podem estar em assinaturas diferentes.

- Se você atualizar a versão de chave no Key Vault e não atualizar os novos detalhes do identificador de chave no recurso de *cluster*, o cluster de Log Analytics continuará usando sua chave anterior e seus dados ficarão inacessíveis. Atualize os novos detalhes do identificador de chave no recurso de *cluster* para retomar a ingestão de dados e a capacidade de consultar dados.

- Algumas operações são longas e podem demorar um pouco para serem concluídas--são elas *: criação de cluster,* atualização de chave de *cluster* e exclusão de *cluster* . Você pode verificar o status da operação de duas maneiras:
  1. ao usar o REST, copie o valor da URL Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
  2. Enviar solicitação GET para *cluster* ou espaço de trabalho e observar a resposta. Por exemplo, o espaço de trabalho desassociado não terá o *clusterResourceId* em *recursos*.

- Para obter suporte e ajuda relacionados à chave gerenciada pelo cliente, use seus contatos na Microsoft.
