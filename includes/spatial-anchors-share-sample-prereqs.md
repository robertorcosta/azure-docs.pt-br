---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 07/31/2020
ms.author: rgarcia
ms.openlocfilehash: 310c0f547ee11a3243589c364755a30a84be1a25
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810151"
---
## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

* Leia a [Visão geral de Âncoras Espaciais do Azure](../articles/spatial-anchors/overview.md).
* Concluído um dos [Inícios Rápidos de 5 minutos](../articles/spatial-anchors/index.yml).
* Conhecimento básico sobre C# e Unity.
* Conhecimento básico sobre <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a>, se você quiser usar o Android, ou <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>, se você quiser usar o iOS.
* Um computador Windows, no qual o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> ou posterior está instalado com a carga de trabalho **ASP.NET e desenvolvimento Web**.
* O [SDK do .NET Core 3.1](https://dotnet.microsoft.com/download).
* Um ou mais dispositivos (iOS ou Android) nos quais implantar e executar um aplicativo.
  * Se você estiver usando Android, será necessário ter:
    * <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3</a> ou posterior, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> e <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> instalados no computador Windows.
    * Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado por desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatível com ARCore</a>.
  * Se você estiver usando iOS, será necessário ter:
    * Um computador macOS no qual o <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> ou posterior, o <a href="https://cocoapods.org" target="_blank">CocoaPods</a> e o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> estejam instalados.
    * Um dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com ARKit</a> habilitado por desenvolvedor.
    * Git instalado via Homebrew. Insira o seguinte comando em uma única linha no Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` Em seguida, executar `brew install git`.
