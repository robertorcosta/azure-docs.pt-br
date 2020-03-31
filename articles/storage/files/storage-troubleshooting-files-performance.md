---
title: Guia de solução de problemas de desempenho de arquivos do Azure
description: Problemas de desempenho conhecidos com compartilhamentos de arquivos do Azure e soluçãos alternativas associadas.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598078"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Solucionar problemas de desempenho do Azure Files

Este artigo lista alguns problemas comuns relacionados com as partes de arquivos do Azure. Ele fornece possíveis causas e soluções quando esses problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Alta latência, baixo rendimento e problemas gerais de desempenho

### <a name="cause-1-share-experiencing-throttling"></a>Causa 1: Compartilhar experimentando estrangulamento

A cota padrão em uma ação premium é de 100 GiB, que fornece 100 IOPS de linha de base (com potencial para estourar até 300 por uma hora). Para obter mais informações sobre o provisionamento e sua relação com o IOPS, consulte a seção [de ações provisionadas](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) do guia de planejamento.

Para confirmar se sua parte está sendo estrangulada, você pode aproveitar o Azure Metrics no portal.

1. Faça login no [portal Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** e, em seguida, procure **métricas**.

1. Selecione **Métricas**.

1. Selecione sua conta de armazenamento como o recurso.

1. Selecione **Arquivo** como o espaço de nome métrico.

1. Selecione **Transações** como métrica.

1. Adicione um filtro para **ResponseType** e verifique se alguma solicitação tem um código de resposta de **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REST).

