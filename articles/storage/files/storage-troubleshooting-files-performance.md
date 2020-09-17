---
title: Guia de solução de problemas de desempenho de arquivos do Azure
description: Solucionar problemas de desempenho conhecidos com compartilhamentos de arquivos do Azure. Descubra possíveis causas e soluções alternativas associadas quando esses problemas forem encontrados.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 7afaa057ecc94cf67d4fd5b041d95210fcf26717
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707587"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Solucionar problemas de desempenho de arquivos do Azure

Este artigo lista alguns problemas comuns relacionados aos compartilhamentos de arquivos do Azure. Ele fornece possíveis causas e soluções alternativas quando esses problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Alta latência, baixa taxa de transferência e problemas gerais de desempenho

### <a name="cause-1-share-was-throttled"></a>Causa 1: o compartilhamento foi limitado

As solicitações são limitadas quando os limites de IOPS, de entrada ou de saída de um compartilhamento de arquivos são atingidos. Para entender os limites dos compartilhamentos de arquivos Standard e Premium, consulte [compartilhamento de arquivos e destinos de escala de arquivos](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets).

Para confirmar se o compartilhamento está sendo limitado, você pode aproveitar as métricas do Azure no Portal.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** e, em seguida, pesquise **métricas**.

1. Selecione **Métricas**.

1. Selecione sua conta de armazenamento como o recurso.

1. Selecione **arquivo** como o namespace de métrica.

1. Selecione **Transações** como a métrica.

1. Adicione um filtro para **ResponseType** e verifique se alguma solicitação tem um código de resposta de **SUCCESSWITHTHROTTLING** (para SMB) ou **ClientThrottlingError** (para REST).

