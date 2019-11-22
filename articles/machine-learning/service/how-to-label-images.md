---
title: Como usar a ferramenta de rotulagem do Azure Machine Learning
title.suffix: Azure Machine Learning
description: Este artigo ensina a usar as ferramentas de marcação de dados em um projeto de rotulagem do Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9cbebb905ba39dabaf905be2fa741702bd1a0088
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838953"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Como marcar imagens em um projeto de rotulagem

Após o administrador de projeto criar um projeto de rotulagem no Azure Machine Learning, você poderá usar a ferramenta de rotulagem para preparar rapidamente os dados para um projeto de aprendizado de máquina. 

> [!div class="checklist"]
> * Como acessar seus projetos de rotulagem
> * Ferramentas de rotulagem
> * Como usar as ferramentas para tarefas de rotulagem específicas

## <a name="prerequisites"></a>Pré-requisitos

* A URL do portal de rotulagem para um projeto de rotulagem de dados em execução
* Uma [conta Microsoft](https://account.microsoft.com/account) ou
* Uma conta do Azure Active Directory para a organização e o projeto

> [!Note]
> O administrador de projeto pode encontrar a URL do portal de rotulagem na guia **Detalhes** na página **Detalhes do projeto**. 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Fazendo logon no portal de rotulagem do projeto

Acesse a URL do portal de rotulagem fornecida a você pelo administrador de projeto. 

Faça logon usando a conta de email que o administrador usou para adicioná-lo à equipe. Para a maioria dos usuários, o logon será feito com sua conta Microsoft. Se o projeto de rotulagem usar o Azure Active Directory, você fará logon assim. 

## <a name="understanding-the-labeling-task"></a>Compreendendo a tarefa de rotulagem

Depois de entrar, você será levado para a página de visão geral do projeto. 

A primeira coisa a fazer é **Exibir instruções detalhadas**. Essas instruções são específicas de seu projeto e explicam o tipo de dados com que você está lidando, como você deve tomar suas decisões e outras informações relevantes. Quando terminar, volte para a página do projeto e escolha **Começar rotulagem**.

## <a name="common-features-of-the-labeling-task"></a>Recursos comuns da tarefa de rotulagem

Todas as tarefas de rotulagem de imagem envolvem a escolha das tags apropriadas em um conjunto especificado pelo administrador de projeto. Você pode selecionar entre as nove primeiras marcas usando as teclas de número do teclado.  

As tarefas de classificação de imagem permitem que você opte por apresentar várias imagens simultaneamente. Os layouts diferentes podem ser escolhidos usando os ícones acima da área da imagem. Você pode selecionar todas as imagens exibidas simultaneamente pressionando o botão **Selecionar tudo**. Selecione fotos individuais usando o botão de seleção circular no canto superior direito da área da imagem. Você precisa selecionar pelo menos uma imagem para aplicar uma tag. Se você tiver várias imagens selecionadas, selecionar uma tag aplicará essa tag a todas as fotos selecionadas.

Aqui, escolhemos um layout de 2 x 2 e estamos prestes a aplicar a tag "Mammal" às imagens do urso e da orca. A imagem com o tubarão já foi marcada como "Cartilaginous fish" e a iguana ainda não foi marcada.

![Layouts e seleção de várias imagens](media/how-to-label-images/layouts.png)

> [!Important] 
> Mude os layouts apenas quando você tiver uma página nova com os dados sem rótulo. Alternar entre layouts limpa o trabalho de marcação em andamento na página. 

O Azure habilitará o botão **Enviar** quando você tiver marcado todas as imagens na página. Pressione **Enviar** para salvar seu trabalho. 

Após você enviar marcas para os dados à disposição, o Azure atualizará a página com um novo conjunto de imagens da fila de trabalho.  

## <a name="tagging-images-for-multi-class-classification"></a>Marcação de imagens para classificação multiclasse

Se o seu projeto for do tipo "Classificação de imagem multiclasse", você atribuirá uma única tag à imagem inteira. A qualquer momento, escolha a página **Instruções** e navegue até **Exibir instruções detalhadas** para ver diretrizes específicas do projeto. 

Conforme discutido anteriormente, você pode selecionar entre vários layouts para apresentar imagens. Se, depois de selecionar uma imagem e atribuir uma tag a ela, você perceber que cometeu um erro, poderá corrigi-la. Se, no rótulo exibido abaixo da imagem, você clicar no `X` de destino, limpará a tag. Ou, se você selecionar a imagem e escolher outra classe, a tag passará para o valor recém-selecionado.

## <a name="tagging-images-for-multi-label-classification"></a>Marcação de imagens para classificação com vários rótulos

Se estiver trabalhando em um projeto do tipo "Classificação de imagem com vários rótulos", você aplicará uma _ou mais_ marcas a uma imagem. A qualquer momento, escolha a página **Instruções** e navegue até **Exibir instruções detalhadas** para ver diretrizes específicas do projeto. 

Selecione a imagem que deseja rotular e, em seguida, clique na tag. Escolher a tag a aplica a todas as imagens selecionadas e cancela sua seleção. Para aplicar mais marcas, você precisa selecionar as imagens novamente. Esta animação mostra uma página de marcação com vários rótulos:

* **Selecionar tudo** é usado para aplicar a tag "Ocean"
* Uma única imagem é selecionada e marcada com "Closeup"
* Três imagens são selecionadas e marcadas com "Wide angle"

![Animação mostrando o fluxo com vários rótulos](media/how-to-label-images/multilabel.gif)

Para corrigir um erro, você pode clicar no `X` para limpar marcas individuais ou pode selecionar as imagens e escolher a tag, o que limpará a tag de todas as imagens selecionadas. Esse cenário é mostrado aqui, com o clique em "Land" limpando essa tag das duas imagens selecionadas.

![Captura de tela mostrando várias seleções anuladas](media/how-to-label-images/multiple-deselection.png)

O Azure só habilitará o botão **Enviar** depois que você tiver aplicado pelo menos uma tag a cada imagem. Pressione **Enviar** para que seu trabalho seja salvo.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Marcação de imagens e caixas delimitadoras para detecção de objetos

Se o projeto for do tipo "Identificação de objeto (caixas delimitadas)", você especificará uma ou mais caixas delimitadores na imagem e aplicará uma tag a essa caixa. Cada imagem pode ter várias caixas delimitadoras, cada uma delas com uma única tag. Use **Exibir instruções detalhadas** para determinar se a adição de várias caixas delimitadoras é apropriada ao seu projeto.

1. Selecione uma tag para a caixa delimitadora que deseja criar
1. Selecione a ferramenta **Caixa retangular** ![Ferramenta caixa retangular](media/how-to-label-images/rectangular-box-tool.png) ou pressione “R” 
1. Clique e arraste diagonalmente em seu destino para criar uma caixa delimitadora aproximada
    * Ajuste a caixa delimitadora clicando nas bordas ou nos cantos da caixa e arrastando

![Captura de tela da criação de uma caixa delimitadora básica](media/how-to-label-images/bounding-box-sequence.png)

Se quiser excluir a caixa delimitadora, clique no destino em formato de X que aparece ao lado da caixa delimitadora após a criação.

Você não pode transferir a tag de uma caixa delimitadora existente. Se cometer um erro ao atribuir a tag, você precisará excluir a caixa delimitadora e criar uma nova com a tag correta.

Por padrão, caixas delimitadas existentes podem ser editadas. A ferramenta **Bloquear/desbloquear regiões** ![Ferramenta bloquear/desbloquear regiões](media/how-to-label-images/lock-bounding-boxes-tool.png), ou "L", ativa e desativa esse comportamento. Se as regiões estiverem bloqueadas, você só poderá alterar a forma ou a localização de uma nova caixa delimitadora.

Use a ferramenta de **Manipulação de regiões** ![Ferramenta de manipulação de regiões](media/how-to-label-images/regions-tool.png), ou "M", para ajustar uma caixa delimitadora existente. Você pode clicar nas bordas ou nos cantos e arrastar para ajustar a forma. Se clicar no interior, você poderá arrastar a caixa delimitadora inteira. Caso não seja possível editar uma região, talvez você tenha acionado a ferramenta **Bloquear/desbloquear regiões**. 

Use a ferramenta de **Caixa baseada em modelo** ![Ferramenta de caixa com modelo](media/how-to-label-images/template-box-tool.png), ou "T", para criar várias caixas delimitadoras do mesmo tamanho. Se a imagem não tiver caixas delimitadoras e você ativar as caixas baseadas em modelo, a ferramenta produzirá caixas de 50 x 50 pixels. Se você tiver criado uma caixa delimitadora e, em seguida, ativar caixas baseadas em modelo, as novas caixas delimitadas terão o tamanho da última que você criou. As caixas baseadas em modelo podem ser redimensionadas após o posicionamento. Redimensionar uma caixa baseada em modelo redimensiona apenas a caixa específica. 

Se quiser excluir _todas_ as caixas delimitadoras na imagem atual, você poderá escolher a ferramenta **Excluir todas as regiões** ![Ferramenta excluir regiões](media/how-to-label-images/delete-regions-tool.png). 

Após criar as caixas delimitadoras para a imagem, pressione **Enviar** para salvar seu trabalho. Seu trabalho em andamento não será salvo, a menos que você pressione o botão **Enviar**. 

## <a name="finishing-up"></a>Finalizando 

Quando você envia uma página de dados marcados, o Azure atribui a você novos dados sem rótulo de uma fila de trabalho. Se não houver mais dados sem rótulo, você verá uma mensagem informando isso, com um link de volta para a página inicial do portal. 

Se você souber que não trabalhará mais com rotulagem, escolha seu nome no canto superior direito do portal de rotulagem e escolha **Sair**. Se você não sair, o Azure "atingirá seu limite de tempo de execução" e atribuirá seus dados a outro rotulador. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [treinar modelos de classificação de imagens no Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Leia sobre a [detecção de objetos usando o Azure e a técnica Faster R-CNN](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)