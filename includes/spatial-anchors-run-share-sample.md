---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882303"
---
## <a name="androidtabandroid"></a>[Android](#tab/Android)

O exemplo do Java Android dá suporte ao compartilhamento entre dispositivos.
Abra o arquivo `SharedActivity.java` na pasta de exemplos no Android Studio. Insira a URL obtida na etapa anterior (em sua implantação do Azure do aplicativo Web ASP.NET) como o valor de `SharingAnchorsServiceUrl` no arquivo `SharedActivity.java`. Substitua o `index.html` na URL por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="iostabios"></a>[iOS](#tab/iOS)

O exemplo do iOS Objective-C dá suporte ao compartilhamento entre dispositivos.
Abra o arquivo `SharedDemoViewController.m` na pasta de exemplos. Insira a URL obtida na etapa anterior (em sua implantação do Azure do aplicativo Web ASP.NET) como o valor de `SharingAnchorsServiceUrl` no arquivo `SharedActivity.java`. Substitua o `index.html` na URL por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarintabxamarin"></a>[Xamarin](#tab/Xamarin)

Os exemplos do Xamarin Android e iOS dão suporte ao compartilhamento entre dispositivos.
Abra o arquivo `AccountDetails.cs` na pasta de exemplos. Insira a URL obtida na etapa anterior (em sua implantação do Azure do aplicativo Web ASP.NET) como o valor de `AnchorSharingServiceUrl` no arquivo `SharedActivity.java`. Substitua o `index.html` na URL por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unitytabunity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **Projeto**, navegue para `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo de cena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

No painel **Projeto**, navegue até `Assets\AzureSpatialAnchors.Examples\Resources`. Selecione `SpatialAnchorSamplesConfig`. Em seguida, no painel **Inspector**, insira o `Sharing Anchors Service url` (de sua implantação do Azure do aplicativo Web ASP.NET) como o valor para `Base Sharing Url`, substituindo `index.html` por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **Arquivo** > **Salvar**.

## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

### <a name="deploy-to-android-device"></a>Implantar em um dispositivo Android

Entre em seu dispositivo Android e conecte-o em seu computador usando um cabo USB.

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Sob **Cenas em compilação**, verifique se todas as cenas tem uma marca de seleção ao lado deles.

Certifique-se de que **Exportar projeto** não tem uma marca de seleção. Selecione **Compilar e Executar**. Você será solicitado a salvar seu `.apk` arquivo. Você pode escolher qualquer nome para ele.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implantar em um dispositivo iOS

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Sob **Cenas em compilação**, verifique se todas as cenas tem uma marca de seleção ao lado deles.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

No Xcode, interrompa o aplicativo selecionando **Parar**.
