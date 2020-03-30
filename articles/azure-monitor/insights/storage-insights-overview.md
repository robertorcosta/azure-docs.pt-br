---
title: Monitore os serviços de armazenamento do Azure com o Monitor para Armazenamento (pré-visualização)| Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor for Storage que fornece administradores de armazenamento com uma rápida compreensão dos problemas de desempenho e utilização com suas contas de armazenamento do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662512"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Monitorando seu serviço de armazenamento com o Azure Monitor for Storage (visualização)

A visualização do Azure Monitor for Storage (preview) fornece um monitoramento abrangente de suas contas de armazenamento do Azure, fornecendo uma visão unificada do desempenho, capacidade e disponibilidade dos serviços de armazenamento do Azure. Você pode observar a capacidade de armazenamento e o desempenho de duas maneiras, exibir diretamente de uma conta de armazenamento ou exibir do Azure Monitor para ver entre grupos de contas de armazenamento. 

Este artigo irá ajudá-lo a entender a experiência que o Azure Monitor for Storage (preview) oferece para obter conhecimento acionável sobre a saúde e o desempenho das contas de armazenamento em escala, com a capacidade de se concentrar em hotspots e diagnosticar latência, estrangulamento, e problemas de disponibilidade.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Introdução ao Monitor azure para armazenamento (visualização)

Antes de mergulhar na experiência, você deve entender como ela apresenta e visualiza informações. Se você selecionar o recurso armazenamento diretamente de uma conta de armazenamento ou do Azure Monitor, o Azure Monitor for Storage apresenta uma experiência consistente. 

Combinado que entrega:

* **Em perspectiva de escala** mostrando uma visão instantânea de sua disponibilidade com base na saúde do serviço de armazenamento ou na operação da API, utilização mostrando número total de solicitações que o serviço de armazenamento recebe e latência mostrando o tempo médio que o serviço de armazenamento ou tipo de operação de API está levando para processar solicitações. Você também pode visualizar a capacidade por bolha, arquivo, tabela e fila.

* **Aprofunde a análise** de uma determinada conta de armazenamento para ajudar a diagnosticar problemas ou realizar análises detalhadas por categoria - disponibilidade, desempenho, falhas e capacidade. Selecionar qualquer uma dessas opções fornece uma visão aprofundada das métricas.  

* **Personalizável** onde você pode alterar quais métricas você deseja ver, modificar ou definir limites que se alinham com seus limites e salvar como sua própria carteira de trabalho. Gráficos na pasta de trabalho podem ser fixados no painel do Azure.  

