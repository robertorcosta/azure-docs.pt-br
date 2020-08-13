---
title: Dimensionar automaticamente os clusters do Azure HDInsight
description: Usar o recurso de autoescala do Azure HDInsight para Apache Hadoop automaticamente os clusters de escala
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: cc294eb1bdfd4a6a8c6ad001c007f83a10983644
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185801"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Dimensionar automaticamente os clusters do Azure HDInsight

O recurso de dimensionamento automático gratuito do Azure HDInsight pode aumentar ou diminuir automaticamente o número de nós de trabalho no cluster com base nos critérios definidos anteriormente. Você define um número mínimo e máximo de nós durante a criação do cluster, estabelece os critérios de dimensionamento usando uma agenda de dia útil ou métricas de desempenho específicas, e a plataforma HDInsight faz o resto.

## <a name="how-it-works"></a>Como ele funciona

O recurso de dimensionamento automático usa dois tipos de condições para disparar eventos de dimensionamento: limites para várias métricas de desempenho de cluster (chamadas *de dimensionamento baseado em carga*) e gatilhos baseados em tempo (chamados *de dimensionamento baseado em agenda*). O dimensionamento baseado em carga altera o número de nós no cluster, dentro de um intervalo definido, para garantir o uso ideal da CPU e minimizar o custo de execução. O dimensionamento baseado em agendamento altera o número de nós no cluster com base nas operações que você associa a datas e horários específicos.

O vídeo a seguir fornece uma visão geral dos desafios que o dimensionamento automático resolve e como ele pode ajudá-lo a controlar os custos com o HDInsight.


> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Escolhendo o dimensionamento baseado em carga ou em agendamento

Considere os seguintes fatores ao escolher um tipo de dimensionamento:

* Variação de carga: a carga do cluster segue um padrão consistente em horários específicos, em dias específicos? Caso contrário, o agendamento baseado em carga é uma opção melhor.
* Requisitos de SLA: o dimensionamento automático é reativo em vez de preditiva. Haverá um atraso suficiente entre o momento em que a carga começa a aumentar e quando o cluster precisa estar em seu tamanho de destino? Se houver requisitos estritos de SLA e a carga for um padrão conhecido fixo, a ' agenda baseada em ' será uma opção melhor.

### <a name="cluster-metrics"></a>Métricas de cluster

O dimensionamento automático monitora o cluster continuamente e coleta as seguintes métricas:

|Métrica|Descrição|
|---|---|
|Total de CPU pendente|o número total de núcleos necessários para iniciar a execução de todos os contêineres pendentes.|
|Total de memória pendente|o total de memória (em MB) necessária para iniciar a execução de todos os contêineres pendentes.|
|Total de CPU livre|a soma de todos os núcleos não utilizados em nós de trabalho ativos.|
|Total de memória livre|A soma de memória não usada (em MB) em nós de trabalho ativos.|
|Memória usada por nó|A carga em um nó de trabalho. Um nó de trabalho no qual 10 GB de memória são usados é considerado como estando sob uma carga maior que um nó de trabalho com 2 GB de memória usada.|
|Número de mestres de aplicativo por nó|O número de contêineres de aplicativo mestre (AM) em execução em um nó de trabalho. Um nó de trabalho que está hospedando dois contêineres AM é considerado mais importante do que um nó de trabalho que está hospedando zero contêineres AM.|

As métricas acima são verificadas a cada 60 segundos. Você pode configurar operações de dimensionamento para o cluster usando qualquer uma dessas métricas.

### <a name="load-based-scale-conditions"></a>Condições de escala baseadas em carga

Quando as seguintes condições forem detectadas, o Dimensionamento automático emitirá uma solicitação de dimensionamento:

|Escalar verticalmente|Reduzir verticalmente|
|---|---|
|A CPU pendente total é maior que a CPU total livre por mais de 3 minutos.|O total de CPU pendente é menor que o total de CPU livre por mais de 10 minutos.|
|A memória total pendente é maior que a memória livre total por mais de 3 minutos.|O total de memória pendente é menor que o total de memória livre por mais de 10 minutos.|

Para expansão, o dimensionamento automático emite uma solicitação de expansão para adicionar o número necessário de nós. A expansão se baseia em quantos novos nós de trabalho são necessários para atender aos requisitos atuais de CPU e memória.

Para reduzir verticalmente, o dimensionamento automático emite uma solicitação para remover um determinado número de nós. A redução vertical é baseada no número de contêineres AM por nó. E os requisitos atuais de CPU e memória. O serviço também detecta quais nós são candidatos para remoção com base na execução do trabalho atual. A operação de redução encerra primeiro os nós e, em seguida, remove-os do cluster.

### <a name="cluster-compatibility"></a>Compatibilidade de cluster

