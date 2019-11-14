---
title: Obter rótulos de dados
titleSuffix: Azure Machine Learning
description: Este artigo mostra como criar e executar projetos de rotulagem para marcar dados para o aprendizado de máquina.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 76f995901814c90ff9fd78585c98d56b3478e8b4
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612763"
---
# <a name="get-labels-for-data"></a>Obter rótulos de dados

Rotular grandes quantidades de dados costuma ser uma dor de cabeça nos projetos de aprendizado de máquina. Os projetos do ML com um componente da Pesquisa Visual Computacional, assim como classificação de imagem ou detecção de objetos, geralmente exige milhares de imagens e os rótulos correspondentes. 
 
O Azure Machine Learning Studio oferece uma localização central para criar, gerenciar e monitorar projetos de rotulagem. Os projetos de rotulagem ajudam a coordenar os dados, os rótulos e os membros da equipe, permitindo que você gerencie com mais eficiência as tarefas de rotulagem. Atualmente, as tarefas compatíveis são a classificação de imagem (seja ela de vários rótulos ou de várias classes) e a identificação de objetos usando caixas delimitadas.

O Azure rastreia o progresso e mantém a fila de tarefas de rotulagem incompletas. Rotuladores não precisam de uma conta do Azure para participar. Uma vez que tiverem autenticado com suas respectivas MSAs (contas Microsoft) ou com o [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), eles poderão trabalhar com rotulagem à vontade, de acordo com o tempo que tiverem disponível para isso. Eles podem atribuir e alterar rótulos usando atalhos de teclado. 

Você pode iniciar e parar o projeto, adicionar e remover pessoas e equipes e monitorar o progresso. Você pode exportar os dados rotulados em um formato COCO ou como um conjunto de dados do Azure Machine Learning. 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um projeto
> * Especificar os dados e a estrutura do projeto
> * Gerenciar as equipes e as pessoas trabalhando no projeto
> * Executar e monitorar o projeto
> * Exportar os rótulos 

## <a name="prerequisites"></a>Pré-requisitos

