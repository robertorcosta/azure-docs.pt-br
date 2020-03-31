---
title: Dimensionar automaticamente os clusters do Azure HDInsight
description: Use o recurso Azure HDInsight Autoscale para automaticamente clusters de escala Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 68bc30d08d95fe8e3d20a8ecb7af6c9710951921
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399719"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Dimensionar automaticamente os clusters do Azure HDInsight

> [!Important]
> O recurso Azure HDInsight Autoscale foi lançado para disponibilidade geral em 7 de novembro de 2019 para clusters Spark e Hadoop e incluiu melhorias não disponíveis na versão de pré-visualização do recurso. Se você criou um cluster Spark antes de 7 de novembro de 2019 e deseja usar o recurso Autoscale em seu cluster, o caminho recomendado é criar um novo cluster e habilitar a Escala Automática no novo cluster.
>
> A escala automática para os clusters DE Consulta Interativa (LLAP) e HBase ainda está em pré-visualização. A escala automática só está disponível nos clusters Spark, Hadoop, Interactive Query e HBase.

O recurso de escala automática do Cluster Azure HDInsight dimensiona automaticamente o número de nódulos do trabalhador em um cluster para cima e para baixo. Outros tipos de nódulos no cluster não podem ser dimensionados atualmente.  Durante a criação de um novo cluster HDInsight, um número mínimo e um número máximo de nós de trabalho podem ser definidos. Em seguida, a escala automática monitora os requisitos de recursos da carga de análise e dimensiona o número de nomes de trabalhadores para cima ou para baixo. Não há nenhuma taxa adicional para este recurso.

## <a name="cluster-compatibility"></a>Compatibilidade de cluster

A tabela a seguir descreve os tipos de cluster e as versões compatíveis com o recurso Autoscale.

| Versão | Spark | Hive | II | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 sem ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 4.0 sem ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 3.6 com ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 4.0 com ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |

\*Os clusters HBase só podem ser configurados para dimensionamento baseado em cronograma, não baseado em carga.

## <a name="how-it-works"></a>Como ele funciona

Você pode escolher dimensionamento baseado em carga ou escala ção por cronograma para o cluster HDInsight. O dimensionamento baseado em carga altera o número de nós em seu cluster, dentro de um intervalo definido, para garantir a utilização ideal da CPU e minimizar o custo de execução.

O dimensionamento baseado em cronograma altera o número de nódulos em seu cluster com base em condições que fazem efeito em momentos específicos. Essas condições dimensionam o cluster para um número desejado de nódulos.

### <a name="metrics-monitoring"></a>Monitoramento de métricas

O dimensionamento automático monitora o cluster continuamente e coleta as seguintes métricas:

|Métrica|Descrição|
|---|---|
|CPU total pendente|o número total de núcleos necessários para iniciar a execução de todos os contêineres pendentes.|
|Memória total pendente|o total de memória (em MB) necessária para iniciar a execução de todos os contêineres pendentes.|
|CPU total grátis|a soma de todos os núcleos não utilizados em nós de trabalho ativos.|
|Memória total livre|A soma de memória não usada (em MB) em nós de trabalho ativos.|
|Memória usada por nó|A carga em um nó de trabalho. Um nó de trabalho no qual 10 GB de memória são usados é considerado como estando sob uma carga maior que um nó de trabalho com 2 GB de memória usada.|
|Número de mestres de aplicação por nó|O número de contêineres de aplicativo mestre (AM) em execução em um nó de trabalho. Um nó de trabalhador que está hospedando dois contêineres AM, é considerado mais importante do que um nó de trabalhador que está hospedando contêineres am zero.|

As métricas acima são verificadas a cada 60 segundos. A autoscale toma decisões de scale-up e scale-down com base nessas métricas.

### <a name="load-based-scale-conditions"></a>Condições de escala baseadas em carga

Quando as seguintes condições forem detectadas, a Autoscale emitirá uma solicitação de escala:

|Escalar verticalmente|Redimensionado|
|---|---|
|O total de CPU pendente é maior do que a CPU total gratuita por mais de 3 minutos.|O total de CPU pendente é menor que o total de CPU livre por mais de 10 minutos.|
|A memória total pendente é maior do que a memória total livre por mais de 3 minutos.|O total de memória pendente é menor que o total de memória livre por mais de 10 minutos.|

Para aumentar a escala, o serviço HDInsight calcula quantos novos nós de trabalhadores são necessários para atender aos requisitos atuais de CPU e memória e, em seguida, emite uma solicitação de scale-up para adicionar o número de nós necessários.

