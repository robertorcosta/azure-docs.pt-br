---
title: Esquema de recurso baseado em workspace do Azure Monitor Application Insights
description: Saiba mais sobre a nova estrutura e o esquema de tabela para Azure Monitor Application Insights recursos baseados no espaço de trabalho.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 8f0bee64d74cfd5b6abef5c918c023974fda3fcf
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931046"
---
# <a name="workspace-based-resource-changes-preview"></a>Alterações de recursos com base no espaço de trabalho (versão prévia)

Antes da introdução dos [recursos de Application insights baseados em espaço de trabalho](create-workspace-resource.md), Application insights dados eram armazenados separados de outros dados de log no Azure monitor. Ambos são baseados no Azure Data Explorer e usam a mesma linguagem de consulta Kusto (KQL). Isso é descrito em [logs em Azure monitor](../platform/data-platform-logs.md).

Com os dados de recursos Application Insights baseados em espaço de trabalho são armazenados em um espaço de trabalho Log Analytics com outros dados de monitoramento e dados de aplicativos. Isso simplifica sua configuração, permitindo que você analise dados com mais facilidade em várias soluções e aproveite os recursos dos espaços de trabalho.

## <a name="table-structure"></a>Estrutura da tabela

| Nome da tabela herdada | Novo nome da tabela | Descrição |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Dados de resumo dos testes de disponibilidade.|
| browserTimings | AppBrowserTimings | Dados sobre o desempenho do cliente, como o tempo necessário para processar os dados de entrada.|
| dependencies | AppDependencies | Chamadas do aplicativo para outros componentes (incluindo componentes externos) registrados por meio de TrackDependency() – por exemplo, chamadas para API REST, banco de dados ou sistema de arquivos.  |
| customEvents | AppEvents | Eventos personalizados criados pelo seu aplicativo. |
| customMetrics | AppMetrics | Métricas personalizadas criadas pelo seu aplicativo. |
| pageViews | AppPageViews| Dados sobre cada exibição do site com informações do navegador. |
| performanceCounters | AppPerformanceCounters | Medições de desempenho dos recursos de computação que dão suporte ao aplicativo, por exemplo, contadores de desempenho do Windows. |
| solicitações | AppRequests | Solicitações recebidas pelo seu aplicativo. Por exemplo, um registro de solicitação separado é registrado para cada solicitação HTTP que o aplicativo Web recebe.  |
| exceptions | AppSystemEvents | As exceções geradas pelo tempo de execução do aplicativo capturam as exceções do lado do servidor e do lado do cliente (navegadores). |
| traces | AppTraces | Logs detalhados (rastreamentos) emitidos por meio de estruturas de log/código do aplicativo gravados por meio de TrackTrace(). |

## <a name="table-schemas"></a>Esquemas de tabela

As seções a seguir mostram o mapeamento entre os nomes de propriedade clássica e os novos nomes de propriedade de Application Insights com base no espaço de trabalho.  Use essas informações para converter todas as consultas que usam tabelas herdadas.

A maioria das colunas tem o mesmo nome com maiúsculas e minúsculas diferentes. Como KQL diferencia maiúsculas de minúsculas, será necessário alterar cada nome de coluna junto com os nomes de tabela nas consultas existentes. Colunas com alterações além de maiúsculas e minúsculas são realçadas. Você ainda pode usar suas consultas Application Insights clássicas dentro do painel **logs** do recurso Application insights, mesmo que ele seja um recurso baseado em espaço de trabalho. Os novos nomes de propriedade são necessários para a consulta de dentro do contexto da experiência de Log Analytics espaço de trabalho.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Tabela herdada: disponibilidade

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|duration|real|DurationMs|real|
|`id`|string|`Id`|cadeia de caracteres|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|String|
|local|string|Localização|cadeia de caracteres|
|message|string|Mensagem|cadeia de caracteres|
|name|string|Nome|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|performanceBucket|cadeia de caracteres|PerformanceBucket|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|tamanho|real|Tamanho|real|
|sucesso|cadeia de caracteres|Êxito|Bool|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Tabela herdada: browserTimings

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|string|
|name|string|Name|DATETIME|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|performanceBucket|cadeia de caracteres|PerformanceBucket|cadeia de caracteres|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|sendDuration|real|SendDurationMs|real|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|totalDuration|real|TotalDurationMs|real|
|url|string|Url|cadeia de caracteres|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

### <a name="appdependencies"></a>AppDependencies

Tabela herdada: dependências

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|Dados|cadeia de caracteres|Dados|cadeia de caracteres|
|duration|real|DurationMs|real|
|`id`|string|`Id`|cadeia de caracteres|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|String|
|name|string|Nome|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|performanceBucket|cadeia de caracteres|PerformanceBucket|cadeia de caracteres|
|resultCode|cadeia de caracteres|ResultCode|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|sucesso|cadeia de caracteres|Êxito|Bool|
|destino|cadeia de caracteres|Destino|cadeia de caracteres|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|type|cadeia de caracteres|DependencyType|cadeia de caracteres|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

### <a name="appevents"></a>AppEvents

Tabela herdada: customEvents

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|string|
|name|string|Nome|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

### <a name="appmetrics"></a>AppMetrics

Tabela herdada: customMetrics

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|string|
|name|string|Nome|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|
|value|real|removido||
|valueCount|INT|ValueCount|INT|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|Valores|real|Valores|real|

### <a name="apppageviews"></a>AppPageViews