Esse recurso não exige que você habilite ou configure nada, as métricas de armazenamento de suas contas de armazenamento são coletadas por padrão. Se você não estiver familiarizado com as métricas disponíveis no Azure Storage, visualize a descrição e a definição nas métricas de armazenamento do Azure, revisando [as métricas de armazenamento do Azure](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Não há custo para acessar esse recurso e você só será cobrado pelos recursos essenciais do Azure Monitor que você configura ou habilita, conforme descrito na página de detalhes de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

>[!NOTE]
>O Azure Monitor for Storage não suporta [contas v1 de uso geral](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Vista do Monitor Azure

A partir do Azure Monitor, você pode visualizar detalhes de transação, latência e capacidade de várias contas de armazenamento em sua assinatura e ajudar a identificar desempenho, problemas de capacidade e falhas.

Para visualizar a utilização e a disponibilidade de suas contas de armazenamento em todas as suas assinaturas, execute as seguintes etapas.

1. Faça login no [portal Azure](https://portal.azure.com).

2. Selecione **Monitor** no painel esquerdo no portal Azure e, na seção **Insights,** selecione **Contas de armazenamento (visualização)**.

    ![Exibição de várias contas de armazenamento](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Carteira de trabalho visão geral

Na agenda **de trabalho visão** geral da assinatura selecionada, a tabela exibe métricas interativas de armazenamento e estado de disponibilidade de serviço para até 10 contas de armazenamento agrupadas na assinatura. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas de baixa:

* **Assinaturas** - apenas assinaturas que possuem contas de armazenamento são listadas.  

* **Contas de armazenamento** - por padrão, 10 contas de armazenamento são pré-selecionadas. Se você selecionar todas ou várias contas de armazenamento no seletor de escopo, até 200 contas de armazenamento serão devolvidas. Por exemplo, se você tivesse um total de 573 contas de armazenamento em três assinaturas que você selecionou, apenas 200 contas seriam exibidas. 

* **Intervalo de tempo** - por padrão, exibe as últimas 4 horas de informações com base nas seleções correspondentes feitas.

O bloco de balcão as listas de baixa lista-se o número total de contas de armazenamento na assinatura e reflete quantas do total são selecionados. Há codificação de cores condicional ou mapas de calor para colunas na caderneta de trabalho que relatam métricas ou erros de transação. A cor mais profunda tem o maior valor e uma cor mais clara é baseada nos valores mais baixos. Para as colunas baseadas em erro, o valor está em vermelho e para as colunas baseadas em métricas, o valor está em azul.

Selecione um valor nas colunas **Disponibilidade,** **Latência E2E,** **Latência do servidor**e **o tipo de erro de transação/Erros** direciona você para um relatório adaptado ao tipo específico de métricas de armazenamento que correspondem à coluna selecionada para essa conta de armazenamento. Para obter mais informações sobre as abanuais de cada categoria, consulte a seção [Desdetalhada das cadernetas de armazenamento](#detailed-storage-workbooks) abaixo. 

>[!NOTE]
>Para obter detalhes sobre quais erros podem ser mostrados no relatório, consulte [esquema tipo de resposta](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) e procure tipos de resposta como **ServerOtherError,** **ClientOtherError,** **ClientThrottlingError**. Dependendo das contas de armazenamento selecionadas, se houver mais de três tipos de erros relatados, todos os outros erros serão representados na categoria de **Outros**.

O limite de **disponibilidade** padrão é:

* Aviso - 99%
* Crítico - 90%

Para definir um limite de disponibilidade com base nos resultados de sua observação ou requisitos, [revise modificar o limite de disponibilidade](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Caderneta de trabalho de capacidade

Selecione **Capacidade** no topo da página e a caderneta de trabalho **Capacidade** é aberta. Ele mostra a quantidade de armazenamento total usado na conta e a capacidade usada por cada serviço de dados na conta para ajudar a identificar o armazenamento mais e menos utilizado.

![Várias contas de armazenamento Carteira de trabalho de capacidade](./media/storage-insights-overview/storage-account-capacity-02.png) 

Há codificação de cores condicional ou mapas de calor para colunas na caderneta de trabalho que relatam métricas de capacidade com um valor azul. A cor mais profunda tem o maior valor e uma cor mais clara é baseada nos valores mais baixos.

Quando você seleciona um valor em qualquer uma das colunas da caderneta de trabalho, você detalha a caderneta **de capacidade** para a conta de armazenamento. Mais detalhes sobre o relatório de detalhamento são descritos na seção [Desdetalhada das regras de armazenamento](#detailed-storage-workbooks) abaixo. 

## <a name="view-from-a-storage-account"></a>Vista de uma conta de armazenamento

Para acessar o Monitor Azure para VMs diretamente de uma conta de armazenamento:

1. No portal Azure, selecione Contas de armazenamento.

2. Na lista, escolha uma conta de armazenamento. Na seção Monitoramento, escolha Insights (visualização).

    ![Página de exibição geral da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-direct-overview-01.png)

Na agenda **de trabalho 'Visão** geral' da conta de armazenamento, ele mostra várias métricas de desempenho de armazenamento que ajudam a avaliar rapidamente:

* Saúde do serviço de armazenamento para ver imediatamente se um problema fora do seu controle está afetando o serviço de armazenamento na região para a qual ele é implantado, o que é declarado na coluna **Resumo.**

* Gráficos de desempenho interativos mostrando os detalhes mais essenciais relacionados à capacidade de armazenamento, disponibilidade, transações e latência.  

* Ladrilhos métricos e de status destacando a disponibilidade do serviço, a contagem total de transações para o serviço de armazenamento, a latência E2E e a latência do servidor.

A seleção de qualquer um dos botões para **Falhas,** **Desempenho,** **Disponibilidade**e **Capacidade** abre a respectiva caderneta de trabalho. 

![Página de exibição geral da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Livros de trabalho de armazenamento detalhado

Se você selecionou um valor nas colunas **Disponibilidade,** **Latência E2E,** **Latência do servidor**e **tipo de erro de transação/Erros** da pasta de **trabalho da** visão geral da conta de armazenamento múltiplo, ou selecionando qualquer um dos botões para **Falhas,** **Desempenho,** **Disponibilidade**e **Capacidade** da pasta de trabalho visão **geral** de uma conta de armazenamento específica, cada um fornece um conjunto de informações interativas relacionadas ao armazenamento adaptadas a essa categoria.  

* **A disponibilidade** abre a **caderneta** de trabalho Disponibilidade. Ele mostra o estado de saúde atual do serviço de armazenamento Azure, uma tabela que mostra o estado de saúde disponível de cada objeto categorizado pelo serviço de dados definido na conta de armazenamento com uma linha de tendência representando o intervalo de tempo selecionado e um gráfico de tendência de disponibilidade para cada serviço de dados na conta.  

    ![Exemplo de relatório de disponibilidade](./media/storage-insights-overview/storage-account-availability-01.png)

* **A latência** e **a latência do servidor** E2E abrem a pasta de trabalho **Performance.** Ele inclui um bloco de status de rollup mostrando latência e latência do servidor E2E, um gráfico de desempenho de E2E versus latência do servidor e uma tabela quebrando latência de chamadas bem-sucedidas por API categorizada pelo serviço de dados definido na conta de armazenamento.

    ![Exemplo de relatório de desempenho](./media/storage-insights-overview/storage-account-performance-01.png)

* Selecionando qualquer uma das categorias de erro listadas na grade abra a **caderneta de falha.** O relatório mostra as faixas métricas de todos os outros erros do lado do cliente, exceto os descritos e solicitações bem-sucedidas, erros de estrangulamento do cliente, um gráfico de desempenho para a métrica **de dimensão tipo de resposta** da transação específica do atributo ClientOtherError e duas tabelas - **Transações por nome de API** e **Transações por tipo de resposta**.

   ![Exemplo de relatório de falha](./media/storage-insights-overview/storage-account-failures-01.png)

* **A capacidade** abre a **caderneta de** capacidade. Ele mostra a quantidade total de armazenamento usada para cada objeto de dados de armazenamento na conta nas telhas e no gráfico, e quantos objetos de dados são armazenados na conta.  

    ![Página de capacidade da conta de armazenamento selecionada](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Pino e exportação

Você pode fixar qualquer uma das seções métricas em um Painel Do Azure selecionando o ícone pushpin no canto superior direito da seção.

![Pino de seção métrica para exemplo de painel](./media/storage-insights-overview/workbook-pin-example.png)

As obras de **visão geral** ou capacidade da conta de várias assinaturas e **de armazenamento** suportam a exportação dos resultados no formato Excel, selecionando o ícone de seta para baixo à direita do ícone pushpin.

![Exemplo de resultados da grade da cartilha de exportação](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Personalizar o Monitor Azure para Armazenamento (visualização)

Esta seção destaca cenários comuns para a edição da carteira de trabalho para personalizar em suporte às suas necessidades de análise de dados:

* Escopo da carteira de trabalho para selecionar sempre uma determinada conta de assinatura ou armazenamento
* Alterar métricas na grade
* Alterar o limite de disponibilidade
* Alterar a renderização de cores

As personalizações são salvas em uma carteira de trabalho personalizada para evitar a substituição da configuração padrão em nossa carteira de trabalho publicada. As apostões de trabalho são salvas dentro de um grupo de recursos, seja na seção **Meus relatórios** que é privada para você ou na seção **Relatórios Compartilhados** que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a carteira de trabalho personalizada, você precisa ir à galeria da carteira de trabalho para lançá-la.

![Lançar galeria de livros de trabalho da barra de comando](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Especificando uma conta de assinatura ou armazenamento

Você pode configurar a visão **geral** da conta de várias assinaturas e armazenamento geral ou as regras de **capacidade** para escopo de uma determinada assinatura ou conta de armazenamento em cada execução, executar as seguintes etapas.

1. Selecione **Monitor** no portal e selecione **Contas de armazenamento (visualização)** no painel esquerdo.

2. Na **caderneta de visão** geral, da barra de comando seleciona **Editar**.

3. Selecione na lista de **assinaturas** a lista de entrada, uma ou mais assinaturas que você deseja que ela seja padrão. Lembre-se, a carteira de trabalho suporta selecionar até um total de 10 assinaturas.  

4. Selecione na lista de itens de lista de baixa **de contas** de armazenamento uma ou mais contas que você deseja que ela seja padrão. Lembre-se, a carteira de trabalho suporta selecionar até um total de 200 contas de armazenamento. 

5. Selecione **Salvar na** barra de comando para salvar uma cópia da carteira de trabalho com suas personalizações e, em seguida, clique **em Editar feito** para retornar ao modo de leitura.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Modificar métricas e cores na caderneta de trabalho

As cadernetas pré-construídas contêm dados métricos e você tem a capacidade de modificar ou remover qualquer uma das visualizações e personalizar para as necessidades específicas da sua equipe.

Em nosso exemplo, estamos trabalhando com a carteira de trabalho de capacidade de conta de várias assinaturas e armazenamento, para demonstrar como:

* Remova uma métrica
* Alterar renderização de cores

Você pode executar as mesmas alterações em qualquer uma das regras de trabalho **Prebuilt Failures,** **Performance,** **Availability**e **Capacity.**

1. Selecione **Monitor** no portal e selecione **Contas de armazenamento (visualização)** no painel esquerdo.

2. Selecione **Capacidade** de mudar para a caderneta de trabalho de capacidade e na barra de comando, **selecione Editar** na barra de comando.

    ![Selecione editar para modificar uma carteira de trabalho](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Ao lado da seção métricas, selecione **Editar**.

    ![Selecione Editar para modificar métricas da carteira de trabalho de capacidade](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Vamos remover a coluna **de tempo de capacidade usada da conta,** então selecione **Configurações de coluna** na grade de métricas.

    ![Editar configurações da coluna](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. No painel **Editar configurações de coluna,** selecione na seção **Colunas** **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$| A capacidade da conta usada Timeline$** e, a lista de vertentes **de lista baixa,** **seleciona Ocultar**.

6. Selecione **Salvar e fechar** para comprometer sua mudança.

Agora vamos mudar o tema de cor para as métricas de capacidade no relatório para usar verde em vez de azul.

1. Selecione **Configurações de coluna** na grade de métricas.

2. No painel **Editar configurações de coluna,** selecione na seção **Colunas** **microsoft.storage/storage-Capacidades-Capacidade-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-Capacity-Capacity-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storage/storageaccounts/tableservices-** Na lista de dados, **na paleta de cores,** selecione **Verde**.

3. Selecione **Salvar e fechar** para comprometer sua mudança.

4. Selecione **Salvar na** barra de comando para salvar uma cópia da carteira de trabalho com suas personalizações e, em seguida, clique **em Editar feito** para retornar ao modo de leitura.  

### <a name="modify-the-availability-threshold"></a>Modificar o limite de disponibilidade

Neste exemplo, estamos trabalhando com a caderneta de capacidade da conta de armazenamento e demonstrando como modificar o limite de disponibilidade. Por padrão, a disponibilidade percentual de nota de reportagem de telha e grade é configurada com um limite mínimo de 90 e limite máximo de 99. Vamos alterar o valor limite mínimo do % de **disponibilidade** na rede de **nome Availability by API** para 85%, o que significa que o estado de saúde muda para crítico se o limite for inferior a 85%. 

1. Selecione Contas de **armazenamento** no portal e selecione uma conta de armazenamento na lista.

2. Selecione **Insights (visualização)** no painel esquerdo.

3. Na caderneta de trabalho, selecione **Disponibilidade** para mudar para a caderneta de trabalho de disponibilidade e, em seguida, **selecione Editar** na barra de comando. 

4. Role até a parte inferior da página e no lado esquerdo ao lado da grade **Disponibilidade por API,** selecione **Editar**.

    ![Editar disponibilidade por configurações de grade de nomes da API](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selecione **Configurações de coluna** e, em seguida, no painel **Editar configurações de coluna,** na seção **Colunas** selecione **Disponibilidade (%) (Limiares + Formatados)**.

6. Alterar o valor para o estado de saúde **crítico** de **90** para **85** e, em seguida, clique **em Salvar e Fechar**.

    ![Modificar o valor limite de disponibilidade para o estado crítico](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selecione **Salvar na** barra de comando para salvar uma cópia da carteira de trabalho com suas personalizações e, em seguida, clique **em Editar feito** para retornar ao modo de leitura.

## <a name="troubleshooting"></a>Solução de problemas

Esta seção irá ajudá-lo com o diagnóstico e a solução de problemas de alguns dos problemas comuns que você pode encontrar ao usar o Azure Monitor for Storage (visualização). Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Resolvendo problemas de desempenho, capacidade ou disponibilidade

Para ajudar a solucionar quaisquer problemas relacionados ao armazenamento que você identifica com o Azure Monitor for Storage (visualização), consulte a [orientação de solução de problemas do](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)Azure Storage .  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Por que só posso ver 200 contas de armazenamento?

O número de contas de armazenamento selecionadas tem um limite de 200, independentemente do número de assinaturas selecionadas.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>O que acontece quando eu clicar em um azulejo recentemente preso no painel?

* Se você clicar em qualquer lugar no azulejo, ele vai levá-lo para a guia de onde o azulejo foi fixado. Por exemplo, se você fixar um gráfico na guia "Visão geral da conta de armazenamento", então, ao clicar nesse bloco no painel, ele abrirá essa exibição padrão, no entanto, se você fixar um gráfico da sua própria cópia salva, ele abrirá a exibição da sua cópia salva.
* O ícone do filtro no canto superior esquerdo do título abre a guia "Configurar configurações de ladrilho".
* O ícone de elipse no canto superior direito lhe dará as opções para "Personalizar dados de título", "personalizar", "atualizar" e "remover do painel".

### <a name="what-happens-when-i-save-a-workbook"></a>O que acontece quando eu salvar uma carteira de trabalho?

* Quando você salva uma carteira de trabalho, ela permite criar uma nova cópia da carteira de trabalho com suas edições e alterar o título. Salvar não sobrescreta a caderneta de trabalho, a carteira de trabalho atual será sempre a exibição padrão.
* Uma carteira de trabalho **não salva** é apenas a exibição padrão.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Por que não vejo todas as minhas assinaturas no portal?

O portal mostrará dados apenas para assinaturas selecionadas no lançamento do portal. Para alterar quais assinaturas são selecionadas, vá para o canto superior direito e clique no notebook com um ícone de filtro. Isso mostrará a guia Diretório + assinaturas.

![Diretório + assinatura](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Como alterar a coloração e o limite de disponibilidade?

Consulte a [seção Modificar o limiar de disponibilidade](storage-insights-overview.md#modify-the-availability-threshold) para as etapas detalhadas sobre como alterar a coloração e os limites de disponibilidade.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Como analisar e solucionar problemas dos dados mostrados no Azure Monitor for Storage?

 Consulte o [artigo Monitor, diagnóstico e solução de problemas do Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) para obter detalhes sobre como analisar e solucionar problemas dos dados de armazenamento do Azure mostrados no Azure Monitor for Storage.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Por que não vejo todos os tipos de erros nas métricas?

Atualmente, até três tipos diferentes de erros são mostrados e o resto dos erros são agrupados em um único balde. Ele é controlado usando splitByLimit e pode ser modificado. Para mudar esta propriedade:

1. Clique em editar a cartilha.
2. Vá para métricas, clique em editar e, em seguida, selecione **Transações, Soma** ou quaisquer métricas que você deseja editar.

    ![Vá para métricas e clique em editar em seguida, em "Transações, Somas"](./media/storage-insights-overview/fqa7.png)

1. Em seguida, altere o número de splits.

    ![Selecione parâmetros métricos"](./media/storage-insights-overview/fqa7-2.png)

Se você quiser ver n tipos diferentes de erro do que especificar splitByLimit como n+1, 1 extra para o resto dos erros.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Guardei minha carteira de trabalho em alguma conta de armazenamento. Por que não posso encontrá-lo agora?

Cada carteira de trabalho é salva na conta de armazenamento em que você a salvou. Tente encontrar a conta de armazenamento específica na qual o usuário salvou a carteira de trabalho. Caso contrário, não há como encontrar uma carteira de trabalho específica sem saber o recurso (conta de armazenamento).

### <a name="what-is-time-range"></a>O que é o intervalo de tempo?

O intervalo de tempo mostra dados de um determinado período de tempo. Por exemplo, se o intervalo de tempo é de 24 horas, então ele está mostrando dados das últimas 24 horas.

### <a name="what-is-time-granularity-time-grain"></a>O que é granularidade temporal (grão do tempo)?

Granularidade temporal é a diferença de tempo entre dois pontos de dados. Por exemplo, se o grão de tempo for definido como 1 segundo, isso significa que as métricas são coletadas a cada segundo.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Qual é a granularidade do tempo uma vez que fixamos qualquer parte das anotas em um painel?

A granularidade de tempo padrão é definida como automática, atualmente não pode ser alterada neste momento.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Como alterar o intervalo de tempo/intervalo de tempo da etapa da carteira de trabalho no meu painel?

Por padrão, o intervalo de tempo/intervalo de tempo no painel de instrumentos é definido como 24 horas, para alterar este clique nas elipses no canto superior direito, **selecione Personalizar dados de azulejo,** marque "substituir as configurações de tempo do painel no nível do título" e, em seguida, escolha um intervalo de tempo usando o menu suspenso.  

![Selecione as elipses no canto direito da telha e escolha Personalizar esses dados](./media/storage-insights-overview/fqa-data-settings.png)

![Em Configure configuração de configuração, selecione a gota de tempo para alterar o intervalo de tempo/intervalo de tempo](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Como alterar o título da carteira de trabalho ou um passo da carteira de trabalho que fixei em um painel?

O título da carteira de trabalho ou etapa da carteira de trabalho que está fixado em um painel de instrumentos mantém o mesmo nome que tinha na carteira de trabalho. Para alterar o título, você deve salvar sua própria cópia da carteira de trabalho. Em seguida, você será capaz de nomear a carteira de trabalho antes de pressionar salvar.

![Selecione salvar na parte superior para salvar uma cópia da carteira de trabalho e alterar o nome dela](./media/storage-insights-overview/fqa-change-workbook-name.png)

Para alterar o nome de uma etapa da sua carteira de trabalho salva, selecione editar a etapa e, em seguida, selecione a engrenagem na parte inferior das configurações.

![Selecione editar na parte inferior de uma etapa](./media/storage-insights-overview/fqa-edit.png)
![da carteira de trabalho para abrir as configurações Nas configurações selecione a engrenagem na parte inferior, para poder alterar o nome da etapa](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Próximas etapas

* Configure [alertas métricos](../platform/alerts-metric.md) e [notificações de saúde de serviços](../../service-health/alerts-activity-log-service-notifications.md) para configurar alertas automatizados para ajudar na detecção de problemas.

* Saiba os cenários que as cadernetas de trabalho são projetadas para suportar, como criar relatórios novos e personalizar relatórios existentes e muito mais, revisando [Criar relatórios interativos com as pasta de trabalho do Azure Monitor.](../app/usage-workbooks.md)

* Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
