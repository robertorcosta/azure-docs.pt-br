---
title: Aplicativo Windows Virtual Desktop MSIX attach - Azure
description: Como configurar o aplicativo MSIX para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128327"
---
# <a name="set-up-msix-app-attach"></a>Configurar anexação de aplicativo MSIX

> [!IMPORTANT]
> O aplicativo MSIX está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tópico irá mostrar como configurar o aplicativo MSIX anexado em um ambiente de Desktop Virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar, veja o que você precisa para configurar o aplicativo MSIX:

- Acesso ao portal Windows Insider para obter a versão do Windows 10 com suporte para o aplicativo MSIX anexar APIs.
- Uma implantação de desktop virtual do Windows em funcionamento. Para obter informações, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- A ferramenta de embalagem MSIX
- Um compartilhamento de rede na implantação do Windows Virtual Desktop onde o pacote MSIX será armazenado

## <a name="get-the-os-image"></a>Obter a imagem do sistema operacional

Primeiro, você precisa obter a imagem do sistema operacional que você vai usar para o aplicativo MSIX. Para obter a imagem do sistema operacional:

1. Abra o [portal Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) e faça login.

     >[!NOTE]
     >Você deve ser membro do programa Windows Insider para acessar o portal Windows Insider. Para saber mais sobre o programa Windows Insider, confira nossa [documentação do Windows Insider](/windows-insider/at-home/).

2. Desça até a seção **Selecionar edição** e selecione **Windows 10 Insider Preview Enterprise (FAST) – Build 19035** ou posterior.

3. Selecione **Confirmar,** em seguida, selecione o idioma que deseja usar e, em seguida, **selecione Confirmar** novamente.
    
     >[!NOTE]
     >No momento, o inglês é o único idioma que foi testado com o recurso. Você pode selecionar outros idiomas, mas eles podem não exibir como pretendido.
    
4. Quando o link de download for gerado, selecione o **Download de 64 bits e salve-o** no disco rígido local.

## <a name="prepare-the-vhd-image-for-azure"></a>Prepare a imagem VHD para OZure 

Antes de começar, você precisará criar uma imagem VHD mestre. Se você ainda não criou sua imagem VHD mestre, vá para [Preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md) e siga as instruções lá. 

Depois de criar sua imagem VHD mestre, você deve desativar atualizações automáticas para aplicativos de conexão de aplicativos MSIX. Para desativar as atualizações automáticas, você precisará executar os seguintes comandos em um prompt de comando elevado:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Em seguida, prepare o VM VHD para OZure e carregue o disco VHD resultante para o Azure. Para saber mais, consulte [Prepare e personalize uma imagem VHD mestre.](set-up-customize-master-image.md)

Depois de carregar o VHD para o Azure, crie um pool de host baseado nesta nova imagem seguindo as instruções no Pool Criar um host usando o tutorial [do Azure Marketplace.](create-host-pools-azure-marketplace.md)

## <a name="prepare-the-application-for-msix-app-attach"></a>Prepare o aplicativo para anexar aplicativo MSIX 

