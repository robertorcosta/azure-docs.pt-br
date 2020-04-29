---
title: Como um modelo se torna um serviço Web
titleSuffix: ML Studio (classic) - Azure
description: Uma visão geral da mecânica de como seu modelo de Azure Machine Learning Studio (clássico) progride de um experimento de desenvolvimento para um serviço Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217911"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Como um modelo de Machine Learning Studio (clássico) progride de um experimento para um serviço Web

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Azure Machine Learning Studio (clássico) fornece uma tela interativa que permite desenvolver, executar, testar e iterar um ***experimento*** que representa um modelo de análise preditiva. Há uma grande variedade de módulos disponíveis que podem:

* Inserir dados em seu teste
* Manipular os dados
* Treinar um modelo usando algoritmos de aprendizado de máquina
* Pontuar o modelo
* Avaliar os resultados
* Exibir os valores finais

Quando estiver satisfeito com seu experimento, você poderá implantá-lo como um ***serviço Web clássico Azure Machine Learning*** ou um ***novo Azure Machine Learning Web Service*** para que os usuários possam enviar novos dados e receber resultados.

Neste artigo, ofereceremos uma visão geral sobre a mecânica de como seu modelo de Machine Learning evolui de um experimento de desenvolvimento para um serviço Web operacional.

> [!NOTE]
> Há outras maneiras de desenvolver e implantar modelos de aprendizado de máquina, mas este artigo se concentra em como você usa Machine Learning Studio (clássico). Por exemplo, para ler uma descrição de como criar um serviço Web preditivo clássico com R, confira a postagem no blog [Build & Deploy Predictive Web Apps Using RStudio and Azure Machine Learning studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx) (Compilar e implantar aplicativos Web preditivos usando o RStudio e o Azure Machine Learning Studio).
>
>

Embora Azure Machine Learning Studio (clássico) seja projetado para ajudá-lo a desenvolver e implantar um *modelo de análise preditiva*, é possível usar o estúdio (clássico) para desenvolver um experimento que não inclua um modelo de análise preditiva. Por exemplo, um experimento pode simplesmente inserir dados, manipulá-los e gerar os resultados. Assim como um experimento de análise preditiva, você pode implantar esse experimento não preditiva como um serviço Web, mas é um processo mais simples porque o experimento não está treinando ou pontuando um modelo de aprendizado de máquina. Embora não seja o típico de usar o Studio (clássico) dessa forma, vamos incluí-lo na discussão para que possamos dar uma explicação completa de como o Studio (clássico) funciona.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolvendo e implantando um serviço Web preditivo
Aqui estão os estágios que uma solução típica segue à medida que você desenvolve e implanta usando Machine Learning Studio (clássico):

