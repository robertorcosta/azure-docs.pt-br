---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67172320"
---
| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais de conta de armazenamento |64 | |
| Número máximo de contêineres de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de agendas por modelo de largura de banda |168 |Um horário para cada hora, todos os dias da semana. |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para StorSimple 8100 e StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure |30 TB para StorSimple 8010 <br></br> 64 TB para StorSimple 8020 |StorSimple 8010 e StorSimple 8020 são dispositivos virtuais no Azure que usam armazenamento Standard e armazenamento Premium, respectivamente. |
| Tamanho máximo de um volume localmente afixado em dispositivos físicos |9 TB para StorSimple 8100 <br></br> 24 TB para StorSimple 8600 |StorSimple 8100 e StorSimple 8600 são dispositivos físicos. |
| Número máximo de conexões iSCSI |512 | |
| Número máximo de conexões iSCSI dos iniciadores |512 | |
| Número máximo de registros de controle de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |24 | |
| Número máximo de backups retidos por política de backup |64 | |
| Número máximo de agendas por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume |256 |Essa quantidade inclui instantâneos locais e instantâneos na nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10.000 | |
| Número máximo de volumes que podem ser processados paralelamente para backup, restauração e clonagem |16 |<ul><li>Se houver mais de 16 volumes, eles são processados sequencialmente à medida que os slots de processamento se tornam disponíveis.</li><li>Novos backups de um volume clonado ou hierárquico restaurado não podem ocorrer até que a operação seja concluída. Para um volume local, os backups são permitidos após o volume estiver on-line.</li></ul> |
| Tempo de recuperação de clonagem e restauração para volumes em camadas |<2 minutos |<ul><li>O volume é disponibilizado dentro de 2 minutos após uma operação de restauração ou clone, independentemente do tamanho do volume.</li><li>O desempenho do volume pode inicialmente ser mais lento do que o normal, já que a maioria dos dados e metadados ainda reside na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauração ou clonagem será concluída quando todos os metadados estiverem no dispositivo.</li><li>As operações de backup não podem ser realizadas até que a operação de restauração ou clone esteja totalmente concluída. |
| Tempo de recuperação de restauração para volumes localmente fixados |<2 minutos |<ul><li>O volume é disponibilizado em até 2 minutos durante a operação de restauração, independentemente do tamanho do volume.</li><li>O desempenho do volume pode inicialmente ser mais lento do que o normal, já que a maioria dos dados e metadados ainda reside na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano a uma taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>Ao contrário dos volumes hierárquicos, se houver volumes fixados localmente, os dados de volume também são baixados localmente no dispositivo. A operação de restauração é concluída quando todos os dados de volume são colocados no dispositivo.</li><li>As operações de restauração podem ser longas e o tempo total para concluir a restauração dependerá do tamanho do volume local provisionado, da largura de banda da Internet e dos dados existentes no dispositivo. Operações de backup em volume localmente afixado são permitidas enquanto a operação de restauração está em andamento. |
| Disponibilidade de restauração fina |Último failover | |
| Máximo de throughput de leitura/gravação do cliente, quando servido a partir do nível SSD* |920/720 MB/seg com uma única interface de rede Ethernet de 10 gigabits |Até duas vezes com MPIO e duas interfaces de rede. |
| Máximo de throughput de leitura/gravação do cliente, quando servido a partir do nível HDD* |120/250 MB/seg | |
| Máximo de throughput de leitura/gravação do cliente, quando servido a partir do nível de nuvem* |11/41 MB/seg |A taxa de transferência de leitura depende dos clientes que geram e mantêm profundidade suficiente de fila de E/S. |

&#42;O throughput máximo por tipo de I/O foi medido com 100% de leitura e 100% de cenários de gravação. O throughput real pode ser menor e depende da mistura de I/O e das condições da rede.

