---
title: Provisionar um dispositivo X.509 simulado ao Hub IoT do Azure usando Python
description: Início Rápido – Criar e provisionar um dispositivo X.509 simulado usando o SDK de dispositivo Python para o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT. Este início rápido usa registros individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 44f1a2cd3336eeae87878c333fb05d2e6b1f88e8
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605381"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Início Rápido: Criar e provisionar um dispositivo X.509 simulado usando o SDK do dispositivo Python para o Serviço de Provisionamento do Dispositivo Hub IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Neste início rápido, você criará um dispositivo X.509 simulado em um computador Windows. Você usará um código Python de exemplo do dispositivo para conectar esse dispositivo simulado ao hub IoT usando um registro individual com o DPS (Serviço de Provisionamento de Dispositivos).

## <a name="prerequisites"></a>Prerequisites

- Análise dos [Conceitos de provisionamento automático](concepts-auto-provisioning.md).
- Conclusão de [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md).
- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2015 e posterior](https://visualstudio.microsoft.com/vs/) com o Desenvolvimento para desktop com C++.
- [Sistema de build CMake](https://cmake.org/download/).
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Este artigo aplica-se apenas ao SDK do Python V1 preterido. Os clientes de dispositivo e serviço para o Serviço de Provisionamento de Dispositivos no Hub IoT ainda não estão disponíveis na V2. Atualmente, a equipe está empenhada no trabalho de levar a V2 à paridade de recursos.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Verifique se instalou o [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 ou posterior com a carga de trabalho "Desenvolvimento de área de trabalho com C++" habilitada para sua instalação do Visual Studio.

2. Baixe e instale o [sistema de compilação CMake](https://cmake.org/download/).

3. Verifique se o `git` está instalado em seu computador e é adicionado às variáveis de ambiente que podem ser acessadas pela janela de comando. Confira [ferramentas de cliente Git do Software Freedom Conservancy](https://git-scm.com/download/) para obter a versão mais recente das ferramentas `git` a serem instaladas, que inclui o **Git Bash**, o aplicativo de linha de comando que você pode usar para interagir com seu repositório Git local. 

4. Abra um prompt de comando ou o Bash do Git. Clone o repositório do GitHub para obter um exemplo de código de simulação do dispositivo.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

5. Crie uma pasta em sua cópia local do repositório GitHub para o processo de compilação do CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

6. Execute o comando a seguir para criar a solução do Visual Studio para o cliente de provisionamento.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Criar um certificado de dispositivo X.509 autoassinado e entrada de registro individual

Nesta seção, você usará um certificado X.509 autoassinado. É importante ter em mente os seguintes pontos:

* Os certificados autoassinados são somente para teste e não devem ser usados em produção.
* A data de validade padrão para um certificado autoassinado é de um ano.

Você usará o código de exemplo do SDK de C do IoT do Azure para criar o certificado a ser usado na entrada de registro individual do dispositivo simulado.

O Serviço de Provisionamento de Dispositivos de IoT do Azure dá suporte a dois tipos de registros:

- [Grupos de registros](concepts-service.md#enrollment-group): usados para inscrever vários dispositivos relacionados.
- [Registros individuais](concepts-service.md#individual-enrollment): usados para inscrever um único dispositivo.

Este artigo vai demonstrar o uso de registros individuais.

1. Abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln`e compile-a no Visual Studio.

2. Clique com o botão direito do mouse no projeto **dados\_dispositivo\_registro** da pasta **Provision\_Tools** e selecione **Definir como Projeto de Inicialização**. Execute a solução. 

3. Na janela de saída, insira `i` para registro individual quando solicitado. A janela de saída exibe um certificado X.509 gerado localmente para seu dispositivo simulado. 
    
    Copie o primeiro certificado para área de transferência. Comece com a primeira ocorrência de:
    
        -----BEGIN CERTIFICATE----- 
        
    Termine a cópia após a primeira ocorrência de:
    
        -----END CERTIFICATE-----
        
    Inclua essas duas linhas também. 

    ![Aplicativo de registro de dispositivo de dados](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
4. Crie um arquivo chamado **_X509testcertificate.pem_** em seu computador Windows, abra-o em um editor de sua escolha e copie o conteúdo da área de transferência para o arquivo. Salve o arquivo. 

5. Entre no portal do Azure, selecione o botão **Todos os recursos** no menu esquerdo e abra o serviço de provisionamento.

6. No menu do Serviço de Provisionamento de Dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** e, em seguida, selecione o botão **Adicionar registro individual**, na parte superior. 

7. No painel **Adicionar Registro**, insira as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidade.
   - No *Arquivo .pem ou .cer de certificado primário*, escolha *Selecionar um arquivo* para selecionar o arquivo de certificado **X509testcertificate.pem** criado nas etapas anteriores.
   - Opcionalmente, você pode fornecer as seguintes informações:
     - Selecione um hub IoT vinculado com o serviço de provisionamento.
     - Insira uma ID de dispositivo exclusiva. Evite dados confidenciais ao nomear seu dispositivo. 
     - Atualize o **Estado inicial do dispositivo gêmeo** com a configuração inicial desejada para o dispositivo.
   - Uma vez concluído, pressione o botão **Salvar**. 

     [![Adicionar um registro individual para atestado de X.509 no portal](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Após o registro bem-sucedido, o dispositivo X.509 é exibido como **riot-device-cert** na coluna *ID do Registro* na guia o *Registros Individuais*. 

## <a name="simulate-the-device"></a>Simular o dispositivo

1. No menu do Serviço de Provisionamento de Dispositivos, selecione **Visão geral**. Anote o _Escopo da ID_ e o _Ponto de Extremidade de Serviço Global_.

    ![Informações de serviço](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Baixe e instale o [Python 2.x ou 3.x](https://www.python.org/downloads/). Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python às variáveis de ambiente específica da plataforma. Se você estiver usando o Python 2.x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Se você estiver usando o Windows, instale também os [Pacotes Redistribuíveis do Visual C++ para Visual Studio 2015](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). Os pacotes de pip exigem os pacotes redistribuíveis para carregar/executar as DLLs de C.

3. Execute [estas instruções](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) para compilar os pacotes do Python.

   > [!NOTE]
   > Se usar `pip`, certifique-se de também instalar o pacote `azure-iot-provisioning-device-client`.

4. Navegue até a pasta de amostras.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

5. Usando o IDE do Python, edite o script python chamado **provisionamento\_dispositivo\_cliente\_sample.py**. Modifique as variáveis _GLOBAL\_PROV\_URI_ e _ID\_SCOPE_ para os valores observados anteriormente.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.X509
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

6. Execute o exemplo. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

7. O aplicativo se conectará, registrará o dispositivo e exibirá uma mensagem de registro bem-sucedido.

    ![registro bem-sucedido](./media/python-quick-create-simulated-device-x509/enrollment-success.png)

8. No portal, navegue até o hub IoT vinculado ao seu serviço de provisionamento e abra a folha **Device Explorer**. No provisionamento bem-sucedido do dispositivo X.509 simulado para o hub, sua ID de dispositivo aparecerá na folha **Device Explorer** com o *STATUS* **habilitado**. Talvez seja necessário pressionar o botão **Atualizar**, na parte superior, se você já tiver aberto a folha antes de executar o aplicativo do dispositivo de exemplo. 

    ![Dispositivo é registrado no Hub IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Se você tiver alterado o *estado de dispositivo gêmeo inicial* do valor padrão na entrada de registro para o seu dispositivo, pode receber o estado desejado duas do hub e agir de acordo. Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste início rápido. Caso contrário, use as seguintes etapas para excluir todos os recursos criados por este início rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
2. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra a folha **Gerenciar Registros** do seu serviço e selecione a guia **Registros Individuais**. Marque a caixa de seleção ao lado da *ID DE REGISTRO* do dispositivo registrado neste início rápido e pressione o botão **Excluir**, na parte superior do painel. 
3. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e seu Hub IoT. Abra a folha **Dispositivos IoT** do hub, marque a caixa de seleção ao lado da *ID DO DISPOSITIVO* registrado neste início rápido e pressione o botão **Excluir**, na parte superior do painel.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um dispositivo simulado X.509 no seu computador Windows e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure no portal. Para saber como registrar seu dispositivo X.509 programaticamente, continue com o início rápido do registro programático de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Início rápido do Azure – Registrar dispositivos X.509 no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](quick-enroll-device-x509-python.md)
