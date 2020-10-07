---
ms.openlocfilehash: 055448eacf1cc12c6d021c6571a0478cb35442ba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89566886"
---
O manifesto de implantação define quais módulos são implantados em um dispositivo de borda. Ele também define as configurações desses módulos. 

Siga estas etapas para gerar o manifesto no arquivo de modelo e implantá-lo no dispositivo de borda.

1. Abra o Visual Studio Code.
1. Ao lado do painel **HUB IOT DO AZURE**, selecione o ícone **Mais ações** para definir a cadeia de conexão do Hub IoT. Copie a cadeia de caracteres do arquivo *src/cloud-to-device-console-app/appsettings.json*. 

    ![Definir a cadeia de conexão de IoT](../../../media/quickstarts/set-iotconnection-string.png)
1. Clique com o botão direito do mouse em **src/edge/deployment.template.json** e selecione **Gerar Manifesto de Implantação do IoT Edge**.

    ![Gerar o manifesto de implantação do IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Esta ação deve criar um arquivo de manifesto chamado *deployment.amd64.json* na pasta *src/edge/config*.
1. Clique com o botão direito do mouse em **src/edge/config/deployment.amd64.json**, selecione **Criar Implantação para Dispositivo Único** e selecione o nome do dispositivo de borda.

    ![Criar uma implantação para um dispositivo único](../../../media/quickstarts/create-deployment-single-device.png)

1. Quando receber um prompt para selecionar um dispositivo do Hub IoT, escolha **lva-sample-device** no menu suspenso.
1. Após cerca de 30 segundos, no canto inferior esquerdo da janela, atualize o Hub IoT do Azure. Agora, o dispositivo de borda mostra os seguintes módulos implantados:

    * Análise Dinâmica de Vídeo no IoT Edge (nome do módulo `lvaEdge`)
    * Simulador RTSP (Real-Time Streaming Protocol) (nome do módulo `rtspsim`)

O módulo do simulador RTSP simula um fluxo de vídeo ao vivo usando um arquivo de vídeo que foi copiado para o dispositivo de borda quando você executou o [script de instalação de recursos da Análise Dinâmica de Vídeo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Se você estiver usando o próprio dispositivo de borda em vez de um provisionado pelo nosso script de instalação, acesse o dispositivo de borda e execute os seguintes comandos com **direitos de administrador**, para efetuar pull e armazenar o arquivo de vídeo de exemplo usado neste guia de início rápido:  

```
mkdir /home/lvaadmin/samples      
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
Neste estágio, os módulos estão implantados, mas nenhum grafo de mídia está ativo.
