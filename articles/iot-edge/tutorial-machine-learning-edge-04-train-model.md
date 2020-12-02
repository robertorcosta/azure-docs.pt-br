---
title: 'Tutorial: Treinar e implantar um modelo – Machine Learning no Azure IoT Edge'
description: Neste tutorial, você treinará um modelo de machine learning usando o Azure Machine Learning e, em seguida, empacotará o modelo como uma imagem de contêiner que poderá ser implantada como um módulo do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9a29e890553d15d19853ec836f8c4296869e143
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959621"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Treinar e implantar um modelo do Azure Machine Learning

Neste artigo, executaremos as seguintes tarefas:

* Usar o Azure Notebooks para treinar um modelo de machine learning.
* Empacotar o modelo treinado como uma imagem de contêiner.
* Implantar a imagem de contêiner como um módulo do Azure IoT Edge.

O Azure Notebooks tira proveito de um workspace do Azure Machine Learning, um bloco básico usado para fazer experimentos, treinar e implantar modelos de machine learning.

As etapas neste artigo normalmente são executadas por cientistas de dados.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Cada artigo da série se baseia no trabalho do artigo anterior. Se você chegou a este artigo diretamente, acesse o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="set-up-azure-notebooks"></a>Configurar o Azure Notebooks

Usamos o Azure Notebooks para hospedar os dois Jupyter Notebooks e os arquivos de suporte. Aqui, criamos e configuramos um projeto do Azure Notebooks. Se você ainda não tiver usado o Jupyter e/ou o Azure Notebooks, veja alguns documentos de introdução:

