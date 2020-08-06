---
title: Monitorar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Como monitorar a Sincronização de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 81224e0c055ad4a94bd57ebb3aa7c8a3b30c2dd7
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832613"
---
# <a name="monitor-azure-file-sync"></a>Monitorar a Sincronização de Arquivos do Azure

Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo descreve como monitorar sua implantação do Sincronização de Arquivos do Azure usando Azure Monitor, o serviço de sincronização de armazenamento e o Windows Server.

Os cenários a seguir são abordados neste guia: 
- Exiba Sincronização de Arquivos do Azure métricas no Azure Monitor.
- Crie alertas no Azure Monitor para notificá-lo proativamente sobre condições críticas.
- Monitore a integridade de sua implantação do Sincronização de Arquivos do Azure usando o portal do Azure.
- Como usar os logs de eventos e os contadores de desempenho em seus servidores Windows para monitorar a integridade de sua implantação de Sincronização de Arquivos do Azure. 

## <a name="azure-monitor"></a>Azure Monitor

Use [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para exibir as métricas e configurar alertas para sincronização, camadas de nuvem e conectividade de servidor.  

### <a name="metrics"></a>Métricas

As métricas para a Sincronização de Arquivos do Azure são habilitadas por padrão e são enviadas para o Azure Monitor a cada 15 minutos.

Para exibir Sincronização de Arquivos do Azure métricas em Azure Monitor, selecione o tipo de recurso **serviços de sincronização de armazenamento** .

As métricas a seguir para a Sincronização de Arquivos do Azure estão disponíveis no Azure Monitor:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (upload e download).<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |
| Recall da camada de nuvem | Tamanho dos dados em recall.<br><br>**Observação**: essa métrica será removida no futuro. Use a métrica de tamanho de recuperação de camadas de nuvem para monitorar o tamanho dos dados recuperados.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: nome do servidor |
| Tamanho de recall da camada de nuvem | Tamanho dos dados em recall.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: nome do servidor, nome do grupo de sincronização |
| Tamanho de recall da camada de nuvem por aplicativo | Tamanho dos dados recuperados pelo aplicativo.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: nome do aplicativo, nome do servidor, nome do grupo de sincronização |
| Taxa de transferência de recall da camada de nuvem | Tamanho da taxa de transferência de recall de dados.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: nome do servidor, nome do grupo de sincronização |
| Arquivos não sincronizando | Contagem de arquivos que estão falhando em sincronizar.<br><br>Unidade: Contagem<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |
| Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download).<br><br>Unidade: Contagem<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |
| Status online do servidor | Contagem de pulsações recebidas do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: nome do servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1 = sessão de sincronização bem-sucedida; 0 = sessão de sincronização com falha)<br><br>Unidade: Contagem<br>Tipos de agregação: máximo<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |

### <a name="alerts"></a>Alertas

Os alertas trabalham de forma proativa, mandando notificações quando encontram condições importante em seus dados de monitoramento. Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral de alertas no Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

**Como criar alertas para Sincronização de Arquivos do Azure**

- Vá para o **serviço de sincronização de armazenamento** no **portal do Azure**. 
- Clique em **alertas** na seção monitoramento e, em seguida, clique em **+ nova regra de alerta**.
- Clique em **Selecionar condição** e forneça as seguintes informações para o alerta: 
    - **Métrica**
    - **Nome da dimensão**
    - **Lógica de alerta**
- Clique em **selecionar grupo de ações** e adicione um grupo de ações (email, SMS, etc.) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.
- Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
- Clique em **criar regra de alerta** para criar o alerta.  

A tabela a seguir lista alguns cenários de exemplo para monitorar e a métrica apropriada a ser usada para o alerta:

| Cenário | Métrica a ser usada para alerta |
|-|-|
| Integridade do ponto de extremidade do servidor no portal = erro | Resultado da sessão de sincronização |
| Os arquivos estão falhando ao sincronizar com um ponto de extremidade de servidor ou de nuvem | Arquivos não sincronizando |
| O servidor registrado não está conseguindo se comunicar com o serviço de sincronização de armazenamento | Status online do servidor |
| O tamanho de recall em camadas de nuvem excedeu 500GiB em um dia  | Tamanho de recall da camada de nuvem |

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para exibir a integridade do servidor registrado, a integridade do ponto de extremidade do servidor e as métricas, vá para o serviço de sincronização de armazenamento no portal do Azure. Você pode exibir a integridade do servidor registrado na folha **servidores registrados** e a integridade do ponto de extremidade do servidor na folha **grupos de sincronização** .

### <a name="registered-server-health"></a>Integridade do servidor registrado

- Se o estado do **servidor registrado** estiver **online**, o servidor estará se comunicando com êxito com o serviço.
- Se o estado do **servidor registrado** for **exibido offline**, verifique se o processo do monitor de sincronização de armazenamento (AzureStorageSyncMonitor.exe) no servidor está em execução. Se o servidor estiver protegido por um firewall ou proxy, consulte [Este artigo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) para configurar o firewall e o proxy.

### <a name="server-endpoint-health"></a>Integridade do ponto de extremidade do servidor

