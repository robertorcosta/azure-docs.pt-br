---
title: Monitorar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Como monitorar a Sincronização de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699280"
---
# <a name="monitor-azure-file-sync"></a>Monitorar a Sincronização de Arquivos do Azure

Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo descreve como monitorar sua implantação do Azure File Sync usando o Azure Monitor, Storage Sync Service e O Windows Server.

As seguintes opções de monitoramento estão disponíveis no momento.

## <a name="azure-monitor"></a>Azure Monitor

Use [o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para visualizar métricas e configurar alertas para sincronização, hierarquididade na nuvem e conectividade de servidores.  

### <a name="metrics"></a>Métricas

As métricas para a Sincronização de Arquivos do Azure são habilitadas por padrão e são enviadas para o Azure Monitor a cada 15 minutos.

Para exibir as métricas do Azure File Sync no Azure Monitor, selecione o tipo de recurso **Storage Sync Services.**

As métricas a seguir para a Sincronização de Arquivos do Azure estão disponíveis no Azure Monitor:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (upload e download).<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Recall da camada de nuvem | Tamanho dos dados em recall.<br><br>**Nota**: Esta métrica será removida no futuro. Use a métrica de tamanho de recall de hierarquiagem da nuvem para monitorar o tamanho dos dados recuperados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor |
| Tamanho de recall de hierarquiagem de nuvem | Tamanho dos dados em recall.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor, nome do grupo de sincronização |
| Tamanho de recall de hierarquiagem em nuvem por aplicativo | Tamanho dos dados lembrados por aplicativo.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do aplicativo, nome do servidor, nome do grupo de sincronização |
| Throughput de recall de hierarquidade na nuvem | Tamanho do throughput de recall de dados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor, nome do grupo de sincronização |
| Arquivos não sincronizando | Contagem de arquivos que estão falhando em sincronizar.<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download).<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Status online do servidor | Contagem de pulsações recebidas do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1 = sessão de sincronização bem-sucedida; 0 = sessão de sincronização com falha)<br><br>Unidade: Contagem<br>Tipos de agregação: Máximo<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |

### <a name="alerts"></a>Alertas

Para configurar alertas no Monitor Azure, selecione o Serviço de Sincronização de Armazenamento e selecione a [métrica Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) para usar para o alerta.  

A tabela a seguir lista alguns exemplos de cenários a serem monitorados e a métrica adequada a ser usada para o alerta:

