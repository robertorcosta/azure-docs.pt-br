---
title: Transformar dados usando um fluxo de dados de mapeamento
description: Este tutorial fornece instruções passo-a-passo para usar o Azure Data Factory para transformar dados com o mapeamento do fluxo de dados
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979191"
---
# <a name="transform-data-using-mapping-data-flows"></a>Transformar dados usando fluxos de dados de mapeamento

Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você usará a interface de usuário (UX) do Azure Data Factory para criar um pipeline que copia e transforma dados de uma fonte ADLS (ADLS) De armazenamento no lago de dados do Azure para um dissipador ADLS Gen2 usando o fluxo de dados de mapeamento. O padrão de configuração neste tutorial pode ser expandido ao transformar dados usando o fluxo de dados de mapeamento

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Crie um pipeline com uma atividade de fluxo de dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Executar teste do pipeline.
> * Monitore uma atividade de fluxo de dados

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**. Você usa o armazenamento ADLS como *fonte* e *afunda* os armazenamentos de dados. Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) a fim de conhecer as etapas para criar uma.

O arquivo que estamos transformando neste tutorial é MoviesDB.csv, que pode ser encontrado [aqui](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Para recuperar o arquivo do GitHub, copie o conteúdo para um editor de texto de sua escolha para salvar localmente como um arquivo .csv. Para carregar o arquivo na sua conta de armazenamento, consulte [Upload blobs com o Portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos serão referenciados a um contêiner chamado 'dados de amostra'.

## <a name="create-a-data-factory"></a>Criar uma data factory

Nesta etapa, você cria uma fábrica de dados e abre o UX da Fábrica de Dados para criar um pipeline na fábrica de dados.

1. Abra o **Microsoft Edge** ou **Google Chrome**. Atualmente, a Interface do Usuário de Fábrica de Dados é suportada apenas nos navegadores web Microsoft Edge e Google Chrome.
2. No menu à esquerda, **selecione Criar uma** > fábrica de dados**de análise** > **de**recursos :

   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome da fábrica de dados Azure deve ser *globalmente único.* Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

     ![Novo data factory](./media/doc-common-process/name-not-available-error.png)
4. Selecione a **assinatura** do Azure na qual deseja criar o data factory.
5. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 
6. Em **Versão**, selecione **V2**.
7. Em **Local**, informe uma localização para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Azure Storage e SQL Database) e os cálculos (por exemplo, Azure HDInsight) usados pela fábrica de dados podem estar em outras regiões.
8. Selecione **Criar**.
9. Depois que a criação for concluída, você verá o aviso no centro de notificações. Selecione **Ir para o recurso** para navegar até a página do Data Factory.
10. Clique em **Criar e Monitorar** para iniciar a IU do Azure Data Factory em uma guia separada.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Crie um pipeline com uma atividade de fluxo de dados

Nesta etapa, você criará um pipeline que contém uma atividade de fluxo de dados.

1. Na página **Introdução**, selecione **Criar pipeline**.

   ![Criar um pipeline](./media/doc-common-process/get-started-page.png)

1. Na guia **Geral** para o pipeline, **digite TransformMovies** para **nome** do pipeline.
1. Na barra superior da fábrica, deslize o controle deslizante **de depuração** do fluxo de dados ligado. O modo Debug permite testes interativos da lógica de transformação contra um cluster Spark ao vivo. Os clusters de fluxo de dados levam de 5 a 7 minutos para aquecer e recomenda-se que os usuários ativem a depuração primeiro se planejam fazer o desenvolvimento do Fluxo de Dados. Para obter mais informações, consulte [Modo de depuração](concepts-data-flow-debug-mode.md).

    ![Atividade de fluxo de dados](media/tutorial-data-flow/dataflow1.png)
1. No painel **Atividades,** expanda o acordeom **Move and Transform.** Arraste e solte a atividade de fluxo de **dados** do painel para a tela do gasoduto.

    ![Atividade de fluxo de dados](media/tutorial-data-flow/activity1.png)
1. No **pop-up 'Adicionando fluxo de dados',** selecione **Criar novo fluxo de dados** e, em seguida, nomear seu fluxo de dados **TransformMovies**. Clique em Concluir quando tiver terminado.

    ![Atividade de fluxo de dados](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Crie lógica de transformação na tela de fluxo de dados

Depois de criar seu Fluxo de Dados, você será enviado automaticamente para a tela de fluxo de dados. Nesta etapa, você construirá um fluxo de dados que leva o moviesDB.csv no armazenamento ADLS e agrega a classificação média de comédias de 1910 a 2000. Em seguida, você escreverá este arquivo de volta para o armazenamento ADLS.

1. Na tela de fluxo de dados, adicione uma fonte clicando na caixa **Adicionar origem.**

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow2.png)
1. Nomeie seu **MoviesDB**de origem . Clique em **Novo** para criar um novo conjunto de dados de origem.

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow3.png)
1. Escolha **a Azure Data Lake Storage Gen2**. Clique em Continuar.

    ![Dataset](media/tutorial-data-flow/dataset1.png)
