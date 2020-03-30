---
title: Gerenciar o dimensionamento horizontal do cluster (dimensionamento) para corresponder à demanda no Azure Data Explorer
description: Este artigo descreve etapas para dimensionar e dimensionar em um cluster Azure Data Explorer com base na mudança de demanda.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664126"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gerencie o dimensionamento horizontal do cluster (escala de saída) no Azure Data Explorer para acomodar a demanda em mudança

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Um tamanho de cluster estático pode levar a subutilização ou superutilização, e nenhuma dessas condições é a ideal. Como a demanda em um cluster não pode ser prevista com precisão absoluta, é melhor *escalar* um cluster, adicionando e removendo recursos de capacidade e CPU com a mudança de demanda. 

Existem dois fluxos de trabalho para dimensionar um cluster Azure Data Explorer: 
* Dimensionamento horizontal, também chamado de escala para dentro e para fora.
* [Dimensionamento vertical,](manage-cluster-vertical-scaling.md)também chamado de escala para cima e para baixo.
Este artigo explica o fluxo de trabalho de dimensionamento horizontal.

## <a name="configure-horizontal-scaling"></a>Configurar dimensionamento horizontal

Usando o dimensionamento horizontal, você pode dimensionar a contagem de instâncias automaticamente, com base em regras e horários predefinidos. Para especificar as configurações de escala automática para o cluster:

1. No portal Azure, acesse o recurso de cluster do Azure Data Explorer. Em **Configurações,** selecione **Escala .** 

2. Na janela **Escala para fora,** selecione o método de escala automática que você deseja: **escala manual,** **escala automática otimizada**ou **escala automática personalizada**.

### <a name="manual-scale"></a>Dimensionamento manual