| Cenário | Métrica para usar para alerta |
|-|-|
| Saúde do ponto final do servidor no portal = Erro | Resultado da sessão de sincronização |
| Os arquivos estão falhando em sincronizar com um servidor ou ponto final na nuvem | Arquivos não sincronizando |
| O servidor registrado está falhando em se comunicar com o Serviço de Sincronização de Armazenamento | Status online do servidor |
| O tamanho do recall de hierarquiagem em nuvem excedeu 500GiB em um dia  | Tamanho de recall de hierarquiagem de nuvem |

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [Visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para visualizar a saúde do servidor registrada, a saúde do ponto final do servidor e as métricas, acesse o Serviço de Sincronização de Armazenamento no portal Azure. Você pode visualizar a saúde do servidor registrado na lâmina **de servidores registrados** e na saúde do ponto final do servidor na lâmina grupos **Sync.**

### <a name="registered-server-health"></a>Saúde do servidor cadastrado

- Se o estado **do servidor registrado** estiver **on-line,** o servidor está se comunicando com sucesso com o serviço.
- Se o estado **do servidor registrado** estiver **ausente,** verifique se o processo De sincronia de armazenamento (AzureStorageSyncMonitor.exe) no servidor está sendo executado. Se o servidor estiver por trás de um firewall ou proxy, consulte [este artigo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) para configurar o firewall e o proxy.

### <a name="server-endpoint-health"></a>Saúde do ponto final do servidor

- A integridade do ponto de extremidade do servidor no portal baseia-se nos eventos de sincronização registrados no log de eventos de telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar por causa de um erro transitório, como erro cancelado, a sincronização ainda pode parecer saudável no portal enquanto a sessão de sincronização atual estiver progredindo. O ID de evento 9302 é usado para determinar se os arquivos estão sendo aplicados. Para obter mais informações, consulte o progresso [da sincronização saúde](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se o portal mostrar um erro de sincronização porque a sincronização não está progredindo, consulte a [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para orientação.

### <a name="metric-charts"></a>Gráficos métricos

- Os seguintes gráficos métricos podem ser visualizados no portal Storage Sync Service:

  | Nome da métrica | Descrição | Nome da lâmina |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Recall da camada de nuvem | Tamanho dos dados em recall | Servidores registrados |
  | Arquivos não sincronizando | Contagem de arquivos que estão falhando ao sincronizar | Ponto de extremidade do servidor |
  | Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Status online do servidor | Contagem de pulsações recebidas do servidor | Servidores registrados |

- Para saber mais, consulte [O Monitor do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Os gráficos no portal do Serviço de Sincronização de Armazenamento possuem um intervalo de tempo de 24 horas. Para exibir os diferentes intervalos de tempo ou dimensões, use o Azure Monitor.

## <a name="windows-server"></a>Windows Server

No Windows Server, você pode visualizar hierárquico na nuvem, servidor registrado e sincronizar a saúde.

### <a name="event-logs"></a>Logs de eventos

Use o log de eventos de telemetria no servidor para monitorar a integridade do servidor registrado, da sincronização e das camadas de nuvem. O registro de eventos de telemetria está localizado no Visualizador de Eventos em *Aplicativos e Serviços\Microsoft\FileSync\Agent*.

Saúde de sincronização:

- O ID do evento 9102 é registrado após o término de uma sessão de sincronização. Use este evento para determinar se as sessões de sincronização são bem sucedidas **(HResult = 0**) e se há erros de sincronização por item. Para obter mais informações, consulte a documentação de [sincronização de saúde](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e erros por [item.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Às vezes, as sessões de sincronização falham no geral ou têm um PerItemErrorCount não-zero. No entanto, eles ainda fazem progressos para a frente, e alguns arquivos sincronizam com sucesso. Você pode ver isso nos campos aplicados como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Esses campos dizem quanto da sessão teve sucesso. Se você ver várias sessões de sincronização falharem em uma linha e elas tiverem uma contagem aplicada crescente, dê tempo de sincronização para tentar novamente antes de abrir um ticket de suporte.

- A ID do evento 9302 é registrada a cada 5 a 10 minutos, se há uma sessão de sincronização ativa. Use este evento para determinar se a sessão de sincronização atual está progredindo **(AppliedItemCount > 0**). Se a sincronização não estiver progredindo, a sessão de sincronização eventualmente falhará, e um ID de evento 9102 será registrado com o erro. Para obter mais informações, consulte a [documentação de progresso de sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Saúde do servidor registrado:

- A ID do evento 9301 é registrada a cada 30 segundos quando um servidor consulta o serviço em busca de trabalhos. Se getNextJob terminar com **status = 0,** o servidor poderá se comunicar com o serviço. Se getNextJob terminar com um erro, verifique a [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientação.

Saúde de hierarcada sumida em nuvem:

- Para monitorar a atividade de hierarquidar em um servidor, use o Event ID 9003, 9016 e 9029 no registro de eventos de telemetria, que está localizado no Event Viewer em *Aplicativos e Serviços\Microsoft\FileSync\Agent*.

  - A identificação de evento 9003 fornece distribuição de erro para um terminal do servidor. Por exemplo: Contagem total de erros e código de erro. Um evento é registrado por código de erro.
  - A identificação de evento 9016 fornece resultados de fantasma para um volume. Por exemplo: Porcentagem de espaço livre é, número de arquivos fantasmas em sessão e número de arquivos não conseguiu fantasma.
  - A ID do evento 9029 fornece informações de sessão de conversão em fantasma para um ponto de extremidade de servidor. Por exemplo: Número de arquivos tentados na sessão, número de arquivos hierárquicos na sessão e número de arquivos já hierárquicos.
  
- Para monitorar a atividade de recall em um servidor, use o Event ID 9005, 9006, 9009 e 9059 no registro de eventos de telemetria, que está localizado no Event Viewer em *Aplicativos e Serviços\Microsoft\FileSync\Agent*.

  - A ID de evento 9005 fornece confiabilidade de recall para um ponto de extremidade do servidor. Por exemplo: Total de arquivos exclusivos acessados e total de arquivos exclusivos com acesso com falha.
  - ID do evento 9006 fornece Lembre-se a distribuição de erro para um ponto de extremidade do servidor. Por exemplo: Total de solicitações com falha e Código de erro. Um evento é registrado por código de erro.
  - A ID do evento 9009 fornece informações de sessão de recall para um ponto de extremidade de servidor. Por exemplo: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - A ID do evento 9059 fornece distribuição de recall do aplicativo para um ponto de extremidade de servidor. Por exemplo: ShareId, Nome do aplicativo e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contadores de desempenho

Use os contadores de desempenho da Sincronização de Arquivos do Azure no servidor para monitorar a atividade de sincronização.

Para exibir os contadores de desempenho do Azure File Sync no servidor, abra o Monitor de Desempenho (Perfmon.exe). Você pode encontrar os contadores os **objetos AFS Bytes Transferred** e **AFS Sync Operations.**

Os seguintes contadores de desempenho para a Sincronização de Arquivos do Azure estão disponíveis no Monitor de Desempenho:

| Objeto de Desempenho\Nome do Contador | Descrição |
|-|-|
| Bytes de AFS Transferidos\Bytes Baixados/s | Número de bytes baixados por segundo. |
| Bytes de AFS Transferidos\Bytes Carregados/s | Número de bytes carregados por segundo. |
| Bytes de AFS Transferidos\Total de Bytes/s | Total de bytes por segundo (upload e download). |
| Operações de Sincronização de AFS\Arquivos de Sincronização Baixados/s | Número de arquivos baixados por segundo. |
| Operações de Sincronização de AFS\Arquivos de Sincronização Carregados/s | Número de arquivos carregados por segundo. |
| Operações de Sincronização de AFS\Total de Operações de Arquivo de Sincronização/s | Número total de arquivos sincronizados (upload e download). |

## <a name="next-steps"></a>Próximas etapas
- [Planejamento para uma implantação do Azure File Sync](storage-sync-files-planning.md)
- [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
