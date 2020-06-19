---
title: Anexação do aplicativo MSIX da Área de Trabalho Virtual do Windows – Azure
description: Como configurar a anexação do aplicativo MSIX para a Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a222e5a0602a676872eb8119e565f243f2ecc1b4
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742929"
---
# <a name="set-up-msix-app-attach"></a>Configurar anexação de aplicativo MSIX

> [!IMPORTANT]
> A anexação do aplicativo MSIX está em modo de visualização privada.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tópico explicará como configurar a anexação do aplicativo MSIX em um ambiente de Área de Trabalho Virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar, veja o que você precisa para configurar a anexação do aplicativo MSIX:

- Acesso ao portal do Windows Insider para obter a versão do Windows 10 com suporte para as APIs de anexação do aplicativo MSIX.
- Uma implantação funcional da Área de Trabalho Virtual do Windows. Para saber mais, confira [Criar um locatário na Área de Trabalho Virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- A ferramenta de empacotamento MSIX
- Um compartilhamento de rede em sua implantação de Área de Trabalho Virtual do Windows em que o pacote MSIX será armazenado

## <a name="get-the-os-image"></a>Obter a imagem de SO

Primeiro, você precisa obter a imagem do sistema operacional que usará para o aplicativo MSIX. Para obter a imagem de SO:

1. Abra o [portal do Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) e entre.

     >[!NOTE]
     >Você deve ser membro do programa Windows Insider para acessar o portal do Windows Insider. Para saber mais sobre o programa Windows Insider, confira nossa [documentação do Windows Insider](/windows-insider/at-home/).

2. Role para baixo até a seção **Selecionar edição** e selecione **Windows 10 Insider Preview Enterprise (rápido) – Build 19041** ou posterior.

3. Selecione **Confirmar**, selecione o idioma que deseja usar e, em seguida, selecione **Confirmar** novamente.
    
     >[!NOTE]
     >No momento, o inglês é o único idioma que foi testado com o recurso. Você pode selecionar outros idiomas, mas eles podem não ser exibidos como pretendido.
    
4. Quando o link de download for gerado, selecione o **Download de 64 bits** e salve-o no disco rígido local.

## <a name="prepare-the-vhd-image-for-azure"></a>Preparar imagem VHD do Azure 

Antes de começar, você precisará criar uma imagem VHD mestre. Se você ainda não criou a imagem de VHD mestre, vá até [Preparar e personalizar uma imagem do VHD mestre](set-up-customize-master-image.md) e siga as instruções. 

Depois de criar a imagem de VHD mestre, você deverá desabilitar as atualizações automáticas para aplicativos de anexação do aplicativo MSIX. Para desabilitar as atualizações automáticas, você precisará executar os seguintes comandos em um prompt de comando com privilégios elevados:

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

Depois de desabilitar as atualizações automáticas, você deverá habilitar o Hyper-V porque usará o comando Mount-VHD para preparar e Dismount-VHD para cancelar a preparação. 

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
```
>[!NOTE]
>Essa alteração exigirá que você reinicie a máquina virtual.

Em seguida, prepare o VHD de VM para o Azure e carregue o disco VHD resultante no Azure. Para saber mais, confira [Preparar e personalizar uma imagem de VHD mestre](set-up-customize-master-image.md).

Depois de carregar o VHD no Azure, crie um pool de hosts baseado nessa nova imagem seguindo as instruções no tutorial [Criar um pool de hosts usando o Azure Marketplace](create-host-pools-azure-marketplace.md).

## <a name="prepare-the-application-for-msix-app-attach"></a>Preparar o aplicativo para anexação do aplicativo MSIX 

Se você já tiver um pacote MSIX, pule para [Configurar a infraestrutura de Área de Trabalho Virtual do Windows](#configure-windows-virtual-desktop-infrastructure). Se você quiser testar aplicativos herdados, siga as instruções em [Criar um pacote MSIX de um instalador de área de trabalho em uma VM](/windows/msix/packaging-tool/create-app-package-msi-vm/) para converter o aplicativo herdado em um pacote MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Gerar um pacote VHD ou VHDX para MSIX

Os pacotes estão no formato VHD ou VHDX para otimizar o desempenho. O MSIX requer que os pacotes VHD ou VHDX funcionem corretamente.

Para gerar um pacote VHD ou VHDX para MSIX:

1. [Baixe a ferramenta msixmgr](https://aka.ms/msixmgr) e salve a pasta .zip em uma pasta dentro de uma VM host de sessão.

2. Descompacte a pasta .zip da ferramenta msixmgr.

3. Coloque o pacote MSIX de origem na mesma pasta em que você descompactou a ferramenta msixmgr.

4. Execute o seguinte cmdlet no PowerShell para criar um VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Verifique se o tamanho do VHD é grande o suficiente para manter o MSIX expandido.*

5. Execute o seguinte cmdlet para montar o VHD recém-criado:

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

9. Crie uma pasta pai no VHD montado. Esta etapa é obrigatória, pois a anexação do aplicativo MSIX requer uma pasta pai. Você pode nomear a pasta pai com a sua preferência.

### <a name="expand-msix"></a>Expandir o MSIX

Depois disso, você precisará "expandir" a imagem MSIX desempacotando-a. Para desempacotar a imagem MSIX:

1. Abra um prompt de comando como administrador e navegue até a pasta em que você baixou e descompactou a ferramenta msixmgr.

2. Execute o cmdlet a seguir para descompactar o MSIX no VHD que você criou e montou na seção anterior.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A seguinte mensagem deve aparecer quando o desempacotamento for feito:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Se estiver usando pacotes do Microsoft Store para Empresas (ou Educação) em sua rede ou em dispositivos que não estejam conectados à Internet, você precisará obter as licenças de pacote da loja e instalá-las para executar o aplicativo com êxito. Confira [Usar pacotes offline](#use-packages-offline).

3. Navegue até o VHD montado e abra a pasta do aplicativo e confirme se o conteúdo do pacote está presente.

4. Desmonte o VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Configurar a infraestrutura de Área de Trabalho Virtual do Windows

Por design, um único pacote expandido MSIX (o VHD que você criou na seção anterior) pode ser compartilhado entre várias VMs de host de sessão, pois os VHDs são anexados no modo somente leitura.

Antes de começar, verifique se o compartilhamento de rede atende a estes requisitos:

- O compartilhamento é compatível com o SMB.
- As VMs que fazem parte do pool de hosts de sessão têm permissões NTFS para o compartilhamento.

### <a name="set-up-an-msix-app-attach-share"></a>Configurar um compartilhamento de anexação do aplicativo MSIX 

No seu ambiente de Área de Trabalho Virtual do Windows, crie um compartilhamento de rede e mova o pacote para lá.

>[!NOTE]
> A prática recomendada para a criação de compartilhamentos de rede MSIX é configurar o compartilhamento de rede com permissões de NTFS somente leitura.

## <a name="install-certificates"></a>Instalar certificados

Se seu aplicativo usa um certificado que não é confiável para o público ou foi autoassinado, veja como instalá-lo:

1. Clique com o botão direito do mouse no pacote e selecione **Propriedades**.
2. Na janela exibida, selecione a guia **Assinaturas digitais**. Deve haver apenas um item na lista na guia, conforme mostrado na imagem a seguir. Selecione esse item para realçar o item e, em seguida, selecione **Detalhes**.
3. Quando a janela Detalhes da assinatura digital for exibida, selecione a guia **Geral** e, em seguida, selecione **Instalar certificado**.
4. Quando o instalador for aberto, selecione **Computador local** como o local de armazenamento e, em seguida, selecione **Avançar**.
5. Se o instalador perguntar se você deseja permitir que o aplicativo faça alterações no seu dispositivo, selecione **Sim**.
6. Selecione **Colocar todos os certificados no seguinte repositório** e, em seguida, selecione **Procurar**.
7. Quando a janela Selecionar repositório de certificados for exibida, selecione **Pessoas confiáveis** e, em seguida, selecione **OK**.
8. Selecione **Concluir**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Preparar scripts do PowerShell para anexação do aplicativo MSIX

A anexação do aplicativo MSIX tem quatro fases distintas que devem ser executadas na seguinte ordem:

1. Estágio
2. Registrar
3. Cancelar registro
4. Cancelar preparo

Cada fase cria um script do PowerShell. Os scripts de exemplo para cada fase estão disponíveis [aqui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Preparar o script do PowerShell

Antes de atualizar os scripts do PowerShell, verifique se você tem o GUID do volume no VHD. Para obter o GUID do volume:

1.  Abra o compartilhamento de rede em que o VHD está localizado dentro da VM em que você executará o script.

2.  Clique com o botão direito no VHD e selecione **Montar**. Isso montará o VHD em uma letra da unidade.

3.  Depois de montar o VHD, a janela **Explorador de Arquivos** será aberta. Capturar a pasta pai e atualizar a variável **$parentFolder**

    >[!NOTE]
    >Se você não vir uma pasta pai, isso significará que o MSIX não foi expandido corretamente. Refaça a seção anterior e tente novamente.

4.  Abra a pasta pai. Se tiver sido expandido corretamente, você verá uma pasta com o mesmo nome que o pacote. Atualize a variável **$packageName** para corresponder ao nome desta pasta.

    Por exemplo, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Abra um prompt de comando e digite **mountvol**. Esse comando exibirá uma lista de volumes e seus GUIDs. Copie o GUID do volume em que a letra da unidade corresponde à unidade na qual você montou o VHD na etapa 2.

    Por exemplo, neste exemplo de saída para o comando mountvol, se você montou o VHD para a unidade C, copie o valor acima `C:\`:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Atualize a variável **$volumeGuid** com o GUID do volume que você acabou de copiar.

7. Abra um prompt de administrador do PowerShell e atualize o seguinte script do PowerShell com as variáveis que se aplicam ao seu ambiente.

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

    Mount-VHD -Path $vhdSrc -NoDriveLetter -ReadOnly

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

### <a name="register-powershell-script"></a>Registrar o script do PowerShell

Para executar o script de registro, execute os seguintes cmdlets do PowerShell com os valores de espaço reservado substituídos pelos valores que se aplicam ao seu ambiente.

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

### <a name="deregister-powershell-script"></a>Cancelar o registro do script do PowerShell

Para esse script, substitua o espaço reservado para **$packageName** pelo nome do pacote que você está testando.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Cancelar o preparo do script do PowerShell

Para esse script, substitua o espaço reservado para **$packageName** pelo nome do pacote que você está testando.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurar scripts de simulação para o agente de anexação do aplicativo MSIX

Depois de criar os scripts, os usuários podem executá-los manualmente ou configurá-los para serem executados automaticamente como scripts de inicialização, logon, logoff e desligamento. Para saber mais sobre esses tipos de scripts, confira [Como usar scripts de inicialização, desligamento, logon e logoff na Política de Grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Cada um desses scripts automáticos executa uma fase dos scripts de anexação do aplicativo:

- O script de inicialização executa o script de preparo.
- O script de logon executa o script de registro.
- O script de logoff executa o script de cancelamento de registro.
- O script de desligamento executa o script de cancelamento do preparo.

## <a name="use-packages-offline"></a>Usar pacotes offline

Se estiver usando pacotes do [Microsoft Store para Empresas](https://businessstore.microsoft.com/) ou [Microsoft Store para Educação](https://educationstore.microsoft.com/) em sua rede ou em dispositivos que não estejam conectados à Internet, você precisará obter as licenças de pacote da Microsoft Store e instalá-las no dispositivo para executar o aplicativo com êxito. Se o dispositivo estiver online e puder se conectar ao Microsoft Store para Empresas, as licenças necessárias deverão ser baixadas automaticamente, mas se você estiver offline, precisará configurar as licenças manualmente. 

Para instalar os arquivos de licença, você precisará usar um script do PowerShell que chama a classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 no Provedor de Ponte WMI.  

Veja como configurar as licenças para uso offline: 

1. Baixe o pacote do aplicativo, as licenças e as estruturas necessárias do Microsoft Store para Empresas. Você precisa dos arquivos de licença codificados e não codificados. As instruções detalhadas de download são encontradas [aqui](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Atualize as seguintes variáveis no script para a etapa 3:
      1. `$contentID` é o valor ContentID do arquivo de licença não codificado (.xml). Você pode abrir o arquivo de licença em um editor de texto de sua escolha.
      2. `$licenseBlob` é a cadeia de caracteres inteira para o blob de licença no arquivo de licença codificado (.bin). Você pode abrir o arquivo de licença codificado em um editor de texto de sua escolha. 
3. Execute o script a seguir em um prompt de administrador do PowerShell. Um bom local para executar a instalação da licença é no final do [script de preparo](#stage-the-powershell-script) que também precisa ser executado em um prompt de administrador.

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

Atualmente, não há suporte para esse recurso, mas você pode fazer perguntas para a Comunidade na [TechCommunity da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Você também pode deixar comentários para a Área de Trabalho Virtual do Windows no [Hub de comentários da Área de Trabalho Virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
