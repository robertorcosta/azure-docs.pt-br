---
title: Implantar o Azure Machine Learning em um dispositivo – Azure IoT Edge | Microsoft Docs
description: Neste tutorial, você cria um modelo do Azure Machine Learning e implanta-o como um módulo em um dispositivo de borda
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/16/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 04c09c60a8b633c9ddb51fbe97ac02a319472448
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434690"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Implantar o Azure Machine Learning como um módulo do IoT Edge (versão prévia)

Use os Azure Notebooks para desenvolver um módulo de aprendizado de máquina e para implantá-lo em um dispositivo Linux executando o Azure IoT Edge. 

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial mostra todas as etapas necessárias para implantar um módulo do Azure Machine Learning que prevê quando um dispositivo falhará com base nos dados de temperatura do computador simulado. Para saber mais sobre o Serviço do Azure Machine Learning no IoT Edge, confira a [Documentação do Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

O módulo do Azure Machine Learning criado neste tutorial lê os dados ambientais gerados pelo dispositivo e rotula as mensagens como anômalas ou não.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um módulo do Azure Machine Learning
> * Enviar por push a um contêiner de módulo para um Registro de Contêiner do Azure
> * Implantar um módulo do Azure Machine Learning no dispositivo do IoT Edge
> * Exibir os dados gerados

>[!NOTE]
>Os módulos do Azure Machine Learning no Azure IoT Edge são públicos na versão prévia.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar uma máquina virtual do Azure como um dispositivo do IoT Edge seguindo as etapas no início rápido do [Linux](quickstart-linux.md).
* O módulo do Azure Machine Learning não dá suporte a contêineres do Windows.
* O módulo do Azure Machine Learning não dá suporte a processadores ARM.

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um Workspace do Azure Machine Learning. Siga as instruções em [Usar o portal do Azure para começar com o Azure Machine Learning](../machine-learning/service/quickstart-get-started.md) para criar um desses modelos e aprender como usá-lo.
   * Anote o nome do workspace, o grupo de recursos e a ID da assinatura. Esses valores estão disponíveis na visão geral de workspace no portal do Azure. Você usará esses valores posteriormente no tutorial para conectar um Azure Notebook aos recursos do workspace. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Criar e implantar um módulo do Azure Machine Learning

Nesta seção, você converte arquivos de modelo de machine learning treinados em um contêiner do Serviço do Azure Machine Learning. Todos os componentes necessários para a imagem do Docker estão no [repositório Git do Kit de Ferramentas de IA para o Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Siga estas etapas para carregar esse repositório no Microsoft Azure Notebooks para criar o contêiner e envie-o por push ao Registro de Contêiner do Azure.


1. Navegue até seus projetos do Azure Notebooks. Você pode chegar lá do seu workspace do Serviço do Azure Machine Learning no [portal do Azure](https://portal.azure.com) ou entrando em [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) com sua conta do Azure.

2. Selecione **Carregar o Repositório do GitHub**.

3. Forneça o nome do repositório do GitHub a seguir: `Azure/ai-toolkit-iot-edge`. Desmarque a caixa **Público** se você quiser manter seu projeto privado. Selecione **Importar**. 

4. Depois que a importação for concluída, navegue para o novo projeto **IA-toolkit-iot-edge** e abra a pasta **tutorial de detecção de anomalias do IoT Edge**. 

5. Verifique se o projeto está em execução. Se não estiver, selecione **Executar em Computação Gratuita**.

   ![Executar em computação gratuita](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Abra o arquivo **aml_config/config.json**.

7. Edite o arquivo de configuração para incluir os valores de ID de assinatura do Azure, um grupo de recursos em sua assinatura e o nome do workspace do Serviço do Azure Machine Learning. Você pode obter todos esses valores da seção **Visão geral** do seu workspace no Azure. 

8. Salve o arquivo de configuração.

9. Abra o arquivo **00-anomaly-detection-tutorial.ipynb**.

10. Quando solicitado, selecione o kernel **Python 3.6** e, em seguida, selecione **Definir Kernel**.

11. Edite a primeira célula no notebook de acordo com as instruções nos comentários. Use o mesmo grupo de recursos, ID da assinatura e nome do workspace que você adicionou ao arquivo de configuração.

12. Execute as células no notebook selecionando-as e selecionando **Executar** ou pressionando `Shift + Enter`.

    >[!TIP]
    >Algumas das células no notebook do tutorial de detecção de anomalias são opcionais, pois elas criam recursos que alguns usuários podem ou não ter ainda, assim como um Hub IoT. Se colocar suas informações de recursos existentes na primeira célula, você receberá erros se executar as células que criam novos recursos, porque o Azure não criará recursos duplicados. Não há problema nisso e você pode ignorar os erros ou ignorar por completo essas seções opcionais. 

Ao concluir todas as etapas no notebook, você treinou um modelo de detecção de anomalias, compilado esse modelo como uma imagem de contêiner do Docker e enviado essa imagem por push para o Registro de Contêiner do Azure. Em seguida, você testou o modelo e, finalmente, implantou-o em seu dispositivo IoT Edge. 

## <a name="view-container-repository"></a>Exibir repositório de contêiner

Verifique se a imagem de contêiner foi criada com êxito e armazenada no Registro de Contêiner do Azure associado ao seu ambiente de aprendizado de máquina. O notebook que você usou na seção anterior forneceu automaticamente a imagem de contêiner e as credenciais de registro para seu dispositivo IoT Edge, mas você deve saber onde eles são armazenados para que possa encontrar as informações por conta própria mais tarde. 

1. No [portal do Azure](https://portal.azure.com), navegue até o workspace do serviço do Machine Learning. 

2. A seção **Visão geral** lista os detalhes do workspace e seus recursos associados. Selecione o valor de **Registro**, que deve ser o nome do workspace seguido de números aleatórios. 

3. No registro de contêiner, selecione **Repositórios**. Você deve ver um repositório chamado **tempanomalydetection**, que foi criado pelo notebook que você executou na seção anterior. 

4. Selecione **tempanomalydetection**. Você deve ver que o repositório tem uma tag: **1**. 

   Agora que você sabe o nome do Registro, o nome do repositório e a tag, você sabe o caminho completo da imagem do contêiner. Caminhos para imagens têm a aparência **\<nome_do_Registro\>.azurecr.io/tempanomalydetection:1**. Você pode usar o caminho da imagem para implantar esse contêiner para dispositivos IoT Edge. 

5. No registro de contêiner, selecione **Chaves de acesso**. Você deve ver um número de credenciais de acesso, incluindo o **Servidor de logon** e o **Nome de usuário** e a **Senha** de um usuário administrador.

   Essas credenciais podem ser incluídas no manifesto de implantação para permitir ao dispositivo IoT Edge acesso para efetuar pull de imagens de contêiner do Registro. 

Agora você sabe onde a imagem de contêiner do Machine Learning é armazenada. A próxima seção mostra o passo a passo das etapas para exibir o contêiner em execução como um módulo em seu dispositivo IoT Edge. 

## <a name="view-generated-data"></a>Exibir os dados gerados

Você pode exibir mensagens que são geradas por módulo do IoT Edge, e você pode exibir as mensagens que são entregues para o Hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Exibir dados no seu dispositivo IoT Edge

No seu dispositivo IoT Edge, você pode exibir as mensagens que são enviadas de cada módulo individual.

Você talvez precise usar `sudo` para permissões elevadas para executar comandos `iotedge`. Sair e entrar novamente no seu dispositivo atualiza automaticamente suas permissões.

1. Exibir todos os módulos no seu dispositivo IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Exibir as mensagens que estão sendo enviadas de um dispositivo específico. Use o nome do módulo da saída do comando anterior.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Exibir dados que chegam ao seu Hub IoT

Você pode exibir as mensagens do dispositivo para nuvem recebidas pelo Hub IoT usando a [extensão do Kit de Ferramentas do Hub IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente, extensão do Kit de Ferramentas do Azure IoT).

As etapas a seguir mostram como configurar o Visual Studio Code para monitorar mensagens de dispositivo para a nuvem que chegam em seu Hub IoT.

1. No Visual Studio Code, selecione **Dispositivos de Hub IoT**.

2. Selecione **...** , em seguida, selecione **Definir cadeia de Conexão de Hub IoT** no menu.

   ![Definir cadeia de conexão do Hub IoT](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Na caixa de texto que é aberta na parte superior da página, insira a cadeia de caracteres de conexão iothubowner para seu Hub IoT. Seu dispositivo Edge IoT deverá aparecer na lista de dispositivos de Hub IoT.

4. Selecione **...** novamente e selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**.

5. Observe as mensagens recebidas de tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade chamada **anomaly**, que o módulo de machine learning fornece com um valor true ou false. A propriedade **AzureMLResponse** contém o valor "OK" se o modelo foi executado com êxito.

   ![Resposta do Serviço do Azure Machine Learning no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um módulo do IoT Edge da plataforma Azure Machine Learning. Continue com um dos outros tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)

