---
title: Marcar imagens em um projeto de rotulagem
title.suffix: Azure Machine Learning
description: Saiba como usar as ferramentas de marcação de dados em um projeto de rotulagem do Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 1e27fca86613757c36ac664e2e449cabed68d550
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772441"
---
# <a name="tag-images-in-a-labeling-project"></a>Marcar imagens em um projeto de rotulagem

Depois que o administrador de projeto [criar um projeto de rotulagem](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) no Azure Machine Learning, você poderá usar a ferramenta de rotulagem para preparar rapidamente os dados para um projeto do Machine Learning. Este artigo descreve:

> [!div class="checklist"]
> * Como acessar seus projetos de rotulagem
> * As ferramentas de rotulagem
> * Como usar as ferramentas para tarefas de rotulagem específicas

## <a name="prerequisites"></a>Prerequisites

* A URL do portal de rotulagem para um projeto de rotulagem de dados em execução
* Uma [conta Microsoft](https://account.microsoft.com/account) ou uma conta do Azure Active Directory para a organização e o projeto

> [!NOTE]
> O administrador de projeto pode encontrar a URL do portal de rotulagem na guia **Detalhes** da página **Detalhes do projeto**.

## <a name="sign-in-to-the-projects-labeling-portal"></a>Entrar no portal de rotulagem do projeto

Acesse a URL do portal de rotulagem fornecida pelo administrador de projeto. Entre usando a conta de email que o administrador usou para adicionar você à equipe. Para a maioria dos usuários, essa será a sua conta Microsoft. Se o projeto de rotulagem usar o Azure Active Directory, você entrará conforme mostrado abaixo.

## <a name="understand-the-labeling-task"></a>Entender a tarefa de rotulagem

Depois de entrar, você verá a página de visão geral do projeto.

Acesse **Exibir instruções detalhadas**. Essas instruções são específicas do projeto. Elas explicam o tipo de dados com o qual você está lidando, como deverá tomar decisões e outras informações relevantes. Depois de ler essas informações, retorne à página do projeto e selecione **Iniciar rotulagem**.

## <a name="common-features-of-the-labeling-task"></a>Recursos comuns da tarefa de rotulagem

Em todas as tarefas de rotulagem de imagem, escolha tags apropriadas em um conjunto especificado pelo administrador de projeto. Selecione as nove primeiras marcas usando as teclas numéricas do teclado.  

Em tarefas de classificação de imagem, você pode optar por exibir várias imagens simultaneamente. Use os ícones acima da área da imagem para selecionar o layout. Para selecionar todas as imagens exibidas simultaneamente, use **Selecionar tudo**. Para selecionar imagens individuais, use o botão de seleção circular no canto superior direito da imagem. Você precisa selecionar pelo menos uma imagem para aplicar uma tag. Se você selecionar várias imagens, qualquer tag selecionada será aplicada a todas as imagens selecionadas.

Aqui, escolhemos um layout de 2 x 2 e estamos prestes a aplicar a tag "Mammal" às imagens do urso e da orca. A imagem do tubarão já foi marcada como "Cartilaginous fish", e a iguana ainda não foi marcada.

![Layouts e seleção de várias imagens](./media/how-to-label-images/layouts.png)

> [!Important] 
> Só alterne entre os layouts quando tiver uma página nova de dados sem rótulo. Alternar entre layouts limpa o trabalho de marcação em andamento na página.

O Azure habilitará o botão **Enviar** quando você tiver marcado todas as imagens na página. Selecione **Enviar** para salvar o trabalho.

Depois que você enviar marcas para os dados à disposição, o Azure atualizará a página com um novo conjunto de imagens da fila de trabalho.

## <a name="tag-images-for-multi-class-classification"></a>Marcar imagens para classificação multiclasse

Se o seu projeto for do tipo "Classificação de imagem multiclasse", você atribuirá uma única tag à imagem inteira. Para examinar as orientações a qualquer momento, acesse a página **Instruções** e selecione **Exibir instruções detalhadas**.

Se perceber que cometeu um erro depois de atribuir uma tag a uma imagem, você poderá corrigi-lo. Selecione o "**X**" no rótulo que é exibido abaixo da imagem para limpar a tag. Ou, então, selecione a imagem e escolha outra classe. O valor recém-selecionado substituirá a tag aplicada anteriormente.

## <a name="tag-images-for-multi-label-classification"></a>Marcar imagens para classificação de vários rótulos

Se estiver trabalhando em um projeto do tipo "Classificação de Imagem de Vários Rótulos", você aplicará uma *ou mais* marcas a uma imagem. Para ver as orientações específicas do projeto, selecione **Instruções** e acesse **Exibir instruções detalhadas**.

Selecione a imagem que deseja rotular e, em seguida, selecione a tag. A tag é aplicada a todas as imagens selecionadas e, em seguida, a seleção das imagens é anulada. Para aplicar mais marcas, você precisa selecionar as imagens novamente. A seguinte animação mostra a marcação de vários rótulos:

1. **Selecionar tudo** é usado para aplicar a tag "Ocean".
1. Uma única imagem é selecionada e marcada como "Closeup".
1. Três imagens são selecionadas e marcadas como "Wide angle".

![Animação mostra o fluxo de vários rótulos](./media/how-to-label-images/multilabel.gif)

Para corrigir um erro, clique no "**X**" para limpar uma tag individual ou selecione as imagens e, em seguida, selecione a tag, o que limpa a tag de todas as imagens selecionadas. Esse cenário é mostrado aqui. Um clique em "Land" limpará essa tag das duas imagens selecionadas.

![Uma captura de tela mostra várias seleções anuladas](./media/how-to-label-images/multiple-deselection.png)

O Azure só habilitará o botão **Enviar** depois que você tiver aplicado pelo menos uma tag a cada imagem. Selecione **Enviar** para salvar o trabalho.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Marcar imagens e especificar caixas delimitadoras para detecção de objetos

Se o projeto for do tipo "Identificação do Objeto (Caixas Delimitadoras)", você especificará uma ou mais caixas delimitadoras na imagem e aplicará uma tag a cada caixa. As imagens podem ter várias caixas delimitadoras, cada uma com uma única tag. Use **Exibir instruções detalhadas** para determinar se várias caixas delimitadas são usadas no projeto.

1. Selecione uma tag para a caixa delimitadora que pretende criar.
1. Selecione a ferramenta **Caixa retangular**![ferramenta Caixa retangular](./media/how-to-label-images/rectangular-box-tool.png) ou selecione "R".
3. Clique com o mouse e arraste uma seleção diagonalmente no alvo para criar uma caixa delimitadora aproximada. Para ajustar a caixa delimitadora, arraste as bordas ou os cantos.

![Uma captura de tela mostra a criação de uma caixa delimitadora básica.](./media/how-to-label-images/bounding-box-sequence.png)

Para excluir uma caixa delimitadora, clique no alvo em formato de X exibido ao lado da caixa delimitadora após a criação.

Não é possível alterar a tag de uma caixa delimitadora existente. Se você cometer um erro de atribuição de tag, precisará excluir a caixa delimitadora e criar outra com a tag correta.

Por padrão, você pode editar as caixas delimitadoras existentes. A ferramenta **Bloquear/desbloquear regiões**![Ferramenta bloquear/desbloquear regiões](./media/how-to-label-images/lock-bounding-boxes-tool.png), ou "L", ativa e desativa esse comportamento. Se as regiões estiverem bloqueadas, você só poderá alterar a forma ou a localização de uma nova caixa delimitadora.

Use a ferramenta de **Manipulação de regiões**![Ferramenta de manipulação de regiões](./media/how-to-label-images/regions-tool.png), ou "M", para ajustar uma caixa delimitadora existente. Arraste as bordas ou os cantos para ajustar a forma. Clique no interior para arrastar toda a caixa delimitadora. Caso não seja possível editar uma região, talvez você tenha acionado a ferramenta **Bloquear/desbloquear regiões**.

Use a ferramenta de **Caixa baseada em modelo**![Ferramenta de caixa com modelo](./media/how-to-label-images/template-box-tool.png), ou "T", para criar várias caixas delimitadoras do mesmo tamanho. Se a imagem não tiver caixas delimitadoras e você ativar caixas baseadas em modelo, a ferramenta produzirá caixas de 50 x 50 pixels. Se você criar uma caixa delimitadora e, em seguida, ativar caixas baseadas em modelo, as novas caixas delimitadoras terão o tamanho da última caixa criada. As caixas baseadas em modelo podem ser redimensionadas após o posicionamento. O redimensionamento de uma caixa baseada em modelo só redimensiona essa caixa específica.

Para excluir *todas* as caixas delimitadoras na imagem atual, selecione a ferramenta **Excluir todas as regiões**![ferramenta Excluir regiões](./media/how-to-label-images/delete-regions-tool.png).

Depois de criar as caixas delimitadores para uma imagem, selecione **Enviar** para salvar o trabalho, caso contrário, o trabalho em andamento não será salvo.

## <a name="finish-up"></a>Conclusão

Quando você envia uma página de dados marcados, o Azure atribui a você novos dados sem rótulo de uma fila de trabalho. Se não houver mais dados sem rótulo disponíveis, você receberá uma mensagem indicando isso junto com um link para a home page do portal.

Quando terminar a rotulagem, selecione seu nome no canto superior direito do portal de rotulagem e, em seguida, selecione **Sair**. Se você não sair, o Azure "atingirá seu limite de tempo de execução" e atribuirá seus dados a outro rotulador.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [treinar modelos de classificação de imagens no Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)
* Leia mais sobre a [detecção de objetos usando o Azure e a técnica "Faster R-CNN"](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
