---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 707f18ade87e755243a9122ecb7f898a753b2a8e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224483"
---
| Grupos | Gratuito | Compartilhado | Basic | Standard | Premium (v2) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web, mobile, or API apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) |10 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância de computação |Compartilhado |Compartilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Scale out](../articles/app-service/manage-scale-up.md) (maximum instances) |1 compartilhada |1 compartilhada |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |20 dedicados<sup>3</sup>|100 dedicados<sup>4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU time (5 minutes)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão|
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |
| Memória (1 hora) |1,024 MB per App Service plan |1,024 MB per app |N/D |N/D |N/D |N/D |
| Largura de banda |165 MB |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Arquitetura do aplicativo |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Web sockets per instance<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| IP connections | 600 | 600 | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | 64.000 |
| [Conexões do depurador](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultâneas por aplicativo |1 |1 |1 |5 |5 |5 |
| App Service Certificates per subscription<sup>9</sup>| Sem suporte | Sem suporte |10 |10 |10 |10 |
| Domínios personalizados por aplicativo</a> |0 (somente subdomínio do azurewebsites.net)|500 |500 |500 |500 |500 |
| domínio personalizado [Suporte a SSL](../articles/app-service/configure-ssl-certificate.md) |Not supported, wildcard certificate for *.azurewebsites.net available by default|Not supported, wildcard certificate for *.azurewebsites.net available by default|Conexões SSL de SNI ilimitadas |Conexões SSL de SNI ilimitadas e IP SSL incluídas |Conexões SSL de SNI ilimitadas e IP SSL incluídas | Conexões SSL de SNI ilimitadas e IP SSL incluídas|
| Hybrid connections per plan | | | 5 | 25 | 200 | 200 |
| Integrated load balancer | |X |X |X |X |X<sup>10</sup> |
| [Sempre ativo](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Scheduled backups](../articles/app-service/manage-backup.md) | | | | Scheduled backups every 2 hours, a maximum of 12 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) |
| [Autoescala](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Agendador do Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitoramento do ponto de extremidade](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Staging slots](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1</sup>Aplicativos e cotas de armazenamento são oferecidos por plano de Serviço de Aplicativo, a menos que haja indicação contrária.  
<sup>2</sup>O número real de aplicativos que podem ser hospedados nesses computadores depende da atividade dos aplicativos, do tamanho das instâncias do computador e da utilização do recurso correspondente.  
<sup>3</sup>As instâncias dedicadas podem ter tamanhos diferentes. Para saber mais, veja [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>More are allowed upon request.  
<sup>5</sup>The storage limit is the total content size across all apps in the same App service plan. The total content size of all apps across all App service plans in a single resource group and region cannot exceed 500GB.  
<sup>6</sup>Esses recursos são limitados pelos recursos físicos nas instâncias dedicadas (o tamanho de instância e o número de instâncias).  
<sup>7</sup>Ao escalar um aplicativo na camada Basic para duas instâncias, você tem 350 conexões simultâneas para cada uma das duas instâncias. For Standard tier and above, there are no theoretical limits to web sockets, but other factors can limit the number of web sockets. For example, maximum concurrent requests allowed (defined by `maxConcurrentRequestsPerCpu`) are: 7,500 per small VM, 15,000 per medium VM (7,500 x 2 cores), and 75,000 per large VM (18,750 x 4 cores).  
<sup>8</sup>The maximum IP connections are per instance and depend on the instance size: 1,920 per B1/S1/P1V2 instance, 3,968 per B2/S2/P2V2 instance, 8,064 per B3/S3/P3V2 instance.  
<sup>9</sup>The App Service Certificate quota limit per subscription can be increased via a support request to a maximum limit of 200.  
<sup>10</sup>App Service Isolated SKUs can be internally load balanced (ILB) with Azure Load Balancer, so there's no public connectivity from the internet. Como resultado, alguns recursos do Serviço de Aplicativo Isolado de ILB devem ser usados de computadores com acesso direto ao ponto de extremidade de rede de ILB.  
<sup>11</sup>Run custom executables and/or scripts on demand, on a schedule, or continuously as a background task within your App Service instance. Sempre Ativo é exigido para a execução contínua de Trabalhos Web. O Agendador do Azure Gratuito ou Padrão é necessário para os Trabalhos Web agendados. There's no predefined limit on the number of WebJobs that can run in an App Service instance. There are practical limits that depend on what the application code is trying to do.  