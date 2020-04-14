---
title: Use autoML para criar modelos & implantar
titleSuffix: Azure Machine Learning
description: Crie, revise e implante modelos automatizados de aprendizado de máquina com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 0d6fa02578814c4c5d034be05cbc63093d70603b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257220"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Crie, revise e implante modelos automatizados de aprendizado de máquina com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprende a criar, explorar e implantar modelos automatizados de aprendizado de máquina sem uma única linha de código na interface de estúdio do Azure Machine Learning. O aprendizado automatizado de máquina é um processo no qual o melhor algoritmo de aprendizado de máquina para usar para seus dados específicos é selecionado para você. Esse processo permite gerar modelos de aprendizado de máquina rapidamente. [Saiba mais sobre aprendizado automatizado de máquina.](concept-automated-ml.md)
 
Para um exemplo final a fim, experimente o [tutorial para criar um modelo de classificação com a interface ML automatizada do Azure Machine Learning.](tutorial-first-experiment-automated-ml.md) 

Para uma experiência baseada em código Python, [configure seus experimentos automatizados](how-to-configure-auto-train.md) de aprendizado de máquina com o Azure Machine Learning SDK.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um espaço de trabalho de aprendizado de máquina do Azure com um tipo de **edição Enterprise**. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).  Para atualizar um espaço de trabalho existente para a edição Enterprise, consulte [Upgrade para edição Enterprise](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introdução

1. Faça login no Azure https://ml.azure.comMachine Learning em . 

1. Selecione sua assinatura e espaço de trabalho. 

1. Navegue até o painel esquerdo. Selecione **ML automatizado** na seção **Autor.**

[![Painel de navegação do estúdio Azure Machine Learning](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Se esta é a sua primeira vez fazendo algum experimento, você verá uma lista vazia e links para a documentação. 

Caso contrário, você verá uma lista de seus recentes experimentos automatizados de aprendizado de máquina, incluindo aqueles criados com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar experimentos

1. Selecione **+ Nova execução automatizada do ML** e preencha o formulário.

1. Selecione um conjunto de dados do seu contêiner de armazenamento ou crie um novo conjunto de dados. Os conjuntos de dados podem ser criados a partir de arquivos locais, urls da Web, datastores ou conjuntos de dados abertos do Azure. 

    >[!Important]
    > Requisitos para dados de treinamento:
    >* Os dados devem estar em forma tabular.
    >* O valor que você deseja prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de dados a partir de um arquivo em seu computador local, **selecione Procurar** e, em seguida, selecione o arquivo. 

    1. Dê um nome exclusivo ao conjunto de dados e forneça uma descrição opcional. 

    1. Selecione **Ao lado** para abrir o **formulário de seleção de dados e arquivos**. Neste formulário você seleciona onde fazer upload do seu conjunto de dados; o recipiente de armazenamento padrão que é criado automaticamente com seu espaço de trabalho ou escolher um recipiente de armazenamento que você deseja usar para o experimento. 

    1. Revise as **Configurações e o** formulário de visualização para obter precisão. O formulário é preenchido de forma inteligente com base no tipo de arquivo. 

        Campo| Descrição
        ----|----
        Formato de arquivo| Define o layout e o tipo de dados armazenados em um arquivo.
        Delimitador| Um ou mais caracteres para especificar o limite entre regiões separadas e independentes em texto sem formatação ou outros fluxos de dados.
        Codificação| Identifica qual tabela de esquema de bit para caractere usar para ler seu conjunto de dados.
        Cabeçalhos da coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.
        Ignorar linhas | Indica quantas linhas, se houver, serão ignoradas no conjunto de registros.
    
        Selecione **Avançar**.

    1. O **formulário Esquema** é preenchido de forma inteligente com base nas seleções no formulário **Configurações e na visualização.** Aqui configure o tipo de dados para cada coluna, revise os nomes das colunas e selecione quais colunas **não incluir** para o seu experimento. 
            
        Selecione **A seguir.**

    1. O formulário **Confirmar detalhes** é um resumo das informações previamente preenchidas nos **formulários de informações básicas** e **configurações e pré-visualização.** Você também tem a opção de criar um perfil de dados para o seu conjunto de dados usando um cálculo habilitado para criação de perfil. Saiba mais sobre a [criação de perfil de dados](#profile).

        Selecione **Avançar**.
1. Selecione seu conjunto de dados recém-criado assim que ele aparecer. Você também pode visualizar uma visualização do conjunto de dados e estatísticas de amostra. 

1. No **formulário Configurar executar, digite** um nome de experimento único.

1. Selecione uma coluna de destino; esta é a coluna que você gostaria de fazer previsões.

1. Selecione um cálculo para o trabalho de criação e treinamento de perfil de dados. Uma lista de seus cálculos existentes está disponível no dropdown. Para criar um novo cálculo, siga as instruções da etapa 7.

1. Selecione **Criar uma nova computação** para configurar seu contexto de computação para este experimento.

    Campo|Descrição
    ---|---
    Nome da computação| Insira um nome exclusivo que identifique o contexto da computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual da computação.
    Número mín./máx. de nós (em Configurações Avançadas)| Para analisar os dados, é necessário especificar um ou mais nós. Digite o número máximo de nós para o seu cálculo. O padrão é de 6 nós para um AmL Compute.
    
    Selecione **Criar**. A criação de um novo cálculo pode levar alguns minutos.

    >[!NOTE]
    > Seu nome de computação indicará se a computação que você selecionar/criar estiver *ativando o perfil*. (Consulte o perfil de [dados](#profile) da seção para obter mais detalhes).

    Selecione **Avançar**.

1. No **formulário Tipo e configurações** da Tarefa, selecione o tipo de tarefa: classificação, regressão ou previsão. Veja [como definir tipos de tarefas](how-to-define-task-type.md) para obter mais informações.

    1. Para classificação, você também pode habilitar o aprendizado profundo que é usado para featurizações de texto.

    1. Para previsão:
        1. Seleção da coluna tempo: Esta coluna contém os dados de tempo a serem usados.

        1. Selecione horizonte de previsão: Indique quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever o futuro. Quanto mais o modelo for necessário para prever o futuro, menos preciso ele se tornará. [Saiba mais sobre previsão e horizonte de previsão](how-to-auto-train-forecast.md).

1. (Opcional) Exibir configurações de configuração de adição: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento. Caso contrário, os padrões são aplicados com base na seleção e nos dados de experimento. 

    Configurações adicionais|Descrição
    ------|------
    Métrica principal| Métrica principal usada para marcar seu modelo. [Saiba mais sobre métricas de modelos](how-to-configure-auto-train.md#explore-model-metrics).
    Personalização automática| Selecione para ativar ou desativar o pré-processamento feito por aprendizado de máquina automatizado. O pré-processamento inclui limpeza automática de dados, preparação e transformação para gerar recursos sintéticos. Não suportado para o tipo de tarefa de previsão de séries tempo. [Saiba mais sobre o pré-processamento.](#featurization) 
    Explique o melhor modelo | Selecione para ativar ou desativar para mostrar a explicação do melhor modelo recomendado
    Algoritmo bloqueado| Selecione algoritmos que você deseja excluir do trabalho de treinamento.
    Critério de saída| Quando qualquer um desses critérios são atendidos, o trabalho de treinamento é interrompido. <br> *Tempo de trabalho de treinamento (horas)*: Quanto tempo para permitir que o trabalho de treinamento corra. <br> *Limite de pontuação métrica*: Pontuação métrica mínima para todos os pipelines. Isso garante que, se você tiver uma métrica de destino definida que deseja alcançar, você não gaste mais tempo no trabalho de treinamento do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de treinamento. [Saiba mais sobre validação cruzada.](how-to-configure-auto-train.md)
    Simultaneidade| *Iterações simultâneas máximas*: Número máximo de pipelines (iterações) para testar no trabalho de treinamento. O trabalho não será executado mais do que o número especificado de iterações.

1. (Opcional) Exibir configurações de featurização: se você optar por ativar a **featurização automática** no formulário **configurações adicionais,** este formulário é onde você especifica quais colunas realizar essas featurizations e seleciona qual valor estatístico usar para imputações de valor perdidos.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Estatísticas de & de perfil de dados

Você pode obter uma grande variedade de estatísticas de resumo em seu conjunto de dados para verificar se seu conjunto de dados está pronto para ML. Para colunas não numéricas, elas incluem apenas estatísticas básicas como min, max e contagem de erros. Para colunas numéricas, você também pode rever seus momentos estatísticos e quantiles estimados. Especificamente, nosso perfil de dados inclui:

>[!NOTE]
> Entradas em branco aparecem para recursos com tipos irrelevantes.

Estatística|Descrição
------|------
Recurso| Nome da coluna que está sendo resumido.
Perfil| Visualização em linha com base no tipo inferido. Por exemplo, cordas, booleanos e datas terão contagem de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isso permite que você obtenha uma rápida compreensão da distribuição dos dados.
Distribuição de tipo| Contagem de valor em linha de tipos dentro de uma coluna. Os nulos são do seu próprio tipo, por isso esta visualização é útil para detectar valores ímpares ou ausentes.
Type|Tipo inferido da coluna. Os valores possíveis incluem: cordas, booleanos, datas e decimais.
Mín| Valor mínimo da coluna. Entradas em branco aparecem para características cujo tipo não tenha um pedido inerente (por exemplo, booleanos).
Max| Valor máximo da coluna. 
Contagem| Número total de entradas ausentes e não ausentes na coluna.
Sem contagem faltando| Número de entradas na coluna que não estão faltando. As cordas e os erros vazios são tratados como valores, para que não contribuam para a "contagem não faltante".
Quantis| Valores aproximados em cada quantile para fornecer uma noção da distribuição dos dados.
Média| Média aritmética ou média da coluna.
Desvio padrão| Medir a quantidade de dispersão ou variação dos dados desta coluna.
Variance| A medida de quão longe os dados desta coluna é do seu valor médio. 
Distorção| Medir o quão diferentes os dados desta coluna são de uma distribuição normal.
Curtose| A medida de quão fortemente seguidos os dados desta coluna é comparada a uma distribuição normal.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Opções avançadas de featurização

O aprendizado automatizado de máquina oferece pré-processamento e guardrails de dados automaticamente, para ajudá-lo a identificar e gerenciar possíveis problemas com seus dados. 

### <a name="preprocessing"></a>Pré-processamento

> [!NOTE]
> Se você planeja exportar seus modelos criados por ML automáticos para um [modelo ONNX,](concept-onnx.md)apenas as opções de featurização indicadas com um * são suportadas no formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). 

|Etapas&nbsp;de pré-processamento| Descrição |
| ------------- | ------------- |
|Solte alta cardinalidade ou sem características de variância* |Retire-os de conjuntos de treinamento e validação, incluindo recursos com todos os valores ausentes, mesmo valor em todas as linhas ou com cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).|
|Impute valores ausentes* |Para características numéricas, imputar com média de valores na coluna.<br/><br/>Para características categóricas, imputar com o valor mais freqüente.|
|Gerar recursos adicionais* |Para data-hora características: Ano, Mês, Dia, Dia da semana, Dia do Ano, Trimestre, Semana do Ano, Hora, Minuto, Segundo.<br/><br/>Para características de texto: Freqüência de termo baseada em unigramas, bigramas e tri-caracteres-gramas.|
|Transformar e codificar *|Características numéricas com poucos valores únicos são transformadas em características categóricas.<br/><br/>A codificação de um hot é realizada para baixa cardinalidade categórica; para alta cardinalidade, codificação de hash de um quente.|
|Incorporações de palavras|Featurizer de texto que converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de incorporação de cada palavra em um documento é agregado para produzir um vetor de recurso de documento.|
|Codificações de destino|Para características categóricas, mapeia cada categoria com valor-alvo médio para problemas de regressão e a probabilidade de classe para cada classe para problemas de classificação. A ponderação baseada em freqüência e a validação cruzada k-fold são aplicadas para reduzir a montagem do mapeamento e do ruído causados por categorias de dados esparsas.|
|Codificação de destino de texto|Para entrada de texto, um modelo linear empilhado com saco de palavras é usado para gerar a probabilidade de cada classe.|
|Peso das Evidências (WoE)|Calcula woe como uma medida de correlação de colunas categóricas com a coluna alvo. É calculado como o registro da razão de probabilidades in-class vs fora de classe. Esta etapa produz uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente valores ausentes e tratamento estranho.|
|Distância do cluster|Treina um modelo de agrupamento k-significa em todas as colunas numéricas.  Saídas k novos recursos, uma nova característica numérica por cluster, contendo a distância de cada amostra para o centróide de cada cluster.|

### <a name="data-guardrails"></a>Guardrails de dados

Os guardrails de dados são aplicados quando a featurização automática é ativada ou a validação é definida como automática. Os guardrails de dados ajudam a identificar possíveis problemas com seus dados (por exemplo, valores ausentes, desequilíbrio de classe) e ajudam a tomar ações corretivas para melhores resultados. 

Os usuários podem rever guardrails de dados no estúdio dentro da guia ```show_output=True``` **Data guardrails** de uma execução automatizada de ML ou configurando ao enviar um experimento usando o Python SDK. 

#### <a name="data-guardrail-states"></a>Estados de Guardrail de Dados

Os guardrails de dados exibirão um dos três estados: **Passado,** **Feito**ou **Alertado**.

Estado| Descrição
----|----
Aprovado| Nenhum problema de dados foi detectado e nenhuma ação do usuário é necessária. 
Concluído| Alterações foram aplicadas aos seus dados. Encorajamos os usuários a revisar as ações corretivas que o ML automatizado tomou para garantir que as mudanças estejam alinhadas com os resultados esperados. 
Alertado| Foi detectado um problema de dados que não pôde ser corrigido. Encorajamos os usuários a revisar e corrigir o problema. 

>[!NOTE]
> Versões anteriores de experimentos ML automatizados exibiam um quarto estado: **Fixo**. Experimentos mais novos não exibirão este estado, e todos os guardrails que exibiam o estado **fixo** agora exibirão **Feito**.   

A tabela a seguir descreve os guardrails de dados suportados atualmente e os status associados que os usuários podem encontrar ao enviar seu experimento.

Guardrail|Status|Condição&nbsp;&nbsp;para o gatilho
---|---|---
Imputação de valores de recurso ausente |**Aprovado** <br><br><br> **Concluído**| Não foram detectados valores de recurso ausentes em seus dados de treinamento. Saiba mais sobre [a imputação de valor perdido.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Os valores de recurso perdidos foram detectados em seus dados de treinamento e imputados.
Alta cardinalidade de manuseio de características |**Aprovado** <br><br><br> **Concluído**| Suas entradas foram analisadas, e nenhuma característica de alta cardinalidade foi detectada. Saiba mais sobre [a detecção de recursos de alta cardinalidade.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Características de alta cardinalidade foram detectadas em suas entradas e foram tratadas.
Manipulação dividida de validação |**Concluído**| *A configuração de validação foi definida como 'auto' e os dados de treinamento continham **menos** de 20.000 linhas.* <br> Cada iteração do modelo treinado foi validada por validação cruzada. Saiba mais sobre [dados de validação.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *A configuração de validação foi definida como 'auto' e os dados de treinamento continham **mais** de 20.000 linhas.* <br> Os dados de entrada foram divididos em um conjunto de dados de treinamento e um conjunto de dados de validação para validação do modelo.
Detecção de balanceamento de classe |**Aprovado** <br><br><br><br> **Alertado** | Suas entradas foram analisadas, e todas as classes são equilibradas em seus dados de treinamento. Um conjunto de dados é considerado equilibrado se cada classe tiver uma boa representação no conjunto de dados, medida pelo número e razão das amostras. <br><br><br> Classes desequilibradas foram detectadas em suas entradas. Para corrigir o viés do modelo, corrija o problema de equilíbrio. Saiba mais sobre [dados desequilibrados.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
Detecção de problemas de memória |**Aprovado** <br><br><br><br> **Concluído** |<br> Os valores selecionados {horizon, lag, rolling window} foram analisados e não foram detectados possíveis problemas fora da memória. Saiba mais sobre as configurações de previsão de [séries tempois.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>Os valores selecionados {horizonte, lag, janela de rolagem} foram analisados e potencialmente farão com que seu experimento fique sem memória. As configurações de lag ou janela de rolamento foram desligadas.
Detecção de frequência |**Aprovado** <br><br><br><br> **Concluído** |<br> A série temporal foi analisada e todos os pontos de dados estão alinhados com a frequência detectada. <br> <br> A série temporal foi analisada e foram detectados pontos de dados que não se alinham com a frequência detectada. Esses pontos de dados foram removidos do conjunto de dados. Saiba mais sobre [a preparação de dados para previsão de séries tempois.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>Executar experimentos e ver resultados

Selecione **Terminar** para executar sua experiência. O processo de preparação do experimento pode levar até 10 minutos. Os trabalhos de treinamento podem levar mais 2 a 3 minutos para que cada pipeline termine a execução.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

A **tela 'Executar detalhes'** é aberta na guia **Detalhes.** Esta tela mostra um resumo da execução do experimento, incluindo uma barra de status na parte superior ao lado do número de execução. 

A guia **Modelos** contém uma lista dos modelos criados ordenados pela pontuação da métrica. Por padrão, o modelo com a pontuação mais alta de acordo com a métrica escolhida é exibido no início da lista. Como o trabalho de treinamento testa mais modelos, eles são adicionados à lista. Use isso para obter uma comparação rápida das métricas dos modelos produzidos até agora.

[![Executar painel de detalhes](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Ver detalhes da execução do treinamento

Aprofunde-se em qualquer um dos modelos concluídos para ver detalhes da execução do treinamento, como métricas de execução na guia **Detalhes do Modelo** ou gráficos de desempenho na guia **Visualizações.** [Saiba mais sobre gráficos](how-to-understand-automated-ml.md).

[![Detalhes da iteração](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implantar o seu modelo

Uma vez que você tem o melhor modelo em mãos, é hora de implantá-lo como um serviço web para prever novos dados.

O ML automatizado ajuda você a implantar o modelo sem escrever código:

1. Você tem algumas opções para implantação. 

    + Opção 1: Para implantar o melhor modelo (de acordo com os critérios métricos definidos), **selecione** o botão Implantar o melhor modelo na guia **Detalhes.**

    + Opção 2: Para implantar uma iteração de modelo específica a partir deste experimento, aprofunde o modelo para abrir sua guia **de detalhes do modelo** e selecione Implantar **modelo**.

1. Preencha o painel **do modelo Implantar.**

    Campo| Valor
    ----|----
    Nome| Digite um nome exclusivo para sua implantação.
    Descrição| Digite uma descrição para identificar melhor para que é essa implantação.
    Tipo de computação| Selecione o tipo de ponto final que deseja implantar: *Azure Kubernetes Service (AKS)* ou *Azure Container Instance (ACI)*.
    Nome da computação| *Aplica-se apenas a AKS:* Selecione o nome do cluster AKS para o que deseja implantar.
    Habilitar autenticação | Selecione para permitir autenticação baseada em tokens ou baseada em chaves.
    Usar ativos da implantação personalizada| Habilite esse recurso se quiser fazer upload do seu próprio script de pontuação e arquivo de ambiente. [Saiba mais sobre os scripts de pontuação](how-to-deploy-and-where.md#script).

    >[!Important]
    > Os nomes dos arquivos devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricas. Pode incluir traços, sublinhados, apontates e alfanuméricas entre. Espaços não são permitidos.

    O *menu Avançado* oferece recursos de implantação padrão, como [configurações de coleta de dados](how-to-enable-app-insights.md) e utilização de recursos. Se você deseja substituir esses padrões faça isso neste menu.

1. Selecione **Implantar**. A implantação pode levar cerca de 20 minutos para ser concluída.

Agora você tem um serviço web operacional para gerar previsões! Você pode testar as previsões consultando o serviço do [Power BI's construído no suporte de Machine Learning do Azure](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Próximas etapas

* [Aprenda a consumir um serviço web.](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)
* [Entenda os resultados automatizados de aprendizado de máquina.](how-to-understand-automated-ml.md)
* [Saiba mais sobre machine learning automatizado](concept-automated-ml.md) e Machine Learning do Azure.
