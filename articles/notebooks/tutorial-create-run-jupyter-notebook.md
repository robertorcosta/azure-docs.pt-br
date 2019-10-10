---
title: Tutorial – Criar e executar um Jupyter Notebook no Azure
description: Como criar e executar um Jupyter Notebook no Azure Notebooks que demonstra o processo de regressão linear na ciência de dados.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: 827338c299b19d04245d7114a99d946d0332c82f
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973038"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Tutorial: criar e executar um Jupyter Notebook com Python

Este tutorial orienta você no processo de usar o Azure Notebooks para criar um Jupyter Notebook completo que demonstra uma regressão linear simples. No decorrer deste tutorial, você se familiarizará com a interface do usuário do Jupyter Notebook, que inclui a criação de células diferentes, a execução das células e a apresentação do notebook como uma apresentação de slides.

O notebook completo pode ser encontrado em [GitHub – Exemplos do Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Este tutorial, no entanto, começa com um novo projeto e um notebook vazio, para que você possa experimentar criá-lo passo a passo.

## <a name="create-the-project"></a>Criar o projeto

1. Vá até [Azure Notebooks](https://notebooks.azure.com) e entre. (Para obter mais detalhes, consulte [Início Rápido – Entrar no Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Em sua página de perfil público, selecione **Meus Projetos** na parte superior da página:

    ![Link Meus Projetos na parte superior da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **Meus Projetos**, selecione **+ Novo Projeto**(atalho de teclado: n); o botão poderá aparecer apenas como **+** , se a janela do navegador for estreita:

    ![Comando Novo Projeto na página Meus Projetos](media/quickstarts/new-project-command.png)

1. No pop-up **Criar Novo Projeto** exibido, insira ou defina os seguintes detalhes e selecione **Criar**:

    - **Nome do projeto**: Exemplo de regressão linear – Cricket Chirps
    - **ID do projeto**: linear-regression-example
    - **Projeto público**: (desmarcado)
    - **Criar um README.md**: (desmarcado)

1. Após alguns segundos, o Azure Notebooks o direcionará para o novo projeto.

## <a name="create-the-data-file"></a>Criar o arquivo de dados

O modelo de regressão linear criado no notebook usa dados de um arquivo em seu projeto chamado *cricket_chirps.csv*. É possível criar esse arquivo copiando-o de [GitHub – Exemplos do Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps) ou inserindo os dados diretamente. As seções a seguir descrevem as duas abordagens.

### <a name="upload-the-data-file"></a>Carregar o arquivo de dados

1. No painel do projeto no Azure Notebooks, selecione **Upload** > **Da URL**
1. No pop-up, insira a URL a seguir em **URL do arquivo** e *cricket_chirps.csv* em **Nome do Arquivo**; em seguida, selecione **Concluído**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. O arquivo *cricket_chirps.csv* agora deve aparecer na lista de arquivos do projeto:

    ![Arquivo CSV recém-criado aparecendo na lista de arquivos do projeto](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Criar um arquivo do zero

1. No painel do projeto no Azure Notebooks, selecione **+ Novo** > **Arquivo em Branco**
1. Um campo é exibido na lista de arquivos do projeto. Insira *cricket_chirps.csv* e pressione Enter.
1. Clique com botão direito do mouse em *cricket_chirps.csv* e selecione **Editar Arquivo**.
1. No editor que aparece, digite os seguintes dados:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Selecione **Salvar Arquivo** para salvar o arquivo e retornar ao painel do projeto.

## <a name="install-project-level-packages"></a>Instalar pacotes do nível do projeto

Dentro de um notebook, você sempre pode usar comandos como `!pip install` em uma célula de código para instalar os pacotes necessários. No entanto, esses comandos são executados sempre que você executa células de código do notebook e isso pode levar um tempo considerável. Por esse motivo, em vez disso, você pode instalar pacotes no nível do projeto usando um arquivo `requirements.txt`.

1. Use o processo descrito em [Criar um arquivo do zero](#create-a-file-from-scratch) para criar um arquivo chamado `requirements.txt` com o seguinte conteúdo:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Você também pode carregar um arquivo `requirements.txt` de seu computador local, se preferir, conforme descrito em [Carregar o arquivo de dados](#upload-the-data-file).

1. No painel do projeto, selecione **Configurações do Projeto**.
1. No popup que aparece, selecione a guia **Ambiente** e selecione **+Adicionar**.
1. No primeiro controle de lista suspensa (a operação) em **Etapas de Configuração do Ambiente**, escolha **Requirements.txt**.
1. No segundo controle de lista suspensa (o nome do arquivo), escolha *Requirements.txt* (o arquivo que você criou).
1. No terceiro controle de lista suspensa (a versão do Python), escolha **Python versão 3.6**.
1. Clique em **Salvar**.

![A guia Ambiente de Configurações do Projeto especificando um arquivo Requirements.txt](media/tutorial/tutorial-requirements-txt.png)

Com essa etapa de configuração resolvida, todos os notebooks executados no projeto serão executados em um ambiente onde esses pacotes estiverem instalados.

## <a name="create-and-run-a-notebook"></a>Criar e executar um notebook

Com o arquivo de dados pronto e o ambiente do projeto definido, agora você pode criar e abrir o notebook.

1. No painel do projeto, selecione **+ Novo** > **Notebook**.
1. Na janela pop-up, insira *Exemplo de Regressão Linear - Cricket Chirps.ipynb* como **Nome do ITEM**, escolha **Python 3.6** como a linguagem e selecione **Novo**.
1. Depois que o novo notebook aparecer na lista de arquivos, selecione-o para iniciá-lo. Uma nova guia do navegador é aberta automaticamente.
1. Como você tem um arquivo *Requirements.txt* nas configurações do ambiente, verá a mensagem “Aguardando o contêiner concluir a preparação”. Você pode selecionar **OK** para fechar a mensagem e continuar a trabalhar no notebook; no entanto, não é possível executar células de código até que o ambiente esteja totalmente configurado.
1. O notebook é aberto na interface do Jupyter com uma única célula de código vazia como o padrão.

    [![Exibição inicial de um novo notebook no Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Fazer um tour pela interface do notebook

Com o notebook em execução, você pode adicionar código e células de Markdown, executar essas células e gerenciar a operação do notebook. No entanto, primeiro vale a pena tirar alguns minutos para se familiarizar com a interface. Para obter uma documentação completa, selecione o comando de menu **Ajuda** > **Ajuda do Notebook ajuda**.

Na parte superior da janela, você verá os seguintes itens:

(A) O nome do notebook, que você pode editar clicando nele.
(B) Botões para navegar até o projeto e seu painel de projetos, que abre novas guias no navegador.
(C) Um menu com comandos para trabalhar com o notebook.
(D) Uma barra de ferramentas com atalhos para operações comuns.
(E) A tela de edição que contêm células.
(F) Indicador de confiabilidade do notebook (o padrão é **Não Confiável**).
(G) O kernel usado para executar o notebook, juntamente com um indicador de atividade.

[![Principais áreas de interface do usuário da interface do Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

O Jupyter fornece um tour interno pelos elementos da interface do usuário principal. Inicie o tour selecionando o comando **Ajuda** > **Tour de Interface do Usuário** e clicando nos pop-ups.

Os grupos de comandos de menu são os seguintes:

| Menu | DESCRIÇÃO |
| --- | --- |
| Arquivo | Comandos para gerenciar o arquivo do notebook, inclusive comandos para criar e copiar notebooks, mostram uma visualização de impressão e baixam o notebook em diferentes formatos. |
| Editar | Comandos comuns para recortar, copiar e colar células, localizar e substituir valores, gerenciar anexos de célula e inserir imagens.  |
| Visualizar | Comandos para controlar a visibilidade de partes diferentes da interface do usuário do Jupyter. |
| Inserir | Comandos para inserir uma nova célula acima ou abaixo da célula atual. Você usa esses comandos com frequência durante a criação de um notebook. |
| Célula | Os vários comandos **Executar** executam uma ou mais células em combinações diferentes. Os comandos **Tipo de Célula** alteram o tipo de uma célula entre **Código**, **Markdown** e **NBConvert Bruto** (texto sem formatação). Os comandos **Saídas Atuais** e **Todas as Saídas** controlam como a saída da execução do código é mostrada e inclui um comando para limpar todas as saídas. |
| Kernel | Comandos para gerenciar como o código está sendo executado no kernel, juntamente com **Alterar kernel** a fim de alterar a linguagem ou a versão do Python usada para executar o notebook. |
| Dados | Comandos para carregar e baixar arquivos do projeto ou da sessão. Consulte [Trabalhar com arquivos de dados do projeto](work-with-project-data-files.md) |
| Widgets | Comandos para gerenciar [Jupyter Widgets](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), que fornecem recursos adicionais de visualização, mapeamento e plotagem.|
| Ajuda | Comandos que fornecem ajuda e documentação para a interface do Jupyter. |

A maioria dos comandos na barra de ferramentas tem comandos de menu equivalentes. Uma exceção é **Inserir/Editar Apresentação de Slides RISE**, que é discutido em [Compartilhar e apresentar notebooks](present-jupyter-notebooks-slideshow.md).

Você usará vários desses comandos para preencher o notebook nas seções a seguir.

## <a name="create-a-markdown-cell"></a>Criar uma célula de Markdown

1. Clique na primeira célula vazia mostrada na tela do notebook. Por padrão, uma célula é do tipo **Código**, o que significa que ela foi criada para conter código executável para o kernel selecionado (Python, R ou F#). O tipo atual é mostrado no lista suspensa de tipos na barra de ferramentas:

    ![Lista suspensa da barra de ferramentas do tipo de célula](media/tutorial/tutorial-cell-type-drop-down.png)

1. Altere o tipo de célula para **Markdown** usando a lista suspensa da barra de ferramentas; como alternativa, use o menu de comando **Célula** > **Tipo de Célula**  >   **Markdown**:

    ![Comando de menu Tipo de célula](media/tutorial/tutorial-cell-type-menu.png)

1. Clique na célula para começar a editar e insira o seguinte Markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Para renderizar o Markdown em HTML para o navegador, selecione o comando **Executar** na barra de ferramentas ou use o comando **Célula** > **Executar Células**. O código de Markdown para formatação e links agora é exibido da forma esperada em um navegador.

1. Quando você executa a última célula no notebook, o Jupyter cria automaticamente uma nova célula abaixo da que você executou. Coloque mais Markdown nessa célula repetindo as etapas desta seção com o seguinte Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Para editar o Markdown novamente, clique duas vezes na célula renderizada. Para renderizar o HTML novamente depois de fazer alterações, execute a célula.

## <a name="create-a-code-cell-with-commands"></a>Criar uma célula de código com comandos

Conforme explicado na célula de Markdown anterior, você pode incluir comandos diretamente no notebook. Você pode usar comandos para instalar pacotes, executar curl ou wget para recuperar dados, ou qualquer outra coisa. Os notebooks Jupyter são executados efetivamente em uma máquina virtual Linux, para que você tenha o comando completo do Linux configurado à disposição.

1. Digite os comandos abaixo na célula de código que apareceu depois que você usou **Executar** na célula de Markdown anterior. Se você não vir uma nova célula, crie uma com **Inserir** > **Inserir Célula Abaixo** ou use o botão **+** na barra de ferramentas.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Antes de executar a célula, crie uma nova célula com o botão **+** na barra de ferramentas, defina-a como Markdown e insira a explicação abaixo:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selecione o comando **Célula** > **Executar Tudo** para executar todas as células no notebook. Observe que as células de Markdown renderizam como HTML e o comando é executado no kernel, e observe o indicador de kernel conforme descrito no próprio Markdown:

    ![Indicador de ocupado para o kernel do notebook](media/tutorial/tutorial-kernel-busy.png)

1. Também leva algum tempo para que todos os comandos `pip install` sejam executados e, como você já instalou esses pacotes no ambiente de projeto (e porque eles também estão incluídos no Azure Notebooks por padrão), verá muitas mensagens que indicam que o “requisito já foi atendido”. Toda essa saída pode ser visualmente confusa, portanto, selecione a célula (usando um único clique) e use **Célula** > **Saídas de Célula** > **Ativar/desativar** para ocultar a saída. Você também pode usar o comando **Limpar** desse mesmo submenu para remover a saída totalmente.

    O comando **Ativar/desativar** oculta apenas a saída mais recente da célula; se você executar a célula novamente, a saída será exibida novamente.

1. Como os pacotes estão instalados no ambiente do projeto, transforme os comandos `! pip install` em comentário usando `#`; dessa maneira, eles podem permanecer no notebook como material de ensino, mas não usarão tempo de execução e não produzirão saídas desnecessárias. Nesse caso, manter os comandos comentados no notebook também indica as dependências do notebook.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Criar as células restantes

Para preencher o restante do notebook, crie uma série de células de Markdown e de código. Para cada célula listada abaixo, primeiro crie a nova célula, defina o tipo e cole o conteúdo.

Embora você possa esperar para executar o notebook depois de criar cada célula, é interessante executar cada célula depois de criada. Nem todas as células mostram a saída. Se você não vir erros, suponha que a célula foi executada normalmente.

Cada célula de código depende do código que foi executado em células anteriores e, se você não executar uma das células, células posteriores poderão produzir erros. Se você acha que esqueceu a execução de uma célula, tente usar **Célula** > **Executar Todos Acima** antes de executar a célula atual.

Se você vir resultados inesperados (e provavelmente vai!), verifique se cada célula está definida como “Código” ou “Markdown”, conforme necessário. Por exemplo, um erro de “Sintaxe inválida” normalmente ocorre quando você insere Markdown em uma Célula de código.

1. Célula de markdown:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Célula de código; quando executada, mostra o conteúdo da tabela como saída. Você pode suprimir a saída transformando a instrução `print` em comentário.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Você pode ver avisos desse código sobre “tamanho de numpy.dtype alterado”; os avisos podem ser ignorados com segurança.

1. Célula de Markdown:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Célula de código; quando executada, não tem saída.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Célula de Markdown:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Célula de código; quando executada, mostra a saída, `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Célula de Markdown:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Célula de código; quando executada, mostra resultados como `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Célula de Markdown:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Célula de Markdown:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Célula de código; quando executada, produz uma plotagem gráfica. Se você não vir o tempo de plotagem da primeira vez (e, em vez disso, vir “Tamanho de figura 640 x 480 com 1 Eixo”), execute a célula novamente.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Saída da plotagem do código de matplotlib](media/tutorial/tutorial-plot-output.png)

1. Célula de Markdown:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Limpar as saídas e executar novamente todas as células

Depois de seguir as etapas na seção anterior para preencher todo o notebook, você criou uma parte de código em execução no contexto de um tutorial completo sobre regressão linear. Essa combinação direta de código e texto é uma das grandes vantagens dos notebooks!

Agora tente executar novamente o notebook inteiro:

1. Limpe todos os dados de sessão do kernel e toda a saída das células selecionando **Kernel** > **Reiniciar e Limpar Saída**. É sempre bom executar esse comando quando você conclui um notebook, apenas para ter certeza de que não criou dependências estranhas entre células de código.

1. Execute o notebook novamente usando **Célula** > **Executar Tudo**. Observe que o indicador de kernel é preenchido durante a execução do código.

    Se tiver um código que é executado por muito tempo ou que fica travado, você poderá parar o kernel usando o comando **Kernel** > **Interromper**.

1. Percorra o notebook para examinar os resultados. (Se o gráfico não aparecer novamente, execute a célula mais uma vez.)

## <a name="save-halt-and-close-the-notebook"></a>Salvar, parar e fechar o notebook

Enquanto estiver editando um notebook, você pode salvar seu estado atual com o comando **Arquivo** > **Salvar e Criar ponto de verificação** ou com o botão Salvar na barra de ferramentas. Um “ponto de verificação” cria um instantâneo para o qual você poderá reverter a qualquer momento durante a sessão. Pontos de verificação permitem que você faça uma série de alterações experimentais e, se essas alterações não funcionarem, basta reverter para um ponto de verificação usando o comando **Arquivo** > **Reverter para o Ponto de Verificação**. Uma abordagem alternativa é criar células adicionais e transformar em comentário qualquer código que não será executado; as duas formas funcionam.

Você também pode usar o comando **Arquivo** > **Fazer uma Cópia** a qualquer momento para fazer uma cópia do estado atual do notebook em um novo arquivo no projeto. Essa cópia é aberta automaticamente em uma nova guia do navegador.

Quando tiver terminado de usar um notebook, use o comando **Arquivo** > **Fechar e Parar**, que fecha o notebook e desliga o kernel que estava executando-o. O Azure Notebooks fecha a guia do navegador automaticamente.

## <a name="debug-notebooks-using-visual-studio-code"></a>Depurar notebooks usando o Visual Studio Code

Se as células de código no notebook não se comportarem da maneira esperada, você poderá ter bugs no código ou outros problemas. No entanto, além de usar instruções `print` para mostrar o valor das variáveis, um ambiente Jupyter típico não oferece nenhum facilitador de depuração.

Felizmente, você pode baixar o arquivo *.ipynb* do notebook e, em seguida, abri-lo no Visual Studio Code usando a extensão do Python. A extensão importa diretamente um notebook como um arquivo de código único, preservando as células de Markdown nos comentários. Depois de importar o notebook, você pode usar o depurador do Visual Studio Code para depurar o código, definir pontos de interrupção, examinar o estado e assim por diante. Depois de fazer correções em seu código, você exporta o arquivo *.ipynb* do Visual Studio Code e faz o upload novamente no Azure Notebooks.

Para obter mais informações, confira [Depurar um Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) na documentação do Visual Studio Code.

Confira também [Visual Studio Code – suporte do Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) para recursos adicionais do Visual Studio Code para notebooks Jupyter.

## <a name="next-steps"></a>Próximas etapas

- [Explorar notebooks de exemplo](azure-notebooks-samples.md)

Artigos de instrução:

- [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
- [Configurar e gerenciar dispositivos](configure-manage-azure-notebooks-projects.md)
- [Instalar pacotes em um notebook](install-packages-jupyter-notebook.md)
- [Exibir uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
- [Trabalhar com arquivos de dados](work-with-project-data-files.md)
- [Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Usar os Serviços do Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
