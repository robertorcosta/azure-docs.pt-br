---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013699"
---
## <a name="business-disaster-recovery"></a>Recuperação de desastre de negócios

Esta seção descreve características do Azure Time Series Insights que mantêm aplicativos e serviços em execução, mesmo que ocorra um desastre (conhecido como *recuperação de desastres comerciais*).

### <a name="high-availability"></a>Alta disponibilidade

Como um serviço do Azure, o Time Series Insights fornece certos recursos *de alta disponibilidade* usando redundâncias no nível da região do Azure. Por exemplo, o Azure suporta recursos de recuperação de desastres através do recurso *de disponibilidade entre regiões* do Azure.

Outros recursos adicionais de alta disponibilidade fornecidos através do Azure (e também disponíveis para qualquer instância do Time Series Insights) incluem:

- **Failover**: O Azure fornece [geo-replicação e balanceamento de carga](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Restauração de dados** e **recuperação de armazenamento**: O Azure oferece [várias opções para preservar e recuperar dados.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)
- **Azure Site Recovery**: O Azure fornece recursos de recuperação do site através [da Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Backup do Azure** [: O Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) suporta backup no local e em nuvem de VMs do Azure.

Certifique-se de ativar os recursos relevantes do Azure para fornecer alta disponibilidade global entre regiões para seus dispositivos e usuários.

> [!NOTE]
> Se o Azure estiver configurado para permitir a disponibilidade entre regiões, nenhuma configuração adicional de disponibilidade entre regiões será necessária no Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT e hubs de eventos

Alguns serviços de IoT do Azure também incluem recursos de recuperação de desastres comerciais incorporados:

- [Azure IoT Hub recuperação de desastres de alta disponibilidade,](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)que inclui redundância intra-região
- [Políticas do Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundância de armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

A integração do Time Series Insights com os outros serviços oferece oportunidades adicionais de recuperação de desastres. Por exemplo, a telemetria enviada ao seu hub de eventos pode ser persistida em um banco de armazenamento azure Blob de backup.

### <a name="time-series-insights"></a>Time Series Insights

Existem várias maneiras de manter seus dados, aplicativos e serviços da série do tempo funcionando, mesmo que eles sejam interrompidos. 

No entanto, você pode determinar que uma cópia completa de backup do ambiente Azure Time Series também é necessária, para os seguintes propósitos:

- Como uma *instância de failover* especificamente para o Time Series Insights para redirecionar dados e tráfego para
- Para preservar dados e informações de auditoria

Em geral, a melhor maneira de duplicar um ambiente de Insights de séries tempoé criar um segundo ambiente time series insights em uma região azure de backup. Os eventos também são enviados para este ambiente secundário a partir de sua fonte primária de eventos. Certifique-se de usar um segundo grupo de consumidores dedicado. Siga as diretrizes de recuperação de desastres de negócios dessa fonte, como descrito anteriormente.

Para criar um ambiente duplicado:

1. Crie um ambiente na segunda região. Para obter mais informações, leia [Criar um novo ambiente de Insights de Séries Tempo no portal Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Crie um segundo grupo de consumidores dedicado para a origem do evento.
1. Conecte essa origem do evento ao novo ambiente. Certifique-se de designar o segundo grupo de consumidores dedicado.
1. Revise a documentação do Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) e [event Hubs.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)

Se ocorrer um evento:

1. Se a região primária for afetada durante um incidente de desastres, redirecione as operações para o backup do ambiente do Time Series Insights.
1. Use sua segunda região para fazer backup e recuperar todos os dados de telemetria e consulta de séries de tempo.

> [!IMPORTANT]
> Se ocorrer um failover:
> 
> * Um atraso também pode ocorrer.
> * Um pico momentâneo no processamento de mensagens pode ocorrer, à medida que as operações são redirecionadas.
> 
> Para obter mais informações, leia [Mitigar a latência em Insights de Séries Tempo](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

