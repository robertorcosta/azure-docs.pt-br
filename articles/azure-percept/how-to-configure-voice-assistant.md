---
title: Configurar o aplicativo de assistente de voz usando o Hub IoT do Azure
description: Configurar o aplicativo de assistente de voz usando o Hub IoT do Azure
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: ec3e06b2d161785b5e6978cdf4cc6415fc0eb592
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661765"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Configurar o aplicativo de assistente de voz usando o Hub IoT do Azure

Este artigo descreve como configurar seu aplicativo de assistente de voz usando o Hub IoT. Para obter um tutorial passo a passo que orienta você durante o processo de criação de um assistente de voz usando o modelo de demonstração, consulte [criar um assistente de voz sem código com o Azure Percept Studio e o áudio do Azure Percept](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>Atualizar sua configuração do assistente de voz

1. Abra o [portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) e digite **Hub IOT** na barra de pesquisa. Clique no ícone para abrir a página do Hub IoT.

1. Na página Hub IoT, selecione o Hub IoT ao qual seu dispositivo foi provisionado.

1. Selecione **IOT Edge** em **Gerenciamento de dispositivo automático** no menu de navegação à esquerda para exibir todos os dispositivos conectados ao Hub IOT.

1. Selecione o dispositivo ao qual seu aplicativo de assistente de voz foi implantado.

1. Clique em **definir módulos**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Imagem.":::

1. Verifique se a entrada a seguir está presente na seção **credenciais de registro de contêiner** . Adicione credenciais, se necessário.

    |Name|Endereço|Nome de Usuário|Senha|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. Na seção **módulos IOT Edge** , selecione **azureearspeechclientmodule**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Imagem.":::

1. Clique na guia **configurações do módulo** . Verifique a seguinte configuração:

    |URI da imagem|Reinicializar política|Status desejado|
    |---------|--------------|--------------|
    |azureedgedevices.azurecr.io/azureearspeechclientmodule:preload-devkit |always|executando|

    Se as configurações não corresponderem, edite-as e clique em **Atualizar**.

1. Clique na guia **variáveis de ambiente** . Verifique se não há nenhuma variável de ambiente definida.

1. Clique na guia **Opções de criação de contêiner** . Verifique se as configurações do **HostConfig** correspondem às mostradas abaixo. Se não forem correspondentes, atualize suas configurações.

    ```
    {
        "HostConfig": {
            "Privileged": true,
            "Binds": [
                "/dev:/dev"
            ]
        }
    }
    ```

1. Clique na guia **configurações de configuração do módulo** . Atualize a seção **speechConfigs** da seguinte maneira:

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedspeechscenarios.blob.core.windows.net/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > A palavra-chave usada acima é uma palavra-chave padrão publicamente disponível. Se você quiser usar seu próprio, poderá adicionar sua própria palavra-chave personalizada carregando um arquivo de tabela criado para o armazenamento de BLOBs. O armazenamento de BLOBs precisa ser configurado com acesso de contêiner anônimo ou acesso de blob anônimo.

## <a name="how-to-find-out-appid-key-and-region"></a>Como descobrir appId, chave e região

Para localizar a **AppID**, a **chave** e a **região**, acesse [Speech Studio](https://speech.microsoft.com/):

1. Entre e selecione o recurso de fala apropriado.
1. No home page do **Speech Studio** , clique em **comandos personalizados** em **assistentes de voz**.
1. Selecione seu projeto de destino.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Imagem.":::

1. Clique em **configurações** no painel de menu à esquerda.
1. A **AppID** e a **chave** estarão localizadas na guia configurações **gerais** .

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Imagem.":::

1. Para localizar sua **região**, abra a guia **recursos do Luis** dentro das configurações. A seleção de **recursos de criação** conterá informações de região.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Imagem.":::

1. Depois de inserir suas informações de **speechConfigs** , clique em **Atualizar**.

1. Clique na guia **rotas** na parte superior da página **definir módulos** . Verifique se você tem uma rota com o seguinte valor:

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Adicione a rota se ela não existir.

1. Clique em **Revisar + Criar**.

1. Clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

Depois de atualizar sua configuração do assistente de voz, retorne à demonstração no Azure Percept Studio para interagir com o aplicativo.