- A integridade do ponto de extremidade do servidor no portal baseia-se nos eventos de sincronização registrados no log de eventos de telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como erro cancelado, a sincronização ainda poderá aparecer íntegra no portal, desde que a sessão de sincronização atual esteja progredindo. A ID de evento 9302 é usada para determinar se os arquivos estão sendo aplicados. Para obter mais informações, consulte [sincronizar integridade](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronizar andamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se o portal mostrar um erro de sincronização porque a sincronização não está progredindo, consulte a [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter diretrizes.

### <a name="metric-charts"></a>Gráficos de métricas

- Os gráficos de métrica a seguir são visíveis no portal do serviço de sincronização de armazenamento:

  | Nome da métrica | Descrição | Nome da folha |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Recall da camada de nuvem | Tamanho dos dados em recall | Servidores registrados |
  | Arquivos não sincronizando | Contagem de arquivos que estão falhando ao sincronizar | Ponto de extremidade do servidor |
  | Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Status online do servidor | Contagem de pulsações recebidas do servidor | Servidores registrados |

- Para saber mais, consulte [Azure monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Os gráficos no portal do Serviço de Sincronização de Armazenamento possuem um intervalo de tempo de 24 horas. Para exibir os diferentes intervalos de tempo ou dimensões, use o Azure Monitor.

## <a name="windows-server"></a>Windows Server

No Windows Server, você pode exibir a camada de nuvem, o servidor registrado e a integridade da sincronização.

### <a name="event-logs"></a>Logs de eventos

Use o log de eventos de telemetria no servidor para monitorar a integridade do servidor registrado, da sincronização e das camadas de nuvem. O log de eventos de telemetria está localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

Integridade da sincronização:

- A ID de evento 9102 é registrada após a conclusão de uma sessão de sincronização. Use esse evento para determinar se as sessões de sincronização são bem-sucedidas (**HRESULT = 0**) e se há erros de sincronização por item. Para obter mais informações, consulte a documentação [sincronizar integridade](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [erros por item](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > Às vezes, as sessões de sincronização falham em geral ou têm um PerItemErrorCount diferente de zero. No entanto, eles ainda encaminham o progresso e alguns arquivos são sincronizados com êxito. Você pode ver isso nos campos aplicados, como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Esses campos informam quanto da sessão foi bem-sucedida. Se você vir a falha de várias sessões de sincronização em uma linha e elas tiverem uma contagem aplicada crescente, forneça o tempo de sincronização para tentar novamente antes de abrir um tíquete de suporte.

- A ID do evento 9302 é registrada a cada 5 a 10 minutos, se há uma sessão de sincronização ativa. Use esse evento para determinar se a sessão de sincronização atual está fazendo o andamento (**AppliedItemCount > 0**). Se a sincronização não estiver progredindo, a sessão de sincronização deverá eventualmente falhar e uma ID de evento 9102 será registrada com o erro. Para obter mais informações, consulte a [documentação progresso da sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integridade do servidor registrado:

- A ID do evento 9301 é registrada a cada 30 segundos quando um servidor consulta o serviço em busca de trabalhos. Se GetNextJob for concluído com **status = 0**, o servidor será capaz de se comunicar com o serviço. Se GetNextJob for concluído com um erro, consulte a [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter diretrizes.

Integridade de camadas de nuvem:

- Para monitorar a atividade de camadas em um servidor, use a ID de evento 9003, 9016 e 9029 no log de eventos de telemetria, que está localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

  - A identificação de evento 9003 fornece distribuição de erro para um terminal do servidor. Por exemplo: contagem de erros total e ErrorCode. Um evento é registrado por código de erro.
  - A identificação de evento 9016 fornece resultados de fantasma para um volume. Por exemplo: a porcentagem de espaço livre é, o número de arquivos fantasmas na sessão e o número de arquivos com falha no fantasma.
  - A ID do evento 9029 fornece informações de sessão de conversão em fantasma para um ponto de extremidade de servidor. Por exemplo: o número de arquivos tentados na sessão, o número de arquivos em camadas na sessão e o número de arquivos já em camadas.
  
- Para monitorar a atividade de recuperação em um servidor, use a ID de evento 9005, 9006, 9009 e 9059 no log de eventos de telemetria, localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

  - A ID de evento 9005 fornece confiabilidade de recall para um ponto de extremidade do servidor. Por exemplo: total de arquivos exclusivos acessados e total de arquivos exclusivos com falha de acesso.
  - ID do evento 9006 fornece Lembre-se a distribuição de erro para um ponto de extremidade do servidor. Por exemplo: total de solicitações com falha e ErrorCode. Um evento é registrado por código de erro.
  - A ID do evento 9009 fornece informações de sessão de recall para um ponto de extremidade de servidor. Por exemplo: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - A ID do evento 9059 fornece distribuição de recall do aplicativo para um ponto de extremidade de servidor. Por exemplo: Shareid, nome do aplicativo e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contadores de desempenho

Use os contadores de desempenho da Sincronização de Arquivos do Azure no servidor para monitorar a atividade de sincronização.

Para exibir Sincronização de Arquivos do Azure contadores de desempenho no servidor, abra o monitor de desempenho (Perfmon.exe). Você pode encontrar os contadores nos objetos de **operações de sincronização** de **AFS bytes transferidos** e AFS.

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
- [Planejar uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
