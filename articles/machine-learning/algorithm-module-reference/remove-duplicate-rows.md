---
title: 'Remover linhas duplicadas: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Remover linhas duplicadas no Azure Machine Learning para remover possíveis duplicatas de um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456014"
---
# <a name="remove-duplicate-rows-module"></a>Remover módulo de linhas duplicadas

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para remover duplicatas potenciais de um conjunto de dados.

Por exemplo, suponha que seus dados se pareçam com os seguintes e represente vários registros para pacientes. 

| Pacienteid | Initials| Gênero|Idade|Admitiu|
|----|----|----|----|----|
|1|F.m.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.m.| M| 24| Fev|
|4| F.m.| M| 23| Fev|
| | F.m.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Claramente, este exemplo tem várias colunas com dados potencialmente duplicados. Se eles são realmente duplicados depende do seu conhecimento dos dados. 

+ Por exemplo, você deve saber que muitos pacientes têm o mesmo nome. Você não eliminaria duplicatas usando quaisquer colunas de nome, apenas a coluna **ID.** Dessa forma, apenas as linhas com valores de ID duplicados são filtradas, independentemente de os pacientes terem o mesmo nome ou não.

+ Alternativamente, você pode decidir permitir duplicatas no campo ID e usar alguma outra combinação de arquivos para encontrar registros exclusivos, como nome, sobrenome, idade e gênero.  

Para definir os critérios para saber se uma linha é duplicada ou não, você especifica uma única coluna ou um conjunto de colunas para usar como **chaves**. Duas linhas são consideradas duplicadas somente quando os valores em **todas as** colunas-chave são iguais. Se alguma linha tiver valor faltante para **as teclas,** elas não serão consideradas linhas duplicadas. Por exemplo, se Gênero e Idade forem definidos como Chaves na tabela acima, as linhas 6 e 7 não são linhas duplicadas, dado que eles têm valor perdido na Idade.

Quando você executa o módulo, ele cria um conjunto de dados do candidato e retorna um conjunto de linhas que não têm duplicatas no conjunto de colunas especificadas.

> [!IMPORTANT]
> O conjunto de dados de origem não está alterado; este módulo cria um novo conjunto de dados filtrado para excluir duplicatas, com base nos critérios especificados.

## <a name="how-to-use-remove-duplicate-rows"></a>Como usar remover linhas duplicadas

1. Adicione o módulo ao seu pipeline. Você pode encontrar o módulo **Remover linhas duplicadas** em **Transformação de Dados,** **Manipulação**.  

2. Conecte o conjunto de dados que deseja verificar para linhas duplicadas.

3. No painel **Propriedades,** em Expressão do filtro de **seleção de coluna Chave,** clique em **Iniciar seletor de colunas**para escolher colunas a serem usados na identificação de duplicatas.

    Neste contexto, **Key** não significa um identificador único. Todas as colunas selecionadas usando o Seletor de colunas são designadas como **colunas-chave**. Todas as colunas não selecionadas são consideradas colunas não-chave. A combinação de colunas que você seleciona como chaves determina a singularidade dos registros. (Pense nisso como uma declaração SQL que usa múltiplas igualdades se junta.)

    Exemplos:

    + "Quero garantir que os IDs sejam únicos": Escolha apenas a coluna ID.
    + "Quero garantir que a combinação de primeiro nome, sobrenome e ID seja única": Selecione as três colunas.

4. Use a **caixa de seleção Reter primeira linha duplicada** para indicar qual linha a retornar quando as duplicatas forem encontradas:

    + Se selecionado, a primeira linha é devolvida e outras descartadas. 
    + Se você desmarcar esta opção, a última linha duplicada será mantida nos resultados e outras serão descartadas. 

5. Envie o oleoduto.

6. Para revisar os resultados, clique com o botão direito do mouse no módulo e selecione **Visualizar**. 

> [!TIP]
> Se os resultados forem difíceis de entender ou se você quiser excluir algumas colunas da consideração, você pode remover colunas usando o [módulo Selecionar colunas no](./select-columns-in-dataset.md) conjunto de dados.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 