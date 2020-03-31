---
title: 'Partição e Amostra: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Partição e Amostra no Azure Machine Learning para realizar a amostragem em um conjunto de dados ou para criar partições a partir do seu conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477503"
---
# <a name="partition-and-sample-module"></a>Módulo partição e amostra

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use o módulo Partição e Amostra para realizar a amostragem em um conjunto de dados ou para criar partições a partir do seu conjunto de dados.

A amostragem é uma ferramenta importante na aprendizagem por máquina porque permite reduzir o tamanho de um conjunto de dados, mantendo a mesma proporção de valores. Este módulo suporta várias tarefas relacionadas que são importantes no aprendizado de máquina: 

- Dividindo seus dados em várias subseções do mesmo tamanho. 

  Você pode usar as partições para validação cruzada ou atribuir casos a grupos aleatórios.

- Separando dados em grupos e, em seguida, trabalhando com dados de um grupo específico. 

  Depois de atribuir casos aleatoriamente a diferentes grupos, talvez seja necessário modificar os recursos associados a apenas um grupo.

- Amostragem. 

  Você pode extrair uma porcentagem dos dados, aplicar amostragem aleatória ou escolher uma coluna para usar para equilibrar o conjunto de dados e realizar amostragem estratificada em seus valores.

- Criando um conjunto de dados menor para testes. 

  Se você tiver um monte de dados, você pode querer usar apenas as primeiras linhas *n* durante a configuração do pipeline e, em seguida, mudar para usar o conjunto de dados completo quando você construir o seu modelo. Você também pode usar amostragem para criar um conjunto de dados menor para uso em desenvolvimento.

## <a name="configure-the-module"></a>Configure o módulo

Este módulo suporta os seguintes métodos para dividir seus dados em partições ou para amostragem. Escolha o método primeiro e, em seguida, defina opções adicionais que o método requer.

- Head
- amostragem
- Atribuir às dobras
- Selecionar dobra

### <a name="get-top-n-rows-from-a-dataset"></a>Obtenha linhas TOP N a partir de um conjunto de dados

Use este modo para obter apenas as primeiras *n* linhas. Esta opção é útil se você quiser testar um pipeline em um pequeno número de linhas, e você não precisa que os dados sejam balanceados ou amostrados de forma alguma.

1. Adicione o módulo **Partição e Amostra** ao seu pipeline na interface e conecte o conjunto de dados.  

1. **Partição ou modo de amostra**: Defina esta opção como **Cabeça**.

1. **Número de linhas a serem selecionadas**: Digite o número de linhas a retornar.

   O número de linhas deve ser um inteiro não negativo. Se o número de linhas selecionadas for maior do que o número de linhas no conjunto de dados, todo o conjunto de dados será retornado.

1. Envie o oleoduto.

O módulo produz um único conjunto de dados que contém apenas o número especificado de linhas. As linhas são sempre lidas do topo do conjunto de dados.

### <a name="create-a-sample-of-data"></a>Crie uma amostra de dados

Esta opção suporta amostragem aleatória simples ou amostragem aleatória estratificada. É útil se você quiser criar um conjunto de dados de amostra menor para testes.

1. Adicione o módulo **Partição e Amostra** ao seu pipeline e conecte o conjunto de dados.

1. **Partição ou modo de amostra**: Defina esta opção como **Amostragem**.

1. **Taxa de amostragem**: Digite um valor entre 0 e 1. este valor especifica a porcentagem de linhas do conjunto de dados de origem que deve ser incluída no conjunto de dados de saída.

   Por exemplo, se você quiser apenas metade `0.5` do conjunto de dados original, digite para indicar que a taxa de amostragem deve ser de 50%.

   As linhas do conjunto de dados de entrada são embaralhadas e colocadas seletivamente no conjunto de dados de saída, de acordo com a razão especificada.

1. **Sementes aleatórias para amostragem**: Opcionalmente, digite um inteiro para usar como valor de semente.

   Esta opção é importante se você quiser que as linhas sejam divididas da mesma maneira todas as vezes. O valor padrão é **0**, o que significa que uma semente inicial é gerada com base no relógio do sistema. Esse valor pode levar a resultados ligeiramente diferentes cada vez que você executar o pipeline.

1. **Divisão estratificada para amostragem**: Selecione esta opção se for importante que as linhas no conjunto de dados sejam divididas uniformemente por alguma coluna-chave antes da amostragem.

   Para **a coluna-chave Estratificação para amostragem,** selecione uma *única coluna de estratos* a ser usada ao dividir o conjunto de dados. As linhas no conjunto de dados são então divididas da seguinte forma:

   1. Todas as linhas de entrada são agrupadas (estratificadas) pelos valores na coluna estratos especificados.

   1. As linhas são colocadas em ordem aleatória dentro de cada grupo.

   1. Cada grupo é seletivamente adicionado ao conjunto de dados de saída para atender à taxa especificada.