![Fluxo de implantação](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 - Estágios de um modelo típico de análise preditiva*

### <a name="the-training-experiment"></a>O teste de treinamento
O ***teste de treinamento*** é a fase inicial de desenvolvimento de seu serviço Web no Machine Learning Studio (clássico). A finalidade do teste de treinamento é fornecer a você um lugar para desenvolver, testar, iterar e, eventualmente, treinar um modelo de aprendizado de máquina. Você pode até mesmo treinar vários modelos simultaneamente à medida que procura a melhor solução, mas quando terminar de experimentar, você selecionará um único modelo treinado e eliminará o restante do experimento. Para obter um exemplo de desenvolvimento de um experimento de análise preditiva, consulte [desenvolver uma solução de análise preditiva para avaliação de risco de crédito em Azure Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>O teste preditivo
Depois de ter um modelo treinado em seu experimento de treinamento, clique em **configurar serviço Web** e selecione **serviço Web de previsão** no Machine Learning Studio (clássico) para iniciar o processo de conversão do teste de treinamento em um ***experimento de previsão***. A finalidade do experimento preditivo é usar o modelo treinado para pontuar novos dados, com o objetivo de se tornar posteriormente operacionalizado como um serviço Web do Azure.

Essa conversão é feita para você pelas seguintes etapas:

* Converter o conjunto de módulos usados para treinamento em um único módulo e salvá-lo como um modelo treinado
* Eliminar quaisquer módulos externos não relacionados à pontuação
* Adicionar portas de entrada e saída que serão usadas pelo possível serviço Web

Pode haver mais alterações que você queira fazer para ter seu experimento preditivo pronto para ser implantado como um serviço Web. Por exemplo, se você quiser que o serviço Web gere apenas um subconjunto dos resultados, você pode adicionar um módulo de filtragem antes da porta de saída.

O teste de treinamento não é descartado neste processo de conversão. Quando o processo for concluído, você terá duas guias no Studio (clássico): uma para o teste de treinamento e outra para o experimento de previsão. Dessa forma, você poderá alterar o teste de treinamento antes de implantar o serviço Web e recompilar o teste preditivo. Ou você pode salvar uma cópia do teste de treinamento para iniciar outra linha de teste.

> [!NOTE]
> Quando você clica em **Serviço Web Preditivo**, um processo automático é iniciado para converter seu teste de treinamento em um teste preditivo, e isso funciona bem na maioria dos casos. Se o seu teste de treinamento for complexo (por exemplo, se você tiver vários caminhos para treinamento reunidos), convém fazer essa conversão manualmente. Para obter mais informações, consulte [como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>O serviço Web
Quando estiver satisfeito com o experimento de previsão estar pronto, você poderá implantar seu serviço como um serviço Web clássico ou um novo serviço Web baseado em Azure Resource Manager. Para colocar em operação seu modelo implantando-o como um *Serviço Web clássico do Machine Learning*, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [clássico]**. Para implantar como *Novo serviço Web de Machine Learning*, clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [Novo]**. Os usuários podem agora enviar dados para seu modelo usando a API REST do serviço Web e receber os resultados de volta. Para saber mais, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>O caso não típico: criando um serviço Web não preditivo
Se seu experimento não treinar um modelo de análise preditiva, você não precisará criar um teste de treinamento e um experimento de Pontuação – há apenas um experimento e você pode implantá-lo como um serviço Web. Machine Learning Studio (clássico) detecta se o experimento contém um modelo de previsão analisando os módulos que você usou.

Depois de iterar em seu experimento e estar satisfeito com ele:

1. Clique em **Configurar Serviço Web** e selecione **Novo Treinamento de Serviço Web** – nós de entrada e saída são adicionados automaticamente
2. Clique em **executar**
3. Clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [clássico]** ou **Implantar Serviço Web [Novo]** dependendo do ambiente no qual você deseja implantar.

Seu serviço Web está implantado, e você pode acessá-lo e gerenciá-lo como um serviço Web preditivo.

## <a name="updating-your-web-service"></a>Atualização de seu serviço Web
Agora que você implantou seu experimento como um serviço Web, e se precisar atualizá-lo?

Depende do que você precisa atualizar:

**Para alterar a entrada ou saída, ou para modificar como o serviço Web manipula dados**

Se você não estiver alterando o modelo, mas estiver apenas alterando como o serviço Web lida com os dados, poderá editar o teste de previsão e clicar em **implantar serviço** Web e selecionar **implantar serviço Web [clássico]** ou **implantar serviço Web [novo]** novamente. O serviço Web é parado, o teste preditivo atualizado é implantado e o serviço Web é reiniciado.

Veja um exemplo: Suponha que seu experimento de previsão retorne toda a linha de dados de entrada com o resultado previsto. Talvez você queira que o serviço Web retorne apenas o resultado. Então adicione um módulo de **Colunas de Projeto** no experimento preditivo, logo antes da porta de saída, para excluir colunas que não sejam o resultado. Quando você clica em **Implantar Serviço Web** e seleciona **Implantar Serviço Web [Clássico]** ou **Implantar Serviço Web [Novo]** novamente, o serviço Web é atualizado.

**Você quer treinar novamente o modelo com novos dados**

Se você desejar manter a modelo de aprendizado de máquina, mas se quiser treiná-lo novamente com novos dados, terá duas opções:

1. **Treinar novamente o modelo enquanto o serviço Web está em execução** – se você quiser treinar novamente seu modelo enquanto o serviço Web preditivo estiver em execução, faça isso por meio de algumas modificações no teste de treinamento para transformá-lo em um ***teste de novo treinamento***, para poder implantá-lo como um ***serviço* Web de novo treinamento**. Para obter instruções sobre como fazer isso, veja [Treinar novamente os modelos do Machine Learning de forma programática](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Volte para o teste de treinamento original e use dados de treinamento diferentes para desenvolver seu modelo** – seu teste preditivo está vinculado ao serviço Web, mas o teste de treinamento não é diretamente vinculado dessa maneira. Se você modificar o teste de treinamento original e clicar em **Configurar Serviço Web**, será criado um *novo* teste preditivo que, quando implantado, criará um *novo* serviço Web. Ele não atualiza apenas o serviço Web original.

   Se você precisar modificar o teste de treinamento, abra-o e clique em **Salvar como** para fazer uma cópia. Isso deixará intacto o teste de treinamento original, o teste preditivo e o serviço Web. Agora você pode criar um novo serviço Web com suas alterações. Depois de implantar o novo serviço Web, você pode decidir se deseja parar o serviço Web anterior ou mantê-lo em execução junto com o novo.

**Você deseja treinar um modelo diferente**

Se você deseja fazer alterações em seu teste preditivo original, como selecionar um algoritmo de aprendizado de máquina diferente, tentar um método diferente de treinamento etc., siga o segundo procedimento descrito acima para treinar novamente seu modelo: abra o teste de treinamento, clique em **Salvar como** para fazer uma cópia e inicie o novo caminho de desenvolvimento de modelo, criando o teste preditivo e implantando o serviço Web. Isso criará um novo serviço Web não relacionado ao original – você pode decidir qual deles, ou ambos, continuará em execução.

## <a name="next-steps"></a>Próximas etapas
Para obter mais detalhes sobre o processo de desenvolvimento e teste, veja os seguintes artigos:

* convertendo o experimento- [como preparar seu modelo para implantação no Azure Machine Learning Studio (clássico)](convert-training-experiment-to-scoring-experiment.md)
* implantando o serviço Web – [Implantar um serviço Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* treinando novamente o modelo - [Treinar novamente os modelos do Machine Learning de forma programática](/azure/machine-learning/studio/retrain-machine-learning-model)

Para obter exemplos do processo inteiro, consulte:

* [Tutorial de aprendizado de máquina: criar seu primeiro experimento no Azure Machine Learning Studio (clássico)](create-experiment.md)
* [Walkthrough: desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning](tutorial-part1-credit-risk.md)

