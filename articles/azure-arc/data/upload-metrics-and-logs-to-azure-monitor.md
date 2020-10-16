---
title: Carregar dados de uso, métricas e logs para Azure Monitor
description: Carregar inventário de recursos, dados de uso, métricas e logs para Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cda123adb667b4c857e05ce53d603e328e995766
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108160"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Carregar dados de uso, métricas e logs para Azure Monitor

Periodicamente, você pode exportar informações de uso para fins de cobrança, métricas de monitoramento e logs e, em seguida, carregá-las no Azure.  A exportação e o upload de qualquer um desses três tipos de dados também criarão e atualizarão o controlador de dados, a instância gerenciada do SQL e os recursos do grupo de servidores de hiperescala do PostgreSQL no Azure.

> [!NOTE] 
> Durante o período de versão prévia, não há nenhum custo para usar os serviços de dados habilitados para Arc do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do CLI do Azure (AZ) e da CLI de dados do Azure (azdata) instalada.  [Instalar ferramentas](./install-client-tools.md).

Antes de carregar dados no Azure, você precisa garantir que sua assinatura do Azure tenha o provedor de recursos Microsoft. AzureData registrado.

Você pode verificar isso executando o seguinte comando:

```console
az provider show -n Microsoft.AzureData -o table
```

Se o provedor de recursos não estiver registrado em sua assinatura no momento, você poderá registrá-lo executando o comando a seguir.  Esse comando leva um minuto ou dois para ser concluído.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Carregar dados de uso

Informações de uso, como inventário e uso de recursos, podem ser carregadas no Azure na seguinte maneira de duas etapas:

1. Exporte os dados de uso usando o ```azdata export``` comando, da seguinte maneira:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Este comando cria um `usage.json` arquivo com todos os recursos de dados habilitados para o Azure Arc, como instâncias gerenciadas do SQL e instâncias de hiperescala do PostgreSQL, etc. que são criados no controlador de dados.

2. Carregar os dados de uso usando o ```azdata upload``` comando

   > [!NOTE]
   > Aguarde pelo menos 24 horas depois de criar o controlador de dados de arco do Azure antes de executar o carregamento

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Carregar métricas e logs

Com os serviços de dados de arco do Azure, você pode, opcionalmente, carregar suas métricas e logs para Azure Monitor para que possa agregar e analisar métricas, logs, gerar alertas, enviar notificações ou disparar ações automatizadas. 

Enviar seus dados para Azure Monitor também permite que você armazene dados de monitoramento e logs fora do local e em grande escala habilitando o armazenamento de longo prazo dos dados para análise avançada.

Se você tiver vários sites que têm o Azure Arc Data Services, poderá usar Azure Monitor como um local central para coletar todos os seus logs e métricas em seus sites.

### <a name="before-you-begin"></a>Antes de começar

Há algumas etapas de configuração única necessárias para habilitar os cenários de upload de logs e métricas:

1. Crie um aplicativo de Azure Active Directory/entidade de serviço, incluindo a criação de um segredo de acesso do cliente e a atribuição da entidade de serviço à função ' Editor de métricas de monitoramento ' nas assinaturas em que os recursos da instância do banco de dados estão localizados.
2. Crie um espaço de trabalho do log Analytics e obtenha as chaves e defina as informações em variáveis de ambiente.

O primeiro item é necessário para carregar as métricas e a segunda é necessária para carregar os logs.

Siga estes comandos para criar sua entidade de serviço de carregamento de métricas e atribuí-la às funções ' Editor de métricas de monitoramento ' e ' colaborador ' para que a entidade de serviço possa carregar métricas e executar operações de criação e carregamento.

## <a name="create-service-principal-and-assign-roles"></a>Criar entidade de serviço e atribuir funções

Siga estes comandos para criar sua entidade de serviço carregar métricas e atribuí-la à função ' Editor de métricas de monitoramento ':

Para criar uma entidade de serviço, execute este comando:

