---
title: 'Início Rápido: Configurar o Azure Kinect DK'
description: Este início rápido fornece instruções sobre como configurar o hardware do Azure Kinect DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, set up, hardware, quick, usb, power, viewer, sensor, streaming, setup, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211272"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Início Rápido: Configurar o Azure Kinect DK

Este início rápido fornece diretrizes de como configurar o Azure Kinect DK. Mostraremos como testar a visualização do fluxo de sensor e usar o [Azure Kinect Viewer](azure-kinect-viewer.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="system-requirements"></a>Requisitos do sistema

Verifique os [Requisitos do sistema](system-requirements.md) para ver se a configuração do computador host atende a todos os requisitos mínimos do Azure Kinect DK.

## <a name="set-up-hardware"></a>Configurar o hardware

> [!NOTE]
> Remova a película de proteção da câmera antes de usar o dispositivo.

1. Conecte o conector de alimentação à entrada de alimentação na parte traseira do dispositivo. Conecte o adaptador de energia USB à outra extremidade do cabo e, em seguida, conecte o adaptador a uma tomada elétrica.
2. Conecte o cabo de dados USB ao dispositivo e, em seguida, a uma porta USB 3.0 no computador.
   >[!NOTE]
   >Para obter melhores resultados, conecte o cabo diretamente ao dispositivo e ao computador. Evite usar extensões ou adaptadores extras na conexão.

3. Verifique se o LED de indicador de energia (ao lado do cabo USB) é branco e contínuo.
  
   São necessários alguns segundos para ligar o dispositivo. O dispositivo estará pronto para ser usado quando o indicador de streaming do LED frontal desligar.  

   Para obter mais informações sobre o LED do indicador de energia, confira [O que a luz significa?](hardware-specification.md#what-does-the-light-mean)

    ![Recursos completos do dispositivo](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Baixar o SDK

1. Selecione o link para [Baixar o SDK](sensor-sdk-download.md).
2. Instale o SDK no computador.

## <a name="update-firmware"></a>Atualizar firmware

Para funcionar corretamente, o SDK exige a última versão do firmware do dispositivo. Para verificar e atualizar a versão do firmware, siga as etapas em [Atualizar o firmware do Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Verificar se o dispositivo transmite os dados

1. Inicie o [Azure Kinect Viewer](azure-kinect-viewer.md). Inicie essa ferramenta usando um destes métodos:
   - Inicie o visualizador na linha de comando ou clicando duas vezes no arquivo executável. O arquivo, `k4aviewer.exe`, reside no diretório de ferramentas do SDK (por exemplo, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, em que `X.Y.Z` é a versão instalada do SDK).
   - Inicie o Azure Kinect Viewer no menu **Iniciar** do dispositivo.
2. No Azure Kinect Viewer, selecione **Abrir Dispositivo** > **Iniciar**.

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Verifique se a ferramenta visualiza cada fluxo de sensor:
   - Câmera de profundidade
   - Câmera em cores
   - Câmera infravermelha
   - IMU
   - Microfones

    ![Ferramenta de visualização](./media/quickstarts/visualization-tool.png)

Você concluiu a configuração do Azure Kinect DK. Agora você pode começar a desenvolver seu aplicativo ou integrar serviços.

Em caso de problemas, verifique a [Solução de problemas](troubleshooting.md).

## <a name="see-also"></a>Confira também

- [Informações de hardware do Azure Kinect DK](hardware-specification.md)
- [Atualizar o firmware do dispositivo](update-device-firmware.md)
- Saiba mais sobre o [Azure Kinect Viewer](azure-kinect-viewer.md)

## <a name="next-steps"></a>Próximas etapas

Depois que o Azure Kinect DK estiver pronto e funcionando, você também poderá aprender a
> [!div class="nextstepaction"]
> [Registrar fluxos de sensor em um arquivo](record-sensor-streams-file.md)
