---
title: Solucionar problemas de erros do módulo
titleSuffix: Azure Machine Learning
description: Solucionar as exceções do módulo no azure Machine Learning designer usando códigos de erro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/03/2019
ms.openlocfilehash: cda499b81a61a5b78ca86a96372640e368f90357
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364201"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Exceções e códigos de erro para o designer (visualização)

Este artigo descreve as mensagens de erro e os códigos de exceção no azure Machine Learning designer (visualização) para ajudá-lo a solucionar problemas em seus pipelines de aprendizado de máquina.

Há duas maneiras de obter o texto completo de uma mensagem de erro no designer:  

- Clique no link, **Exibir registro de**saída , no painel direito e role até a parte inferior. A mensagem de erro detalhada é exibida nas duas últimas linhas da janela.  
  
- Selecione o módulo que tem o erro e clique no X vermelho. Apenas o texto de erro pertinente é exibido.

## <a name="error-0001"></a>Erro 0001  
 Ocorrerá uma exceção se uma ou mais das colunas especificadas do conjunto de dados não puder ser encontrada.  

 Você receberá este erro se uma seleção de coluna for feita para um módulo, mas as colunas selecionadas não existirão no conjunto de dados de entrada. Esse erro pode ocorrer se você tiver digitado manualmente um nome de coluna ou se o seletor de coluna tiver fornecido uma coluna sugerida que não existia no seu conjunto de dados quando você executou o pipeline.  

**Resolução:** Revisite o módulo lançando essa exceção e valide se o nome ou nomes da coluna estão corretos e que todas as colunas referenciadas existem.  

|Mensagens de Exceção|
|------------------------|
|Não foram encontradas uma ou mais colunas especificadas.|
|Coluna com nome ou índice "{column_id}" não encontrado.|
|A coluna com nome ou índice "{column_id}" não existe em "{arg_name_missing_column}".|
|A coluna com nome ou índice "{column_id}" não existe em "{arg_name_missing_column}", mas existe em "{arg_name_has_column}".|
|Colunas com nome ou índice "{column_names}" não encontrados.|
|Colunas com nome ou índice "{column_names}" não existem em "{arg_name_missing_column}".|
|Colunas com nome ou índice "{column_names}" não existem em "{arg_name_missing_column}", mas existem em "{arg_name_has_column}".|


## <a name="error-0002"></a>Erro 0002  
 Ocorrerá uma exceção se um ou mais parâmetros não puder ser analisado ou convertido do tipo especificado no exigido pelo tipo de método de destino.  

 Esse erro ocorre no Azure Machine Learning quando você especifica um parâmetro como entrada e o tipo de valor é diferente do tipo esperado, e a conversão implícita não pode ser realizada.  

**Resolução:** Verifique os requisitos do módulo e determine qual tipo de valor é necessário (string, inteiro, duplo, etc.)  

|Mensagens de Exceção|
|------------------------|
|Não parem os parâmetros.|
|Não conseguiu analisar o parâmetro "{arg_name_or_column}".|
|Não conseguiu converter o parâmetro "{arg_name_or_column}" em "{to_type}".|
|Não conseguiu converter o parâmetro "{arg_name_or_column}" de "{from_type}" para "{to_type}".|
|Falha ao converter o valor do parâmetro "{arg_name_or_column}" "{arg_value}" de "{from_type}" para "{to_type}".|
|Não foi convertido o valor "{arg_value}" na coluna "{arg_name_or_column}" de "{from_type}" para "{to_type}" com o uso do formato "{fmt}" fornecido.|


## <a name="error-0003"></a>Erro 0003  
 Ocorrerá uma exceção se uma ou mais das entradas for nula ou estiver vazia.  

 Você receberá este erro no Azure Machine Learning se quaisquer entradas ou parâmetros para um módulo estiverem nulos ou vazios.  Esse erro pode ocorrer, por exemplo, quando você não digitou nenhum valor para um parâmetro. Também pode acontecer se você escolher um conjunto de dados que tenha valores ausentes ou um conjunto de dados vazio.  

**Solução:**

+ Abra o módulo que produziu a exceção e verifique se todas as entradas foram especificadas. Certifique-se de que todas as entradas necessárias sejam especificadas. 
+ Certifique-se de que os dados carregados do armazenamento do Azure estão acessíveis e que o nome ou a chave da conta não foram alterados.  
+ Verifique os dados de entrada para valores faltantes ou nulos.
+ Se usar uma consulta em uma fonte de dados, verifique se os dados estão sendo retornados no formato que você espera. 
+ Verifique se há erros de digitação ou outras alterações na especificação dos dados.
  
|Mensagens de Exceção|
|------------------------|
|Uma ou mais entradas são nulas ou vazias.|
|A entrada "{name}" é nula ou vazia.|


## <a name="error-0004"></a>Erro 0004  
 Ocorrerá uma exceção se o parâmetro for menor ou igual ao valor específico.  

 Você receberá este erro no Azure Machine Learning se o parâmetro na mensagem estiver abaixo de um valor de limite necessário para que o módulo processe os dados.  

**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro para ser maior do que o valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior que o valor do limite.|
|O valor do parâmetro "{arg_name}" deve ser maior do que {lower_boundary}.|
|Parâmetro "{arg_name}" tem valor "{actual_value}" que deve ser maior do que {lower_boundary}.|


## <a name="error-0005"></a>Erro 0005  
 Ocorrerá uma exceção se o parâmetro for menor que um valor específico.  

 Você receberá este erro no Azure Machine Learning se o parâmetro na mensagem estiver abaixo ou igual a um valor de limite necessário para que o módulo processe os dados.  

**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro para ser maior ou igual ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior ou igual ao valor do limite.|
|O valor do parâmetro "{arg_name}" deve ser maior ou igual a {lower_boundary}.|
|Parâmetro "{arg_name}" tem valor "{value}" que deve ser maior ou igual a {lower_boundary}.|


## <a name="error-0006"></a>Erro 0006  
 Ocorrerá uma exceção se o parâmetro for maior ou igual ao valor especificado.  

 Você receberá este erro no Azure Machine Learning se o parâmetro na mensagem for maior ou igual a um valor de limite necessário para que o módulo processe os dados.  

