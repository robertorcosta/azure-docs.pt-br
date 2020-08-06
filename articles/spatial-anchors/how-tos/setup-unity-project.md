---
title: Instalar âncoras espaciais do Azure para o Unity
description: Configurar um projeto do Unity para usar âncoras espaciais do Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812272"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurando âncoras espaciais do Azure em um projeto do Unity

## <a name="requirements"></a>Requisitos

As âncoras espaciais do Azure atualmente dão suporte ao Unity 2019,4 (LTS) com as seguintes configurações.

* O Unity 2019,4 com o AR Foundation 3,1 tem suporte nas âncoras espaciais do Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Configurando um projeto

As âncoras espaciais do Azure para o Unity são distribuídas no momento usando um pacote de ativos do Unity ( `.unitypackage` ), que pode ser encontrado nas [versões do GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Importar o pacote de ativos

1. Baixe o `AzureSpatialAnchors.unitypackage` arquivo da versão que você deseja direcionar das [versões do GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Siga as instruções [aqui](https://docs.unity3d.com/Manual/AssetPackagesImport.html) para importar o pacote de ativos do Unity para seu projeto.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: criar e localizar âncoras no Unity](./create-locate-anchors-unity.md)
