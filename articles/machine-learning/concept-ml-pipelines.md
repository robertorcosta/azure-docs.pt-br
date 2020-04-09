---
title: O que são gasodutos ML
titleSuffix: Azure Machine Learning
description: Neste artigo, aprenda as vantagens dos pipelines de aprendizado de máquina (ML) que você pode construir com o Azure Machine Learning SDK para Python. Os pipelines de aprendizado de máquina são usados por cientistas de dados para construir, otimizar e gerenciar seus fluxos de trabalho de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 0cefa78b6f52cc67df8817f68a9b793ab86b2a7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878571"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>O que são pipelines de Machine Learning do Azure?

Os pipelines de Machine Learning do Azure permitem que você crie fluxos de trabalho em seus projetos de aprendizado de máquina. Esses fluxos de trabalho têm uma série de benefícios: 

+ Simplicidade
+ Velocidade
+ Repetição
+ Flexibilidade
+ Versão e rastreamento
+ Modularidade 
+ Garantia de qualidade
+ Controle de custos

Esses benefícios se tornam significativos assim que seu projeto de aprendizado de máquina vai além da exploração pura e da iteração. Mesmo os simples oleodutos de um passo podem ser valiosos. Projetos de aprendizagem de máquina estão frequentemente em um estado complexo, e pode ser um alívio tornar a realização precisa de um único fluxo de trabalho um processo trivial.

Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md).