Escala manual é a configuração padrão durante a criação de clusters. O cluster tem uma capacidade estática que não muda automaticamente. Você seleciona a capacidade estática usando a **barra de contagem de** instâncias. O dimensionamento do cluster permanece nessa configuração até que você faça outra alteração.

   ![Método de escala manual](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Escala automática otimizada (visualização)

A escala automática otimizada é o método de escala automática recomendado. Este método otimiza o desempenho e os custos do cluster. Se o cluster se aproximar de um estado de subutilização, ele será dimensionado. Esta ação reduz os custos, mas mantém o nível de desempenho. Se o cluster se aproximar de um estado de superutilização, ele será dimensionado para manter o desempenho ideal. Para configurar a escala automática otimizada:

1. Selecione **escala automática otimizada**. 

1. Selecione uma contagem mínima de instâncias e uma contagem máxima de instâncias. O cluster de dimensionamento automático varia entre esses dois números, com base na carga.

1. Selecione **Salvar**.

   ![Método de escala automática otimizado](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

A escala automática otimizada começa a funcionar. Suas ações agora são visíveis no registro de atividades do Azure do cluster.

#### <a name="logic-of-optimized-autoscale"></a>Lógica de autoescala otimizada 

**Escala**

Quando o cluster se aproximar de um estado de superutilização, dimensione para manter o desempenho ideal. A escala ocorrerá quando:
* O número de instâncias de cluster está abaixo do número máximo de instâncias definidas pelo usuário.
* A utilização do cache é alta por mais de uma hora.
* A CPU está alta por mais de uma hora.
* A utilização da ingestão é alta por mais de uma hora.

> [!NOTE]
> A lógica de saída de escala não considera atualmente a métrica de utilização da ingestão. Se essa métrica for importante para o seu caso de uso, use [uma escala automática personalizada](#custom-autoscale).

**Reduzir horizontalmente**

Quando o cluster se aproxima de um estado de subutilização, dimensione para reduzir custos, mas mantenha o desempenho. Várias métricas são usadas para verificar se é seguro escalar no cluster. As seguintes regras são avaliadas por hora durante 6 horas antes da escala ser realizada:
* O número de instâncias é acima de 2 e acima do número mínimo de instâncias definidas.
* Para garantir que não haja sobrecarga de recursos, as seguintes métricas devem ser verificadas antes que a escala seja realizada: 
    * A utilização do cache não é alta
    * CPU está abaixo da média 
    * A utilização da ingestão está abaixo da média 
    * A utilização do streaming (se o streaming for usado) não é alta
    * Mantenha-se vivo os eventos estão acima de um mínimo definido, processados corretamente e a tempo.
    * Sem estrangulamento de consulta 
    * O número de consultas com falha está abaixo de um mínimo definido.

> [!NOTE]
> A escala na lógica atualmente requer uma avaliação de 7 dias antes da implementação de escala otimizada. Essa avaliação ocorre uma vez a cada 24 horas. Se for necessária uma mudança rápida, use [a escala manual](#manual-scale).

### <a name="custom-autoscale"></a>Escala automática personalizada

Ao usar a escala automática personalizada, você pode dimensionar seu cluster dinamicamente com base em métricas especificadas. O gráfico a seguir mostra o fluxo e as etapas para configurar a escala automática personalizada. Mais detalhes seguem o gráfico.

1. Na caixa **de nome de configuração de escala** automática, digite um nome, como *Escala-out: utilização de cache*. 

   ![Regra de escala](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Para **o modo Escala,** selecione **Escala com base em uma métrica**. Este modo fornece dimensionamento dinâmico. Você também pode selecionar **Escala para uma contagem de instâncias específicas**.

3. Selecione **+ Adicione uma regra**.

4. Na seção **De regra Escala** à direita, digite valores para cada configuração.

    **Critérios**

    | Configuração | Descrição e valor |
    | --- | --- |
    | **Agregação de tempo** | Selecione um critério de agregação, como **Média**. |
    | **Nome da métrica** | Selecione a métrica na qual a operação de escala se baseará, como **Utilização de cache**. |
    | **Estatística de intervalo de agregação** | Escolha entre **Média**, **Mínima**, **Máxima** e **Soma**. |
    | **Operador** | Escolha a opção apropriada, como **Maior que ou igual a**. |
    | **Limite** | Escolha um valor apropriado. Por exemplo, para a utilização do cache, 80% é um bom ponto de partida. |
    | **Duração (em minutos)** | Escolha um período apropriado para o sistema retroceder ao calcular métricas. Comece com o padrão de dez minutos. |
    |  |  |

    **Ação**

    | Configuração | Descrição e valor |
    | --- | --- |
    | **Operação** | Escolha a opção apropriada para reduzir ou escalar horizontalmente. |
    | **Contagem de instâncias** | Escolha o número de nós ou instâncias que você deseja adicionar ou remover quando uma condição de métrica for atendida. |
    | **Tempo de resfriamento (minutos)** | Escolha um intervalo de tempo apropriado de espera entre as operações de escala. Comece com o padrão de cinco minutos. |
    |  |  |

5. Selecione **Adicionar**.

6. Na **seção ''Limites** de ocorrência à esquerda', digite valores para cada configuração.

    | Configuração | Descrição e valor |
    | --- | --- |
    | **Mínimo** | É o número de instâncias para baixo do qual o cluster não será reduzido, independentemente da utilização. |
    | **Máximo** | É o número de instâncias para acima do qual o cluster não será aumentado, independentemente da utilização. |
    | **Padrão** | O número padrão de instâncias. Esta configuração é usada se houver problemas com a leitura das métricas de recursos. |
    |  |  |

7. Selecione **Salvar**.

Agora, você configurou o dimensionamento horizontal para o cluster Azure Data Explorer. Adicione outra regra para dimensionamento vertical. Se você precisar de assistência com problemas de dimensionamento de cluster, [abra uma solicitação](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) de suporte no portal Azure.

## <a name="next-steps"></a>Próximas etapas

* [Monitore o desempenho, a saúde e o uso do Azure Data Explorer com métricas](using-metrics.md)
* [Gerencie o dimensionamento vertical](manage-cluster-vertical-scaling.md) do cluster para dimensionamento apropriado de um cluster.
