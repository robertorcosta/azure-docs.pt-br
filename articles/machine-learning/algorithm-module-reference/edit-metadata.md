---
title: 'Editar metadados: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Editar metadados no Azure Machine Learning para alterar metadados associados a colunas em um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064256"
---
# <a name="edit-metadata-module"></a>Editar módulo de metadados

Este artigo descreve um módulo incluído no azure Machine Learning designer (visualização).

Use o módulo Editar metadados para alterar metadados associados a colunas em um conjunto de dados. O valor e o tipo de dados do conjunto de dados serão alterados após o uso do módulo Editar metadados.

As alterações de metadados típicas podem incluir:
  
+ Tratando colunas booleanas ou numéricas como valores categóricos.
  
+ Indicando qual coluna contém o rótulo **de classe** ou contém os valores que deseja categorizar ou prever.
  
+ Marcando colunas como características.
  
+ Alterar valores de data/hora para valores numéricos ou vice-versa.
  
+ Renomeando colunas.
  
 Use Editar metadados a qualquer momento que você precisar modificar a definição de uma coluna, normalmente para atender aos requisitos de um módulo a jusante. Por exemplo, alguns módulos funcionam apenas com tipos de dados `IsFeature` `IsCategorical`específicos ou exigem bandeiras nas colunas, como ou .  
  
 Depois de executar a operação necessária, você pode redefinir os metadados para o seu estado original.
  
## <a name="configure-edit-metadata"></a>Configurar editar metadados
  
1. No designer de Machine Learning do Azure, adicione o módulo Editar metadados ao seu pipeline e conecte o conjunto de dados que deseja atualizar. Você pode encontrar o módulo na categoria **Transformação de Dados.**
  
1. Clique em **Editar coluna** no painel direito do módulo e escolha a coluna ou conjunto de colunas para trabalhar. Você pode escolher colunas individualmente por nome ou índice, ou pode escolher um grupo de colunas por tipo.  
  
1. Selecione a opção **Tipo de dados** se você precisar atribuir um tipo de dados diferente às colunas selecionadas. Você pode precisar alterar o tipo de dados para determinadas operações. Por exemplo, se o conjunto de dados de origem tiver números manipulados como texto, você deve alterá-los para um tipo de dados numérico antes de usar operações matemáticas.

    + Os tipos de dados suportados são **String**, **Inteiro**, **Double,** **Boolean**e **DateTime**.

    + Se você selecionar várias colunas, você deve aplicar as alterações de metadados em *todas as* colunas selecionadas. Por exemplo, digamos que você escolha duas ou três colunas numéricas. Você pode alterá-los todos para um tipo de dados de seqüência e renomeá-los em uma operação. No entanto, você não pode alterar uma coluna para um tipo de dados de seqüência e outra coluna de um flutuador para um inteiro.
  
    + Se você não especificar um novo tipo de dados, os metadados da coluna não são alterados.

    + O tipo de coluna e os valores serão alterarapós executar a operação Editar metadados. Você pode recuperar o tipo de dados original a qualquer momento usando Edit Metadata para redefinir o tipo de dados da coluna.  

    > [!NOTE]
    > Se você alterar qualquer tipo de número para o tipo **DateTime,** deixe o campo **Formato datetime** em branco. Atualmente, não é possível especificar o formato de dados de destino.  

1. Selecione a opção **Categórico** para especificar que os valores nas colunas selecionadas devem ser tratados como categorias.

    Por exemplo, você pode ter uma coluna que contém os números 0, 1 e 2, mas saiba que os números realmente significam "Fumante", "Não fumante" e "Desconhecido". Nesse caso, ao sinalizar a coluna como categórica, você garante que os valores sejam usados apenas para agrupar dados e não em cálculos numéricos.
  
1. Use a opção **Campos** se quiser alterar a forma como o Azure Machine Learning usa os dados em um modelo.

    + **Recurso**: Use esta opção para sinalizar uma coluna como um recurso em módulos que operam apenas em colunas de recursos. Por padrão, todas as colunas são inicialmente tratadas como recursos.  
  
    + **Rótulo**: Use esta opção para marcar o rótulo, que também é conhecido como atributo previsível ou variável de destino. Muitos módulos exigem que exatamente uma coluna de rótulo esteja presente no conjunto de dados.

        Em muitos casos, o Azure Machine Learning pode inferir que uma coluna contém um rótulo de classe. Ao definir esses metadados, você pode garantir que a coluna seja identificada corretamente. A definição dessa opção não altera os valores de dados. Ele muda apenas a maneira como alguns algoritmos de aprendizagem de máquina lidam com os dados.
  
    > [!TIP]
    > Você tem dados que não se encaixam nessas categorias? Por exemplo, seu conjunto de dados pode conter valores como identificadores exclusivos que não são úteis como variáveis. Às vezes, essas IDs podem causar problemas quando usadas em um modelo.
    >
    > Felizmente, o Azure Machine Learning mantém todos os seus dados, para que você não tenha que excluir tais colunas do conjunto de dados. Quando você precisar executar operações em algum conjunto especial de colunas, basta remover todas as outras colunas temporariamente usando o [módulo Selecionar colunas no conjunto de dados.](select-columns-in-dataset.md) Mais tarde, você pode mesclar as colunas de volta ao conjunto de dados usando o módulo [Adicionar colunas.](add-columns.md)  
  
1. Use as seguintes opções para limpar seleções anteriores e restaurar metadados para os valores padrão.  
  
    + **Recurso claro**: Use esta opção para remover o sinalizador de recurso.  
  
         Todas as colunas são inicialmente tratadas como características. Para módulos que realizam operações matemáticas, talvez seja necessário usar essa opção para evitar que colunas numéricas sejam tratadas como variáveis.
  
    + **Etiqueta clara**: Use esta opção para remover os metadados do **rótulo** da coluna especificada.  
  
    + **Pontuação clara**: Use esta opção para remover os metadados de **pontuação** da coluna especificada.  
  
         No momento, você não pode marcar explicitamente uma coluna como uma pontuação no Azure Machine Learning. No entanto, algumas operações resultam em uma coluna sendo sinalizada como uma pontuação internamente. Além disso, um módulo R personalizado pode produzir valores de pontuação de saída.

1. Para **novos nomes de coluna,** digite o novo nome da coluna ou colunas selecionadas.  
  
    + Os nomes das colunas podem usar apenas caracteres que são suportados pela codificação UTF-8. Cordas vazias, nulos ou nomes que consistem inteiramente em espaços não são permitidos.  
  
    + Para renomear várias colunas, digite os nomes como uma lista separada por comuma, em ordem dos índices das colunas.  
  
    + Todas as colunas selecionadas devem ser renomeadas. Você não pode omitir ou pular colunas.  
  
1. Envie o oleoduto.  

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.