1. Escolha **DelimitedText**. Clique em Continuar.

    ![Dataset](media/tutorial-data-flow/dataset2.png)
1. Nomeie seu conjunto de dados **MoviesDB**. Na parada de serviço vinculado, escolha **Novo**.

    ![Dataset](media/tutorial-data-flow/dataset3.png)
1. Na tela de criação de serviços vinculados, nomeie seu serviço vinculado a ADLS gen2 **ADLSGen2** e especifique seu método de autenticação. Em seguida, digite suas credenciais de conexão. Neste tutorial, estamos usando a chave Conta para se conectar à nossa conta de armazenamento. Você pode clicar em Testar a **conexão** para verificar se suas credenciais foram inseridas corretamente. Clique em Criar quando terminar.

    ![Serviço vinculado](media/tutorial-data-flow/ls1.png)
1. Uma vez que você esteja de volta na tela de criação do conjunto de dados, digite onde seu arquivo está localizado no campo **Caminho arquivo.** Neste tutorial, o arquivo moviesDB.csv está localizado em dados de amostra de contêiner. Como o arquivo tem cabeçalhos, verifique **a primeira linha como cabeçalho**. Selecione **De conexão/armazenamento** para importar o esquema de cabeçalho diretamente do arquivo em armazenamento. Toque em OK quando terminar.

    ![Conjunto de dados](media/tutorial-data-flow/dataset4.png)
1. Se o cluster de depuração tiver sido iniciado, vá até a guia **Visualização** de dados da transformação de origem e clique **em Atualizar** para obter um instantâneo dos dados. Você pode usar a visualização de dados para verificar se sua transformação está configurada corretamente.

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow4.png)
1. Ao lado do nó de origem na tela de fluxo de dados, clique no ícone de adição para adicionar uma nova transformação. A primeira transformação que você está adicionando é um **Filtro**.

    ![Tela de fluxo de dados](media/tutorial-data-flow/dataflow5.png)
1. Nomeie seu filtro de transformação **FilterYears**. Clique na caixa de expressão ao lado **de Filtrar** para abrir o construtor de expressão. Aqui você especificará sua condição de filtragem.

    ![Filtrar](media/tutorial-data-flow/filter1.png)
1. O construtor de expressão de fluxo de dados permite que você construa expressões interativas para usar em várias transformações. As expressões podem incluir funções incorporadas, colunas do esquema de entrada e parâmetros definidos pelo usuário. Para obter mais informações sobre como construir expressões, consulte [Data Flow expression builder](concepts-data-flow-expression-builder.md).

    Neste tutorial, você quer filtrar filmes de comédia de gênero que saíram entre os anos de 1910 e 2000. Como o ano é atualmente uma seqüência, você ```toInteger()``` precisa convertê-lo em um inteiro usando a função. Use os operadores maiores ou iguais a (>=) e menos ou iguais a (<=) para comparar com os valores do ano literal 1910 e 200-. Suponha essas expressões junto com o operador e (&&). A expressão sai como:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Para descobrir quais filmes são comédias, você pode usar a função para encontrar o ```rlike()``` padrão 'Comédia' nos gêneros da coluna. Suinoe a expressão rlike com a comparação do ano para obter:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Se você tiver um cluster de depuração ativo, você pode verificar sua lógica clicando **em Atualizar** para ver a saída de expressão em comparação com as entradas usadas. Há mais de uma resposta certa sobre como você pode realizar essa lógica usando a linguagem de expressão de fluxo de dados.

    ![Filtrar](media/tutorial-data-flow/filter2.png)

    Clique **em Salvar e Terminar** assim que terminar sua expressão.

1. Busque uma **visualização de dados** para verificar se o filtro está funcionando corretamente.

    ![Filtrar](media/tutorial-data-flow/filter3.png)
1. A próxima transformação que você adicionará é **uma transformação agregada** **o modificador Schema**.

    ![Agregado](media/tutorial-data-flow/agg1.png)
