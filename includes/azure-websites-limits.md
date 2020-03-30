---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304963"
---
| Recurso | Grátis | Compartilhado | Basic | Standard | Premium (v2) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicativos web, móveis ou API](https://azure.microsoft.com/services/app-service/) por [plano de serviço do aplicativo Azure](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano de serviço de aplicativos](../articles/app-service/overview-hosting-plans.md) |10 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância de computação |Compartilhado |Compartilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Dimensionar](../articles/app-service/manage-scale-up.md) (instâncias máximas) |1 compartilhada |1 compartilhada |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |30 dedicados<sup>3</sup>|100 dedicados<sup>4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo da CPU (5 minutos)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a>|
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |Ilimitado, pague a [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão</a> |
| Memória (1 hora) |1.024 MB por plano de serviço de aplicativo |1.024 MB por aplicativo |N/D |N/D |N/D |N/D |
| Largura de banda |165 MB |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Arquitetura do aplicativo |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Soquetes da Web por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| Conexões IP | 600 | 600 | Depende do tamanho da ocorrência<sup>8</sup> | Depende do tamanho da ocorrência<sup>8</sup> | Depende do tamanho da ocorrência<sup>8</sup> | 16.000 |
| [Conexões do depurador](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultâneas por aplicativo |1 |1 |1 |5 |5 |5 |
| Certificados de serviço de aplicativo por assinatura<sup>9</sup>| Sem suporte | Sem suporte |10 |10 |10 |10 |
| Domínios personalizados por aplicativo</a> |0 (somente subdomínio do azurewebsites.net)|500 |500 |500 |500 |500 |
| domínio personalizado [Suporte a SSL](../articles/app-service/configure-ssl-certificate.md) |Certificado curinga não suportado para *.azurewebsites.net disponível por padrão|Certificado curinga não suportado para *.azurewebsites.net disponível por padrão|Conexões SSL de SNI ilimitadas |Conexões SSL de SNI ilimitadas e IP SSL incluídas |Conexões SSL de SNI ilimitadas e IP SSL incluídas | Conexões SSL de SNI ilimitadas e IP SSL incluídas|
| Conexões híbridas por plano | | | 5 | 25 | 200 | 200 |
| Balanceador de carga integrado | |X |X |X |X |X<sup>10</sup> |
| [Sempre ligado](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backups programados](../articles/app-service/manage-backup.md) | | | | Backups programados a cada 2 horas, um máximo de 12 backups por dia (manual + programado) | Backups programados a cada hora, um máximo de 50 backups por dia (manual + programado) | Backups programados a cada hora, um máximo de 50 backups por dia (manual + programado) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitoramento do ponto de extremidade](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slots de preparação](../articles/app-service/deploy-staging-slots.md) por aplicativo| | | |5 |20 |20 |
| Contrato de Nível de Serviço | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup>Aplicativos e cotas de armazenamento são oferecidos por plano de Serviço de Aplicativo, a menos que haja indicação contrária.  
<sup>2</sup>O número real de aplicativos que podem ser hospedados nesses computadores depende da atividade dos aplicativos, do tamanho das instâncias do computador e da utilização do recurso correspondente.  
<sup>3</sup>As instâncias dedicadas podem ter tamanhos diferentes. Para saber mais, veja [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Mais são permitidos mediante solicitação.  
<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo em todos os aplicativos do mesmo plano de serviço do App. O tamanho total de conteúdo de todos os aplicativos em todos os planos de serviço do App em um único grupo de recursos e região não pode exceder 500GB.  
<sup>6</sup>Esses recursos são limitados pelos recursos físicos nas instâncias dedicadas (o tamanho de instância e o número de instâncias).  
<sup>7</sup>Ao escalar um aplicativo na camada Basic para duas instâncias, você tem 350 conexões simultâneas para cada uma das duas instâncias. Para o nível Padrão e acima, não há limites teóricos para soquetes web, mas outros fatores podem limitar o número de soquetes web. Por exemplo, as solicitações simultâneas máximas permitidas (definidas por `maxConcurrentRequestsPerCpu`) são: 7.500 por VM pequeno, 15.000 por VM médio (7.500 x 2 núcleos) e 75.000 por VM grande (18.750 x 4 núcleos).  
<sup>8</sup> As conexões IP máximas são por instância e dependem do tamanho da ocorrência: 1.920 por exemplo B1/S1/P1V2, 3.968 por instância B2/S2/P2V2, 8.064 por instância B3/S3/P3V2.  
<sup>9</sup> O limite de cota do Certificado de Serviço de Aplicativo por assinatura pode ser aumentado através de uma solicitação de suporte para um limite máximo de 200.  
<sup>10</sup> Serviço de aplicativo SKUs isolados podem ser carregados internamente balanceados (ILB) com o Azure Load Balancer, de modo que não há conectividade pública a partir da internet. Como resultado, alguns recursos do Serviço de Aplicativo Isolado de ILB devem ser usados de computadores com acesso direto ao ponto de extremidade de rede de ILB.  
<sup>11</sup> Execute executáveis personalizados e/ou scripts demanda, em um cronograma ou continuamente como uma tarefa em segundo plano dentro da instância do Serviço de Aplicativo. Para a execução contínua de Trabalhos Web, a opção Sempre Ativado é obrigatória. Não há um limite predefinido sobre o número de WebJobs que podem ser executados em uma instância do App Service. Existem limites práticos que dependem do que o código de aplicação está tentando fazer.  
