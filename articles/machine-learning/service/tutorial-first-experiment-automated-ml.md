---
title: Criar seu primeiro experimento automatizado de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Aprenda a treinar e implantar um modelo de classificação com aprendizado de máquina automatizado na página de aterrissagem do workspace do Azure Machine Learning (versão prévia).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: 3ddd228488d8ba4adc6780db1f65fdb634291d3b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350510"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Tutorial: Crie seu primeiro modelo de classificação com o aprendizado de máquina automatizado

Neste tutorial, você aprenderá a criar seu primeiro experimento de aprendizado de máquina automatizado na página de aterrissagem do workspace (versão prévia) sem escrever nenhuma linha de código. Este exemplo cria um modelo de classificação para prever se um cliente assinará um depósito a prazo fixo com uma instituição financeira.

Com o aprendizado de máquina automatizado, você pode automatizar tarefas intensivas e demoradas. O aprendizado de máquina automatizado itera rapidamente em muitas combinações de algoritmos e hiperparâmetros para ajudar você a encontrar o melhor modelo com base em uma métrica de sucesso de sua escolha.

Neste tutorial, você aprenderá a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar um Workspace de Azure Machine Learning.
> * Executar um experimento de aprendizado de máquina automatizado.
> * Exibir detalhes do experimento.
> * Implantar o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Baixe o arquivo de dados [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). A coluna **y** indica se um cliente assinou um depósito a prazo fixo, que é posteriormente identificado como a coluna de destino para as previsões neste tutorial. 

## <a name="create-a-workspace"></a>Criar um workspace

Um Workspace do Azure Machine Learning é o recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um workspace pelo portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu **workspace** e sua **assinatura**. Você precisará dessas informações para criar o experimento no local certo. 

## <a name="create-and-run-the-experiment"></a>Criar e executar o experimento

Conclua a configuração do experimento a seguir e execute as etapas na página de aterrissagem do workspace, uma interface consolidada que inclui ferramentas de aprendizado de máquina para realizar cenários de ciência de dados para praticantes de todos os níveis de habilidade. A página de aterrissagem do workspace não é compatível com navegadores Internet Explorer.

