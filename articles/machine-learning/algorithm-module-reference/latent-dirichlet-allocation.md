---
title: Alocação de Dirichlet latente
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de alocação Dirichlet latente para agrupar texto não classificado em várias categorias.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80109219"
---
# <a name="latent-dirichlet-allocation"></a>Alocação de Dirichlet latente

Este artigo descreve como usar o módulo de **alocação Dirichlet latente** no designer de Azure Machine Learning (versão prévia) para agrupar o texto não classificado em várias categorias. 

A alocação de Dirichlet latente (LDA) é geralmente usada no processamento de idioma natural (NLP) para localizar textos semelhantes. Outro termo comum é a *modelagem de tópico*.

Esse módulo usa uma coluna de texto e gera essas saídas:

+ O texto de origem, junto com uma pontuação para cada categoria

+ Uma matriz de recursos, contendo termos extraídos e coeficientes para cada categoria

+ Uma transformação, que você pode salvar e reaplicar ao novo texto usado como entrada

Esse módulo usa a biblioteca scikit-learn. Para obter mais informações sobre o scikit-learn, consulte o [repositório do GitHub, que inclui tutoriais e uma explicação do algoritmo.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Mais sobre a alocação de Dirichlet latente (LDA)

Em termos gerais, LDA não é um método de classificação por si, mas usa uma abordagem geração. Isso significa que você não precisa fornecer rótulos de classe conhecidos e, em seguida, inferir os padrões.  Em vez disso, o algoritmo gera um modelo probabilística que é usado para identificar grupos de tópicos. Você pode usar o modelo probabilística para classificar os casos de treinamento existentes ou os novos casos que você fornecer ao modelo como entrada.

Um modelo de geração pode ser preferível porque evita fazer qualquer suposição forte sobre a relação entre o texto e as categorias e usa apenas a distribuição de palavras para os tópicos de modelo matematica.

+ A teoria é discutida neste documento, disponível como um download de PDF: [alocação de Dirichlet latente: blei, NG e Jordânia](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ A implementação neste módulo é baseada na [biblioteca scikit-Learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) para Lda.

Para obter mais informações, consulte a seção [observações técnicas](#technical-notes) .

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Como configurar a alocação de Dirichlet latente

Este módulo requer um conjunto de um DataSet que contém uma coluna de texto, seja ele bruto ou pré-processado.

1. Adicione o módulo de **alocação de Dirichlet latente** ao pipeline.

2. Como entrada para o módulo, forneça um conjunto de dados que contenha uma ou mais colunas de texto.

3. Para **colunas de destino**, escolha uma ou mais colunas que contenham texto para analisar.

    Você pode escolher várias colunas, mas elas devem ser do tipo de dados de cadeia de caracteres.

    Em geral, como o LDA cria uma matriz de recursos grande do texto, você normalmente analisará uma única coluna de texto.

4. Para obter o **número de tópicos a serem modelados**, digite um inteiro entre 1 e 1000 que indica quantas categorias ou tópicos você deseja derivar do texto de entrada.

    Por padrão, são criados 5 tópicos.

5. Para **n-grams**, especifique o comprimento máximo de n-gramas gerados durante o hash.

    O padrão é 2, o que significa que bigrams e unigrams são gerados.

6. Selecione a opção **normalizar** para converter valores de saída em probabilidades. Portanto, em vez de representar os valores transformados como inteiros, os valores no conjunto de resultados de saída e de recurso seriam transformados da seguinte maneira:

    + Os valores no DataSet serão representados como uma probabilidade em `P(topic|document)`que.

    + Os valores na matriz de tópicos de recursos serão representados como uma `P(word|topic)`probabilidade de onde.

    > [!NOTE] 
    > No designer de Azure Machine Learning (versão prévia), como a biblioteca com base em scikit, não há mais suporte para a saída de *doc_topic_distr* não normalizada da versão 0,19, portanto, neste módulo, o parâmetro **Normalize** só pode ser aplicado à saída de matriz de **tópico de recurso** , a saída do conjunto de informações **transformado** é sempre normalizada.

7. Selecione a opção, **Mostrar todas as opções**e, em seguida, defina-a como true se você quiser exibir e, em seguida, definir parâmetros avançados adicionais.

    Esses parâmetros são específicos para a implementação scikit-Learn de LDA. Há alguns bons tutoriais sobre LDA no scikit-learn, bem como o [documento oficial de scikit-Learn](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parâmetro de Rô**. Forneça uma probabilidade anterior para a dispersão de distribuições de tópico. Corresponde ao parâmetro de `topic_word_prior` sklearn. Você usaria o valor 1 se espera que a distribuição de palavras seja simples; ou seja, assume-se todas as palavras equiprobable. Se você considerar que a maioria das palavras aparece de forma mais espessa, poderá defini-la como um valor muito menor.

    + **Parâmetro alfa**. Especifique uma probabilidade anterior para a dispersão de pesos de tópico por documento.  Corresponde ao parâmetro de `doc_topic_prior` sklearn.

    + **Número estimado de documentos**. Digite um número que represente a melhor estimativa do número de documentos (linhas) que será processado. Isso permite que o módulo aloque uma tabela de hash de tamanho suficiente.  Corresponde ao `total_samples` parâmetro em scikit-learn.

    + **Tamanho do lote**. Digite um número que indique quantas linhas incluir em cada lote de texto enviado ao modelo LDA. Corresponde ao `batch_size` parâmetro em scikit-learn.

    + **Valor inicial de iteração usado no agendamento de atualização de aprendizado**. Especifique o valor inicial que downweights a taxa de aprendizagem para iterações iniciais no aprendizado online. Corresponde ao `learning_offset` parâmetro em scikit-learn.

    + **Energia aplicada à iteração durante as atualizações**. Indique o nível de energia aplicado à contagem de iteração para controlar a taxa de aprendizado durante as atualizações online. Corresponde ao `learning_decay` parâmetro em scikit-learn.

    + **Número de passagens sobre os dados**. Especifique o número máximo de vezes que o algoritmo passará pelos dados. Corresponde ao `max_iter` parâmetro em scikit-learn.

8. Selecione a opção, **criar dicionário de ngrams** ou **criar dicionário de NGRAMS antes de Lda**, se você quiser criar a lista de n-grams em uma passagem inicial, antes de classificar o texto.

    Se você criar o dicionário inicial antecipadamente, poderá usar posteriormente o dicionário ao revisar o modelo. Ser capaz de mapear resultados para texto em vez de índices numéricos é geralmente mais fácil para interpretação. No entanto, salvar o dicionário levará mais tempo e usará armazenamento adicional.

9. Para o **tamanho máximo do dicionário ngram**, digite o número total de linhas que podem ser criadas no dicionário de n-Gram.

    Essa opção é útil para controlar o tamanho do dicionário. No entanto, se o número de ngrams na entrada exceder esse tamanho, poderão ocorrer colisões.

10. Envie o pipeline. O módulo LDA usa Bayes teorema para determinar quais tópicos podem ser associados a palavras individuais. As palavras não são associadas exclusivamente a tópicos ou grupos; em vez disso, cada n-Gram tem uma probabilidade aprendida de ser associada a qualquer uma das classes descobertas.

## <a name="results"></a>Resultados

O módulo tem duas saídas:

+ **Conjunto de dados transformado**: contém o texto de entrada e um número especificado de categorias descobertas, junto com as pontuações para cada exemplo de texto para cada categoria.

+ **Matriz de tópicos de recursos**: a coluna mais à esquerda contém o recurso de texto extraído e há uma coluna para cada categoria que contém a pontuação para esse recurso nessa categoria.


### <a name="lda-transformation"></a>Transformação de LDA

Esse módulo também gera a *transformação Lda* que aplica Lda ao conjunto de resultados.

Você pode salvar essa transformação registrando o conjunto de registros na guia **saídas + logs** no painel direito do módulo e reutilizá-lo para outros conjuntos de os. Isso pode ser útil se você tiver treinado em um grande corpus e quiser reutilizar os coeficientes ou categorias.

### <a name="refining-an-lda-model-or-results"></a>Refinando um modelo ou resultados do LDA

Normalmente, você não pode criar um único modelo LDA que atenderá a todas as necessidades, e até mesmo um modelo projetado para uma tarefa pode exigir muitas iterações para melhorar a precisão. Recomendamos que você experimente todos esses métodos para melhorar seu modelo:

+ Alterando os parâmetros do modelo
+ Usando a visualização para entender os resultados
+ Obter os comentários dos especialistas no assunto para verificar se os tópicos gerados são úteis.

As medidas qualitativas também podem ser úteis para avaliar os resultados. Para avaliar os resultados da modelagem de tópico, considere:

+ Exatidão – são itens semelhantes realmente semelhantes?
+ Diversidade – o modelo discriminar várias entre itens semelhantes quando necessário para o problema de negócios?
+ Escalabilidade-funciona em uma grande variedade de categorias de texto ou apenas em um domínio de destino estreito?

A precisão dos modelos baseados em LDA geralmente pode ser melhorada usando o processamento de linguagem natural para limpar, resumir e simplificar ou categorizar o texto. Por exemplo, as técnicas a seguir, todas com suporte no Azure Machine Learning, podem melhorar a precisão da classificação:

+ Remoção de palavra irrelevante (stop word)

+ Normalização de caso

+ Lematização ou lematização

+ Reconhecimento de entidade nomeada

Para obter mais informações, consulte [pré-processar Text](preprocess-text.md).

No designer, você também pode usar bibliotecas R ou Python para processamento de texto: [Executar script r](execute-r-script.md), [Executar script do Python](execute-python-script.md)



## <a name="technical-notes"></a>Observações técnicas

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="implementation-details"></a>Detalhes de implementação

Por padrão, as distribuições de saídas para o conjunto de recursos transformados e a matriz de tópicos de recurso são normalizadas como probabilidades.

+ O conjunto de dados transformado é normalizado como a probabilidade condicional de tópicos, dado um documento. Nesse caso, a soma de cada linha é igual a 1.

+ A matriz de recursos-tópico é normalizada como a probabilidade condicional de palavras, considerando um tópico. Nesse caso, a soma de cada coluna é igual a 1.

> [!TIP]
> Ocasionalmente, o módulo pode retornar um tópico vazio, que é causado com mais frequência pela inicialização pseudo aleatória do algoritmo.  Se isso acontecer, você poderá tentar alterar os parâmetros relacionados, como o tamanho máximo do dicionário de N-Gram ou o número de bits a serem usados para o hash de recurso.

### <a name="lda-and-topic-modeling"></a>LDA e modelagem de tópico

A alocação de Dirichlet latente (LDA) é geralmente usada para *modelagem de tópico com base em conteúdo*, o que basicamente significa categorias de aprendizado de texto não classificado. Na modelagem de tópico baseado em conteúdo, um tópico é uma distribuição sobre palavras.

Por exemplo, suponha que você tenha fornecido uma corpus de revisões de clientes que inclui muitos produtos, muitos. O texto das revisões que foram enviadas por muitos clientes ao longo do tempo contém muitos termos, alguns dos quais são usados em vários tópicos.

Um **tópico** que é identificado pelo processo Lda pode representar revisões para um produto individual ou pode representar um grupo de revisões de produtos. Para LDA, o tópico em si é apenas uma distribuição de probabilidade ao longo do tempo para um conjunto de palavras.

Os termos raramente são exclusivos para qualquer produto, mas podem consultar outros produtos ou ser termos gerais que se aplicam a tudo ("ótimo", "horrível"). Outros termos podem ser palavras de ruído.  No entanto, é importante entender que o método LDA não tem o propósito de capturar todas as palavras do universo ou entender como as palavras estão relacionadas, além das probabilidades de coocorrência. Ele só pode agrupar palavras que foram usadas no domínio de destino.

Depois que os índices de termo foram computados, linhas individuais de texto são comparadas usando uma medida de similaridade baseada em distância, para determinar se duas partes do texto são semelhantes.  Por exemplo, você pode achar que o produto tem vários nomes que estão fortemente correlacionados. Ou talvez você ache que termos fortemente negativos geralmente são associados a um produto específico. Você pode usar a medida de similaridade para identificar termos relacionados e para criar recomendações.

###  <a name="module-parameters"></a>Parâmetros do módulo

|Nome|Type|Intervalo|Opcional|Padrão|Descrição|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Coluna(s) de destino|Seleção de coluna||Obrigatório|StringFeature|Nome ou índice da coluna de destino|  
|Número de tópicos a serem modelados|Integer|[1; 1000]|Obrigatório|5|Modelar a distribuição de documentos em relação a N tópicos|  
|N-grams|Integer|[1; 10]|Obrigatório|2|Ordem de N-gramas gerados durante o hash|  
|Normaliza|Booliano|Verdadeiro ou Falso|Obrigatório|true|Normalizar a saída para probabilidades.  O conjunto de texto transformado será P (tópico&#124;documento) e a matriz do tópico do recurso será P (tópico&#124;do Word)|  
|Mostrar todas as opções|Booliano|Verdadeiro ou Falso|Obrigatório|Falso|Apresenta parâmetros adicionais específicos para scikit-saiba LDA online|  
|Parâmetro de Rô|Float|[0.00001; 1.0]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|0,01|Tópico palavra de distribuição anterior|  
|Parâmetro alfa|Float|[0.00001; 1.0]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|0,01|Tópico do documento distribuição anterior|  
|Número estimado de documentos|Integer|[1;int.MaxValue]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|1000|Número estimado de documentos (corresponde ao parâmetro total_samples)|  
|Tamanho do lote|Integer|[1; 1024]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|32|Tamanho do lote|  
|Valor inicial da iteração usada no agendamento de atualização da taxa de aprendizagem|Integer|[0; int. MaxValue|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|0|Valor inicial que downweights a taxa de aprendizagem para iterações iniciais. Corresponde ao parâmetro learning_offset|  
|Energia aplicada à iteração durante as atualizações|Float|[0,0; 1,0]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|0.5|Energia aplicada à contagem de iteração para controlar a taxa de aprendizado. Corresponde ao parâmetro learning_decay |  
|Número de iterações de treinamento|Integer|[1; 1024]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|25|Número de iterações de treinamento|  
|Criar dicionário de ngrams|Booliano|Verdadeiro ou Falso|Aplica-se quando a caixa de seleção **Mostrar todas as opções** *não* está marcada|verdadeiro|Cria um dicionário de ngrams antes de computar LDA. Útil para inspeção e interpretação de modelo|  
|Tamanho máximo do dicionário ngram|Integer|[1;int.MaxValue]|Aplica-se quando o **dicionário de compilação de opção de ngrams** é verdadeiro|20000|Tamanho máximo do dicionário ngrams. Se o número de tokens na entrada exceder esse tamanho, poderão ocorrer colisões|  
|Número de bits a serem usados para o hash de recurso|Integer|[1; 31]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** *não* está marcada e o **dicionário de compilação de ngrams** é false|12|Número de bits a serem usados para o hash de recurso| 
|Criar dicionário de ngrams antes de LDA|Booliano|Verdadeiro ou Falso|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada|verdadeiro|Cria um dicionário de ngrams antes de LDA. Útil para inspeção e interpretação de modelo|  
|Número máximo de ngrams no dicionário|Integer|[1;int.MaxValue]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada e o **dicionário de compilação de opção de ngrams** é verdadeiro|20000|Tamanho máximo do dicionário. Se o número de tokens na entrada exceder esse tamanho, poderão ocorrer colisões|  
|Número de bits de hash|Integer|[1; 31]|Aplica-se quando a caixa de seleção **Mostrar todas as opções** está marcada e o **dicionário de compilação de opção de ngrams** é false|12|Número de bits a serem usados durante o hash de recurso|   


## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.   
Para obter uma lista de erros específicos para os módulos, consulte [exceções e códigos de erro para o designer](designer-error-codes.md).
