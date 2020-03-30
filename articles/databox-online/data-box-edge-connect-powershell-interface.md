---
title: Conecte-se e gerencie o dispositivo Microsoft Azure Data Box Edge através da interface Windows PowerShell | Microsoft Docs
description: Descreve como se conectar e, em seguida, gerenciar data box edge através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265474"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gerenciar um dispositivo Azure Data Box Edge via Windows PowerShell

A solução Azure Data Box Edge permite processar dados e enviá-los pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento do dispositivo Data Box Edge. Você pode usar o portal Azure, a interface web local ou a interface Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra nas tarefas que você faz usando a interface PowerShell.

Este artigo inclui os seguintes procedimentos:

- Conecte-se à interface PowerShell
- Criar um pacote de suporte
- Carregar um certificado
- Redefinir o dispositivo
- Exibir informações do dispositivo
- Obtenha registros de cálculo
- Monitorar e solucionar problemas módulos de computação

## <a name="connect-to-the-powershell-interface"></a>Conecte-se à interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar um certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Você também pode carregar certificados IoT Edge para permitir uma conexão segura entre seu dispositivo IoT Edge e os dispositivos downstream que podem se conectar a ele. Existem três certificados IoT Edge (formato *.pem)* que você precisa instalar:

- Certificado de CA raiz ou ca proprietário
- Certificado de autoridade de certificação de dispositivo
- Certificado de chave do dispositivo

O exemplo a seguir mostra o uso deste cmdlet para instalar certificados IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Ao executar este cmdlet, você será solicitado a fornecer a senha para o compartilhamento de rede.

Para obter mais informações sobre certificados, vá para [certificados Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [Instale certificados em um gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Exibir informações do dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Redefinir o dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obtenha registros de cálculo

Se a função de computação estiver configurada no seu dispositivo, você também poderá obter os logs de computação através da interface PowerShell.

1. [Conecte-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Use `Get-AzureDataBoxEdgeComputeRoleLogs` o para obter os registros de computação do seu dispositivo.

    O exemplo a seguir mostra o uso deste cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Aqui está uma descrição dos parâmetros utilizados para o cmdlet:
    - `Path`: Forneça um caminho de rede para o compartilhamento onde você deseja criar o pacote de log de computação.
    - `Credential`: Forneça o nome de usuário para o compartilhamento da rede. Ao executar este cmdlet, você precisará fornecer a senha de compartilhamento.
    - `FullLogCollection`: Este parâmetro garante que o pacote de log contenha todos os registros de cálculo. Por padrão, o pacote de log contém apenas um subconjunto de logs.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorar e solucionar problemas módulos de computação

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Saia da sessão remota

Para sair da sessão remota do PowerShell, feche a janela PowerShell.

## <a name="next-steps"></a>Próximas etapas

- Implante o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.
