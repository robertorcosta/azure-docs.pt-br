---
title: Métricas da Análise de Armazenamento do Azure (clássico)
description: Saiba como usar as métricas de Análise de Armazenamento no Armazenamento do Microsoft Azure. Saiba mais sobre métricas de capacidade e transação, como as métricas são armazenadas, habilitando métricas e muito mais.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2f3fa755f61d398ce7f0965fba86262c3e3ec863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021146"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas da Análise de Armazenamento do Azure (clássico)

O Armazenamento do Microsoft Azure usa a solução Análise de Armazenamento para armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação de API e no nível de serviço de armazenamento. A capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para:
- Analisar o uso do serviço de armazenamento.
- Diagnosticar problemas com solicitações feitas no serviço de armazenamento.
- Melhorar o desempenho dos aplicativos que usam um serviço.

 A métrica da Análise de Armazenamento vem habilitada por padrão nas novas contas de armazenamento. Você pode configurar as métricas no [Portal do Azure](https://portal.azure.com/). Para obter mais informações, confira [Monitorar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações Definir Propriedades do Serviço para habilitar a Análise de Armazenamento para cada serviço.  

> [!NOTE]
> As métricas da Análise de Armazenamento estão disponíveis para armazenamento de Blobs do Azure, armazenamento de Filas do Azure, armazenamento de Tabelas do Azure e Arquivos do Azure.
> Agora as métricas de Análise de Armazenamento são métricas clássicas. Recomendamos o uso de [métricas de armazenamento no Azure Monitor](monitor-storage.md), em vez das métricas de Análise de Armazenamento.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um conjunto robusto de dados é registrado em intervalos de horas ou minutos para cada serviço de armazenamento e operações de API solicitadas, o que inclui entrada e saída, disponibilidade, erros e percentuais de solicitação categorizados. Para obter uma lista completa dos detalhes da transação, confira [Esquema da tabela de métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Os dados de transação são registrados no nível de serviço e no nível de operação da API. No nível de serviço, as estatísticas que resumem todas as operações de API solicitadas são gravadas em uma entidade de tabela a cada hora, mesmo que nenhuma solicitação seja feita ao serviço. No nível de operação da API, as estatísticas serão gravadas somente em uma entidade se a operação foi solicitada nessa hora.  

 Por exemplo, se você executar uma operação **GetBlob** em seu serviço de blob, a Métrica da Análise de Armazenamento registrará a solicitação e a incluirá nos dados agregados para o serviço de blob e a operação **GetBlob**. Se nenhuma operação **GetBlob** for solicitada durante a hora, uma entidade não será gravada no *$MetricsTransactionsBlob* dessa operação.  

 As métricas de transação são registradas para solicitações de usuários e solicitações feitas pela própria Análise de Armazenamento. Por exemplo, solicitações pela análise de armazenamento para gravar logs e entidades de tabela são registradas.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade estão disponíveis somente para o serviço blob.

 Os dados de capacidade são gravados diariamente para o serviço blob de uma conta de armazenamento e duas entidades de tabela são gravadas. Uma entidade fornece estatísticas para dados de usuário e a outra fornece estatísticas sobre o contêiner de blob `$logs` usado pela análise de armazenamento. A tabela *$MetricsCapacityBlob* inclui as seguintes estatísticas:  

- **Capacity**: A quantidade de armazenamento utilizada pelo serviço blob da conta de armazenamento em bytes.  
- **ContainerCount**: O número de contêineres de blob no serviço blob da conta de armazenamento.  
- **ObjectCount**: O número de blobs de páginas ou de blocos confirmados e não confirmados no serviço blob da conta de armazenamento.  

  Para saber mais sobre métricas de capacidade, consulte [Esquema da tabela de métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados de métricas para cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para o serviço. Uma tabela é para informações de transação, uma tabela é para informações de transação de minuto e outra tabela é para informações de capacidade. As informações de transação e de transação de minuto consistem em dados de solicitação e resposta. As informações de capacidade consistem em dados de uso de armazenamento. A métrica de horas, a métrica de minutos e a capacidade de um serviço blob da conta de armazenamento são acessadas nas tabelas que são nomeadas conforme descrito na tabela a seguir.  

|Nível de métricas|Nomes da tabela|Suporte para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas por hora, local principal|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Somente versões anteriores a 15 de agosto de 2013. Embora esses nomes ainda tenham suporte, recomendamos que você passe a usar as tabelas a seguir.|  
|Métricas por hora, local principal|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Todas as versões. O suporte para métricas do serviço de arquivo está disponível somente na versão de 5 de abril de 2015 e posterior.|  
|Métricas por minuto, local principal|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Todas as versões. O suporte para métricas do serviço de arquivo está disponível somente na versão de 5 de abril de 2015 e posterior.|  
|Métricas por hora, local secundário|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Todas as versões. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.|  
|Métricas por minuto, local secundário|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. A replicação de redundância geográfica com acesso de leitura deve estar habilitada.|  
|Capacidade (apenas serviço blob)|$MetricsCapacityBlob|Todas as versões.|  

 Essas tabelas são criadas automaticamente quando a Análise de Armazenamento do Azure é habilitada para um ponto de extremidade de serviço de armazenamento. Elas são acessadas por meio do namespace da conta de armazenamento, por exemplo`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. As tabelas de métricas não são exibidas em uma operação de listagem e devem ser acessadas diretamente por meio do nome da tabela.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Habilitar métricas usando o portal do Azure
Siga estas etapas para habilitar as métricas no [portal do Azure](https://portal.azure.com):

1. Vá até sua conta de armazenamento.
1. Selecione **Configurações de diagnóstico (clássicas)** no painel de menus.
1. O **Status** deve ser definido como **Ativado**.
1. Selecione as métricas para os serviços que deseja monitorar.
1. Especifica uma política de retenção para indicar por quanto tempo deve-se manter as métricas e os dados de log.
1. Clique em **Salvar**.

No momento, o [portal do Azure](https://portal.azure.com) não permite configurar métricas mínimas na conta de armazenamento. Você deve habilitar métricas de minutos usando o PowerShell ou programaticamente.

## <a name="enable-storage-metrics-by-using-powershell"></a>Habilitar métricas de armazenamento usando o PowerShell  
Você pode usar o PowerShell no computador local para configurar as métricas de armazenamento na sua conta de armazenamento usando o cmdlet do Azure PowerShell **Get-AzStorageServiceMetricsProperty** para recuperar as configurações atuais. Use o cmdlet **Set-AzStorageServiceMetricsProperty** para alterar as configurações atuais.  

Os cmdlets que controlam as métricas de armazenamento usam os seguintes parâmetros:  

* **ServiceType**: Os possíveis valores são **Blob**, **Fila**, **Tabela** e **Arquivo**.
* **MetricsType**: Os valores possíveis são **Hora** e **Minuto**.  
* **MetricsLevel**: Os valores possíveis são:
   * **Nenhum**: Desativa o monitoramento.
   * **Serviço**: Coleta métricas como entrada e saída, disponibilidade, latência e porcentagens de êxitos, que são agregadas aos serviços blob, fila, tabela e arquivo.
   * **ServiceAndApi**: Além das métricas de serviço, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API do Serviço de Armazenamento do Microsoft Azure.

Por exemplo, o comando a seguir ativa a métrica de minutos para o serviço blob na conta de armazenamento com o período de retenção definido para cinco dias: 

> [!NOTE]
> Este comando pressupõe que você entrou na sua assinatura do Azure usando o comando`Connect-AzAccount`.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Substitua o valor do espaço reservado `<resource-group-name>` pelo nome do grupo de recursos.      
* Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.



O comando a seguir recupera o nível de métricas por hora atual e dias de retenção para o serviço blob na conta de armazenamento padrão:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Para saber mais sobre como configurar os cmdlets do PowerShell do Azure para funcionar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão para usar, consulte [Instalar e configurar o Microsoft Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Habilitar métricas de armazenamento programaticamente  
Além de usar o Portal do Azure ou os cmdlets do Azure PowerShell para controlar as métricas de armazenamento, você também pode usar uma das APIs de Armazenamento do Microsoft Azure. Por exemplo, se você usa uma linguagem .NET, pode usar a biblioteca de clientes do Armazenamento do Microsoft Azure.  

As classes **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient** e **CloudFileClient** têm métodos como **SetServiceProperties** e **SetServicePropertiesAsync**, que usam um objeto **ServiceProperties** como parâmetro. Você pode usar o objeto **ServiceProperties** para configurar Métricas de Armazenamento. Por exemplo, o seguinte snippet de C# mostra como alterar o nível de métricas e os dias de retenção para as métricas de fila de hora:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Para obter mais informações sobre como usar uma linguagem .NET para configurar Métricas de Armazenamento, consulte [Bibliotecas de clientes do Armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Para obter informações gerais sobre a configuração de métricas de armazenamento usando a API REST, consulte [Habilitação e configuração da Análise de Armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="view-storage-metrics"></a>Exibir métricas de armazenamento  
Após configurar as métricas Análise de Armazenamento para monitorar sua conta de armazenamento, a Análise de Armazenamento registra as métricas em um conjunto conhecido de tabelas na sua conta de armazenamento. Você pode configurar gráficos para exibir as métricas por hora no [portal do Azure](https://portal.azure.com):

1. Acesse sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Selecione **Métricas (clássicas)** no painel de menus para o serviço cujas métricas você deseja exibir.
1. Selecione o gráfico que você deseja configurar.
1. No painel **Editar gráfico**, selecione o **Intervalo de tempo**, **Tipo de gráfico** e as métricas que você deseja exibir no gráfico.

Na seção **Monitoramento (clássico)** do painel de menus da conta de armazenamento no portal do Azure, você pode configurar as [Regras de alerta](#metrics-alerts). Por exemplo, você pode enviar alertas de email para notificar quando uma métrica específica atinge determinado valor.

Se você quiser baixar as métricas para armazenamento a longo prazo ou para analisá-las localmente, você deve usar uma ferramenta ou escrever um código para ler as tabelas. Você deve baixar a métrica de minutos para análise. As tabelas não aparecem quando você lista todas as tabelas em sua conta de armazenamento, mas você pode acessá-las diretamente por nome. Muitas ferramentas de navegação de armazenamento reconhecem essas tabelas e permitem que você as exiba diretamente. Para obter uma lista das ferramentas disponíveis, confira as [ferramentas do cliente de Armazenamento do Microsoft Azure](/azure/storage/storage-explorers).

|Métricas|Nomes da tabela|Observações| 
|-|-|-|  
|Métricas por hora|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Nas versões anteriores a 15 de agosto de 2013, essas tabelas eram conhecidas como:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> As métricas do serviço de arquivo estão disponíveis a partir da versão de 5 de abril de 2015.|  
|Métricas por minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Só podem ser habilitadas usando o PowerShell ou programaticamente.<br /><br /> As métricas do serviço de arquivo estão disponíveis a partir da versão de 5 de abril de 2015.|  
|Capacity|$MetricsCapacityBlob|Somente para serviço blob.|  

Para obter detalhes completos dos esquemas dessas tabelas, confira [Esquema de tabelas das métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema). As linhas de exemplo a seguir mostram apenas um subconjunto das colunas disponíveis, mas ilustram alguns recursos importantes da maneira como as métricas de armazenamento salvam essas métricas:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Disponibilidade|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|usuário;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Neste exemplo dos dados de métrica de minutos, a chave de partição usa o tempo de resolução minuto. A tecla de registro identifica o tipo de informação que é armazenado na linha. As informações são compostas pelo tipo de acesso e pelo tipo de solicitação:  

-   O tipo de acesso é um **usuário** ou **sistema**, onde o **usuário** refere-se a todas as solicitações de usuário para o serviço de armazenamento e o **sistema** refere-se às solicitações feitas pela Análise de Armazenamento.  
-   O tipo de solicitação é **todos** que, nesse caso, é uma linha de resumo ou identifica a API específica, como **QueryEntity** ou **UpdateEntity**.  

Esses dados de amostra mostram todos os registros para um único minuto (a partir das 11:00). Portanto, o número de solicitações de **QueryEntities** mais o número de solicitações de **QueryEntity** mais o número de solicitações de **UpdateEntity** somam sete. Esse total é mostrado na linha **user:All**. Da mesma forma, você pode derivar a latência média de ponta a ponta 104.4286 na linha **user:All** ao calcular ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Alertas de métricas
Considere a possibilidade de configurar alertas no [portal do Azure](https://portal.azure.com) para ser avisado automaticamente sobre alterações importantes no comportamento dos serviços de armazenamento. Se você usar uma ferramenta de Gerenciador de Armazenamento para baixar esses dados de métricas em um formato delimitado, você pode usar o Microsoft Excel para analisar os dados. Para obter uma lista das ferramentas disponíveis do Gerenciador de Armazenamento, confira [Ferramentas do Cliente de Armazenamento do Microsoft Azure](/azure/storage/storage-explorers). Você pode configurar os alertas no painel de **Alerta (clássico)** , que está acessível em **Monitoramento (clássico)** no painel de menus da conta de armazenamento.

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas por hora ou minuto correspondentes são registrados. No caso de métricas por minuto, diversos minutos de dados podem ser gravados de uma só vez. Esse problema pode levar à agregação de transações de minutos anteriores à transação do minuto atual. Quando esse problema acontece, o serviço de alerta pode não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, o que pode levar ao acionamento inesperado de alertas.
>

## <a name="access-metrics-data-programmatically"></a>Acessar dados de métrica programaticamente  
A listagem a seguir mostra o código C# de exemplo, que acessa a métrica de minutos para um intervalo de minutos e exibe os resultados em uma janela de console. O exemplo de código usa a biblioteca de clientes do Armazenamento do Microsoft Azure versão 4.x ou posterior, que inclui a classe **CloudAnalyticsClient** que simplifica o acesso às tabelas de métricas no armazenamento.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Cobrança sobre métricas de armazenamento
Gravar solicitações para criar entidades de tabela para métricas são cobradas de acordo com as taxas padrão aplicáveis a todas as operações de armazenamento do Azure.  

As solicitações de leitura e exclusão dos dados de métrica também podem ser cobradas de acordo com as taxas padrão. Se você configurou uma política de retenção de dados, não será cobrado quando o Armazenamento do Microsoft Azure excluir dados de métricas antigos. Se você excluir dados de análise, sua conta será cobrada pelas operações de exclusão.  

A capacidade usada pelas tabelas de métricas também pode ser faturada. Use as seguintes informações para estimar a capacidade usada para o armazenamento de dados de métricas:  

-   Se a cada hora em que um serviço utiliza todas as APIs em todos os serviços, aproximadamente 148 KB de dados são armazenados a cada hora nas tabelas de transações de métricas, se você habilitou um resumo no nível de serviço e no nível de API.  
-   Se, a cada hora, um serviço utiliza todas as APIs do serviço, aproximadamente 12 KB de dados são armazenados a cada hora nas tabelas de transações de métricas, se você habilitou apenas um resumo do nível de serviço.  
-   A tabela de capacidade para blobs tem duas linhas adicionadas a cada dia, desde que o usuário tenha optado pelos logs. Esse cenário implica que, a cada dia, o tamanho dessa tabela aumenta em até cerca de 300 bytes.

## <a name="next-steps"></a>Próximas etapas
* [Monitorar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Esquema da tabela de métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Mensagens de operações e status registradas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registro da Análise de Armazenamento](storage-analytics-logging.md)
