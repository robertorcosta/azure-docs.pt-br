---
title: Monitor Azure para configuração de monitores de saúde de contêineres | Microsoft Docs
description: Este artigo fornece conteúdo descrevendo a configuração detalhada dos monitores de saúde no Monitor Azure para contêineres.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055711"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Monitor azure para guia de configuração do monitor de saúde de contêineres

Os monitores são o elemento principal para medir a saúde e detectar erros no Monitor Azure para contêineres. Este artigo ajuda você a entender os conceitos de como a saúde é medida e os elementos que compõem o modelo de saúde para monitorar e relatar a saúde do seu cluster Kubernetes com o recurso [Saúde (preview).](container-insights-health.md)

>[!NOTE]
>O recurso Saúde está em pré-visualização pública neste momento.
>

## <a name="monitors"></a>Monitores

Um monitor mede a integridade de alguns aspectos de um objeto gerenciado. Cada monitor tem dois ou três estados de integridade. Um monitor estará em um, e somente um, de seus possíveis estados em um determinado momento. Quando um monitor carregado pelo agente contêiner, ele é inicializado para um estado saudável. O estado só muda se forem detectadas as condições especificadas para outro estado.

A integridade geral de um determinado objeto será determinada com base na integridade de cada um de seus monitores. Essa hierarquia é ilustrada no painel Health Hierarchy no Azure Monitor para contêineres. A política de como a saúde é enrolada faz parte da configuração dos monitores agregados.

## <a name="types-of-monitors"></a>Tipos de monitores

|Monitoramento | Descrição | 
|--------|-------------|
| Monitor de unidade |Um monitor unitário mede algum aspecto de um recurso ou aplicação. Isso pode estar verificando um contador de desempenho para determinar o desempenho do recurso ou sua disponibilidade. |
|Monitor agregado | Monitores agregados agrupam múltiplos monitores para fornecer um único estado de saúde agregado à saúde. Os monitores unitários são normalmente configurados sob um monitor agregado específico. Por exemplo, um monitor agregado no nó aumenta o status da utilização da CPU do nó, a utilização da memória e o status do nó.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Política de rollup de monitor de saúde agregada

Cada monitor agregado define uma política de rollup de saúde, que é a lógica utilizada para determinar a saúde do monitor agregado com base na saúde dos monitores ele. As possíveis políticas de rollup de saúde para um monitor agregado são as seguintes:

#### <a name="worst-state-policy"></a>Pior política do Estado

O estado do monitor agregado corresponde ao estado do monitor infantil com o pior estado de saúde. Esta é a política mais comum usada pelos monitores agregados.

