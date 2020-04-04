---
title: Equipes da Microsoft no Windows Virtual Desktop - Azure
description: Como usar o Microsoft Teams no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b9b33076a2c2cea27fea181b760a721488682c9
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657014"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Use o Microsoft Teams no windows virtual desktop

> Aplica-se a: Windows 10 e Windows 10 IoT Enterprise

Os ambientes virtualizados apresentam um conjunto único de desafios para aplicativos de colaboração, como o Microsoft Teams, incluindo aumento da latência, alto uso de CPU host e desempenho geral de áudio e vídeo. Para saber mais sobre como usar o Microsoft Teams em ambientes VDI, confira [equipes de infra-estrutura de desktop virtualizada.](https://docs.microsoft.com/microsoftteams/teams-for-vdi)

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o Microsoft Teams no Windows Virtual Desktop, você precisará fazer essas coisas:

- Instale [o cliente Windows Desktop](connect-windows-7-and-10.md) em um dispositivo Windows 10 que atenda aos [requisitos de hardware](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)do Microsoft Teams .
- Conecte-se a uma máquina virtual Enterprise (VM) multi-sessão do Windows 10 ou com Windows 10.
- [Prepare sua rede](https://docs.microsoft.com/microsoftteams/prepare-network) para as equipes da Microsoft.

## <a name="use-unoptimized-microsoft-teams"></a>Use equipes Microsoft não otimizadas

Você pode usar equipes microsoft não otimizadas em seus ambientes de Desktop Virtual windows para aproveitar os recursos completos de chat e colaboração das Equipes Microsoft, bem como chamadas de áudio. A qualidade do áudio nas chamadas variará de acordo com a configuração do host porque as chamadas não otimizadas usam mais da CPU do host.

### <a name="prepare-your-image-for-teams"></a>Prepare sua imagem para equipes

Para habilitar a instalação de equipes por máquina, defina a seguinte chave de registro no host:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 0x1
```

### <a name="install-microsoft-teams"></a>Instale as equipes da Microsoft

Você pode implantar o aplicativo de desktop Teams usando uma instalação por máquina. Para instalar o Microsoft Teams no ambiente de desktop virtual do Windows:

1. Baixe o [pacote MSI das equipes](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) que corresponde ao seu ambiente. Recomendamos o uso do instalador de 64 bits em um sistema operacional de 64 bits.
2. Execute este comando para instalar o MSI na VM host.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSERS=1
      ```

      Isso instalará equipes para arquivos de programa ou arquivos de programa (x86). Da próxima vez que você fizer login e iniciar equipes, o aplicativo pedirá suas credenciais.

      > [!NOTE]
      > Usuários e administradores não podem desativar o lançamento automático para equipes durante o login neste momento.

      Para desinstalar o MSI da VM host, execute este comando:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Se você instalar Equipes com a configuração MSI ALLUSERS=1, as atualizações automáticas serão desativadas. Recomendamos que você certifique-se de atualizar equipes pelo menos uma vez por mês.