* Os dados que você deseja rotular, seja em arquivos locais ou já no Armazenamento do Azure
* O conjunto de rótulos que você deseja aplicar
* Instruções para rotulagem
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)
* Um Workspace do Azure Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Os projetos de rotulagem são administrados do [Azure Machine Learning Studio](https://ml.azure.com/). A página **Projetos de rotulagem** permite que você gerencie seus projetos, equipes e pessoas. Um projeto tem uma ou mais equipes atribuídas a ele e uma equipe tem uma ou mais pessoas atribuídas a ela. 

Se os dados já estiverem armazenados no Armazenamento de Blobs do Azure, você deverá disponibilizá-los como um armazenamento de dados antes de criar seu projeto de rotulagem. Para obter informações, confira [Criar e registrar armazenamentos de dados](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores). 

Para criar um projeto, escolha **Adicionar projeto**. Dê a ele um nome apropriado e selecione **Tipo de tarefa de rotulagem**. 

![Assistente de criação de projeto de rotulagem](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Escolha **Vários Rótulos de Classificação de Imagem** para projetos nos quais **um _ou mais_** rótulos de um conjunto de classes podem ser aplicados a uma imagem. Por exemplo, uma foto de um cachorro pode ser rotulada com *cachorro* e *dia*
* Escolha **Várias Classes de Classificação de Imagem** para projetos nos quais apenas uma **única classe** de um conjunto de classes pode ser aplicada a uma imagem
* Escolha **Identificação de objeto (caixa delimitadora)** para projetos nos quais a tarefa é atribuir uma classe a um objeto dentro de uma imagem e também especificar uma caixa delimitadora ao redor do objeto

Escolha **Avançar** quando estiver pronto para continuar.

## <a name="specify-data-to-be-labeled"></a>Especificar os dados a serem rotulados

Se já tiver criado um conjunto de dados que contenha seus dados, você poderá selecioná-lo no menu suspenso **Selecionar um conjunto de dados existente**. Ou escolha **Criar um conjunto de dados** para selecionar um armazenamento de dados do Azure existente ou para fazer upload de arquivos locais. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Criar um conjunto de dados de um armazenamento de dados do Azure

Embora escolher o upload direto de arquivos locais seja adequado para muitos casos de uso, o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é mais robusto e mais rápido para transferir quantidades significativas de dados. Recomendamos o Gerenciador de Armazenamento do Azure como o método padrão de mover arquivos.

Para criar um conjunto de dados com base em dados que você já armazenou no Armazenamento de Blobs do Azure:

1. Escolha **Criar um conjunto de dados** e **De armazenamento de dados**
1. Atribuir um **Nome** para seu conjunto de dados
1. Você deve escolher "Arquivo" como o **Tipo de conjunto de dados**  
1. Selecionar o armazenamento de dados 
1. Se os dados estiverem em uma subpasta no Armazenamento de Blobs, escolha **Procurar** para selecionar o caminho. 
    * Além disso, você pode acrescentar `/**` após o caminho para incluir todos os arquivos em subpastas do caminho selecionado
    * Use `**/*.*` para incluir todos os dados no contêiner e subpastas atuais
1. Forneça uma descrição para o conjunto de dados
1. Selecione **Avançar** 
1. Confirme os detalhes. Você pode escolher **Voltar** para modificar as configurações ou escolher **Criar** para criar o conjunto de dados

### <a name="create-a-dataset-by-uploading-data"></a>Criar um conjunto de dados carregando dados

Se você deseja fazer upload de seus dados diretamente:

1. Escolha **Criar um conjunto de dados** e **De arquivos locais**
1. Atribuir um **Nome** para seu conjunto de dados
1. Você deve escolher "Arquivo" como o **Tipo de conjunto de dados**
1. Se escolher **Configurações avançadas**, você poderá personalizar o armazenamento de dados, o contêiner e o caminho para os dados
1. Escolha **Procurar** para selecionar arquivos locais para upload
1. Forneça uma descrição para o conjunto de dados
1. Selecione **Avançar** 
1. Confirme os detalhes. Você pode escolher **Voltar** para modificar as configurações ou escolher **Criar** para criar o conjunto de dados

Os dados são carregados no repositório de blobs padrão (`workspaceblobstore`) do seu Workspace do Azure ML.

## <a name="specify-label-classes"></a>Especificar classes de rótulo

Na página **Classes de rótulo**, você especifica o conjunto de classes usadas para categorizar seus dados. Pense bem ao definir essas classes, pois a precisão e a velocidade dos rotuladores serão afetadas pela facilidade com que eles conseguirem escolher corretamente entre elas. Por exemplo, em vez de soletrar o gênero e a espécie completos de plantas ou animais, pode ser melhor usar códigos de campo ou abreviar o gênero. 

Insira um rótulo por linha, usando o botão **+** para adicionar uma nova linha. Se você tiver mais de três ou quatro rótulos, mas menos de dez, considere prefixá-los com "1:", "2:", etc. a fim de fornecer algumas diretrizes, permitindo que os rotuladores usem as teclas numéricas para acelerar seu próprio trabalho. 

## <a name="describe-the-labeling-task"></a>Descrever a tarefa de rotulagem

É importante explicar claramente a tarefa de rotulagem. A página **Instruções de rotulagem** permite que você vincule a um site externo para obter instruções a serem apresentadas aos rotuladores. Mantenha as instruções orientadas a tarefas e apropriadas para o público-alvo. 

* Quais são os rótulos que as pessoas verão e como escolherão entre eles? Há algum texto de referência que elas devem consultar?
* O que elas deverão fazer se nenhum rótulo parecer apropriado? 
* O que elas deverão fazer se vários rótulos parecerem apropriados?
* Qual limite de confiança elas devem aplicar a um rótulo? Você deseja que elas forneçam o "melhor palpite" se não tiverem certeza?
* O que elas deverão fazer com objetos de interesse parcialmente obstruídos ou sobrepostos?
* O que elas deverão fazer se um objeto de interesse for recortado pela borda da imagem?
* O que elas deverão fazer se, depois de enviarem uma imagem, acharem que cometeram um erro com ela? 

Com caixas delimitadoras, outras perguntas importantes incluem:

* Como a caixa delimitadora é definida para essa tarefa? Ela deve ser totalmente interior ao objeto, cortada o mais próximo possível de sua extensão ou então uma quantidade de espaço livre é aceitável? 
* Que nível de cuidado e de consistência você espera que o rotulador aplique para definir caixas delimitadoras?

>[!Note]
> Não deixe de dizer que o rotulador poderá escolher entre os primeiros 9 rótulos com as teclas numéricas de 1 a 9. 

## <a name="initialize-the-labeling-project"></a>Inicializar o projeto de rotulagem

Depois de inicializados, alguns aspectos do projeto de rotulagem são imutáveis: não é possível alterar o tipo de tarefa nem o conjunto de dados. Você pode modificar os rótulos e alterar a URL para a descrição da tarefa. Examine cuidadosamente as configurações antes de criar o projeto. Depois de enviar o projeto, você será levado de volta para a página inicial **Rotulagem**, que mostrará o projeto como **Inicializando**. Esta página não é atualizada automaticamente, portanto, se você atualizá-la manualmente após algum tempo, ela mostrará o projeto como **Criado**. 

## <a name="manage-teams-and-people"></a>Gerenciar equipes e pessoas

Um projeto de rotulagem obtém uma equipe padrão e adiciona você como um membro padrão. Cada projeto de rotulagem obtém uma nova equipe padrão, mas as equipes podem ser compartilhadas entre projetos. Os projetos podem ter mais de uma equipe. A criação de uma equipe é feita escolhendo **Adicionar equipe** na página **Equipes**. 

As pessoas são gerenciadas na página **Pessoas**. Você pode adicionar e remover pessoas com chave nos respectivos endereços de email. Cada rotulador usado por você terá que se autenticar usando a respectiva conta Microsoft ou do Azure Active Directory.  

Depois de adicionar uma pessoa, você pode atribuí-la a uma ou mais equipes. Navegue até a página **Equipes**, selecione a equipe em que você está interessado e, em seguida, use **Atribuir pessoas** ou **Remover pessoas** conforme desejado.

Sempre que desejar enviar um email para todos na equipe, você poderá fazer isso escolhendo a equipe, o que abrirá a página **Detalhes da equipe**. Nessa página, o botão **Email para a equipe** abrirá seu editor de email com os endereços de todos na equipe.

## <a name="run-and-monitor-the-project"></a>Executar e monitorar o projeto

Depois que o projeto for inicializado, o Azure começará a executá-lo. Se clicar no projeto na página de **rotulagem** principal, você será levado para os **Detalhes do projeto**. A guia **Painel** mostrará o progresso na tarefa de rotulagem. 

Na guia **Dados**, você pode examinar seu conjunto de dados e examinar os dados rotulados. Se você vir dados rotulados incorretamente, poderá **selecioná-los** e escolher **Rejeitar**, o que removerá os rótulos e colocará os dados de volta na fila sem rótulo. 

Na guia **Equipe**, você pode atribuir equipes a este projeto ou cancelar tais atribuições. 

Se você quiser colocar o projeto offline ou online, escolha o botão **Pausar**/**Iniciar**, que alterna o estado de execução do projeto.

Você pode rotular dados diretamente da página **Detalhes do projeto** selecionando **Rotular dados**. Você só pode rotular dados quando o projeto está em execução. 

## <a name="export-the-labels"></a>Exportar os rótulos

A qualquer momento, você pode exportar os dados de rótulo para experimentação em aprendizado de máquina. Os rótulos de imagem podem ser exportados no [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados do Azure ML. Você encontrará o botão **Exportar** na página **Detalhes do projeto** do seu projeto de rotulagem.

O arquivo COCO é criado no repositório de blobs padrão do Workspace do Azure ML em uma pasta dentro de **export/coco**. Você pode acessar o conjunto de dados do Azure ML exportado na seção **Conjuntos de dados** do Studio. A página de detalhes do conjunto de dados também fornece um código de exemplo para acessar seus rótulos por meio do Python.

![Conjunto de dados exportado](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Próximas etapas

* Rotular imagens para [classificação de imagens ou detecção de objetos](how-to-label-images.md)
* Saiba mais sobre o [Azure Machine Learning e Studio](../compare-azure-ml-to-studio-classic.md)