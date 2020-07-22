---
title: 'Tutorial: Experimento em notebooks Jupyter (Python)'
titleSuffix: Machine Learning - Azure
description: Neste tutorial, você começará a usar o SDK de Python do Azure Machine Learning em execução em notebooks Jupyter.  Na parte 1, você cria um espaço de trabalho no qual gerenciará experimentos e modelos de ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: tracking-python
ms.openlocfilehash: 8ba6141bddd60c2bdc2fdf17845e8f470dd6ef55
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86520520"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Tutorial: Introdução à criação de seu primeiro experimento de ML com o SDK de Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você concluirá as etapas de ponta a ponta para começar a usar o SDK de Python do Azure Machine Learning em execução em notebooks Jupyter. Este tutorial é **parte um de uma série de tutoriais de duas partes** e aborda a configuração e instalação do ambiente Python, bem como a criação de um workspace para gerenciar seus experimentos e modelos de machine learning. [**A parte dois**](tutorial-1st-experiment-sdk-train.md) se baseia nisso para treinar vários modelos de machine learning e apresentar o processo de gerenciamento de modelos usando o Azure Machine Learning Studio e o SDK.

Neste tutorial, você:

> [!div class="checklist"]
> * Criar um [Workspace do Azure Machine Learning](concept-workspace.md) para usar no próximo tutorial.
> * Clonar o notebook de tutoriais em sua pasta no workspace.
> * Criar uma instância de computação baseada em nuvem com o SDK do Python do Azure Machine Learning instalado e pré-configurado.


Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Criar um workspace

Um Workspace do Azure Machine Learning é o recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um workspace pelo portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu **workspace** e sua **assinatura**. Você precisará dessas informações para criar o experimento no local certo. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Executar o notebook em seu workspace

Este tutorial usa o servidor de notebook em nuvem em seu workspace para uma experiência pré-configurada e sem instalação. Use [seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controle sobre seu ambiente, pacotes e dependências.

 Acompanhe o tutorial com este vídeo ou use as etapas detalhadas abaixo para clonar e executar o tutorial em seu workspace. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Clonar uma pasta do notebook

Conclua a configuração do experimento a seguir e execute as etapas no Azure Machine Learning Studio, uma interface consolidada que inclui ferramentas de aprendizado de máquina para realizar cenários de ciência de dados para praticantes de todos os níveis de habilidade.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).

1. Selecione a assinatura e o workspace criado.

1. Selecione **Notebooks** à esquerda.

1. Selecione a guia **Amostras** na parte superior.

1. Abra a pasta **Python**.

1. Abra a pasta com um número de versão.  Esse número representa a versão atual do SDK do Python.

1. Selecione o **"..."** à direita da pasta **tutorials** e, em seguida, selecione **Clonar**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Clonar a pasta tutoriais":::

1. Uma lista de pastas exibe cada usuário que acessa o espaço de trabalho.  Selecione sua pasta para clonar a pasta **tutoriais** lá.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Abrir o notebook clonado

1. Abra a pasta **tutoriais** que acabou de ser fechada na seção **Arquivos do usuário**.

    > [!IMPORTANT]
    > Você pode exibir notebooks na pasta **samples**, mas não pode executar um notebook partindo dela.  Para executar um notebook, abra a versão clonada do notebook na seção **Arquivos do Usuário**.
    
1. Selecione o arquivo **tutorial-1st-experiment-sdk-train.ipynb** na pasta **tutorials/create-first-ml-experiment**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Abrir a pasta tutoriais":::


1. Na barra superior, selecione uma instância de computação a ser usada para executar o notebook. Essas VMs são pré-configuradas com [tudo o que você precisa para executar Azure Machine Learning](concept-compute-instance.md#contents). 

1. Se nenhuma VM for encontrada, selecione **+ Adicionar** para criar a VM da instância de computação. 

    1. Ao criar uma VM, siga estas regras:  
        + O nome é obrigatório e não pode ficar vazio.
        + O nome precisa ser exclusivo (sem diferenciar maiúsculas de minúsculas) em todas as instâncias de computação existentes na região do Azure do workspace/da instância de computação. Você receberá um alerta se o nome escolhido não for exclusivo.
        + Os caracteres válidos são letras maiúsculas e minúsculas, números (0 a 9) e caractere de traço (-).
        + O nome precisa ter entre 3 e 24 caracteres.
        + O nome deve começar com uma letra (não um número nem um caractere de traço).
        + Se for usado o caractere de traço, ele precisará ser seguido por pelo menos uma letra após o traço. Exemplo: Teste-, Teste-0, Teste-01 são inválidos, enquanto que Teste-a0, Teste-0A são instâncias válidas.

    1.  Selecione o tamanho da máquina virtual nas opções disponíveis.

    1. Em seguida, selecione **Criar**. A configuração da VM pode levar aproximadamente cinco minutos.

1. Depois que a VM estiver disponível, ela será exibida na barra de ferramentas superior.  Agora você pode executar o notebook usando **Executar tudo** na barra de ferramentas ou usando **Shift + Enter** nas células de código do notebook.

Caso você tenha widgets personalizados ou prefira usar o Jupyter/o JupyterLab, selecione o menu suspenso do **Jupyter** na extrema direita e, em seguida, selecione **Jupyter** ou **JupyterLab**. A nova janela do navegador será aberta.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você concluiu estas tarefas:

* Criou um Workspace do Azure Machine Learning.
* Criou e configurou um servidor de notebook em nuvem no seu workspace.

Na **parte dois** do tutorial, você executa o código em `tutorial-1st-experiment-sdk-train.ipynb` para treinar um modelo de machine learning. 

> [!div class="nextstepaction"]
> [Tutorial: Treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se não planeja seguir a parte 2 deste tutorial ou nenhum outro tutorial, você deve [interromper a VM do servidor de notebook na nuvem](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quando não a estiver usando para reduzir o custo.