Para reduzir a escala, com base no número de recipientes AM por nó e nos requisitos atuais de CPU e memória, a Escala automática emite uma solicitação para remover um determinado número de nomes. O serviço também detecta quais nós são candidatos à remoção com base na execução atual do trabalho. A operação de remoção de escala primeiro desativa os nódulos e, em seguida, remove-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Crie um cluster com autodimensionamento baseado em carga

Para habilitar o recurso Autoscale com dimensionamento baseado em carga, complete as seguintes etapas como parte do processo normal de criação de clusters:

1. Na guia **Configuração + preços,** selecione a caixa de seleção **Ativar em escala automática.**
1. Selecione **'Carga', baseada em** carga, em **tipo autoescala .**
1. Digite os valores desejados para as seguintes propriedades:  

    * Número inicial **de nódulos** para **nó trabalhador**.
    * **Número min** de nódulos operários.
    * **Número máximo** de nódulos operários.

    ![Habilitar a escala automática baseada em carga do nó do trabalhador](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

O número inicial de nós de trabalho deve estar entre o mínimo e máximo, inclusive. Este valor define o tamanho inicial do cluster quando ele é criado. O número mínimo de nós trabalhadores deve ser definido para três ou mais. Dimensionar seu cluster para menos de três nós pode resultar em ficar preso no modo de segurança devido à replicação insuficiente do arquivo.  Para obter mais informações, consulte [Ficar preso no modo de segurança](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Crie um cluster com autodimensionamento baseado em cronograma

Para habilitar o recurso Autoscale com dimensionamento baseado em cronograma, complete as seguintes etapas como parte do processo normal de criação de clusters:

1. Na guia **Configuração + preços,** verifique a caixa de seleção **Ativar em escala automática.**
1. Digite o **número de nós** para **nó Worker**, que controla o limite para dimensionamento do cluster.
1. Selecione a opção **Agendado no** **tipo Autoscale**.
1. Selecione **Configurar** para abrir a janela **de configuração de escala** automática.
1. Selecione seu fuso horário e clique **em + Adicionar condição**
1. Selecione os dias da semana a que a nova condição deve se aplicar.
1. Edite a hora em que a condição deve entrar em vigor e o número de nós para os os que o cluster deve ser dimensionado.
1. Adicione mais condições, se necessário.

    ![Habilite a criação baseada no cronograma do nó do trabalhador](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

O número de nós deve ser entre 3 e o número máximo de nós de trabalhadores que você entrou antes de adicionar condições.

### <a name="final-creation-steps"></a>Etapas finais de criação

Para dimensionamento baseado em carga e cronograma, selecione o tipo VM para nós do trabalhador, selecionando uma VM na lista de baixa em **tamanho de nó**. Depois de escolher o tipo de VM para cada tipo de nó, você pode ver a faixa de custo estimada para todo o cluster. Ajuste os tipos de VM para se adequar ao seu orçamento.

![Habilite o tamanho do nó do nó do trabalhador baseado em cronograma](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Sua assinatura tem uma cota de capacidade para cada região. O número total de núcleos de seus nós de cabeça combinados com o número máximo de nós de trabalhadores não pode exceder a cota de capacidade. No entanto, essa cota é um limite flexível; você sempre pode criar um tíquete de suporte para aumentá-lo facilmente.

> [!Note]  
> Se você exceder o limite total de cota principal, você receberá uma mensagem de erro dizendo 'o nó máximo excedeu os núcleos disponíveis nesta região, escolha outra região ou entre em contato com o suporte para aumentar a cota'.

Para obter mais informações sobre a criação de clusters HDInsight usando o portal do Azure, confira [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo do Resource Manager

#### <a name="load-based-autoscaling"></a>Autoscaling baseado em carga

Você pode criar um cluster HDInsight com autodimensionamento automático baseado em `autoscale` carga um `computeProfile`  >  `workernode` modelo do `minInstanceCount` `maxInstanceCount` Azure Resource Manager, adicionando um nó à seção com as propriedades e como mostrado no trecho de json abaixo.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Para obter mais informações sobre como criar cluster com modelos do Resource Manager, confira [Criar clusters do Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Autoscaling baseado em cronograma

Você pode criar um cluster HDInsight com um modelo de Gerenciador `autoscale` de recursos `computeProfile`  >  `workernode` baseado em programação, adicionando um nó à seção. O `autoscale` nó contém `recurrence` um `timezone` que `schedule` tem um e que descreve quando a mudança ocorrerá.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Habilitar e desabilitar o dimensionamento automático para um cluster em execução

#### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para habilitar a escala automática em um cluster em execução, selecione **tamanho de cluster** em **Configurações**. Em seguida, **selecione Ativar escala automática**. Selecione o tipo de escala automática desejada e digite as opções de escala baseada em carga ou com base em agendamento. Por fim, selecione **Salvar**.

![Habilitar cluster de execução de escala automática baseada em cronograma do nó do trabalhador](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Usando a API REST

Para ativar ou desativar a escala automática em um cluster em execução usando a API REST, faça uma solicitação POST para o ponto final da escala automática, conforme mostrado no trecho de código abaixo:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Use os parâmetros apropriados na carga útil da solicitação. A carga json abaixo poderia ser usada para ativar a escala automática. Use a `{autoscale: null}` carga para desativar a escala automática.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Consulte a seção anterior sobre [a habilitação da escala automática baseada em carga](#load-based-autoscaling) para obter uma descrição completa de todos os parâmetros de carga.

## <a name="best-practices"></a>Práticas recomendadas

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Escolhendo dimensionamento baseado em carga ou com cronograma

Considere os seguintes fatores antes de tomar uma decisão sobre qual modo escolher:

* Habilite a escala automática durante a criação de clusters.
* O número mínimo de nódulos deve ser de pelo menos três.
* Variância de carga: a carga do cluster segue um padrão consistente em momentos específicos, em dias específicos. Se não, o agendamento baseado em carga é uma opção melhor.
* Requisitos de SLA: O dimensionamento em escala automática é reativo em vez de preditivo. Haverá um atraso suficiente entre quando a carga começar a aumentar e quando o cluster precisa estar no seu tamanho de destino? Se houver requisitos rígidos de SLA e a carga for um padrão fixo conhecido, 'baseado em horários' é uma opção melhor.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considere a latência de escalar ou reduzir as operações

Pode levar de 10 a 20 minutos para uma operação de escala ser concluída. Ao configurar um cronograma personalizado, planeje esse atraso. Por exemplo, se você precisar que o tamanho do cluster seja de 20 às 9:00 AM, defina o gatilho de horário para um horário anterior, como 8:30 AM, de modo que a operação de dimensionamento tenha sido concluída às 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Preparação para reduzir

Durante o processo de redução do cluster, a Autoscale desativará os nós para atender ao tamanho do destino. Se houver tarefas em execução nesses nós, a Autoscale esperará até que as tarefas sejam concluídas. Uma vez que cada nó do trabalhador também serve um papel no HDFS, os dados temporários serão transferidos para os demais. Então você deve ter certeza de que há espaço suficiente nos nós restantes para hospedar todos os dados temporários.

Os trabalhos de execução continuarão a funcionar e terminar. Os trabalhos pendentes serão agendados normalmente com menos nódulos disponíveis.

### <a name="minimum-cluster-size"></a>Tamanho mínimo do cluster

Não dimensione seu cluster para menos de três nós. Dimensionar seu cluster para menos de três nós pode resultar em ficar preso no modo de segurança devido à replicação insuficiente do arquivo.  Para obter mais informações, consulte [Ficar preso no modo de segurança](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="monitoring"></a>Monitoramento

### <a name="cluster-status"></a>Status do cluster

O status do cluster listado no portal Azure pode ajudá-lo a monitorar as atividades da Escala Automática.

![Habilitar o status de cluster de escala automática baseada em nó do trabalhador](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todas as mensagens de status do cluster que você pode ver são explicadas na lista abaixo.

| Status do cluster | Descrição |
|---|---|
| Executando | O cluster está funcionando normalmente. Todas as atividades anteriores da Autoscale foram concluídas com sucesso. |
| Atualizando  | A configuração de escala automática do cluster está sendo atualizada.  |
| Configuração HDInsight  | Uma operação de escala de cluster para cima ou para baixo está em andamento.  |
| Erro de atualização  | O HDInsight encontrou problemas durante a atualização de configuração da escala automática. Os clientes podem optar por tentar novamente a atualização ou desativar a escala automática.  |
| Erro  | Algo está errado com o cluster, e não é utilizável. Exclua este cluster e crie um novo.  |

Para exibir o número atual de números de nó sumido em seu cluster, vá para o gráfico **de tamanho de cluster** na página Visão **geral** para o cluster ou selecione Tamanho **de Cluster** em **Configurações**.

### <a name="operation-history"></a>Histórico da operação

Você pode visualizar o histórico de escalae escala de cluster como parte das métricas do cluster. Você também pode listar todas as ações de dimensionamento ao longo do último dia, semana ou outro período de tempo.

Selecione **Métricas** em **Monitoramento**. Em seguida, **selecione Adicionar métrica** e **número de trabalhadores ativos** na caixa de eda **métrica.** Selecione o botão no canto superior direito para alterar o intervalo de tempo.

![Habilite a métrica de escala automática baseada em cronograma do nó do trabalhador](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Próximas etapas

Leia sobre as práticas recomendadas para dimensionar os clusters manualmente em [Práticas recomendadas de dimensionamento](hdinsight-scaling-best-practices.md)