![Opções de métricas para compartilhamentos de filepremium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Para receber um alerta se um compartilhamento de arquivos for limitado, consulte [como criar um alerta se um compartilhamento de arquivos for limitado](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Solução

- Se você estiver usando um compartilhamento de arquivos padrão, habilite [grandes compartilhamentos de arquivos](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) em sua conta de armazenamento. Compartilhamentos de arquivos grandes dão suporte a até 10.000 IOPS por compartilhamento.
- Se você estiver usando um compartilhamento de arquivos premium, aumente o tamanho do compartilhamento de arquivos provisionado para aumentar o limite de IOPS. Para saber mais, confira a seção [noções básicas sobre provisionamento para compartilhamentos de arquivos Premium](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) no guia de planejamento de arquivos do Azure.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: carga de trabalho pesada de metadados/namespace

Se a maioria de suas solicitações for centrada em metadados, (como CreateFile/OpenFile/CloseFile/QueryInfo/querydirectory), a latência será pior quando comparada com as operações de leitura/gravação.

Para confirmar se a maioria das suas solicitações são centradas em metadados, você pode usar as mesmas etapas acima. Exceto em vez de adicionar um filtro para **ResponseType**, adicione um filtro para o **nome da API**.

![Filtrar o nome da API em suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução alternativa

- Verifique se o aplicativo pode ser modificado para reduzir o número de operações de metadados.
- Adicione um VHD no compartilhamento de arquivos e monte o VHD sobre SMB do cliente para executar operações de arquivos nos dados. Essa abordagem funciona para cenários de gravador único e vários leitores e permite que as operações de metadados sejam locais, oferecendo desempenho semelhante a um armazenamento de conexão direta local.

### <a name="cause-3-single-threaded-application"></a>Causa 3: aplicativo de thread único

Se o aplicativo que está sendo usado pelo cliente for de thread único, isso poderá resultar em IOPS/taxa de transferência significativamente menor do que o máximo possível com base no tamanho do compartilhamento provisionado.

### <a name="solution"></a>Solução

- Aumente o paralelismo do aplicativo aumentando o número de threads.
- Alterne para aplicativos onde o paralelismo é possível. Por exemplo, para operações de cópia, os clientes podem usar o AzCopy ou o RoboCopy de clientes Windows ou o comando **paralelo** em clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para solicitações

### <a name="cause"></a>Causa

A VM do cliente pode estar localizada em uma região diferente do compartilhamento de arquivos.

### <a name="solution"></a>Solução

- Execute o aplicativo de uma VM que está localizada na mesma região que o compartilhamento de arquivos.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>O cliente não consegue obter a taxa de transferência máxima com suporte pela rede

Uma causa potencial disso é a falta de suporte a vários canais SMB. Atualmente, os compartilhamentos de arquivos do Azure dão suporte apenas a um único canal, portanto, há apenas uma conexão da VM do cliente com o servidor. Essa conexão única é vinculada a um único núcleo na VM do cliente, portanto, a taxa de transferência máxima Obtida de uma VM é associada a um único núcleo.

### <a name="workaround"></a>Solução alternativa

- A obtenção de uma VM com um núcleo maior pode ajudar a melhorar a taxa de transferência.
- A execução do aplicativo cliente de várias VMs aumentará a taxa de transferência.

- Use as APIs REST sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>A taxa de transferência em clientes Linux é significativamente menor quando comparada aos clientes Windows.

### <a name="cause"></a>Causa

Esse é um problema conhecido com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução alternativa

- Espalhe a carga entre várias VMs.
- Na mesma VM, use vários pontos de montagem com a opção **nosharesock** e espalhe a carga entre esses pontos de montagem.
- No Linux, tente montar com a opção **nostrictsync** para evitar forçar a liberação SMB em cada chamada **fsync** . Para arquivos do Azure, essa opção não interfere na consistência dos dados, mas pode resultar em metadados de arquivo obsoletos na listagem de diretório (comando**ls-l** ). Consultar diretamente os metadados do arquivo (comando**stat** ) retornará os metadados de arquivo mais atualizados.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Altas latências de metadados cargas de trabalho pesadas envolvendo operações de abertura/fechamento extensivas.

### <a name="cause"></a>Causa

Falta de suporte para concessões de diretório.

### <a name="workaround"></a>Solução alternativa

- Se possível, evite um excesso de identificadores de abertura/fechamento no mesmo diretório em um curto período de tempo.
- Para VMs do Linux, aumente o tempo limite do cache de entrada de diretório especificando **actimeo = \<sec> ** como uma opção de montagem. Por padrão, é um segundo, portanto, um valor maior, como três ou cinco, pode ajudar.
- Para VMs do Linux, atualize o kernel para 4,20 ou superior.

## <a name="low-iops-on-centosrhel"></a>IOPS baixo no CentOS/RHEL

### <a name="cause"></a>Causa

A profundidade de e/s maior que uma não tem suporte no CentOS/RHEL.

### <a name="workaround"></a>Solução alternativa

- Atualize para o CentOS 8/RHEL 8.
- Altere para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Linux

Se estiver experimentando uma cópia de arquivos lenta de e para arquivos do Azure, confira a seção [cópia de arquivo lento de e para arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) no guia de solução de problemas do Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Tremulação/padrão de serra-Tooth para IOPS

### <a name="cause"></a>Causa

O aplicativo cliente excede consistentemente o IOPS de linha de base. Atualmente, não há nenhuma suavização do lado do serviço da carga da solicitação; portanto, se o cliente exceder o IOPS de linha de base, ele será limitado pelo serviço. Essa limitação pode resultar no cliente com um padrão de IOPS de tremulação/Serra-Tooth. Nesse caso, o IOPS médio obtido pelo cliente pode ser menor do que o IOPS de linha de base.

### <a name="workaround"></a>Solução alternativa

- Reduza a carga de solicitação do aplicativo cliente, para que o compartilhamento não seja limitado.
- Aumente a cota do compartilhamento para que o compartilhamento não seja limitado.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas de DirectoryOpen/DirectoryClose

### <a name="cause"></a>Causa

Se o número de chamadas DirectoryOpen/DirectoryClose estiver entre as principais chamadas de API e você não espera que o cliente esteja fazendo essa quantidade de chamadas, pode ser um problema com o antivírus instalado na VM do cliente do Azure.

### <a name="workaround"></a>Solução alternativa

- Uma correção para esse problema está disponível na [atualização da plataforma de abril para Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>A criação do arquivo é mais lenta do que o esperado

### <a name="cause"></a>Causa

As cargas de trabalho que dependem da criação de um grande número de arquivos não verão uma diferença significativa entre o desempenho de compartilhamentos de arquivos Premium e compartilhamentos de arquivos padrão.

### <a name="workaround"></a>Solução alternativa

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou do servidor 2012 R2

### <a name="cause"></a>Causa

Maior que a latência esperada Acessando arquivos do Azure para cargas de trabalho com uso intensivo de e

### <a name="workaround"></a>Solução alternativa

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se um compartilhamento de arquivos for limitado

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção monitoramento, clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/File.
4. Clique em **Selecionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica **Transações** .
6. Na folha **Configurar lógica de sinal** , clique na lista suspensa **nome da dimensão** e selecione **tipo de resposta**.
7. Clique na lista suspensa **valores de dimensão** e selecione **SUCCESSWITHTHROTTLING** (para SMB) ou **ClientThrottlingError** (para REST).

  > [!NOTE]
  > Se o valor da dimensão SuccessWithThrottling ou ClientThrottlingError não estiver listado, isso significará que o recurso não foi limitado. Para adicionar o valor de dimensão, clique em **adicionar valor personalizado** ao lado da lista suspensa **valores de dimensão** , digite **SuccessWithThrottling** ou **ClientThrottlingError**, clique em **OK** e repita a etapa #7.

8. Clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**.
9. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.

  > [!NOTE]
  > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, selecione **todos os valores atuais e futuros**. O menu suspenso valores de dimensão não listará os compartilhamentos de arquivos porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas de limitação para compartilhamentos de arquivos padrão serão disparados se algum compartilhamento de arquivos dentro da conta de armazenamento for limitado e o alerta não identificar qual compartilhamento de arquivos foi limitado. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivos por conta de armazenamento.

10. Defina os **parâmetros de alerta** (valor de limite, operador, granularidade de agregação e frequência de avaliação) e clique em **concluído**.

  > [!TIP]
  > Se você estiver usando um limite estático, o gráfico de métrica poderá ajudar a determinar um valor de limite razoável se o compartilhamento de arquivos estiver sendo limitado no momento. Se você estiver usando um limite dinâmico, o gráfico de métrica exibirá os limites calculados com base nos dados recentes.

11. Clique em **selecionar grupo de ações** para adicionar um **grupo de ações** (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
13. Clique em **criar regra de alerta** para criar o alerta.

Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral de alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-towards-being-throttled"></a>Como criar alertas se um compartilhamento de arquivos Premium estiver em direção à limitação

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção monitoramento, clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/File.
4. Clique em **Selecionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica de **saída** .

  > [!NOTE]
  > Você precisa criar 3 alertas separados para serem alertados quando a entrada, saída ou transações exceder o valor de limite definido. Isso ocorre porque um alerta só é acionado quando todas as condições são atendidas. Portanto, se você colocar todas as condições em um alerta, só será alertado se a entrada, saída e transações excederem seus valores de limite.

6. Role para baixo. Clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**.
7. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.
8. Defina os **parâmetros de alerta** (valor de limite, operador, granularidade de agregação e frequência de avaliação) e clique em **concluído**.

  > [!NOTE]
  > As métricas de egresso, de entrada e de transações são por minuto, embora você seja provisionado a saída, a entrada e a IOPS por segundo. (fale sobre a granularidade de agregação-> por minuto = mais ruído, portanto, escolha diff One) Portanto, por exemplo, se a saída provisionada for 90 MiB/segundo e você quiser que o limite seja de 80% de saída provisionada, deverá selecionar os seguintes parâmetros de alerta: 75497472 para **valor de limite**, maior ou igual a para **operador**e média para o **tipo de agregação**. Dependendo de quanto ruído você deseja que o alerta seja, você pode escolher quais valores selecionar para granularidade de agregação e frequência de avaliação. Por exemplo, se eu quiser que meu alerta examine a entrada média durante o período de uma hora e eu quiser que minha regra de alerta seja executada a cada hora, eu selecionaria uma hora para a **granularidade de agregação** e uma hora para a **frequência de avaliação**.

9. Clique em **selecionar grupo de ações** para adicionar um **grupo de ações** (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
10. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
11. Clique em **criar regra de alerta** para criar o alerta.

  > [!NOTE]
  > Para ser notificado se o compartilhamento de arquivos Premium estiver perto de ser limitado devido à entrada provisionada, siga as mesmas etapas, exceto na etapa 5, selecione a métrica de **entrada** em vez disso.

  > [!NOTE]
  > Para ser notificado se o compartilhamento de arquivos Premium estiver perto de ser limitado devido ao IOPS provisionado, você terá que fazer algumas alterações. Na etapa 5, selecione a métrica **Transações** em vez disso. Além disso, para a etapa 10, a única opção para o **tipo de agregação** é total. Portanto, o valor de limite seria dependente de sua granularidade de agregação selecionada. Por exemplo, se você quisesse que o limite fosse de 80% de IOPS de linha de base provisionado e selecionou 1 hora para **granularidade de agregação**, seu **valor de limite** será o IOPS de linha de base (em bytes) x 0,8 x 3600. Além dessas alterações, siga as mesmas etapas listadas acima. 

Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral de alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Confira também
* [Solucionar problemas do Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas do Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Perguntas frequentes sobre o Azure Files](storage-files-faq.md)
