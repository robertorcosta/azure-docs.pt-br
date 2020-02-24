---
title: Restaurar o Azure Kinect DK
description: Descreve como restaurar um dispositivo Azure Kinect DK para a imagem de fábrica
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, reset
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201892"
---
# <a name="reset-azure-kinect-dk"></a>Restaurar o Azure Kinect DK

Você poderá estar em uma situação na qual precisará restaurar o Azure Kinect DK para a imagem de fábrica (por exemplo, se uma atualização de firmware não foi instalada corretamente).

1. Desligue o Azure Kinect DK. Para fazer isso, remova o cabo USB e o cabo de alimentação.
  ![Um diagrama que mostra a localização do parafuso que cobre o botão de reinicialização.](media/reset-azure-kinect-dk-diagram.png)
1. Para localizar o botão de reinicialização, remova o parafuso localizado na trava de montagem do tripé.
1. Reconecte o cabo de alimentação.
1. Insira a ponta de um clipe esticado no orifício do parafuso vazio, na trava da montagem do tripé.
1. Use o clipe para pressionar suavemente e segurar o botão de reinicialização.
1. Enquanto você segura o botão de reinicialização, reconecte o cabo USB.
1. Após cerca de 3 segundos, a luz do indicador de energia mudará para âmbar. Após a mudança da luz, libere o botão de reinicialização.  
   
   Depois que você liberar o botão de reinicialização, a luz do indicador de energia piscará na cores branca e âmbar enquanto o dispositivo estiver sendo restaurado. 
1. Aguarde até a luz do indicador de energia ficar branca e contínua.
1. Substitua o parafuso na trava da montagem do tripé sobre o botão de reinicialização.
1. Use o Azure Kinect Viewer para verificar se o firmware foi restaurado. Para fazer isso, inicie o [Azure Kinect Viewer](azure-kinect-viewer.md) e, em seguida, selecione **Informações de versão do firmware do dispositivo** para ver a versão do firmware que está instalada no Azure Kinect DK.

Sempre verifique se você tem o firmware mais recente instalado no dispositivo. Para obter a última versão do firmware, use a Azure Kinect Firmware Tool. Para obter mais informações sobre como verificar o status do firmware, confira [Verificar a versão do firmware do dispositivo](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Tópicos relacionados

- [Sobre o Azure Kinect DK](about-azure-kinect-dk.md)
- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Especificações de hardware do Azure Kinect DK: ambiente operacional](hardware-specification.md#operating-environment)
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Sincronização entre vários dispositivos do Azure Kinect DK](multi-camera-sync.md)
