---
title: Registro em log da Análise de Armazenamento do Azure
description: Saiba como registrar detalhes sobre solicitações feitas no Azure Storage.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5b94a97f1286e1273300014e4eef140be412436b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637174"
---
# <a name="azure-storage-analytics-logging"></a>Registro em log da Análise de Armazenamento do Azure

A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

 O log da Análise de Armazenamento não está habilitado por padrão na conta de armazenamento. Você pode habilitá-la no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [Monitorar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as propriedades do [serviço Get Blob,](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) [obtenha propriedades de serviço de fila](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)e obtenha propriedades de serviço de [mesa](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) para habilitar o Storage Analytics para cada serviço.

 As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto final blob, mas não em seus pontos finais de Tabela ou Fila, apenas logs relativos ao serviço Blob serão criados.

> [!NOTE]
>  No momento, o log da Análise de Armazenamento está disponível apenas para os serviços de Blob, Fila e Tabela. No entanto, não há suporte para a conta de armazenamento Premium.

## <a name="requests-logged-in-logging"></a>Solicitações logadas
### <a name="logging-authenticated-requests"></a>Solicitações de registro em log autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações de dados de análise

  As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

### <a name="logging-anonymous-requests"></a>Registro em log de solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e o servidor
- Solicitações GET com falha com o código de erro 304 (Não Modificado)

  Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

## <a name="how-logs-are-stored"></a>Como os logs são armazenados

Todos os logs são armazenados em blobs de blocos em um contêiner denominado `$logs`, que é criado automaticamente quando a Análise de Armazenamento é habilitada para uma conta de armazenamento. O contêiner `$logs` está localizado no namespace de blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contêiner não pode ser excluído quando a análise de armazenamento tiver sido habilitada, embora seu conteúdo possa ser excluído. Se você usar sua ferramenta de navegação de armazenamento para navegar diretamente para o contêiner, você verá todas as bolhas que contêm seus dados de registro.

> [!NOTE]
>  O `$logs` contêiner não é exibido quando uma operação de listagem de contêineres é realizada, como a operação Contêiner de Lista. Ele deve ser acessado diretamente. Por exemplo, você pode usar a operação List Blobs `$logs` para acessar as bolhas no recipiente.

Como as solicitações são registradas, a análise de armazenamento carrega resultados intermediários como blocos. Periodicamente, a análise de armazenamento confirmará esses blocos e os disponibilizará como um blob. Pode levar até uma hora para que os dados de registro apareçam nas bolhas no recipiente **$logs** porque a freqüência em que o serviço de armazenamento libera os escritores de log. Podem existir registros duplicados para os logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando **RequestId** e o número da **Operação**.

Se tiver um grande volume de dados de log com vários arquivos para cada hora, você poderá usar os metadados de blob para determinar quais dados o log contém examinando os campos de metadados do blob. Isso também é útil porque às vezes pode haver um atraso enquanto os dados são gravados nos arquivos de log: os metadados blob dão uma indicação mais precisa do conteúdo blob do que o nome blob.

A maioria das ferramentas de navegação de armazenamento permite que você exiba os metadados de blobs. Além disso, você pode ler essas informações usando o PowerShell ou de forma programática. O seguinte snippet do PowerShell é um exemplo de filtragem da lista de blobs de log pelo nome, para especificar uma hora, e por metadados, para identificar apenas os logs que contêm operações de **gravação**.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Para obter informações sobre a listagem de bolhas de forma programática, consulte [Enumerando recursos blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) e [configuração e recuperação de propriedades e metadados para recursos blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de log

 Cada log será gravado no seguinte formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela a seguir descreve cada atributo no nome do log:

|Atributo|Descrição|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob`, `table` ou `queue`.|
|`YYYY`|O ano de quatro dígitos do log. Por exemplo: `2011`|
|`MM`|O mês de dois dígitos do log. Por exemplo: `07`|
|`DD`|O dia de dois dígitos do log. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora inicial dos logs, no formato UTC de 24 horas. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto inicial dos logs. **Nota:**  Esse valor não é suportado na versão atual do Storage `00`Analytics, e seu valor será sempre .|
|`<counter>`|Um contador baseado em zero com seis dígitos que indica o número de blobs de log gerado para o serviço de armazenamento em um período de uma hora. Esse contador se inicia em `000000`. Por exemplo: `000001`|

 O nome a seguir é um exemplo de nome de log completo, que combina os exemplos anteriores:

 `blob/2011/07/31/1800/000001.log`

 O URI a seguir é um de exemplo de URI que pode ser usado para acessar o log anterior:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando uma solicitação de armazenamento estiver conectada, o nome do log resultante se correlaciona com a hora quando concluir a operação solicitada. Por exemplo, se uma solicitação GetBlob foi concluída às 18h30, em 31/07/2011, o log será gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de log

 Todos os blobs de log são armazenados com metadados que podem ser usados para identificar os dados de log que contém o blob. A tabela a seguir descreve cada atributo de metadados:

|Atributo|Descrição|
|---------------|-----------------|
|`LogType`|Descreve se o log contém informações referentes a operações de ler, gravar ou de exclusão. Esse valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3:`read,write,delete`|
|`StartTime`|A hora da entrada mais antiga do log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|A hora da entrada mais recente do log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato do log.|

 A lista a seguir exibe exemplos de metadados que usam os exemplos anteriores:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Habilitar o registro de armazenamento

Você pode habilitar o registro de armazenamento com portais Azure, PowerShell e SDKs de armazenamento.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Habilite o registro de armazenamento usando o portal Azure  

No portal Azure, use a lâmina **de configurações diagnósticos (clássica)** para controlar o registro de armazenamento, acessível a partir da seção **Monitoramento (clássico)** da **lâmina Menu**de uma conta de armazenamento .

Você pode especificar os serviços de armazenamento que deseja registrar e o período de retenção (em dias) para os dados registrados.  

### <a name="enable-storage-logging-using-powershell"></a>Habilite o registro de armazenamento usando o PowerShell  

 Você pode usar o PowerShell em seu computador local para configurar o Log de Armazenamento em sua conta de armazenamento usando o cmdlet do PowerShell do Azure **Get-AzureStorageServiceLoggingProperty** para recuperar as configurações atuais e o cmdlet **Set-AzureStorageServiceLoggingProperty** para alterar as configurações atuais.  

 Os cmdlets que controlam o Log de Armazenamento usam um parâmetro **LoggingOperations** que é uma cadeia de caracteres contendo uma lista de tipos de solicitação separados por vírgulas para registro em log. Os três possíveis tipos de solicitação são **leitura**, **gravação** e **exclusão**. Para desativar o log, use o valor **none** no parâmetro **LoggingOperations**.  

 O comando a seguir muda para registrar para ler, gravar e excluir solicitações no serviço Fila em sua conta de armazenamento padrão com retenção definida para cinco dias:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 O seguinte comando desativa o registro em log para o serviço de tabela em sua conta de armazenamento padrão:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Para saber mais sobre como configurar os cmdlets do PowerShell do Azure para funcionar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão para usar, consulte: [Como instalar e configurar o PowerShell do Azure](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Habilite o registro de armazenamento de forma programática  

 Além de usar o portal Azure ou os cmdlets Do Azure PowerShell para controlar o registro de armazenamento, você também pode usar uma das APIs de armazenamento do Azure. Por exemplo, se estiver usando uma linguagem .NET, você poderá usar a Biblioteca do Cliente de Armazenamento.  

 As classes **CloudBlobClient**, **CloudQueueClient** e **CloudTableClient** têm métodos como **SetServiceProperties** e **SetServicePropertiesAsync**, que usam um objeto **ServiceProperties** como um parâmetro. Você pode usar o objeto **ServiceProperties** para configurar o Log de Armazenamento. Por exemplo, o seguinte snippet de C# mostra como alterar o que é registrado em log e o período de retenção para a fila de log:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Para obter mais informações sobre o uso de um idioma .NET para configurar o registro de armazenamento, consulte [A referência da biblioteca do cliente de armazenamento](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Para obter informações gerais sobre como configurar o registro de armazenamento usando a API REST, consulte [Ativar e configurar análises de armazenamento](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Baixar dados de registro de registro de armazenamento

 Para exibir e analisar seus dados de log, baixe os blobs que contêm os dados de log nos quais você está interessado para um computador local. Muitas ferramentas de navegação de armazenamento permitem que você baixe blobs da sua conta de armazenamento; você também pode usar a equipe de armazenamento do Azure fornecido pela linha de comando Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) para baixar seus dados de log.  
 
>[!NOTE]
> O `$logs` contêiner não está integrado ao Event Grid, então você não receberá notificações quando os arquivos de registro forem gravados. 

 Para baixar os dados de log nos quais você está interessado e para evitar o download dos mesmos dados de log mais de uma vez:  

-   Use a convenção de nomenclatura de data e hora para blobs contendo dados de log a fim de acompanhar quais blobs você já baixou para análise e não baixar novamente os mesmos dados mais de uma vez.  

-   Use os metadados nos blobs que contêm dados de log para identificar o período específico para o qual o blob contém dados de log a fim de identificar o blob exato que você precisa baixar.  

Para começar com o AzCopy, consulte [Get started com AzCopy](storage-use-azcopy-v10.md) 

O exemplo a seguir mostra como você pode baixar os dados de log para o serviço de fila para as horas a partir das 09:00, 10:00 e 11:00 em 20 de maio de 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Para saber mais sobre como baixar arquivos específicos, consulte [Baixar arquivos específicos](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Após baixar seus dados de log, você pode exibir as entradas de log nos arquivos. Esses arquivos de log usam um formato de texto delimitado que muitas ferramentas de leitura de log são capazes de analisar, incluindo o Microsoft Message Analyzer (para obter mais informações, consulte o guia [Monitoramento, Diagnóstico e Solução de Problemas do Microsoft Azure Storage).](storage-monitoring-diagnosing-troubleshooting.md) Ferramentas diferentes têm recursos diferentes para formatação, filtragem, classificação e pesquisa de anúncios para o conteúdo de seus arquivos de log. Para obter mais informações sobre o formato e o conteúdo do arquivo de log de registro de armazenamento, consulte [o formato de registro de armazenamento](/rest/api/storageservices/storage-analytics-log-format) e as [análises de armazenamento de operações registradas e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Próximas etapas

* [Formato de Log de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Mensagens de operações e status registradas de análise de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas de análise de armazenamento (clássicas)](storage-analytics-metrics.md)
