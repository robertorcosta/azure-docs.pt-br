---
title: Azure Monitor configuração de chave gerenciada pelo cliente
description: Informações e etapas para configurar a chave gerenciada pelo cliente (CMK) para criptografar dados em seus espaços de trabalho do Log Analytics usando uma chave de Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758809"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor configuração de chave gerenciada pelo cliente 

Este artigo fornece informações básicas e etapas para configurar chaves gerenciadas pelo cliente (CMK) para seus espaços de trabalho do Log Analytics e componentes do Application Insights. Uma vez configurado, todos os dados enviados aos seus espaços de trabalho ou componentes são criptografados com sua chave de Azure Key Vault.

Recomendamos que você revise as [limitações e restrições](#limitations-and-constraints) abaixo antes da configuração.

## <a name="disclaimers"></a>Avisos de Isenção de Responsabilidade

- Azure Monitor CMK é um recurso de acesso antecipado e está habilitado para assinaturas registradas.

- A implantação do CMK descrita neste artigo é fornecida em qualidade de produção e tem suporte como tal, embora seja um recurso de acesso antecipado.

- O recurso CMK é fornecido em um cluster de armazenamento de dados dedicado, que é um cluster de Data Explorer do Azure (ADX) e adequado para clientes que enviam 1 TB por dia ou mais. 

- O modelo de preços do CMK não está disponível no momento e não é abordado neste artigo. Um modelo de preço para o cluster ADX dedicado é esperado no segundo trimestre do ano civil (CY) 2020 e será aplicado a qualquer implantação existente do CMK.

- Este artigo descreve a configuração do CMK para espaços de trabalho do Log Analytics. O CMK para componentes Application Insights também tem suporte usando este artigo, enquanto as diferenças são listadas no apêndice.

> [!NOTE]
> Log Analytics e Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta.
> Estamos reunindo esses dois repositórios por meio da integração de Application Insights ao Log Analytics para criar um único repositório unificado de logs em Azure Monitor. Essa alteração está planejada para o segundo trimestre do ano civil 2020. Se você não precisar implantar CMK para seus dados de Application Insights, recomendamos aguardar a conclusão da consolidação, uma vez que essas implantações serão interrompidas pela consolidação e você terá que reconfigurar o CMK após a migração para Log Analytics espaço de trabalho. O mínimo de 1 TB por dia se aplica ao nível do cluster e até que a consolidação seja concluída durante o segundo trimestre, Application Insights e Log Analytics exigir clusters separados.

## <a name="customer-managed-key-cmk-overview"></a>Visão geral da CMK (chave gerenciada pelo cliente)

[A criptografia em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) é um requisito comum de privacidade e segurança nas organizações. Você pode permitir que o Azure gerencie completamente a criptografia em repouso, enquanto você tem várias opções para gerenciar de forma rigorosa as chaves de criptografia ou criptografia.

O armazenamento de dados Azure Monitor garante que todos os dados criptografados em repouso usando chaves gerenciadas pelo Azure enquanto estão armazenados no armazenamento do Azure. O Azure Monitor também fornece uma opção para a criptografia de dados usando sua própria chave armazenada em sua [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), que é acessada usando a autenticação de [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) atribuída pelo sistema. Essa chave pode ser um [software ou hardware-HSM protegido](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
O uso Azure Monitor da criptografia é idêntico ao modo como a [criptografia de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) Opera.

A frequência que Azure Monitor os acessos de armazenamento Key Vault para operações de encapsulamento e desencapsulamento é entre 6 e 60 segundos.Azure Monitor armazenamento sempre respeita as alterações nas permissões de chave em uma hora.

Os dados ingeridos nos últimos 14 dias também são mantidos no cache quente (com suporte SSD) para uma operação eficiente do mecanismo de consulta. Esses dados permanecem criptografados com chaves da Microsoft, independentemente da configuração CMK, mas estamos trabalhando para ter o SSD criptografado com CMK no primeiro semestre de 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Como o CMK funciona em Azure Monitor

Azure Monitor utiliza a identidade gerenciada atribuída pelo sistema para conceder acesso ao seu Azure Key Vault.A identidade gerenciada atribuída pelo sistema só pode ser associada a um único recurso do Azure. A identidade de Azure Monitor armazenamento de dados (cluster ADX) tem suporte no nível de cluster e isso determina que a funcionalidade CMK é entregue em um cluster ADX dedicado. Para dar suporte a CMK em vários espaços de trabalho, um novo recurso de Log Analytics (*cluster*) é executado como uma conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho do log Analytics. Esse conceito está em conformidade com a restrição de identidade atribuída pelo sistema e a identidade é mantida entre o cluster ADX e o recurso de *cluster* log Analytics, enquanto os dados de todos os espaços de trabalho associados são protegidos com sua chave de Key Vault. O armazenamento de cluster underlay ADX usa a identidade gerenciada que\'os s associados ao recurso de *cluster* para autenticar e acessar seu Azure Key Vault via Azure Active Directory.

![Visão geral do CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Key Vault do cliente.
2.    O recurso de *Cluster* log Analytics do cliente com identidade gerenciada com permissões para Key Vault – a identidade tem suporte no nível de armazenamento de dados (cluster ADX).
3.    Azure Monitor cluster ADX dedicado.
4.    Espaços de trabalho do cliente associados ao recurso de *cluster* para criptografia CMK.

## <a name="encryption-keys-management"></a>Gerenciamento de chaves de criptografia

Há três tipos de chaves envolvidas na criptografia de dados de armazenamento:

- **Kek** -chave de criptografia de chave (CMK)
- **AEK** -chave de criptografia da conta
- **DEK** -chave de criptografia de dados

As seguintes regras se aplicam:

- As contas de armazenamento ADX geram uma chave de criptografia exclusiva para cada conta de armazenamento, que é conhecida como AEK.

- O AEK é usado para derivar DEKs, que são as chaves que são usadas para criptografar cada bloco de dados gravados no disco.

- Quando você configura sua chave em Key Vault e faz referência a ela no recurso de *cluster* , o armazenamento do Azure envia solicitações ao seu Azure Key Vault para encapsular e desencapsular o AEK para executar as operações de criptografia e descriptografia de dados.

- Seu KEK nunca deixa seu Key Vault e, no caso de uma chave HSM, ele nunca deixa o hardware.

- O armazenamento do Azure usa a identidade gerenciada associada ao recurso de *cluster* para autenticar e acessar o Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedimento de provisionamento do CMK

Para Application Insights configuração do CMK, siga o conteúdo do apêndice para as etapas 3 e 6.

1. Lista de permissões da assinatura--isso é necessário para este recurso de acesso antecipado
2. Criando Azure Key Vault e armazenando a chave
3. Criando um recurso de *cluster*
4. Provisionamento de Azure Monitor armazenamento de dados (cluster ADX)
5. Concedendo permissões ao seu Key Vault
6. Associando espaços de trabalho Log Analytics

O procedimento não tem suporte na interface do usuário no momento e o processo de provisionamento é executado por meio da API REST.

> [!IMPORTANT]
> Qualquer solicitação de API deve incluir um token de autorização de portador no cabeçalho da solicitação.

Por exemplo:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Em que *eyJ0eXAiO...* representa o token de autorização completo. 

Você pode adquirir o token usando um destes métodos:

1. Use [registros de aplicativo](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) método.
2. No portal do Azure
    1. Navegue até portal do Azure enquanto estiver em "ferramenta de desenvolvedor" (F12)
    1. Procure a cadeia de caracteres de autorização em "cabeçalhos de solicitação" em uma das instâncias de "lote? API-Version". Ele é semelhante a: "Authorization: portador eyJ0eXAiO....". 
    1. Copie e adicione-o à sua chamada à API de acordo com os exemplos abaixo.
3. Navegue até o site de documentação REST do Azure. Pressione "Experimente" em qualquer API e copie o token de portador.

### <a name="asynchronous-operations-and-status-check"></a>Operações assíncronas e verificação de status

Algumas das operações neste procedimento de configuração são executadas de forma assíncrona porque não podem ser concluídas rapidamente. A resposta para a operação assíncrona inicialmente retorna um código de status HTTP 200 (OK) e o cabeçalho com a propriedade *Azure-AsyncOperation* quando aceita:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Você pode verificar o status da operação assíncrona enviando uma solicitação GET para o valor do cabeçalho *Azure-AsyncOperation* :
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

### <a name="subscription-whitelisting"></a>Lista de permissões da assinatura

O recurso CMK é um recurso de acesso antecipado. As assinaturas em que você planeja criar recursos de *cluster* devem estar na lista de permissões antecipadamente pelo grupo de produtos do Azure. Use seus contatos na Microsoft para fornecer suas IDs de assinaturas.

> [!IMPORTANT]
> O recurso CMK é regional. Seu Azure Key Vault, recurso de *cluster* e espaços de trabalho de log Analytics associados devem estar na mesma região, mas podem estar em assinaturas diferentes.

### <a name="storing-encryption-key-kek"></a>Armazenando a chave de criptografia (KEK)

Crie ou use um Azure Key Vault que você já tenha para gerar ou importe uma chave a ser usada para criptografia de dados. O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso aos dados no Azure Monitor. Você pode verificar essa configuração em Propriedades em seu Key Vault, a *exclusão reversível* e a *proteção de limpeza* devem ser habilitadas.

![Exclusão reversível e limpeza de configurações de proteção](media/customer-managed-keys/soft-purge-protection.png)

Essas configurações estão disponíveis por meio da CLI e do PowerShell:
- [Exclusão Reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Limpar proteção](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) protege contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível

### <a name="create-cluster-resource"></a>Criar recurso de *cluster*

Esse recurso é usado como uma conexão de identidade intermediária entre seu Key Vault e seus espaços de trabalho do Log Analytics. Depois de receber a confirmação de que suas assinaturas foram colocadas na lista de permissões, crie um Log Analytics recurso de *cluster* na região em que os espaços de trabalho estão localizados. Application Insights e Log Analytics exigem tipos de recursos de *cluster* separados. O tipo do recurso de *cluster* é definido no momento da criação, definindo a propriedade *clustertype* como *LogAnalytics*ou *ApplicationInsights*. O tipo de recurso de cluster não pode ser alterado após.

Para Application Insights configuração do CMK, siga o conteúdo do apêndice.

Você deve especificar o nível de reserva de capacidade (SKU) ao criar um recurso de *cluster* . O nível de reserva de capacidade pode estar no intervalo de 1.000 a 2.000 GB por dia e você pode atualizá-lo em etapas de 100 mais tarde. Se você precisar de um nível de reserva de capacidade maior que 2.000 GB por dia, acesse seu contato da Microsoft para habilitá-lo. Essa propriedade não afeta a cobrança atualmente, uma vez que o modelo de preços do cluster dedicado foi introduzido, a cobrança será aplicada a todas as implantações de CMK existentes.

**Criar**

Essa solicitação do Gerenciador de recursos é uma operação assíncrona.

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
A identidade é atribuída ao recurso de *cluster* no momento da criação.

**Responde**

200 OK e header.
Durante o período de acesso antecipado do recurso, o cluster ADX é provisionado manualmente. Embora leve o provisionamento do cluster ADX em um tempo para ser concluído, você pode verificar o estado de provisionamento de duas maneiras:
1. Copie o valor da URL Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no recurso de *cluster* e examine o valor *provisioningState* . É *ProvisioningAccount* durante o provisionamento e com *êxito* quando concluído.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Provisionamento de Azure Monitor armazenamento de dados (cluster ADX)

Durante o período de acesso antecipado do recurso, o cluster ADX é provisionado manualmente pela equipe de produto depois que as etapas anteriores são concluídas. Use seu canal da Microsoft para esta etapa e forneça a resposta do recurso de *cluster* . 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Copie e salve a resposta, pois você precisará dos detalhes nas próximas etapas.

**Responde**

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

O GUID "PrincipalId" é gerado pelo serviço de identidade gerenciada para o recurso de *cluster* .

### <a name="grant-key-vault-permissions"></a>Conceder permissões de Key Vault

Atualize seu Key Vault com uma nova política de acesso que concede permissões para o recurso de *cluster* . Essas permissões são usadas pelo underlay Azure Monitor armazenamento para criptografia de dados. Abra o Key Vault no portal do Azure e clique em "políticas de acesso" e, em seguida, em "+ Adicionar política de acesso" para criar uma política com estas configurações:

- Permissões de chave: selecione as permissões ' obter ', ' quebrar chave ' e ' desencapsular chave '.
- Selecionar entidade de segurança: Insira o valor da ID principal que foi retornado na resposta na etapa anterior.

![conceder permissões de Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A permissão *Get* é necessária para verificar se o Key Vault está configurado como recuperável para proteger sua chave e o acesso aos seus dados de Azure monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Atualizar recurso de cluster com detalhes do identificador de chave

Esta etapa é executada durante as atualizações de versão de chave inicial e futura no seu Key Vault. Ele informa Azure Monitor armazenamento sobre a versão da chave a ser usada para criptografia de dados. Quando atualizado, sua nova chave está sendo usada para encapsular e desencapsular para a chave de armazenamento (AEK).

Para atualizar o recurso de *cluster* com os detalhes do *identificador de chave* Key Vault, selecione a versão atual de sua chave no Azure Key Vault para obter os detalhes do identificador de chave.

![Conceder permissões de Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Atualize o Resource Vaultproperties do recurso de *cluster* com os detalhes do identificador de chave.

**Atualização**

Essa solicitação do Resource Manager é uma operação assíncrona ao atualizar detalhes do identificador de chave, enquanto é síncrona ao atualizar o valor da capacidade.

> [!Warning]
> Você deve fornecer um corpo completo na atualização de recurso de *cluster* que inclui *identidade*, *SKU*, *keyvaultproperties* e *local*. Os detalhes de *Keyvaultproperties* não serão removidos do recurso de *cluster* e causarão a [revogação de chave](#cmk-kek-revocation).

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
"Keyvaultproperties" contém os detalhes do identificador de chave Key Vault.

**Responde**

200 OK e header.
Ela leva a propagação do identificador de chave alguns minutos para ser concluída. Você pode verificar o estado de atualização de duas maneiras:
1. Copie o valor da URL Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no recurso de *cluster* e examine as propriedades de *keyvaultproperties* . Os detalhes do identificador de chave atualizados recentemente devem retornar na resposta.

Uma resposta para a solicitação GET no recurso de *cluster* deve ser parecida com esta quando a atualização do identificador de chave for concluída:

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

### <a name="workspace-association-to-cluster-resource"></a>Associação de espaço de trabalho ao recurso de *cluster*
Para Application Insights configuração do CMK, siga o conteúdo do apêndice para esta etapa.

Você precisa ter permissões de ' gravação ' no espaço de trabalho e no recurso de *cluster* para executar essa operação, o que inclui estas ações:

- No espaço de trabalho: Microsoft. OperationalInsights/Workspaces/Write
- Em recurso de *cluster* : Microsoft. OperationalInsights/clusters/Write

> [!IMPORTANT]
> Esta etapa deve ser executada somente após o provisionamento do cluster ADX. Se você associar espaços de trabalho e ingerir dados antes do provisionamento, os dados ingeridos serão descartados e não poderão ser recuperados.

**Associar um espaço de trabalho**

Essa solicitação do Gerenciador de recursos é uma operação assíncrona.

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

**Responde**

200 OK e header.
Os dados ingeridos são armazenados criptografados com a chave gerenciada após a operação de associação, o que pode levar até 90 minutos para ser concluído. Você pode verificar o estado de associação do espaço de trabalho de duas maneiras:
1. Copie o valor da URL Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Enviar [espaços de trabalho – obter](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) solicitação e observar a resposta, o espaço de trabalho associado terá um clusterResourceId em "recursos".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Responde**

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

Você pode revogar o acesso aos seus dados desabilitando sua chave ou excluindo a política de acesso ao recurso de *cluster* em seu Key Vault. Azure Monitor armazenamento sempre respeitará as alterações nas permissões de chave em uma hora, normalmente antes e o armazenamento ficará indisponível. Todos os dados ingeridos em espaços de trabalho associados ao recurso de *cluster* serão eliminados e as consultas falharão. Os dados anteriormente ingeridos permanecem inacessíveis no armazenamento Azure Monitor, desde que você seja o recurso de *cluster* e seus espaços de trabalho não sejam excluídos. Os dados inacessíveis são regidos pela política de retenção de dados e serão limpos quando a retenção for atingida.

O armazenamento sonda periodicamente seu Key Vault para tentar desencapsular a chave de criptografia e, depois de acessado, a ingestão de dados e a retomada de consulta dentro de 30 minutos.

## <a name="cmk-kek-rotation"></a>Rotação de CMK (KEK)

A rotação de CMK requer uma atualização explícita do recurso de *cluster* com a nova versão de chave no Azure Key Vault. Para atualizar Azure Monitor com a nova versão de chave, siga as instruções na etapa "atualizar recurso de *cluster* com detalhes do identificador de chave". Se você atualizar a versão de chave em Key Vault e não atualizar os novos detalhes do identificador de chave no recurso de *cluster* , o armazenamento de Azure monitor continuará usando sua chave anterior.
Todos os seus dados podem ser acessados após a operação de rotação de chaves, incluindo dados ingeridos antes da rotação e depois dele, já que todos os dados permanecem criptografados pela AEK (chave de criptografia da conta) enquanto o AEK agora está sendo criptografado pela nova versão de KEK (chave de criptografia de chave).

## <a name="limitations-and-constraints"></a>Limitações e restrições

- O recurso CMK tem suporte no nível de cluster ADX e requer um cluster dedicado Azure Monitor ADX com requisito de envio de 1 TB por dia ou mais.

- O número máximo de recursos de *cluster* por assinatura é limitado a 2

- A associação de recursos de *cluster* ao espaço de trabalho deve ser realizada somente depois que você tiver verificado que o provisionamento do cluster ADX foi concluído. Os dados enviados ao seu espaço de trabalho antes da conclusão do provisionamento serão removidos e não serão recuperáveis.

- A criptografia CMK se aplica a dados recentemente incluídos após a configuração do CMK. Os dados que foram ingeridos antes da configuração do CMK permanecem criptografados com a chave da Microsoft. Você pode consultar dados ingeridos antes e depois da configuração do CMK diretamente.

- Você pode desassociar um espaço de trabalho de um recurso de *cluster* ao decidir que CMK não é necessário para um espaço de trabalho específico. Novos dados ingeridos após a operação de desassociação são armazenados no armazenamento de Log Analytics compartilhado como era antes de serem associados ao recurso de *cluster* . Você pode consultar dados ingeridos antes e depois da desassociação sem interrupções se o recurso de *cluster* for provisionado e configurado com uma chave de Key Vault válida.

- O Azure Key Vault deve ser configurado como recuperável. Essas propriedades não são habilitadas por padrão e devem ser configuradas usando a CLI ou o PowerShell:

  - A [exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve estar ativada
  - A [proteção de limpeza](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) deve ser ativada para proteção contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível

- Application Insights e Log Analytics exigem recursos de *cluster* separados. O tipo do recurso de *cluster* é definido no momento da criação, definindo a propriedade "clustertype" como "LogAnalytics" ou "ApplicationInsights". O tipo de recurso de *cluster* não pode ser alterado.

- A movimentação de recursos de *cluster* para outro grupo de recursos ou assinatura não tem suporte no momento.

- Seu Azure Key Vault, recurso de *cluster* e espaços de trabalho associados devem estar na mesma região e no mesmo locatário do Azure Active Directory (AD do Azure), mas podem estar em assinaturas diferentes.

- A associação de espaço de trabalho ao recurso de *cluster* falhará se estiver associada a outro recurso de *cluster*

## <a name="troubleshooting-and-management"></a>Solução de problemas e gerenciamento

- Disponibilidade de Key Vault
    - Em operação normal--caches de armazenamento AEK por curtos períodos de tempo e volta para Key Vault para desfazer o encapsulamento periodicamente.
    
    - Erros de conexão transitórios – o armazenamento trata erros transitórios (tempos limite, falhas de conexão, problemas de DNS), permitindo que as chaves permaneçam no cache por um curto tempo e isso supera qualquer blips pequena em disponibilidade. Os recursos de consulta e ingestão continuam sem interrupção.
    
    - Site ativo--a indisponibilidade de aproximadamente 30 minutos fará com que a conta de armazenamento fique indisponível. A funcionalidade de consulta não está disponível e os dados ingeridos são armazenados em cache por várias horas usando a chave da Microsoft para evitar a perda de dados. Quando o acesso a Key Vault é restaurado, a consulta fica disponível e os dados temporários armazenados em cache são ingeridos no armazenamento de dados e criptografados com CMK.

- Se você criar um recurso de *cluster* e especificar o keyvaultproperties imediatamente, a operação poderá falhar, pois a política de acesso não poderá ser definida até que a identidade do sistema seja atribuída ao recurso de *cluster* .

- Se você atualizar o recurso de *cluster* existente com keyvaultproperties e a política de acesso à chave ' Get ' estiver ausente no Key Vault, a operação falhará.

- Se você tentar excluir um recurso de *cluster* associado a um espaço de trabalho, a operação de exclusão falhará.

- Se você receber um erro de conflito ao criar um recurso de *cluster* , pode ser que você tenha excluído o recurso de *cluster* nos últimos 14 dias e esteja em um período de exclusão reversível. O nome do recurso de *cluster* permanece reservado durante o período de exclusão reversível e você não pode criar um novo cluster com esse nome. O nome é liberado após o período de exclusão reversível quando o recurso de *cluster* é excluído permanentemente.

- Obter todos os recursos de *cluster* para um grupo de recursos:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Responde**
  
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

- Obter todos os recursos de *cluster* para uma assinatura:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Responde**
    
  A mesma resposta que para ' recursos de*cluster* para um grupo de recursos ', mas no escopo da assinatura.

- Atualizar a *reserva de capacidade* no recurso de *cluster* – quando o volume de dados para seus espaços de trabalho associados forem alterados e você quiser atualizar o nível de reserva de capacidade para considerações de cobrança, siga o [recurso atualizar *cluster* ](#update-cluster-resource-with-key-identifier-details) e forneça seu novo valor de capacidade. O nível de reserva de capacidade pode estar no intervalo de 1.000 a 2.000 GB por dia e em etapas de 100. Para um nível superior a 2.000 GB por dia, acesse o contato da Microsoft para habilitá-lo.

- Excluir o recurso de *cluster* – uma operação de exclusão reversível é executada para permitir a recuperação do seu recurso de *cluster* , incluindo seus dados dentro de 14 dias, se a exclusão foi acidental ou intencional. O nome do recurso de *cluster* permanece reservado durante o período de exclusão reversível e você não pode criar um novo cluster com esse nome. Após o período de exclusão reversível, o nome do recurso de *cluster* é liberado, o recurso de *cluster* e os dados são excluídos permanentemente e não são recuperáveis. Qualquer espaço de trabalho associado é desassociado do recurso de *cluster* na operação de exclusão. Novos dados ingeridos são armazenados no armazenamento Log Analytics compartilhado e criptografados com a chave da Microsoft. A operação de associação de espaços de trabalho é assíncrona.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Responde**

  200 OK

- Recupere o recurso de *cluster* e seus dados--um recurso de *cluster* que foi excluído nos últimos 14 dias está no estado de exclusão reversível e pode ser recuperado. Isso é executado manualmente pelo grupo de produtos no momento. Use seu canal da Microsoft para solicitações de recuperação.

## <a name="appendix"></a>Apêndice

Application Insights chave gerenciada pelo cliente (CMK) também tem suporte, embora você deva considerar a seguinte alteração para ajudá-lo a planejar a implantação do CMK para seus componentes do Application insights.

Log Analytics e Application Insights estão usando a mesma plataforma de armazenamento de dados e o mecanismo de consulta. Estamos reunindo esses dois repositórios por meio da integração de Application Insights ao Log Analytics para fornecer um único repositório unificado de logs em Azure Monitor pelo segundo trimestre de
2020. Essa alteração colocará os dados do aplicativo em Log Analytics espaços de trabalho e tornará as consultas, as informações e outras melhorias possíveis, enquanto a configuração de CMK em seu espaço de trabalho, também se aplicará aos seus dados de Application Insights.

> [!NOTE]
> Se você não precisar implantar o CMK para seus dados do Application insights antes da integração, é recomendável aguardar com Application Insights CMK, uma vez que essas implantações serão interrompidas pela integração e você terá que reconfigurar o CMK após a migração para Log Analytics espaço de trabalho. O mínimo de 1 TB por dia se aplica ao nível do cluster e até que a consolidação seja concluída durante o segundo trimestre, Application Insights e Log Analytics exigir clusters separados.

## <a name="application-insights-cmk-configuration"></a>Configuração do Application Insights CMK

A configuração de Application Insights CMK é idêntica ao processo ilustrado neste artigo, incluindo restrições e solução de problemas, exceto estas etapas:

- Criar um recurso de *cluster*
- Associar um componente a um recurso de *cluster*

Ao configurar o CMK para Application Insights, use estas etapas em vez dos listados acima.

### <a name="create-a-cluster-resource"></a>Criar um recurso de *cluster*

Esse recurso é usado como conexão de identidade intermediária entre seu Key Vault e seus componentes. Depois de receber uma confirmação de que suas assinaturas foram colocadas na lista de permissões, crie um Log Analytics recurso de *cluster* na região em que os componentes estão localizados. O tipo do recurso de *cluster* é definido no momento da criação, definindo a propriedade *clustertype* como *LogAnalytics*ou *ApplicationInsights*. Deve ser *ApplicationInsights* para Application insights CMK. A configuração de *clustertype* não pode ser alterada após a configuração.

**Criar**

Essa solicitação do Gerenciador de recursos é uma operação assíncrona.

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

**Responde**

200 OK e header.
Durante o período de acesso antecipado do recurso, o cluster ADX é provisionado manualmente. Embora leve o provisionamento do cluster ADX em um tempo para ser concluído, você pode verificar o estado de provisionamento de duas maneiras:
1. Copie o valor da URL Azure-AsyncOperation da resposta e siga a [verificação de status de operações assíncronas](#asynchronous-operations-and-status-check).
2. Envie uma solicitação GET no recurso de *cluster* e examine o valor *provisioningState* . É *ProvisioningAccount* durante o provisionamento e com *êxito* quando concluído.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Associar um componente a um recurso de *cluster* usando [componentes – criar ou atualizar](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) a API

Você precisa ter permissões de ' gravação ' em seu componente e recurso de *cluster* para executar essa operação, que incluem estas ações:

- Em componente: Microsoft. insights/componente/gravação
- Em recurso de *cluster* : Microsoft. OperationalInsights/clusters/Write

> [!IMPORTANT]
> Esta etapa deve ser executada somente após o provisionamento do cluster ADX. Se você associar componentes e ingerir dados antes do provisionamento, os dados ingeridos serão descartados e não poderão ser recuperados.
> Para verificar se o cluster ADX está provisionado, execute o recurso de *cluster* obter API REST e verifique se o valor de *provisioningState* foi *bem-sucedido*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Responde**
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
> Copie e mantenha a resposta, pois você precisará dela nas próximas etapas.

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
"clusterDefinitionId" é o valor de "clusterid" fornecido na resposta da etapa anterior.
o exemplo "Kind" é "Web".

**Responde**

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
"clusterDefinitionId" é a ID de recurso de *cluster* associada a esse componente.

Após a associação, os dados enviados aos seus componentes são armazenados criptografados com a chave gerenciada.
