---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: b84b503fad951b5a6998bf0b06d466ce41031aa8
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72526723"
---
| Grupos | Gratuito | Compartilhado | Básico | Standard | Premium (v2) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicativos Web, móveis ou de API](https://azure.microsoft.com/services/app-service/) por [Azure app plano de serviço](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) |10 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância de computação |Compartilhado |Compartilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Scale out](../articles/app-service/manage-scale-up.md) (máximo de instâncias) |1 compartilhado |1 compartilhado |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |20 dedicados<sup>3</sup>|100 dedicado<sup>4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo de CPU (5 minutos)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão|
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |
| Memória (1 hora) |1\.024 MB por plano do serviço de aplicativo |1\.024 MB por aplicativo |N/D |N/D |N/D |N/D |
| Largura de banda |165 MB |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Arquitetura do aplicativo |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Web Sockets por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| Conexões IP | 600 | 600 | Depende do tamanho da instância<sup>8</sup> | Depende do tamanho da instância<sup>8</sup> | Depende do tamanho da instância<sup>8</sup> | 64.000 |
| [Conexões do depurador](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultâneas por aplicativo |1 |1 |1 |5 |5 |5 |
| Certificados do serviço de aplicativo por assinatura<sup>9</sup>| Sem suporte | Sem suporte |10 |10 |10 |10 |
| Domínios personalizados por aplicativo</a> |0 (somente subdomínio azurewebsites.net)|500 |500 |500 |500 |500 |
| domínio personalizado [Suporte a SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Sem suporte, certificado curinga para *. azurewebsites.net disponível por padrão|Sem suporte, certificado curinga para *. azurewebsites.net disponível por padrão|Conexões SSL SNI ilimitadas |Conexões SSL SNI e 1 IP SSL inclusas incluídas |Conexões SSL SNI e 1 IP SSL inclusas incluídas | Conexões SSL SNI e 1 IP SSL inclusas incluídas|
| Conexões híbridas por plano | | | 5 | 25 | 200 | 200 |
| Balanceador de carga integrado | |w.x.y. |w.x.y. |w.x.y. |w.x.y. |X<sup>10</sup> |
| [Sempre ativo](../articles/app-service/configure-common.md) | | |w.x.y. |w.x.y. |w.x.y. |w.x.y. |
| [Backups agendados](../articles/app-service/manage-backup.md) | | | | Backups agendados a cada 2 horas, no máximo 12 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) |
| [Autoescala](../articles/app-service/manage-scale-up.md) | | | |w.x.y. |w.x.y. |w.x.y. |
| [Trabalhos](../articles/app-service/webjobs-create.md)Web<sup>11</sup> |w.x.y. |w.x.y. |w.x.y. |w.x.y. |w.x.y. |w.x.y. |
| [Agendador do Azure](https://azure.microsoft.com/services/scheduler/) | |w.x.y. |w.x.y. |w.x.y. |w.x.y. |w.x.y. |
| [Monitoramento do ponto de extremidade](../articles/app-service/web-sites-monitor.md) | | |w.x.y. |w.x.y. |w.x.y. |w.x.y. |
| [Slots de preparo](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1</sup>Aplicativos e cotas de armazenamento são oferecidos por plano de Serviço de Aplicativo, a menos que haja indicação contrária.  
<sup>2</sup>O número real de aplicativos que podem ser hospedados nesses computadores depende da atividade dos aplicativos, do tamanho das instâncias do computador e da utilização do recurso correspondente.  
<sup>3</sup>As instâncias dedicadas podem ter tamanhos diferentes. Para obter mais informações, consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Mais permissões são permitidas mediante solicitação.  
<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo em todos os aplicativos no mesmo plano do serviço de aplicativo. O tamanho total do conteúdo de todos os aplicativos em todos os planos do serviço de aplicativo em um único grupo de recursos e região não pode exceder 500 GB.  
<sup>6</sup>Esses recursos são limitados pelos recursos físicos nas instâncias dedicadas (o tamanho de instância e o número de instâncias).  
<sup>7</sup>Ao escalar um aplicativo na camada Basic para duas instâncias, você tem 350 conexões simultâneas para cada uma das duas instâncias. Para a camada Standard e superior, não há limites teóricos para os soquetes da Web, mas outros fatores podem limitar o número de soquetes da Web. Por exemplo, as solicitações simultâneas máximas permitidas (definidas por `maxConcurrentRequestsPerCpu`) são: 7.500 por VM pequena, 15.000 por VM média (7.500 x 2 núcleos) e 75.000 por VM grande (18.750 x 4 núcleos).  
<sup>8</sup> As conexões IP máximas são por instância e dependem do tamanho da instância: 1.920 por instância B1/S1/P1V2, 3.968 por instância B2/S2/P2V2, 8.064 por instância B3/S3/P3V2.  
<sup>9</sup> O limite de cota de Certificado do Serviço de Aplicativo por assinatura pode ser aumentado por meio de uma solicitação de suporte para um limite máximo de 200.  
<sup>10</sup> Serviço do Aplicativo Isolado SKUs podem ter balanceamento de carga internamente (ILB) com Azure Load Balancer, portanto, não há conectividade pública da Internet. Como resultado, alguns recursos do Serviço de Aplicativo Isolado de ILB devem ser usados de computadores com acesso direto ao ponto de extremidade de rede de ILB.  
<sup>11</sup> Execute executáveis personalizados e/ou scripts sob demanda, em um agendamento ou continuamente como uma tarefa em segundo plano em sua instância do serviço de aplicativo. Sempre Ativo é exigido para a execução contínua de Trabalhos Web. O Agendador do Azure Gratuito ou Padrão é necessário para os Trabalhos Web agendados. Não há nenhum limite predefinido para o número de trabalhos Web que podem ser executados em uma instância do serviço de aplicativo. Há limites práticos que dependem do que o código do aplicativo está tentando fazer.  