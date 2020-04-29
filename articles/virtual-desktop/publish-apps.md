---
title: Publicar aplicativos internos na área de trabalho virtual do Windows – Azure
description: Como publicar aplicativos internos na área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127749"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publicar aplicativos internos na área de trabalho virtual do Windows

Este artigo lhe dirá como publicar aplicativos em seu ambiente de área de trabalho virtual do Windows.

## <a name="publish-built-in-apps"></a>Publicar aplicativos internos

Para publicar um aplicativo interno:

1. Conecte-se a uma das máquinas virtuais em seu pool de hosts.
2. Obtenha o **PackageFamilyName** do aplicativo que você deseja publicar seguindo as instruções neste [artigo](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Por fim, execute o seguinte cmdlet `<PackageFamilyName>` com substituído pelo **PackageFamilyName** encontrado na etapa anterior:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> A área de trabalho virtual do Windows só dá suporte à publicação de `C:\Program Files\Windows Apps`aplicativos com locais de instalação que começam com.

## <a name="update-app-icons"></a>Atualizar ícones de aplicativos

Depois de publicar um aplicativo, ele terá o ícone de aplicativo padrão do Windows em vez de sua imagem de ícone normal. Para alterar o ícone para o ícone normal, coloque a imagem do ícone desejado em um compartilhamento de rede. Os formatos de imagem com suporte são PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Publicar o Microsoft Edge

O processo usado para publicar o Microsoft Edge é um pouco diferente do processo de publicação para outros aplicativos. Para publicar o Microsoft Edge com a página inicial padrão, execute este cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como configurar feeds para organizar como os aplicativos são exibidos para usuários em [Personalizar feed para usuários da área de trabalho virtual do Windows](customize-feed-for-virtual-desktop-users.md).
- Saiba mais sobre o recurso de anexo de aplicativo MSIX em [Configurar anexação de aplicativo MSIX](app-attach.md).

