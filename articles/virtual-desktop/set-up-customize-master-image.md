---
title: Preparar e personalizar uma imagem VHD mestre - Azure
description: Como preparar, personalizar e carregar uma imagem mestra do Windows Virtual Desktop para o Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127735"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparar e personalizar uma imagem de VHD mestre

Este artigo diz como preparar uma imagem master virtual de disco rígido (VHD) para upload no Azure, incluindo como criar máquinas virtuais (VMs) e instalar software nelas. Essas instruções são referentes a uma configuração específica da Área de Trabalho Virtual do Windows que pode ser usada com os processos existentes de sua organização.

## <a name="create-a-vm"></a>Criar uma máquina virtual

A multisessão Windows 10 Enterprise está disponível na Galeria de Imagens do Azure. Existem duas opções para personalizar esta imagem.

A primeira opção é provisionar uma máquina virtual (VM) no Azure seguindo as instruções em [Criar uma VM a partir de uma imagem gerenciada](../virtual-machines/windows/create-vm-generalized-managed.md)e, em seguida, pular para a [preparação e instalação do Software](set-up-customize-master-image.md#software-preparation-and-installation).

A segunda opção é criar a imagem localmente baixando a imagem, provisionando um Hyper-V VM e personalizando-a para atender às suas necessidades, que cobrimos na seção a seguir.

### <a name="local-image-creation"></a>Criação de imagens locais

Depois de baixar a imagem para um local local, abra o **Hyper-V Manager** para criar uma VM com o VHD copiado. As instruções a seguir são uma versão simples, mas você pode encontrar instruções mais detalhadas em [Criar uma máquina virtual em Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Para criar uma VM com o VHD copiado:

1. Abra o **Novo Assistente de Máquina Virtual**.

2. Na página Especificar geração, selecione **Geração 1**.

    ![Uma captura de tela da página Especificar geração. A opção "Geração 1" está selecionada.](media/a41174fd41302a181e46385e1e701975.png)

3. Em Tipo de Verificação, desabilite os pontos de verificação desverificando a caixa de seleção.

    ![Uma captura de tela da seção Tipo de ponto de verificação da página Pontos de verificação.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Você também pode executar o cmdlet a seguir no PowerShell para desativar pontos de verificação.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fixo

Se você criar uma VM a partir de um VHD existente, ele criará um disco dinâmico por padrão. Ele pode ser alterado para um disco fixo selecionando **Editar disco...** como mostrado na imagem a seguir. Para obter instruções mais detalhadas, consulte [Prepare um Windows VHD ou VHDX para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

![Uma captura de tela da opção Editar disco.](media/35772414b5a0f81f06f54065561d1414.png)

Você também pode executar o seguinte cmdlet PowerShell para alterar o disco para um disco fixo.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Preparação e instalação de software

Esta seção abrange como preparar e instalar o FSLogix e o Windows Defender, bem como algumas opções básicas de configuração para aplicativos e registro de sua imagem. 

Se você estiver instalando o Office 365 ProPlus e o OneDrive em sua VM, vá ao [Install Office em uma imagem VHD mestre](install-office-on-wvd-master-image.md) e siga as instruções para instalar os aplicativos. Depois que terminar, volte para este artigo.

Se seus usuários precisarem acessar certos aplicativos LOB, recomendamos instalá-los após completar as instruções desta seção.

### <a name="set-up-user-profile-container-fslogix"></a>Configurar o contêiner de perfil do usuário (FSLogix)

Para incluir o contêiner FSLogix como parte da imagem, siga as instruções em [Criar um recipiente de perfil para um pool de host usando um compartilhamento de arquivos](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Você pode testar a funcionalidade do contêiner FSLogix com [este quickstart](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Configurar o Windows Defender

Se o Windows Defender estiver configurado na VM, certifique-se de que ele está configurado para não escanear todo o conteúdo dos arquivos VHD e VHDX durante o anexo.

Esta configuração apenas remove a digitalização de arquivos VHD e VHDX durante o anexo, mas não afetará a digitalização em tempo real.

Para obter instruções mais detalhadas sobre como configurar o Windows Defender no Windows Server, consulte [Configure as exclusões do Windows Defender Antivirus no Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Para saber mais sobre como configurar o Windows Defender para excluir certos arquivos da digitalização, consulte [Configurar e validar exclusões com base na extensão de arquivos e na localização da pasta](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Desativar atualizações automáticas

Para desativar atualizações automáticas via Política de Grupo local:

1. Abrir **modelos\\administrativos\\do editor\\de políticas de grupo local Windows Components Windows Update**.
2. Clique com o botão direito do mouse **Configurar atualização automática** e configurá-la como **Desativada**.

Você também pode executar o seguinte comando em um prompt de comando para desativar atualizações automáticas.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Especificar o layout Iniciar para PCs do Windows 10 (opcional)

Execute este comando para especificar um layout Iniciar para PCs do Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurar o redirecionamento do fuso horário

O redirecionamento do fuso horário pode ser aplicado no nível de Diretiva de Grupo, uma vez que todas as VMs em um pool de host fazem parte do mesmo grupo de segurança.

Para redirecionar os fusos horários:

1. No servidor Active Directory, abra o **Console de Gerenciamento de Políticas de Grupo**.
2. Expanda seu domínio e objetos de política de grupo.
3. Clique com o botão direito do mouse no **objeto de diretiva de grupo** criado para as configurações de diretiva de grupo e selecione **Editar**.
4. No **Group Policy Management Editor**, navegue até**políticas** >  **de configuração** > de computador**Modelos administrativos** > **Quais componentes do Windows Componentes Remotas** > **Desktop Services** > **Remote Desktop Session Host** > **Device e Resource Redirection**.
5. Habilite a configuração **de redirecionamento do fuso horário** allow.

Você também pode executar este comando na imagem principal para redirecionar fusos horários:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Desativar o senso de armazenamento

Para o windows virtual desktop host que usa windows 10 Enterprise ou Windows 10 Enterprise multi-sessão, recomendamos desativar o Storage Sense. Você pode desativar o Storage Sense no menu Configurações em **Armazenamento,** conforme mostrado na captura de tela a seguir:

![Uma captura de tela do menu Armazenamento em Configurações. A opção "Senso de armazenamento" está desatada.](media/storagesense.png)

Você também pode alterar a configuração com o registro executando o seguinte comando:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Inclua suporte adicional ao idioma

Este artigo não abrange como configurar linguagem e suporte regional. Para obter mais informações, consulte os seguintes artigos:

- [Adicionar idiomas às imagens do Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Características demanda](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Características de linguagem e região demanda (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Outros aplicativos e configuração de registro

Esta seção abrange a configuração do aplicativo e do sistema operacional. Toda a configuração nesta seção é feita através de entradas de registro que podem ser executadas por ferramentas de linha de comando e regedit.

>[!NOTE]
>Você pode implementar práticas recomendadas na configuração com GPOs (Group Policy Objects, objetos de política de grupo) ou importações de registro. O administrador pode escolher qualquer opção com base nos requisitos de sua organização.

Para obter a coleta de dados de telemetria no Windows 10 Enterprise multi-session, execute este comando:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Execute o seguinte comando para corrigir falhas do Watson:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Digite os seguintes comandos no editor de registro para corrigir o suporte à resolução de 5k. Você deve executar os comandos antes de habilitar a pilha lado a lado.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Prepare a imagem para upload para o Azure

Depois de terminar a configuração e instalar todos os aplicativos, siga as instruções em [Prepare um Windows VHD ou VHDX para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) para preparar a imagem.

Depois de preparar a imagem para upload, certifique-se de que a VM permanece no estado desligado ou desalocado.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Faça upload de imagem mestra para uma conta de armazenamento no Azure

Esta seção só se aplica quando a imagem principal foi criada localmente.

As instruções a seguir lhe dirão como carregar sua imagem principal em uma conta de armazenamento do Azure. Se você ainda não tiver uma conta de armazenamento Azure, siga as instruções [deste artigo](/azure/javascript/tutorial-vscode-static-website-node-03) para criar uma.

1. Converta a imagem VM (VHD) em Fixada se você ainda não tiver. Se você não converter a imagem em Fixação, você não poderá criar a imagem com sucesso.

2. Carregue o VHD em um recipiente blob em sua conta de armazenamento. Você pode carregar rapidamente com a [ferramenta Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Para saber mais sobre a ferramenta Storage Explorer, consulte [este artigo](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Uma captura de tela da janela de pesquisa do Microsoft Azure Storage Explorer Tool. A caixa de seleção "Upload .vhd ou vhdx como blobs de página (recomendado)" é selecionada.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Em seguida, vá para o portal Azure em seu navegador e procure por "Imagens". Sua pesquisa deve levá-lo à página **Criar imagem,** conforme mostrado na captura de tela a seguir:

    ![Uma captura de tela da página Criar imagem do portal Azure, preenchida com valores de exemplo para a imagem.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Depois de criar a imagem, você deve ver uma notificação como a da seguinte captura de tela:

    ![Uma captura de tela da notificação "imagem criada com sucesso".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma imagem, você pode criar ou atualizar pools de host. Para saber mais sobre como criar e atualizar pools de host, consulte os seguintes artigos:

- [Criar um pool de host com um modelo do Azure Resource Manager](create-host-pools-arm-template.md)
- [Tutorial: Crie um pool de hosts com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)
- [Criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivo](create-host-pools-user-profile.md)
- [Configurar o método de balanceamento de carga da Área de Trabalho Virtual do Windows](configure-host-pool-load-balancing.md)
