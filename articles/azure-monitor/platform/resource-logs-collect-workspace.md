---
title: Coletar os logs de recursos do Azure no workspace do Log Analytics
description: Aprenda a transmitir logs de recursos do Azure para um espaço de trabalho do Log Analytics no Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248587"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Colete logs da plataforma Azure no espaço de trabalho do Log Analytics no Azure Monitor
[Os logs de plataforma](platform-logs-overview.md) no Azure, incluindo logs de atividades do Azure e registros de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure da qual dependem. Este artigo descreve a coleta de logins de recursos em um espaço de trabalho do Log Analytics que permite analisá-lo com outros dados de monitoramento coletados no Azure Monitor Logs usando consultas de log poderosas e também para aproveitar outros recursos do Azure Monitor, como alertas e Visualizações. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>O que você pode fazer com logs de plataforma em um espaço de trabalho
A coleta de logins de plataforma em um espaço de trabalho do Log Analytics permite que você analise os logs de todos os seus recursos do Azure e aproveite todos os recursos disponíveis para o [Azure Monitor Logs,](data-platform-logs.md) que inclui o seguinte:

* **Consultas de log** - Crie [consultas de log](../log-query/log-query-overview.md) usando uma linguagem de consulta poderosa para analisar e obter insights rapidamente sobre seus dados de diagnóstico e analisá-los com dados coletados de outras fontes no Azure Monitor.
* **Alerta -** Obtenha uma notificação proativa de condições e padrões críticos identificados em seus registros de recursos usando [alertas de log no Azure Monitor](alerts-log.md).
* **Visualizações** - Fixar os resultados de uma consulta de log em um painel do Azure ou incluí-lo em uma pasta de trabalho como parte de um relatório interativo.

## <a name="prerequisites"></a>Pré-requisitos
Você precisa [criar um novo espaço de trabalho](../learn/quick-create-workspace.md) se você ainda não tiver um. O espaço de trabalho não precisa estar na mesma assinatura que os logs de envio de recursos, desde que o usuário que configura a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
Envie logs de plataforma para um espaço de trabalho do Log Analytics e outros destinos, criando uma configuração de diagnóstico para um recurso do Azure. Consulte [Criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md) para obter detalhes.


## <a name="activity-log-collection"></a>Coleta de registros de atividades
Você pode enviar o registro de atividades de qualquer assinatura única para até cinco espaços de trabalho do Log Analytics. Os dados de registro de recursos coletados em um espaço de trabalho do Log Analytics são armazenados na tabela **AzureActivity.** 

## <a name="resource-log-collection-mode"></a>Modo de coleta de registros de recursos
Os dados de registro de recursos coletados em um espaço de trabalho do Log Analytics são armazenados em tabelas conforme descrito na [estrutura do Azure Monitor Logs](../log-query/logs-structure.md). As tabelas usadas pelos registros de recursos dependem do tipo de coleta que o recurso está usando:

- Diagnóstico do Azure - Todos os dados escritos estão na tabela _AzureDiagnostics._
- Específico do recurso - Os dados são escritos na tabela individual para cada categoria do recurso.

### <a name="azure-diagnostics-mode"></a>Modo Diagnóstico do Azure 
Neste modo, todos os dados de qualquer [configuração de diagnóstico](diagnostic-settings.md) serão coletados na tabela _AzureDiagnostics._ Este é o método legado usado hoje pela maioria dos serviços do Azure.

Uma vez que vários tipos de recursos enviam dados para a mesma tabela, seu esquema é o superconjunto dos esquemas de todos os diferentes tipos de dados que estão sendo coletados.

Considere o seguinte exemplo em que as configurações de diagnóstico estão sendo coletadas no mesmo espaço de trabalho para os seguintes tipos de dados:

- Registros de auditoria do serviço 1 (tendo um esquema composto das colunas A, B e C)  
- Registros de erro do serviço 1 (tendo um esquema composto das colunas D, E e F)  
- Registros de auditoria do serviço 2 (tendo um esquema composto das colunas G, H e I)  

A tabela AzureDiagnostics será a seguinte:  

| ResourceProvider    | Categoria     | Um  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft.Service1 | Loglogs de erros    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | Loglogs de erros    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Específico de recursos
Neste modo, são criadas tabelas individuais no espaço de trabalho selecionado para cada categoria selecionada na configuração diagnóstica. Este método é recomendado, pois torna muito mais fácil trabalhar com os dados em consultas de log, fornece melhor capacidade de descoberta de esquemas e sua estrutura, melhora o desempenho em tempos de latência e consulta de ingestão, e a capacidade de conceder direitos RBAC em um tabela específica. Todos os serviços do Azure eventualmente migrarão para o modo Específico de Recursos. 

