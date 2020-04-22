---
title: Configuração da chave gerenciada pelo cliente do Azure Monitor
description: Informações e etapas para configurar cmk (Customer-Managed Key, chave gerenciada pelo cliente) para criptografar dados em seus espaços de trabalho do Log Analytics usando uma chave Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758809"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Configuração da chave gerenciada pelo cliente do Azure Monitor 

Este artigo fornece informações e etapas de fundo para configurar cmk (Customer-Managed Keys, chaves gerenciadas pelo cliente) para seus espaços de trabalho do Log Analytics e componentes do Application Insights. Uma vez configurados, todos os dados enviados para seus espaços de trabalho ou componentes são criptografados com a chave Azure Key Vault.

Recomendamos que você [revise limitações e restrições](#limitations-and-constraints) abaixo antes da configuração.

## <a name="disclaimers"></a>Avisos de Isenção de Responsabilidade

- O Azure Monitor CMK é um recurso de acesso antecipado e habilitado para assinaturas registradas.

- A implantação do CMK descrita neste artigo é entregue na qualidade da produção e suportada como tal, embora seja um recurso de acesso antecipado.

- O recurso CMK é fornecido em um cluster dedicado de armazenamento de dados, que é um cluster Azure Data Explorer (ADX) e adequado para clientes que enviam 1 TB por dia ou mais. 

- O modelo de preços CMK não está disponível no momento e não está coberto neste artigo. Um modelo de preços para cluster ADX dedicado é esperado no segundo trimestre do ano civil (CY) 2020 e se aplicará a quaisquer implantações cmk existentes.

- Este artigo descreve a configuração CMK para espaços de trabalho do Log Analytics. Os componentes CMK for Application Insights também são suportados usando este artigo, enquanto as diferenças são listadas no apêndice.

> [!NOTE]
> O Log Analytics e o Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta.
> Estamos reunindo essas duas lojas através da integração do Application Insights no Log Analytics para criar uma única loja unificada de logs no Azure Monitor. Essa mudança está prevista para o segundo trimestre do ano civil de 2020. Se você não tiver que implantar o CMK para seus dados do Application Insights até lá, recomendamos esperar a conclusão da consolidação, uma vez que tais implantações serão interrompidas pela consolidação e você terá que reconfigurar o CMK após a migração para o espaço de trabalho do Log Analytics. O mínimo de 1 TB por dia se aplica ao nível de cluster e até que a consolidação seja concluída durante o segundo trimestre, o Application Insights e o Log Analytics requerem clusters separados.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da chave gerenciada pelo cliente (CMK)

[A criptografia no Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança nas organizações. Você pode permitir que o Azure gerencie completamente a criptografia em repouso, enquanto você tem várias opções para gerenciar de perto as chaves de criptografia ou criptografia.

O armazenamento de dados do Azure Monitor garante que todos os dados sejam criptografados em repouso usando chaves gerenciadas pelo Azure enquanto armazenadas no Azure Storage. O Azure Monitor também fornece uma opção para criptografia de dados usando sua própria chave armazenada no [Seu Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), que é acessado usando autenticação de identidade [gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pelo sistema. Esta chave pode ser protegida por [software ou hardware-HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
O uso de criptografia do Monitor Do Azure é idêntico ao modo como a [criptografia do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) opera.

A freqüência que o Azure Monitor Storage acessa o Key Vault para operações de envoltório e desembrulhado é entre 6 e 60 segundos.O Azure Monitor Storage sempre respeita as alterações nas permissões-chave dentro de uma hora.

Os dados ingeridos nos últimos 14 dias também são mantidos em hot-cache (apoiado por SSD) para uma operação eficiente do mecanismo de consulta. Esses dados permanecem criptografados com as chaves da Microsoft, independentemente da configuração do CMK, mas estamos trabalhando para que o SSD seja criptografado com CMK no primeiro semestre de 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Como o CMK funciona no Monitor Azure

O Azure Monitor aproveita a identidade gerenciada atribuída ao sistema para conceder acesso ao seu Cofre de Chaves Do Azure.A identidade gerenciada atribuída ao sistema só pode ser associada a um único recurso do Azure. A identidade do azure Monitor data-store (cluster ADX) é suportada no nível de cluster e isso dita que o recurso CMK é fornecido em um cluster ADX dedicado. Para suportar o CMK em vários espaços de trabalho, um novo recurso Log Analytics *(Cluster)* funciona como uma conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho do Log Analytics. Esse conceito está em conformidade com a restrição de identidade atribuída ao Sistema e a identidade é mantida entre o cluster ADX e o recurso Log Analytics *Cluster,* enquanto os dados de todos os espaços de trabalho associados são protegidos com a chave Key Vault. O armazenamento em cluster ADX de\'base usa a identidade gerenciada associada ao recurso *Cluster* para autenticar e acessar seu Azure Key Vault via Azure Active Directory.

![Visão geral do CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Cofre de Chaves do Cliente.
2.    Recurso log analytics *cluster* do cliente que gerencia a identidade com permissões para o Key Vault – A identidade é suportada no nível de armazenamento de dados (cluster ADX).
3.    Cluster ADX dedicado ao AZure Monitor.
4.    Os espaços de trabalho do cliente associados ao recurso *Cluster* para criptografia CMK.

## <a name="encryption-keys-management"></a>Gerenciamento de chaves de criptografia

Existem 3 tipos de chaves envolvidas na criptografia de dados de armazenamento:

- **KEK** - Chave de criptografia de chave (CMK)
- **AEK** - Chave de criptografia de conta
- **DEK** - Chave de criptografia de dados

As seguintes regras se aplicam:

- As contas de armazenamento ADX geram uma chave de criptografia exclusiva para cada conta de armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves usadas para criptografar cada bloco de dados gravados em disco.

- Quando você configura sua chave no Key Vault e a faz referência no recurso *Cluster,* o Azure Storage envia solicitações ao seu Azure Key Vault para embrulhar e desembrulhar o AEK para executar operações de criptografia de dados e descriptografia.

- Seu KEK nunca sai do seu Cofre de Chaves e, no caso de uma chave HSM, ele nunca sai do hardware.

- O Azure Storage usa a identidade gerenciada associada ao recurso *Cluster* para autenticar e acessar o Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento cmk

Para a configuração cmk do Application Insights, siga o conteúdo do apêndice para as etapas 3 e 6.

1. Whitelisting de assinatura - isso é necessário para este recurso de acesso antecipado
2. Criando o Azure Key Vault e armazenando a chave
3. Criando um recurso *de cluster*
4. Provisionamento do cluster Azure Monitor (cluster ADX)
5. Concessão de permissões ao seu Cofre de Chaves
6. Associando espaços de trabalho do Log Analytics

O procedimento não é suportado na UI atualmente e o processo de provisionamento é realizado via API REST.

> [!IMPORTANT]
> Qualquer solicitação de API deve incluir um token de autorização do Portador no cabeçalho de solicitação.

Por exemplo:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Onde *eyJ0eXAiO....* representa o token de autorização total. 

Você pode adquirir o token usando um desses métodos:

1. Use o método [de registros do Aplicativo.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
2. No portal do Azure
    1. Navegue até o portal Azure enquanto estiver em "ferramenta de desenvolvedor" (F12)
    1. Procure a seqüência de autorização em "Cabeçalhos de solicitação" em uma das instâncias "batch?api-version". Parece: "Autorização: Portador eyJ0eXAiO....". 
    1. Copie e adicione à sua chamada de API de acordo com os exemplos abaixo.
3. Navegue até o site de documentação do Azure REST. Pressione "Experimente" em qualquer API e copie o token bearer.

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de status

Algumas das operações neste procedimento de configuração são executadas de forma assíncrona porque não podem ser concluídas rapidamente. A resposta para operação assíncrona retorna inicialmente um código de status HTTP 200 (OK) e um cabeçalho com a propriedade *Azure-AsyncOperation* quando aceito:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Você pode verificar o status da operação assíncrona enviando uma solicitação GET para o valor do cabeçalho *Azure-AsyncOperation:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

A resposta contém informações sobre a operação e seu *Status*. Pode ser um dos seguintes:

A operação está em andamento.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

A operação está concluída
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

### <a name="subscription-whitelisting"></a>Whitelisting de assinatura

O recurso CMK é um recurso de acesso antecipado. As assinaturas onde você planeja criar recursos *do Cluster* devem ser listadas antecipadamente pelo grupo de produtos Azure. Use seus contatos na Microsoft para fornecer seus IDs de assinaturas.

> [!IMPORTANT]
> A capacidade do CMK é regional. O azure Key Vault, o recurso *Cluster* e os espaços de trabalho log analytics associados devem estar na mesma região, mas podem estar em assinaturas diferentes.

### <a name="storing-encryption-key-kek"></a>Armazenamento da chave de criptografia (KEK)

Crie ou use um Azure Key Vault que você já precisa gerar ou importe uma chave a ser usada para criptografia de dados. O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos seus dados no Monitor Do Azure. Você pode verificar essa configuração em propriedades no cofre de chaves, tanto *a proteção de exclusão suave* quanto *de purga* deve ser ativada.

![Configurações de proteção de exclusão e purga suave](media/customer-managed-keys/soft-purge-protection.png)

Essas configurações estão disponíveis via CLI e PowerShell:
- [Exclusão Reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Purgar](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) guardas de proteção contra a exclusão da força do segredo / cofre mesmo após a exclusão suave

### <a name="create-cluster-resource"></a>Criar recurso *de cluster*

Esse recurso é usado como uma conexão de identidade intermediária entre o Key Vault e os espaços de trabalho do Log Analytics. Depois de receber a confirmação de que suas assinaturas foram listadas em branco, crie um recurso do Log Analytics *Cluster* na região onde seus espaços de trabalho estão localizados. O Application Insights e o Log Analytics exigem tipos de recursos de *cluster* separados. O tipo de recurso *Cluster* é definido no momento da criação definindo a propriedade *clusterType* para *LogAnalytics*ou *ApplicationInsights*. O tipo de recurso do Cluster não pode ser alterado depois.

Para a configuração cmk do Application Insights, siga o conteúdo do apêndice.

Você deve especificar o nível de reserva de capacidade (sku) ao criar um recurso *Cluster.* O nível de reserva de capacidade pode estar na faixa de 1.000 a 2.000 GB por dia e você pode atualizá-lo em etapas de 100 mais tarde. Se você precisar de um nível de reserva de capacidade superior a 2.000 GB por dia, entre em contato com a Microsoft para habilitá-lo. Essa propriedade não afeta o faturamento atualmente -- uma vez que o modelo de preços para cluster dedicado for introduzido, o faturamento se aplicará a quaisquer implantações cmk existentes.

**Criar**

Esta solicitação do Gerenciador de Recursos é uma operação assíncrona.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
A identidade é atribuída ao recurso *Cluster* no momento da criação.

**Resposta**

200 OK e cabeçalho.
Durante o período de acesso inicial do recurso, o cluster ADX é provisionado manualmente. Embora leve o provisionamento do cluster ADX subjacente por um tempo para ser concluído, você pode verificar o estado de provisionamento de duas maneiras:
1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação de status das [operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET sobre o recurso *Cluster* e veja o valor do *provisionamentoState.* É *ProvisionamentoConta* durante o provisionamento e *teve sucesso* quando concluído.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Provisionamento do cluster Azure Monitor (cluster ADX)

Durante o período de acesso inicial do recurso, o cluster ADX é provisionado manualmente pela equipe do produto assim que as etapas anteriores forem concluídas. Use seu canal microsoft para esta etapa e forneça a resposta de recurso *do Cluster.* 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Copie e salve a resposta, pois você precisará dos detalhes nos próximos passos.

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
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

O GUID "principalId" é gerado pelo serviço de identidade gerenciado para o recurso *Cluster.*

### <a name="grant-key-vault-permissions"></a>Conceder permissões do Cofre chave

Atualize seu Key Vault com uma nova política de acesso que concede permissões ao recurso *Cluster.* Essas permissões são usadas pelo underlay Azure Monitor Storage para criptografia de dados. Abra seu Cofre de Chaves no portal Azure e clique em "Políticas de acesso" e depois em "+ Adicionar política de acesso" para criar uma política com essas configurações:

- Permissões de chave: selecione as permissões 'Get', 'Wrap Key' e 'Desembrulhe a chave'.
- Selecione principal: digite o valor principal-id que retornou na resposta na etapa anterior.

![conceder permissões Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Obter* é necessária para verificar se o cofre de chaves está configurado como recuperável para proteger sua chave e o acesso aos dados do Monitor do Azure.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar recurso do Cluster com detalhes do identificador chave

Esta etapa é realizada durante as atualizações iniciais e futuras da versão principal em seu Key Vault. Ele informa o Azure Monitor Storage sobre a versão-chave a ser usada para criptografia de dados. Quando atualizada, sua nova chave está sendo usada para embrulhar e desembrulhar a chave de armazenamento (AEK).

Para atualizar o recurso *Cluster* com os detalhes do *identificador Key* Vault Key, selecione a versão atual da sua chave no Azure Key Vault para obter os detalhes do identificador Key.

![Conceder permissões do Cofre chave](media/customer-managed-keys/key-identifier-8bit.png)

Atualize o recurso *Cluster* KeyVaultProperties com detalhes do identificador de chaves.

**Atualizar**

Esta solicitação do Gerenciador de recursos é uma operação assíncrona ao atualizar detalhes do identificador de chaves, enquanto é síncrono ao atualizar o valor da capacidade.

> [!Warning]
> Você deve fornecer um corpo completo na atualização de recursos *do Cluster* que inclua *identidade,* *sku,* *KeyVaultProperties* e *localização.* A falta dos detalhes *do KeyVaultProperties* removerá o identificador de chave do recurso *Cluster* e causará [a revogação da chave](#cmk-kek-revocation).

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
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" contém os detalhes do identificador da chave do Key Vault.

**Resposta**

200 OK e cabeçalho.
Leva alguns minutos para a propagação do identificador Key ser concluída. Você pode verificar o estado de atualização de duas maneiras:
1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação de status das [operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no recurso *Cluster* e veja as propriedades *KeyVaultProperties.* Seus detalhes de identificador de chave recentemente atualizados devem retornar na resposta.

Uma resposta à solicitação GET no recurso *Cluster* deve ser semelhante a esta quando a atualização do identificador de chave estiver concluída:

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Associação do espaço de trabalho ao recurso *cluster*
Para a configuração cmk do Application Insights, siga o conteúdo do apêndice para esta etapa.

Você precisa ter permissões de 'gravação' para o seu espaço de trabalho e recurso *cluster* para executar esta operação, que incluem essas ações:

- No espaço de trabalho: Microsoft.OperationalInsights/workspaces/write
- Em *recurso de cluster:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Esta etapa deve ser executada somente após o provisionamento de cluster ADX. Se você associar espaços de trabalho e ingerir dados antes do provisionamento, os dados ingeridos serão descartados e não serão recuperáveis.

**Associar um espaço de trabalho**

Esta solicitação do Gerenciador de Recursos é uma operação assíncrona.

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
Os dados ingeridos são armazenados criptografados com sua chave gerenciada após a operação de associação, que pode levar até 90 minutos para ser concluída. Você pode verificar o estado de associação do espaço de trabalho de duas maneiras:
1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação de status das [operações assíncronas](#asynchronous-operations-and-status-check).
2. Enviar um [Espaços de Trabalho – Obter](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) solicitação e observar a resposta, o espaço de trabalho associado terá um clusterResourceId em "recursos".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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
    "retentionInDays": days,
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

## <a name="cmk-kek-revocation"></a>Revogação de CMK (KEK)

Você pode revogar seu acesso aos seus dados desativando sua chave ou excluindo a política de acesso a recursos *do Cluster* em seu Cofre de Chaves. O Azure Monitor Storage sempre respeitará as alterações nas permissões-chave dentro de uma hora, normalmente mais cedo, e o armazenamento ficará indisponível. Todos os dados ingeridos em espaços de trabalho associados ao recurso *cluster* são descartados e as consultas falharão. Os dados ingeridos anteriormente permanecem inacessíveis no Azure Monitor Storage, desde que você seja o recurso *do Cluster* e seus espaços de trabalho não sejam excluídos. Os dados inacessíveis são regidos pela política de retenção de dados e serão eliminados quando a retenção for atingida.

O armazenamento fará uma pesquisa periódica no seu Cofre de Chaves para tentar desembrulhar a chave de criptografia e, uma vez acessado, a ingestão de dados e a consulta serão retomadas em 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação cmk (KEK)

A rotação do CMK requer atualização explícita do recurso *Cluster* com a nova versão-chave no Azure Key Vault. Para atualizar o Azure Monitor com sua nova versão-chave, siga as instruções na etapa "Atualizar recurso *do Cluster* com detalhes do identificador chave". Se você atualizar sua versão-chave no Key Vault e não atualizar os novos detalhes do identificador Key no recurso *Cluster,* o Azure Monitor Storage continuará usando sua chave anterior.
Todos os seus dados estão acessíveis após a operação de rotação de chaves, incluindo dados ingeridos antes da rotação e depois dele, uma vez que todos os dados permanecem criptografados pela AEK (Account Encryption Key, tecca) enquanto o AEK está sendo criptografado pela nova versão KEK (Key Encryption Key Key).

## <a name="limitations-and-constraints"></a>Limitações e restrições

- O recurso CMK é suportado no nível de cluster ADX e requer um cluster Azure Monitor ADX dedicado com a necessidade de enviar 1 TB por dia ou mais.

- O número máximo de recursos do *Cluster* por assinatura é limitado a 2

- *A* associação de recursos de cluster ao espaço de trabalho deve ser realizada somente depois de verificar que o provisionamento de cluster ADX foi concluído. Os dados enviados ao seu espaço de trabalho antes da conclusão do provisionamento serão descartados e não serão recuperáveis.

- A criptografia CMK aplica-se a dados recém-ingeridos após a configuração do CMK. Os dados que foram ingeridos antes da configuração do CMK, permanecem criptografados com a chave da Microsoft. Você pode consultar dados ingeridos antes e depois da configuração do CMK perfeitamente.

- Você pode desassociar um espaço de trabalho de um recurso *do Cluster* ao decidir que o CMK não é necessário para um espaço de trabalho específico. Novos dados ingeridos após a operação de desassociação são armazenados no armazenamento compartilhado do Log Analytics como era antes de ser associado ao recurso *Cluster.* Você pode consultar dados ingeridos antes e depois da desassociação perfeitamente se o recurso *do Cluster* estiver provisionado e configurado com a chave key vault válida.

- O Cofre de Chaves Do Azure deve ser configurado como recuperável. Essas propriedades não estão habilitadas por padrão e devem ser configuradas usando CLI ou PowerShell:

  - [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ligado
  - [A proteção contra expurgo](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ligada para se proteger contra a exclusão da força do segredo / cofre mesmo após a exclusão suave

- O Application Insights e o Log Analytics exigem recursos separados *do Cluster.* O tipo de recurso *Cluster* é definido no momento da criação, definindo a propriedade "clusterType" como 'LogAnalytics' ou 'ApplicationInsights'. O tipo de recurso *do Cluster* não pode ser alterado.

- *A* transferência de recursos do cluster para outro grupo de recursos ou assinatura não é suportada no momento.

- O seu Azure Key Vault, recurso *de cluster* e espaços de trabalho associados devem estar na mesma região e no mesmo inquilino do Azure Active Directory (Azure AD), mas eles podem estar em assinaturas diferentes.

- A associação do espaço de trabalho ao recurso *Cluster* falhará se estiver associada a outro recurso *do Cluster*

## <a name="troubleshooting-and-management"></a>Solução e gerenciamento de problemas

- Disponibilidade do Key Vault
    - Em operação normal -- Caches de armazenamento AEK por curtos períodos de tempo e volta ao Key Vault para desembrulhar periodicamente.
    
    - Erros de conexão transitórios -- O armazenamento lida com erros transitórios (tempos, falhas de conexão, problemas de DNS) permitindo que as chaves permaneçam em cache por um curto período de tempo e isso supera quaisquer pequenos deslizes na disponibilidade. Os recursos de consulta e ingestão continuam sem interrupção.
    
    - Site ao vivo - a indisponibilidade de cerca de 30 minutos fará com que a conta armazenamento fique indisponível. O recurso de consulta não está disponível e os dados ingeridos são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso ao Key Vault é restaurado, a consulta fica disponível e os dados em cache temporários são ingeridos no armazenamento de dados e criptografados com CMK.

- Se você criar um recurso *Cluster* e especificar o KeyVaultProperties imediatamente, a operação pode falhar, pois a política de acesso não pode ser definida até que a identidade do sistema seja atribuída ao recurso *Cluster.*

- Se você atualizar o recurso *de cluster* existente com keyVaultProperties e 'Get' key Access Policy estiver faltando no Key Vault, a operação falhará.

- Se você tentar excluir um recurso *de Cluster* associado a um espaço de trabalho, a operação de exclusão falhará.

- Se você tiver erro de conflito ao criar um recurso *cluster* – pode ser que você tenha excluído seu recurso *do Cluster* nos últimos 14 dias e ele esteja em um período de exclusão suave. O nome do recurso *Cluster* permanece reservado durante o período de exclusão suave e você não pode criar um novo cluster com esse nome. O nome é lançado após o período de exclusão suave quando o recurso *Cluster* é excluído permanentemente.

- Obtenha todos os recursos *do Cluster* para um grupo de recursos:

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
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Obtenha todos os recursos *do Cluster* para uma assinatura:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Resposta**
    
  A mesma resposta de *'Recursos de cluster* para um grupo de recursos', mas no escopo de assinatura.

- Atualizar *a reserva de capacidade* no recurso *Cluster* -- quando o volume de dados para seus espaços de trabalho associados mudar e você quiser atualizar o nível de reserva de capacidade para considerações de faturamento, siga o recurso de atualização [do *Cluster* ](#update-cluster-resource-with-key-identifier-details) e forneça seu novo valor de capacidade. O nível de reserva de capacidade pode ser na faixa de 1.000 a 2.000 GB por dia e em etapas de 100. Para um nível superior a 2.000 GB por dia, entre em contato com a Microsoft para habilitá-lo.

- Exclua o recurso *Cluster* -- uma operação de exclusão suave é realizada para permitir a recuperação do recurso *do Cluster,* incluindo seus dados dentro de 14 dias, se a exclusão foi acidental ou intencional. O nome do recurso *Cluster* permanece reservado durante o período de exclusão suave e você não pode criar um novo cluster com esse nome. Após o período de exclusão suave, o nome do recurso *Cluster* é liberado, o recurso e os dados *do Cluster* são excluídos permanentemente e não podem ser recuperáveis. Qualquer espaço de trabalho associado é desassociado do recurso *Cluster* na operação excluir. Novos dados ingeridos são armazenados no armazenamento compartilhado do Log Analytics e criptografados com a chave da Microsoft. A operação desassociada dos espaços de trabalho é assíncrona.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Resposta**

  200 OK

- Recupere seu recurso *de Cluster* e seus dados -- Um recurso *do Cluster* que foi excluído nos últimos 14 dias está em estado de exclusão suave e pode ser recuperado. Isso é realizado manualmente pelo grupo de produtos atualmente. Use seu canal da Microsoft para solicitações de recuperação.

## <a name="appendix"></a>Apêndice

O CMK (Application Insights Customer Managed Key, tec) também é suportado, embora você também deva considerar a seguinte alteração para ajudá-lo a planejar a implantação do CMK para seus componentes do Application Insight.

O Log Analytics e o Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta. Estamos reunindo essas duas lojas através da integração do Application Insights no Log Analytics para fornecer uma única loja unificada de logs sob o Azure Monitor até o segundo trimestre de
2020. Essa alteração trará seus dados do Application Insight para os espaços de trabalho do Log Analytics e tornará possíveis consultas, insights e outras melhorias, enquanto a configuração do CMK em seu espaço de trabalho também se aplicará aos dados do Application Insights.

> [!NOTE]
> Se você não tiver que implantar o CMK para seus dados do Application Insight antes da integração, recomendamos esperar com o Application Insights CMK, uma vez que tais implantações serão interrompidas pela integração e você terá que reconfigurar o CMK após a migração para o espaço de trabalho do Log Analytics. O mínimo de 1 TB por dia se aplica ao nível de cluster e até que a consolidação seja concluída durante o segundo trimestre, o Application Insights e o Log Analytics requerem clusters separados.

## <a name="application-insights-cmk-configuration"></a>Configuração cmk do Application Insights

A configuração do Application Insights CMK é idêntica ao processo ilustrado neste artigo, incluindo restrições e solução de problemas, exceto estas etapas:

- Criar um recurso *cluster*
- Associar um componente a um recurso *cluster*

Ao configurar o CMK para Insights de aplicativos, use essas etapas em vez das listadas acima.

### <a name="create-a-cluster-resource"></a>Criar um recurso *cluster*

Este recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus componentes. Depois de receber a confirmação de que suas assinaturas foram listadas em branco, crie um recurso do Log Analytics *Cluster* na região onde seus componentes estão localizados. O tipo de recurso *Cluster* é definido no momento da criação definindo a propriedade *clusterType* para *LogAnalytics*ou *ApplicationInsights*. Deve ser *ApplicationInsights* for Application Insights CMK. A configuração *clusterType* não pode ser alterada após a configuração.

**Criar**

Esta solicitação do Gerenciador de Recursos é uma operação assíncrona.

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Resposta**

200 OK e cabeçalho.
Durante o período de acesso inicial do recurso, o cluster ADX é provisionado manualmente. Embora leve o provisionamento do cluster ADX subjacente por um tempo para ser concluído, você pode verificar o estado de provisionamento de duas maneiras:
1. Copie o valor de URL Azure-AsyncOperation da resposta e siga a verificação de status das [operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET sobre o recurso *Cluster* e veja o valor do *provisionamentoState.* É *ProvisionamentoConta* durante o provisionamento e *teve sucesso* quando concluído.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Associar um componente a um recurso *de cluster* usando [componentes - Criar ou atualizar](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

Você precisa ter permissões de 'gravação' no seu componente e recurso *cluster* para executar esta operação, que incluem essas ações:

- No componente: Microsoft.Insights/componente/gravação
- Em *recurso de cluster:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Esta etapa deve ser executada somente após o provisionamento de cluster ADX. Se você associar componentes e ingerir dados antes do provisionamento, os dados ingeridos serão descartados e não serão recuperáveis.
> Para verificar se o cluster ADX está provisionado, execute o recurso *Cluster* Obtenha a API REST e verifique se o valor do *provisionamentoEstado* é *bem sucedido*.

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Copie e mantenha a resposta, pois você precisará dela nos próximos passos.

**Associar um componente**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" é o valor "clusterId" fornecido na resposta da etapa anterior.
"gentil" exemplo é "web".

**Resposta**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" é o *ID* de recurso do Cluster que está associado a este componente.

Após a associação, os dados enviados aos seus componentes são armazenados criptografados com sua chave gerenciada.
