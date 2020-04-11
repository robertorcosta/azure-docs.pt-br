---
title: Entenda a ferramenta de migração para alertas do Azure Monitor
description: Entenda como funciona a ferramenta de migração de alertas e soluciona problemas.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114318"
---
# <a name="understand-how-the-migration-tool-works"></a>Entender como a ferramenta de migração funciona

Como [anunciado anteriormente,](monitoring-classic-retirement.md)os alertas clássicos no Azure Monitor estão sendo retirados até 31 de agosto de 2019 (originalmente era 30 de junho de 2019). Uma ferramenta de migração está disponível no portal Azure para clientes que usam regras clássicas de alerta e que querem acionar a migração.

Este artigo explica como funciona a ferramenta de migração voluntária. Também descreve remédios para alguns problemas comuns.

> [!NOTE]
> Devido ao atraso na implantação da ferramenta de migração, a data de aposentadoria para migração de alertas clássicos foi prorrogada para 31 de agosto de [2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a partir da data originalmente anunciada de 30 de junho de 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regras de alerta clássico que não serão migradas

> [!IMPORTANT]
> Os alertas de registro de atividade (incluindo alertas de saúde do Serviço) e alertas de log não são afetados pela migração. A migração só se aplica às regras clássicas de alerta descritas [aqui.](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)

Embora a ferramenta possa migrar quase todas as [regras clássicas de alerta,](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)existem algumas exceções. As seguintes regras de alerta não serão migradas usando a ferramenta (ou durante a migração automática a partir de setembro de 2019):

- Regras clássicas de alerta em métricas de hóspedes de máquinavirtual (tanto Windows quanto Linux). Consulte a [orientação para recriar tais regras de alerta em novos alertas métricos](#guest-metrics-on-virtual-machines) mais tarde neste artigo.
- Regras clássicas de alerta em métricas clássicas de armazenamento. Consulte a [orientação para monitorar suas contas clássicas de armazenamento](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regras clássicas de alerta em algumas métricas da conta de armazenamento. Veja [detalhes](#storage-account-metrics) mais tarde neste artigo.
- Regras clássicas de alerta em algumas métricas do Cosmos DB. Veja [detalhes](#cosmos-db-metrics) mais tarde neste artigo.
- Regras clássicas de alerta em todas as máquinas virtuais clássicas e métricas de serviços em nuvem (Microsoft.ClassicCompute/virtualMachines e Microsoft.ClassicCompute/domainNames/slots/roles). Veja [detalhes](#classic-compute-metrics) mais tarde neste artigo.

Se sua assinatura tiver regras clássicas, você deve migrar manualmente. Como não podemos fornecer uma migração automática, quaisquer alertas métricos clássicos existentes desses tipos continuarão a funcionar até junho de 2020. Esta extensão lhe dá tempo para passar para novos alertas. Você também pode continuar a criar novos alertas clássicos sobre as exceções listadas acima até junho de 2020. No entanto, para todo o resto, nenhum novo alerta clássico pode ser criado após agosto de 2019.

> [!NOTE]
> Além das exceções listadas acima, se suas regras clássicas de alerta forem inválidas, ou seja, estão em [métricas depreciadas](#classic-alert-rules-on-deprecated-metrics) ou recursos que foram excluídos, eles não serão migrados e não estarão disponíveis após a aposentadoria do serviço.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de hóspedes em máquinas virtuais

Antes de criar novos alertas métricos sobre métricas de hóspedes, as métricas dos hóspedes devem ser enviadas para a loja de métricas personalizadas do Azure Monitor. Siga estas instruções para ativar a pia do Monitor Azure nas configurações de diagnóstico:

- [Habilitando métricas de hóspedes para VMs do Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Habilitando métricas de hóspedes para VMs Linux](collect-custom-metrics-linux-telegraf.md)

Depois que essas etapas são feitas, você pode criar novos alertas métricos nas métricas dos hóspedes. E depois de criar novos alertas métricos, você pode excluir alertas clássicos.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos em contas de armazenamento podem ser migrados, exceto alertas sobre essas métricas:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentClientOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

As regras clássicas de alerta sobre métricas percent devem ser migradas com base [no mapeamento entre métricas antigas e novas de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Os limites precisarão ser modificados adequadamente porque a nova métrica disponível é absoluta.

As regras clássicas de alerta no AnonymousThrottlingError, SASThrottlingError e ThrottlingError devem ser divididas em dois novos alertas porque não há uma métrica combinada que forneça a mesma funcionalidade. Os limites precisarão ser adaptados adequadamente.

### <a name="cosmos-db-metrics"></a>Métricas do Cosmos DB

Todos os alertas clássicos nas métricas do Cosmos DB podem ser migrados, exceto alertas sobre essas métricas:

- Solicitações médias por segundo
- Nível de Consistência
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Erro interno do servidor
- RUPM max consumido por minuto
- Max Rus por segundo
- Solicitações falhadas da contagem de Mongo
- Mongo excluir solicitações falhadas
- Solicitações com falha de inserção do Mongo
- Outras solicitações fracassadas da Mongo
- Mongo Outra taxa de solicitação
- Taxa de solicitação de outros mongo
- Solicitações falhadas da Consulta Mongo
- Solicitações com falha da atualização do Mongo
- Latência de leitura observada
- Latência de gravação observada
- Disponibilidade do serviço
- Capacidade de Armazenamento
- Pedidos estrangulados
- Total de Solicitações

Média de solicitações por segundo, nível de consistência, RUPM max consumido por minuto, Max RUs por segundo, Latência de leitura observada, latência de gravação observada, capacidade de armazenamento não estão disponíveis no [novo sistema.](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)

Alertas sobre métricas de solicitação como Http 2xx, Http 3xx, Http 400, Http 401, Internal Server Error, Service Availability, Throttled Requests e Total Requests não são migrados porque a forma como as solicitações são contadas é diferente entre métricas clássicas e novas métricas. Os alertas sobre estes precisarão ser recriados manualmente com os limites ajustados.

Os alertas sobre as métricas de solicitações com falha do Mongo devem ser divididos em vários alertas porque não há uma métrica combinada que forneça a mesma funcionalidade. Os limites precisarão ser adaptados adequadamente.

### <a name="classic-compute-metrics"></a>Métricas clássicas de computação

Quaisquer alertas sobre métricas clássicas de computação não serão migrados usando a ferramenta de migração, pois os recursos clássicos de computação ainda não são suportados com novos alertas. O suporte para novos alertas sobre esses tipos de recursos será adicionado no futuro. Uma vez disponível, os clientes devem recriar novas regras de alerta equivalentes com base em suas regras clássicas de alerta antes de junho de 2020.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regras clássicas de alerta sobre métricas depreciadas

Estas são regras clássicas de alerta sobre métricas que foram previamente suportadas, mas eventualmente foram preteridas. Uma pequena porcentagem de clientes pode ter regras de alerta clássicas inválidas em tais métricas. Como essas regras de alerta são inválidas, elas não serão migradas.

| Tipo de recurso| Métricas depreciadas |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent compute_limit. |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent compute_limit. |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servidores/bancos de dados | service_level_objective, storage_limit, storage_used, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingAmbientes/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingAmbientes/workerpools | bytesrecebido, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Como novas regras de alerta e grupos de ação equivalentes são criados

A ferramenta de migração converte suas regras clássicas de alerta em novas regras de alerta equivalentes e grupos de ação. Para a maioria das regras clássicas de alerta, novas regras `windowSize` `aggregationType`de alerta equivalentes estão na mesma métrica com as mesmas propriedades como e . No entanto, existem algumas regras clássicas de alerta estão em métricas que têm uma métrica diferente e equivalente no novo sistema. Os seguintes princípios aplicam-se à migração de alertas clássicos, a menos que especificados na seção abaixo:

- **Frequência**: Define a frequência com que uma regra de alerta clássica ou nova verifica a condição. As `frequency` regras de alerta clássicas não eram configuráveis pelo usuário e sempre foram 5 mins para todos os tipos de recursos, exceto componentes do Application Insights para os quais ele foi de 1 min. Assim, a frequência de regras equivalentes também é definida para 5 min e 1 min, respectivamente.
- **Tipo de agregação**: Define como a métrica é agregada sobre a janela de interesse. O `aggregationType` também é o mesmo entre alertas clássicos e novos alertas para a maioria das métricas. Em alguns casos, uma vez que a métrica é `aggregationType` diferente `primary Aggregation Type` entre alertas clássicos e novos alertas, é utilizado o equivalente ou o definido para a métrica.
- **Unidades**: Propriedade da métrica em que o alerta é criado. Algumas métricas equivalentes têm unidades diferentes. O limiar é ajustado adequadamente conforme necessário. Por exemplo, se a métrica original tem segundos como unidades, mas a nova métrica equivalente tem milissegundos como unidades, o limiar original é multiplicado por 1000 para garantir o mesmo comportamento.
- **Tamanho da janela**: Define a janela sobre a qual os dados métricos são agregados para comparar com o limiar. Para `windowSize` valores padrão como 5mins, 15mins, 30mins, 1h, 3h, 6 horas, 12 horas, 1 dia, não há alteração para nova regra de alerta equivalente. Para outros valores, `windowSize` o mais próximo é escolhido para ser usado. Para a maioria dos clientes, não há impacto com essa mudança. Para uma pequena porcentagem de clientes, pode haver a necessidade de ajustar o limiar para obter exatamente o mesmo comportamento.

Nas seções a seguir, detalhamos as métricas que possuem uma métrica diferente e equivalente no novo sistema. Qualquer métrica que permaneça a mesma para regras clássicas e novas de alerta não está listada. Você pode encontrar uma lista de métricas suportadas no novo sistema [aqui](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageContas/serviços

Para serviços de conta de armazenamento como blob, tabela, arquivo e fila, as seguintes métricas são mapeadas para métricas equivalentes, conforme mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Métrica de transações com as dimensões "ResponseType"="AuthorizationError" e "Authentication" = "Anonymous"| |
| AnonymousClientOtherError | Métrica de transações com as dimensões "ResponseType"="ClientOtherError" e "Authentication" = "Anonymous" | |
| Erro anônimo de tempo de cliente| Métrica de transações com as dimensões "ResponseType"="ClientTimeOutError" e "Autenticação" = "Anônimo" | |
| AnonymousNetworkError | Métrica de transações com as dimensões "ResponseType"="NetworkError" e "Authentication" = "Anonymous" | |
| AnonymousServerOtherError | Métrica de transações com as dimensões "ResponseType"="ServerOtherError" e "Authentication" = "Anonymous" | |
| Erro de saída do servidor anônimo | Métrica de transações com as dimensões "ResponseType"="ServerTimeOutError" e "Autenticação" = "Anônimo" | |
| AnonymousSuccess | Métrica de transações com as dimensões "ResponseType"="Success" e "Authentication" = "Anonymous" | |
| AuthorizationError | Métrica de transações com as dimensões "ResponseType"="AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | Use `aggregationType` 'média' em vez de 'último'. Métrica só se aplica aos serviços Blob |
| ClientOtherError | Métrica de transações com as dimensões "ResponseType"="ClientOtherError"  | |
| ClientTimeoutError | Métrica de transações com as dimensões "ResponseType"="ClientTimeOutError" | |
| ContainerCount | ContainerCount | Use `aggregationType` 'média' em vez de 'último'. Métrica só se aplica aos serviços Blob |
| NetworkError | Métrica de transações com as dimensões "ResponseType"="NetworkError" | |
| ObjectCount | BlobCount| Use `aggregationType` 'média' em vez de 'último'. Métrica só se aplica aos serviços Blob |
| SASAuthorizationError | Métrica de transações com as dimensões "ResponseType"="AuthorizationError" e "Authentication" = "SAS" | |
| SASClientOtherError | Métrica de transações com as dimensões "ResponseType"="ClientOtherError" e "Authentication" = "SAS" | |
| SASClientTimeOutError | Métrica de transações com as dimensões "ResponseType"="ClientTimeOutError" e "Autenticação" = "SAS" | |
| SASNetworkError | Métrica de transações com as dimensões "ResponseType"="NetworkError" e "Authentication" = "SAS" | |
| SASServerOtherError | Métrica de transações com as dimensões "ResponseType"="ServerOtherError" e "Authentication" = "SAS" | |
| SASServerTimeOutError | Métrica de transações com as dimensões "ResponseType"="ServerTimeOutError" e "Autenticação" = "SAS" | |
| SASSuccess | Métrica de transações com as dimensões "ResponseType"="Success" e "Authentication" = "SAS" | |
| ServerOtherError | Métrica de transações com as dimensões "ResponseType"="ServerOtherError" | |
| Erro de saída de tempo do servidor | Métrica de transações com as dimensões "ResponseType"="ServerTimeOutError"  | |
| Sucesso | Métrica de transações com as dimensões "ResponseType"="Sucesso" | |
| TotalBillableRequests| Transactions | |
| TotalEgress | Saída | |
| TotalIngress | Entrada | |
| TotalRequests | Transactions | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/componentes

Para insights de aplicativos, métricas equivalentes são como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| disponibilidade.disponibilidadeMetric.value | disponibilidadeResultados/disponibilidadePorcentagem|   |
| disponibilidade.duraçãoMetric.value | availabilityResults/duration| Multiplique o limiar original por 1000 como unidades para métrica clássica estão em segundos e para novo estão em milissegundos.  |
| basicExceptionBrowser.count | exceptions/browser|  Use `aggregationType` 'contagem' em vez de 'soma'. |
| basicExceptionServer.count | exceptions/server| Use `aggregationType` 'contagem' em vez de 'soma'.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Multiplique o limiar original por 1000 como unidades para métrica clássica estão em segundos e para novo estão em milissegundos.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Multiplique o limiar original por 1000 como unidades para métrica clássica estão em segundos e para novo estão em milissegundos. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Multiplique o limiar original por 1000 como unidades para métrica clássica estão em segundos e para novo estão em milissegundos.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Multiplique o limiar original por 1000 como unidades para métrica clássica estão em segundos e para novo estão em milissegundos.  |
| clientPerformance.total.value | browserTimings/totalDuration| Multiplique o limiar original por 1000 como unidades para métrica clássica estão em segundos e para novo estão em milissegundos.  |
| desempenhoCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| desempenhoCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| desempenhoCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| desempenhoCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| desempenhoCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| O limiar precisará ser adequadamente modificado, pois a métrica original estava em todos os núcleos e a nova métrica é normalizada em um núcleo. A ferramenta de migração não altera limites.  |
| desempenhoCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| desempenhoCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| desempenhoCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| desempenhoCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| desempenhoCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| Multiplique o limiar original por 1000 como unidades para métrica clássica estão em segundos e para novo estão em milissegundos.  |
| request.rate | solicitações/taxa|   |
| requestFailed.count | requests/failed| Use `aggregationType` 'contagem' em vez de 'soma'.   |
| view.count | pageViews/count| Use `aggregationType` 'contagem' em vez de 'soma'.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Para Cosmos DB, métricas equivalentes são como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Tamanho dos dados | DataUsage| |
| Contagem de documentos | DocumentCount||
| Tamanho do Índice | IndexUsage||
| Taxa de solicitação de contagem de Mongo| MongoRequestCharge com a dimensão "CommandName" = "contagem"||
| Taxa de solicitação de contagem de Mongo | MongoRequestsContagem com a dimensão "CommandName" = "contagem"||
| Carga de solicitação de exclusão de Mongo | MongoRequestCharge com a dimensão "CommandName" = "delete"||
| Taxa de solicitação de exclusão de Mongo | MongoRequestsContagcontagem com a dimensão "CommandName" = "delete"||
| Mongo Insert Request Charge | MongoRequestCharge com a dimensão "CommandName" = "insert"||
| Taxa de solicitação de inserção de Mongo | MongoRequestsContagcontagem com a dimensão "CommandName" = "insert"||
| Taxa de solicitação de solicitação de consulta de Mongo | MongoRequestCharge com a dimensão "CommandName" = "find"||
| Taxa de solicitação de consulta de Mongo | MongoRequestsContagem com a dimensão "CommandName" = "find"||
| Carga de solicitação de atualização do Mongo | MongoRequestCharge com a dimensão "CommandName" = "update"||
| Serviço indisponível| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Como grupos de ação equivalentes são criados

As regras clássicas de alerta tinham ações de e-mail, webhook, app lógico e runbook vinculadas à própria regra de alerta. Novas regras de alerta usam grupos de ação que podem ser reutilizados em várias regras de alerta. A ferramenta de migração cria um grupo de ação único para as mesmas ações, independentemente de quantas regras de alerta estejam usando a ação. Grupos de ação criados pela ferramenta de migração usam o formato de nomeação 'Migrated_AG*'.

> [!NOTE]
> Alertas clássicos enviavam e-mails localizados com base na localização do administrador clássico quando usados para notificar funções clássicas de administrador. Novos e-mails de alerta são enviados via Action Groups e estão apenas em inglês.

## <a name="rollout-phases"></a>Fases de distribuição

A ferramenta de migração está sendo desmembrada em fases para clientes que usam regras clássicas de alerta. Os proprietários de assinaturas receberão um e-mail quando a assinatura estiver pronta para ser migrada usando a ferramenta.

> [!NOTE]
> Como a ferramenta está sendo implantada em fases, você pode ver que algumas de suas assinaturas ainda não estão prontas para serem migradas durante as fases iniciais.

A maioria das assinaturas está atualmente marcada como pronta para migração. Apenas as assinaturas que possuem alertas clássicos sobre os seguintes tipos de recursos ainda não estão prontas para migração.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/componentes

## <a name="who-can-trigger-the-migration"></a>Quem pode desencadear a migração?

Qualquer usuário que tenha a função incorporada de Monitorar Contribuinte no nível de assinatura pode desencadear a migração. Os usuários que têm uma função personalizada com as seguintes permissões também podem acionar a migração:

- */leitura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Além de ter permissões acima, sua assinatura deve ser registrada adicionalmente no provedor de recursos Microsoft.AlertsManagement. Isso é necessário para migrar com sucesso alertas de anomalia de falha em insights de aplicativos. 

## <a name="common-problems-and-remedies"></a>Problemas e remédios comuns

Depois [de acionar a migração,](alerts-using-migration-tool.md)você receberá e-mail nos endereços fornecidos para notificá-lo de que a migração está concluída ou se alguma ação for necessária de você. Esta seção descreve alguns problemas comuns e como lidar com eles.

### <a name="validation-failed"></a>Falha na validação

Devido a algumas mudanças recentes nas regras clássicas de alerta em sua assinatura, a assinatura não pode ser migrada. Este problema é temporário. Você pode reiniciar a migração depois que o status de migração voltar **Pronto para migração** em poucos dias.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Bloqueio de escopo impedindo-nos de migrar suas regras

Como parte da migração, novos alertas métricos e novos grupos de ação serão criados e, em seguida, regras clássicas de alerta serão excluídas. No entanto, um bloqueio de escopo pode nos impedir de criar ou excluir recursos. Dependendo do bloqueio de escopo, algumas ou todas as regras não poderiam ser migradas. Você pode resolver esse problema removendo o bloqueio de escopo para a assinatura, grupo de recursos ou recurso, que está listado na [ferramenta de migração,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)e acionando a migração novamente. O bloqueio de escopo não pode ser desativado e deve ser removido durante a duração do processo de migração. [Saiba mais sobre como gerenciar bloqueios de escopo](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Política com efeito 'Negar' nos impede de migrar suas regras

Como parte da migração, novos alertas métricos e novos grupos de ação serão criados e, em seguida, regras clássicas de alerta serão excluídas. No entanto, uma política pode nos impedir de criar recursos. Dependendo da apólice, algumas ou todas as regras não poderiam ser migradas. As políticas que estão bloqueando o processo estão listadas na [ferramenta de migração](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Resolva este problema por qualquer um:

- Excluindo as assinaturas ou grupos de recursos durante a duração do processo de migração da atribuição de diretiva. [Saiba mais sobre o gerenciamento do escopo de exclusão de políticas](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Remoção ou alteração de efeito para 'auditoria' ou 'apêndice' (que, por exemplo, pode resolver problemas relacionados a tags ausentes). [Saiba mais sobre o efeito de gestão de políticas](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Próximas etapas

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para a migração](alerts-prepare-migration.md)
