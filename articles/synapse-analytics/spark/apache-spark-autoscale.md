---
title: Dimensionar automaticamente instâncias de Apache Spark do Azure Synapse
description: Usar o recurso de autoescala do Azure Synapse para dimensionar automaticamente as instâncias de Apache Spark
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: a2f907384326aa887c12c293feb8c988f42bbaf1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210508"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Dimensionar automaticamente os pools de Apache Spark do Azure Synapse Analytics

O recurso de dimensionamento automático do pool do Azure Synapse Spark dimensiona automaticamente o número de nós em uma instância de cluster para cima e para baixo. Durante a criação de um novo pool do Azure Synapse Spark, um número mínimo e máximo de nós podem ser definidos quando o dimensionamento automático é selecionado. O dimensionamento automático monitora os requisitos de recursos da carga e dimensiona o número de nós para cima ou para baixo. Não há encargos adicionais para esse recurso.

## <a name="metrics-monitoring"></a>Monitoramento de métricas

A autoescala monitora continuamente a instância do Spark e coleta as seguintes métricas:

|Métrica|Descrição|
|---|---|
|Total de CPU pendente|O número total de núcleos necessários para iniciar a execução de todos os nós pendentes.|
|Memória total pendente|A memória total (em MB) necessária para iniciar a execução de todos os nós pendentes.|
|Total de CPU livre|A soma de todos os núcleos não utilizados nos nós ativos.|
|Memória livre total|A soma da memória não utilizada (em MB) nos nós ativos.|
|Memória usada por nó|A carga em um nó. Um nó no qual são usados 10 GB de memória é considerado com mais carga do que um trabalhador com 2 GB de memória usada.|

As métricas acima são verificadas a cada 30 segundos. O dimensionamento automático faz decisões de redução e redução com base nessas métricas.

## <a name="load-based-scale-conditions"></a>Condições de escala baseadas em carga

Quando as condições a seguir forem detectadas, o dimensionamento automático emitirá uma solicitação de dimensionamento:

|Escalar verticalmente|Reduzir verticalmente|
|---|---|
|O total de CPU pendente é maior que o total de CPU livre por mais de 1 minuto.|A CPU pendente total é menor que a CPU livre total por mais de 2 minutos.|
|O total de memória pendente é maior que o total de memória livre por mais de 1 minuto.|A memória total pendente é menor que a memória livre total por mais de 2 minutos.|

Para expansão, o serviço de dimensionamento automático Synapse do Azure calcula quantos novos nós são necessários para atender aos requisitos atuais de CPU e memória e, em seguida, emite uma solicitação de expansão para adicionar o número necessário de nós.

Para reduzir verticalmente, com base no número de executores, Application Masters por nó e os requisitos atuais de CPU e memória, o dimensionamento automático emite uma solicitação para remover um determinado número de nós. O serviço também detecta quais nós são candidatos para remoção com base na execução do trabalho atual. A operação de redução vertical primeiro encerra os nós e, em seguida, remove-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-spark-pool-with-autoscaling"></a>Criar um pool do Spark com dimensionamento automático

Para habilitar o recurso de dimensionamento automático, conclua as seguintes etapas como parte do processo de criação de pool normal:

1. Na guia **noções básicas** , marque a caixa de seleção **habilitar autoescala** .
1. Insira os valores desejados para as seguintes propriedades:  

    * Número **mínimo** de nós.
    * Número **máximo** de nós.

O número inicial de nós será o mínimo. Esse valor define o tamanho inicial da instância quando ela é criada. O número mínimo de nós não pode ser menor que três.

## <a name="best-practices"></a>Práticas recomendadas

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considerar a latência de operações de escala ou redução vertical

Pode levar de 1 a 5 minutos para que uma operação de dimensionamento seja concluída.

### <a name="preparation-for-scaling-down"></a>Preparação para reduzir verticalmente

Durante o processo de redução de escala de instância, o dimensionamento automático colocará os nós no estado de encerramento para que nenhum executor novo possa ser iniciado nesse nó.

Os trabalhos em execução continuarão a ser executados e concluídos. Os trabalhos pendentes aguardarão para serem agendados normalmente com menos nós disponíveis.

## <a name="next-steps"></a>Próximas etapas

Início rápido para configurar um novo pool do Spark [criar um pool do Spark](..\quickstart-create-apache-spark-pool.md)
