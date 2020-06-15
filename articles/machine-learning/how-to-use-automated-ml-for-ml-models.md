---
title: Usar o autoML para criar modelos e implantar
titleSuffix: Azure Machine Learning
description: Crie, revise e implante modelos de machine learning automatizado com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 841d518c02dbc76a172890f6019d78d048f4e8bb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653841"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Criar, revisar e implantar modelos de machine learning automatizado com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprenderá a criar, explorar e implantar modelos de machine learning automatizado sem uma única linha de código na interface do estúdio do Azure Machine Learning. O machine learning automatizado é um processo no qual o melhor algoritmo de machine learning a ser usado para seus dados específicos é selecionado para você. Esse processo permite que você gere modelos de machine learning rapidamente. [Saiba mais sobre o machine learning automatizado](concept-automated-ml.md).
 
Para obter um exemplo de ponta a ponta, experimente o [tutorial para criar um modelo de classificação com a interface do ML automatizado do Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

Para uma experiência baseada em códigos Python, [configure seus experimentos de machine Learning automatizado](how-to-configure-auto-train.md) com o SDK do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um espaço de trabalho do Azure Machine Learning com um tipo de **edição Enterprise**. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).  Para atualizar um espaço de trabalho existente para a edição Enterprise, consulte [Atualizar para a edição Enterprise](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introdução

1. Entre no Azure Machine Learning em https://ml.azure.com. 

1. Selecione sua assinatura e seu espaço de trabalho. 

1. Navegue até o painel esquerdo. Selecione **ML Automatizado** na seção **Criar**.

[![Painel de navegação do Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Caso essa seja a primeira vez que você faz experimentos, você verá uma lista vazia e links para a documentação. 

Caso contrário, você verá uma lista das suas experiências recentes de machine learning automatizado, incluindo aquelas criadas com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar o experimento

1. Selecione **+ Nova execução de ML automatizado** e preencha o formulário.

1. Selecione um conjunto de dados no seu contêiner de armazenamento ou crie um novo. Os conjuntos de dados podem ser criados a partir de arquivos locais, URLs da Web, armazenamento de dados ou Azure Open Datasets. 

    >[!Important]
    > Requisitos para dados de treinamento:
    >* Os dados devem estar em formato de tabela.
    >* O valor que você quer prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de dados de um arquivo em seu computador local, selecione **Procurar** e, depois, selecione o arquivo. 

    1. Dê um nome exclusivo ao conjunto de dados e forneça uma descrição opcional. 

    1. Selecione **Avançar** para abrir o **Formulário de armazenamento de dados e de seleção de arquivos**. Nesse formulário, você seleciona onde quer carregar seu conjunto de dados, o contêiner de armazenamento padrão que é criado automaticamente com seu espaço de trabalho, ou escolhe um contêiner de armazenamento que deseja usar para o experimento. 

    1. Revise a precisão do formulário de **Configurações e versão prévia**. O formulário é preenchido de forma inteligente com base no tipo de arquivo. 

        Campo| Descrição
        ----|----
        Formato de arquivo| Define o layout e o tipo de dados armazenados em um arquivo.
        Delimitador| Um ou mais caracteres para especificar o limite entre regiões separadas e independentes em texto sem formatação ou outros fluxos de dados.
        Codificação| Identifica qual tabela de esquema de bit para caractere usar para ler seu conjunto de dados.
        Cabeçalhos da coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.
        Ignorar linhas | Indica quantas linhas, se houver, serão ignoradas no conjunto de registros.
    
        Selecione **Avançar**.

    1. O formulário **Esquema** é preenchido de forma inteligente com base nas seleções feitas no formulário **Configurações e visualização**. Aqui, configure o tipo de dados para cada coluna, revise os nomes das colunas e selecione quais delas **não incluir** no seu experimento. 
            
        Selecione **Avançar**.

    1. O formulário **Confirmar detalhes** é um resumo das informações previamente preenchidas nos formulários **Informações básicas** e **Configurações e versão prévia**. Você também tem a opção de criar um perfil de dados para o seu conjunto de dados usando uma computação de criação de perfil habilitada. Saiba mais sobre a [criação de perfil de dados](#profile).

        Selecione **Avançar**.
1. Selecione seu conjunto de dados recém-criado assim que ele aparecer. Você também pode exibir uma versão prévia do conjunto de dados e das estatísticas de amostra. 

1. No formulário **Configurar execução**, insira um nome exclusivo para o experimento.

1. Selecione uma coluna de destino, a qual representa a coluna na qual você gostaria de fazer previsões.

1. Selecione uma computação para o trabalho de treinamento e de criação de perfis de dados. Haverá uma lista suspensa contendo suas computações existentes. Para criar uma nova computação, siga as instruções da Etapa 7.

1. Selecione **Criar uma nova computação** para configurar o contexto de computação desse experimento.

    Campo|Descrição
    ---|---
    Nome da computação| Insira um nome exclusivo que identifique o contexto da computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual da computação.
    Número mín./máx. de nós (em Configurações Avançadas)| Para analisar os dados, é necessário especificar um ou mais nós. Insira o número máximo de nós da sua computação. Uma Computação do AML tem 6 nós como padrão.
    
    Selecione **Criar**. A criação da nova computação pode levar alguns minutos.

    >[!NOTE]
    > O nome da sua computação indicará se a computação que você selecionar/criar é *habilitada para criação de perfil*. (Consulte a seção [criação de perfil de dados](#profile) para obter mais detalhes.)

    Selecione **Avançar**.

1. No formulário **Tipo de tarefa e configurações**, selecione o tipo de tarefa: classificação, regressão ou previsão. Confira [Como definir tipos de tarefa](how-to-define-task-type.md) para obter mais informações.

    1. Para classificação, você também pode habilitar o aprendizado profundo, o qual é usado para definição de recursos de texto.

    1. Para previsão:
        1. Selecione a coluna de tempo: essa coluna contém os dados de tempo a serem usados.

        1. Selecione o horizonte de previsão: indique quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais o modelo for usado para fazer previsões do futuro, menos preciso ele será. [Saiba mais sobre previsão e horizonte de previsão](how-to-auto-train-forecast.md).

1. (Opcional) Exibir definições de configuração adicionais: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento. Caso contrário, os padrões são aplicados com base na seleção e nos dados de experimento. 

    Configurações adicionais|Descrição
    ------|------
    Métrica principal| Métrica principal usada para a pontuação do seu modelo. [Saiba mais sobre as métricas do modelo](how-to-configure-auto-train.md#explore-model-metrics).
    Personalização automática| Selecione para habilitar ou desabilitar o pré-processamento feito pelo machine learning automatizado. O pré-processamento inclui limpeza, preparação e transformação automáticas de dados para gerar recursos sintéticos. Não tem suporte para o tipo de tarefa de previsão de série temporal. [Saiba mais sobre o pré-processamento](#featurization). 
    Explicar o melhor modelo | Selecione para habilitar ou desabilitar a exibição da explicação do melhor modelo recomendado.
    Algoritmo bloqueado| Selecione os algoritmos que você deseja excluir do trabalho de treinamento.
    Critério de saída| Quando qualquer um desses critérios for atendido, o trabalho de treinamento é interrompido. <br> *Tempo do trabalho de treinamento (horas)* : o tempo permitido de execução do trabalho de treinamento. <br> *Limite de pontuação da métrica*:  pontuação mínima da métrica para todos os pipelines. Isso garante que, caso você tenha uma métrica de destino definida e que deseje alcançar, não gastará mais tempo no trabalho de treinamento do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para ser usada no trabalho de treinamento. [Saiba mais sobre a validação cruzada](how-to-configure-auto-train.md).
    Simultaneidade| *Máximo de iterações simultâneas*: número máximo de pipelines (iterações) a serem testados no trabalho de treinamento. O trabalho não será executado mais vezes do que o número de iterações especificado.

1. (Opcional) Exibir configurações de personalização: caso opte por habilitar a **Definição de recursos automática** no formulário **Definições de configuração adicionais**, é nesse formulário que você especifica em quais colunas essas definições de recursos devem ser executadas e seleciona qual valor estatístico deve ser usado para imputações de valor ausentes.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Criação de perfil de dados e estatísticas de resumo

É possível obter uma grande variedade de estatísticas de resumo em seu conjunto de dados para verificar se ele está pronto para o ML. Para colunas não numéricas, eles incluem apenas estatísticas básicas, como mínimo, máximo e contagem de erros. Para colunas numéricas, também é possível revisar os momentos estatísticos e os quantis estimados. Nosso perfil de dados inclui especificamente:

>[!NOTE]
> Entradas em branco são exibidas para recursos com tipos irrelevantes.

Estatística|Descrição
------|------
Recurso| Nome da coluna que está sendo resumida.
Perfil| Visualização em linha baseada no tipo inferido. Por exemplo, cadeias de caracteres, boolianos e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isso permite que você obtenha uma compreensão rápida da distribuição dos dados.
Distribuição de tipos| Contagem de valor em linha de tipos dentro de uma coluna. Os nulos são do seu próprio tipo, portanto, essa visualização é útil para detectar valores ímpares ou ausentes.
Type|Tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, boolianos, datas e decimais.
Mín| Valor mínimo da coluna. Entradas em branco são exibidas para recursos cujo tipo não tenha uma ordenação inerente (por exemplo, boolianos).
Max| Valor máximo da coluna. 
Contagem| Quantidade total de entradas ausentes e não ausentes na coluna.
Sem contagem faltando| Quantidade de entradas na coluna que não estão ausentes. Cadeias de caracteres e erros vazios são tratados como valores, portanto, eles não contribuirão para a “contagem de não ausentes”.
Quantis| Valores aproximados em cada quantil para fornecer uma noção da distribuição dos dados.
Média| Média aritmética ou média da coluna.
Desvio padrão| Medida da quantidade de dispersão ou variação dos dados dessa coluna.
Variance| A medida de quão difundidos estão os dados dessa coluna em comparação ao valor médio. 
Distorção| Medida de quão diferentes os dados dessa coluna são em comparação a uma distribuição normal.
Curtose| Medida de quão profundamente conectados os dados dessa coluna estão em comparação a uma distribuição normal.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Opções avançadas de definição de recursos

O machine learning automatizado oferece pré-processamento e verificadores de integridade dos dados automaticamente para ajudar você a identificar e gerenciar possíveis problemas com seus dados, como [dados com sobreajuste e desequilibrados](concept-manage-ml-pitfalls.md#prevent-over-fitting). 

### <a name="preprocessing"></a>Pré-processamento

> [!NOTE]
> Se você planeja exportar seus modelos de machine learning automatizado para um [modelo de ONNX](concept-onnx.md), somente as opções de definição de recursos marcadas com um * têm suporte no formato ONNX. Saiba mais sobre [conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). 

|Etapas de&nbsp;pré-processamento| Descrição |
| ------------- | ------------- |
|Remover alta cardinalidade ou nenhum recurso de variação* |Remova-os de conjuntos de treinamento e validação, inclusive recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).|
|Acrescentar valores ausentes* |Para recursos numéricos, atribua com a média dos valores na coluna.<br/><br/>Para recursos categóricos, atribua com o valor mais frequente.|
|Gerar recursos adicionais* |Para recursos DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para recursos Text: A frequência do termo com base em unigramas, bigramas e gramas de três caracteres.|
|Transformar e codificar*|Os recursos numéricos com poucos valores únicos são transformados em recursos categóricos.<br/><br/>A codificação one-hot é executada para uma baixa cardinalidade categórica; para alta cardinalidade, use a codificação one-hot-hash.|
|Inserções de palavras|O definidor de recursos de texto que converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de inserção de cada palavra em um documento é agregado em conjunto para produzir um vetor de recurso de documento.|
|Codificações de destino|Para recursos categóricos, mapeia cada categoria com o valor de destino médio para problemas de regressão e para a probabilidade de classe de cada classe para problemas de classificação. A pesagem baseada na frequência e a validação cruzada k-fold são aplicadas para reduzir o sobreajuste do mapeamento e do ruído causado por categorias de dados esparsas.|
|Codificação de destino de texto|Para uma entrada de texto, um modelo linear empilhado com um conjunto de palavras é usado para gerar a probabilidade de cada classe.|
|Peso de evidência (WoE)|Calcula o WoE como uma medida de correlação de colunas categóricas para a coluna de destino. Ele é calculado como o log da taxa de probabilidades de em classe vs. fora da classe. Essa etapa gera uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente os valores ausentes e o tratamento de exceções.|
|Distância do cluster|Treina um modelo de clustering k-means em todas as colunas numéricas.  Gera novos recursos de k, um novo recurso numérico por cluster, contendo a distância de cada amostra para o centroide de cada cluster.|

### <a name="data-guardrails"></a>Verificadores de integridade dos dados

Os verificadores de integridade dos dados são aplicados quando a definição de recursos automática está habilitada ou quando a validação está definida como automática. Os verificadores de integridade dos dados ajudam a identificar possíveis problemas com seus dados (por exemplo, valores ausentes, desequilíbrio de classe) e ajudam a tomar ações corretivas para obter resultados melhores. 

Os usuários podem revisar os verificadores de integridade dos dados no estúdio na guia **Verificadores de integridade dos dados** de uma execução de ML automatizado ou ao definir ```show_output=True``` ao enviar um experimento usando o SDK do Python. 

#### <a name="data-guardrail-states"></a>Estados dos verificadores de integridade dos dados

Os verificadores de integridade dos dados exibirão um de três estados: **Aprovado**, **Concluído** ou **Alertado**.

Estado| Descrição
----|----
Aprovado| Nenhum problema de dados foi detectado, e nenhuma ação é necessária por parte do usuário. 
Concluído| As alterações foram aplicadas aos seus dados. Incentivamos os usuários a examinar as ações corretivas que o ML automatizado tomou para garantir que as alterações estejam alinhadas aos resultados esperados. 
Alertado| Foi detectado um problema de dados que não pôde ser corrigido. Incentivamos os usuários a revisar e a corrigir o problema. 

>[!NOTE]
> Versões anteriores de experimentos de ML automatizado exibiram um quarto estado: **Corrigido**. Experimentos mais recentes não exibirão esse estado, e todos os verificadores de integridade que exibiram o estado **Corrigido** passarão a exibir o estado **Concluído**.   

A tabela a seguir descreve os verificadores de integridade dos dados que têm suporte atualmente, além de exibir os status associados que os usuários podem obter ao enviar seus experimentos.

Verificador de integridade|Status|Condição&nbsp;para&nbsp;gatilho
---|---|---
Imputação de valores de recurso ausente |**Aprovado** <br><br><br> **Concluído**| Não foram detectados valores de recursos ausentes em seus dados de treinamento. Saiba mais sobre a [imputação de valores ausentes](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options). <br><br> Foram detectados valores de recursos ausentes em seus dados de treinamento.
Tratamento de recursos de alta cardinalidade |**Aprovado** <br><br><br> **Concluído**| As entradas foram analisadas, e nenhum recurso de alta cardinalidade foi detectado. Saiba mais sobre [Detecção de recursos de alta cardinalidade.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Foram detectados recursos de alta cardinalidade nas suas entradas, os quais foram tratados.
Tratamento de divisão de validação |**Concluído**| *A configuração de validação foi definida como “automática”, e os dados de treinamento continham **menos** de 20.000 linhas.* <br> Cada iteração do modelo treinado foi validada por meio da validação cruzada. Saiba mais sobre os [dados de validação.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *A configuração de validação foi definida como “automática”, e os dados de treinamento continham **mais** de 20.000 linhas.* <br> Os dados de entrada foram divididos em um conjunto de dados de treinamento e um conjunto de dados de validação para a validação do modelo.
Detecção de equilíbrio de classe |**Aprovado** <br><br><br><br> **Alertado** | Suas entradas foram analisadas, e todas as classes estão equilibradas nos dados de treinamento. Um conjunto de dados é considerado equilibrado caso cada classe tenha uma boa representação no conjunto de dados, conforme medido pela quantidade e proporção de exemplos. <br><br><br> Foram detectadas classes desequilibradas nas suas entradas. Para corrigir o desvio de modelo, corrija o problema de equilíbrio. Saiba mais sobre [dados desequilibrados.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
Detecção de problemas de memória |**Aprovado** <br><br><br><br> **Concluído** |<br> Os valores {horizonte, retardo, janela sem interrupção} selecionados foram analisados, mas nenhum problema de falta de memória potencial foi detectado. Saiba mais sobre as [configurações de previsão](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) de série temporal. <br><br><br>Os valores selecionados {horizonte, retardo, janela sem interrupção} foram analisados e, potencialmente, farão com que o teste fique sem memória. As configurações de retardo ou de janela sem interrupção foram desativadas.
Detecção de frequência |**Aprovado** <br><br><br><br> **Concluído** |<br> A série temporal foi analisada, e todos os pontos de dados estão alinhados com a frequência detectada. <br> <br> A série temporal foi analisada, e foram detectados pontos de dados que não estão alinhados com a frequência detectada. Esses pontos de dados foram removidos do conjunto de dados. Saiba mais sobre a [preparação de dados de para a previsão de série temporal.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>Executar o experimento e exibir os resultados

Selecione **Concluir**, para executar o experimento. O processo de preparação do experimento pode levar até 10 minutos. Os trabalhos de treinamento podem levar mais 2 a 3 minutos para que cada pipeline termine a execução.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

A tela **Executar detalhes** é aberta na guia **Detalhes**. Essa tela mostra um resumo da execução do experimento, incluindo uma barra de status na parte superior, ao lado do número de execução. 

A guia **Modelos** contém uma lista dos modelos criados ordenados pela pontuação da métrica. Por padrão, o modelo com a pontuação mais alta de acordo com a métrica escolhida é exibido no início da lista. Como o trabalho de treinamento testa mais modelos, eles são adicionados à lista. Use isso para obter uma comparação rápida das métricas dos modelos produzidos até agora.

[![Painel de detalhes da execução](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Exibir detalhes da execução do treinamento

Faça uma busca detalhada em qualquer dos modelos concluídos para ver detalhes da execução de treinamento, como executar métricas na guia **Detalhes do modelo** ou gráficos de desempenho na guia **Visualizações**. [Saiba mais sobre gráficos](how-to-understand-automated-ml.md).

[![Detalhes da iteração](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implantar o seu modelo

Assim que você tiver o melhor modelo em mãos, é hora de implantá-lo como um serviço Web para prever novos dados.

O ML automatizado ajuda a implantar o modelo sem escrever códigos:

1. Você tem algumas opções de implantação: 

    + Opção 1: para implantar o melhor modelo (de acordo com os critérios de métrica definidos por você), selecione o botão **Implantar o melhor modelo** na guia **Detalhes**.

    + Opção 2: para implantar uma iteração de modelo específica desse experimento, faça uma busca detalhada no modelo para abrir a guia **Detalhes do modelo** e selecione **Implantar modelo**.

1. Preencha o painel **Implantar um modelo**.

    Campo| Valor
    ----|----
    Nome| Insira um nome exclusivo para sua implantação.
    Descrição| Insira uma descrição para identificar melhor a finalidade da implantação.
    Tipo de computação| Selecione o tipo de ponto de extremidade que você deseja implantar: *Serviço de Kubernetes do Azure (AKS)* ou *Instância de Contêiner do Azure (ACI)* .
    Nome da computação| *Aplica-se somente ao AKS:* selecione o nome do cluster AKS no qual você deseja fazer a implantação.
    Habilitar autenticação | Selecione para permitir a autenticação baseada em token ou em chave.
    Usar ativos da implantação personalizada| Habilite esse recurso caso queira carregar seu próprio script de pontuação e o arquivo de ambiente. [Saiba mais sobre scripts de pontuação](how-to-deploy-and-where.md#script).

    >[!Important]
    > Os nomes de arquivo devem ter menos de 32 caracteres e devem começar e terminar com caracteres alfanuméricos. Eles podem conter traços, sublinhados, pontos e caracteres alfanuméricos nas partes do meio. Espaços não são permitidos.

    O menu *Avançado* oferece recursos de implantação padrão, como [coleta de dados](how-to-enable-app-insights.md) e configurações de utilização de recursos. Caso queira substituir esses padrões, você deve fazê-lo nesse menu.

1. Selecione **Implantar**. A implantação pode levar cerca de 20 minutos para ser concluída.

Agora você tem um serviço Web operacional para gerar previsões. Você pode testar as previsões por meio de consultas ao serviço de [Suporte ao Azure Machine Learning interno do Power BI](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Próximas etapas

* [Saiba como consumir um serviço Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Entender os resultados de machine learning automatizado](how-to-understand-automated-ml.md).
* [Saiba mais sobre o machine learning automatizado](concept-automated-ml.md) e o Azure Machine Learning.
