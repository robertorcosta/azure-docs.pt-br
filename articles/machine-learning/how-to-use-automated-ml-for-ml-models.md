---
title: Usar o AutoML para criar modelos & implantar
titleSuffix: Azure Machine Learning
description: Crie, revise e implante modelos de machine learning automatizado com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: aniththa
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/10/2020
ms.openlocfilehash: ac5357d0f8ba03943af14d7dd4ce6928b20db128
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074627"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Criar, revisar e implantar modelos de machine learning automatizado com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprenderá a criar, explorar e implantar modelos de aprendizado de máquina automatizados sem uma única linha de código no Azure Machine Learning Studio.

>[!IMPORTANT]
> A experiência de ML automatizada no estúdio de Machine Learning do Azure está em versão prévia. Alguns recursos podem não ter suporte ou ter recursos limitados.

 O machine learning automatizado é um processo no qual o melhor algoritmo de machine learning a ser usado para seus dados específicos é selecionado para você. Esse processo permite que você gere modelos de machine learning rapidamente. [Saiba mais sobre o machine learning automatizado](concept-automated-ml.md).
 
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

1. Selecione um conjunto de dados no seu contêiner de armazenamento ou crie um novo. Os conjuntos de dados podem ser criados a partir de arquivos locais, URLs da Web, armazenamento de dados ou Azure Open Datasets. Saiba mais sobre a [criação do conjunto](how-to-create-register-datasets.md)de informações.  

    >[!Important]
    > Requisitos para dados de treinamento:
    >* Os dados devem estar em formato de tabela.
    >* O valor que você quer prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de um de um arquivo em seu computador local, selecione **+ criar conjunto** de um e, em seguida, selecione **do arquivo local**. 

    1. No formulário **informações básicas** , dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Avançar** para abrir o **Formulário de armazenamento de dados e de seleção de arquivos**. Nesse formulário, você seleciona onde quer carregar seu conjunto de dados, o contêiner de armazenamento padrão que é criado automaticamente com seu espaço de trabalho, ou escolhe um contêiner de armazenamento que deseja usar para o experimento. 
    
        1. Se seus dados estiverem atrás de uma rede virtual, você precisará habilitar a função **ignorar a validação** para garantir que o espaço de trabalho possa acessar seus dados. Saiba mais sobre [isolamento de rede e privacidade](how-to-enable-virtual-network.md#machine-learning-studio). 
    
    1. Selecione **procurar** para carregar o arquivo de dados para o seu DataSet. 

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
    Prioridade da máquina virtual| As máquinas virtuais de baixa prioridade são mais baratas, mas não garantem os nós de computação. 
    Tipo de máquina virtual| Selecione CPU ou GPU para o tipo de máquina virtual.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual da computação.
    Nós mín./máx.| Para analisar os dados, é necessário especificar um ou mais nós. Insira o número máximo de nós da sua computação. Uma Computação do AML tem 6 nós como padrão.
    Configurações avançadas | Essas configurações permitem que você configure uma conta de usuário e uma rede virtual existente para seu experimento. 
    
    Selecione **Criar**. A criação da nova computação pode levar alguns minutos.

    >[!NOTE]
    > O nome da sua computação indicará se a computação que você selecionar/criar é *habilitada para criação de perfil*. (Consulte a seção [criação de perfil de dados](#profile) para obter mais detalhes.)

    Selecione **Avançar**.

1. No formulário **Tipo de tarefa e configurações**, selecione o tipo de tarefa: classificação, regressão ou previsão. Consulte [tipos de tarefas com suporte](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) para obter mais informações.

    1. Para **classificação**, você também pode habilitar o aprendizado profundo, que é usado para featurizations de texto.

    1. Para **previsão** , você pode, 
    
        1. Habilitar o aprendizado profundo
    
        1. Selecionar *coluna de tempo*: esta coluna contém os dados de hora a serem usados.

        1. Selecione *previsão horizonte*: indica quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais o modelo for usado para fazer previsões do futuro, menos preciso ele será. [Saiba mais sobre previsão e horizonte de previsão](how-to-auto-train-forecast.md).

1. (Opcional) Exibir definições de configuração adicionais: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento. Caso contrário, os padrões são aplicados com base na seleção e nos dados de experimento. 

    Configurações adicionais|Descrição
    ------|------
    Métrica principal| Métrica principal usada para a pontuação do seu modelo. [Saiba mais sobre as métricas do modelo](how-to-configure-auto-train.md#explore-model-metrics).
    Explicar o melhor modelo | Selecione para habilitar ou desabilitar o, a fim de mostrar a explicação do melhor modelo recomendado.
    Algoritmo bloqueado| Selecione os algoritmos que você deseja excluir do trabalho de treinamento.
    Critério de saída| Quando qualquer um desses critérios for atendido, o trabalho de treinamento é interrompido. <br> *Tempo do trabalho de treinamento (horas)* : o tempo permitido de execução do trabalho de treinamento. <br> *Limite de pontuação da métrica*:  pontuação mínima da métrica para todos os pipelines. Isso garante que, caso você tenha uma métrica de destino definida e que deseje alcançar, não gastará mais tempo no trabalho de treinamento do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para ser usada no trabalho de treinamento. [Saiba mais sobre a validação cruzada](how-to-configure-cross-validation-data-splits.md#prerequisites).
    Simultaneidade| *Máximo de iterações simultâneas*: número máximo de pipelines (iterações) a serem testados no trabalho de treinamento. O trabalho não será executado mais vezes do que o número de iterações especificado.

1. Adicional Exibir configurações de personalização: se você optar por habilitar o **personalização automático** no formulário de **definições de configuração adicional, as** técnicas padrão do personalização serão aplicadas. Em **exibir configurações de personalização** , você pode alterar esses padrões e personalizá-los adequadamente. Saiba como [Personalizar o featurizations](#customize-featurization). 

    ![Formulário tipo de tarefa do Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

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

## <a name="customize-featurization"></a>Personalizar o personalização

No formulário **personalização** , você pode habilitar/desabilitar o personalização automático e personalizar as configurações de personalização automática para o experimento. Para abrir esse formulário, consulte a etapa 10 na seção [criar e executar experimento](#create-and-run-experiment) . 

A tabela a seguir resume as personalizações disponíveis no momento por meio do estúdio. 

Coluna| Personalização
---|---
Incluso | Especifica quais colunas incluir para treinamento.
Tipo de recurso| Altere o tipo de valor da coluna selecionada.
Imputar com| Selecione o valor com o qual imputar valores ausentes em seus dados.

![Formulário tipo de tarefa do Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Executar o experimento e exibir os resultados

Selecione **Concluir**, para executar o experimento. O processo de preparação do experimento pode levar até 10 minutos. Os trabalhos de treinamento podem levar mais 2 a 3 minutos para que cada pipeline termine a execução.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

A tela **Executar detalhes** é aberta na guia **Detalhes**. Essa tela mostra um resumo da execução do experimento, incluindo uma barra de status na parte superior, ao lado do número de execução. 

A guia **Modelos** contém uma lista dos modelos criados ordenados pela pontuação da métrica. Por padrão, o modelo com a pontuação mais alta de acordo com a métrica escolhida é exibido no início da lista. Como o trabalho de treinamento testa mais modelos, eles são adicionados à lista. Use isso para obter uma comparação rápida das métricas dos modelos produzidos até agora.

[![Painel de detalhes da execução](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Exibir detalhes da execução do treinamento

Faça uma busca detalhada em qualquer um dos modelos concluídos para ver os detalhes da execução de treinamento, como um resumo do modelo na guia **modelo** ou nos gráficos de métrica de desempenho na guia **métricas** . [saiba mais sobre gráficos](how-to-understand-automated-ml.md).

[![Detalhes da iteração](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implantar o seu modelo

Assim que você tiver o melhor modelo em mãos, é hora de implantá-lo como um serviço Web para prever novos dados.

O ML automatizado ajuda a implantar o modelo sem escrever códigos:

1. Você tem algumas opções de implantação: 

    + Opção 1: implante o melhor modelo, de acordo com os critérios de métrica que você definiu. 
        1. Após a conclusão do experimento, navegue até a página de execução pai selecionando **executar 1** na parte superior da tela. 
        1.  Selecione o modelo listado na seção de **Resumo do melhor modelo** . 
        1. Selecione **implantar** na parte superior esquerda da janela. 

    + Opção 2: para implantar uma iteração de modelo específica deste experimento.
        1. Selecione o modelo desejado na guia **modelos**
        1. Selecione **implantar** na parte superior esquerda da janela.

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
    Depois que a implantação for iniciada, a guia **Resumo do modelo** será exibida. Consulte o progresso da implantação na seção **implantar status** . 

Agora você tem um serviço Web operacional para gerar previsões. Você pode testar as previsões por meio de consultas ao serviço de [Suporte ao Azure Machine Learning interno do Power BI](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Próximas etapas

* [Saiba como consumir um serviço Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Entender os resultados de machine learning automatizado](how-to-understand-automated-ml.md).
* [Saiba mais sobre o machine learning automatizado](concept-automated-ml.md) e o Azure Machine Learning.
