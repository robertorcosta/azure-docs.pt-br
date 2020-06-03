---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998101"
---
A próxima etapa é configurar o aplicativo para usar o identificador e a chave de conta. Você os copiou em um editor de texto ao [configurar o recurso Âncoras Espaciais](#create-a-spatial-anchors-resource).

No painel **Projeto**, navegue até `Assets\AzureSpatialAnchors.SDK\Resources`. Selecione `SpatialAnchorConfig`. Em seguida, no painel **Inspetor**, insira `Account Key` como o valor de `Spatial Anchors Account Key` e `Account ID` como o valor de `Spatial Anchors Account Id`.

Em seguida, abra `SpatialAnchorManager.cs`. Localize `CreateSessionAsync()` e adicione a seguinte linha, substituindo seu domínio de conta anterior: `session.Configuration.AccountDomain = "MyAccountDomain";`. Você pode adicionar a linha diretamente antes deste comentário `// Configure authentication`.
