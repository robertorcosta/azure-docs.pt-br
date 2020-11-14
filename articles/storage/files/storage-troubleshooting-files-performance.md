---
title: Guia de solução de problemas de desempenho de compartilhamento de arquivos do Azure
description: Solucionar problemas de desempenho conhecidos com compartilhamentos de arquivos do Azure. Descubra possíveis causas e soluções alternativas associadas quando esses problemas forem encontrados.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 3e6490babb5a4e68c1ecd931251ea4eb99d6c3f5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630134"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Solucionar problemas de desempenho de compartilhamento de arquivos do Azure

Este artigo lista alguns problemas comuns relacionados aos compartilhamentos de arquivos do Azure. Ele fornece possíveis causas e soluções alternativas para quando você encontrar esses problemas.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Alta latência, baixa taxa de transferência e problemas gerais de desempenho

### <a name="cause-1-share-was-throttled"></a>Causa 1: o compartilhamento foi limitado

As solicitações são limitadas quando os limites de IOPS (operações de e/s por segundo), de entrada ou de saída de um compartilhamento de arquivos são atingidos. Para entender os limites dos compartilhamentos de arquivos Standard e Premium, consulte [compartilhamento de arquivos e destinos de escala de arquivos](./storage-files-scale-targets.md#file-share-and-file-scale-targets).

Para confirmar se o compartilhamento está sendo limitado, você pode acessar e usar as métricas do Azure no Portal.

1. No portal do Microsoft Azure, acesse sua conta de armazenamento.

1. No painel esquerdo, em **monitoramento** , selecione **métricas**.

1. Selecione **arquivo** como o namespace de métrica para o escopo da conta de armazenamento.

1. Selecione **Transações** como a métrica.

1. Adicione um filtro para **tipo de resposta** e, em seguida, verifique se alguma solicitação tem um dos seguintes códigos de resposta:
   * **SuccessWithThrottling** : para SMB (bloco de mensagens do servidor)
   * **ClientThrottlingError** : para REST

   ![Captura de tela das opções de métricas para compartilhamentos de arquivos premium, mostrando um filtro de propriedade "tipo de resposta".](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > Para receber um alerta, consulte a seção ["como criar um alerta se um compartilhamento de arquivos for limitado"](#how-to-create-an-alert-if-a-file-share-is-throttled) mais adiante neste artigo.

### <a name="solution"></a>Solução

- Se você estiver usando um compartilhamento de arquivos padrão, habilite [grandes compartilhamentos de arquivos](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) em sua conta de armazenamento. Compartilhamentos de arquivos grandes dão suporte a até 10.000 IOPS por compartilhamento.
- Se você estiver usando um compartilhamento de arquivos premium, aumente o tamanho do compartilhamento de arquivos provisionado para aumentar o limite de IOPS. Para saber mais, consulte a seção "compreendendo o provisionamento para compartilhamentos de arquivos Premium" no [Guia de planejamento de arquivos do Azure](./storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>Causa 2: metadados ou carga de trabalho pesada de namespace

Se a maioria de suas solicitações for centrada em metadados (como CreateFile, OpenFile, CloseFile, QueryInfo ou querydirectory), a latência será pior do que as operações de leitura/gravação.

Para determinar se a maioria das suas solicitações são centradas em metadados, comece seguindo as etapas 1-4 conforme descrito anteriormente na causa 1. Para a etapa 5, em vez de adicionar um filtro para o **tipo de resposta** , adicione um filtro de propriedade para o **nome da API**.

![Captura de tela das opções de métricas para compartilhamentos de arquivos premium, mostrando um filtro de propriedade "nome da API".](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução alternativa

- Verifique se o aplicativo pode ser modificado para reduzir o número de operações de metadados.
- Adicione um VHD (disco rígido virtual) no compartilhamento de arquivos e monte o VHD sobre SMB do cliente para executar operações de arquivo nos dados. Essa abordagem funciona para cenários de gravador único e vários leitores e permite que as operações de metadados sejam locais. A instalação oferece desempenho semelhante ao de um armazenamento local diretamente anexado.

### <a name="cause-3-single-threaded-application"></a>Causa 3: aplicativo de thread único

Se o aplicativo que você está usando for de thread único, essa configuração poderá resultar em uma taxa de transferência de IOPS significativamente menor do que a taxa de transferência máxima possível, dependendo do tamanho do compartilhamento provisionado.

### <a name="solution"></a>Solução

- Aumente o paralelismo do aplicativo aumentando o número de threads.
- Alterne para aplicativos onde o paralelismo é possível. Por exemplo, para operações de cópia, você pode usar o AzCopy ou o RoboCopy de clientes Windows ou o comando **paralelo** de clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para solicitações

### <a name="cause"></a>Causa

A VM (máquina virtual) do cliente pode estar localizada em uma região diferente do compartilhamento de arquivos.

### <a name="solution"></a>Solução

- Execute o aplicativo de uma VM que está localizada na mesma região que o compartilhamento de arquivos.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>O cliente não consegue obter a taxa de transferência máxima com suporte pela rede

### <a name="cause"></a>Causa
Uma possível causa é a falta de suporte a vários canais SMB. Atualmente, os arquivos do Azure dão suporte apenas a um único canal, portanto, há apenas uma conexão da VM do cliente para o servidor. Essa conexão única é vinculada a um único núcleo na VM do cliente, portanto, a taxa de transferência máxima Obtida de uma VM é associada a um único núcleo.

### <a name="workaround"></a>Solução alternativa

- A obtenção de uma VM com um núcleo maior pode ajudar a melhorar a taxa de transferência.
- A execução do aplicativo cliente de várias VMs aumentará a taxa de transferência.
- Use as APIs REST sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>A taxa de transferência em clientes Linux é significativamente menor do que a de clientes Windows

### <a name="cause"></a>Causa

Esse é um problema conhecido com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução alternativa

- Espalhe a carga entre várias VMs.
- Na mesma VM, use vários pontos de montagem com uma opção **nosharesock** e espalhe a carga entre esses pontos de montagem.
- No Linux, tente montar com uma opção **nostrictsync** para evitar forçar uma liberação SMB em cada chamada **fsync** . Para arquivos do Azure, essa opção não interfere na consistência dos dados, mas pode resultar em metadados de arquivo obsoletos em listagens de diretório (comando **ls-l** ). Consultar diretamente os metadados de arquivo usando o comando **stat** retornará os metadados de arquivo mais atualizados.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latências altas para cargas de trabalho pesadas de metadados que envolvem operações amplas de abertura/fechamento

### <a name="cause"></a>Causa

Falta de suporte para concessões de diretório.

### <a name="workaround"></a>Solução alternativa

- Se possível, evite usar um identificador de abertura/fechamento excessivo no mesmo diretório em um curto período de tempo.
- Para VMs do Linux, aumente o tempo limite do cache de entrada de diretório especificando **actimeo = \<sec>** como uma opção de montagem. Por padrão, o tempo limite é de 1 segundo, portanto, um valor maior, como 3 ou 5 segundos, pode ajudar.
- Para VMs CentOS Linux ou Red Hat Enterprise Linux (RHEL), atualize o sistema para CentOS Linux 8,2 ou RHEL 8,2. Para outras VMs do Linux, atualize o kernel para 5,0 ou posterior.

## <a name="low-iops-on-centos-linux-or-rhel"></a>IOPS baixo em CentOS Linux ou RHEL

### <a name="cause"></a>Causa

Uma profundidade de e/s maior que 1 não tem suporte no CentOS Linux ou RHEL.

### <a name="workaround"></a>Solução alternativa

- Atualize para o CentOS Linux 8 ou RHEL 8.
- Altere para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Cópia de arquivos lenta de e para compartilhamentos de arquivos do Azure no Linux

Se estiver experimentando uma cópia de arquivo lenta, consulte a seção "copiando arquivos lentos de e para compartilhamentos de arquivos do Azure no Linux" no [Guia de solução de problemas do Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux).

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Padrão de tremulação ou Sawtooth para IOPS

### <a name="cause"></a>Causa

O aplicativo cliente excede consistentemente o IOPS de linha de base. Atualmente, não há nenhuma suavização do lado do serviço da carga da solicitação. Se o cliente exceder o IOPS de linha de base, ele será limitado pelo serviço. A limitação pode resultar no cliente que está apresentando um padrão de Sawtooth de tremulação ou de IOPS. Nesse caso, a média de IOPS alcançada pelo cliente pode ser menor do que o IOPS de linha de base.

### <a name="workaround"></a>Solução alternativa
- Reduza a carga de solicitação do aplicativo cliente, para que o compartilhamento não seja limitado.
- Aumente a cota do compartilhamento para que o compartilhamento não seja limitado.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas de DirectoryOpen/DirectoryClose

### <a name="cause"></a>Causa

Se o número de chamadas **DirectoryOpen/DirectoryClose** estiver entre as principais chamadas de API e você não esperar que o cliente faça essa quantidade de chamadas, o problema poderá ser causado pelo software antivírus que está instalado na VM do cliente do Azure.

### <a name="workaround"></a>Solução alternativa

- Uma correção para esse problema está disponível na [atualização da plataforma de abril para Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>A criação do arquivo é mais lenta do que o esperado

### <a name="cause"></a>Causa

As cargas de trabalho que dependem da criação de um grande número de arquivos não verão uma diferença significativa no desempenho entre compartilhamentos de arquivos Premium e compartilhamentos de arquivos padrão.

### <a name="workaround"></a>Solução alternativa

- nenhuma.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou do servidor 2012 R2

### <a name="cause"></a>Causa

Maior que a latência esperada acessando compartilhamentos de arquivos do Azure para cargas de trabalho com e/s intensa.

### <a name="workaround"></a>Solução alternativa

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se um compartilhamento de arquivos for limitado

1. No portal do Microsoft Azure, acesse sua conta de armazenamento.
1. Na seção **monitoramento** , selecione **alertas** e, em seguida, selecione **nova regra de alerta**.
1. Selecione **Editar recurso** , selecione o **tipo de recurso arquivo** para a conta de armazenamento e, em seguida, selecione **concluído**. Por exemplo, se o nome da conta de armazenamento for *contoso* , selecione o recurso contoso/File.
1. Selecione **Selecionar condição** para adicionar uma condição.
1. Na lista de sinais que têm suporte para a conta de armazenamento, selecione a métrica **Transações** .
1. No painel **Configurar lógica de sinal** , na lista suspensa **nome da dimensão** , selecione tipo de **resposta**.
1. Na lista suspensa **valores de dimensão** , selecione **SUCCESSWITHTHROTTLING** (para SMB) ou **ClientThrottlingError** (para REST).

   > [!NOTE]
   > Se nem o valor da dimensão **SuccessWithThrottling** nem o **ClientThrottlingError** estiver listado, isso significa que o recurso não foi limitado. Para adicionar o valor de dimensão, ao lado da lista suspensa **valores de dimensão** , selecione **adicionar valor personalizado** , insira **SuccessWithThrottling** ou **ClientThrottlingError** , selecione **OK** e repita a etapa 7.

1. Na lista suspensa **nome da dimensão** , selecione Compartilhamento de **arquivos**.
1. Na lista suspensa **valores de dimensão** , selecione o compartilhamento de arquivos ou os compartilhamentos que você deseja alertar.

   > [!NOTE]
   > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, selecione **todos os valores atuais e futuros**. A lista suspensa valores de dimensão não lista os compartilhamentos de arquivos, pois as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas de limitação para compartilhamentos de arquivos padrão serão disparados se algum compartilhamento de arquivos dentro da conta de armazenamento for limitado e o alerta não identificar qual compartilhamento de arquivos foi limitado. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, recomendamos que você use um compartilhamento de arquivos por conta de armazenamento.

1. Defina os parâmetros de alerta inserindo **o valor de limite** , o **operador** , a **granularidade de agregação** e a **frequência de avaliação** e, em seguida, selecione **concluído**.

    > [!TIP]
    > Se você estiver usando um limite estático, o gráfico de métrica poderá ajudá-lo a determinar um valor de limite razoável se o compartilhamento de arquivos estiver sendo limitado no momento. Se você estiver usando um limite dinâmico, o gráfico de métrica exibirá os limites calculados com base nos dados recentes.

1. Selecione **selecionar grupo de ação** e, em seguida, adicione um grupo de ação (por exemplo, email ou SMS) ao alerta selecionando um grupo de ação existente ou criando um novo grupo de ação.
1. Insira os detalhes do alerta, como **nome da regra de alerta** , **Descrição** e **severidade**.
1. Selecione **Criar regra de alerta** para criar o alerta.

Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral de alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Como criar alertas se um compartilhamento de arquivos Premium estiver se aproximando da limitação

1. No portal do Microsoft Azure, acesse sua conta de armazenamento.
1. Na seção **monitoramento** , selecione **alertas** e, em seguida, selecione **nova regra de alerta**.
1. Selecione **Editar recurso** , selecione o **tipo de recurso arquivo** para a conta de armazenamento e, em seguida, selecione **concluído**. Por exemplo, se o nome da conta de armazenamento for *contoso* , selecione o recurso contoso/File.
1. Selecione **Selecionar condição** para adicionar uma condição.
1. Na lista de sinais que têm suporte para a conta de armazenamento, selecione a métrica de **saída** .

   > [!NOTE]
   > Você precisa criar três alertas separados para serem alertados quando os valores de entrada, de egresso ou de transação excederem os limites definidos. Isso ocorre porque um alerta é disparado somente quando todas as condições são atendidas. Por exemplo, se você colocar todas as condições em um alerta, você só será alertado se a entrada, saída e transações excederem seus valores de limite.

1. Role para baixo. Na lista suspensa **nome da dimensão** , selecione Compartilhamento de **arquivos**.
1. Na lista suspensa **valores de dimensão** , selecione o compartilhamento de arquivos ou os compartilhamentos que você deseja alertar.
1. Defina os parâmetros de alerta selecionando valores nas listas suspensas **operador** , **valor de limite** , **granularidade de agregação** e **frequência de avaliação** e, em seguida, selecione **concluído**.

   As métricas de saída, de entrada e de transações são expressas por minuto, embora você tenha provisionado egresso, entrada e e/s por segundo. Portanto, por exemplo, se a saída provisionada for 90 &nbsp; mebibytes por segundo (MiB/s) e você quiser que o limite seja &nbsp; de 80% de saída provisionada, selecione os seguintes parâmetros de alerta: 
   - Para o **valor de limite** : *75497472* 
   - Para o **operador** : *maior ou igual a*
   - Para o **tipo de agregação** : *média*
   
   Dependendo de quanto ruído você deseja que o alerta seja, você também pode selecionar valores para **granularidade de agregação** e **frequência de avaliação**. Por exemplo, se você quiser que o alerta examine a entrada média durante o período de 1 hora e quiser que sua regra de alerta seja executada a cada hora, selecione o seguinte:
   - Para **granularidade de agregação** : *1 hora*
   - Para **frequência de avaliação** : *1 hora*

1. Selecione **selecionar grupo de ação** e, em seguida, adicione um grupo de ação (por exemplo, email ou SMS) ao alerta selecionando um grupo de ações existente ou criando um novo.
1. Insira os detalhes do alerta, como **nome da regra de alerta** , **Descrição** e **severidade**.
1. Selecione **Criar regra de alerta** para criar o alerta.

    > [!NOTE]
    > - Para ser notificado de que seu compartilhamento de arquivos Premium está perto de ser limitado *devido à entrada provisionada* , siga as instruções anteriores, mas com a seguinte alteração:
    >    - Na etapa 5, selecione a métrica de **entrada** em vez de **saída**.
    >
    > - Para ser notificado de que o compartilhamento de arquivos Premium está perto de ser limitado *devido ao IOPS provisionado* , siga as instruções anteriores, mas com as seguintes alterações:
    >    - Na etapa 5, selecione a métrica **Transações** em vez de **saída**.
    >    - Na etapa 10, a única opção para o **tipo de agregação** é *total*. Portanto, o valor de limite depende da granularidade de agregação selecionada. Por exemplo, se você quiser que o limite seja &nbsp; de 80% de IOPS de linha de base provisionado e selecionar *1 hora* para **granularidade de agregação** , seu **valor de limite** será o IOPS de linha de base (em bytes) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral de alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Confira também
- [Solucionar problemas do Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Solucionar problemas do Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [Perguntas frequentes sobre os Arquivos do Azure](storage-files-faq.md)