![Opções de métricas para compartilhamentos de arquivos premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Para receber um alerta se um compartilhamento de arquivos for estrangulado, consulte [Como criar um alerta se um compartilhamento de arquivos for estrangulado](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Solução

- Aumente a capacidade provisionada de ações especificando uma cota maior em sua participação.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: Carga de trabalho pesada metadados/namespace

Se a maioria das suas solicitações for centrada em metadados (como criar arquivo/abrir/fechar arquivo/queryinfo/querydirectory) a latência será pior quando comparada às operações de leitura/gravação.

Para confirmar se a maioria de suas solicitações são centradas em metadados, você pode usar os mesmos passos acima. Exceto em vez de adicionar um filtro para **ResponseType,** adicione um filtro para **Nome de API**.

![Filtro para nome de API em suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução alternativa

- Verifique se o aplicativo pode ser modificado para reduzir o número de operações de metadados.
- Adicione um VHD no compartilhamento de arquivos e monte VHD sobre SMB do cliente para executar operações de arquivos contra os dados. Essa abordagem funciona para cenários de um único escritor e vários leitores e permite que as operações de metadados sejam locais, oferecendo desempenho semelhante a um armazenamento conectado diretamente local.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicativo de rosca única

Se o aplicativo que está sendo usado pelo cliente for de um thread único, isso pode resultar em IOPS/throughput significativamente menor do que o máximo possível com base no tamanho do seu compartilhamento provisionado.

### <a name="solution"></a>Solução

- Aumente o paralelismo da aplicação aumentando o número de threads.
- Mude para aplicativos onde o paralelismo é possível. Por exemplo, para operações de cópia, os clientes podem usar o AzCopy ou o RoboCopy de clientes do Windows ou o comando **paralelo** em clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para pedidos

### <a name="cause"></a>Causa

A VM do cliente poderia estar localizada em uma região diferente do compartilhamento de arquivos.

### <a name="solution"></a>Solução

- Execute o aplicativo de uma VM localizada na mesma região que o compartilhamento de arquivos.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Cliente incapaz de alcançar o máximo de throughput suportado pela rede

Uma das causas potenciais disso é a falta de suporte multicanal fo SMB. Atualmente, os compartilhamentos de arquivos do Azure suportam apenas um único canal, então há apenas uma conexão da VM do cliente com o servidor. Esta única conexão é atrelada a um único núcleo na VM do cliente, de modo que o throughput máximo alcançável de uma VM é vinculado por um único núcleo.

### <a name="workaround"></a>Solução alternativa

- A obtenção de uma VM com um núcleo maior pode ajudar a melhorar o throughput.
- A execução do aplicativo cliente de várias VMs aumentará o throughput.

- Use APIs REST sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>O throughput em clientes Linux é significativamente menor quando comparado com clientes Windows.

### <a name="cause"></a>Causa

Este é um problema conhecido com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução alternativa

- Espalhe a carga em várias VMs.
- Na mesma VM, use vários pontos de montagem com opção **nosharesock** e espalhe a carga por esses pontos de montagem.
- No Linux, tente montar com a opção **nostrictsync** para evitar forçar o flush do SMB em cada chamada **fsync.** Para arquivos Azure, essa opção não interfere na consistência dos dados, mas pode resultar em metadados de arquivo obsoletos na listagem do diretório (comando**ls -l).** Consultando diretamente metadados de arquivo (comando**stat)** retornará os metadados de arquivo mais atualizados.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Altas latências para cargas de trabalho pesadas de metadados envolvendo operações extensas de abertura/fechamento.

### <a name="cause"></a>Causa

Falta de apoio para arrendamentos de diretórios.

### <a name="workaround"></a>Solução alternativa

- Se possível, evite a alça de abertura/fechamento excessiva no mesmo diretório em um curto período de tempo.
- Para VMs Linux, aumente o tempo limite de cache de entrada do diretório especificando **actimeo=\<seg>** como uma opção de montagem. Por padrão, é um segundo, então um valor maior como três ou cinco pode ajudar.
- Para VMs Linux, atualize o kernel para 4.20 ou mais.

## <a name="low-iops-on-centosrhel"></a>IOPS baixo no CentOS/RHEL

### <a name="cause"></a>Causa

A profundidade de IO maior que uma não é suportada no CentOS/RHEL.

### <a name="workaround"></a>Solução alternativa

- Upgrade para CentOS 8 / RHEL 8.
- Mude para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Linux

Se você estiver experimentando cópia lenta de arquivos de e para arquivos Do Azure, dê uma olhada na [cópia de arquivo lento para e de Arquivos Azure na](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) seção Linux no guia de solução de problemas do Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Padrão nervoso/dente de serra para IOPS

### <a name="cause"></a>Causa

O aplicativo cliente excede consistentemente o IOPS de linha de base. Atualmente, não há suavização do lado de serviço da carga de solicitação, portanto, se o cliente exceder o IOPS de linha de base, ele será estrangulado pelo serviço. Esse estrangulamento pode resultar em um padrão IOPS nervoso/dente-de-serra. Neste caso, o IOPS médio alcançado pelo cliente pode ser menor que o IOPS de linha de base.

### <a name="workaround"></a>Solução alternativa

- Reduza a carga de solicitação do aplicativo cliente, para que a ação não seja estrangulada.
- Aumente a cota da ação para que a ação não seja estrangulada.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Diretório excessivoAbrir/diretórioFechar chamadas

### <a name="cause"></a>Causa

Se o número de chamadas DirectoryOpen/DirectoryClose estiver entre as principais chamadas de API e você não esperar que o cliente esteja fazendo tantas chamadas, pode ser um problema com o antivírus instalado na VM cliente do Azure.

### <a name="workaround"></a>Solução alternativa

- Uma correção para este problema está disponível na [Atualização da Plataforma de Abril para Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>A criação de arquivos é mais lenta do que o esperado

### <a name="cause"></a>Causa

As cargas de trabalho que dependem da criação de um grande número de arquivos não verão uma diferença substancial entre o desempenho de compartilhamentos de arquivos premium e compartilhamentos de arquivos padrão.

### <a name="workaround"></a>Solução alternativa

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou Server 2012 R2

### <a name="cause"></a>Causa

Latência maior do que o esperado acessando arquivos Azure para cargas de trabalho intensivas em IO.

### <a name="workaround"></a>Solução alternativa

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível .

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se um compartilhamento de arquivos for estrangulado

1. No [portal Azure,](https://portal.azure.com)clique em **Monitor**. 

2. Clique **em Alertas** e clique **em + Nova regra de alerta**.

3. Clique **em Selecionar** para selecionar o recurso de **conta/arquivo** de armazenamento que contém o compartilhamento de arquivos que você deseja alertar e, em seguida, clique em **Feito**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/file.

4. Clique **em Adicionar** para adicionar uma condição.

5. Você verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **Transações.**

6. Na **lâmina lógica de sinal Configurar,** vá para a dimensão **tipo resposta,** clique na **dimensão de valores** parabaixo e selecione **SuccessWithThrottling** (for SMB) ou **ClientThrottlingError** (para REST). 

  > [!NOTE]
  > Se o valor da dimensão SuccessWithThrottling ou ClientThrottlingError não estiver listado, isso significa que o recurso não foi estrangulado.  Para adicionar o valor **+** da dimensão, clique no lado da **dimensão valores** de saque, digite **SuccessWithThrottling** ou **ClientThrottlingError**, clique em **OK** e repita a etapa #6.

7. Vá para a dimensão **Compartilhamento de arquivos,** clique na **dimensão de valores** de sibilamento e selecione os compartilhamentos de arquivos que deseja alertar. 

  > [!NOTE]
  > Se o compartilhamento de arquivos for um compartilhamento padrão de arquivos, os valores de dimensão serão em branco porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas de estrangulamento para compartilhamentos de arquivos padrão serão acionados se qualquer compartilhamento de arquivo dentro da conta de armazenamento for estrangulado e o alerta não identificar qual compartilhamento de arquivo foi estrangulado. Uma vez que as métricas por ação não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivo por conta de armazenamento. 

8. Defina os **parâmetros de alerta** (limiar, operador, granularidade e frequência da agregação) que são usados para avaliar a regra de alerta métrica e clique **em Feito**.

  > [!TIP]
  > Se você estiver usando um limiar estático, o gráfico métrico pode ajudar a determinar um limite razoável se o compartilhamento de arquivos estiver sendo estrangulado no momento. Se você estiver usando um limiar dinâmico, o gráfico métrico exibirá os limiares calculados com base em dados recentes.

9. Adicione um **grupo de ação** (e-mail, SMS, etc.) ao alerta selecionando um grupo de ação existente ou criando um novo grupo de ação.

10. Preencha os **detalhes** do Alerta como **nome da regra Alerta,** **Descrição** e **Gravidade**.

11. Clique **em Criar regra de alerta** para criar o alerta.

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [Visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