> [!Important]
> O recurso de dimensionamento automático do Azure HDInsight foi lançado para disponibilidade geral em 7 de novembro de 2019, para clusters Spark e Hadoop, e incluía aprimoramentos não estão disponíveis na versão prévia do recurso. Se você criou um cluster Spark antes de 7 de novembro de 2019 e deseja usar o recurso de dimensionamento automático em seu cluster, o caminho recomendado é criar um novo cluster e habilitar o dimensionamento automático no novo cluster.
>
> O dimensionamento automático para clusters Interactive Query (LLAP) e HBase ainda está na versão prévia. O dimensionamento automático só está disponível em clusters Spark, Hadoop, Interactive Query e HBase.

A tabela a seguir descreve os tipos de cluster e as versões que são compatíveis com o recurso de dimensionamento automático.

| Versão | Spark | Hive | LLAP | O HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 sem ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 4,0 sem ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 3,6 com ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 4,0 com ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |

\*Os clusters HBase só podem ser configurados para dimensionamento baseado em agendamento, não baseado em carga.

## <a name="get-started"></a>Introdução

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Criar um cluster com dimensionamento automático baseado em carga

Para habilitar o recurso de dimensionamento automático com dimensionamento baseado em carga, conclua as seguintes etapas como parte do processo normal de criação do cluster:

