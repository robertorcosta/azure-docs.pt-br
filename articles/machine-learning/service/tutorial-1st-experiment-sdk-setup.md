---
title: 'Tutorial: Criar seu primeiro experimento de ML: Configuração'
titleSuffix: Azure Machine Learning
description: Nesta série de tutoriais, você concluirá as etapas de ponta a ponta para começar a usar o SDK de Python do Azure Machine Learning em execução em notebooks Jupyter.  A parte um abrange a criação de um ambiente de servidor de notebook de nuvem, bem como a criação de um workspace para gerenciar seus experimentos e modelos de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 3bbda22689bb330acc836173162a64b840f1bbd8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828046"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Tutorial: Introdução à criação de seu primeiro experimento de ML com o SDK de Python

Neste tutorial, você concluirá as etapas de ponta a ponta para começar a usar o SDK de Python do Azure Machine Learning em execução em notebooks Jupyter. Este tutorial é **parte um de uma série de tutoriais de duas partes** e aborda a configuração e instalação do ambiente Python, bem como a criação de um workspace para gerenciar seus experimentos e modelos de machine learning. [**A parte dois**](tutorial-1st-experiment-sdk-train.md) se baseia nisso para treinar vários modelos de machine learning e apresentar o processo de gerenciamento de modelos usando o portal do Azure e o SDK.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar um [Workspace do Azure Machine Learning](concept-workspace.md) para usar no próximo tutorial.
> * Clonar o notebook de tutoriais em sua pasta no workspace.
> * Criar uma VM de Jupyter Notebook baseada em nuvem com o SDK do Python do Azure Machine Learning instalado e pré-configurado.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Criar um workspace

Um Workspace do Azure Machine Learning é o recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um workspace pelo portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu **workspace** e sua **assinatura**. Você precisará dessas informações para criar o experimento no local certo. 


## <a name="azure"></a>Clonar uma pasta do notebook

Este exemplo usa o servidor de notebook em nuvem em seu workspace para uma experiência pré-configurada e sem instalação. Use [seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controle sobre seu ambiente, pacotes e dependências.

Conclua a configuração do experimento a seguir e execute as etapas na página de aterrissagem do workspace (versão prévia), uma interface consolidada que inclui ferramentas de aprendizado de máquina para realizar cenários de ciência de dados para praticantes de todos os níveis de habilidade.

1. Entre na [página de aterrissagem do workspace](https://ml.azure.com/).

1. Selecione a assinatura e o workspace criado.

1. Selecione **Notebooks e Arquivos** à esquerda.

1. Abra a pasta **samples**.

1. Selecione o **"..."** à direita da pasta **tutorials** e, em seguida, selecione **Clonar**.

    ![Clonar pasta](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Há uma pasta que é exibida para cada usuário que acessa o workspace.  Selecione sua pasta para clonar a pasta **tutorial** lá.

## <a name="a-nameopenselect-a-vm-to-run-the-notebook"></a><a name="open">Selecionar uma VM para executar o notebook

1. Em **Arquivos do Usuário**, abra a pasta e, em seguida, abra a pasta **tutorials** clonada.

    ![Abrir a pasta tutorials](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Você pode exibir notebooks na pasta **samples**, mas não pode executar um notebook partindo dela.  Para executar um notebook, abra a versão clonada do notebook na seção **Arquivos do Usuário**.
    
1. Selecione o arquivo **tutorial-1st-experiment-sdk-train.ipynb** na pasta **tutorials**.

1. Na barra superior, selecione uma VM de notebook a ser usada para executar o notebook. Essas VMs são pré-configuradas com tudo o que você precisa para executar Azure Machine Learning. Você pode selecionar uma VM criada por qualquer usuário do seu workspace. 

1. Se nenhuma VM for encontrada, selecione **+ Nova VM** para criar a VM.

    ![Criar uma máquina virtual](media/tutorial-1st-experiment-sdk-setup/no-vm.png)

    1. Ao criar uma VM, forneça um nome.  O nome da VM deve ter entre 2 e 16 caracteres. Os caracteres válidos são letras, dígitos e o caractere -, sendo que esse nome deve ser exclusivo na assinatura do Azure.

    1. Em seguida, selecione **Criar**. A configuração da VM pode levar aproximadamente cinco minutos.

1. Depois que a VM estiver disponível, ela será exibida na barra de ferramentas superior.  Agora você pode executar o notebook usando **Executar tudo** na barra de ferramentas ou usando **Shift + Enter** nas células de código do notebook.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você concluiu estas tarefas:

* Criou um Workspace do Azure Machine Learning.
* Criou e configurou um servidor de notebook em nuvem no seu workspace.

Na **parte dois** do tutorial, você executa o código em `tutorial-1st-experiment-sdk-train.ipynb` para treinar um modelo de machine learning. 

> [!div class="nextstepaction"]
> [Tutorial: Treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se não planeja seguir a parte 2 deste tutorial ou nenhum outro tutorial, você deve [interromper a VM do servidor de notebook na nuvem](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quando não a estiver usando para reduzir o custo.


