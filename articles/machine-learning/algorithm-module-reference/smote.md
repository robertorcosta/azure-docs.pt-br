---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo SMOTE no Azure Machine Learning para aumentar o número de exemplos de baixa incidência em um conjunto de dados usando a superamostragem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477452"
---
# <a name="smote"></a>SMOTE

Este artigo descreve como usar o módulo SMOTE no Azure Machine Learning designer (preview) para aumentar o número de casos sub-representados em um conjunto de dados usado para aprendizado de máquina. O SMOTE é uma maneira melhor de aumentar o número de casos raros do que simplesmente duplicar os casos existentes.  

Você conecta o módulo SMOTE a um conjunto de dados que está *desequilibrado*. Há muitas razões pelas quais um conjunto de dados pode ser desequilibrado. Por exemplo, a categoria que você está mirando pode ser rara na população, ou os dados podem ser difíceis de coletar. Normalmente, você usa SMOTE quando a *classe* que você deseja analisar é sub-representada. 
  
O módulo retorna um conjunto de dados que contém as amostras originais. Ele também retorna uma série de amostras de minorias sintéticas, dependendo da porcentagem que você especificar.  
  
## <a name="more-about-smote"></a>Mais sobre o SMOTE

A Técnica de Superamostração de Minorias Sintéticas (SMOTE) é uma técnica estatística para aumentar o número de casos em seu conjunto de dados de forma equilibrada. O módulo funciona gerando novas instâncias a partir de casos minoritários existentes que você fornece como entrada. Esta implementação do SMOTE *não* altera o número de casos majoritários.

As novas instâncias não são apenas cópias de casos minoritários existentes. Em vez disso, o algoritmo pega amostras do espaço de *recurso* para cada classe alvo e seus vizinhos mais próximos. O algoritmo então gera novos exemplos que combinam características do caso de destino com características de seus vizinhos. Essa abordagem aumenta os recursos disponíveis para cada classe e torna os exemplos mais gerais.
  
O SMOTE toma todo o conjunto de dados como uma entrada, mas aumenta a porcentagem de apenas casos minoritários. Por exemplo, suponha que você tenha um conjunto de dados desequilibrado onde apenas 1% dos casos têm o valor-alvo A (a classe minoritária), e 99% dos casos têm o valor B. Para aumentar o percentual de casos minoritários para o dobro do percentual anterior, você entraria em **200** por **porcentagem de SMOTE** nas propriedades do módulo.  
  
## <a name="examples"></a>Exemplos  

Recomendamos que você tente usar o SMOTE com um conjunto de dados menor para ver como funciona. O exemplo a seguir usa o conjunto de dados de doação de sangue disponível no designer de Machine Learning do Azure.
  
Se você adicionar o conjunto de dados a um pipeline e selecionar **Visualizar** na saída do conjunto de dados, você poderá ver que das 748 linhas ou casos no conjunto de dados, 570 casos (76%) são de Classe 0 e 178 casos (24%) são de Classe 1. Embora este resultado não seja terrivelmente desequilibrado, a Classe 1 representa as pessoas que doaram sangue, então essas linhas contêm o *espaço de recurso* que você deseja modelar.
 
Para aumentar o número de casos, você pode definir o valor da **porcentagem de SMOTE,** usando múltiplos de 100, da seguinte forma:

||Classe 0|Classe 1|total|  
|-|-------------|-------------|-----------|  
|Conjunto de dados original<br /><br /> (equivalente à =  **porcentagem SMOTE****0**)|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**SMOTE percentual** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE percentual** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1.104|  
|**SMOTE percentual** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> O aumento do número de casos usando o SMOTE não é garantido para produzir modelos mais precisos. Tente pipelining com diferentes percentagens, diferentes conjuntos de recursos e diferentes números de vizinhos mais próximos para ver como adicionar casos influencia seu modelo.  
  
## <a name="how-to-configure-smote"></a>Como configurar o SMOTE
  
1.  Adicione o módulo SMOTE ao seu pipeline. Você pode encontrar o módulo em **módulos de Transformação de Dados,** na categoria **Manipulação.**

