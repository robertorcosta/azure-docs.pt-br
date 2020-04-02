---
title: Guia para configurar uma máquina de modelo do Windows | Microsoft Docs
description: Etapas genéricas para preparar uma máquina de modelo do Windows em Serviços de Laboratório.  Essas etapas incluem a definição do cronograma do Windows Update, a instalação do OneDrive e a instalação do Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521181"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guia para configurar uma máquina de modelo do Windows no Azure Lab Services

Se você está configurando uma máquina de modelo do Windows 10 para o Azure Lab Services, aqui estão algumas práticas recomendadas e dicas a considerar. As etapas de configuração abaixo são todas opcionais.  No entanto, essas etapas preparatórias podem ajudar a tornar seus alunos mais produtivos, minimizar interrupções no tempo de aula e garantir que eles estejam usando as mais recentes tecnologias.

>[!IMPORTANT]
>Este artigo contém trechos do PowerShell para agilizar o processo de modificação do modelo da máquina.  Para todos os scripts PowerShell mostrados, você vai querer executá-los no Windows PowerShell com privilégios de administrador. No Windows 10, uma maneira rápida de fazer isso é clicar com o botão direito do mouse no Menu Iniciar e escolher o "Windows PowerShell (Admin)".

## <a name="install-and-configure-onedrive"></a>Instale e configure o OneDrive

Para proteger que os dados dos alunos sejam perdidos se uma máquina virtual for redefinida, recomendamos que os alunos devolvam seus dados para a nuvem.  O Microsoft OneDrive pode ajudar os alunos a proteger seus dados.  

### <a name="install-onedrive"></a>Instale o OneDrive

