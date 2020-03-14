---
title: Como monitorar uma conta de armazenamento do Azure no portal do Azure | Microsoft Docs
description: Saiba como monitorar uma conta de armazenamento no Azure usando o portal do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268282"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorar uma conta de armazenamento no portal do Azure

[Análise de Armazenamento do Azure](storage-analytics.md) fornece métricas para todos os serviços de armazenamento e logs para blobs, filas e tabelas. Você pode usar o [portal do Azure](https://portal.azure.com) de configurar quais métricas e logs são registrados para sua conta e configurar gráficos que fornecem representações visuais dos dados de métricas. 

Recomendamos que você examine [Azure monitor para armazenamento](../../azure-monitor/insights/storage-insights-overview.md) (versão prévia). É um recurso do Azure Monitor que oferece monitoramento abrangente de suas contas de armazenamento do Azure, fornecendo uma exibição unificada de desempenho, capacidade e disponibilidade dos serviços de armazenamento do Azure. Ele não exige que você habilite ou configure nada, e você pode exibir imediatamente essas métricas dos gráficos interativos predefinidos e de outras visualizações incluídas.

> [!NOTE]
> Há custos associados ao exame de dados de monitoramento no portal do Azure. Para obter mais informações, consulte [Análise de Armazenamento](storage-analytics.md).
>
> O Arquivos do Azure atualmente dá suporte às métricas de Análise de Armazenamento, mas ainda não dá suporte ao registro em log.
>
> As contas de armazenamento de blobs de blocos de desempenho Premium não dão suporte a métricas analíticas de armazenamento, mas oferecem suporte ao log Você pode habilitar o log programaticamente por meio da API REST ou da biblioteca de cliente. Se você quiser exibir métricas com contas de armazenamento de blob de blob de desempenho premium, considere o uso [de métricas de armazenamento do Azure no Azure monitor](storage-metrics-in-azure-monitor.md).
>
> Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configurar o monitoramento para uma conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento** e o nome de conta de armazenamento para abrir o painel de conta.
1. Selecione **Diagnóstico** na seção **MONITORAMENTO** da folha de menu.

    ![Opções de monitoramento](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Selecione o **tipo** de dados de métrica para cada **serviço** que você deseja monitorar e a **política de retenção** para os dados. Você também pode desabilitar o monitoramento definindo **Status** como **Desativado**.

    ![Opções de monitoramento](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Para definir a política de retenção de dados, mova o controle deslizante **Retenção (dias)** ou insira o número de dias de dados devem ser retidos, de 1 a 365. O padrão para novas contas de armazenamento é de sete dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento.

   > [!WARNING]
   > Você é cobrado quando exclui manualmente dados de métricas. Dados de análise obsoletos (dados anteriores à política de retenção) são excluídos pelo sistema sem custo. É recomendável configurar uma política de retenção com base no tempo pelo qual você deseja manter os dados de análise de armazenamento para sua conta. Consulte [cobrança sobre métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   >

1. Ao concluir a configuração de monitoramento, selecione **Salvar**.

Um conjunto de métricas padrão é exibido em gráficos na folha da conta de armazenamento, bem como nas folhas de serviço individuais (blob, fila, tabela e arquivo). Depois que você habilita métricas para um serviço, pode levar até uma hora para que os dados apareçam nos gráficos. Você pode selecionar **Editar** em qualquer gráfico de métricas para configurar quais métricas serão exibidas.

Você pode desabilitar a coleta de métricas e o registro em log definindo **Status** como **Desativado**.

> [!NOTE]
> O Armazenamento do Azure usa o [armazenamento de tabelas](storage-introduction.md#table-storage) para armazenar as métricas para sua conta de armazenamento e armazena as métricas em tabelas em sua conta. Para obter mais informações, consulte: [Como as métricas são armazenadas](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personalizar gráficos de métricas

Use o procedimento a seguir para escolher quais métricas de armazenamento devem ser exibidas em um gráfico de métricas.

1. Comece exibindo um gráfico de métricas de armazenamento no portal do Azure. Você pode encontrar gráficos na **folha da conta de armazenamento** e na folha **Métricas** de um serviço individual (blob, fila, tabela, arquivo).

   Neste exemplo, usa o gráfico a seguir, que aparece na **folha de conta de armazenamento**:

   ![Seleção de gráfico no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Clique em qualquer lugar dentro do gráfico para editar o gráfico.

1. Em seguida, selecione **Editar Gráfico**, selecione o Intervalo de Tempo das métricas para exibir no gráfico e o **service** (blob, fila, tabela, arquivo) cujas métricas você deseja exibir. Aqui, as métricas da semana passada são selecionadas para exibir o serviço de blob:

   ![Intervalo de tempo e seleção de serviço na folha Editar Gráfico](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Selecione as **métricas** individuais que deseja exibir no gráfico e clique em **OK**.

   ![Seleção de métrica individual na folha Editar Gráfico](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Suas configurações de gráfico não afetam a coleta, agregação ou armazenamento de dados de monitoramento na conta de armazenamento.

### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de métricas disponíveis é alterada com base em qual serviço você escolheu na lista suspensa e qual tipo de unidade do gráfico você está editando. Por exemplo, você só poderá selecionar as métricas de percentual como *PercentNetworkError* e *PercentThrottlingError* se estiver editando um gráfico que exiba as unidades em percentual:

![Gráfico de percentual de erro de solicitação no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolução de métricas

As métricas que você selecionou em **Diagnósticos** determinam a resolução das métricas que estão disponíveis para sua conta:

* O monitoramento **Agregado** fornece métricas como percentuais de entrada/saída, disponibilidade, latência e sucesso. Essas métricas são agregadas dos serviços de blob, tabela, arquivo e fila.
* **Por API** fornece resolução mais detalhada, com métricas disponíveis para operações de armazenamento individuais, além de agregações de nível de serviço.

## <a name="configure-metrics-alerts"></a>Configurar alertas de métricas

Você pode criar alertas para notificá-lo quando os limites forem atingidos para métricas de recursos de armazenamento.

1. Para abrir a **folha de Regras de alerta**, role para baixo até a seção **MONITORAMENTO** da **folha de Menu** e selecione **Alertas (clássico)** .
2. Selecione **Adicionar alerta de métrica (clássica)** para abrir a folha **Adicionar uma regra de alerta**
3. Dê um **Nome** e uma **Descrição** para o alerta.
4. Selecione a **Métrica** para a qual você deseja adicionar um alerta, uma **Condição** de alerta e um **Limite**. O tipo de unidade de limite é alterado dependendo da métrica que você escolheu. Por exemplo, "count" é o tipo de unidade para *ContainerCount*, enquanto a unidade para a métrica *PercentNetworkError* é um percentual.
5. Selecione o **Período**. As métricas que atingiram ou excederam o limite dentro do período disparam um alerta.
6. (Opcional) Configurar notificações de **Email** e **Webhook**. Para obter mais informações sobre webhooks, confira [Configurar um webhook em um alerta de métrica do Azure](../../azure-monitor/platform/alerts-webhooks.md). Se você não configurar notificações por email ou webhook, serão exibidos alertas somente no portal do Azure.

![Folha 'Adicionar uma regra de alerta' no portal do Azure](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicionar gráficos de métricas para o painel do portal

Você pode adicionar gráficos de métricas do Armazenamento do Azure para qualquer uma de suas contas de armazenamento no painel do portal.

1. Clique para selecionar **Editar painel** ao exibir o painel no [portal do Azure](https://portal.azure.com).
1. Na **Galeria de Blocos**, selecione **Localizar blocos por** > **Tipo**.
1. Selecione **Tipo** > **Contas de armazenamento**.
1. Em **Recursos**, selecione a conta de armazenamento cujas métricas deseja adicionar ao painel.
1. Selecione **Categorias** > **Monitoramento**.
1. Arraste e solte o bloco do gráfico no painel para a métrica que você deseja exibir. Repita para todas as métricas que deseja exibir no painel. Na imagem a seguir, o gráfico "Blobs - total de solicitações" está realçado como um exemplo, mas todos os gráficos estão disponíveis para posicionamento em seu painel.

   ![Galeria de blocos no portal do Azure](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Selecione **Personalização concluída** na parte superior do painel quando terminar a adição de gráficos.

Depois de adicionar gráficos ao painel, você pode personalizá-los conforme descrito em Personalizar gráficos de métricas.

## <a name="configure-logging"></a>Configurar o registro em log

Você pode instruir o Armazenamento do Azure a salvar logs de diagnóstico para ler, gravar e excluir solicitações para os serviços de fila, blob e tabela. A política de retenção de dados que você define também se aplica a esses logs.

> [!NOTE]
> O Arquivos do Azure atualmente dá suporte às métricas de Análise de Armazenamento, mas ainda não dá suporte ao registro em log.
>

1. No [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento** e o nome da conta de armazenamento para abrir a folha de conta de armazenamento.
1. Selecione **configurações de diagnóstico (clássico)** na seção **monitoramento (clássico)** da folha do menu.

    ![Item do menu de diagnóstico em MONITORAMENTO no portal do Azure.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Verifique se **Status** está definido como **Ativado**e selecione os **Serviços** para os quais deseja habilitar os logs.

    ![Configure os logs no portal do Azure.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Clique em **Save** (Salvar).

Os logs de diagnóstico são salvos em um contêiner de blob denominado *$logs* em sua conta de armazenamento. Você pode exibir os dados de log usando um gerenciador de armazenamento como o [Gerenciador de Armazenamento da Microsoft](https://storageexplorer.com) ou de forma programática, usando a biblioteca de cliente de armazenamento ou o PowerShell.

Para obter informações sobre como acessar o contêiner de $logs, consulte [log de análise de armazenamento](storage-analytics-logging.md).

## <a name="next-steps"></a>Próximas etapas

* Encontre mais detalhes sobre [métricas, logs e cobrança](storage-analytics.md) para Análise de Armazenamento.
