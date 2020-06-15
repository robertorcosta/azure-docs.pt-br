---
title: Converter palavra em vetor
titleSuffix: Azure Machine Learning
description: Saiba como usar três modelos do Word2Vec fornecidos para extrair um vocabulário e suas incorporações de palavras correspondentes de um corpus de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853648"
---
# <a name="convert-word-to-vector"></a>Converter palavra em vetor

Este artigo descreve como usar o módulo **Converter palavras em vetor** no designer do Azure Machine Learning (versão prévia), para aplicar vários modelos diferentes do Word2Vec (Word2Vec, FastText, modelo pré-treinado do Glove) no corpus de texto que você especificou como entrada e gerar um vocabulário com incorporações de palavras.

Esse módulo usa a biblioteca Gensim. Para obter mais informações sobre o Gensim, consulte o [site oficial](https://radimrehurek.com/gensim/apiref.html), que inclui tutoriais e explicações de algoritmos.

### <a name="more-about-convert-word-to-vector"></a>Mais sobre converter palavras em vetor

Em geral, a conversão de palavras em vetor, ou a vetorização de palavras, é um processo de processamento de idioma natural que usa modelos de linguagem ou técnicas para mapear palavras em um espaço vetorial, ou seja, representar cada palavra por um vetor de números reais e, enquanto isso, permite que palavras com significações semelhantes tenham representações semelhantes.

As incorporações de palavras podem ser usadas como entrada inicial para tarefas de downstream NLP, como classificação de texto, análise de sentimentos, etc.

Entre várias tecnologias de inserção de palavras, neste módulo implementamos três métodos amplamente usados, incluindo dois modelos de treinamento online, o Word2Vec e o FastText, e um modelo pré-treinado, love-wiki-gigaword-100. Os modelos de treinamento online são treinados em seus dados de entrada, enquanto os modelos pré-treinados são treinados offline em um corpus de texto maior, (por exemplo, Wikipédia, Google News) que geralmente contém cerca de 100.000.000.000 de palavras e, em seguida, a inserção de palavras permanece constante durante a vetorização de palavras. Modelos de palavras pré-treinados fornecem benefícios como tempo de treinamento reduzido, melhores vetores de palavras codificados e desempenho geral aprimorado.

+ O Word2Vec é uma das técnicas mais populares para aprender incorporações de palavras usando uma rede neural superficial. Saiba mais sobre esta teoria, que é discutida neste documento disponível para download em PDF: [Efficient Estimation of Word Representations in Vector Space, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). A implementação neste módulo é baseada na [biblioteca Gensim para Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ A teoria do FastText é explicada neste documento, disponível para download em PDF: [Enriching Word Vectors with Subword Information, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). A implementação neste módulo é baseada na [biblioteca Gensim para FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Modelo pré-treinado diferenciado: glove-wiki-gigaword-100 é uma coleção de vetores pré-treinados com base no corpus de texto da Wikipédia, que contém 5.6B de tokens e 400K de vocabulários sem análise. O PDF está disponível aqui: [GloVe: Global Vectors for Word Representation](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Como configurar a conversão de palavras em vetor

Este módulo requer um conjunto de dados que contém uma coluna de texto, sendo que o texto pré-processado é melhor.

1. Adicione o módulo **Converter palavras em vetor** em seu pipeline.

2. Como entrada para o módulo, forneça um conjunto de dados que contenha uma ou mais colunas de texto.

3. Para a **Coluna de destino**, escolha apenas uma das colunas contendo o texto a ser processado.

    Em geral, como esse módulo cria um vocabulário a partir do texto, o conteúdo de diferentes colunas é diferente, o que gera conteúdos de vocabulário diferentes, portanto, o módulo aceita apenas uma coluna de destino.

4. Para a **estratégia Word2Vec**, escolha entre `GloVe pretrained English Model`, `Gensim Word2Vec`e `Gensim FastText`.

5. Se a **estratégia Word2Vec** é `Gensim Word2Vec` ou `Gensim FastText`:

    + **Algoritmo de treinamento Word2Vec**. Escolha entre `Skip_gram` e `CBOW`. A diferença é apresentada no [artigo](https://arxiv.org/pdf/1301.3781.pdf).

        O método padrão é `Skip_gram`.

    + **Comprimento da inserção de palavras**. Especifique a dimensionalidade dos vetores de palavras. Corresponde ao parâmetro `size` na biblioteca Gensim.

        O embedding_size padrão é 100.

    + **Tamanho da janela de contexto**. Especifique a distância máxima entre a palavra que está sendo prevista e a palavra atual. Corresponde ao parâmetro `window` na biblioteca Gensim.

        O tamanho padrão da janela é 5.

    + **Número de épocas**. Especifique o número de épocas (iterações) em relação ao corpus. Corresponde ao parâmetro `iter` na biblioteca Gensim.

        O número de épocas padrão é 5.

6. Para **Tamanho máximo de vocabulário**, especifique o número máximo de palavras no vocabulário gerado.

    Se houver mais palavras exclusivas do que isso, remova as raras.

    O tamanho de vocabulário padrão é 10.000.

7. Para **Contagem de palavras mínima**, forneça uma contagem de palavras mínima, o que faz com que o módulo ignore todas as palavras que têm uma frequência menor do que esse valor.

    O valor padrão é 5.

8. Envie o pipeline.

## <a name="examples"></a>Exemplos

O módulo tem uma saída:

+ **Vocabulário com incorporações**: Contém o vocabulário gerado, junto com a inserção de cada palavra, sendo que uma dimensão ocupa uma coluna.

### <a name="result-examples"></a>Exemplos de resultados

Para ilustrar como o módulo **Converter palavras em vetor** funciona, o exemplo a seguir aplica esse módulo com as configurações padrão ao conjunto de módulos (em inglês) da Wikipédia SP 500, fornecido no Azure Machine Learning (versão prévia).

#### <a name="source-dataset"></a>Conjunto de dados de origem

O conjunto de conteúdo contém uma coluna de categoria, bem como o texto completo buscado da Wikipédia. Esta tabela mostra apenas alguns exemplos representativos.

|text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>Vocabulário de saída com incorporações

A tabela a seguir contém a saída desse módulo usando o conjunto de dados da Wikipédia SP 500 como entrada. A coluna mais à esquerda indica o vocabulário, seu vetor de inserção é representado pelos valores das colunas restantes na mesma linha.

|Vocabulário|Inserindo dim 0|Inserindo dim 1|Inserindo dim 2|Inserindo dim 3|Inserindo dim 4|Inserindo dim 5|...|Inserindo dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0,375865|0,609234|0,812797|-0,002236|0,319071|-0,591986|...|0,364276
|componente|0,081302|0,40001|0,121803|0,108181|0,043651|-0,091452|...|0,636587
|s|-0,34355|-0,037092|-0,012167|-0,151542|-0,601019|0,084501|...|0,149419
|p|-0,133407|0,073244|0,170396|0,326706|0,213463|-0,700355|...|-0,530901
fundação|-0,166819|-0,10883|-0,07933|-0,073753|0,262137|0,045725|...|0,27487
fundador|-0,297408|0,493067|0,316709|-0,031651|0,455416|-0,284208|...|0,22798
local|-0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|-0,460828|0,505516|-0,074294|-0,00639|0,116545|0,494368|...|-0,2403
apple|0,05779|0,672657|0,597267|-0,898889|0,099901|0,11833|...|0,4636
campus|-0,281835|0,29312|0,106966|-0,031385|0,100777|-0,061452|...|0,05978
infinito|-0,263074|0,245753|0,07058|-0,164666|0,162857|-0,027345|...|-0,0525
loop|-0,391421|0,52366|0,141503|-0,105423|0,084503|-0,018424|...|-0,0521

Neste exemplo, usamos o `Gensim Word2Vec` padrão como a **estratégia do Word2Vec**, o **Algoritmo de treinamento** é `Skip-gram`, o **Comprimento de inserção de palavras** é 100, portanto, temos 100 colunas de inserção.

## <a name="technical-notes"></a>Observações técnicas

Esta seção contém dicas e respostas para perguntas frequentes.

+ Diferença entre o modelo de treinamento online e o pré-treinado

    Neste módulo **Converter palavras em vetor**, fornecemos três estratégias diferentes, dois modelos de treinamento online e um modelo pré-treinado. O modelo de treinamento online usa o conjunto de dados de entrada como dado de treinamento, gera um vocabulário e vetores de palavras durante o treinamento, enquanto o modelo pré-treinado já é treinado por corpus de texto muito maior, como a Wikipédia ou o texto do Twitter, portanto, o modelo previamente treinado é, na verdade, uma coleção do par (palavra, inserção).  

    Se o modelo pré-treinado Glove for escolhido como estratégia de vetorização de palavras, ele resumirá um vocabulário do conjunto de dados de entrada e gerará o vetor de inserção para cada palavra do modelo pré-treinado, sem treinamento online. O uso do modelo pré-treinado poderá economizar tempo de treinamento e terá um melhor desempenho, especialmente quando o tamanho do conjunto de dados de entrada for relativamente pequeno.

+ Tamanho da inserção

    Em geral, o comprimento da inserção de palavras é definido em centenas (por exemplo, 100, 200, 300) para obter um bom desempenho, porque um tamanho pequeno de inserção significa ter espaço de vetor pequeno, o que pode causar colisões na inserção de palavras.  

    Para modelos pré-treinados, a duração das inserções de palavras é fixa. No caso desta implementação, o tamanho de inserção do glove-wiki-gigaword-100 é 100.


## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

Para obter uma lista de erros específicos para os módulos de designer (versão prévia), consulte [Códigos de erro do Machine Learning](designer-error-codes.md).
