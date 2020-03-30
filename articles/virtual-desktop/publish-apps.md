---
title: Publicar aplicativos incorporados no Windows Virtual Desktop - Azure
description: Como publicar aplicativos incorporados no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127749"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publique aplicativos incorporados no Windows Virtual Desktop

Este artigo lhe dirá como publicar aplicativos em seu ambiente de Desktop Virtual windows.

## <a name="publish-built-in-apps"></a>Publicar aplicativos internos

Para publicar um aplicativo integrado:

1. Conecte-se a uma das máquinas virtuais em seu pool de host.
2. Obtenha o **PackageFamilyName** do aplicativo que deseja publicar seguindo as instruções [deste artigo](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Por fim, execute o `<PackageFamilyName>` cmdlet a seguir com substituído pelo **PackageFamilyName** encontrado na etapa anterior:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> O Windows Virtual Desktop só suporta aplicativos de `C:\Program Files\Windows Apps`publicação com locais de instalação que começam com .

## <a name="update-app-icons"></a>Atualizar ícones do aplicativo

Depois de publicar um aplicativo, ele terá o ícone padrão do aplicativo windows em vez de sua imagem de ícone regular. Para alterar o ícone para seu ícone regular, coloque a imagem do ícone desejado em um compartilhamento de rede. Os formatos de imagem suportados são PNG, BMP, GIF, JPG, JPEG e ICO.

## <a name="publish-microsoft-edge"></a>Publicar o Microsoft Edge

O processo que você usa para publicar o Microsoft Edge é um pouco diferente do processo de publicação para outros aplicativos. Para publicar o Microsoft Edge com a página inicial padrão, execute este cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar feeds para organizar como os aplicativos são exibidos para usuários no [Feed Personalizar para usuários do Windows Virtual Desktop](customize-feed-for-virtual-desktop-users.md).
- Saiba mais sobre o recurso de anexar o aplicativo MSIX no [attach do aplicativo Configuração MSIX](app-attach.md).