Para baixar e instalar manualmente o OneDrive, consulte as páginas de download do [OneDrive](https://onedrive.live.com/about/download/) ou [OneDrive for Business.](https://onedrive.live.com/about/business/)

Você também pode usar o seguinte script PowerShell.  Ele fará o download automático e instalará a versão mais recente do OneDrive.  Uma vez instalado o cliente OneDrive, execute o instalador.  Em nosso exemplo, `/allUsers` usamos o switch para instalar o OneDrive para todos os usuários da máquina. Também usamos `/silent` o interruptor para instalar silenciosamente o OneDrive.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Personalizações do OneDrive

Existem muitas [personalizações que podem ser feitas no OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Vamos cobrir algumas das personalizações mais comuns.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Mova silenciosamente pastas conhecidas do Windows para o OneDrive

Pastas como Documentos, Downloads e Imagens são frequentemente usadas para armazenar arquivos de estudantes. Para garantir que essas pastas sejam copiadas para o OneDrive, recomendamos que você mova essas pastas para o OneDrive.

Se você estiver em uma máquina que não está usando o Active Directory, os usuários podem mover manualmente essas pastas para o OneDrive assim que autenticarem no OneDrive.

1. Abrir o Explorador de Arquivos
2. Clique com o botão direito do mouse na pasta Documentos, Downloads ou Imagens.
3. Vá para Propriedades > Localização.  Mova a pasta para uma nova pasta no diretório OneDrive.

Se a máquina virtual estiver conectada ao Active Directory, você poderá definir a máquina de modelos para solicitar automaticamente aos alunos que movam as pastas conhecidas para o OneDrive.  

Você vai precisar recuperar o seu Escritório Inquilino ID primeiro.  Para obter mais instruções, [consulte o seu Office 365 ID de inquilino](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  Você também pode obter o ID do Inquilino do Office 365 usando o seguinte PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Depois de ter seu ID de inquilino do Office 365, defina o OneDrive para solicitar a movimentação de pastas conhecidas para o OneDrive usando o seguinte PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Use arquivos OneDrive sob demanda

Os alunos podem ter muitos arquivos dentro de suas contas do OneDrive. Para ajudar a economizar espaço na máquina e reduzir o tempo de download, recomendamos que todos os arquivos armazenados na conta OneDrive do aluno sejam on-demand.  Os arquivos sob demanda só são baixados quando um usuário acessa o arquivo.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Faça login silenciosamente nos usuários do OneDrive

O OneDrive pode ser configurado para fazer login automaticamente com as credenciais do Windows do usuário logado.  O login automático é útil para aulas onde o aluno entra com suas credenciais escolares Office 365.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Desativar o tutorial que aparece no final da configuração do OneDrive

Esta configuração permite que você impeça o tutorial de ser lançado em um navegador da Web no final da configuração do OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Defina o tamanho máximo de um arquivo que será baixado automaticamente

Esta configuração é usada em conjunto com os usuários de login silencioso para o cliente de sincronização OneDrive com suas credenciais do Windows em dispositivos que não têm arquivos OneDrive on-demand ativados. Qualquer usuário que tenha um OneDrive maior que o limite especificado (em MB) será solicitado a escolher as pastas que deseja sincronizar antes que o cliente de sincronização OneDrive (OneDrive.exe) baixe os arquivos.  Em nosso exemplo, "1111-2222-3333-4444" é o Office 365 ID de inquilino e 0005000 estabelece um limite de 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Instale e configure o Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Instale o Microsoft Office 365

Se a máquina de modelo precisar do Office, recomendamos a instalação do Office através da Ferramenta de Implantação do [Escritório (ODT).](https://www.microsoft.com/download/details.aspx?id=49117 ) Você precisará criar um arquivo de configuração reutilizável usando o [Serviço de Configuração do Cliente do Office 365](https://config.office.com/) para escolher qual arquitetura, quais recursos você precisará do Office e com que frequência ele atualiza.

1. Vá para [o Serviço de Configuração do Cliente do Office 365](https://config.office.com/) e baixe seu próprio arquivo de configuração.
2. Baixar [a ferramenta de implantação do escritório](https://www.microsoft.com/download/details.aspx?id=49117).  Arquivo baixado `setup.exe`será .
3. Execute `setup.exe /download configuration.xml` para baixar os componentes do Office.
4. Execute `setup.exe /configure configuration.xml` para instalar componentes do Office.

### <a name="change-the-microsoft-office-365-update-channel"></a>Alterar o canal de atualização do Microsoft Office 365

Usando a Ferramenta de Configuração do Escritório, você pode definir com que frequência o Office recebe atualizações. No entanto, se você precisar modificar a frequência com que o Office recebe atualizações após a instalação, você pode alterar a URL do canal de atualização. Atualizar endereços de URL do canal pode ser encontrado no [Canal de atualização Do Office 365 ProPlus para dispositivos em sua organização](https://docs.microsoft.com/deployoffice/change-update-channels). O exemplo abaixo mostra como definir o Office 365 para usar o Canal de Atualização Mensal.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instale e configure atualizações

### <a name="install-the-latest-windows-updates"></a>Instale as atualizações mais recentes do Windows

Recomendamos que você instale as últimas atualizações da Microsoft na máquina de modelos para fins de segurança antes de publicar o modelo VM.  Ele também evita que os alunos sejam interrompidos em seu trabalho quando as atualizações são executadas em momentos inesperados.

1. **Configurações** de lançamento do menu iniciar
2. Clique em **Atualizar** & Segurança
3. Clique **em Verificar para atualizações**
4. As atualizações serão baixadas e instaladas.

Você também pode usar o PowerShell para atualizar a máquina de modelos.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Algumas atualizações podem exigir que a máquina seja reiniciada.  Você será solicitado se uma reinicialização for necessária.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Instale as últimas atualizações para aplicativos da Microsoft Store

Recomendamos que todos os aplicativos da Microsoft Store sejam atualizados para suas versões mais recentes.  Aqui estão instruções para atualizar manualmente os aplicativos da Microsoft Store.  

1. Inicie o aplicativo **Microsoft Store.**
2. Clique na elipse (...) ao lado da foto do usuário no canto superior do aplicativo.
3. Selecione **Baixar** e atualizar no menu suspenso.
4. Clique no botão **Obter atualização.**

Você também pode usar o PowerShell para atualizar os aplicativos da Microsoft Store que já estão instalados.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Parar atualizações automáticas do Windows

Depois de atualizar o Windows para a versão mais recente, você pode considerar parar as atualizações do Windows.  Atualizações automáticas podem interferir potencialmente com o tempo de aula programado.  Se o seu curso for mais longo, considere pedir aos alunos que verifiquem manualmente se há atualizações ou decomode atualizações automáticas por um tempo fora do horário de aula programado.  Para obter mais informações sobre as opções de personalização do Windows Update, consulte as [configurações adicionais](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)do Windows Update .

As atualizações automáticas do Windows podem ser interrompidas usando o seguinte script PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instale pacotes de língua estrangeira

Se você precisar de idiomas adicionais instalados na máquina virtual, você pode adicioná-los através da Microsoft Store.

1. Lançar a Microsoft Store
2. Procure por "pacote de idiomas"
3. Escolha o idioma para instalar

Se você já estiver conectado ao modelo VM, use o [atalho "Instalar pacote de idiomas"](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) para ir diretamente à página de configurações apropriada.

## <a name="remove-unneeded-built-in-apps"></a>Remover aplicativos embutidos desnecessários

O Windows 10 vem com muitos aplicativos embutidos que podem não ser necessários para sua classe particular. Para simplificar a imagem da máquina para os alunos, você pode querer desinstalar alguns aplicativos da sua máquina de modelo.  Para ver uma lista de aplicativos instalados, use o cmdlet PowerShell. `Get-AppxPackage`  O exemplo abaixo mostra todos os aplicativos instalados que podem ser removidos.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Para remover um aplicativo, use o cmdlet Remove-Appx.  O exemplo abaixo mostra como remover tudo relacionado ao XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instale aplicativos comuns relacionados ao ensino

Instale outros aplicativos comumente usados para ensinar através do aplicativo Windows Store. As sugestões incluem aplicativos como [o aplicativo Microsoft Whiteboard,](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)e Minecraft [Education Edition](https://education.minecraft.net/). Esses aplicativos devem ser instalados manualmente através da Windows Store ou através de seus respectivos sites no modelo VM.

## <a name="conclusion"></a>Conclusão

Este artigo mostrou etapas opcionais para preparar seu modelo VM do Windows para uma classe eficaz.  As etapas incluem instalar o OneDrive e instalar o Office 365, instalar as atualizações para o Windows e instalar atualizações para aplicativos da Microsoft Store.  Também discutimos como definir atualizações para um cronograma que funciona melhor para sua classe.  

## <a name="next-steps"></a>Próximas etapas
Veja o artigo sobre como controlar o comportamento de desligamento do Windows para ajudar no gerenciamento de custos: [Guia para controlar o comportamento de desligamento do Windows](how-to-windows-shutdown.md)