Tabela herdada: pageViews

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|duration|real|DurationMs|real|
|`id`|string|`Id`|cadeia de caracteres|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|String|
|name|string|Nome|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|performanceBucket|cadeia de caracteres|PerformanceBucket|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|url|string|Url|cadeia de caracteres|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Tabela herdada: performanceCounters

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|category|string|Categoria|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|contador|cadeia de caracteres|removido||
|customDimensions|dinâmico|Propriedades|Dinâmico|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|instance|cadeia de caracteres|Instância|cadeia de caracteres|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|string|
|name|string|Nome|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|
|value|real|Valor|real|

### <a name="apprequests"></a>AppRequests

Tabela herdada: solicitações

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|Dinâmico|
|customMeasurements|dinâmico|Medidas|Dinâmico|
|duration|real|DurationMs|Real|
|`id`|cadeia de caracteres|`Id`|String|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|String|
|name|string|Nome|String|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|performanceBucket|cadeia de caracteres|PerformanceBucket|String|
|resultCode|cadeia de caracteres|ResultCode|String|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|source|cadeia de caracteres|Fonte|String|
|sucesso|cadeia de caracteres|Êxito|Bool|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|url|string|Url|String|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

### <a name="appsystemevents"></a>AppSystemEvents

Tabela herdada: exceções

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|assembly|cadeia de caracteres|Assembly|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|dinâmico|
|customMeasurements|dinâmico|Medidas|dinâmico|
|detalhes|dinâmico|Detalhes|dinâmico|
|handledAt|cadeia de caracteres|HandledAt|cadeia de caracteres|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|innermostAssembly|cadeia de caracteres|InnermostAssembly|cadeia de caracteres|
|innermostMessage|cadeia de caracteres|InnermostMessage|cadeia de caracteres|
|innermostMethod|cadeia de caracteres|InnermostMethod|cadeia de caracteres|
|mais interno|cadeia de caracteres|Mais interno|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|string|
|message|string|Mensagem|cadeia de caracteres|
|method|string|Método|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|outerAssembly|cadeia de caracteres|OuterAssembly|cadeia de caracteres|
|outerMessage|cadeia de caracteres|OuterMessage|cadeia de caracteres|
|outerMethod|cadeia de caracteres|OuterMethod|cadeia de caracteres|
|outertype|cadeia de caracteres|Outertype|cadeia de caracteres|
|problemid|cadeia de caracteres|Problemid|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|Nível|INT|SeverityLevel|INT|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|type|cadeia de caracteres|ExceptionType|cadeia de caracteres|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

### <a name="apptraces"></a>AppTraces

Tabela herdada: rastreamentos

|ApplicationInsights|Digite|LogAnalytics|Digite|
|:---|:---|:---|:---|
|appId|cadeia de caracteres|\_ResourceGUID|cadeia de caracteres|
|application_Version|cadeia de caracteres|AppVersion|cadeia de caracteres|
|appName|cadeia de caracteres|\_ResourceId|cadeia de caracteres|
|client_Browser|cadeia de caracteres|ClientBrowser|cadeia de caracteres|
|client_City|cadeia de caracteres|ClientCity|cadeia de caracteres|
|client_CountryOrRegion|cadeia de caracteres|ClientCountryOrRegion|cadeia de caracteres|
|client_IP|cadeia de caracteres|ClientIP|cadeia de caracteres|
|client_Model|cadeia de caracteres|ClientModel|cadeia de caracteres|
|client_OS|cadeia de caracteres|ClientOS|cadeia de caracteres|
|client_StateOrProvince|cadeia de caracteres|ClientStateOrProvince|cadeia de caracteres|
|client_Type|cadeia de caracteres|Clientetype|cadeia de caracteres|
|cloud_RoleInstance|cadeia de caracteres|AppRoleInstance|cadeia de caracteres|
|cloud_RoleName|cadeia de caracteres|AppRoleName|cadeia de caracteres|
|customDimensions|dinâmico|Propriedades|dinâmico|
|customMeasurements|dinâmico|Medidas|dinâmico|
|iKey|cadeia de caracteres|IKey|cadeia de caracteres|
|itemCount|INT|ItemCount|INT|
|itemId|cadeia de caracteres|\_ItemId|cadeia de caracteres|
|itemType|cadeia de caracteres|Type|string|
|message|string|Mensagem|cadeia de caracteres|
|operation_Id|cadeia de caracteres|OperationId|cadeia de caracteres|
|operation_Name|cadeia de caracteres|OperationName|cadeia de caracteres|
|operation_ParentId|cadeia de caracteres|OperationParentId|cadeia de caracteres|
|operation_SyntheticSource|cadeia de caracteres|OperationSyntheticSource|cadeia de caracteres|
|sdkVersion|cadeia de caracteres|SdkVersion|cadeia de caracteres|
|session_Id|cadeia de caracteres|SessionId|cadeia de caracteres|
|Nível|INT|SeverityLevel|INT|
|timestamp|DATETIME|TimeGenerated|DATETIME|
|user_AccountId|cadeia de caracteres|UserAccountId|cadeia de caracteres|
|user_AuthenticatedId|cadeia de caracteres|UserAuthenticatedId|cadeia de caracteres|
|user_Id|cadeia de caracteres|UserId|cadeia de caracteres|

## <a name="next-steps"></a>Próximas etapas

* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas do Analytics](../log-query/log-query-overview.md)