Se você já tiver um pacote MSIX, pule para configurar a [infra-estrutura de desktop virtual do Windows](#configure-windows-virtual-desktop-infrastructure). Se você quiser testar aplicativos legados, siga as instruções em [Criar um pacote MSIX de um instalador de desktop em uma VM](/windows/msix/packaging-tool/create-app-package-msi-vm/) para converter o aplicativo legado em um pacote MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Gerar um pacote VHD ou VHDX para MSIX

Os pacotes estão em formato VHD ou VHDX para otimizar o desempenho. O MSIX requer que os pacotes VHD ou VHDX funcionem corretamente.

Para gerar um pacote VHD ou VHDX para MSIX:

1. [Baixe a ferramenta msixmgr](https://aka.ms/msixmgr) e salve a pasta .zip em uma pasta dentro de uma VM host de sessão.

2. Descompacte a ferramenta msixmgr .zip folder.

3. Coloque o pacote MSIX de origem na mesma pasta onde você descompactou a ferramenta msixmgr.

4. Execute o cmdlet a seguir no PowerShell para criar um VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Certifique-se de que o tamanho do VHD é grande o suficiente para segurar o MSIX expandido.*

5. Execute o cmdlet a seguir para montar o VHD recém-criado:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Execute este cmdlet para inicializar o VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Execute este cmdlet para criar uma nova partição:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Execute este cmdlet para formatar a partição:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Crie uma pasta pai no VHD montado. Esta etapa é obrigatória, pois o anexo do aplicativo MSIX requer uma pasta pai. Você pode nomear a pasta dos pais o que quiser.

### <a name="expand-msix"></a>Expandir MSIX

Depois disso, você precisará "expandir" a imagem MSIX desempacotando-a. Para desempacotar a imagem MSIX:

1. Abra um prompt de comando como Administrador e navegue até a pasta onde você baixou e descompactou a ferramenta msixmgr.

2. Execute o cmdlet a seguir para desempacotar o MSIX no VHD que você criou e montou na seção anterior.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A seguinte mensagem deve aparecer assim que a desempacotação estiver pronta:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Se usar pacotes da Microsoft Store for Business (ou Education) dentro de sua rede ou em dispositivos que não estão conectados à internet, você precisará obter as licenças de pacotes da Loja e instalá-las para executar o aplicativo com sucesso. Consulte [Usar pacotes offline](#use-packages-offline).

3. Navegue até o VHD montado e abra a pasta do aplicativo e confirme que o conteúdo do pacote está presente.

4. Desmonte o VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Configurar a infra-estrutura de desktop virtual do Windows

Pelo design, um único pacote expandido MSIX (o VHD que você criou na seção anterior) pode ser compartilhado entre vMs de host de sessão múltipla à medida que os VHDs são anexados no modo somente leitura.

Antes de começar, certifique-se de que o compartilhamento da rede atenda a esses requisitos:

- A ação é compatível com SMB.
- As VMs que fazem parte do pool de host de sessão têm permissões NTFS para a ação.

### <a name="set-up-an-msix-app-attach-share"></a>Configure um compartilhamento de anexo de aplicativo MSIX 

No ambiente windows virtual desktop, crie um compartilhamento de rede e mova o pacote para lá.

>[!NOTE]
> A melhor prática para criar compartilhamentos de rede MSIX é configurar o compartilhamento de rede com permissões somente de leitura NTFS.

## <a name="install-certificates"></a>Instalar certificados

Se o seu aplicativo usa um certificado que não é de confiança pública ou foi auto-assinado, veja como instalá-lo:

1. Clique com o botão direito do mouse no pacote e selecione **Propriedades**.
2. Na janela em que aparece, selecione a guia **Assinaturas Digitais.** Deve haver apenas um item na lista na guia, como mostrado na imagem a seguir. Selecione esse item para destacar o item e selecione **Detalhes**.
3. Quando a janela de detalhes de assinatura digital for exibida, selecione a guia **Geral** e, em seguida, **selecione 'Instalar'.**
4. Quando o instalador for aberto, selecione **a máquina local** como local de armazenamento e selecione **Next**.
5. Se o instalador perguntar se deseja permitir que o aplicativo faça alterações no seu dispositivo, selecione **Sim**.
6. Selecione **Colocar todos os certificados na seguinte loja**e, em seguida, **selecione Procurar**.
7. Quando a janela selecionar a loja de certificados for exibida, **selecione Pessoas confiáveis**e selecione **OK**.
8. Selecione **Concluir**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Prepare os scripts do PowerShell para anexar o aplicativo MSIX

O aplicativo msix tem quatro fases distintas que devem ser executadas na seguinte ordem:

1. Estágio
2. Registrar 
3. Cancelar
4. Destage

Cada fase cria um script PowerShell. Os scripts de amostra para cada fase estão disponíveis [aqui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Encenar o script PowerShell

Antes de atualizar os scripts do PowerShell, certifique-se de ter o guid de volume do volume no VHD. Para obter o volume GUID:

1.  Abra o compartilhamento de rede onde o VHD está localizado dentro da VM onde você executará o script.

2.  Clique com o botão direito do mouse no VHD e selecione **Mount**. Isso irá montar o VHD em uma letra de unidade.

3.  Depois de montar o VHD, a janela **File Explorer** será aberta. Capture a pasta pai e atualize a variável **$parentFolder**

    >[!NOTE]
    >Se você não ver uma pasta pai, isso significa que o MSIX não foi expandido corretamente. Refaça a seção anterior e tente novamente.

4.  Abra a pasta pai. Se corretamente expandido, você verá uma pasta com o mesmo nome do pacote. Atualize a variável **$packageName** para combinar com o nome desta pasta.

    Por exemplo, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Abra um prompt de comando e digite **mountvol**. Este comando exibirá uma lista de volumes e seus GUIDs. Copie o GUID do volume onde a letra de unidade corresponde à unidade que você montou seu VHD na etapa 2.

    Por exemplo, nesta saída de exemplo para o comando mountvol, se você montou seu VHD `C:\`para unidade C, você vai querer copiar o valor acima:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Atualize a variável **$volumeGuid** com o volume GUID que você acabou de copiar.

7. Abra um prompt powershell do Admin e atualize o seguinte script PowerShell com as variáveis que se aplicam ao seu ambiente.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Registre o script powershell

Para executar o script de registro, execute os seguintes cmdlets powershell com os valores de espaço reservado substituídos por valores que se aplicam ao seu ambiente.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Desregistre o script powershell

Para este script, substitua o espaço reservado por **$packageName** com o nome do pacote que você está testando.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Destage PowerShell script

Para este script, substitua o espaço reservado por **$packageName** com o nome do pacote que você está testando.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configure scripts de simulação para o agente de conexão do aplicativo MSIX

Depois de criar os scripts, os usuários podem executá-los manualmente ou configurá-los para executá-los automaticamente como scripts de inicialização, logon, logoff e shutdown. Para saber mais sobre esses tipos de scripts, consulte [Usando scripts de inicialização, desligamento, logon e logoff na Diretiva de Grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Cada um desses scripts automáticos executa uma fase dos scripts de anexação do aplicativo:

- O roteiro de inicialização executa o roteiro do palco.
- O script de logon executa o script de registro.
- O script de logoff executa o script de descadastramento.
- O script de desligamento executa o script de desestágio.

## <a name="use-packages-offline"></a>Use pacotes offline

Se você estiver usando pacotes da [Microsoft Store for Business](https://businessstore.microsoft.com/) ou da Microsoft Store for [Education](https://educationstore.microsoft.com/) dentro de sua rede ou em dispositivos que não estão conectados à internet, você precisa obter as licenças de pacotes da Microsoft Store e instalá-las em seu dispositivo para executar o aplicativo com sucesso. Se o seu dispositivo estiver online e puder se conectar à Microsoft Store for Business, as licenças necessárias devem ser baixadas automaticamente, mas se você estiver off-line, precisará configurar as licenças manualmente. 

Para instalar os arquivos de licença, você precisará usar um script PowerShell que chama a classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 no Provedor de Ponte WMI.  

Veja como configurar as licenças para uso offline: 

1. Baixe o pacote do aplicativo, licenças e estruturas necessárias na Microsoft Store for Business. Você precisa dos arquivos de licença codificados e não codificados. Instruções detalhadas de download podem ser encontradas [aqui](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Atualize as seguintes variáveis no script para a etapa 3:
      1. `$contentID`é o valor ContentID do arquivo de licença não codificado (.xml). Você pode abrir o arquivo de licença em um editor de texto de sua escolha.
      2. `$licenseBlob`é toda a string para a bolha de licença no arquivo de licença Codificado (.bin). Você pode abrir o arquivo de licença codificado em um editor de texto de sua escolha. 
3. Execute o seguinte script a partir de um prompt PowerShell do Admin. Um bom lugar para realizar a instalação da licença é no final do script de [encenação](#stage-the-powershell-script) que também precisa ser executado a partir de um prompt de admin.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>Próximas etapas

Este recurso não é suportado no momento, mas você pode fazer perguntas à comunidade no [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Você também pode deixar feedback para o Windows Virtual Desktop no [hub de feedback do Windows Virtual Desktop,](https://aka.ms/MRSFeedbackHub)ou deixar feedback para o aplicativo MSIX e ferramenta de embalagem no aplicativo [MSIX anexar o hub de feedback](https://aka.ms/msixappattachfeedback) e o hub de feedback da ferramenta de embalagem [MSIX](https://aka.ms/msixtoolfeedback).