O exemplo acima resultaria na criação de três tabelas:
 
- Serviço *de tabela1Loglogs de auditoria* da seguinte forma:

    | Provedor de recursos | Categoria | Um | B | C |
    | -- | -- | -- | -- | -- |
    | Serviço1 | AuditLogs | x1 | y1 | Z1 |
    | Serviço1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Serviço *de tabela1ErrorLogs* da seguinte forma:  

    | Provedor de recursos | Categoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Serviço1 | Loglogs de erros |  q1 | w1 | e1 |
    | Serviço1 | Loglogs de erros |  q2 | w2 | e2 |
    | ... |

- Serviço *de tabela2Loglogs de auditoria* da seguinte forma:  

    | Provedor de recursos | Categoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Serviço2 | AuditLogs | j1 | k1 | l1|
    | Serviço2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Selecione o modo de coleta
A maioria dos recursos do Azure gravará dados no espaço de trabalho no modo **Azure Diagnostic** ou **Resource-Specific** sem lhe dar uma escolha. Consulte a [documentação de cada serviço para](diagnostic-logs-schema.md) obter detalhes sobre qual modo ele usa. Todos os serviços do Azure eventualmente usarão o modo Específico de Recursos. Como parte dessa transição, alguns recursos permitirão que você selecione um modo na configuração de diagnóstico. Você deve especificar o modo específico de recursos para quaisquer novas configurações de diagnóstico, uma vez que isso torna os dados mais fáceis de gerenciar e pode ajudá-lo a evitar migrações complexas em uma data posterior.
  
   ![Seletor de modo de configurações de diagnóstico](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Atualmente, **o diagnóstico do Azure** e o modo específico de **recursos** só podem ser selecionados ao configurar a configuração de diagnóstico no portal Azure. Se você configurar a configuração usando CLI, PowerShell ou API Rest, ela será padrão para **o diagnóstico do Azure**.

Você pode modificar uma configuração de diagnóstico existente para um modo específico de recurso. Neste caso, os dados que já foram coletados permanecerão na tabela _AzureDiagnostics_ até que sejam removidos de acordo com a configuração de retenção do espaço de trabalho. Novos dados serão coletados na tabela dedicada. Use o operador [do sindicato](https://docs.microsoft.com/azure/kusto/query/unionoperator) para consultar dados em ambas as tabelas.

Continue a assistir ao blog [Azure Updates](https://azure.microsoft.com/updates/) para obter anúncios sobre os serviços do Azure que suportam o modo Específico de Recursos.

### <a name="column-limit-in-azurediagnostics"></a>Limite de coluna no AzureDiagnostics
Há um limite de 500 propriedades para qualquer tabela no Azure Monitor Logs. Uma vez alcançado esse limite, todas as linhas que contenham dados com qualquer propriedade fora dos primeiros 500 serão retiradas no momento da ingestão. A tabela *AzureDiagnostics* é particularmente suscetível a esse limite, uma vez que inclui propriedades para todos os serviços do Azure que o escrevem.

Se você estiver coletando registros de recursos de vários serviços, _o AzureDiagnostics_ pode exceder esse limite e os dados serão perdidos. Até que todos os serviços do Azure suportem o modo específico de recursos, você deve configurar recursos para gravar em vários espaços de trabalho para reduzir a possibilidade de atingir o limite de 500 colunas.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure
A Azure Data Factory, devido a um conjunto muito detalhado de logs, é um serviço que é conhecido por escrever um grande número de colunas e potencialmente fazer com que _o AzureDiagnostics_ exceda seu limite. Para quaisquer configurações de diagnóstico configuradas antes da ativação do modo específico de recurso, haverá uma nova coluna criada para cada parâmetro de usuário com nome exclusivo contra qualquer atividade. Mais colunas serão criadas devido à natureza verbosa de entradas e saídas de atividade.
 
Você deve migrar seus logs para usar o modo específico de recursos o mais rápido possível. Se você não conseguir fazê-lo imediatamente, uma alternativa provisória é isolar os logs da Fábrica de Dados do Azure em seu próprio espaço de trabalho para minimizar a chance desses logs impactarem outros tipos de log sendo coletados em seus espaços de trabalho.


## <a name="next-steps"></a>Próximas etapas

* [Leia mais sobre registros de recursos.](platform-logs-overview.md)
* [Crie configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md).
