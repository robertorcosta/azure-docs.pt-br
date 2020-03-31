---
title: Instale o Office em uma imagem VHD mestre - Azure
description: Como instalar e personalizar o Office em uma imagem mestra do Windows Virtual Desktop para o Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127842"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalar o Office em uma imagem de VHD mestre

Este artigo diz como instalar o Office 365 ProPlus, OneDrive e outros aplicativos comuns em uma imagem master virtual de disco rígido (VHD) para upload no Azure. Se seus usuários precisarem acessar determinados aplicativos de linha de negócios (LOB), recomendamos instalá-los após o preenchimento das instruções neste artigo.

Este artigo pressupõe que você já criou uma máquina virtual (VM). Caso assim, consulte [Preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md#create-a-vm)

Este artigo também pressupõe que você tenha acesso elevado na VM, seja provisionado no Azure ou no Hyper-V Manager. Caso assim, consulte [Elevar o acesso para gerenciar todos os grupos de assinatura e gerenciamento do Azure](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Essas instruções são referentes a uma configuração específica da Área de Trabalho Virtual do Windows que pode ser usada com os processos existentes de sua organização.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instale o Office no modo de ativação compartilhada do computador

A ativação compartilhada do computador permite que você implante o Office 365 ProPlus em um computador em sua organização que é acessado por vários usuários. Para obter mais informações sobre ativação compartilhada do computador, consulte [Visão geral da ativação compartilhada do computador para o Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

Use a [ferramenta de implantação do escritório](https://www.microsoft.com/download/details.aspx?id=49117) para instalar o Office. O Windows 10 Enterprise multi-session só suporta as seguintes versões do Office:
- Office 365 ProPlus
- Office 365 Business que vem com uma assinatura Microsoft 365 Business

A ferramenta de implantação do escritório requer uma configuração de arquivo XML. Para personalizar a seguinte amostra, consulte as [opções de configuração para a ferramenta de implantação do escritório](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Esta configuração de amostra XML que fornecemos fará as seguintes coisas:

- Instale o Office a partir do canal mensal e entregue atualizações do canal mensal quando forem executadas.
- Use a arquitetura x64.
- Desativar atualizações automáticas.
- Remova todas as instalações existentes do Office e migre suas configurações.
- Habilite a ativação compartilhada do computador.

>[!NOTE]
>O recurso de pesquisa de estêncil do Visio pode não funcionar como esperado no Windows Virtual Desktop.

Aqui está o que esta configuração de amostra XML não vai fazer:

- Instale o Skype para negócios
- Instale o OneDrive no modo por usuário. Para saber mais, consulte [Instalar o OneDrive no modo por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>A ativação compartilhada do computador pode ser configurada através de GPOs (Group Policy Objects, objetos de diretiva de grupo) ou configurações de registro. O GPO está localizado em **\\Configurações\\de configuração\\de computador\\Modelos administrativos Configurações de licenciamento do Microsoft Office 2016 (Máquina)**

A ferramenta de implantação do escritório contém setup.exe. Para instalar o Office, execute o seguinte comando em uma linha de comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Configuração da amostra.xml

A seguinte amostra XML instalará a versão mensal.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>A equipe do Office recomenda o uso da instalação de 64 bits para o parâmetro **OfficeClientEdition.**

Depois de instalar o Office, você pode atualizar o comportamento padrão do Office. Execute os seguintes comandos individualmente ou em um arquivo em lote para atualizar o comportamento.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Instale o OneDrive no modo por máquina

O OneDrive é normalmente instalado por usuário. Neste ambiente, deve ser instalado por máquina.

Veja como instalar o OneDrive no modo por máquina:

1. Primeiro, crie um local para encenar o instalador oneDrive. Uma pasta de\\\\disco local ou um local [unc] (file://unc) está bem.

2. Baixe OneDriveSetup.exe para sua localização em estágio com este link:<https://aka.ms/OneDriveWVD-Installer>

3. Se você instalou o escritório com o OneDrive omitindo ** \<\>ExcluApp ID="OneDrive" /**, desinstale quaisquer instalações existentes do OneDrive por usuário a partir de um prompt de comando elevado, executando o seguinte comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Execute este comando a partir de um prompt de comando elevado para definir o valor de registro **AllUsersInstall:**

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Execute este comando para instalar o OneDrive no modo por máquina:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Execute este comando para configurar o OneDrive para iniciar o login para todos os usuários:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. **Habilite configurar silenciosamente a conta do usuário** executando o seguinte comando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecione e mova pastas conhecidas do Windows para o OneDrive executando o seguinte comando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Equipes e Skype

O Windows Virtual Desktop não suporta skype para negócios e equipes.

## <a name="next-steps"></a>Próximas etapas

Agora que você adicionou o Office à imagem, você pode continuar a personalizar sua imagem VHD mestre. Consulte [Preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md).
