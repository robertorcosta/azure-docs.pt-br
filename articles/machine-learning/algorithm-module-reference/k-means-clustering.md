---
title: 'K-Significa Clustering: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de cluster de meios K no Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921157"
---
# <a name="module-k-means-clustering"></a>Módulo: K-Means Clustering

Este artigo descreve como usar o módulo *de clustering de meios K* no azure Machine Learning designer (preview) para criar um modelo de clustering de meios K não treinados. 
 
K-means é um dos mais simples e mais conhecidos algoritmos de aprendizagem *não supervisionados.* Você pode usar o algoritmo para uma variedade de tarefas de aprendizado de máquina, tais como: 

* [Detectando dados anormais](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Agrupamento de documentos de texto.
* Analisar conjuntos de dados antes de usar outros métodos de classificação ou regressão. 

Para criar um modelo de clustering, você:

* Adicione este módulo ao seu pipeline.
* Conecte um conjunto de dados.
* Defina parâmetros, como o número de clusters esperados, a métrica de distância a ser usada na criação dos clusters, e assim por diante. 
  
Depois de configurar os hiperparâmetros do módulo, você conecta o modelo não treinado ao [Modelo de Agrupamento de Trens](train-clustering-model.md). Como o algoritmo k-means é um método de aprendizagem não supervisionado, uma coluna de rótulos é opcional. 

+ Se seus dados incluem um rótulo, você pode usar os valores do rótulo para orientar a seleção dos clusters e otimizar o modelo. 

+ Se seus dados não têm rótulo, o algoritmo cria clusters representando possíveis categorias, com base apenas nos dados.  

##  <a name="understand-k-means-clustering"></a>Entenda o agrupamento de meios K
 
Em geral, o clustering utiliza técnicas iterativas para agrupar casos em um conjunto de dados em clusters que possuem características semelhantes. Esses agrupamentos são úteis para explorar dados, identificar anomalias nos dados e, eventualmente, fazer previsões. Os modelos de clustering também podem ajudá-lo a identificar relacionamentos em um conjunto de dados que você pode não derivar logicamente navegando ou observando simples. Por essas razões, o clustering é frequentemente usado nas fases iniciais das tarefas de aprendizado de máquina, para explorar os dados e descobrir correlações inesperadas.  
  
 Quando você configura um modelo de clustering usando o método de meios K, você deve especificar um número de destino *k* que indique o número de *centroídeos* desejados no modelo. O centróide é um ponto representativo de cada aglomerado. O algoritmo de meios K atribui cada ponto de dados de entrada a um dos clusters minimizando a soma de quadrados dentro do cluster. 
 
Quando processa os dados de treinamento, o algoritmo k-significa começa com um conjunto inicial de centrosides escolhidos aleatoriamente. Os centróides servem como pontos de partida para os clusters, e eles aplicam o algoritmo de Lloyd para refinar iterativamente suas localizações. O algoritmo k-significa parar de construir e refinar clusters quando atende a uma ou mais dessas condições:  
  
-   Os centróides estabilizam-se, o que significa que as atribuições de cluster para pontos individuais não mudam mais e o algoritmo convergiu para uma solução.  
  
-   O algoritmo é concluído executando o número especificado de iterações.  
  
 Depois de concluir a fase de treinamento, você usa o módulo [Atribuir dados a clusters](assign-data-to-clusters.md) para atribuir novos casos a um dos clusters que você encontrou usando o algoritmo de meios K. Você executa a atribuição de cluster calculando a distância entre o novo caso e o centróide de cada cluster. Cada novo caso é atribuído ao cluster com o centróide mais próximo.  

## <a name="configure-the-k-means-clustering-module"></a>Configure o módulo de cluster de meios K
  
1.  Adicione o módulo **de cluster de meios K** ao seu pipeline.  
  
2.  Para especificar como deseja que o modelo seja treinado, selecione a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único**: Se você sabe os parâmetros exatos que deseja usar no modelo de clustering, você pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **número de centróides, digite**o número de clusters que deseja que o algoritmo comece.  
  
     O modelo não é garantido para produzir exatamente este número de clusters. O algoritmo começa com esse número de pontos de dados e itera para encontrar a configuração ideal.  
  
4.  As propriedades **Initialização** são usadas para especificar o algoritmo usado para definir a configuração inicial do cluster.  
  
    -   **Primeiro N**: Alguns pontos de dados iniciais são escolhidos a partir do conjunto de dados e usados como meios iniciais. 
    
         Este método também é chamado de *método Forgy*.  
  
    -   **Aleatório**: O algoritmo coloca aleatoriamente um ponto de dados em um cluster e, em seguida, calcula a média inicial para ser o centroide dos pontos atribuídos aleatoriamente pelo cluster. 

         Este método também é chamado de método *de partição aleatória.*  
  
    -   **K-Means++**: Este é o método padrão para inicializar clusters.  
  
         O algoritmo **K-means++** foi proposto em 2007 por David Arthur e Sergei Vassilvitskii para evitar agrupamentos ruins pelo algoritmo k-means padrão. **K-means++** melhora os meios K padrão usando um método diferente para escolher os centros de cluster iniciais.  
  
    
5.  Para **sementes de número aleatório, digite**opcionalmente um valor para usar como semente para a inicialização do cluster. Esse valor pode ter um efeito significativo na seleção de clusters.  
  
6.  Para **Métrica,** escolha a função a ser usada para medir a distância entre vetores de cluster, ou entre novos pontos de dados e o centroide escolhido aleatoriamente. O Azure Machine Learning suporta as seguintes métricas de distância de cluster:  
  
    -   **Euclidean**: A distância euclidiana é comumente usada como uma medida de dispersão de aglomerados para agrupamento de k-médias. Essa métrica é preferencial porque minimiza a distância média entre pontos e os centróides.
  
7.  Para **Iterações, digite**o número de vezes que o algoritmo deve iterar sobre os dados de treinamento antes de finalizar a seleção de centroides.  
  
     Você pode ajustar este parâmetro para equilibrar a precisão contra o tempo de treinamento.  
  
8.  Para **o modo De atribuir rótulo,** escolha uma opção que especifique como uma coluna de rótulo, se estiver presente no conjunto de dados, deve ser tratada.  
  
     Como o cluster ingerestece isto K é um método de aprendizado de máquina não supervisionado, os rótulos são opcionais. No entanto, se o seu conjunto de dados já tiver uma coluna de rótulos, você pode usar esses valores para orientar a seleção dos clusters, ou pode especificar que os valores serão ignorados.  
  
    -   **Ignorar a coluna da etiqueta**: Os valores na coluna da etiqueta são ignorados e não são usados na construção do modelo.
  
    -   **Preencha os valores faltantes**: Os valores da coluna do rótulo são usados como recursos para ajudar a construir os clusters. Se alguma linha estiver faltando um rótulo, o valor será imputado usando outros recursos.  
  
    -   **Sobregravação do mais próximo ao centro**: Os valores da coluna do rótulo são substituídos por valores de etiqueta previstos, utilizando o rótulo do ponto mais próximo do centroide atual.  

8.  Selecione a opção **Normalize recursos** se quiser normalizar os recursos antes do treinamento.
  
     Se você aplicar a `[0,1]` normalização, antes do treinamento, os pontos de dados serão normalizados pelo MinMaxNormalizer.

10. Treinar o modelo.  
  
    -   Se você definir **Criar modo de treinador** para um único **parâmetro,** adicione um conjunto de dados marcado e treine o modelo usando o módulo [Modelo de Agrupamento de Trens.](train-clustering-model.md)  
  
## <a name="results"></a>Resultados

Depois de terminar de configurar e treinar o modelo, você tem um modelo que você pode usar para gerar pontuações. No entanto, existem várias maneiras de treinar o modelo, e várias maneiras de visualizar e usar os resultados: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capture um instantâneo do modelo em seu espaço de trabalho

Se você usou o módulo [Modelo de Agrupamento de Trens:](train-clustering-model.md)

1. Selecione o módulo **Modelo de Agrupamento de Trens** e abra o painel direito.

2. Selecionar **guia Saídas.** Selecione o ícone **do conjunto de dados Registrar** para salvar uma cópia do modelo treinado.

O modelo salvo representa os dados de treinamento no momento em que você salvou o modelo. Se você atualizar mais tarde os dados de treinamento usados no pipeline, ele não atualizará o modelo salvo. 

### <a name="see-the-clustering-result-dataset"></a>Consulte o conjunto de dados de resultados de clustering 

Se você usou o módulo [Modelo de Agrupamento de Trens:](train-clustering-model.md)

1. Clique com o botão direito do mouse no módulo **Modelo de Agrupamento de Trens.**

2. Selecione **Visualizar**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Dicas para gerar o melhor modelo de clustering  

Sabe-se que o processo *de semeação* que é usado durante o agrupamento pode afetar significativamente o modelo. Semeada significa a colocação inicial de pontos em potenciais centróides.
 
Por exemplo, se o conjunto de dados contiver muitos outliers, e um outlier for escolhido para semear os clusters, nenhum outro ponto de dados se encaixaria bem com esse cluster, e o cluster poderia ser um singleton. Ou seja, pode ter apenas um ponto.  
  
Você pode evitar esse problema de algumas maneiras:  
  
-   Altere o número de centróides e tente múltiplos valores de sementes.  
  
-   Crie vários modelos, variando a métrica ou iterando mais.  
  
Em geral, com modelos de clustering, é possível que qualquer configuração dada resulte em um conjunto de clusters otimizados localmente. Em outras palavras, o conjunto de clusters devolvidos pelo modelo se adequa apenas aos pontos de dados atuais e não é generalizável para outros dados. Se você usar uma configuração inicial diferente, o método de meios K pode encontrar uma configuração diferente, superior. 

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