1. Nomeie sua transformação **agregada AggregateComedyRatings**. No **Grupo por** guia, selecione **o ano** desde a queda até agrupar as agregações no ano em que o filme foi ao ar.

    ![Agregado](media/tutorial-data-flow/agg2.png)
1. Vá para a guia **Agregados.** Na caixa de texto à esquerda, nomeie a coluna **agregada AverageComedyRating**. Clique na caixa de expressão direita para inserir a expressão agregada através do construtor de expressão.

    ![Agregado](media/tutorial-data-flow/agg3.png)
1. Para obter a média da ```avg()``` classificação da **coluna,** use a função agregada. Como **rating** é ```avg()``` uma string e recebe uma entrada numérica, devemos converter ```toInteger()``` o valor para um número através da função. Esta é a expressão que parece:

    ```avg(toInteger(Rating))```

    Clique **em Salvar e Terminar** quando estiver pronto.

    ![Agregado](media/tutorial-data-flow/agg4.png)
1. Vá para a guia **Visualização de dados** para visualizar a saída de transformação. Observe que apenas duas colunas estão lá, **ano** e **AverageComedyRating**.

    ![Agregado](media/tutorial-data-flow/agg3.png)
1. Em seguida, você deseja adicionar uma transformação **sink** em **Destination**.

    ![Coletor](media/tutorial-data-flow/sink1.png)
1. Nomeie sua pia **Sink**. Clique **em Novo** para criar seu conjunto de dados sink.

    ![Coletor](media/tutorial-data-flow/sink2.png)
1. Escolha **a Azure Data Lake Storage Gen2**. Clique em Continuar.

    ![Dataset](media/tutorial-data-flow/dataset1.png)
1. Escolha **DelimitedText**. Clique em Continuar.

    ![Dataset](media/tutorial-data-flow/dataset2.png)
1. Nomeie seu conjunto de dados sink **MoviesSink**. Para serviço vinculado, escolha o serviço vinculado a ADLS gen2 que você criou na etapa 6. Digite uma pasta de saída para gravar seus dados. Neste tutorial, estamos escrevendo para a pasta 'saída' no contêiner 'dados de amostra'. A pasta não precisa existir de antemão e pode ser criada dinamicamente. Defina **a primeira linha como cabeçalho** como verdadeira e **selecione** **Nenhum** para importar esquema . Clique em Concluir.

    ![Coletor](media/tutorial-data-flow/sink3.png)

Agora você terminou de construir seu fluxo de dados. Você está pronto para executá-lo em seu oleoduto.

## <a name="running-and-monitoring-the-data-flow"></a>Execução e monitoramento do Fluxo de Dados

Você pode depurar um oleoduto antes de publicá-lo. Nesta etapa, você vai desencadear uma execução de depuração do pipeline de fluxo de dados. Enquanto a visualização de dados não escreve dados, uma execução de depuração gravará dados para o destino do sumidouro.

1. Vá para a tela do oleoduto. Clique **em Depurar** para ativar uma execução de depuração.

    ![Pipeline](media/tutorial-data-flow/pipeline1.png)
1. A depuração de pipeline das atividades de Fluxo de Dados usa o cluster de depuração ativa, mas ainda leva pelo menos um minuto para inicializar. Você pode acompanhar o progresso através da guia **Saída.** Uma vez que a execução seja bem sucedida, clique no ícone dos óculos para abrir o painel de monitoramento.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. No painel de monitoramento, você pode ver o número de linhas e tempo gasto em cada etapa de transformação.

    ![Monitoramento](media/tutorial-data-flow/pipeline3.png)
1. Clique em uma transformação para obter informações detalhadas sobre as colunas e particionamento dos dados.

    ![Monitoramento](media/tutorial-data-flow/pipeline4.png)

Se você seguiu este tutorial corretamente, você deve ter escrito 83 linhas e 2 colunas em sua pasta de dissipação. Você pode verificar se os dados estão corretos verificando seu armazenamento blob.

## <a name="next-steps"></a>Próximas etapas

O pipeline neste tutorial executa um fluxo de dados que agrega a classificação média de comédias de 1910 a 2000 e escreve os dados para a ADLS. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Crie um pipeline com uma atividade de fluxo de dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Executar teste do pipeline.
> * Monitore uma atividade de fluxo de dados

Saiba mais sobre a linguagem de expressão do [fluxo de dados](data-flow-expression-functions.md).
