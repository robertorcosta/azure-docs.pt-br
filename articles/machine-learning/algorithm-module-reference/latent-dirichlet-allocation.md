---
title: Alocação latente de Dirichlet
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Latent Dirichlet Allocation para agrupar texto não classificado em várias categorias.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109219"
---
# <a name="latent-dirichlet-allocation"></a>Alocação latente de Dirichlet

Este artigo descreve como usar o módulo **latente dirichlet allocation** no azure Machine Learning designer (preview), para agrupar texto não classificado em várias categorias. 

Latent Dirichlet Allocation (LDA) é frequentemente usado no processamento de linguagem natural (NLP) para encontrar textos semelhantes. Outro termo comum é *modelagem de tópicos.*

Este módulo pega uma coluna de texto e gera essas saídas:

+ O texto de origem, juntamente com uma pontuação para cada categoria

+ Uma matriz de recursos, contendo termos e coeficientes extraídos para cada categoria

+ Uma transformação, que você pode salvar e reaplicar ao novo texto usado como entrada

Este módulo usa a biblioteca scikit-learn. Para obter mais informações sobre o scikit-learn, consulte o repositório [GitHub, que inclui tutoriais e uma explicação do algoritmo.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Mais sobre alocação latente de dirichlet (LDA)

De um modo geral, o LDA não é um método de classificação em si, mas usa uma abordagem generativa. O que isso significa é que você não precisa fornecer rótulos de classe conhecidos e, em seguida, inferir os padrões.  Em vez disso, o algoritmo gera um modelo probabilístico que é usado para identificar grupos de tópicos. Você pode usar o modelo probabilístico para classificar casos de treinamento existentes ou novos casos que você fornece ao modelo como entrada.

Um modelo generativo pode ser preferível porque evita fazer quaisquer suposições fortes sobre a relação entre texto e categorias, e usa apenas a distribuição de palavras para modelar matematicamente temas.

+ A teoria é discutida neste artigo, disponível como um download em PDF: [Latent Dirichlet Allocation: Blei, Ng, and Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ A implementação neste módulo é baseada na [biblioteca scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) para LDA.

Para obter mais informações, consulte a seção [Notas Técnicas.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Como configurar a alocação latente de Dirichlet

Este módulo requer um conjunto de dados que contenha uma coluna de texto, cru ou pré-processado.

1. Adicione o módulo **de alocação de dirichlet latente** ao seu pipeline.

2. Como entrada para o módulo, forneça um conjunto de dados contendo uma ou mais colunas de texto.

3. Para **colunas Target,** escolha uma ou mais colunas contendo texto para analisar.

    Você pode escolher várias colunas, mas elas devem ser do tipo de dados de seqüência.

    Em geral, como o LDA cria uma grande matriz de recursos a partir do texto, você normalmente analisará uma única coluna de texto.

4. Para **número de tópicos para modelar,** digite um inteiro entre 1 e 1000 que indique quantas categorias ou tópicos você deseja derivar do texto de entrada.

    Por padrão, 5 tópicos são criados.

5. Para **N-grams,** especifique o comprimento máximo de N-gramas gerado durante o hash.

    O padrão é 2, o que significa que tanto bigrams quanto unigramas são gerados.

6. Selecione a opção **Normalizar** para converter valores de saída em probabilidades. Portanto, ao invés de representar os valores transformados como inteiros, os valores no conjunto de dados de saída e recurso seriam transformados da seguinte forma:

    + Os valores no conjunto de dados `P(topic|document)`serão representados como uma probabilidade onde .

    + Os valores na matriz do tópico de `P(word|topic)`recurso serão representados como uma probabilidade onde .

    > [!NOTE] 
    > No azure Machine Learning designer (visualização), porque a biblioteca que baseamos, scikit-learn, não suporta mais saída *de doc_topic_distr* não normalizada da versão 0.19, portanto, neste módulo, **normalizar** parâmetro só pode ser aplicado à saída **da matriz Feature Topic,** a saída do conjunto de dados **transformada** é sempre normalizada.

7. Selecione a opção, **Mostre todas as opções**e defina-a como TRUE se quiser visualizar e, em seguida, defina parâmetros avançados adicionais.

    Esses parâmetros são específicos para a implementação scikit-learn do LDA. Existem alguns bons tutoriais sobre LDA no scikit-learn, bem como o documento oficial [scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parâmetro rho**. Fornecer uma probabilidade prévia para a esparsidade das distribuições de tópicos. Corresponde ao parâmetro de `topic_word_prior` Sklearn. Você usaria o valor 1 se esperasse que a distribuição das palavras fosse plana; ou seja, todas as palavras são presumidas equiprováveis. Se você acha que a maioria das palavras aparecem com pouco tempo, você pode defini-la para um valor muito menor.

    + **Parâmetro alfa.** Especifique uma probabilidade prévia para a esparsidade dos pesos tópicos por documento.  Corresponde ao parâmetro de `doc_topic_prior` Sklearn.

    + **Número estimado de documentos**. Digite um número que represente a sua melhor estimativa do número de documentos (linhas) que serão processados. Isso permite que o módulo aloque uma tabela hash de tamanho suficiente.  Corresponde ao `total_samples` parâmetro no scikit-learn.

    + **Tamanho do lote**. Digite um número que indique quantas linhas incluir em cada lote de texto enviado ao modelo LDA. Corresponde ao `batch_size` parâmetro no scikit-learn.

    + **Valor inicial da iteração usada no cronograma de atualização de aprendizagem**. Especifique o valor inicial que diminui a taxa de aprendizagem para iterações precoces no aprendizado online. Corresponde ao `learning_offset` parâmetro no scikit-learn.

    + **Energia aplicada à iteração durante as atualizações**. Indique o nível de potência aplicado à contagem de iterações para controlar a taxa de aprendizado durante as atualizações on-line. Corresponde ao `learning_decay` parâmetro no scikit-learn.

    + **Número de passes sobre os dados**. Especifique o número máximo de vezes que o algoritmo irá ciclo sobre os dados. Corresponde ao `max_iter` parâmetro no scikit-learn.

8. Selecione a opção, **Crie dicionário de ngrams** ou **Construa dicionário de ngrams antes do LDA**, se você quiser criar a lista de n-gram em um passe inicial, antes de classificar o texto.

    Se você criar o dicionário inicial de antemão, você pode usar o dicionário mais tarde ao revisar o modelo. Ser capaz de mapear resultados para texto em vez de índices numéricos é geralmente mais fácil de interpretação. No entanto, salvar o dicionário levará mais tempo e usará armazenamento adicional.

9. Para **o tamanho máximo do dicionário ngram,** digite o número total de linhas que podem ser criadas no dicionário n-gram.

    Esta opção é útil para controlar o tamanho do dicionário. No entanto, se o número de ngramas na entrada exceder esse tamanho, podem ocorrer colisões.

10. Envie o oleoduto. O módulo LDA usa o teorema de Bayes para determinar quais tópicos podem estar associados a palavras individuais. As palavras não estão exclusivamente associadas a nenhum tópico ou grupo; em vez disso, cada n-grama tem uma probabilidade aprendida de ser associado a qualquer uma das classes descobertas.

## <a name="results"></a>Resultados

O módulo tem duas saídas:

+ **Conjunto de dados transformado**: Contém o texto de entrada e um número especificado de categorias descobertas, juntamente com as pontuações de cada exemplo de texto para cada categoria.

+ **Matriz de tópicos de recurso**: A coluna mais à esquerda contém o recurso de texto extraído, e há uma coluna para cada categoria contendo a pontuação para esse recurso nessa categoria.


### <a name="lda-transformation"></a>Transformação de LDA

Este módulo também produz a *transformação LDA* que aplica O LDA ao conjunto de dados.

Você pode salvar essa transformação registrando conjunto de dados na guia **Saídas+logs** no painel direito do módulo e reutilizá-la para outros conjuntos de dados. Isso pode ser útil se você tiver treinado em um grande corpus e quiser reutilizar os coeficientes ou categorias.

### <a name="refining-an-lda-model-or-results"></a>Refinando um modelo ou resultados LDA

Normalmente, você não pode criar um único modelo LDA que atenda a todas as necessidades, e mesmo um modelo projetado para uma tarefa pode exigir muitas iterações para melhorar a precisão. Recomendamos que você experimente todos esses métodos para melhorar o seu modelo:

+ Alterando os parâmetros do modelo
+ Usando a visualização para entender os resultados
+ Obter o feedback de especialistas do assunto para verificar se os tópicos gerados são úteis.

Medidas qualitativas também podem ser úteis para avaliar os resultados. Para avaliar os resultados da modelagem de tópicos, considere:

+ Precisão - Itens similares são realmente semelhantes?
+ Diversidade - O modelo pode discriminar itens semelhantes quando necessário para o problema do negócio?
+ Escalabilidade - Funciona em uma ampla gama de categorias de texto ou apenas em um domínio de destino estreito?

A precisão dos modelos baseados em LDA pode muitas vezes ser melhorada usando processamento de linguagem natural para limpar, resumir e simplificar ou categorizar o texto. Por exemplo, as seguintes técnicas, todas suportadas no Azure Machine Learning, podem melhorar a precisão da classificação:

+ Remoção de palavra irrelevante (stop word)

+ Normalização de casos

+ Lemmatização ou decorrente

+ Reconhecimento de entidade nomeada

Para obter mais informações, consulte [Texto de pré-processo](preprocess-text.md).

No designer, você também pode usar bibliotecas R ou Python para processamento de texto: [Execute R Script](execute-r-script.md), Execute Script [Python](execute-python-script.md)



## <a name="technical-notes"></a>Observações técnicas

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="implementation-details"></a>Detalhes de implementação

Por padrão, as distribuições das saídas para conjunto de dados transformado e matriz de tópicos de recurso são normalizadas como probabilidades.

+ O conjunto de dados transformado é normalizado como a probabilidade condicional dos tópicos dados por um documento. Neste caso, a soma de cada linha é igual a 1.

+ A matriz de recurso-tópico é normalizada como a probabilidade condicional das palavras dadas a um tópico. Neste caso, a soma de cada coluna é igual a 1.

> [!TIP]
> Ocasionalmente, o módulo pode retornar um tópico vazio, que é mais frequentemente causado pela inicialização pseudo-aleatória do algoritmo.  Se isso acontecer, você pode tentar alterar parâmetros relacionados, como o tamanho máximo do dicionário N-gram ou o número de bits para usar para hashing de recurso.

### <a name="lda-and-topic-modeling"></a>LDA e modelagem de tópicos

Latent Dirichlet Allocation (LDA) é frequentemente usado para *modelagem de tópicos baseados em conteúdo,* o que basicamente significa categorias de aprendizagem a partir de texto não classificado. Na modelagem de tópicos baseada em conteúdo, um tópico é uma distribuição sobre palavras.

Por exemplo, suponha que você forneceu um corpus de avaliações de clientes que inclui muitos, muitos produtos. O texto de revisões que foram enviados por muitos clientes ao longo do tempo conteria muitos termos, alguns dos quais são usados em vários tópicos.

Um **tópico** identificado pelo processo LDA pode representar avaliações para um produto a cada um, ou pode representar um grupo de avaliações de produtos. Para o LDA, o tópico em si é apenas uma distribuição de probabilidade ao longo do tempo para um conjunto de palavras.

Os termos raramente são exclusivos de qualquer produto, mas podem se referir a outros produtos, ou serem termos gerais que se aplicam a tudo ("ótimo", "horrível"). Outros termos podem ser palavras de ruído.  No entanto, é importante entender que o método LDA não pretende capturar todas as palavras no universo, ou entender como as palavras estão relacionadas, além das probabilidades de co-ocorrência. Ele só pode agrupar palavras que foram usadas no domínio de destino.

Após a computação dos índices de termo, as linhas individuais de texto são comparadas usando uma medida de similaridade baseada na distância, para determinar se duas partes de texto são parecidas entre si.  Por exemplo, você pode descobrir que o produto tem vários nomes que estão fortemente correlacionados. Ou, você pode descobrir que termos fortemente negativos são geralmente associados a um determinado produto. Você pode usar a medida de similaridade tanto para identificar termos relacionados quanto para criar recomendações.

###  <a name="module-parameters"></a>Parâmetros do módulo

|Nome|Type|Intervalo|Opcional|Padrão|Descrição|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Coluna(s) de destino|Seleção de coluna||Obrigatório|StringFeature|Nome ou índice da coluna de destino|  
|Número de tópicos para modelar|Integer|[1;1000]|Obrigatório|5|Modele a distribuição de documentos em relação aos tópicos N|  
|N-grams|Integer|[1;10]|Obrigatório|2|Ordem de N-grams gerada durante o hash|  
|Normalizar|Boolean|Verdadeiro ou Falso|Obrigatório|true|Normalize a saída para probabilidades.  O conjunto de dados transformado será P (tópico&#124;documento) e a matriz de tópicos de recurso será P (palavra&#124;tópico)|  
|Mostrar todas as opções|Boolean|Verdadeiro ou Falso|Obrigatório|Falso|Apresenta parâmetros adicionais específicos para o Scikit-Learn Online LDA|  
|Parâmetro de Rho|Float|[0.00001;1.0]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|0,01|Distribuição prévia da palavra tópico|  
|Parâmetro alfa|Float|[0.00001;1.0]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|0,01|Distribuição prévia do tópico do documento|  
|Número estimado de documentos|Integer|[1;int.MaxValue]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|1000|Número estimado de documentos (Corresponde a total_samples parâmetro)|  
|Tamanho do lote|Integer|[1;1024]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|32|Tamanho do lote|  
|Valor inicial da iteração usada no cronograma de atualização da taxa de aprendizagem|Integer|[0;int. MaxValue]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|0|Valor inicial que diminui a taxa de aprendizagem para iterações precoces. Corresponde ao parâmetro learning_offset|  
|Energia aplicada à iteração durante atualizações|Float|[0.0;1.0]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|0.5|Poder aplicado à contagem de iteração para controlar a taxa de aprendizagem. Corresponde ao parâmetro learning_decay |  
|Número de iterações de treinamento|Integer|[1;1024]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|25|Número de iterações de treinamento|  
|Construir dicionário de ngrams|Boolean|Verdadeiro ou Falso|Aplica-se quando a caixa de seleção **Mostrar todas as opções** *não* for selecionada|True|Constrói um dicionário de ngrams antes de computar lDA. Útil para inspeção e interpretação de modelos|  
|Tamanho máximo do dicionário ngram|Integer|[1;int.MaxValue]|Aplica-se quando a opção **Construir dicionário de ngrams** é Verdadeira|20000|Tamanho máximo do dicionário ngrams. Se o número de tokens na entrada exceder esse tamanho, podem ocorrer colisões|  
|Número de bits para usar para hashing de recursos|Integer|[1;31]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** *não* for selecionada e construir dicionário **de ngrams** é falso|12|Número de bits para usar para hashing de recursos| 
|Construir dicionário de ngrams antes do LDA|Boolean|Verdadeiro ou Falso|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada|True|Constrói um dicionário de ngrams antes do LDA. Útil para inspeção e interpretação de modelos|  
|Número máximo de ngramas no dicionário|Integer|[1;int.MaxValue]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada e a opção **Construir dicionário de ngrams** é True|20000|Tamanho máximo do dicionário. Se o número de tokens na entrada exceder esse tamanho, podem ocorrer colisões|  
|Número de bits de hash|Integer|[1;31]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** é selecionada e a opção **Construir dicionário de ngrams** é falsa|12|Número de bits para usar durante o hashing do recurso|   


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.   
Para obter uma lista de erros específicos dos módulos, consulte [Exceções e códigos de erro para o designer](designer-error-codes.md).