1. Na guia **configuração + preços** , marque a caixa de seleção **habilitar autoescala** .
1. Selecione **baseado em carga** em **tipo de dimensionamento automático**.
1. Insira os valores pretendidos para as seguintes propriedades:  

    * **Número inicial de nós** para o **nó de trabalho**.
    * Número **mínimo** de nós de trabalho.
    * Número **máximo** de nós de trabalho.

    ![Habilitar dimensionamento automático baseado em carga de nó de trabalho](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

O número inicial de nós de trabalho deve estar entre o mínimo e máximo, inclusive. Esse valor define o tamanho inicial do cluster quando ele é criado. O número mínimo de nós de trabalho deve ser definido como três ou mais. Dimensionar o cluster para menos de três nós pode fazer com que ele fique preso no modo de segurança devido à replicação de arquivo insuficiente.  Para obter mais informações, consulte [ficando preso no modo de segurança](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Criar um cluster com dimensionamento automático baseado em agendamento

Para habilitar o recurso de dimensionamento automático com dimensionamento baseado em agendamento, conclua as seguintes etapas como parte do processo normal de criação do cluster:

1. Na guia **configuração + preços** , marque a caixa de seleção **Habilitar dimensionamento automático** .
1. Insira o **número de nós** para o **nó de trabalho**, que controla o limite para escalar verticalmente o cluster.
1. Selecione a opção **baseado em agendamento** em **tipo de dimensionamento automático**.
1. Selecione **Configurar** para abrir a janela **configuração de dimensionamento automático** .
1. Selecione seu fuso horário e clique em **+ Adicionar condição**
1. Selecione os dias da semana aos quais a nova condição deve ser aplicada.
1. Edite a hora em que a condição deve entrar em vigor e o número de nós para o qual o cluster deve ser dimensionado.
1. Adicione mais condições, se necessário.

    ![Habilitar a criação baseada em agendamento de nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

O número de nós deve estar entre 3 e o número máximo de nós de trabalho que você inseriu antes de adicionar condições.

### <a name="final-creation-steps"></a>Etapas de criação final

Selecione o tipo de VM para nós de trabalho selecionando uma VM na lista suspensa em **tamanho do nó**. Depois de escolher o tipo de VM para cada tipo de nó, você poderá ver o intervalo de custo estimado para todo o cluster. Ajuste os tipos de VM para se ajustarem ao seu orçamento.

![Habilitar o tamanho do nó de dimensionamento automático baseado na agenda do nó de trabalho](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Sua assinatura tem uma cota de capacidade para cada região. O número total de núcleos de seus nós de cabeçalho e o máximo de nós de trabalho não podem exceder a cota de capacidade. No entanto, essa cota é um limite flexível; você sempre pode criar um tíquete de suporte para aumentá-lo facilmente.

> [!Note]  
> Se você exceder o limite de cota de núcleo total, receberá uma mensagem de erro informando ' o nó máximo excedeu os núcleos disponíveis nesta região, escolha outra região ou entre em contato com o suporte para aumentar a cota. '

Para obter mais informações sobre a criação de clusters HDInsight usando o portal do Azure, confira [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo do Resource Manager

#### <a name="load-based-autoscaling"></a>Dimensionamento automático baseado em carga

Você pode criar um cluster HDInsight com o dimensionamento automático baseado em carga um modelo de Azure Resource Manager, adicionando um `autoscale` nó à `computeProfile`  >  `workernode` seção com as propriedades `minInstanceCount` e `maxInstanceCount` conforme mostrado no trecho de JSON abaixo. Para obter um modelo completo do Resource Manager, consulte [modelo de início rápido: implantar cluster Spark com dimensionamento automático baseado em loadhabilitado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

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

#### <a name="schedule-based-autoscaling"></a>Dimensionamento automático baseado em agendamento

Você pode criar um cluster HDInsight com dimensionamento automático baseado em agendamento um modelo de Azure Resource Manager, adicionando um `autoscale` nó à `computeProfile`  >  `workernode` seção. O `autoscale` nó contém um `recurrence` que tem um `timezone` e `schedule` que descreve quando ocorre a alteração. Para obter um modelo completo do Resource Manager, consulte [implantar cluster Spark com dimensionamento automático baseado em agendamento habilitado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

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
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Habilitar e desabilitar o dimensionamento automático para um cluster em execução

#### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para habilitar o dimensionamento automático em um cluster em execução, selecione **tamanho do cluster** em **configurações**. Em seguida, selecione **Habilitar dimensionamento automático**. Selecione o tipo de dimensionamento automático desejado e insira as opções para o dimensionamento baseado em carga ou em agendamento. Por fim, selecione **Salvar**.

![Habilitar dimensionamento automático baseado em agenda de nó de trabalho executando cluster](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Usando a API REST

Para habilitar ou desabilitar o dimensionamento automático em um cluster em execução usando a API REST, faça uma solicitação POST para o ponto de extremidade de dimensionamento automático:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Use os parâmetros apropriados na carga de solicitação. A carga JSON abaixo pode ser usada para habilitar o dimensionamento automático. Use a carga `{autoscale: null}` para desabilitar o dimensionamento automático.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Consulte a seção anterior sobre como [habilitar o dimensionamento automático baseado em carga](#load-based-autoscaling) para obter uma descrição completa de todos os parâmetros de carga.

## <a name="monitoring-autoscale-activities"></a>Monitorando atividades de dimensionamento automático

### <a name="cluster-status"></a>Status do cluster

O status do cluster listado no portal do Azure pode ajudá-lo a monitorar as atividades de dimensionamento automático.

![Habilitar o status do cluster de dimensionamento automático baseado em carga do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todas as mensagens de status do cluster que você pode ver são explicadas na lista abaixo.

| Status do cluster | Descrição |
|---|---|
| Executando | O cluster está funcionando normalmente. Todas as atividades de dimensionamento automático anteriores foram concluídas com êxito. |
| Atualizar  | A configuração de autoescala do cluster está sendo atualizada.  |
| Configuração do HDInsight  | Uma operação de expansão ou redução do cluster está em andamento.  |
| Erro de atualização  | O HDInsight atendeu a problemas durante a atualização de configuração de dimensionamento automático. Os clientes podem optar por repetir a atualização ou desabilitar o dimensionamento automático.  |
| Erro  | Algo está errado com o cluster e não é utilizável. Exclua este cluster e crie um novo.  |

Para exibir o número atual de nós no cluster, acesse o gráfico de **tamanho do cluster** na página **visão geral** do cluster. Ou selecione o **tamanho do cluster** em **configurações**.

### <a name="operation-history"></a>Histórico de operação

Você pode exibir o histórico de expansão e redução do cluster como parte das métricas do cluster. Você também pode listar todas as ações de dimensionamento no dia anterior, semana ou outro período de tempo.

Selecione **métricas** em **monitoramento**. Em seguida, selecione **Adicionar métrica** e **número de trabalhadores ativos** na caixa suspensa de **métrica** . Selecione o botão no canto superior direito para alterar o intervalo de tempo.

![Habilitar métrica de dimensionamento automático baseado em agenda de nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="other-considerations"></a>Outras considerações

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considerar a latência de operações de aumento ou redução

Pode levar de 10 a 20 minutos para que uma operação de dimensionamento seja concluída. Ao configurar um agendamento personalizado, planeje esse atraso. Por exemplo, se você precisar que o tamanho do cluster seja 20 às 9:00 AM, defina o gatilho de agendamento como um tempo anterior, como 8:30, para que a operação de dimensionamento seja concluída pela 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Preparação para reduzir

Durante o processo de redução do dimensionamento do cluster, o dimensionamento automático encerrará os nós para atender ao tamanho do destino. Se as tarefas estiverem em execução nesses nós, o dimensionamento automático aguardará até que as tarefas sejam concluídas. Como cada nó de trabalho também serve uma função no HDFS, os dados temporários serão deslocados para os nós restantes. Portanto, você deve verificar se há espaço suficiente nos nós restantes para hospedar todos os dados temporários.

Os trabalhos em execução continuarão. Os trabalhos pendentes aguardarão o agendamento com menos nós de trabalho disponíveis.

### <a name="minimum-cluster-size"></a>Tamanho mínimo do cluster

Não dimensione o cluster para menos de três nós. Dimensionar o cluster para menos de três nós pode fazer com que ele fique preso no modo de segurança devido à replicação de arquivo insuficiente.  Para obter mais informações, consulte [ficando preso no modo de segurança](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="next-steps"></a>Próximas etapas

Leia sobre as diretrizes para dimensionar clusters manualmente em [diretrizes de dimensionamento](hdinsight-scaling-best-practices.md)