1. Envie o oleoduto.

   Com esta opção, o módulo produz um único conjunto de dados que contém uma amostra representativa dos dados. A parte restante e não amostrada do conjunto de dados não é saída. 

## <a name="split-data-into-partitions"></a>Dividir dados em partições

Use esta opção quando quiser dividir o conjunto de dados em subconjuntos dos dados. Essa opção também é útil quando você deseja criar um número personalizado de dobras para validação cruzada ou para dividir linhas em vários grupos.

1. Adicione o módulo **Partição e Amostra** ao seu pipeline e conecte o conjunto de dados.

1. Para **partição ou modo de amostra,** **selecione Atribuir a dobras**.

1. **Use a substituição na particionamento**: Selecione esta opção se quiser que a linha amostrada seja colocada de volta no pool de linhas para uma possível reutilização. Como resultado, a mesma linha pode ser atribuída a várias dobras.

   Se você não usar a substituição (a opção padrão), a linha amostrada não será colocada de volta no pool de linhas para reutilização potencial. Como resultado, cada linha pode ser atribuída a apenas uma dobra.

1. **Divisão aleatória**: Selecione esta opção se quiser que as linhas sejam atribuídas aleatoriamente às dobras.

   Se você não selecionar essa opção, as linhas serão atribuídas a dobras através do método round-robin.

1. **Sementes aleatórias**: Opcionalmente, digite um inteiro para usar como valor de semente. Esta opção é importante se você quiser que as linhas sejam divididas da mesma maneira todas as vezes. Caso contrário, o valor padrão de **0** significa que uma semente inicial aleatória será usada.

1. **Especifique o método particionário:** Indique como deseja que os dados sejam repartidos em cada partição, usando essas opções:

   - **Partição uniforme :** Use esta opção para colocar um número igual de linhas em cada partição. Para especificar o número de partições de saída, digite um número inteiro no **número Especificar de dobras para dividir uniformemente em** caixa.

   - **Partição com proporções personalizadas**: Use esta opção para especificar o tamanho de cada partição como uma lista separada por comuma.

     Por exemplo, suponha que você deseja criar três partições. A primeira partição conterá 50% dos dados. As duas partições restantes conterão 25% dos dados. Na **Lista de proporções separadas por caixa de comma, digite** estes números: **.5, .25, .25**.

     A soma de todos os tamanhos de partição deve somar exatamente 1.

     Se você inserir números que somam *menos de 1,* uma partição extra será criada para segurar as linhas restantes. Por exemplo, se você inserir os valores **.2** e **.3**, uma terceira partição será criada para manter os 50% restantes de todas as linhas.
     
     Se você inserir números que somam *mais de 1*, um erro será levantado quando você executar o pipeline.

1. **Divisão estratificada**: Selecione esta opção se quiser que as linhas sejam estratificadas quando divididas e, em seguida, escolha a _coluna estratos_.

1. Envie o oleoduto.

   Com essa opção, o módulo produz vários conjuntos de dados. Os conjuntos de dados são particionados de acordo com as regras especificadas.

### <a name="use-data-from-a-predefined-partition"></a>Use dados de uma partição predefinida  

Use essa opção quando você tiver dividido um conjunto de dados em várias partições e agora deseja carregar cada partição para análise ou processamento posterior.

1. Adicione o módulo **Partição e Amostra** ao pipeline.

1. Conecte o módulo à saída de uma instância anterior de **Partição e Amostra**. Essa instância deve ter usado a opção **Atribuir a Dobras** para gerar algum número de partições.

1. **Partição ou modo de amostra**: Selecione Escolher **Dobra**.

1. **Especifique qual dobra a ser amostrada de**: Selecione uma partição a ser usada inserindo seu índice. Os índices de partição são baseados em 1. Por exemplo, se você dividisse o conjunto de dados em três partes, as partições teriam os índices 1, 2 e 3.

   Se você inserir um valor de índice inválido, um erro de tempo de projeto será levantado: "Erro 0018: Conjunto de dados contém dados inválidos".

   Além de agrupar o conjunto de dados por dobras, você pode separar o conjunto de dados em dois grupos: uma dobra de destino e tudo mais. Para fazer isso, digite o índice de uma única dobra e selecione a opção **Escolha complemento da dobra selecionada** para obter tudo, menos os dados na dobra especificada.

1. Se você estiver trabalhando com várias partições, você deve adicionar mais instâncias do módulo **Partição e Amostra** para lidar com cada partição.

   Por exemplo, o módulo **Partição e Amostra** na segunda linha está definido como **Atribuir a Dobras**e o módulo na terceira linha está definido como **Pick Fold**.   

   ![Partição e exemplo](./media/module/partition-and-sample.png)

1. Envie o oleoduto.

   Com essa opção, o módulo produz um único conjunto de dados que contém apenas as linhas atribuídas a essa dobra.

> [!NOTE]
>  Você não pode ver as designações da dobra diretamente. Eles estão presentes apenas nos metadados.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 