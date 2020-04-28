---
title: Microsoft Teams na área de trabalho virtual do Windows – Azure
description: Como usar o Microsoft Teams na área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187521"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Usar o Microsoft Teams na área de trabalho virtual do Windows

> Aplica-se a: Windows 10 e Windows 10 IoT Enterprise

Ambientes virtualizados apresentam um conjunto exclusivo de desafios para aplicativos de colaboração como o Microsoft Teams, incluindo maior latência, uso de CPU de host alto e desempenho geral de áudio e vídeo baixo. Para saber mais sobre como usar o Microsoft Teams em ambientes de VDI, confira [equipes para infraestrutura de área de trabalho virtualizada](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar o Microsoft Teams na área de trabalho virtual do Windows, você precisará fazer o seguinte:

- Instale o [cliente de desktop do Windows](connect-windows-7-and-10.md) em um dispositivo Windows 10 que atenda aos requisitos de [hardware](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)do Microsoft Teams.
- Conecte-se a uma VM (máquina virtual) do Windows 10 de várias sessões ou do Windows 10 Enterprise.
- [Prepare sua rede para o](https://docs.microsoft.com/microsoftteams/prepare-network) Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Usar o Microsoft Teams não otimizado

Você pode usar as equipes da Microsoft em seus ambientes de área de trabalho virtual do Windows para aproveitar os recursos de chat e colaboração do Microsoft Teams. A área de trabalho virtual do Windows não dá suporte a equipes em otimizações de AV (áudio/vídeo) do VDI. Não há suporte para chamadas e reuniões. Se as políticas de sua organização permitirem, você ainda poderá fazer chamadas de áudio e ingressar em reuniões com áudio, mas a qualidade de áudio não otimizada em chamadas variará com base na sua configuração de host e pode não ser confiável. Para saber mais, Confira as [equipes sobre a consideração de desempenho de VDI](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations).

### <a name="prepare-your-image-for-teams"></a>Preparar sua imagem para equipes

Para habilitar a instalação de equipes por computador, defina a seguinte chave do registro no host:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Instalar o Microsoft Teams

Você pode implantar o aplicativo de área de trabalho de equipes usando uma instalação por máquina. Para instalar o Microsoft Teams em seu ambiente de área de trabalho virtual do Windows:

1. Baixe o [pacote MSI das equipes](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) que corresponde ao seu ambiente. É recomendável usar o instalador de 64 bits em um sistema operacional de 64 bits.
2. Execute este comando para instalar o MSI na VM do host.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Isso instalará as equipes em arquivos de programas ou arquivos de programas (x86). Na próxima vez que você entrar e iniciar as equipes, o aplicativo solicitará suas credenciais.

      > [!NOTE]
      > Os usuários e administradores não podem desabilitar o lançamento automático para equipes durante a entrada no momento.

      Para desinstalar o MSI da VM do host, execute este comando:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Se você instalar equipes com a configuração de MSI usuário = 1, as atualizações automáticas serão desabilitadas. Recomendamos que você atualize as equipes pelo menos uma vez por mês. Para saber mais sobre como implantar o aplicativo de área de trabalho das equipes, confira [implantar o aplicativo de área de trabalho das equipes na VM](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizar propriedades de protocolo RDP para um pool de hosts
Personalizar as propriedades de protocolo RDP (RDP) de um pool de hosts, como a experiência de vários monitores, habilitar o redirecionamento de microfone e áudio, permite que você forneça uma experiência ideal para seus usuários com base em suas necessidades. Você pode personalizar as propriedades de RDP na área de trabalho virtual do Windows usando o parâmetro **-CustomRdpProperty** no cmdlet **set-RdsHostPool** .
Consulte [configurações de arquivo RDP com suporte](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa das propriedades com suporte e seus valores padrão.