* **Início Rápido:** [Criar e compartilhar um notebook](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutorial:** [criar e executar um Jupyter Notebook com Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

O uso do Azure Notebooks garante um ambiente uniforme para o exercício.

> [!NOTE]
> Após configurado, o serviço do Azure Notebooks pode ser acessado de qualquer computador. Durante a configuração, você deve usar a VM de desenvolvimento, que tem todos os arquivos que serão necessários.

### <a name="create-an-azure-notebooks-account"></a>Criar uma conta do Azure Notebooks

Para usar o Azure Notebooks, você precisará criar uma conta. Contas do Azure Notebooks são independentes das assinaturas do Azure.

1. Acesse o [Azure Notebooks](https://notebooks.azure.com).

1. Clique em **Entrar** no canto superior direito da página.

1. Entre usando sua conta corporativa ou de estudante (Azure Active Directory) ou sua conta pessoal (conta Microsoft).

1. Se nunca tiver usado o Azure Notebooks, você precisará permitir acesso ao aplicativo do Azure Notebooks.

1. Crie uma ID de usuário para o Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Fazer upload de arquivos do Jupyter Notebook

Vamos carregar arquivos de notebook de exemplo em um novo projeto do Azure Notebooks.

1. Na página de usuário da sua nova conta, selecione **Meus Projetos** na barra de menus superior.

1. Adicione um novo projeto selecionando o botão **+** .

1. Na caixa de diálogo **Criar Projeto**, forneça um **Nome do Projeto**. 

1. Deixe as opções **Público** e **README** desmarcadas, pois não é necessário que o projeto seja público ou tenha um arquivo leiame.

1. Selecione **Criar**.

1. Selecione **Carregar** (o ícone de seta para cima) e escolha **Do Computador**.

1. Selecione **Escolher arquivos**.

1. Navegue até **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os arquivos na lista e clique em **Abrir**.

1. Marque a caixa **Confiar no conteúdo desses arquivos**.

1. Selecione **Upload** para começar a carregar e, em seguida, selecione **Concluído** quando o processo for concluído.

### <a name="azure-notebook-files"></a>Arquivos do Azure Notebook

Vamos examinar os arquivos que você carregou em seu projeto do Azure Notebooks. As atividades nesta parte do tutorial se estendem por dois arquivos de notebook, que usam alguns arquivos de suporte.

* **01-turbofan\_regression.ipynb:** Este notebook usa o workspace do serviço do Machine Learning para criar e executar um experimento de aprendizado de máquina. Amplamente, o notebook executa as seguintes etapas:

  1. Baixa os dados da conta de Armazenamento do Azure que foi gerada pelo agente do dispositivo.
  1. Explora e prepara os dados e, em seguida, usa os dados para treinar o modelo de classificador.
  1. Avalia o modelo do experimento usando um conjunto de dados de teste (Test\_FD003.txt).
  1. Publica o melhor modelo do classificador no workspace do serviço do Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** Este notebook usa o modelo criado no notebook anterior e o utiliza para criar uma imagem de contêiner pronta para ser implantada em um dispositivo do Azure IoT Edge. O notebook realiza as seguintes etapas:

  1. Cria um script de pontuação para o modelo.
  1. Produz uma imagem de contêiner usando o modelo de classificador que foi salvo no workspace do serviço do Machine Learning.
  1. Implanta a imagem como um serviço Web na Instância de Contêiner do Azure.
  1. Usar o serviço Web para validar se o modelo e a imagem funcionam conforme o esperado. A imagem validada será implantada em nosso dispositivo do IoT Edge na parte [Criar e implantar módulos personalizados do IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) deste tutorial.

* **Test\_FD003.txt:** este arquivo contém os dados que usaremos como nosso conjunto de teste para validar nosso classificador treinado. Optamos por usar os dados de teste, conforme fornecidos para o concurso original, como nosso conjunto de teste para manter a simplicidade.

* **RUL\_FD003.txt:** este arquivo contém a RUL (Vida Útil Restante) do último ciclo de cada dispositivo no arquivo Test\_FD003.txt. Confira os arquivos readme.txt e Damage Propagation Modeling.pdf em C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan para ver uma explicação detalhada dos dados.

* **Utils.py:** contém um conjunto de funções de utilitário de Python para trabalhar com os dados. O primeiro notebook contém uma explicação detalhada das funções.

* **README.md:** arquivo Leiame que descreve o uso de notebooks.  

## <a name="run-azure-notebooks"></a>Executar Azure Notebooks

Agora que o projeto foi criado, você pode executar os notebooks. 

1. Na página do projeto, selecione **01-turbofan\_regression.ipynb**.

    ![Selecione o primeiro notebook a ser executado](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Se o notebook estiver listado como **Não Confiável**, clique no widget **Não Confiável** no canto superior direito do notebook. Quando a caixa de diálogo for exibida, selecione **Confiar**.

1. Para obter os melhores resultados, leia a documentação de cada célula e a execute individualmente. Selecione **Executar** na barra de ferramentas. Mais adiante, você será mais fácil executar várias células. Você pode desconsiderar os avisos de atualização e preterimento.

    Quando uma célula está em execução, ela exibe um asterisco entre colchetes ([\*]). Quando a operação da célula for concluída, o asterisco será substituído por um número e a saída relevante poderá aparecer. As células em um notebook são criadas em sequência e apenas uma pode ser executada de cada vez.

    Você também pode usar as opções de execução no menu **Célula**, `Ctrl` + `Enter` para executar uma célula e `Shift` + `Enter` para executar uma célula e avançar para a próxima célula.

    > [!TIP]
    > Para operações de célula consistentes, evite executar o mesmo notebook em várias guias no navegador.

1. Na célula após as instruções **Definir propriedades globais**, digite os valores para a assinatura, as configurações e os recursos do Azure. Em seguida, execute a célula.

    ![Definir propriedades globais no notebook](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Na célula anterior a **Detalhes do workspace**, depois que ela tiver sido executada, procure o link que orienta você a entrar para se autenticar:

    ![Prompt de entrada para autenticação de dispositivo](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Abra o link e insira o código especificado. Esse procedimento de entrada autentica o notebook Jupyter para acessar os recursos do Azure usando a interface de linha de comando multiplataforma do Microsoft Azure.  

    ![Autenticar aplicativo na confirmação do dispositivo](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Na célula que precede **Explorar os resultados**, copie o valor da ID de execução e cole-a para a ID de execução na célula após **Reconstituir uma execução**.

   ![Copiar a ID de execução entre células](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Execute as células restantes no notebook.

1. Salve o notebook e retorne à sua página do projeto.

1. Abra **02-turbofan\_deploy\_model.ipynb** e execute cada célula. Você precisará entrar para autenticar-se na célula após **Configurar workspace**.

1. Salve o notebook e retorne à sua página do projeto.

### <a name="verify-success"></a>Verificar êxito

Para verificar se os notebooks foram concluídos com êxito, verifique se alguns itens foram criados.

1. Na página do projeto do Azure Notebooks, selecione **Mostrar itens ocultos** para que os nomes de item que começam com um ponto apareçam.

1. Verifique se os seguintes arquivos foram criados:

    | Arquivo | Descrição |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Arquivo de configuração usado para criar o Workspace do Azure Machine Learning. |
    | ./aml_config/model_config.json | Arquivo de configuração que precisaremos implantar o modelo no workspace do Machine Learning **turbofanDemo** no Azure. |
    | myenv.yml| Fornece informações sobre as dependências para o modelo de Machine Learning implantado.|

1. Verifique se os seguintes recursos do Azure foram criados. Alguns nomes de recursos são acrescentados com caracteres aleatórios.

    | Recursos do Azure | Nome |
    | --- | --- |
    | Workspace do Machine Learning | turborfanDemo |
    | Registro de Contêiner | turbofandemoxxxxxxxx |
    | Applications Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Armazenamento | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Depuração

Você pode inserir instruções do Python no notebook para depuração, como o comando `print()` para mostrar valores. Se você vir variáveis ou objetos que não estão definidos, execute as células em que eles são declarados ou instanciados primeiro.

Talvez seja necessário excluir arquivos e recursos do Azure criados anteriormente se precisar refazer os notebooks.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, usamos dois Jupyter Notebooks em execução no Azure Notebooks para usar os dados dos dispositivos turbofan para treinar um classificador de RUL (vida útil restante), para salvar o classificador como um modelo, para criar uma imagem de contêiner e para implantar e testar a imagem como um serviço Web.

Prossiga para o próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