2. Conecte o conjunto de dados que deseja impulsionar. Se você quiser especificar o espaço de recurso para a construção dos novos casos, usando apenas colunas específicas ou excluindo algumas, use o módulo [Selecionar colunas no conjunto de dados.](select-columns-in-dataset.md) Em seguida, você pode isolar as colunas que deseja usar antes de usar o SMOTE.
  
    Caso contrário, a criação de novos casos através do SMOTE é baseada em *todas as* colunas que você fornece como entradas. Pelo menos uma coluna das colunas de recursos é numérica.
  
3.  Certifique-se de que a coluna que contém o rótulo ou a classe de destino esteja selecionada. O SMOTE aceita apenas rótulos binários.
  
4.  O módulo SMOTE identifica automaticamente a classe minoritária na coluna de rótulos e, em seguida, recebe todos os exemplos para a classe minoritária. Todas as colunas não podem ter valores NaN.
  
5.  Na opção **porcentagem SMOTE, digite** um número inteiro que indique a porcentagem-alvo de casos minoritários no conjunto de dados de saída. Por exemplo:   
  
    - Você **digita 0**. O módulo SMOTE retorna exatamente o mesmo conjunto de dados que você forneceu como entrada. Não acrescenta novos casos minoritários. Neste conjunto de dados, a proporção de classe não mudou.  
  
    - Você digita **100**. O módulo SMOTE gera novos casos minoritários. Acrescenta o mesmo número de casos minoritários que estavam no conjunto de dados original. Como o SMOTE não aumenta o número de casos majoritários, a proporção de casos de cada classe mudou.  
  
    - Você digita **200**. O módulo dobra a porcentagem de casos minoritários em comparação com o conjunto de dados original. Isso *não resulta* em ter o dobro de casos minoritários do que antes. Em vez disso, o tamanho do conjunto de dados é aumentado de tal forma que o número de casos majoritários permanece o mesmo. O número de casos minoritários é aumentado até corresponder ao valor percentual desejado.  
  
    > [!NOTE]
    > Use apenas múltiplos de 100 para a porcentagem SMOTE.

6.  Use a opção **Número de vizinhos mais próximos** para determinar o tamanho do espaço de recurso que o algoritmo SMOTE usa na construção de novos casos. Um vizinho mais próximo é uma linha de dados (um caso) que é semelhante a um caso de destino. A distância entre duas ocorrências é medida pela combinação dos vetores de peso de todos os recursos.  
  
    + Ao aumentar o número de vizinhos mais próximos, você recebe características de mais casos.
    + Ao manter o número de vizinhos mais próximos baixo, você usa recursos que são mais parecidos com os da amostra original.  
  
7. Digite um valor na caixa **de sementes Aleatória** se você quiser garantir os mesmos resultados sobre as corridas do mesmo pipeline, com os mesmos dados. Caso contrário, o módulo gera uma semente aleatória com base nos valores do relógio do processador quando o pipeline é implantado. A geração de uma semente aleatória pode causar resultados ligeiramente diferentes ao longo das corridas.

8. Envie o oleoduto.  
  
   A saída do módulo é um conjunto de dados que contém as linhas originais mais uma série de linhas adicionadas com casos minoritários.  

## <a name="technical-notes"></a>Observações técnicas

+ Quando estiver publicando um modelo que usa o módulo **SMOTE,** remova o **SMOTE** do pipeline preditivo antes de ser publicado como um serviço web. A razão é que o SMOTE destina-se a melhorar um modelo durante o treinamento, não para pontuação. Você pode obter um erro se um pipeline preditivo publicado contiver o módulo SMOTE.

+ Muitas vezes você pode obter melhores resultados se você limpar valores ausentes ou aplicar outras transformações para corrigir dados antes de aplicar o SMOTE. 

+ Alguns pesquisadores têm investigado se o SMOTE é eficaz em dados de alta dimensão ou esparsos, como dados usados na classificação de texto ou conjuntos de dados genômicos. Este artigo tem um bom resumo dos efeitos e da validade teórica da aplicação do SMOTE nesses casos: [Blagus e Lusa: SMOTE para dados de classe desequilibrada de alta dimensão.](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

+ Se o SMOTE não for eficaz no seu conjunto de dados, outras abordagens que você pode considerar incluem:
  + Métodos de superamostragem dos casos minoritários ou subamostragem dos casos majoritários.
  + Técnicas de conjunto que ajudam o aluno diretamente usando clustering, ensacar ou impulsionar adaptativos.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