1. Entre na [página de aterrissagem do workspace](https://ml.azure.com/workspaceportal/).

1. Selecione a assinatura e o workspace criado.

1. Selecione **Introdução**.

1. No painel esquerdo, selecione **ML Automatizado** na seção **Criar**.

   Como esse é seu primeiro experimento de ML automatizado, você verá a tela Introdução.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Selecione **Criar um experimento**. 

1. Insira este nome de experimento: `my-1st-automl-experiment`

1. Selecione **Criar uma computação** e configure o destino de computação. Um destino de computação é um ambiente de recursos local ou baseado em nuvem usado para executar o script de treinamento ou hospedar a implantação de serviço. Para este experimento, usamos uma computação baseada em nuvem. 

   Campo | DESCRIÇÃO | Valor para o tutorial
   ----|---|---
   Nome da computação |Um nome exclusivo que identifique o contexto de computação.|automl-compute
   Tamanho&nbsp;da&nbsp;máquina virtual| Selecione o tamanho da máquina virtual da computação.|Standard_DS12_V2
   Número mín./máx. de nós (em Configurações Avançadas)| Para analisar os dados, é necessário especificar um ou mais nós.|Número mín. de nós: 1<br>Número máx. de nós: 6

   >[!NOTE]
   >Para este tutorial, você usará a conta de armazenamento padrão e o contêiner criado com a nova computação. Eles são populados automaticamente no formulário.
    
1. Selecione **Criar** para obter o destino de computação. 

   **Isso levará alguns minutos para ser concluído.** 

1. Após a criação, selecione o novo destino de computação na lista suspensa e selecione **Avançar**.

1. Selecione **Carregar do arquivo local** para começar a criar um conjunto de dados. 

    1. Selecione **Procurar**.
    
    1. Escolha o arquivo **bankmarketing_train.csv** no computador local. Esse é o arquivo que você baixou como [pré-requisito](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Dê um nome exclusivo ao conjunto de dados e forneça uma descrição opcional. 

    1. Selecione **Avançar** no canto inferior esquerdo para fazer upload dele no contêiner padrão que foi automaticamente configurado durante a criação do workspace. A versão prévia pública só é compatível com carregamentos de arquivo local. 
    
       Após a conclusão do upload, o formulário Configurações e visualização será pré-populado com base no tipo de arquivo. 
       
    1. Verifique se o formulário **Configurações e visualização** é populado conforme mostrado a seguir e selecione **Avançar**.
        
        Campo|DESCRIÇÃO| Valor para o tutorial
        ---|---|---
        Formato de arquivo|Define o layout e o tipo de dados armazenados em um arquivo.| Delimitado
        Delimitador|Um ou mais caracteres para especificar o limite entre regiões&nbsp; separadas e independentes em texto sem formatação ou outros fluxos de dados. |Vírgula
        Codificação|Identifica qual tabela de esquema de bit para caractere usar para ler seu conjunto de dados.| UTF-8
        Cabeçalhos da coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.| Todos os arquivos têm os mesmos cabeçalhos
        Ignorar linhas | Indica quantas linhas, se houver, serão ignoradas no conjunto de registros.| Nenhum
    
        ![Configuração da guia Visualização](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Selecione **Classificação** como a tarefa de previsão.

1. Selecione **y** como a coluna de destino, o que você quer prever. Essa coluna indica se o cliente assinou um depósito a prazo ou não.

1. Expanda **Configurações Avançadas** e preencha os campos conforme demonstrado a seguir. Essas configurações destinam-se a controlar melhor o trabalho de treinamento. Caso contrário, os padrões são aplicados com base na seleção e nos dados de experimento.

   >[!NOTE]
   > Neste tutorial, você não definirá uma pontuação de métrica nem um limite máximo de núcleos por iterações. Você também não bloqueará o teste dos algoritmos.
   
   Configurações&nbsp;avançadas|DESCRIÇÃO|Valor&nbsp;para o&nbsp;tutorial
   ------|---------|---
   Métrica principal| Métrica de avaliação pela qual o algoritmo de aprendizado de máquina será medido.|AUC_weighted
   Critérios de saída| Se um critério for atendido, o trabalho de treinamento será interrompido. |Hora&nbsp;do&nbsp;trabalho de treinamento: 5 <br> <br> Número máx.&nbsp;#&nbsp;de&nbsp;iterações&#58;10
   Pré-processamento| Habilita o pré-processamento feito pelo aprendizado de máquina automatizado. Isso inclui limpeza, preparação e transformação automáticas de dados para gerar recursos sintéticos.| Habilitar
   Tipo de validação | Escolha um tipo de validação cruzada.|Validação cruzada K vezes
   Número de validações | Número de testes. | Duas validações cruzadas 
   Simultaneidade| O número máximo de iterações simultâneas.|5
   
1. Clique em **Iniciar** para executar o experimento. Uma tela é exibida com uma mensagem de status à medida que a preparação do experimento é iniciada.

>[!IMPORTANT]
> A preparação leva de **10 a 15 minutos** para preparar a execução do experimento. Durante a execução, são necessários **mais 2 a 3 minutos para cada iteração**.  
>
> Em produção, provavelmente, isso demorará mais. Mas para este tutorial, sugerimos que você comece explorando os resultados das iterações conforme elas são concluídas enquanto as outras ainda estão em execução. 

##  <a name="explore-iteration-results"></a>Explorar os resultados da iteração

À medida que o experimento progride, a tela atualiza o **gráfico de iteração** e a **lista de iteração** com as diferentes iterações (modelos) criadas conforme são concluídas. Por padrão, as iterações são ordenadas por pontuação de métrica. Por este tutorial, o modelo com a pontuação mais alta de acordo com a métrica **AUC_weighted** escolhida é exibida no início da lista.

Enquanto você aguarda a conclusão de todas as iterações do experimento, selecione o **Nome** de uma iteração concluída para explorar seus detalhes de desempenho. 

Veja a seguir os gráficos e as métricas de execução geradas para cada iteração, como uma curva de precisão/recall, matriz de confusão, pontuações de precisão ponderada etc. 

![Detalhes da iteração de execução](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Implantar o modelo

O machine learning automatizado na página de aterrissagem do workspace permite que você implante o melhor modelo como um serviço Web em algumas etapas. A implantação é a integração do modelo para que ele possa prever novos dados e identificar possíveis áreas de oportunidade. Para este experimento, a implantação em um serviço Web significa que a instituição financeira agora tem uma solução Web iterativa e escalonável para identificar clientes potenciais para depósito a prazo fixo. 

Quando a execução for concluída, volte à página de detalhes do **Gráfico de iterações** e da **Lista de iterações**. 

Nesse contexto de experimento, **VotingEnsemble** é considerado o melhor modelo, com base na métrica **AUC_weighted**.  Implantamos esse modelo, mas saiba que a implantação demora cerca de 20 minutos para ser concluída. O processo de implantação envolve várias etapas, incluindo o registro do modelo, a geração de recursos e a configuração deles para o serviço Web.

1. Selecione o botão **Implantar o Melhor Modelo** no canto superior direito.

1. Preencha o painel **Implantar o Melhor Modelo** da seguinte maneira:

    Campo| Valor
    ----|----
    Nome da implantação| my-automl-deploy
    Descrição da implantação| Minha primeira implantação de experimento de aprendizado de máquina automatizado
    Script de pontuação| Gerar automaticamente
    Script do ambiente| Gerar automaticamente
    
1. Selecione **Implantar**.  

    Uma mensagem de conclusão da implantação é exibida quando a implantação é concluída com êxito.
    
Agora você tem um serviço Web operacional para gerar previsões.

## <a name="clean-up-resources"></a>Limpar recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua somente os arquivos de implantação para minimizar os custos de sua conta ou se você quiser manter o workspace e os arquivos do experimento. Caso contrário, se você não planeja usar nenhum dos arquivos, exclua o grupo de recursos inteiro.  

### <a name="delete-the-deployment-instance"></a>Excluir a instância de implantação

Exclua apenas a instância de implantação do portal do Azure se você quiser manter o grupo de recursos e o workspace para outros tutoriais e explorações. 

1. Vá para o [Portal do Azure](https://portal.azure.com//). Navegue até o workspace e, no lado esquerdo, no painel **Ativos**, selecione **Implantações**. 

1. Selecione a implantação que você deseja excluir e selecione **Excluir**. 

1. Selecione **Continuar**.

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de aprendizado de máquina automatizado, você usou a página de aterrissagem do workspace para criar e implantar um modelo de classificação. Confira estes artigos para obter mais informações e ver as próximas etapas:

> [!div class="nextstepaction"]
> [Consumir um serviço Web](how-to-consume-web-service.md)

+ Saiba mais sobre o [pré-processamento](how-to-create-portal-experiments.md#preprocess).
+ Saiba mais sobre a [criação de perfil de dados](how-to-create-portal-experiments.md#profile).
+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).
+ Para saber mais sobre métricas e gráficos de classificação, confira o artigo [Noções básicas sobre os resultados de machine learning automatizado](how-to-understand-automated-ml.md#classification).

>[!NOTE]
> Esse conjunto de dados de marketing bancário é disponibilizado sob a [licença Creative Commons (CCO: Domínio Público)](https://creativecommons.org/publicdomain/zero/1.0/). Todos os direitos no conteúdo individual do banco de dados são licenciados sob a [Licença de Conteúdo do Banco de Dados](https://creativecommons.org/publicdomain/zero/1.0/) e estão disponíveis no [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Esse conjunto de dados estava originalmente disponível no [banco de dados de aprendizado de máquina da UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. Uma abordagem controlada por dados para prever o sucesso do telemarketing bancário. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho de 2014.
