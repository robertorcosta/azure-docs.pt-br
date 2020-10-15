---
title: Instalar âncoras espaciais do Azure para um aplicativo/WinRT HoloLens do C++
description: Configurar um projeto de HoloLens do C++/WinRT para usar âncoras espaciais do Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43d5c1ae03c359dcbef21f8e7ba3205bc6ab0004
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096106"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Configurando âncoras espaciais do Azure em um projeto de HoloLens do C++/WinRT

## <a name="requirements"></a>Requisitos

* O [Visual Studio 2019](https://www.visualstudio.com/downloads/) instalado com a carga de trabalho de **desenvolvimento plataforma universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** .

## <a name="configuring-a-project"></a>Configurando um projeto

As âncoras espaciais do Azure para o HoloLens e o C++/WinRT são distribuídas usando o pacote NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) .

Siga as instruções [aqui](/nuget/consume-packages/install-use-packages-visual-studio) para usar o Gerenciador de pacotes NuGet do Visual Studio para instalar o pacote NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) em seu projeto.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: criar e localizar âncoras em C++/WinRT](./create-locate-anchors-cpp-winrt.md)