> [!NOTE]
> A criação de uma entidade de serviço requer [determinadas permissões no Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Saída de exemplo:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Salve os valores de appId e locatário em uma variável de ambiente para uso posterior. 

Para salvar os valores de appId e locatário com o PowerShell, siga este exemplo:

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

Como alternativa, no Linux ou no macOS, você pode salvar os valores de appId e locatário com este exemplo:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Execute este comando para atribuir a entidade de serviço à função ' Editor de métricas de monitoramento ' na assinatura em que os recursos da instância de banco de dados estão localizados:


> [!NOTE]
> Você precisa usar aspas duplas para nomes de função ao executar de um ambiente do Windows.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Saída de exemplo:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do log Analytics

Em seguida, execute estes comandos para criar um espaço de trabalho Log Analytics e defina as informações de acesso em variáveis de ambiente.

> [!NOTE]
> Ignore esta etapa se você já tiver um espaço de trabalho.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Saída de exemplo:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Para carregar as métricas para suas instâncias gerenciadas SQL habilitadas do Arc do Azure e os grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc, os seguintes comandos da CLI:

1. Exportar todas as métricas para o arquivo especificado:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Carregar métricas no Azure monitor:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Aguarde pelo menos 30 minutos após a criação das instâncias de dados habilitadas para o Arc do Azure para o primeiro upload
   >
   >Verifique se `upload` as métricas estão imediatamente após `export` as Azure monitor aceita apenas as métricas dos últimos 30 minutos. [Saiba mais](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Se você vir algum erro indicando "falha ao obter métricas" durante a exportação, verifique se a coleta de dados está definida como ```true``` executando o seguinte comando:

```console
azdata arc dc config show
```

e examine "seção de segurança"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Verifique se as `allowNodeMetricsCollection` `allowPodMetricsCollection` Propriedades e estão definidas como `true` .

## <a name="view-the-metrics-in-the-portal"></a>Exibir as métricas no portal

Depois que suas métricas forem carregadas, você poderá exibi-las no portal do Azure.
> [!NOTE]
> Observe que pode levar alguns minutos para que os dados carregados sejam processados antes que você possa exibir as métricas no Portal.


Para exibir suas métricas no portal, use este link para abrir o portal: <https://portal.azure.com> em seguida, pesquise a instância do banco de dados por nome na barra de pesquisa:

Você pode exibir a utilização da CPU na página Visão geral ou se quiser métricas mais detalhadas, nas quais você pode clicar em métricas no painel de navegação à esquerda

Escolha o SQL Server como o namespace de métrica:

Selecione a métrica que você deseja visualizar (você também pode selecionar várias):

Altere a frequência para os últimos 30 minutos:

> [!NOTE]
> Só é possível carregar métricas para os últimos 30 minutos. Azure Monitor rejeita as métricas com mais de 30 minutos.

## <a name="upload-logs-to-azure-monitor"></a>Carregar logs para o Azure Monitor

 Para carregar logs para suas instâncias gerenciadas SQL habilitadas do Arc do Azure e os grupos de servidores de hiperescala PostgreSQL habilitados para AzureArc, execute os seguintes comandos da CLI-

1. Exportar todos os logs para o arquivo especificado:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Carregar logs em um espaço de trabalho do Azure monitor log Analytics:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Exibir seus logs no portal do Azure

Depois que os logs forem carregados, você poderá consultá-los usando o gerenciador de consultas de log da seguinte maneira:

1. Abra o portal do Azure e, em seguida, pesquise seu espaço de trabalho por nome na barra de pesquisa na parte superior e, em seguida, selecione-o
2. Clique em Logs no painel esquerdo
3. Clique em introdução (ou clique nos links na página Introdução para saber mais sobre Log Analytics se você for novo)
4. Siga o tutorial para saber mais sobre Log Analytics se esta for a primeira vez que você usa Log Analytics
5. Expanda os Logs Personalizados na parte inferior da lista de tabelas e você verá uma tabela chamada "sql_instance_logs_CL".
6. Clique no ícone de "olho" ao lado do nome da tabela
7. Clique no botão "Exibir no editor de consultas"
8. Agora você terá uma consulta no editor de consultas que mostrará os 10 eventos mais recentes no log
9. A partir daqui, você pode experimentar consultando os logs ao usar o editor de consultas, definir alertas etc.

## <a name="automating-uploads-optional"></a>Automatizando carregamentos (opcional)

Se você quiser carregar as métricas e os logs de forma agendada, poderá criar um script e executá-lo em um temporizador a cada poucos minutos. Veja abaixo um exemplo de como automatizar os carregamentos usando um script de shell do Linux.

Em seu editor de texto/código favorito, adicione o script a seguir ao arquivo e salve como um arquivo executável de script, como. sh (Linux/Mac) ou. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Tornar o arquivo de script executável

```console
chmod +x myuploadscript.sh
```

Executar o script a cada 20 minutos:

```console
watch -n 1200 ./myuploadscript.sh
```

Você também pode usar um Agendador de trabalho como cron ou Windows Agendador de Tarefas ou um orquestrador como Ansible, Puppet ou chefe.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Diretrizes gerais sobre como exportar e carregar o uso, métricas

As operações CRUD (criar, ler, atualizar e excluir) nos serviços de dados habilitados para o Arc do Azure são registradas para fins de cobrança e monitoramento. Há serviços em segundo plano que monitoram essas operações CRUD e calculam o consumo adequadamente. O cálculo real do uso ou do consumo ocorre de forma programada e é feito em segundo plano. 

Durante a visualização, esse processo ocorre à noite. As diretrizes gerais são carregar o uso apenas uma vez por dia. Quando as informações de uso são exportadas e carregadas várias vezes no mesmo período de 24 horas, somente o inventário de recursos é atualizado no portal do Azure, mas não no uso do recurso.

Para carregar métricas, o Azure monitor só aceita os últimos 30 minutos de dados ([saiba mais](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). As diretrizes para carregar métricas são carregar as métricas imediatamente após criar o arquivo de exportação para que você possa exibir o conjunto de dados inteiro em portal do Azure. Por exemplo, se você exportou as métricas às 2:00 PM e executou o comando upload às 2:50 PM. Como Azure Monitor só aceita dados nos últimos 30 minutos, talvez você não veja nenhum dado no Portal. 

## <a name="next-steps"></a>Próximas etapas

[Carregar dados de cobrança no Azure e exibi-los no portal do Azure](view-billing-data-in-azure.md)

[Exibir recurso do controlador de dados de arco do Azure no portal do Azure](view-data-controller-in-azure-portal.md)