![Exemplo de rollup de monitor agregado pior estado](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Política percentual

O objeto de origem corresponde ao pior estado de um único membro de uma porcentagem especificada de objetos-alvo no melhor estado. Esta política é usada quando uma certa porcentagem de objetos-alvo deve ser saudável para que o objeto-alvo seja considerado saudável. A política percentual classifica os monitores em ordem decrescente de gravidade do estado, e o estado do monitor agregado é calculado como o pior estado de N% (N é ditado pelo parâmetro de configuração *StateThresholdPercentage*).

Por exemplo, suponha que existam cinco instâncias de contêiner de uma imagem de recipiente, e seus estados individuais são **Críticos**, **Aviso**, **Saudável**, **Saudável**, **Saudável**.  O status do monitor de utilização da CPU do contêiner será **crítico,** já que o pior estado de 90% dos recipientes é **Crítico** quando classificado em ordem decrescente de gravidade.

## <a name="understand-the-monitoring-configuration"></a>Entenda a configuração de monitoramento

O Monitor Azure para contêineres inclui uma série de cenários de monitoramento importantes que são configurados da seguinte forma.

### <a name="unit-monitors"></a>Monitores unitários

|**Nome do monitor** | Tipo de monitoração | **Descrição** | **Parâmetro** | **Valor** |
|-----------------|--------------|-----------------|---------------|-----------|
|Utilização da memória do nó |Monitor de unidade |Este monitor avalia a utilização da memória de um nó a cada minuto, usando os dados relatados pelo cadvisor. |Amostras consecutivasparatransição do Estado<br> FalhaemifGreaterThanPercentage<br> WarnifMaiorthanPercentagePercentage | 3<br> 90<br> 80  ||
|Utilização da CPU do nó |Monitor de Unidade |Este monitor verifica a utilização da CPU do nó a cada minuto, usando os dados relatados pelo cadvisor. | Amostras consecutivasparatransição do Estado<br> FalhaemifGreaterThanPercentage<br> WarnifMaiorthanPercentagePercentage | 3<br> 90<br> 80  ||
|Status do nó |Monitor de unidade |Este monitor verifica as condições do nó relatadas pelos Kubernetes.<br> Atualmente, as seguintes condições do nó são verificadas: Pressão de disco, pressão de memória, pressão PID, fora do disco, rede indisponível, status pronto para o nó.<br> Fora das condições acima, se *não houver* **true**disco ou *rede indisponível,* o monitor muda para **estado crítico.**<br> Se quaisquer outras condições forem **verdadeiras,** além de um status **Pronto,** o monitor será alterado para um estado **de aviso.** | NodeConditionTypeForFailedState | outofdisk,redeindisponível ||
|Utilização da memória do contêiner |Monitor de unidade |Este monitor relata o estado de saúde combinado da utilização da memória (RSS) das instâncias do recipiente.<br> Ele realiza uma comparação simples que compara cada amostra a um único limiar e especificado pelo parâmetro de configuração **ConsecutivoSamplesForStateTransition**.<br> Seu estado é calculado como o pior estado de 90% das instâncias do contêiner (StateThresholdPercentage), classificado em ordem decrescente de gravidade do estado de saúde do contêiner (ou seja, Crítico, Aviso, Saudável).<br> Se nenhum registro for recebido de uma ocorrência de contêiner, então o estado de saúde da ocorrência do contêiner é relatado como **Desconhecido**, e tem maior precedência na ordem de classificação sobre o estado **Crítico.**<br> O estado de cada instância de contêiner é calculado usando os limiares especificados na configuração. Se o uso estiver acima do limite crítico (90%), então a instância está em um estado **crítico,** se for menor que o limiar crítico (90%) mas maior do que o limiar de aviso (80%), então a instância está em um estado **de aviso.** Caso contrário, está em estado **saudável.** |Amostras consecutivasparatransição do Estado<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnifMaiorthanPercentagePercentage| 3<br> 90<br> 90<br> 80 ||
|Utilização da CPU do contêiner |Monitor de unidade |Este monitor relata o estado de saúde combinado da utilização da CPU das instâncias do recipiente.<br> Ele realiza uma comparação simples que compara cada amostra a um único limiar e especificado pelo parâmetro de configuração **ConsecutivoSamplesForStateTransition**.<br> Seu estado é calculado como o pior estado de 90% das instâncias do contêiner (StateThresholdPercentage), classificado em ordem decrescente de gravidade do estado de saúde do contêiner (ou seja, Crítico, Aviso, Saudável).<br> Se nenhum registro for recebido de uma ocorrência de contêiner, então o estado de saúde da ocorrência do contêiner é relatado como **Desconhecido**, e tem maior precedência na ordem de classificação sobre o estado **Crítico.**<br> O estado de cada instância de contêiner é calculado usando os limiares especificados na configuração. Se o uso estiver acima do limite crítico (90%), então a instância está em um estado **crítico,** se for menor que o limiar crítico (90%) mas maior do que o limiar de aviso (80%), então a instância está em um estado **de aviso.** Caso contrário, está em estado **saudável.** |Amostras consecutivasparatransição do Estado<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnifMaiorthanPercentagePercentage| 3<br> 90<br> 90<br> 80 ||
|Pods de carga de trabalho do sistema prontos |Monitor de unidade |Este monitor relata o status com base na porcentagem de pods em estado pronto em uma determinada carga de trabalho. Seu estado é definido como **Crítico** se menos de 100% das cápsulas estiverem em um estado **saudável** |Amostras consecutivasparatransição do Estado<br> FailIfLessThanPercentage |2<br> 100 ||
|Status da API kube |Monitor de unidade |Este monitor informa o status do serviço de API Kube. O monitor está em estado crítico caso o ponto final da API kube não esteja disponível. Para este monitor em particular, o estado é determinado fazendo uma consulta ao ponto final 'nós' para o servidor kube-api. Qualquer coisa que não seja um código de resposta OK altera o monitor para um estado **crítico.** | Sem propriedades de configuração |||

### <a name="aggregate-monitors"></a>Monitores agregados

|**Nome do monitor** | **Descrição** | **Algoritmo** |
|-----------------|-----------------|---------------|
|Nó |Este monitor é um agregado de todos os monitores de nó. Corresponde ao estado do monitor infantil com o pior estado de saúde:<br> Utilização da CPU do nó<br> Utilização da memória do nó<br> Status do nó | Pior de|
|Piscina de nó |Este monitor relata o estado de saúde combinado de todos os nós na piscina de *nó*. Trata-se de um monitor de três estados, cujo estado se baseia no pior estado de 80% dos nódulos na piscina de nó, classificados em ordem descendente de gravidade dos estados de nó (isto é, Crítico, Aviso, Saudável).|Porcentagem |
|Nódulos (pai da piscina de node) |Este é um monitor agregado de todas as piscinas de nós. Seu estado baseia-se no pior estado de seus monitores infantis (ou seja, as piscinas de nó presentes no cluster). |Pior de |
|Cluster (pai de nódulos/<br> Infraestrutura kubernetes) |Este é o monitor dos pais que combina o estado do monitor infantil com o pior estado de saúde, ou seja, a infraestrutura e os nódulos kubernetes. |Pior de |
|Infraestrutura kubernetes |Este monitor relata o estado de saúde combinado dos componentes de infra-estrutura gerenciada do cluster. seu status é calculado como o "pior dos" estados do monitor de crianças, ou seja, cargas de trabalho do sistema kube e status do Servidor API. |Pior de|
|Carga de trabalho do sistema |Este monitor relata o estado de saúde de uma carga de trabalho do sistema kube. Este monitor corresponde ao estado do monitor infantil com o pior estado de saúde, ou seja, os **Pods em estado pronto** (monitor e os recipientes na carga de trabalho). |Pior de |
|Contêiner |Este monitor relata o estado geral de saúde de um contêiner em uma determinada carga de trabalho. Este monitor corresponde ao estado do monitor infantil com o pior estado de saúde, ou seja, os monitores **de utilização** da CPU e **memória.** |Pior de |

## <a name="next-steps"></a>Próximas etapas

Veja [a saúde do monitor de cluster](container-insights-health.md) sustais para saber como ver o estado de saúde do seu cluster Kubernetes.
