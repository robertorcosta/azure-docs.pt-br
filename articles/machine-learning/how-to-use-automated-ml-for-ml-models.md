---
title: Usar o autoML para criar modelos & implantar
titleSuffix: Azure Machine Learning
description: Crie, revise e implante modelos de aprendizado de máquina automatizados com o Azure Machine Learning.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257220"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Crie, revise e implante modelos de aprendizado de máquina automatizados com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprenderá a criar, explorar e implantar modelos de aprendizado de máquina automatizados sem uma única linha de código na interface do estúdio do Azure Machine Learning. O Machine Learning automatizado é um processo no qual o melhor algoritmo de aprendizado de máquina a ser usado para seus dados específicos é selecionado para você. Esse processo permite que você gere modelos de aprendizado de máquina rapidamente. [Saiba mais sobre o aprendizado de máquina automatizado](concept-automated-ml.md).
 
Para obter um exemplo de ponta a ponta, experimente o [tutorial para criar um modelo de classificação com a interface ml automatizada do Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

Para uma experiência baseada em código do Python, [Configure seus experimentos de aprendizado de máquina automatizados](how-to-configure-auto-train.md) com o SDK do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um espaço de trabalho Azure Machine Learning com um tipo de **edição Enterprise**. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).  Para atualizar um espaço de trabalho existente para o Enterprise Edition, consulte [atualizar para o Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introdução

1. Entre no Azure Machine Learning em https://ml.azure.com. 

1. Selecione sua assinatura e seu espaço de trabalho. 

1. Navegue até o painel esquerdo. Selecione **ml automatizado** na seção **autor** .

[![Painel de navegação do Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Se esta for a primeira vez que você faz qualquer experimento, você verá uma lista vazia e links para a documentação. 

Caso contrário, você verá uma lista de experiências de aprendizado de máquina automatizadas recentes, incluindo aquelas criadas com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar experimento

1. Selecione **+ nova execução automática de ml** e preencha o formulário.

1. Selecione um conjunto de uma do seu contêiner de armazenamento ou crie um novo conjunto de um. Os conjuntos de valores podem ser criados a partir de arquivos locais, URLs da Web, repositórios de armazenamento ou conjuntos de os Azure Open DataSets. 

    >[!Important]
    > Requisitos para dados de treinamento:
    >* Os dados devem estar no formato de tabela.
    >* O valor que você deseja prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de um de um arquivo em seu computador local, selecione **procurar** e, em seguida, selecione o arquivo. 

    1. Dê um nome exclusivo ao conjunto de dados e forneça uma descrição opcional. 

    1. Selecione **Avançar** para abrir o **formulário repositório de armazenamento e seleção de arquivo**. Neste formulário, você seleciona onde carregar seu conjunto de seus conjuntos de seus. o contêiner de armazenamento padrão criado automaticamente com seu espaço de trabalho ou escolha um contêiner de armazenamento que você deseja usar para o experimento. 

    1. Examine as **configurações e** o formulário de visualização para obter precisão. O formulário é populado de forma inteligente com base no tipo de arquivo. 

        Campo| Descrição
        ----|----
        Formato de arquivo| Define o layout e o tipo de dados armazenados em um arquivo.
        Delimitador| Um ou mais caracteres para especificar o limite entre regiões separadas e independentes em texto sem formatação ou outros fluxos de dados.
        Codificação| Identifica qual tabela de esquema de bit para caractere usar para ler seu conjunto de dados.
        Cabeçalhos da coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.
        Ignorar linhas | Indica quantas linhas, se houver, serão ignoradas no conjunto de registros.
    
        Selecione **Avançar**.

    1. O formulário de **esquema** é preenchido de forma inteligente com base nas seleções no formulário **configurações e visualização** . Aqui configure o tipo de dados para cada coluna, examine os nomes de coluna e selecione quais colunas **não incluir** para o experimento. 
            
        Selecione **Avançar.**

    1. O formulário **confirmar detalhes** é um resumo das informações previamente populadas nas **informações básicas** e **nas configurações e na visualização** de formulários. Você também tem a opção de criar um perfil de dados para o seu DataSet usando uma computação de criação de perfil habilitada. Saiba mais sobre a [criação de perfil de dados](#profile).

        Selecione **Avançar**.
1. Selecione o conjunto de seus conjuntos de seu recém-criado quando ele for exibido. Você também pode exibir uma visualização do conjunto de exemplos e estatísticas de exemplo. 

1. No formulário **configurar execução** , insira um nome de teste exclusivo.

1. Selecione uma coluna de destino; Essa é a coluna na qual você gostaria de fazer previsões.

1. Selecione uma computação para o trabalho de criação de perfil de dados e treinamento. Uma lista de suas computações existentes está disponível na lista suspensa. Para criar uma nova computação, siga as instruções na etapa 7.

1. Selecione **criar uma nova computação** para configurar o contexto de computação para este experimento.

    Campo|Descrição
    ---|---
    Nome da computação| Insira um nome exclusivo que identifique o contexto da computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual da computação.
    Número mín./máx. de nós (em Configurações Avançadas)| Para analisar os dados, é necessário especificar um ou mais nós. Insira o número máximo de nós para sua computação. O padrão é 6 nós para uma computação AML.
    
    Selecione **Criar**. A criação de uma nova computação pode levar alguns minutos.

    >[!NOTE]
    > Seu nome de computação indicará se a computação que você selecionou/criar está com a *criação de perfil habilitada*. (Consulte a seção [criação de perfil de dados](#profile) para obter mais detalhes).

    Selecione **Avançar**.

1. No formulário **tipo de tarefa e configurações** , selecione o tipo de tarefa: classificação, regressão ou previsão. Consulte [como definir tipos de tarefa](how-to-define-task-type.md) para obter mais informações.

    1. Para classificação, você também pode habilitar o aprendizado profundo, que é usado para featurizations de texto.

    1. Para previsão:
        1. Selecionar coluna de tempo: esta coluna contém os dados de hora a serem usados.

        1. Selecione previsão Horizonte: indica quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais o modelo for necessário para prever no futuro, menor será a sua precisão. [Saiba mais sobre previsão e previsão horizonte](how-to-auto-train-forecast.md).

1. Adicional Exibir definições de configuração de adição: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento. Caso contrário, os padrões são aplicados com base na seleção e nos dados de experimento. 

    Configurações adicionais|Descrição
    ------|------
    Métrica principal| Métrica principal usada para pontuar seu modelo. [Saiba mais sobre métricas de modelo](how-to-configure-auto-train.md#explore-model-metrics).
    Personalização automática| Selecione para habilitar ou desabilitar o pré-processamento feito pelo Machine Learning automatizado. O pré-processamento inclui a limpeza, preparação e transformação automáticas de dados para gerar recursos sintéticos. Sem suporte para o tipo de tarefa previsão de série temporal. [Saiba mais sobre o pré-processamento](#featurization). 
    Explicar o melhor modelo | Selecione para habilitar ou desabilitar para mostrar a explicação do melhor modelo recomendado
    Algoritmo bloqueado| Selecione os algoritmos que você deseja excluir do trabalho de treinamento.
    Critério de saída| Quando qualquer um desses critérios for atendido, o trabalho de treinamento será interrompido. <br> *Tempo de trabalho de treinamento (horas)*: por quanto tempo permitir que o trabalho de treinamento seja executado. <br> *Limite de Pontuação de métrica*: Pontuação de métrica mínima para todos os pipelines. Isso garante que, se você tiver uma métrica de destino definida que deseja alcançar, não gaste mais tempo no trabalho de treinamento do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de treinamento. [Saiba mais sobre a validação cruzada](how-to-configure-auto-train.md).
    Simultaneidade| *Máximo de iterações simultâneas*: número máximo de pipelines (iterações) a serem testados no trabalho de treinamento. O trabalho não será executado mais do que o número especificado de iterações.

1. Adicional Exibir configurações de personalização: se você optar por habilitar o **personalização automático** no formulário de **definições de configuração adicional** , esse formulário será onde você especifica em quais colunas executar essas featurizations e seleciona qual valor estatístico usar para o valor ausente imputations.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Criação de perfil de dados & estatísticas de resumo

Você pode obter uma grande variedade de estatísticas de resumo em seu conjunto de dados para verificar se o conjunto de dados está pronto para ML. Para colunas não numéricas, elas incluem apenas estatísticas básicas, como mín., máx. e contagem de erros. Para colunas numéricas, você também pode revisar seus momentos estatísticos e quantis estimados. Especificamente, nosso perfil de dados inclui:

>[!NOTE]
> Entradas em branco aparecem para recursos com tipos irrelevantes.

Estatística|Descrição
------|------
Recurso| Nome da coluna que está sendo resumida.
Perfil| Visualização embutida com base no tipo inferido. Por exemplo, cadeias de caracteres, Boolianos e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isso permite que você tenha uma compreensão rápida da distribuição dos dados.
Distribuição de tipo| Valor na linha contagem de tipos dentro de uma coluna. Os nulos são de seu próprio tipo, portanto, essa visualização é útil para detectar valores ímpares ou ausentes.
Type|Tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, Boolianos, datas e decimais.
Mín| Valor mínimo da coluna. Entradas em branco aparecem para recursos cujo tipo não tem uma ordenação inerente (por exemplo, Boolianos).
Max| Valor máximo da coluna. 
Contagem| Número total de entradas ausentes e não ausentes na coluna.
Sem contagem faltando| Número de entradas na coluna que não estão ausentes. Cadeias de caracteres e erros vazios são tratados como valores, portanto, eles não contribuirão para a "contagem não encontrada".
Quantis| Valores aproximados em cada Quantil para fornecer uma noção da distribuição dos dados.
Média| Média aritmética ou médio da coluna.
Desvio padrão| Medida da quantidade de dispersão ou variação dos dados desta coluna.
Variance| A medida de difundir os dados desta coluna é de seu valor médio. 
Distorção| Medida de quão diferentes os dados dessa coluna são de uma distribuição normal.
Curtose| A medida de quão cauda os dados desta coluna é comparada a uma distribuição normal.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Opções de personalização avançadas

O Machine Learning automatizado oferece pré-processamento e guardrails de dados automaticamente, para ajudá-lo a identificar e gerenciar possíveis problemas com seus dados. 

### <a name="preprocessing"></a>Pré-processamento

> [!NOTE]
> Se você planeja exportar seus modelos de ML criados automaticamente para um [modelo ONNX](concept-onnx.md), somente as opções de personalização indicadas com um * têm suporte no formato ONNX. Saiba mais sobre como [converter modelos em ONNX](concept-automated-ml.md#use-with-onnx). 

|&nbsp;Etapas de pré-processamento| Descrição |
| ------------- | ------------- |
|Descartar alta cardinalidade ou nenhum recurso de variação * |Descarte-os dos conjuntos de treinamento e validação, incluindo recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou com cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).|
|Imputar valores ausentes * |Para recursos numéricos, imputar com a média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|Gerar recursos adicionais * |Para recursos de data e hora: ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para recursos de texto: a frequência de termos com base em unigrams, bi-grams e Tri-Character-grams.|
|Transformar e codificar *|Recursos numéricos com poucos valores exclusivos são transformados em recursos categóricos.<br/><br/>A codificação One-Hot é executada para uma baixa cardinalidade categórica; para alta cardinalidade, codificação One-Hot-hash.|
|Incorporações de palavras|Texto featurizer que converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de incorporação de cada palavra em um documento é agregado em conjunto para produzir um vetor de recurso de documento.|
|Codificações de destino|Para recursos categóricos, o mapeia cada categoria com o valor de destino médio para problemas de regressão e a probabilidade de classe de cada classe para problemas de classificação. O peso baseado em frequência e a validação cruzada de k-fold são aplicados para reduzir o ajuste do mapeamento e do ruído causados por categorias de dados esparsas.|
|Codificação de destino de texto|Para entrada de texto, um modelo linear empilhado com conjunto de palavras é usado para gerar a probabilidade de cada classe.|
|Peso de evidência (WoE)|Calcula WoE como uma medida de correlação de colunas categóricas para a coluna de destino. Ele é calculado como o log da taxa de probabilidades de fora de classe vs in-Class. Esta etapa gera uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente os valores ausentes e o tratamento de exceção.|
|Distância do cluster|Treina um modelo de clustering k-means em todas as colunas numéricas.  Gera novos recursos e um novo recurso numérico por cluster, contendo a distância de cada amostra para o centróide de cada cluster.|

### <a name="data-guardrails"></a>Guardrails de dados

Os dados guardrails são aplicados quando o personalização automático está habilitado ou a validação é definida como automática. Os data guardrails ajudam a identificar possíveis problemas com seus dados (por exemplo, valores ausentes, desequilíbrio de classe) e ajudam a tomar medidas corretivas para resultados aprimorados. 

Os usuários podem examinar os dados guardrails no estúdio na guia **Data guardrails** de uma execução de ml automatizada ou ```show_output=True``` definindo ao enviar um experimento usando o SDK do Python. 

#### <a name="data-guardrail-states"></a>Estados de guardrail de dados

Os Guardrails de dados exibirão um dos três Estados: **aprovado**, **concluído**ou **alertado**.

Estado| Descrição
----|----
Aprovado| Nenhum problema de dados foi detectado e nenhuma ação do usuário é necessária. 
Concluído| As alterações foram aplicadas aos seus dados. Incentivamos os usuários a examinar as ações corretivas que o ML automatizado levou para garantir que as alterações se alinhem aos resultados esperados. 
Alertado| Um problema de dados que não pôde ser remediado foi detectado. Incentivamos os usuários a revisar e corrigir o problema. 

>[!NOTE]
> Versões anteriores de experimentos de ML automatizados exibiram um quarto estado: **corrigido**. Experimentos mais recentes não exibirão esse Estado e todos os guardrails **que exibirem**o estado **fixo** agora serão exibidos.   

A tabela a seguir descreve os dados guardrails com suporte no momento e os status associados que os usuários podem chegar ao enviar o experimento.

Guardrail|Status|Condição&nbsp;para&nbsp;gatilho
---|---|---
Valores de recurso ausentes imputação |**Aprovado** <br><br><br> **Concluído**| Nenhum valor de recurso ausente foi detectado nos dados de treinamento. Saiba mais sobre o [valor ausente de imputação.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Valores de recursos ausentes foram detectados nos dados de treinamento e imputados.
Manipulação de recursos de alta cardinalidade |**Aprovado** <br><br><br> **Concluído**| Suas entradas foram analisadas e nenhum recurso de cardinalidade alto foi detectado. Saiba mais sobre a [detecção de recursos de cardinalidade alta.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Os recursos de alta cardinalidade foram detectados nas suas entradas e foram manipulados.
Manipulação de divisão de validação |**Concluído**| *A configuração de validação foi definida como ' auto ' e os dados de treinamento continham **menos** de 20.000 linhas.* <br> Cada iteração do modelo treinado foi validada por meio de validação cruzada. Saiba mais sobre [os dados de validação.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> *A configuração de validação foi definida como ' auto ' e os dados de treinamento continham **mais** de 20.000 linhas.* <br> Os dados de entrada foram divididos em um conjunto de informações de treinamento e um conjunto de informações de validação para validação do modelo.
Detecção de balanceamento de classe |**Aprovado** <br><br><br><br> **Alertado** | Suas entradas foram analisadas e todas as classes são equilibradas em seus dados de treinamento. Um conjunto de um DataSet é considerado balanceado se cada classe tem uma boa representação no DataSet, conforme medido por número e proporção de exemplos. <br><br><br> As classes desbalanceadas foram detectadas em suas entradas. Para corrigir a diferença de modelo, corrija o problema de balanceamento. Saiba mais sobre [dados desequilibrados.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
Detecção de problemas de memória |**Aprovado** <br><br><br><br> **Concluído** |<br> Os valores de {horizonte, atraso, janela sem interrupção} selecionados foram analisados e não foram detectados problemas de falta de memória em potencial. Saiba mais sobre [as configurações de previsão](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) de série temporal. <br><br><br>Os valores selecionados {Horizonte, lag, janela sem interrupção} foram analisados e, potencialmente, farão com que o teste fique sem memória. As configurações de atraso ou janela sem interrupção foram desativadas.
Detecção de frequência |**Aprovado** <br><br><br><br> **Concluído** |<br> A série temporal foi analisada e todos os pontos de dados estão alinhados com a frequência detectada. <br> <br> A série temporal foi analisada e os pontos de dados que não estão alinhados com a frequência detectada foram detectados. Esses pontos de dados foram removidos do DataSet. Saiba mais sobre [a preparação de dados para a previsão de série temporal.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>Executar experimento e exibir resultados

Selecione **concluir** para executar seu experimento. O processo de preparação do experimento pode levar até 10 minutos. Os trabalhos de treinamento podem levar mais 2 a 3 minutos para que cada pipeline termine a execução.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

A tela de **detalhes da execução** é aberta na guia **detalhes** . Esta tela mostra um resumo da execução do experimento, incluindo uma barra de status na parte superior ao lado do número de execução. 

A guia **Modelos** contém uma lista dos modelos criados ordenados pela pontuação da métrica. Por padrão, o modelo com a pontuação mais alta de acordo com a métrica escolhida é exibido no início da lista. Como o trabalho de treinamento testa mais modelos, eles são adicionados à lista. Use isso para obter uma comparação rápida das métricas dos modelos produzidos até agora.

[![Painel de detalhes de execução](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Exibir detalhes da execução de treinamento

Faça uma busca detalhada em qualquer um dos modelos concluídos para ver detalhes de execução de treinamento, como executar métricas na guia **detalhes do modelo** ou gráficos de desempenho na guia **visualizações** . [saiba mais sobre gráficos](how-to-understand-automated-ml.md).

[![Detalhes da iteração](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implantar o seu modelo

Quando você tem o melhor modelo em mãos, é hora de implantá-lo como um serviço Web para prever novos dados.

O ML automatizado ajuda você a implantar o modelo sem escrever código:

1. Você tem algumas opções de implantação. 

    + Opção 1: para implantar o melhor modelo (de acordo com os critérios de métrica que você definiu), selecione o botão **implantar melhor modelo** na guia **detalhes** .

    + Opção 2: para implantar uma iteração de modelo específica deste experimento, faça uma busca detalhada no modelo para abrir a guia **detalhes do modelo** e selecione **implantar modelo**.

1. Preencha o painel **implantar modelo** .

    Campo| Valor
    ----|----
    Nome| Insira um nome exclusivo para sua implantação.
    Descrição| Insira uma descrição para identificar melhor a finalidade dessa implantação.
    Tipo de computação| Selecione o tipo de ponto de extremidade que você deseja implantar: *AKs (serviço kubernetes do Azure)* ou *instância de contêiner do Azure (ACI)*.
    Nome da computação| *Aplica-se somente a AKs:* Selecione o nome do cluster AKS no qual você deseja implantar.
    Habilitar autenticação | Selecione para permitir a autenticação baseada em token ou baseada em chave.
    Usar ativos da implantação personalizada| Habilite esse recurso se você quiser carregar seu próprio script de Pontuação e arquivo de ambiente. [Saiba mais sobre scripts de Pontuação](how-to-deploy-and-where.md#script).

    >[!Important]
    > Os nomes de arquivo devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Pode incluir traços, sublinhados, pontos e alfanuméricos entre. Espaços não são permitidos.

    O menu *avançado* oferece recursos de implantação padrão, como a [coleta de dados](how-to-enable-app-insights.md) e as configurações de utilização de recursos. Se você quiser substituir esses padrões, faça isso neste menu.

1. Selecione **Implantar**. A implantação pode levar cerca de 20 minutos para ser concluída.

Agora você tem um serviço Web operacional para gerar previsões! Você pode testar as previsões consultando o serviço do [suporte Azure Machine Learning interno Power bi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Próximas etapas

* [Saiba como consumir um serviço Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Entenda os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).
* [Saiba mais sobre o aprendizado de máquina](concept-automated-ml.md) e a Azure Machine Learning automatizados.
