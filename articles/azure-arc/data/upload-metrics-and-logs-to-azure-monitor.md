---
title: Carregar inventário de recursos, dados de uso, métricas e logs para Azure Monitor
description: Carregar inventário de recursos, dados de uso, métricas e logs para Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933204"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Carregar inventário de recursos, dados de uso, métricas e logs para Azure Monitor

Com o Azure Arc Data Services, você pode *, opcionalmente,* carregar suas métricas e logs para Azure monitor para que possa agregar e analisar métricas, logs, gerar alertas, enviar notificações ou disparar ações automatizadas. Enviar seus dados para Azure Monitor também permite que você armazene dados de monitoramento e logs fora do local e em grande escala habilitando o armazenamento de longo prazo dos dados para análise avançada.  Se você tiver vários sites que têm o Azure Arc Data Services, poderá usar Azure Monitor como um local central para coletar todos os seus logs e métricas em seus sites.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Antes de começar

Há algumas etapas de configuração única necessárias para habilitar os cenários de upload de logs e métricas:

1) Crie um aplicativo de Azure Active Directory/entidade de serviço, incluindo a criação de um segredo de acesso do cliente e a atribuição da entidade de serviço à função ' Editor de métricas de monitoramento ' nas assinaturas em que os recursos da instância do banco de dados estão localizados.
2) Crie um espaço de trabalho do log Analytics e obtenha as chaves e defina as informações em variáveis de ambiente.

O primeiro item é necessário para carregar as métricas e a segunda é necessária para carregar os logs.

Siga estes comandos para criar sua entidade de serviço de carregamento de métricas e atribuí-la às funções ' Editor de métricas de monitoramento ' e ' colaborador ' para que a entidade de serviço possa carregar métricas e executar operações de criação e carregamento.

## <a name="create-service-principal-and-assign-roles"></a>Criar entidade de serviço e atribuir funções

Siga estes comandos para criar sua entidade de serviço carregar métricas e atribuí-la à função ' Editor de métricas de monitoramento ':

Para criar uma entidade de serviço, execute este comando:

> [!NOTE]
> A criação de uma entidade de serviço requer [determinadas permissões no Azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Saída de exemplo:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Salve os valores de appId e locatário em uma variável de ambiente para uso posterior:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Execute este comando para atribuir a entidade de serviço à função ' Editor de métricas de monitoramento ' na assinatura em que os recursos da instância de banco de dados estão localizados:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Saída de exemplo:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do log Analytics

Em seguida, execute estes comandos para criar um espaço de trabalho Log Analytics e defina as informações de acesso em variáveis de ambiente.

> [!NOTE]
> Ignore esta etapa se você já tiver um espaço de trabalho.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Saída de exemplo:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Atribuir ID e chave compartilhada a variáveis de ambiente

Salve o customerId (ID do espaço de trabalho) como uma variável de ambiente a ser usada posteriormente:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Este comando imprimirá as chaves de acesso necessárias para se conectar ao seu espaço de trabalho do log Analytics:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Saída de exemplo:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Salve a chave primária em uma variável de ambiente a ser usada mais tarde:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Definir variáveis de ambiente finais e confirmar

Defina a URL da autoridade de SPN em uma variável de ambiente:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Certifique-se de que todas as variáveis de ambiente necessárias estejam definidas se você quiser:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Carregar métricas para Azure Monitor

Para carregar as métricas para as instâncias gerenciadas do Azure SQL e o banco de dados do Azure para os grupos de servidores de hiperescala do PostgreSQL, execute os seguintes comandos da CLI:

Isso exportará todas as métricas para o arquivo especificado:

```console
azdata arc dc export -t metrics --path metrics.json
```

Isso carregará as métricas no Azure Monitor:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Exibir as métricas no portal

Depois que as métricas forem carregadas, você poderá visualizá-las no portal do Azure.

Para exibir suas métricas no portal, use este link especial para abrir o portal: <https://portal.azure.com> em seguida, pesquise a instância do banco de dados por nome na barra de pesquisa:

Você pode exibir a utilização da CPU na página Visão geral ou se quiser métricas mais detalhadas, nas quais você pode clicar em métricas no painel de navegação à esquerda

Escolha o SQL Server como o namespace de métrica:

Selecione a métrica que você deseja visualizar (você também pode selecionar várias):

Altere a frequência para os últimos 30 minutos:

> [!NOTE]
> Só é possível carregar métricas para os últimos 30 minutos. Azure Monitor rejeita as métricas com mais de 30 minutos.

## <a name="upload-logs-to-azure-monitor"></a>Carregar logs para o Azure Monitor

 Para carregar os logs para suas instâncias gerenciadas do Azure SQL e o banco de dados do Azure para PostgreSQL, os grupos de servidores de hiperescala executam os seguintes comandos CLI-

Isso exportará todos os logs para o arquivo especificado:

```console
azdata arc dc export -t logs --path logs.json
```

Isso carregará os logs para um espaço de trabalho do Azure monitor log Analytics:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Exibir seus logs no portal do Azure

Depois que os logs forem carregados, você poderá consultá-los usando o gerenciador de consultas de log da seguinte maneira:

1. Abra o portal do Azure e, em seguida, pesquise seu espaço de trabalho por nome na barra de pesquisa na parte superior e, em seguida, selecione-o
2. Clique em Logs no painel esquerdo
3. Clique em introdução (ou clique nos links na página Introdução para saber mais sobre Log Analytics se você for novo)
4. Siga o tutorial para saber mais sobre Log Analytics se é a primeira vez
5. Expanda os Logs Personalizados na parte inferior da lista de tabelas e você verá uma tabela chamada "sql_instance_logs_CL".
6. Clique no ícone de "olho" ao lado do nome da tabela
7. Clique no botão "Exibir no editor de consultas"
8. Agora você terá uma consulta no editor de consultas, que mostrará os 10 eventos mais recentes no log
9. A partir daqui, você pode experimentar consultando os logs ao usar o editor de consultas, definir alertas etc.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Automatizando carregamentos de métricas e de logs (opcional)

Se você quiser carregar constantemente as métricas e os logs, poderá criar um script e executá-lo em um temporizador a cada poucos minutos.  Veja abaixo um exemplo de como automatizar os carregamentos usando um script de shell do Linux.

Em seu editor de texto/código favorito, adicione o seguinte ao conteúdo do script ao arquivo e salve como um arquivo executável de script, como. sh (Linux/Mac) ou. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Tornar o arquivo de script executável

```console
chmod +x myuploadscript.sh
```

Execute o script a cada 2 minutos:

```console
watch -n 120 ./myuploadscript.sh
```

Você também pode usar um Agendador de trabalho como cron ou Windows Agendador de Tarefas ou um orquestrador como Ansible, Puppet ou chefe.