**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro a ser inferior ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|Incompatibilidade de parâmetros. Um dos parâmetros deve ser menor que o outro.|
|O valor do parâmetro "{arg_name}" deve ser menor do que o parâmetro "{upper_boundary_parameter_name}".|
|Parâmetro "{arg_name}" tem valor "{value}" que deve ser menor que {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Erro 0007  
 Ocorrerá uma exceção se o parâmetro for maior que um valor específico.  

 Você receberá este erro no Azure Machine Learning se, nas propriedades do módulo, você especificar um valor maior do que o permitido. Por exemplo, você pode especificar um dado que está fora do intervalo de datas suportadas ou pode indicar que cinco colunas serão usadas quando apenas três colunas estiverem disponíveis. 

 Você também pode ver esse erro se estiver especificando dois conjuntos de dados que precisam corresponder de alguma forma. Por exemplo, se você estiver renomeando colunas e especificando as colunas por índice, o número de nomes fornecidos deve corresponder ao número de índices de colunas. Outro exemplo pode ser uma operação matemática que usa duas colunas, onde as colunas devem ter o mesmo número de linhas. 

**Solução:**

 + Abra o módulo em questão e revise as configurações numéricas de propriedade.
 + Certifique-se de que os valores dos parâmetros se enquadram na faixa de valores suportada para essa propriedade.
 + Se o módulo tiver várias entradas, certifique-se de que as entradas são do mesmo tamanho.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Verifique se o conjunto de dados ou a fonte de dados foi alterado. Às vezes, um valor que funcionou com uma versão anterior dos dados falhará após o número de colunas, os tipos de dados da coluna ou o tamanho dos dados ter sido alterado.  

|Mensagens de exceção|
|------------------------|
|Incompatibilidade de parâmetros. Um dos parâmetros deve ser menor ou igual a outro.|
|O valor do parâmetro "{arg_name}" deve ser menor ou igual ao valor do parâmetro "{upper_boundary_parameter_name}".|
|O parâmetro "{arg_name}" tem o valor "{actual_value}" que deve ser menor ou igual a {upper_boundary}.|
|O valor do parâmetro "{arg_name}" {actual_value} deve ser menor ou igual ao parâmetro "{upper_boundary_parameter_name}" valor {upper_boundary}.|


## <a name="error-0008"></a>Erro 0008  
 Ocorrerá uma exceção se o parâmetro não estiver no intervalo.  

 Você receberá este erro no Azure Machine Learning se o parâmetro na mensagem estiver fora dos limites necessários para que o módulo processe os dados.  

 Por exemplo, esse erro é exibido se você tentar usar Adicionar linhas para combinar dois [conjuntos](add-rows.md) de dados que têm um número diferente de colunas.  

**Resolução:** Revisite o módulo lançando a exceção e modifique o parâmetro a ser dentro do intervalo especificado.  

|Mensagens de Exceção|
|------------------------|
|O valor do parâmetro não está no intervalo especificado.|
|O valor do parâmetro "{arg_name}" não está no intervalo.|
|O valor do parâmetro "{arg_name}" deve estar na faixa de [{lower_boundary}, {upper_boundary}].|
|O valor do parâmetro "{arg_name}" não está no intervalo. {razão}|


## <a name="error-0009"></a>Erro 0009  
 A exceção ocorre quando o nome da conta de armazenamento do Azure ou o nome do contêiner são especificados incorretamente.  

Esse erro ocorre no designer azure Machine Learning quando você especifica parâmetros para uma conta de armazenamento Do Zure, mas o nome ou senha não pode ser resolvido. Erros em senhas ou nomes de contas podem acontecer por muitas razões:

 + A conta é do tipo errado. Alguns novos tipos de conta não são suportados para uso com designer de Machine Learning. Consulte [Dados de importação](import-data.md) para obter detalhes.
 + Você inseriu o nome da conta incorreta
 + A conta não existe mais
 + A senha da conta de armazenamento está errada ou mudou
 + Você não especificou o nome do contêiner, ou o recipiente não existe
 + Você não especificou totalmente o caminho do arquivo (caminho para a bolha)
   

**Solução:**

Esses problemas geralmente ocorrem quando você tenta digitar manualmente o nome da conta, senha ou caminho do contêiner. Recomendamos que você use o novo assistente para o módulo [Dados de Importação,](import-data.md) que ajuda você a olhar para cima e verificar nomes.

Verifique também se a conta, o contêiner ou a bolha foram excluídos. Use outro utilitário de armazenamento do Azure para verificar se o nome e a senha da conta foram inseridos corretamente e que o contêiner existe. 

Alguns tipos de conta mais novos não são suportados pelo Azure Machine Learning. Por exemplo, os novos tipos de armazenamento "quente" ou "frio" não podem ser usados para aprendizado de máquina. Tanto contas de armazenamento clássicas quanto contas de armazenamento criadas como "propósito geral" funcionam bem.

Se o caminho completo para uma bolha foi especificado, verifique se o caminho está especificado como **contêiner/blobname**e se o contêiner e a bolha existem na conta.  

 O caminho não deve conter uma barra principal. Por **exemplo/ o recipiente/bolha** está incorreto e deve ser inserido como **recipiente/bolha**.  


|Mensagens de Exceção|
|------------------------|
|O nome da conta de armazenamento do Azure ou o nome do contêiner está incorreto.|
|O nome da conta de armazenamento do Azure "{account_name}" ou o nome do contêiner "{container_name}" está incorreto; Era esperado um nome de contêiner do formato container/blob.|


## <a name="error-0010"></a>Erro 0010  
 Ocorrerá uma exceção se os conjuntos de dados da entrada tiverem nomes de colunas que deveriam corresponder, mas não correspondem.  

 Você receberá este erro no Azure Machine Learning se o índice da coluna na mensagem tiver nomes de coluna diferentes nos dois conjuntos de dados de entrada.  

**Resolução:** Use [Editar metadados](edit-metadata.md) ou modificar o conjunto de dados original para ter o mesmo nome da coluna para o índice de coluna especificado.  

|Mensagens de Exceção|
|------------------------|
|As colunas de índice correspondente em conjuntos de dados de entrada têm nomes diferentes.|
|Os nomes das colunas não são os mesmos para a coluna {col_index} (baseado em zero) dos conjuntos de dados de entrada ({dataset1} e {dataset2} respectivamente).|


## <a name="error-0011"></a>Erro 0011  
 Ocorrerá uma exceção se o argumento de conjunto de colunas passado não se aplicar a qualquer uma das colunas do conjunto de dados.  

 Você receberá este erro no Azure Machine Learning se a seleção de coluna especificada não corresponder a nenhuma das colunas no conjunto de dados dado.  

 Você também pode obter esse erro se você não tiver selecionado uma coluna e pelo menos uma coluna for necessária para que o módulo funcione.  

**Resolução:** Modifique a seleção da coluna no módulo para que ela se aplique às colunas no conjunto de dados.  

 Se o módulo exigir que você selecione uma coluna específica, como uma coluna de rótulo, verifique se a coluna direita está selecionada.  

 Se forem selecionadas colunas inadequadas, remova-as e execute o pipeline.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas especificado não se aplica a qualquer uma das colunas do conjunto de dados.|
|O conjunto de colunas especificado "{column_set}" não se aplica a nenhuma das colunas do conjunto de dados.|


## <a name="error-0012"></a>Erro 0012  
 Ocorrerá uma exceção se não foi possível criar instância da classe com o conjunto de argumentos passado.  

**Resolução:** Este erro não é acionável pelo usuário e será preterido em uma versão futura.  

|Mensagens de Exceção|
|------------------------|
|Modelo não treinado; treine primeiro o modelo.|
|Modelo não treinado ({arg_name}), use modelo treinado.|


## <a name="error-0013"></a>Erro 0013  
 A exceção ocorre se o aluno passado para o módulo for um tipo inválido.  

 Este erro ocorre sempre que um modelo treinado é incompatível com o módulo de pontuação conectado. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Solução:**

Determine o tipo de aluno que é produzido pelo módulo de treinamento e determine o módulo de pontuação apropriado para o aluno. 

Se o modelo foi treinado utilizando qualquer um dos módulos de treinamento especializados, conecte o modelo treinado apenas ao módulo de pontuação especializado correspondente. 


|Tipo de modelo|Módulo de treinamento| Módulo de pontuação|
|----|----|----|
|qualquer classificador|[Treinar Modelo](train-model.md) |[Pontuar Modelo](score-model.md)|
|qualquer modelo de regressão|[Treinar Modelo](train-model.md) |[Pontuar Modelo](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Mensagens de Exceção|
|------------------------|
|Aprendiz de tipo inválido foi passado.|
|O aluno "{arg_name}" tem tipo inválido.|
|O aluno "{arg_name}" tem o tipo inválido "{learner_type}".|


## <a name="error-0014"></a>Erro 0014  
 A exceção ocorre se a contagem de valores únicos da coluna for maior do que o permitido.  

 Este erro ocorre quando uma coluna contém muitos valores únicos.  Por exemplo, você pode ver esse erro se especificar que uma coluna será tratada como dados categóricos, mas há muitos valores únicos na coluna para permitir que o processamento seja concluído. Você também pode ver esse erro se houver uma incompatibilidade entre o número de valores únicos em duas entradas.   

**Solução:**

Abra o módulo que gerou o erro e identifique as colunas usadas como entradas. Para alguns módulos, você pode clicar com o botão direito do mouse na entrada do conjunto de dados e selecionar **Visualizar** para obter estatísticas em colunas individuais, incluindo o número de valores únicos e sua distribuição.

Para colunas que você pretende usar para agrupamento ou categorização, tome medidas para reduzir o número de valores únicos em colunas. Você pode reduzir de diferentes maneiras, dependendo do tipo de dados da coluna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Incapaz de encontrar uma resolução que corresponda ao seu cenário? Você pode fornecer feedback sobre este tópico que inclui o nome do módulo que gerou o erro, e o tipo de dados e cardinalidade da coluna. Usaremos as informações para fornecer etapas mais direcionadas para solução de problemas para cenários comuns.   

|Mensagens de Exceção|
|------------------------|
|A quantidade de valores exclusivos da coluna é maior que o permitido.|
|Número de valores únicos na coluna: "{column_name}" é maior do que o permitido.|
|Número de valores únicos na coluna: "{column_name}" excede a contagem tupla da {limitação}.|


## <a name="error-0015"></a>Erro 0015  
 Ocorrerá uma exceção se a conexão do banco de dados tiver falhado.  

 Você receberá esse erro se inserir um nome de conta SQL incorreto, senha, servidor de banco de dados ou nome do banco de dados, ou se uma conexão com o banco de dados não puder ser estabelecida devido a problemas com o banco de dados ou servidor.  

**Resolução:** Verifique se o nome da conta, a senha, o servidor de banco de dados e o banco de dados foram inseridos corretamente e que a conta especificada tem o nível correto de permissões. Verifique se o banco de dados está acessível no momento.  

|Mensagens de Exceção|
|------------------------|
|Erro ao estabelecer conexão com o banco de dados.|
|Conexão de banco de dados de erro: {connection_str}.|


## <a name="error-0016"></a>Erro 0016  
 Ocorrerá uma exceção se os conjuntos de dados de entrada passados para o módulo devessem ter tipos de coluna compatíveis, mas não tiverem.  

 Você receberá este erro no Azure Machine Learning se os tipos de colunas passadas em dois ou mais conjuntos de dados não forem compatíveis entre si.  

**Resolução:** Use [Editar metadados](edit-metadata.md) ou modificar o conjunto de dados de entrada original<!--, or use [Convert to Dataset](convert-to-dataset.md)--> para garantir que os tipos das colunas sejam compatíveis.  

|Mensagens de Exceção|
|------------------------|
|As colunas com índice correspondente em conjuntos de dados de entrada têm tipos incompatíveis.|
|As colunas '{first_col_names}' são incompatíveis entre os dados do trem e do teste.|
|As colunas '{first_col_names}' e '{second_col_names}' são incompatíveis.|
|Os tipos de elementos da coluna não são compatíveis para a coluna '{first_col_names}' (baseado em zero) dos conjuntos de dados de entrada ({first_dataset_names} e {second_dataset_names} respectivamente).|


## <a name="error-0017"></a>Erro 0017  
 A exceção ocorre se uma coluna selecionada usar um tipo de dados que não é suportado pelo módulo atual.  

 Por exemplo, você pode receber esse erro no Azure Machine Learning se a seleção da coluna incluir uma coluna com um tipo de dados que não pode ser processado pelo módulo, como uma coluna de strings para uma operação de matemática ou uma coluna de pontuação onde uma coluna de recurso categórica é Necessário.  

**Solução:**
 1. Identifique a coluna que é o problema.
 2. Revise os requisitos do módulo.
 3. Modifique a coluna para que ela esteja em conformidade com os requisitos. Você pode precisar usar vários dos seguintes módulos para fazer alterações, dependendo da coluna e da conversão que você está tentando:
    + Use [Editar metadados](edit-metadata.md) para alterar o tipo de dados das colunas ou para alterar o uso da coluna de recurso para numérico, categórico para não categórico, e assim por diante.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Como último recurso, talvez seja necessário modificar o conjunto de dados de entrada original.

> [!TIP]
> Incapaz de encontrar uma resolução que corresponda ao seu cenário? Você pode fornecer feedback sobre este tópico que inclui o nome do módulo que gerou o erro, e o tipo de dados e cardinalidade da coluna. Usaremos as informações para fornecer etapas mais direcionadas para solução de problemas para cenários comuns. 

|Mensagens de Exceção|
|------------------------|
|Não é possível processar a coluna do tipo atual. O tipo não é suportado pelo módulo.|
|Não é possível processar a coluna do tipo {col_type}. O tipo não é suportado pelo módulo.|
|Não é possível processar a coluna "{col_name}" do tipo {col_type}. O tipo não é suportado pelo módulo.|
|Não é possível processar a coluna "{col_name}" do tipo {col_type}. O tipo não é suportado pelo módulo. Nome do parâmetro: {arg_name}.|


## <a name="error-0018"></a>Erro 0018  
 Ocorrerá uma exceção se o conjunto de dados de entrada não for válido.  

**Resolução:** Este erro no Azure Machine Learning pode aparecer em muitos contextos, portanto não há uma única resolução. Em geral, o erro indica que os dados fornecidos como entrada para um módulo têm o número errado de colunas, ou que o tipo de dados não corresponde aos requisitos do módulo. Por exemplo:   

-   O módulo requer uma coluna de rótulo, mas nenhuma coluna está marcada como um rótulo ou você ainda não selecionou uma coluna de rótulo.  
  
-   O módulo exige que os dados sejam categóricos, mas seus dados são numéricos.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Os dados estão no formato incorreto.  
  
-   Os dados importados contêm caracteres inválidos, valores ruins ou valores fora do intervalo.  
-   A coluna está vazia ou contém muitos valores faltantes.  

 Para determinar os requisitos e como seus dados podem, revise o tópico de ajuda para o módulo que estará consumindo o conjunto de dados como entrada.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados não é válido.|
|{dataset1} contém dados inválidos.|
|{dataset1} e {dataset2} devem ser consistentes em termos de coluna.|
|{dataset1} contém dados inválidos, {reason}.|
|{dataset1} contém {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} não é válido, {razão}. {troubleshoot_hint}|


## <a name="error-0019"></a>Erro 0019  
 Ocorrerá uma exceção se a coluna tiver de conter valores classificados, mas não contiver.  

 Você receberá este erro no Azure Machine Learning se os valores de coluna especificados estiverem fora de ordem.  

**Resolução:** Classifique os valores da coluna modificando manualmente o conjunto de dados de entrada e execute o módulo.  

|Mensagens de Exceção|
|------------------------|
|Os valores da coluna não estão classificados.|
|Os valores na coluna "{col_index}" não são classificados.|
|Os valores na coluna "{col_index}" do conjunto de dados "{dataset}" não são classificados.|


## <a name="error-0020"></a>Erro 0020  
 Ocorrerá uma exceção se o número de colunas em alguns dos conjuntos de dados transmitidos para o módulo for muito pequeno.  

 Você receberá este erro no Azure Machine Learning se não forem selecionadas colunas suficientes para um módulo.  

**Resolução:** Revisite o módulo e certifique-se de que o seletor de colunas tenha o número correto de colunas selecionadas.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados de entrada é menor que o mínimo permitido.|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é menor do que o mínimo permitido.|
|O número de colunas no conjunto de dados de entrada é menor do que o mínimo permitido de colunas {required_columns_count}.|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é menor do que o mínimo permitido de colunas {required_columns_count}.|


## <a name="error-0021"></a>Erro 0021  
 Ocorrerá uma exceção se o número de linhas em alguns dos conjuntos de dados passados para o módulo for muito pequeno.  

 Este erro é visto no Azure Machine Learning quando não há linhas suficientes no conjunto de dados para executar a operação especificada. Por exemplo, você pode ver esse erro se o conjunto de dados de entrada estiver vazio ou se estiver tentando executar uma operação que requer um número mínimo de linhas para ser válida. Tais operações podem incluir (mas não se limitam a) agrupamento ou classificação com base em métodos estatísticos, certos tipos de binning e aprendizado com contagens.  

**Solução:**

 + Abra o módulo que retornou o erro e verifique as propriedades do conjunto de dados de entrada e do módulo. 
 + Verifique se o conjunto de dados de entrada não está vazio e que há linhas de dados suficientes para atender aos requisitos descritos na ajuda do módulo.  
 + Se seus dados forem carregados de uma fonte externa, certifique-se de que a fonte de dados está disponível e que não há erro ou alteração na definição de dados que faria com que o processo de importação obtenha menos linhas.
 + Se você estiver realizando uma operação no upstream de dados do módulo que pode afetar o tipo de dados ou o número de valores, como limpeza, divisão ou operações de adesão, verifique as saídas dessas operações para determinar o número de linhas devolvidas.  

|Mensagens de Exceção|
|------------------------|
|O número de linhas no conjunto de dados de entrada é menor que o mínimo permitido.|
|O número de linhas no conjunto de dados de entrada é menor do que o mínimo permitido de linha {required_rows_count}.|
|O número de linhas no conjunto de dados de entrada é menor do que o mínimo permitido de linha {required_rows_count}. {razão}|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é menor do que o mínimo permitido de linha {required_rows_count}.|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é {actual_rows_count}, menor do que o mínimo permitido de linha {required_rows_count}(s).|
|O número de linhas "{row_type}" no conjunto de dados de entrada "{arg_name}" é {actual_rows_count}, menor do que o mínimo permitido de linha {required_rows_count}(s).|


## <a name="error-0022"></a>Erro 0022  
 Ocorrerá uma exceção se o número de colunas selecionadas no conjunto de dados de entrada não for igual ao número esperado.  

 Este erro no Azure Machine Learning pode ocorrer quando o módulo ou operação a jusante requer um número específico de colunas ou entradas, e você forneceu poucas ou muitas colunas ou entradas. Por exemplo:   

-   Você especifica uma única coluna de rótulo ou coluna-chave e selecionou acidentalmente várias colunas.  
  
-   Você está renomeando colunas, mas forneceu mais ou menos nomes do que há colunas.  
  
-   O número de colunas na origem ou destino foi alterado ou não corresponde ao número de colunas usadas pelo módulo.  
  
-   Você forneceu uma lista de valores separados por comuma para entradas, mas o número de valores não corresponde ou várias entradas não são suportadas.  

**Resolução:** Revisite o módulo e verifique a seleção da coluna para garantir que o número correto de colunas esteja selecionado. Verifique as saídas dos módulos upstream e os requisitos das operações a jusante.  

 Se você usou uma das opções de seleção de colunas que podem selecionar várias colunas (índices de colunas, todos os recursos, todos numéricos, etc.), valide o número exato de colunas retornadas pela seleção.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Verifique se o número ou o tipo de colunas upstream não foi alterado.  

 Se você estiver usando um conjunto de dados de recomendação para treinar um modelo, lembre-se de que o recomendador espera um número limitado de colunas, correspondentes a pares de itens de usuário ou rankings de itens de usuário. Remova colunas adicionais antes de treinar o modelo ou dividir conjuntos de dados de recomendação. Para obter mais informações, consulte [Dados divididos](split-data.md).  

|Mensagens de Exceção|
|------------------------|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual ao número esperado.|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual a {expected_col_count}.|
|O padrão de seleção de colunas "{selection_pattern_friendly_name}" fornece número de colunas selecionadas no conjunto de dados de entrada não igual a {expected_col_count}.|
|Espera-se que o padrão de seleção de colunas "{selection_pattern_friendly_name}" forneça {expected_col_count} coluna(s) selecionada no conjunto de dados de entrada, mas {selected_col_count} coluna(s) é/são realmente fornecidas.|


## <a name="error-0023"></a>Erro 0023  

Ocorrerá uma exceção se a coluna de destino do conjunto de dados de entrada não for válida para o módulo atual do instrutor.  

Esse erro no Azure Machine Learning ocorre se a coluna de destino (como selecionada nos parâmetros do módulo) não for do tipo de dados válido, contivesse todos os valores faltantes ou não fosse categórica como esperado.  

**Resolução:** Revisite a entrada do módulo para inspecionar o conteúdo da coluna rótulo/destino. Certifique-se de que não tem todos os valores perdidos. Se o módulo espera que a coluna-alvo seja categórica, certifique-se de que há mais de um valor distinto na coluna de destino.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados de entrada possui uma coluna de destino sem suporte.|
|O conjunto de dados de entrada tem a coluna de destino sem suporte "{column_index}".|
|O conjunto de dados de entrada tem uma coluna de destino sem suporte "{column_index}" para o aluno do tipo {learner_type}.|


## <a name="error-0024"></a>Erro 0024  
Ocorrerá uma exceção se o conjunto de dados não contiver uma coluna de rótulo.  

 Esse erro no Azure Machine Learning ocorre quando o módulo requer uma coluna de rótulo e o conjunto de dados não possui uma coluna de rótulo. Por exemplo, a avaliação de um conjunto de dados pontuado geralmente requer que uma coluna de rótulo esteja presente para calcular métricas de precisão.  

Também pode acontecer que uma coluna de rótuloesteja presente no conjunto de dados, mas não detectada corretamente pelo Azure Machine Learning.

**Solução:**

+ Abra o módulo que gerou o erro e determine se uma coluna de rótulo está presente. O nome ou tipo de dados da coluna não importa, desde que a coluna contenha um único resultado (ou variável dependente) que você está tentando prever. Se você não tiver certeza de qual coluna tem o rótulo, procure um nome genérico, como *Classe* ou *Destino*. 
+  Se o conjunto de dados não incluir uma coluna de rótulo, é possível que a coluna do rótulo tenha sido explicitamente ou acidentalmente removida a montante. Também pode ser que o conjunto de dados não seja a saída de um módulo de pontuação a montante.
+ Para marcar explicitamente a coluna como a coluna de rótulo, adicione o módulo [Editar metadados](edit-metadata.md) e conectar o conjunto de dados. Selecione apenas a coluna de rótulos e selecione **'Rótulo'** na lista de paradas **Campos.** 
+ Se a coluna errada for escolhida como rótulo, você pode selecionar **'Limpar rótulo'** nos **Campos** para corrigir os metadados na coluna. 
  
|Mensagens de Exceção|
|------------------------|
|Não há nenhuma coluna de rótulo no conjunto de dados.|
|Não há coluna de rótulos em "{dataset_name}".|


## <a name="error-0025"></a>Erro 0025  
 Ocorrerá uma exceção se o conjunto de dados não contiver uma coluna de classificação.  

 Este erro no Azure Machine Learning ocorre se a entrada para o modelo de avaliação não contiver colunas de pontuação válidas. Por exemplo, o usuário tenta avaliar um conjunto de dados antes de ser pontuado com um modelo treinado corretamente, ou a coluna de pontuação foi explicitamente lançada rio acima. Essa exceção também ocorre se as colunas de pontuação nos dois conjuntos de dados forem incompatíveis. Por exemplo, você pode estar tentando comparar a precisão de um regressor linear com um classificador binário.  

**Resolução:** Revisite a entrada no modelo de avaliação e examine se ela contém uma ou mais colunas de pontuação. Caso assim, o conjunto de dados não foi pontuado ou as colunas de pontuação foram lançadas em um módulo upstream.  

|Mensagens de Exceção|
|------------------------|
|Não há nenhuma coluna de classificação no conjunto de dados.|
|Não há coluna de pontuação em "{dataset_name}".|
|Não há uma coluna de pontuação em "{dataset_name}" que seja produzida por um "{learner_type}". Classificação do conjunto de dados usando o tipo correto de aprendiz.|


## <a name="error-0026"></a>Erro 0026  
 A exceção ocorre se não forem permitidas colunas com o mesmo nome.  

 Esse erro no Azure Machine Learning ocorre se várias colunas tiverem o mesmo nome. Uma maneira de receber esse erro é se o conjunto de dados não tiver uma linha de cabeçalho e os nomes das colunas forem atribuídos automaticamente: Col0, Col1, etc.  

**Resolução:** Se as colunas tiverem o mesmo nome, insira um módulo [Editar metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo. Use o seletor de colunas em [Editar metadados](edit-metadata.md) para selecionar colunas para renomear, digitando os novos nomes na caixa de texto Novos nomes de **coluna.**  

|Mensagens de Exceção|
|------------------------|
|Os nomes de colunas iguais são especificados nos argumentos. Não são permitidos nomes de colunas iguais por módulo.|
|Nomes iguais de coluna nos argumentos "{arg_name_1}" e "{arg_name_2}" não são permitidos. Especifique nomes diferentes.|


## <a name="error-0027"></a>Erro 0027  
 Ocorrerá uma exceção no caso de dois objetos precisarem ser do mesmo tamanho, mas não forem.  

 Este é um erro comum no Azure Machine Learning e pode ser causado por muitas condições.  

**Resolução:** Não há uma resolução específica. No entanto, você pode verificar condições como as seguintes:  

-   Se você estiver renomeando colunas, certifique-se de que cada lista (as colunas de entrada e a lista de novos nomes) tenha o mesmo número de itens.  
  
-   Se você estiver juntando ou concatenando dois conjuntos de dados, certifique-se de que eles têm o mesmo esquema.  
  
-   Se você estiver juntando dois conjuntos de dados que possuem várias colunas, certifique-se de que as colunas-chave tenham o mesmo tipo de dados e selecione a opção **Permitir duplicatas e preservar a ordem de coluna na seleção**.  

|Mensagens de Exceção|
|------------------------|
|O tamanho dos objetos passados está inconsistente.|
|O tamanho de "{friendly_name1}" é inconsistente com o tamanho de "{friendly_name2}".|


## <a name="error-0028"></a>Erro 0028  
 Ocorrerá uma exceção no caso em que o conjunto de colunas contiver nomes de colunas duplicados e isso não for permitido.  

 Esse erro no Azure Machine Learning ocorre quando os nomes das colunas são duplicados; ou seja, não é único.  

**Resolução:** Se alguma coluna tiver o mesmo nome, adicione uma instância de [Editar metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo levantando o erro. Use o Seletor de colunas em [Editar metadados](edit-metadata.md) para selecionar colunas para renomear e digitar os nomes das novas colunas na caixa de texto Novos nomes de **colunas.** Se você estiver renomeando várias colunas, certifique-se de que os valores digitados nos nomes da **coluna Novo** são únicos.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas contém nomes de colunas duplicados.|
|O nome "{duplicated_name}" é duplicado.|
|O nome "{duplicated_name}" é duplicado em "{arg_name}".|
|O nome "{duplicated_name}" é duplicado. Detalhes: {detalhes}|


## <a name="error-0029"></a>Erro 0029  
 Ocorrerá uma exceção quando um URI inválido for passado.  

 Esse erro no Azure Machine Learning ocorre no caso de uri inválido ser passado.  Você receberá este erro se alguma das seguintes condições for verdadeira:  

-   O URI Público ou SAS fornecido para armazenamento Azure Blob para leitura ou gravação contém um erro.  
  
-   A janela de tempo para o SAS expirou.  
  
-   A URL da Web via fonte HTTP representa um arquivo ou um URI de loopback.  
  
-   A URL da Web via HTTP contém uma URL formatado incorretamente.  
  
-   A URL não pode ser resolvida pela fonte remota.  

**Resolução:** Revisite o módulo e verifique o formato do URI. Se a fonte de dados for uma URL da Web via HTTP, verifique se a fonte pretendida não é um arquivo ou um URI de loopback (localhost).  

|Mensagens de Exceção|
|------------------------|
|Um Uri inválido é passado.|
|O Uri "{invalid_url}" é inválido.|


## <a name="error-0030"></a>Erro 0030  
 Ocorrerá uma exceção quando não for possível baixar um arquivo.  

 Essa exceção no Azure Machine Learning ocorre quando não é possível baixar um arquivo. Você receberá esta exceção quando uma tentativa de leitura de uma fonte HTTP falhar após três (3) tentativas de repetição.  

**Resolução:** Verifique se o URI para a fonte HTTP está correto e se o site está atualmente acessível via Internet.  

|Mensagens de Exceção|
|------------------------|
|Não é possível baixar um arquivo.|
|Erro ao baixar o arquivo: {file_url}.|


## <a name="error-0031"></a>Erro 0031  
 Ocorrerá uma exceção se o número de colunas no conjunto de colunas for menor que o necessário.  

 Esse erro no Azure Machine Learning ocorre se o número de colunas selecionadas for menor do que o necessário.  Você receberá este erro se o número mínimo de colunas necessário não for selecionado.  

**Resolução:** Adicione colunas adicionais à seleção de colunas usando o **Seletor de colunas**.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de colunas é menor que o necessário.|
|Pelo menos {required_columns_count} coluna(s) deve ser especificada para o argumento de entrada "{arg_name}".|
|Pelo menos {required_columns_count} coluna(s) deve ser especificada para o argumento de entrada "{arg_name}". O número real de colunas especificadas é {input_columns_count}.|


## <a name="error-0032"></a>Erro 0032  
 Ocorrerá uma exceção se o argumento não for um número.  

 Você receberá este erro no Azure Machine Learning se o argumento for um duplo ou NaN.  

**Resolução:** Modifique o argumento especificado para usar um valor válido.  

|Mensagens de Exceção|
|------------------------|
|O argumento não é um número.|
|"{arg_name}" não é um número.|


## <a name="error-0033"></a>Erro 0033  
 Ocorrerá uma exceção se o argumento for Infinito.  

 Esse erro no Azure Machine Learning ocorre se o argumento for infinito. Você receberá este erro se `double.NegativeInfinity` o `double.PositiveInfinity`argumento for ou .  

**Resolução:** Modifique o argumento especificado para ser um valor válido.  

|Mensagens de Exceção|
|------------------------|
|O argumento deve ser finito.|
|"{arg_name}" não é finito.|


## <a name="error-0034"></a>Erro 0034  
 Ocorrerá uma exceção se houver mais de uma classificação para um determinado par de usuário-item.  

 Esse erro no Azure Machine Learning ocorre na recomendação se um par de itens de usuário tiver mais de um valor de classificação.  

**Resolução:** Certifique-se de que o par de itens de usuário possui apenas um valor de classificação.  

|Mensagens de Exceção|
|------------------------|
|Mais de uma classificação existe para o valor(s) no conjunto de dados.|
|Mais de uma classificação para usuário {usuário} e item {item} na tabela de dados de previsão de classificação.|
|Mais de uma classificação para usuário {usuário} e item {item} em {dataset}.|


## <a name="error-0035"></a>Erro 0035  
 Ocorrerá uma exceção se nenhum recurso for fornecido por um determinado usuário ou item.  

 Este erro no Azure Machine Learning ocorre que você está tentando usar um modelo de recomendação para pontuação, mas um vetor de recurso não pode ser encontrado.  

**Solução:**

O recomendador matchbox tem certos requisitos que devem ser cumpridos ao usar recursos de itens ou recursos do usuário.  Este erro indica que falta um vetor de recurso para um usuário ou item fornecido como entrada. Certifique-se de que um vetor de recursos esteja disponível nos dados de cada usuário ou item.  

 Por exemplo, se você treinou um modelo de recomendação usando recursos como idade, localização ou renda do usuário, mas agora quer criar pontuações para novos usuários que não foram vistos durante o treinamento, você deve fornecer algum conjunto equivalente de recursos (ou seja, idade, localização e valores de renda) para os novos usuários, a fim de fazer previsões adequadas para eles. 

 Se você não tiver nenhum recurso para esses usuários, considere a engenharia de recursos para gerar recursos apropriados.  Por exemplo, se você não tiver valores individuais de idade ou renda do usuário, você pode gerar valores aproximados para usar para um grupo de usuários. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Resolução não aplicável ao seu caso? Você é bem-vindo para enviar feedback sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o número de linhas na coluna. Usaremos essas informações para fornecer etapas mais detalhadas de solução de problemas no futuro.

|Mensagens de Exceção|
|------------------------|
|Nenhum recurso foi fornecido para um item ou usuário necessário.|
|Recursos para {required_feature_name} necessários, mas não fornecidos.|


## <a name="error-0036"></a>Erro 0036  
 Ocorrerá uma exceção se vários vetores do recurso forem fornecidos para um determinado usuário ou item.  

 Esse erro no Azure Machine Learning ocorre se um vetor de recurso for definido mais de uma vez.  

**Resolução:** Certifique-se de que o vetor de recurso não está definido mais de uma vez.  

|Mensagens de Exceção|
|------------------------|
|Definição de recurso duplicada para um usuário ou item.|


## <a name="error-0037"></a>Erro 0037  
 Ocorrerá uma exceção se várias colunas de rótulo forem especificadas e apenas uma for permitida.  

 Esse erro no Azure Machine Learning ocorre se mais de uma coluna for selecionada para ser a nova coluna de rótulos. A maioria dos algoritmos de aprendizagem supervisionados requer que uma única coluna seja marcada como alvo ou rótulo.  

**Resolução:** Certifique-se de selecionar uma única coluna como a nova coluna de rótulos.  

|Mensagens de Exceção|
|------------------------|
|Várias colunas de rótulo estão especificadas.|
|Várias colunas de rótulos são especificadas em "{dataset_name}".|


## <a name="error-0039"></a>Erro 0039  
 A exceção ocorre se uma operação falhar.  

 Este erro no Azure Machine Learning ocorre quando uma operação interna não pode ser concluída.  

**Resolução:** Este erro é causado por muitas condições e não há um remédio específico.  
 A tabela a seguir contém mensagens genéricas para este erro, que são seguidas por uma descrição específica da condição. 

 Se não houver detalhes disponíveis, [envie feedback](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) e forneça informações sobre os módulos que geraram o erro e as condições relacionadas.

|Mensagens de Exceção|
|------------------------|
|Falha na operação.|
|Erro ao concluir a operação: "{failed_operation}".|
|Erro ao concluir a operação: "{failed_operation}". Motivo: "{reason}".|


## <a name="error-0042"></a>Erro 0042  
 Ocorrerá uma exceção quando não for possível converter a coluna em outro tipo.  

 Esse erro no Azure Machine Learning ocorre quando não é possível converter a coluna para o tipo especificado.  Você receberá esse erro se um módulo exigir um tipo de dados específico, como data, texto, um número de ponto flutuante ou inteiro, mas não é possível converter uma coluna existente para o tipo necessário.  

Por exemplo, você pode selecionar uma coluna e tentar convertê-la em um tipo de dados numérico para uso em uma operação matemática e obter esse erro se a coluna contiver dados inválidos. 

Outra razão pela qual você pode obter esse erro se você tentar usar uma coluna contendo números de ponto flutuante ou muitos valores únicos como uma coluna categórica. 

**Solução:**

+ Abra a página de ajuda para o módulo que gerou o erro e verifique os requisitos do tipo de dados.
+ Revise os tipos de dados das colunas no conjunto de dados de entrada.
+ Inspecione os dados originários das chamadas fontes de dados sem esquema.
+ Verifique o conjunto de dados para obter valores ausentes ou caracteres especiais que possam bloquear a conversão para o tipo de dados desejado. 
    + Os tipos de dados numéricos devem ser consistentes: por exemplo, verifique se há números de pontos flutuantes em uma coluna de inteiros.
    + Procure por strings de texto ou valores NA em uma coluna numérica. 
    + Os valores booleanos podem ser convertidos em uma representação apropriada, dependendo do tipo de dados necessário.
    + Examine as colunas de texto para caracteres não unicodificados, caracteres de guia ou caracteres de controle
    + Os dados de data-hora devem ser consistentes para evitar erros de modelagem, mas a limpeza pode ser complexa devido aos muitos formatos. Considere usar <!--the [Execute R Script](execute-r-script.md) or -->[Execute](execute-python-script.md) módulos de script Python para executar a limpeza.  
+ Se necessário, modifique os valores no conjunto de dados de entrada para que a coluna possa ser convertida com sucesso. A modificação pode incluir binning, truncation ou arredondamento, eliminação de outliers ou imputação de valores ausentes. Veja os artigos a seguir para alguns cenários comuns de transformação de dados em aprendizado de máquina:
    + [Limpar dados ausentes](clean-missing-data.md)
    + [Normalizar Dados](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Resolução incerta ou não aplicável ao seu caso? Você é bem-vindo para enviar feedback sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o tipo de dados da coluna. Usaremos essas informações para fornecer etapas mais detalhadas de solução de problemas no futuro.  

|Mensagens de Exceção|
|------------------------|
|Conversão não permitida.|
|Não foi possível converter a coluna do tipo {type1} para a coluna do tipo {type2}.|
|Não foi possível converter a coluna "{col_name1}" do tipo {type1} para a coluna do tipo {type2}.|
|Não foi possível converter a coluna "{col_name1}" do tipo {type1} para a coluna "{col_name2}" do tipo {type2}.|


## <a name="error-0044"></a>Erro 0044  
 Ocorrerá uma exceção quando não for possível derivar o tipo de elemento da coluna a partir dos valores existentes.  

 Esse erro no Azure Machine Learning ocorre quando não é possível inferir o tipo de uma coluna ou colunas em um conjunto de dados. Isso normalmente acontece quando concatena dois ou mais conjuntos de dados com diferentes tipos de elementos. Se o Azure Machine Learning não conseguir determinar um tipo comum que seja capaz de representar todos os valores em uma coluna ou colunas sem perda de informações, ele gerará esse erro.  

**Resolução:** Certifique-se de que todos os valores em uma determinada coluna em ambos os conjuntos de dados que estão sendo combinados são do mesmo tipo (numérico, booleano, categórico, string, data, etc.) ou podem ser coagidos para o mesmo tipo.  

|Mensagens de Exceção|
|------------------------|
|Não é possível derivar o tipo de elemento da coluna.|
|Não é possível derivar o tipo de elemento para a coluna "{column_name}" -- todos os elementos são referências nulas.|
|Não é possível derivar o tipo de elemento para a coluna "{column_name}" do conjunto de dados "{dataset_name}" -- todos os elementos são referências nulas.|


## <a name="error-0045"></a>Erro 0045  
 Ocorrerá uma exceção quando não for possível criar uma coluna devido aos tipos de elemento mistos na origem.  

 Este erro no Azure Machine Learning é produzido quando os tipos de elementos de dois conjuntos de dados que estão sendo combinados são diferentes.  

**Resolução:** Certifique-se de que todos os valores em uma determinada coluna em ambos os conjuntos de dados que estão sendo combinados são do mesmo tipo (numérico, booleano, categórico, string, data, etc.).  

|Mensagens de Exceção|
|------------------------|
|Não for possível criar colunas com tipos de elemento mistos.|
|Não é possível criar coluna com id "{column_id}" de tipos de elementos mistos:<br />O tipo de dados[{row_1}, {column_id}] é "{type_1}". <br />O tipo de dados[{row_2}, {column_id}] é "{type_2}".|
|Não é possível criar coluna com id "{column_id}" de tipos de elementos mistos:<br />Digite em bloco {chunk_id_1} é "{type_1}". <br />Digite em pedaço {chunk_id_2} é "{type_2}" com tamanho do pedaço: {chunk_size}.|


## <a name="error-0046"></a>Erro 0046  
 Ocorrerá uma exceção quando não for possível criar um diretório no caminho especificado.  

 Esse erro no Azure Machine Learning ocorre quando não é possível criar um diretório no caminho especificado. Você receberá este erro se qualquer parte do caminho para o diretório de saída de uma consulta de colmeia estiver incorreta ou inacessível.  

**Resolução:** Revisite o módulo e verifique se o caminho do diretório está corretamente formatado e que ele está acessível com as credenciais atuais.  

|Mensagens de Exceção|
|------------------------|
|Especifique um diretório de saída válido.|
|Diretório: {path} não pode ser criado. Especifique um caminho válido.|


## <a name="error-0047"></a>Erro 0047  
 Ocorrerá uma exceção se o número de colunas de recursos em alguns dos conjuntos de dados transmitidos para o módulo for muito pequeno.  

 Esse erro no Azure Machine Learning ocorre se o conjunto de dados de entrada para treinamento não contiver o número mínimo de colunas exigidas pelo algoritmo. Normalmente, o conjunto de dados está vazio ou contém apenas colunas de treinamento.  

**Resolução:** Revisite o conjunto de dados de entrada para ter certeza de que há uma ou mais colunas adicionais além da coluna de rótulos.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas de recursos no conjunto de dados de entrada é menor que o mínimo permitido.|
|O número de colunas de recursos no conjunto de dados de entrada é menor do que o mínimo permitido de colunas {required_columns_count}.|
|O número de colunas de recursos no conjunto de dados de entrada "{arg_name}" é menor do que o mínimo permitido de colunas {required_columns_count}.|


## <a name="error-0048"></a>Erro 0048  
 Ocorrerá uma exceção quando não for possível abrir um arquivo.  

 Esse erro no Azure Machine Learning ocorre quando não é possível abrir um arquivo para leitura ou gravação. Você pode receber este erro por essas razões:  

-   O recipiente ou o arquivo (blob) não existe  
  
-   O nível de acesso do arquivo ou contêiner não permite que você acesse o arquivo  
  
-   O arquivo é muito grande para ler ou o formato errado  

**Resolução:** Revisite o módulo e o arquivo que você está tentando ler.  

 Verifique se os nomes do contêiner e do arquivo estão corretos.  

 Use o portal clássico do Azure ou uma ferramenta de armazenamento Do Zure para verificar se você tem permissão para acessar o arquivo.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Mensagens de Exceção|
|------------------------|
|Não é possível abrir um arquivo.|
|Erro ao abrir o arquivo: {file_name}.|
|Erro ao abrir o arquivo: {file_name}. Mensagem de exceção de armazenamento: {exception}.|


## <a name="error-0049"></a>Erro 0049  
 Ocorrerá uma exceção quando não for possível analisar um arquivo.  

 Esse erro no Azure Machine Learning ocorre quando não é possível analisar um arquivo. Você receberá este erro se o formato de arquivo selecionado no módulo [Importar dados](import-data.md) não corresponder ao formato real do arquivo ou se o arquivo contiver um caractere irreconhecível.  

**Resolução:** Revisite o módulo e corrija a seleção do formato do arquivo se ele não corresponder ao formato do arquivo. Se possível, inspecione o arquivo para confirmar que ele não contém caracteres ilegais.  

|Mensagens de Exceção|
|------------------------|
|Não é possível analisar um arquivo.|
|Erro ao analisar o arquivo {file_format}.|
|Erro ao analisar o arquivo {file_format}: {file_name}.|
|Erro ao analisar o arquivo {file_format}. Motivo: {failure_reason}.|
|Erro ao analisar o arquivo {file_format}: {file_name}. Motivo: {failure_reason}.|


## <a name="error-0052"></a>Erro 0052  
 Ocorrerá uma exceção se a chave da conta de armazenamento do Azure for especificada incorretamente.  

 Esse erro no Azure Machine Learning ocorre se a chave usada para acessar a conta de armazenamento do Azure estiver incorreta. Por exemplo, você pode ver esse erro se a chave de armazenamento do Azure foi truncada quando copiada e colada, ou se a chave errada foi usada.  

 Para obter mais informações sobre como obter a chave de uma conta de armazenamento do Azure, consulte [Exibir, copiar e regenerar as chaves de acesso de armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  

**Resolução:** Revisite o módulo e verifique se a chave de armazenamento do Azure está correta para a conta; copiar a chave novamente do portal clássico do Azure, se necessário.  

|Mensagens de Exceção|
|------------------------|
|A chave da conta de armazenamento do Azure está incorreta.|


## <a name="error-0053"></a>Erro 0053  
 A exceção ocorre no caso em que não há recursos ou itens do usuário para recomendações de caixas de fósforos.  

 Este erro no Azure Machine Learning é produzido quando um vetor de recurso não pode ser encontrado.  

**Resolução:** Certifique-se de que um vetor de recurso está presente no conjunto de dados de entrada.  

|Mensagens de Exceção|
|------------------------|
|Os recursos e/ou itens do usuário são necessários, mas não foram fornecidos.|


## <a name="error-0056"></a>Erro 0056  
 A exceção ocorre se as colunas selecionadas para uma operação violarem os requisitos.  

 Esse erro no Azure Machine Learning ocorre quando você está escolhendo colunas para uma operação que requer que a coluna seja de um determinado tipo de dados. 

 Esse erro também pode acontecer se a coluna for o tipo de dados correto, mas o módulo que você está usando exige que a coluna também seja marcada como uma coluna de recurso, rótulo ou categórico.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Solução:**

1.  Revise o tipo de dados das colunas selecionadas no momento. 

2. Verifique se as colunas selecionadas são categóricas, rotuladas ou colunas de recursos.  
  
3.  Revise o tópico de ajuda para o módulo em que você fez a seleção da coluna, para determinar se existem requisitos específicos para o tipo de dados ou uso da coluna.  
  
3.  Use [Editar metadados](edit-metadata.md) para alterar o tipo de coluna durante a duração desta operação. Certifique-se de alterar o tipo de coluna de volta ao seu valor original, usando outra instância de [Editar metadados,](edit-metadata.md)se você precisar dele para operações a jusante.  

|Mensagens de Exceção|
|------------------------|
|Uma ou mais colunas selecionadas não estavam em uma categoria permitida.|
|A coluna com o nome "{col_name}" não está em uma categoria permitida.|


## <a name="error-0057"></a>Erro 0057  
 Ocorrerá uma exceção ao tentar criar um arquivo ou um blob que já existe.  

 Essa exceção ocorre quando você está usando o módulo [Dados de Exportação](export-data.md) ou outro módulo para salvar resultados de um pipeline no armazenamento de blob do Azure Machine Learning para o Azure Blob, mas você tenta criar um arquivo ou blob que já existe.   

**Solução:**

 Você receberá esse erro somente se você definir anteriormente o **modo de gravação de armazenamento azure blob** para **Erro**. Por design, este módulo levanta um erro se você tentar escrever um conjunto de dados para uma bolha que já existe.

 - Abra as propriedades do módulo e altere o **modo de gravação de armazenamento do Azure blob** para **'Sobregravação'.**
 - Alternativamente, você pode digitar o nome de uma bolha de destino diferente ou arquivo e certifique-se de especificar uma bolha que ainda não existe.  

|Mensagens de Exceção|
|------------------------|
|O arquivo ou blob já existe.|
|Arquivo ou Blob "{file_path}" já existe.|


## <a name="error-0058"></a>Erro 0058  
 Esse erro no Azure Machine Learning ocorre se o conjunto de dados não contiver a coluna de rótulo sustiva esperada.  

 Essa exceção também pode ocorrer quando a coluna de rótulo fornecida não corresponde aos dados ou dados esperados pelo aluno, ou tem os valores errados. Por exemplo, essa exceção é produzida ao usar uma coluna de rótulo de valor real ao treinar um classificador binário.  

**Resolução:** A resolução depende do aluno ou treinador que você está usando e dos tipos de dados das colunas em seu conjunto de dados. Primeiro, verifique os requisitos do algoritmo de aprendizagem de máquina ou módulo de treinamento.  

 Revisite o conjunto de dados de entrada. Verifique se a coluna que você espera ser tratada como a etiqueta tem o tipo de dados certo para o modelo que você está criando.  

 Verifique as entradas para obter valores faltantes e elimine-os ou substitua-os se necessário.  

 Se necessário, adicione o módulo [Editar metadados](edit-metadata.md) e garantir que a coluna do rótulo esteja marcada como um rótulo.  

|Mensagens de Exceção|
|------------------------|
|Os valores da coluna do rótulo e os valores da coluna do rótulo não são comparáveis.|
|A coluna de rótulos não é como esperado em "{dataset_name}".|
|A coluna de rótulos não é como esperado em "{dataset_name}", {reason}.|
|A coluna de rótulos "{column_name}" não é esperada em "{dataset_name}".|
|A coluna de rótulos "{column_name}" não é esperada em "{dataset_name}", {reason}.|


## <a name="error-0059"></a>Erro 0059  
 Ocorrerá uma exceção se um índice de coluna especificado em um seletor de colunas não puder ser analisado.  

 Esse erro no Azure Machine Learning ocorre se um índice de coluna especificado ao usar o Seletor de colunas não puder ser analisado.  Você receberá este erro quando o índice da coluna estiver em um formato inválido que não pode ser analisado.  

**Resolução:** Modifique o índice da coluna para usar um valor de índice válido.  

|Mensagens de Exceção|
|------------------------|
|Um ou mais índices de coluna especificados ou intervalos de índice não puderam ser analisados.|
|O índice da coluna ou o intervalo "{column_index_or_range}" não puderam ser analisados.|


## <a name="error-0060"></a>Erro 0060  
 Ocorrerá uma exceção quando um intervalo de coluna fora do intervalo for especificado em um seletor de colunas.  

 Esse erro no Azure Machine Learning ocorre quando um intervalo de colunas fora de alcance é especificado no Seletor de colunas. Você receberá este erro se o intervalo de colunas no seletor de colunas não corresponder às colunas no conjunto de dados.  

**Resolução:** Modifique o intervalo de colunas no seletor de colunas para corresponder às colunas no conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Intervalo de índice de coluna inválido ou fora do intervalo especificado.|
|O intervalo de colunas "{column_range}" é inválido ou fora de alcance.|


## <a name="error-0061"></a>Erro 0061  
 Ocorrerá uma exceção ao tentar adicionar uma linha a uma DataTable que tenha um número diferente de colunas da tabela.  

 Esse erro no Azure Machine Learning ocorre quando você tenta adicionar uma linha a um conjunto de dados que tem um número diferente de colunas do que o conjunto de dados.  Você receberá esse erro se a linha que está sendo adicionada ao conjunto de dados tiver um número diferente de colunas do conjunto de dados de entrada.  A linha não pode ser anexada ao conjunto de dados se o número de colunas for diferente.  

**Resolução:** Modifique o conjunto de dados de entrada para ter o mesmo número de colunas que a linha adicionada ou modifique a linha adicionada para ter o mesmo número de colunas que o conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Todas as tabelas devem ter o mesmo número de colunas.|
|As colunas em bloco "{chunk_id_1}" são diferentes com o pedaço "{chunk_id_2}" com tamanho do pedaço: {chunk_size}.|
|A contagem de colunas no arquivo "{filename_1}" (count={column_count_1}) é diferente com o arquivo "{filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Erro 0062  
 Ocorrerá uma exceção durante a tentativa de comparar dois modelos com tipos diferentes de aprendiz.  

 Este erro no Azure Machine Learning é produzido quando as métricas de avaliação para dois conjuntos de dados pontuados diferentes não podem ser comparadas. Neste caso, não é possível comparar a eficácia dos modelos utilizados para produzir os dois conjuntos de dados pontuados.  

**Resolução:** Verifique se os resultados pontuados são produzidos pelo mesmo tipo de modelo de aprendizagem de máquina (classificação binária, regressão, classificação multiclasse, recomendação, clustering, detecção de anomalias, etc.) Todos os modelos que você compara devem ter o mesmo tipo de aprendiz.  

|Mensagens de Exceção|
|------------------------|
|Todos os modelos devem ter o mesmo tipo de aprendiz.|
|Tem tipo de aprendiz incompatível: "{actual_learner_type}". Os tipos de aprendizesperados são: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Erro 0064  
 Ocorrerá uma exceção se o nome da conta de armazenamento do Azure ou a chave de armazenamento for especificado incorretamente.  

 Esse erro no Azure Machine Learning ocorre se o nome da conta de armazenamento do Azure ou a chave de armazenamento for especificada incorretamente. Você receberá este erro se inserir um nome de conta ou senha incorreto para a conta de armazenamento. Isso pode ocorrer se você digitar manualmente o nome da conta ou senha. Também pode ocorrer se a conta tiver sido excluída.  

**Resolução:** Verifique se o nome da conta e a senha foram inseridos corretamente e se a conta existe.  

|Mensagens de Exceção|
|------------------------|
|O nome da conta de armazenamento ou a chave de armazenamento do Azure está incorreto.|
|O nome da conta de armazenamento do Azure "{account_name}" ou a chave de armazenamento para o nome da conta está incorreto.|


## <a name="error-0065"></a>Erro 0065  
 Ocorrerá uma exceção se o nome do blob do Azure estiver especificado incorretamente.  

 Este erro no Azure Machine Learning ocorre se o nome blob do Azure for especificado incorretamente.  Você receberá o erro se:  

-   A bolha não pode ser encontrada no recipiente especificado.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Apenas o contêiner foi especificado como a fonte em uma solicitação [de Dados de Importação](import-data.md) quando o formato era Excel ou CSV com codificação; concatenação do conteúdo de todas as bolhas dentro de um recipiente não é permitida com esses formatos.  
  
-   Um Uri SAS não contém o nome de uma bolha válida.  

**Resolução:** Revisite o módulo lançando a exceção. Verifique se a bolha especificada existe no contêiner na conta de armazenamento e se as permissões permitem que você veja a bolha. Verifique se a entrada é do nome do contêiner do **formulário/nome de arquivo** se você tiver Excel ou CSV com formatos de codificação. Verifique se um Uri SAS contém o nome de uma bolha válida.  

|Mensagens de Exceção|
|------------------------|
|O nome blob de armazenamento Do Zure está incorreto.|
|O nome blob de armazenamento Azure "{blob_name}" está incorreto.|
|O nome blob de armazenamento Do Zure com prefixo "{blob_name_prefix}" não existe.|
|Não conseguiu encontrar nenhum blobs de armazenamento do Azure o contêiner {container_name}.|
|Não conseguiu encontrar nenhum blobs de armazenamento do Azure com o caminho curinga "{blob_wildcard_path}".|


## <a name="error-0066"></a>Erro 0066  
 Ocorrerá uma exceção se um recurso não puder ser carregado para um blob do Azure.  

 Esse erro no Azure Machine Learning ocorre se um recurso não puder ser carregado em um Azure Blob.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Ambos são salvos na mesma conta de armazenamento do Azure que a conta que contém o arquivo de entrada.  

**Resolução:** Revisite o módulo. Verifique se o nome da conta do Azure, a chave de armazenamento e o contêiner estão corretos e se a conta tem permissão para escrever no contêiner.  

|Mensagens de Exceção|
|------------------------|
|Não foi possível carregar o recurso no armazenamento do Azure.|
|O arquivo "{source_path}" não pôde ser carregado no armazenamento do Azure como "{dest_path}".|


## <a name="error-0067"></a>Erro 0067  
 Ocorrerá uma exceção se um conjunto de dados tiver um número diferente de colunas que o esperado.  

 Esse erro no Azure Machine Learning ocorre se um conjunto de dados tiver um número diferente de colunas do que o esperado.  Você receberá este erro quando o número de colunas no conjunto de dados for diferente do número de colunas que o módulo espera durante a execução.  

**Resolução:** Modifique o conjunto de dados de entrada ou os parâmetros.  

|Mensagens de Exceção|
|------------------------|
|Número de colunas na tabela de dados inesperado.|
|Número inesperado de colunas no conjunto de dados "{dataset_name}".|
|A coluna esperada "{expected_column_count}" mas encontrou colunas "{actual_column_count}".|
|No conjunto de dados de entrada "{dataset_name}", esperado coluna "{expected_column_count}", mas encontrado coluna (s) "{actual_column_count}".|


## <a name="error-0068"></a>Erro 0068  
 Ocorrerá uma exceção se o script de Hive especificado não estiver correto.  

 Esse erro no Azure Machine Learning ocorre se houver erros de sintaxe em um script QL da Colmeia ou se o intérprete hive encontrar um erro durante a execução da consulta ou do script.  

**Solução:**

A mensagem de erro da Hive é normalmente relatada no Registro de Erros para que você possa agir com base no erro específico. 

+ Abra o módulo e inspecione a consulta em busca de erros.  
+ Verifique se a consulta funciona corretamente fora do Azure Machine Learning fazendo login no console Hive do seu cluster Hadoop e executando a consulta.  
+ Tente colocar comentários em seu script hive em uma linha separada em vez de misturar declarações executáveis e comentários em uma única linha.  

### <a name="resources"></a>Recursos

Veja os artigos a seguir para obter ajuda com consultas de Colmeia para aprendizado de máquina:

+ [Criar tabelas do Hive e carregar dados do Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Explorar dados em tabelas com consultas de Colmeia](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Criar recursos para os dados em um cluster Hadoop usando as consultas do Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Colmeia para usuários SQL Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Mensagens de Exceção|
|------------------------|
|O script de hive está incorreto.|


## <a name="error-0069"></a>Erro 0069  
 Ocorrerá uma exceção se o script SQL especificado não estiver correto.  

 Esse erro no Azure Machine Learning ocorre se o script SQL especificado tiver problemas de sintaxe ou se as colunas ou tabela especificadas no script não forem válidas. 

 Você receberá este erro se o mecanismo SQL encontrar qualquer erro durante a execução da consulta ou do script. A mensagem de erro SQL é normalmente relatada de volta no Registro de Erros para que você possa agir com base no erro específico.  

**Resolução:** Revisite o módulo e inspecione a consulta SQL em busca de erros.  

 Verifique se a consulta funciona corretamente fora do Azure ML fazendo login diretamente no servidor de banco de dados e executando a consulta.  

 Se houver uma mensagem gerada por SQL relatada pela exceção do módulo, tome medidas com base no erro relatado. Por exemplo, as mensagens de erro às vezes incluem orientação específica sobre o erro provável:
+ *Nenhuma coluna ou banco de dados ausente,* indicando que você pode ter digitado um nome de coluna errado. Se você tiver certeza de que o nome da coluna está correto, tente usar suportes ou aspas para fechar o identificador da coluna.
+ *Erro de lógica \<SQL perto\>da palavra-chave SQL,* indicando que você pode ter um erro de sintaxe antes da palavra-chave especificada

  
|Mensagens de Exceção|
|------------------------|
|O script SQL está incorreto.|
|A consulta SQL "{sql_query}" não está correta.|
|A consulta SQL "{sql_query}" não está correta. Mensagem de exceção: {exception}.|


## <a name="error-0070"></a>Erro 0070  
 Ocorrerá uma exceção ao tentar acessar a tabela do Azure inexistente.  

 Esse erro no Azure Machine Learning ocorre quando você tenta acessar uma tabela Azure inexistente. Você receberá esse erro se especificar uma tabela no armazenamento Azure, que não existe ao ler ou escrever para o Azure Table Storage. Isso pode acontecer se você digitar mal o nome da tabela desejada, ou você tiver uma incompatibilidade entre o nome de destino e o tipo de armazenamento. Por exemplo, você pretendia ler de uma tabela, mas digitou o nome de uma bolha em vez disso.  

**Resolução:** Revisite o módulo para verificar se o nome da tabela está correto.  

|Mensagens de Exceção|
|------------------------|
|Não existe tabela do Azure.|
|A tabela azul "{table_name}" não existe.|


## <a name="error-0072"></a>Erro 0072  
 Ocorrerá uma exceção no caso de tempo limite da conexão.  

 Esse erro no Azure Machine Learning ocorre quando uma conexão é agendada. Você receberá esse erro se houver atualmente problemas de conectividade com a fonte ou destino dos dados, como conectividade lenta da internet, ou se o conjunto de dados for grande e/ou a consulta SQL para ler nos dados realiza um processamento complicado.  

**Resolução:** Determine se existem atualmente problemas com conexões lentas ao armazenamento do Azure ou à internet.  

|Mensagens de Exceção|
|------------------------|
|O tempo de conexão ocorreu.|


## <a name="error-0073"></a>Erro 0073  
 Ocorrerá uma exceção se ocorrer um erro durante a conversão de uma coluna em outro tipo.  

 Esse erro no Azure Machine Learning ocorre quando não é possível converter coluna para outro tipo.  Você receberá este erro se um módulo exigir um determinado tipo e não for possível converter a coluna para o novo tipo.  

**Resolução:** Modifique o conjunto de dados de entrada para que a coluna possa ser convertida com base na exceção interna.  

|Mensagens de Exceção|
|------------------------|
|Falha ao converter a coluna.|
|Falha ao converter coluna para {target_type}.|


## <a name="error-0075"></a>Erro 0075  
Ocorrerá uma exceção quando uma função de agrupamento inválida for usada ao quantizar um conjunto de dados.  

Esse erro no Azure Machine Learning ocorre quando você está tentando binar dados usando um método não suportado ou quando as combinações de parâmetros são inválidas.  

**Solução:**

O manuseio de erros para este evento foi introduzido em uma versão anterior do Azure Machine Learning que permitiu mais personalização dos métodos binning. Atualmente, todos os métodos binning são baseados em uma seleção de uma lista de isto, então tecnicamente não deve ser mais possível obter esse erro.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|Mensagens de Exceção|
|------------------------|
|Função de agrupamento inválido usada.|


## <a name="error-0077"></a>Erro 0077  
 A exceção ocorre quando o modo de gravação de arquivo blob desconhecido é aprovado.  

 Esse erro no Azure Machine Learning ocorre se um argumento inválido for aprovado nas especificações de um destino ou fonte de arquivo blob.  

**Resolução:** Em quase todos os módulos que importam ou exportam dados para e do armazenamento blob do Azure, os valores de parâmetro que controlam o modo de gravação são atribuídos usando uma lista de isento; portanto, não é possível passar um valor inválido, e esse erro não deve aparecer. Este erro será preterido em uma versão posterior.  

|Mensagens de Exceção|
|------------------------|
|Modo de gravação de blob sem suporte.|
|Modo de gravação de bolha sem suporte: {blob_write_mode}.|


## <a name="error-0078"></a>Erro 0078  
 A exceção ocorre quando a opção HTTP para [Dados de Importação](import-data.md) recebe um código de status de 3xx indicando redirecionamento.  

 Esse erro no Azure Machine Learning ocorre quando a opção HTTP para [Dados de Importação](import-data.md) recebe um código de status 3xx (301, 302, 304, etc.) indicando redirecionamento. Você receberá esse erro se tentar se conectar a uma fonte HTTP que redirecione o navegador para outra página. Por razões de segurança, os sites de redirecionamento não são permitidos como fontes de dados para o Azure Machine Learning.  

**Resolução:** Se o site for um site confiável, digite diretamente a URL redirecionada.  

|Mensagens de Exceção|
|------------------------|
|O redirecionamento de Http não é permitido.|


## <a name="error-0079"></a>Erro 0079  
 Ocorrerá uma exceção se o nome do contêiner de armazenamento do Azure estiver especificado incorretamente.  

 Esse erro no Azure Machine Learning ocorre se o nome do contêiner de armazenamento Azure for especificado incorretamente. Você receberá este erro se não tiver especificado o nome do contêiner e do blob (arquivo) usando **o Path to blob começando com a** opção de contêiner ao escrever para o Azure Blob Storage.  

**Resolução:** Revisite o módulo [Dados de Exportação](export-data.md) e verifique se o caminho especificado para a bolha contém o contêiner e o nome do arquivo, no formato **container/filename**.  

|Mensagens de Exceção|
|------------------------|
|O nome do contêiner de armazenamento do Azure está incorreto.|
|O nome do contêiner de armazenamento Azure "{container_name}" está incorreto; Era esperado um nome de contêiner do formato container/blob.|


## <a name="error-0080"></a>Erro 0080  
 Ocorrerá uma exceção quando a coluna com todos os valores ausentes não for permitida pelo módulo.  

 Este erro no Azure Machine Learning é produzido quando uma ou mais das colunas consumidas pelo módulo contêm todos os valores faltantes. Por exemplo, se um módulo estiver computando estatísticas agregadas para cada coluna, ele não poderá operar em uma coluna que não contenha dados. Nesses casos, a execução do módulo é interrompida com essa exceção.  

**Resolução:** Revisite o conjunto de dados de entrada e remova todas as colunas que contenham todos os valores faltantes.  

|Mensagens de Exceção|
|------------------------|
|Colunas com todos os valores ausentes não são permitidas.|
|A coluna {col_index_or_name} tem todos os valores faltando.|


## <a name="error-0081"></a>Erro 0081  
 Ocorrerá uma exceção no módulo PCA se o número de dimensões a serem reduzidas for igual ao número de colunas de recurso no conjunto de dados de entrada, que contém pelo menos uma coluna de recurso esparsa.  

 Este erro no Azure Machine Learning é produzido se as seguintes condições forem atendidas: (a) o conjunto de dados de entrada tem pelo menos uma coluna esparsa e (b) o número final de dimensões solicitadas é o mesmo que o número de dimensões de entrada.  

**Resolução:** Considere reduzir o número de dimensões na saída para ser menor do que o número de dimensões na entrada. Isso é típico em aplicações de PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Mensagens de Exceção|
|------------------------|
|Para um conjunto de dados que contenha colunas de recursos esparsas, o número de dimensões para o qual reduzir deve ser menor que o número de colunas de recurso.|


## <a name="error-0082"></a>Erro 0082  
 Ocorrerá uma exceção quando um modelo não puder ser desserializado com êxito.  

 Esse erro no Azure Machine Learning ocorre quando um modelo ou transformação de aprendizado de máquina salvo não pode ser carregado por uma versão mais recente do tempo de execução do Azure Machine Learning como resultado de uma mudança de ruptura.  

**Resolução:** O gasoduto de treinamento que produziu o modelo ou a transformação deve ser reexecutado e o modelo ou transformação deve ser resalvo.  

|Mensagens de Exceção|
|------------------------|
|O modelo não pôde ser desserializado, pois provavelmente está serializado com um formato de serialização anterior. Por favor, retreine e salve o modelo.|


## <a name="error-0083"></a>Erro 0083  
 Ocorrerá uma exceção se o conjunto de dados usado para treinamento não puder ser usado para o tipo concreto de aprendiz.  

 Este erro no Azure Machine Learning é produzido quando o conjunto de dados é incompatível com o aluno que está sendo treinado. Por exemplo, o conjunto de dados pode conter pelo menos um valor ausente em cada linha e, como resultado, todo o conjunto de dados seria ignorado durante o treinamento. Em outros casos, alguns algoritmos de aprendizagem de máquina, como a detecção de anomalias, não esperam que os rótulos estejam presentes e podem lançar essa exceção se os rótulos estiverem presentes no conjunto de dados.  

**Resolução:** Consulte a documentação do aluno que está sendo usado para verificar os requisitos para o conjunto de dados de entrada. Examine as colunas para ver se todas as colunas necessárias estão presentes.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados usado para treinamento é inválido.|
|{data_name} contém dados inválidos para treinamento.|
|{data_name} contém dados inválidos para treinamento. Tipo de aluno: {learner_type}.|
|{data_name} contém dados inválidos para treinamento. Tipo de aluno: {learner_type}. Razão: {razão}.|
|Falha ao aplicar a ação "{action_name}" nos dados de treinamento {data_name}. Razão: {razão}.|


## <a name="error-0084"></a>Erro 0084  
 Exceção ocorre quando pontuações produzidas a partir de um Script de R são avaliadas. Há suporte atualmente para isso.  

 Este erro no Azure Machine Learning ocorre se você tentar usar um dos módulos para avaliar um modelo com saída de um script R que contém pontuações.  

**Solução:**

|Mensagens de Exceção|
|------------------------|
|A avaliação das pontuações produzidas pelo Custom Model não tem suporte.|


## <a name="error-0085"></a>Erro 0085  
 Ocorre uma exceção quando a avaliação de script falha com um erro.  

 Esse erro no Azure Machine Learning ocorre quando você está executando script personalizado que contém erros de sintaxe.  

**Resolução:** Revise seu código em um editor externo e verifique se há erros.  

|Mensagens de Exceção|
|------------------------|
|Erro durante a avaliação do script.|
|O seguinte erro ocorreu durante a avaliação do script. Consulte o log de saída para obter mais informações:<br />---------- início da mensagem de erro do intérprete {script_language} ----------<br />{mensagem}<br />---------- Mensagem de fim de erro do intérprete {script_language} ----------|


## <a name="error-0090"></a>Erro 0090  
 A exceção ocorre quando a criação da tabela Colmeia falha.  

 Esse erro no Azure Machine Learning ocorre quando você está usando [Dados de Exportação](export-data.md) ou outra opção para salvar dados em um cluster HDInsight e a tabela Colmeia especificada não pode ser criada.  

**Resolução:** Verifique o nome da conta de armazenamento do Azure associada ao cluster e verifique se você está usando a mesma conta nas propriedades do módulo.  

|Mensagens de Exceção|
|------------------------|
|A mesa colmeia não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster seja o mesmo que é passado através do parâmetro do módulo.|
|A tabela Colmeia "{table_name}" não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster seja o mesmo que é passado através do parâmetro do módulo.|
|A tabela Colmeia "{table_name}" não pôde ser criada. Para um cluster HDInsight, certifique-se de que o nome da conta de armazenamento Azure associado ao cluster seja "{cluster_name}".|


## <a name="error-0102"></a>Erro 0102  
 Jogado quando um arquivo ZIP não pode ser extraído.  

 Este erro no Azure Machine Learning ocorre quando você está importando um pacote com zíper com a extensão .zip, mas o pacote não é um arquivo zip ou o arquivo não usa um formato zip suportado.  

**Resolução:** Certifique-se de que o arquivo selecionado é um arquivo .zip válido e que foi compactado usando um dos algoritmos de compactação suportados.  

 Se você tiver esse erro ao importar conjuntos de dados em formato compactado, verifique se todos os arquivos contidos usam um dos formatos de arquivo suportados e estão no formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Tente ler os arquivos desejados para uma nova pasta compactada com zíper e tente adicionar o módulo personalizado novamente.  

|Mensagens de Exceção|
|------------------------|
|Dado, o arquivo ZIP não está no formato correto.|


## <a name="error-0105"></a>Erro 0105  
 Este erro é exibido quando um arquivo de definição de módulo contém um tipo de parâmetro não suportado  
  
 Este erro no Azure Machine Learning é produzido quando você cria uma definição xml de módulo personalizado e o tipo de parâmetro ou argumento na definição não corresponde a um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade do tipo de qualquer elemento **Arg** no arquivo de definição xml do módulo personalizado é um tipo suportado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de parâmetro sem suporte.|  
|Tipo de parâmetro sem suporte '{0}' especificado.|  


## <a name="error-0107"></a>Erro 0107  
 Jogado quando um arquivo de definição de módulo define um tipo de saída sem suporte  
  
 Este erro no Azure Machine Learning é produzido quando o tipo de porta de saída em uma definição xml de módulo personalizado não corresponde a um tipo suportado.  
  
**Resolução:** Certifique-se de que a propriedade do tipo de um elemento De saída no arquivo de definição xml do módulo personalizado é um tipo suportado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de saída sem suporte.|  
|Tipo de saída sem suporte '{output_type}' especificado.|  


## <a name="error-0125"></a>Erro 0125  
 Lançado quando o esquema para vários conjuntos de dados não corresponde.  

**Solução:**

|Mensagens de Exceção|
|------------------------|
|O esquema do conjunto de dados não corresponde.|


## <a name="error-0127"></a>Erro 0127  
 O tamanho de pixel da imagem excede o limite permitido  

 Esse erro ocorre se você estiver lendo imagens de um conjunto de dados de imagem para classificação e as imagens forem maiores do que o modelo pode suportar.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Mensagens de Exceção|
|------------------------|
|O tamanho de pixel da imagem excede o limite permitido.|
|O tamanho do pixel da imagem no arquivo '{file_path}' excede o limite permitido: '{size_limit}'.|


## <a name="error-0128"></a>Erro 0128  
 Número de probabilidades condicionais para colunas categóricas excede o limite.  

**Solução:**

|Mensagens de Exceção|
|------------------------|
|Número de probabilidades condicionais para colunas categóricas excede o limite.|
|Número de probabilidades condicionais para colunas categóricas excede o limite. As colunas '{column_name_or_index_1}' e '{column_name_or_index_2}' são o par problemático.|


## <a name="error-0129"></a>Erro 0129  
 O número de colunas no conjunto de dados excede o limite permitido.  

**Solução:**

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados excede o limite permitido.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o permitido.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite permitido de '{component_name}'.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite '{limit_columns_count}' permitido de '{component_name}'.|


## <a name="error-0134"></a>Erro 0134
A exceção ocorre quando a coluna do rótulo está faltando ou tem número insuficiente de linhas rotuladas.  

Esse erro ocorre quando o módulo requer uma coluna de rótulo, mas você não incluiu uma na seleção da coluna, ou a coluna de rótulos está perdendo muitos valores.

Esse erro também pode ocorrer quando uma operação anterior altera o conjunto de dados de tal forma que linhas insuficientes estão disponíveis para uma operação downstream. Por exemplo, suponha que você use uma expressão no módulo **Partição e Amostra** para dividir um conjunto de dados por valores. Se não forem encontradas correspondências para sua expressão, um dos conjuntos de dados resultantes da partição estaria vazio.

Resolução: 

 Se você incluir uma coluna de rótulo na seleção da coluna, mas ela não for reconhecida, use o módulo [Editar metadados](edit-metadata.md) para marcá-la como uma coluna de rótulo.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Em seguida, você pode usar o módulo ['Limpar dados faltantes'](clean-missing-data.md) para remover linhas com valores ausentes na coluna de rótulos. 

 Verifique os conjuntos de dados de entrada para ter certeza de que eles contêm dados válidos e linhas suficientes para satisfazer os requisitos da operação. Muitos algoritmos gerarão uma mensagem de erro se exigirem algumas linhas numéricas mínimas de dados, mas os dados contêm apenas algumas linhas, ou apenas um cabeçalho.

|Mensagens de Exceção|
|------------------------|
|A exceção ocorre quando a coluna do rótulo está faltando ou tem número insuficiente de linhas rotuladas.|
|A exceção ocorre quando a coluna do rótulo está faltando ou tem linhas rotuladas de {required_rows_count}.|
|A exceção ocorre quando a coluna de rótulos no conjunto de dados {dataset_name} está faltando ou tem menos de {required_rows_count} legendadas linhas.|


## <a name="error-0138"></a>Erro 0138  
 A memória foi esgotada, incapaz de completar a execução do módulo. A redução da amostragem do conjunto de dados pode ajudar a aliviar o problema.  

 Esse erro ocorre quando o módulo que está sendo executado requer mais memória do que está disponível no contêiner Azure. Isso pode acontecer se você estiver trabalhando com um grande conjunto de dados e a operação atual não caber na memória.  

**Resolução:** Se você estiver tentando ler um grande conjunto de dados e a operação não puder ser concluída, a redução da amostragem do conjunto de dados pode ajudar.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Mensagens de Exceção|
|------------------------|
|A memória foi esgotada, incapaz de completar a execução do módulo.|
|A memória foi esgotada, incapaz de completar a execução do módulo. Detalhes: {detalhes}|


## <a name="error-0141"></a>Erro 0141  
 A exceção ocorre se o número das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de seqüência for muito pequeno.  

 Esse erro no Azure Machine Learning ocorre quando não há valores únicos suficientes na coluna selecionada para realizar a operação.  

**Resolução:** Algumas operações realizam operações estatísticas em colunas de recursos e categóricas, e se não houver valores suficientes, a operação pode falhar ou retornar um resultado inválido. Verifique seu conjunto de dados para ver quantos valores existem nas colunas de recurso e rótulo e determine se a operação que você está tentando realizar é estatisticamente válida.  

 Se o conjunto de dados de origem for válido, você também poderá verificar se alguma manipulação de dados a montante ou a operação de metadados alteraram os dados e removeram alguns valores.  

 Se as operações upstream incluem divisão, amostragem ou reamostragem, verifique se as saídas contêm o número esperado de linhas e valores.  

|Mensagens de Exceção|
|------------------------|
|O número das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de cordas é muito pequeno.|
|O número total das colunas numéricas selecionadas e valores únicos nas colunas categóricas e de seqüência (atualmente {actual_num}) deve ser pelo menos {lower_boundary}.|


## <a name="error-0154"></a>Erro 0154  
 A exceção ocorre quando o usuário tenta juntar dados em colunas-chave com tipo de coluna incompatível.

|Mensagens de Exceção|
|------------------------|
|Os tipos de elementos da coluna-chave não são compatíveis.|
|Os tipos de elementos da coluna-chave não são compatíveis. (esquerda: {keys_left}; à direita: {keys_right})|


## <a name="error-0155"></a>Erro 0155  
 A exceção ocorre quando os nomes da coluna do conjunto de dados não são string.

|Mensagens de Exceção|
|------------------------|
|O nome da coluna do dataframe deve ser tipo de string. Os nomes das colunas não são string.|
|O nome da coluna do dataframe deve ser tipo de string. Os nomes da coluna {column_names} não são string.|


## <a name="error-0156"></a>Erro 0156  
 A exceção ocorre quando falha na leitura de dados do Banco de Dados SQL do Azure.

|Mensagens de Exceção|
|------------------------|
|Falha ao ler dados do Banco de Dados SQL do Azure.|
|Falha ao ler dados do Banco de Dados SQL do Azure: {detailed_message} DB: {database_server_name}:{database_name} Consulta: {sql_statement}|


## <a name="error-0157"></a>Erro 0157  
 Datastore não encontrado.

|Mensagens de Exceção|
|------------------------|
|As informações do datastore são inválidas.|
|As informações do datastore são inválidas. Falha ao obter o armazenamento de dados AzureML '{datastore_name}' no espaço de trabalho '{workspace_name}'.|


## <a name="error-0158"></a>Erro 0158
 Jogado quando um diretório de transformação é inválido.

|Mensagens de Exceção|
|------------------------------------------------------------|
|Dado que o Diretório de Transformação é inválido.|
|A formação "{arg_name}" é inválida. Razão: {razão}. Por favor, execute o experimento de treinamento que gera o arquivo Transform. Se o experimento de treinamento foi excluído, por favor, recrie e salve o arquivo Transform.|


## <a name="error-1000"></a>Erro 1000  
Exceção de biblioteca interna.  

Este erro é fornecido para capturar erros internos do motor não manipulados. Portanto, a causa para este erro pode ser diferente dependendo do módulo que gerou o erro.  

Para obter mais ajuda, recomendamos que você publique a mensagem detalhada que acompanha o erro no fórum Azure Machine Learning, juntamente com uma descrição do cenário, incluindo os dados usados como entradas. Esse feedback nos ajudará a priorizar erros e identificar as questões mais importantes para um trabalho adicional.  

|Mensagens de Exceção|
|------------------------|
|Exceção de biblioteca.|
|Exceção da biblioteca: {exception}.|
|Exceção de biblioteca desconhecida: {exception}. {customer_support_guidance}.|