![Pipelines de aprendizado de máquina no Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Qual tecnologia de pipeline Do Azure devo usar?

A nuvem Azure fornece vários outros gasodutos, cada um com um propósito diferente. A tabela a seguir lista os diferentes pipelines e para que eles são usados:

| Cenário | Persona primária | Oferta do Azure | Oferta de OSS | Tubo canônico | Pontos fortes | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orquestração modelo (Aprendizado de máquina) | Cientista de dados | Pipelines do Azure Machine Learning | Gasodutos Kubeflow | Modelo de > de dados | Distribuição, cache, código primeiro, reutilização | 
| Orquestração de dados (preparação de dados) | Engenheiro de dados | [Pipelines do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Fluxo de ar do Apache | Dados -> | Movimento fortemente digitado. Atividades centradas em dados. |
| Orquestração de aplicativos de & de código (CI/CD) | Desenvolvedor de Aplicativos / Ops | [Azure DevOps Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Código + Modelo -> App/Service | Suporte de atividade mais aberto e flexível, filas de aprovação, fases com gating | 


## <a name="what-can-azure-ml-pipelines-do"></a>O que os gasodutos Azure ML podem fazer?

Um pipeline de Aprendizado de Máquina do Azure é um fluxo de trabalho executável independente mente de uma tarefa completa de aprendizado de máquina. As subtarefas são encapsuladas como uma série de etapas no pipeline. Um pipeline de Aprendizado de Máquina do Azure pode ser tão simples quanto aquele que chama um script Python, então _pode_ fazer qualquer coisa. Os pipelines _devem_ se concentrar em tarefas de aprendizado de máquina, tais como:

+ Preparação de dados, incluindo importação, validação e limpeza, mudanças irreversíveis e transformação, normalização e preparo
+ Configuração de treinamento, incluindo argumentos de parametrização, filePaths e configurações de log/relatório
+ Treinando e validando de forma eficiente e repetida. A eficiência pode vir da especificação de subconjuntos de dados específicos, diferentes recursos de computação de hardware, processamento distribuído e monitoramento de progresso
+ Implantação, incluindo controle de versão, dimensionamento, provisionamento e controle de acesso 

Etapas independentes permitem que vários cientistas de dados trabalhem no mesmo pipeline ao mesmo tempo sem sobretaxar recursos computacionais. Etapas separadas também facilitam o uso de diferentes tipos/tamanhos de computação para cada etapa.

Depois que o pipeline é criado, muitas vezes, há mais ajustes finos no loop de treinamento do pipeline. Quando você reexecuta um pipeline, a corrida salta para os passos que precisam ser reexecutados, como um script de treinamento atualizado. As etapas que não precisam ser reexecutadas são ignoradas. A mesma análise se aplica aos scripts inalterados utilizados para a realização da etapa. Essa funcionalidade de reutilização ajuda a evitar executar etapas caras e demoradas, como ingestão e transformação de dados, se os dados subjacentes não mudarem.

Com o Azure Machine Learning, você pode usar vários kits de ferramentas e frameworks, como PyTorch ou TensorFlow, para cada etapa do seu pipeline. O Azure coordena os vários [alvos de computação](concept-azure-machine-learning-architecture.md) que você usa, para que seus dados intermediários possam ser compartilhados com os alvos de computação a jusante.

Você pode [acompanhar as métricas para seus experimentos](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) de pipeline diretamente no portal Azure ou na sua página de [aterrissagem no espaço de trabalho (visualização)](https://ml.azure.com). Depois que um pipeline for publicado, você pode configurar um ponto final REST, que permite que você execute o pipeline de qualquer plataforma ou pilha.

Em suma, todas as tarefas complexas do ciclo de vida de aprendizagem de máquina podem ser ajudadas com pipelines. Outras tecnologias de pipeline do Azure têm suas próprias forças. [Os pipelines da Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) se destacam em trabalhar com dados e o [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é a ferramenta certa para integração e implantação contínuas. Mas se o seu foco é aprendizado de máquina, os pipelines de Machine Learning do Azure provavelmente serão a melhor escolha para suas necessidades de fluxo de trabalho. 

## <a name="what-are-azure-ml-pipelines"></a>O que são os oleodutos Azure ML?

Um pipeline Azure ML executa um fluxo de trabalho lógico completo com uma seqüência ordenada de etapas. Cada passo é uma ação de processamento discreta. Os pipelines são executados no contexto de um Experimento de Aprendizagem de Máquina [do Azure.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

Nos estágios iniciais de um projeto ML, é bom ter um único notebook Jupyter ou script Python que faz todo o trabalho de espaço de trabalho e configuração de recursos do Azure, preparação de dados, configuração de execução, treinamento e validação. Mas assim como funções e classes rapidamente se tornam preferíveis a um único bloco imperativo de código, os fluxos de trabalho ML rapidamente se tornam preferíveis a um notebook ou script monolítico. 

Ao modular tarefas ml, os pipelines suportam o imperativo da Ciência da Computação de que um componente deve "fazer (apenas) uma coisa bem". A modularidade é claramente vital para projetar o sucesso na programação em equipes, mas mesmo trabalhando sozinho, mesmo um pequeno projeto ML envolve tarefas separadas, cada uma com uma boa quantidade de complexidade. As tarefas incluem: configuração do espaço de trabalho e acesso a dados, preparação de dados, definição e configuração do modelo e implantação. Enquanto as saídas de uma ou mais tarefas formam as entradas para outra, os detalhes exatos de implementação de qualquer tarefa são, na melhor das hipóteses, distrações irrelevantes na próxima. Na pior das hipóteses, o estado computacional de uma tarefa pode causar um bug em outra. 

### <a name="analyzing-dependencies"></a>Analisando dependências

Muitos ecossistemas de programação possuem ferramentas que orquestram dependências de recursos, bibliotecas ou compilações. Geralmente, essas ferramentas usam carimbos de data-hora de arquivo para calcular dependências. Quando um arquivo é alterado, apenas ele e seus dependentes são atualizados (baixados, recompilados ou embalados). Os gasodutos Azure ML estendem esse conceito dramaticamente. Como as ferramentas tradicionais de construção, os pipelines calculam dependências entre as etapas e apenas realizam os recálculos necessários. 

A análise de dependência em pipelines Azure ML é mais sofisticada do que simples carimbos de tempo. Cada passo pode ser executado em um ambiente diferente de hardware e software. A preparação de dados pode ser um processo demorado, mas não precisa ser executado em hardware com GPUs poderosas, certas etapas podem exigir software específico do SO, você pode querer usar treinamento distribuído, e assim por diante. Embora a economia de custos para a otimização de recursos possa ser significativa, pode ser esmagadora para malabarizar manualmente todas as diferentes variações em hardware e recursos de software. É ainda mais difícil fazer tudo isso sem nunca cometer um erro nos dados que você transfere entre as etapas. 

Os oleodutos resolvem este problema. O Azure Machine Learning orquestra automaticamente todas as dependências entre as etapas do pipeline. Essa orquestração pode incluir girar para cima e para baixo imagens do Docker, anexar e desvincular recursos de computação e mover dados entre as etapas de forma consistente e automática.

### <a name="reusing-results"></a>Reutilização de resultados

Além disso, a saída de uma etapa pode, se você escolher, ser reutilizada. Se você especificar a reutilização como uma possibilidade e não houver dependências upstream que desencadeiem o recálculo, o serviço de pipeline usará uma versão em cache dos resultados da etapa. Tal reutilização pode diminuir drasticamente o tempo de desenvolvimento. Se você tem uma tarefa complexa de preparação de dados, você provavelmente reexecutá-la com mais freqüência do que é estritamente necessário. Os oleodutos o aliviam dessa preocupação: se necessário, o passo será executado, se não, não vai.

Toda essa análise de dependência, orquestração e ativação são tratadas pelo Azure Machine `Experiment`Learning quando `submit()`você instancia um objeto [pipeline,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) passa-o para um e chama . 

### <a name="coordinating-the-steps-involved"></a>Coordenação das etapas envolvidas

Quando você cria `Pipeline` e executa um objeto, ocorrem as seguintes etapas de alto nível:

+ Para cada etapa, o serviço calcula os requisitos para:
    + Recursos de computação de hardware
    + Recursos do Sistema Operacional (imagem docker))
    + Recursos de software (dependências Conda / virtualenv)
    + Entradas de dados 
+ O serviço determina as dependências entre as etapas, resultando em um gráfico de execução dinâmica
+ Quando cada nó no gráfico de execução é executado:
    + O serviço configura o ambiente de hardware e software necessário (talvez reutilizando os recursos existentes)
    + A etapa é executada, fornecendo informações `Experiment` de registro e monitoramento para seu objeto contendo
    + Quando a etapa é concluída, suas saídas são preparadas como entradas para o próximo passo e/ou escrita para armazenamento
    + Recursos que não são mais necessários são finalizados e separados

![Etapas do gasoduto](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Construindo gasodutos com o Python SDK

No [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), um pipeline `azureml.pipeline.core` é um objeto Python definido no módulo. Um objeto [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) contém uma seqüência ordenada de um ou mais objetos [PipelineStep.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) A `PipelineStep` classe é abstrata e as etapas reais serão de subclasses como [EstimatorStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)ou [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) mantém uma seqüência reutilizável de etapas que podem ser compartilhadas entre os pipelines. Uma `Pipeline` corrida como `Experiment`parte de um .

Um pipeline Azure ML está associado a um espaço de trabalho de Aprendizado de Máquina do Azure e uma etapa do pipeline está associada a um alvo de computação disponível dentro desse espaço de trabalho. Para obter mais informações, consulte [Criar e gerenciar espaços de trabalho de Machine Learning do Azure no portal Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) ou [Quais são os alvos de computação no Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

No Azure Machine Learning, um alvo computacional é o ambiente em que ocorre uma fase ML. O ambiente de software pode ser um VM remoto, Azure Machine Learning Compute, Azure Databricks, Azure Batch, e assim por diante. O ambiente de hardware também pode variar muito, dependendo do suporte à GPU, memória, armazenamento e assim por diante. Você pode especificar a meta de computação para cada etapa, o que lhe dá controle fino sobre os custos. Você pode usar recursos mais ou menos poderosos para as necessidades específicas de ação, volume de dados e desempenho do seu projeto. 

## <a name="building-pipelines-with-the-designer"></a>Construção de gasodutos com o designer

Desenvolvedores que preferem uma superfície de design visual podem usar o designer de Machine Learning do Azure para criar pipelines. Você pode acessar esta ferramenta a partir da seleção **Desenhista** na página inicial do seu espaço de trabalho.  O designer permite arrastar e soltar passos na superfície do design. Para um desenvolvimento rápido, você pode usar módulos existentes em todo o espectro de tarefas ML; Os módulos existentes abrangem tudo, desde a transformação de dados até a seleção de algoritmos até o treinamento até a implantação. Ou você pode criar um pipeline totalmente personalizado combinando seus próprios passos definidos em scripts Python.

Quando você projeta visualmente os pipelines, as entradas e saídas de uma etapa são exibidas visivelmente. Você pode arrastar e soltar conexões de dados, permitindo que você compreenda e modifique rapidamente o fluxo de dados do seu pipeline.
 
![Exemplo do Azure Machine Learning Designer](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Entendendo o gráfico de execução

As etapas dentro de um pipeline podem ter dependências de outras etapas. O serviço de gasodutos Azure ML faz o trabalho de analisar e orquestrar essas dependências. Os nós no "gráfico de execução" resultante estão processando etapas. Cada passo pode envolver a criação ou reutilização de uma determinada combinação de hardware e software, reutilização de resultados em cache, e assim por diante. A orquestração e otimização deste gráfico de execução do serviço pode acelerar significativamente uma fase ML e reduzir custos. 

Como as etapas são executadas de forma independente, os objetos para conter os dados de entrada e saída que fluem entre as etapas devem ser definidos externamente. Este é o papel de [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)e [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), objetos. Esses objetos de dados estão associados a um objeto [Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) que encapsula sua configuração de armazenamento. A `PipelineStep` classe base é sempre `name` criada com `inputs`uma string, `outputs`uma lista de , e uma lista de . Normalmente, ele também `arguments` tem uma lista de `resource_inputs`e muitas vezes ele terá uma lista de . As subclasses geralmente também terão argumentos `PythonScriptStep` adicionais (por exemplo, requer o nome do arquivo e o caminho do script para executar). 

O gráfico de execução é acíclico, mas os pipelines podem ser executados em um cronograma recorrente e podem executar scripts Python que podem escrever informações de estado para o sistema de arquivos, tornando possível criar perfis complexos. Se você projetar seu pipeline para que certas etapas possam ser executadas em paralelo ou assíncrona, o Azure Machine Learning lida de forma transparente com a análise de dependência e coordenação de fan-out e fan-in. Você geralmente não precisa se preocupar com os detalhes do gráfico de execução, mas ele está disponível através do atributo [Pipeline.graph.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 


### <a name="a-simple-python-pipeline"></a>Um simples Pipeline Python

Este trecho mostra os objetos e chamadas necessários para criar e executar um básico: `Pipeline`

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

O trecho começa com objetos comuns de `Workspace`Aprendizado `Datastore`de Máquina Azure, a , a , um [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)e um `Experiment`. Em seguida, o código `input_data` cria `output_data`os objetos para segurar e . A `steps` matriz contém um `PythonScriptStep` único elemento, que usará os `compute_target`objetos de dados e será executado no . Em seguida, o código `Pipeline` instancia o objeto em si, passando no espaço de trabalho e na matriz de passos. A chamada `experiment.submit(pipeline)` para começar a corrida do gasoduto Azure ML. A chamada `wait_for_completion()` para blocos até que o oleoduto esteja pronto. 

Para saber mais sobre como conectar seu pipeline aos seus dados, consulte os artigos [Acesso a dados no Azure Machine Learning](concept-data.md) and Moving data [sem e entre os passos do pipeline ML (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="best-practices-when-using-pipelines"></a>Melhores práticas ao usar gasodutos

Como você pode ver, criar um pipeline Azure ML é um pouco mais complexo do que iniciar um script. Os pipelines exigem que alguns objetos Python sejam configurados e criados. 

Algumas situações que sugerem o uso de um pipeline:

* Em um ambiente de equipe: divida as tarefas ml em várias etapas independentes para que os desenvolvedores possam trabalhar e evoluir seus programas de forma independente. 

* Quando dentro ou perto da implantação: pregue a configuração e use operações programadas e orientadas a eventos para ficar por dentro da alteração de dados.

* Nos estágios iniciais de um projeto ML ou trabalhando sozinho: use pipelines para automatizar a construção. Se você começou a se preocupar em recriar a configuração e o estado computacional antes de implementar uma nova ideia, esse é um sinal de que você pode considerar usar um pipeline para automatizar o fluxo de trabalho. 

É fácil se entusiasmar em reutilizar resultados em cache, controle fino sobre os custos de computação e isolamento de processos, mas os gasodutos têm custos. Alguns anti-padrões incluem:

* Usar os oleodutos como único meio para separar as preocupações. As funções, objetos e módulos incorporados do Python vão longe para evitar um estado programático confuso! Um passo de pipeline é muito mais caro do que uma chamada de função.

* Acoplamento pesado entre as etapas do gasoduto. Se refatorar um passo dependente com freqüência requer modificar as saídas de uma etapa anterior, é provável que etapas separadas sejam atualmente mais um custo do que um benefício. Outra pista de que as etapas estão excessivamente acopladas são argumentos para uma etapa que não são dados, mas bandeiras para controlar o processamento. 

* Otimização prematura de recursos computacionais. Por exemplo, muitas vezes há vários estágios para a preparação de dados e muitas `MpiStep` vezes pode-se ver "Oh, aqui está `PythonScriptStep` um lugar onde eu poderia usar um para programação paralela, mas aqui está um lugar onde eu poderia usar um com um alvo de computação menos poderoso", e assim por diante. E talvez, a longo prazo, criar passos finos como esse possa valer a pena, especialmente se houver a possibilidade de usar resultados em cache em vez de sempre recalcular. Mas os pipelines não se destinam a `multiprocessing` ser um substituto para o módulo nativo do Python. 

Até que um projeto seja grande ou próximo da implantação, seus gasodutos devem ser mais grossos do que finos. Se você pensar no seu projeto ML como envolvendo _etapas_ e um pipeline como fornecendo um fluxo de trabalho completo para movê-lo através de um estágio específico, você está no caminho certo. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens do uso de pipelines para seus fluxos de trabalho de aprendizado de máquina são:

|Principal vantagem|Descrição|
|:-------:|-----------|
|**Execuções&nbsp;autônomas**|Agende etapas para executar em paralelo ou em seqüência de forma confiável e desacompanhada. A preparação e modelagem de dados podem durar dias ou semanas, e os pipelines permitem que você se concentre em outras tarefas enquanto o processo está em execução. |
|**Computação heterogêneo**|Use vários pipelines que são coordenados de forma confiável entre recursos de computação heterogêneos e escaláveis e locais de armazenamento. Faça uso eficiente dos recursos de computação disponíveis executando etapas individuais de pipeline em diferentes alvos de computação, como HDInsight, VMs de Data Science de GPU e Databricks.|
|**Reutilização**|Crie modelos de pipeline para cenários específicos, como retreinamento e pontuação em lote. Acione os pipelines publicados de sistemas externos através de chamadas REST simples.|
|**Acompanhamento e controle de versão**|Em vez de acompanhar manualmente os caminhos de dados e resultados durante a iteração, use o SDK de pipelines para fornecer explicitamente o nome e a versão de fontes de dados, entradas e saídas. Você também pode gerenciar os scripts e os dados separadamente para aumentar a produtividade.|
| **Modularidade** | Separar áreas de preocupações e isolar mudanças permite que o software evolua a uma velocidade mais rápida e com maior qualidade. | 
|**Colaboração**|Os pipelines permitem que os cientistas de dados colaborem em todas as áreas do processo de projeto de aprendizagem de máquina, ao mesmo tempo em que podem trabalhar simultaneamente em etapas de pipeline.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>Escolhendo a subclasse PipelineStep adequada

A `PythonScriptStep` é a subclasse mais `PipelineStep`flexível do resumo. Outras subclasses, `EstimatorStep` como `DataTransferStep` subclasses e podem realizar tarefas específicas com menos código. Por exemplo, `EstimatorStep` um pode ser criado apenas passando um `Estimator`nome para a etapa, um e um alvo de computação. Ou, você pode substituir entradas e saídas, parâmetros de pipeline e argumentos. Para obter mais informações, consulte [Modelos de Trem com O Azure Machine Learning usando o estimador](how-to-train-ml-models.md). 

O `DataTransferStep` facilita a movimentação de dados entre fontes de dados e sumidouros. O código para fazer essa transferência manualmente é simples, mas repetitivo. Em vez disso, `DataTransferStep` você pode apenas criar um com um nome, referências a uma fonte de dados e um dissipador de dados, e um alvo de computação. O notebook [Azure Machine Learning Pipeline com DataTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) demonstra essa flexibilidade.

## <a name="modules"></a>Módulos

Embora as etapas do pipeline permitam a reutilização dos resultados de uma execução anterior, em muitos casos a construção da etapa assume que os scripts e arquivos dependentes necessários devem estar disponíveis localmente. Se um cientista de dados quiser construir em cima do código existente, os scripts e dependências muitas vezes devem ser clonados de um repositório separado.

Os módulos são semelhantes em uso às etapas do pipeline, mas fornecem versões facilitadas através do espaço de trabalho, o que permite colaboração e reutilização em escala. Os módulos são projetados para serem reutilizados em vários pipelines e podem evoluir para adaptar uma computação específica a diferentes casos de uso. Os usuários podem fazer as seguintes tarefas através do espaço de trabalho, sem usar repositórios externos:

* Crie novos módulos e publique novas versões dos módulos existentes
* Depreciar versões existentes
* Versões de marca desativadas para impedir que os consumidores usem essa versão
* Designar versões padrão
* Recuperar módulos por versão do espaço de trabalho, para garantir que as equipes estejam usando o mesmo código

Consulte o [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) para obter exemplos de código sobre como criar, conectar e usar módulos em pipelines azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas

Os gasodutos Azure ML são uma instalação poderosa que começa a fornecer valor nos estágios iniciais de desenvolvimento. O valor aumenta à medida que a equipe e o projeto crescem. Este artigo explicou como os pipelines são especificados com o Azure Machine Learning Python SDK e orquestrados no Azure. Você viu alguns códigos-fonte básicos e `PipelineStep` foi introduzido a algumas das classes que estão disponíveis. Você deve ter uma noção de quando usar os gasodutos Azure ML e como o Azure os executa. 


+ Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md).

+ Aprenda a [executar previsões em lote em dados grandes](tutorial-pipeline-batch-scoring-classification.md ).

+ Consulte os dados de referência do SDK para as [etapas](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) [do núcleo do gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e do gasoduto .

+ Experimente o exemplo de notebooks Jupyter mostrando [os pipelines de Machine Learning do Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Aprenda a [executar notebooks para explorar este serviço.](samples-notebooks.md)
