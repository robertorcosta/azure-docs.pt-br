---
title: Implantar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como implantar a Sincronização de arquivos do Azure do início ao fim.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268061"
---
# <a name="deploy-azure-file-sync"></a>Implantar a Sincronização de Arquivos do Azure
Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

É altamente recomendável que você leia [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md) e [Planejando uma implantação de Sincronização de arquivos do Azure](storage-sync-files-planning.md) antes de completar as etapas descritas neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
* Um compartilhamento de arquivo Azure na mesma região que você deseja implantar o Azure File Sync. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#azure-file-sync-region-availability) para Sincronização de arquivos do Azure.
    - Para orientações passo a passo sobre como criar uma conta de compartilhamento, consulte [Criar uma conta de compartilhamento](storage-how-to-create-file-share.md).
* Pelo menos uma instância suportada do windows server ou do cluster Windows Server para sincronizar com o Azure File Sync. Para obter mais informações sobre versões suportadas do Windows Server, consulte [Interoperabilidade com o Windows Server](storage-sync-files-planning.md#windows-file-server-considerations).
* O módulo Az PowerShell pode ser usado com powershell 5.1 ou PowerShell 6+. Você pode usar o módulo Az PowerShell para Azure File Sync em qualquer sistema suportado, incluindo sistemas não-Windows, no entanto, o cmdlet de registro do servidor deve ser sempre executado na instância do Windows Server que você está registrando (isso pode ser feito diretamente ou via PowerShell remoting). No Windows Server 2012 R2, você pode verificar se está executando pelo menos o PowerShell 5.1. \* olhando para o valor da propriedade **PSVersion** do objeto **$PSVersionTable:**

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se o valor de PSVersion for menor que 5.1. \*, como será o caso com a maioria das novas instalações do Windows Server 2012 R2, será possível facilmente fazer upgrade, baixando e instalando o [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para baixar e instalar para o Windows Server 2012 R2 é **\*\*\*\*\*\*\*win8.1AndW2K12R2-KB -x64.msu**. 

    O PowerShell 6+ pode ser usado com qualquer sistema suportado e pode ser baixado através de sua [página do GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se você planeja usar a ui de registro de servidor, em vez de se registrar diretamente no PowerShell, você deve usar o PowerShell 5.1.

* Se você optou por usar o PowerShell 5.1, certifique-se de que pelo menos .NET 4.7.2 esteja instalado. Saiba mais sobre [as versões e dependências do .NET Framework](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) no seu sistema.

    > [!Important]  
    > Se você estiver instalando o .NET 4.7.2+ no `quiet` Windows `norestart` Server Core, você deve instalar com os e sinalizadores ou a instalação falhará. Por exemplo, se instalar o .NET 4.8, o comando se pareceria com o seguinte:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* O módulo Az PowerShell, que pode ser instalado seguindo as instruções aqui: [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > O módulo Az.StorageSync agora é instalado automaticamente quando você instala o módulo Az PowerShell.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar Servidores Windows para uso com Sincronização de arquivos do Azure
Para cada servidor que você pretende usar com a Sincronização de Arquivos do Azure, incluindo cada nó de servidor em um Cluster de Failover, desabilite a **Configuração de Segurança Reforçada do Internet Explorer**. Isso é necessário apenas para o registro inicial do servidor. Você pode habilitá-la novamente depois que o servidor foi registrado.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Você pode pular esta etapa se estiver implantando o Azure File Sync no Windows Server Core.

1. Abra o Gerenciador de Servidor.
2. Clique em **Servidor Local**:  
    !["Servidor Local" no lado esquerdo da interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. No subpainel **Propriedades**, selecione o link de **Configuração de Segurança Reforçada do IE**.  
    ![O painel "Configuração de Segurança Reforçada do IE" na interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Na caixa de diálogo **Configuração de Segurança Reforçada do Internet Explorer**, selecione **Desativado** para **Administradores** e **Usuários**:  
    ![A janela pop-up Configuração de Segurança Reforçada do Internet Explorer com "Desativado" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Para desabilitar a Configuração de Segurança Reforçada do Internet Explorer, execute o seguinte em uma sessão do PowerShell elevada:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Implantar o Serviço de Sincronização de Armazenamento 
A implantação da Sincronização de Arquivos do Azure começa com a colocação de um recurso do **Serviço de Sincronização de Armazenamento** em um grupo de recursos da assinatura selecionada. É recomendável provisionar alguns desse, conforme necessário. Você criará uma relação de confiança entre os servidores e esse recurso e um servidor somente poderá ser registrado em um Serviço de Sincronização de Armazenamento. Como resultado, é recomendável implantar quantos serviços de sincronização de armazenamento forem necessários para separar grupos de servidores. Tenha em mente que os servidores de diferentes serviços de sincronização de armazenamento não podem sincronizar entre si.

> [!Note]
> O Serviço de Sincronização de Armazenamento herda permissões de acesso do grupo de assinatura e recursos em que foi implantado. É recomendável verificar cuidadosamente quem tem acesso a ele. Entidades com acesso de gravação podem começar a sincronizar novos conjuntos de arquivos de servidores registrados nesse serviço de sincronização de armazenamento e fazer com que os dados fluam para o armazenamento do Azure que está acessível para elas.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para implantar um Serviço de Sincronização de Armazenamento, vá para o [portal Azure,](https://portal.azure.com/)clique em *Criar um recurso* e, em seguida, procure o Azure File Sync. Nos resultados da pesquisa, selecione **Azure File Sync**e selecione **Criar** para abrir a guia **Implantar sincronização de armazenamento.**

No novo painel que será aberta, insira as seguintes informações:

- **Nome**: um nome exclusivo (por assinatura) para o Serviço de Sincronização de Armazenamento.
- **Assinatura**: A assinatura na qual você quer criar o Serviço de Sincronização de Armazenamento. Dependendo da estratégia de configuração da sua empresa, você pode ter acesso a uma ou mais assinaturas. Uma Assinatura do Azure é o contêiner mais básico de cobrança para cada serviço de nuvem (como Arquivos do Azure).
- **Grupo de recursos**: um grupo de recursos é um grupo lógico de recursos do Azure, como uma conta de armazenamento ou um Serviço de Sincronização de Armazenamento. Você pode criar um novo grupo de recursos ou usar um grupo de recursos existente para o Azure File Sync. (Recomendamos o uso de grupos de recursos como contêineres para isolar recursos logicamente para sua organização, como agrupar recursos de RH ou recursos para um projeto específico.)
- **Localização**: A região em que você deseja implantar o Azure File Sync. Apenas regiões suportadas estão disponíveis nesta lista.

Quando terminar, selecione **Criar** para implantar o Serviço De Sincronização De Armazenamento.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Substituir `<Az_Region>` `<RG_Name>`, `<my_storage_sync_service>` e com seus próprios valores, em seguida, usar os seguintes comandos para criar e implantar um Serviço de Sincronização de Armazenamento:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de Sincronização de Arquivo do Azure
O agente de Sincronização de arquivos do Azure é um pacote baixável que permite que o Windows Server seja sincronizado com um compartilhamento de arquivos do Azure. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Você pode baixar o agente do [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Após fazer o download, clique duas vezes no pacote MSI para iniciar a instalação do agente de Sincronização de arquivos do Azure.

> [!Important]  
> Se você pretende usar a Sincronização de arquivos do Azure com um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisa ser instalado em cada nó no cluster. Cada nó no cluster deve ser registrado para trabalhar com o Azure File Sync.

Recomendamos que você faça o seguinte:
- Deixe o caminho de instalação padrão (C:\Program Files\Azure\StorageSyncAgent), para simplificar a manutenção do servidor e solução de problemas.
- Habilite o Microsoft Update para manter a Sincronização de arquivos do Azure atualizada. Todas as atualizações, incluindo hotfixes e atualizações de recursos, para o agente de Sincronização de arquivos do Azure, ocorrerão por meio do Microsoft Update. Recomendamos a instalação da última atualização do Azure File Sync. Para obter mais informações, consulte [a diretiva de atualização do Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente de Sincronização de arquivos do Azure tiver acabado, a interface do usuário de Registro do Servidor abrirá automaticamente. Para fazer o registro é necessário que haja um Serviço de Sincronização de Armazenamento. Veja como criar um Serviço de Sincronização de Armazenamento na próxima seção.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Execute o código a seguir do PowerShell para baixar a versão apropriada do agente da Sincronização de Arquivos do Azure para o sistema operacional e instalar no sistema.

> [!Important]  
> Se você pretende usar a Sincronização de arquivos do Azure com um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisa ser instalado em cada nó no cluster. Cada nó no cluster devem ser registrados para trabalhar com a sincronização de arquivos do Azure.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrar o Windows Server com o Serviço de Sincronização de Armazenamento
Registrar o Windows Server com um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Um servidor somente pode ser registrado em um Serviço de Sincronização de Armazenamento e sincronizar com outros servidores e compartilhamentos de arquivos do Azure associados ao mesmo Serviço de Sincronização de Armazenamento.

> [!Note]
> O registro do servidor usa suas credenciais do Azure para criar uma relação de confiança entre o Serviço de Sincronização de Armazenamento e o Windows Server, mas o servidor cria e usa sua própria identidade, desde que o servidor permaneça registrado e o token de assinatura de acesso compartilhado (Armazenamento SAS) é válido. Um novo token de SAS não poderá ser emitido para o servidor depois que o servidor for cancelado, removendo, assim, a capacidade do servidor de acessar os compartilhamentos de arquivos do Azure, interrompendo qualquer sincronização.

# <a name="portal"></a>[Portal](#tab/azure-portal)
A interface do usuário de Registro do Servidor deve abrir automaticamente após a instalação do agente de Sincronização de arquivos do Azure. Se não estiver, você pode abri-lo manualmente de seu local de arquivo: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quando a interface do usuário de Registro do Servidor abrir, clique em **Entrar** para começar.

Depois de entrar, você deverá fornecer as seguintes informações:

![Uma captura de tela da interface do usuário de Registro do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Assinatura do Azure**: a assinatura que contém o Serviço de Sincronização de Armazenamento (consulte [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service)). 
- **Grupo de recursos**: o grupo de recursos que contém o Serviço de Sincronização de Armazenamento.
- **Serviço de Sincronização de Armazenamento**: o nome do Serviço de Sincronização de Armazenamento com o qual você deseja registrar.

Depois de selecionar as informações apropriadas nos menus suspensos, clique em **Registrar** para concluir o registro do servidor. Como parte do processo de registro, você será solicitado a realizar uma entrada adicional.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Criar um grupo de sincronização e um ponto de extremidade de nuvem
Um grupo de sincronização define a topologia de sincronização para um conjunto de arquivos. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Um grupo de sincronização precisa conter um ponto de extremidade de nuvem que represente um compartilhamento de arquivos do Azure e um ou mais pontos de extremidade do servidor. Um ponto final do servidor representa um caminho em um servidor registrado. Um servidor pode ter pontos de extremidade do servidor em vários grupos de sincronização. É necessário criar tantos grupos de sincronização quantos você precisar para descrever adequadamente sua topologia de sincronização desejada.

Um ponto de extremidade de nuvem é um ponteiro para um compartilhamento de arquivos do Azure. Todos os pontos de extremidade do servidor serão sincronizados com um ponto de extremidade de nuvem, tornando o ponto de extremidade de nuvem o hub. A conta de armazenamento do compartilhamento de arquivos do Azure deve estar localizada na mesma região que o Serviço de Sincronização de Armazenamento. A totalidade do compartilhamento de arquivos do Azure será sincronizada, com uma exceção: uma pasta especial, comparável à pasta "Informações de Volume do Sistema" oculta em um volume NTFS, será provisionada. Este diretório é chamado ".SystemShareInformation". Ele contém importantes metadados de sincronização que não serão sincronizados com outros pontos de extremidade. Não utilize nem exclua-o!

> [!Important]  
> Você pode fazer alterações a qualquer Ponto de extremidade de Nuvem ou de Servidor no Grupo de sincronização e ter seus arquivos sincronizados com os outros pontos de extremidade no Grupo de sincronização. Se você fizer uma alteração diretamente no Ponto de extremidade de nuvem (compartilhamento de Arquivos do Azure), observe que as alterações devem primeiro ser descobertas por um trabalho de detecção de alteração de sincronização de arquivos do Azure. Um trabalho de detecção de alteração é iniciado para um ponto de extremidade da nuvem apenas uma vez a cada 24 horas. Para obter mais informações, consulte [Perguntas frequentes sobre os Arquivos do Azure](storage-files-faq.md#afs-change-detection).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar um grupo de sincronização, no [portal Azure,](https://portal.azure.com/)vá para o serviço de sincronização de armazenamento e selecione **+ Grupo Sincronização**:

![Criar um novo Grupo de Sincronização no Portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel que abrir, insira as informações a seguir para criar um Grupo de Sincronização com um ponto de extremidade de nuvem:

- **Nome do grupo de sincronização**: O nome do grupo de sincronização a ser criado. Esse nome deve ser exclusivo dentro do Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome lógico para você.
- **Assinatura**: a assinatura na qual você implantou o Serviço de Sincronização de Armazenamento em [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: se você selecionar **Selecionar conta de armazenamento**, outro painel aparece no qual você pode selecionar a conta de armazenamento que tenha o compartilhamento de arquivos do Azure que você deseja fazer a sincronização.
- **Compartilhamento de Arquivo do Azure**: o nome do Compartilhamento de Arquivo do Azure com o qual você deseja sincronizar.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Para criar o grupo de sincronização, execute o PowerShell a seguir. Lembre-se de substituir `<my-sync-group>` com o nome desejado do grupo de sincronização.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Depois que o grupo de sincronização tiver sido criado com êxito, será possível criar o ponto de extremidade de nuvem. Certifique-se de substituir `<my-storage-account>` e `<my-file-share>` pelos valores esperados.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Criar um ponto de extremidade do servidor
Um ponto de extremidade do servidor representa um local específico em um servidor registrado, como uma pasta em um volume do servidor. Um ponto de extremidade do servidor deve ser um caminho em um servidor registrado (em vez de um compartilhamento montado) e, para usar a classificação em camadas de nuvem, o caminho deve estar em um volume que não seja do sistema. Não há suporte para Armazenamento NAS (Network Attached Storage).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para adicionar um ponto final do servidor, vá para o grupo de sincronização recém-criado e, em seguida, selecione **Adicionar ponto final do servidor**.

![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No **painel adicionar ponto final** do servidor, digite as seguintes informações para criar um ponto final do servidor:

- **Servidor registrado**: O nome do servidor ou cluster onde você deseja criar o ponto final do servidor.
- **Caminho**: O caminho do Servidor Windows a ser sincronizado como parte do grupo de sincronização.
- **Nuvem em camadas**: uma opção para habilitar ou desabilitar a nuvem camadas. Com nuvem em camadas, arquivos raramente usados ou acessados de nuvem podem ter várias camada para arquivos do Azure.
- **Espaço Livre**de Volume : A quantidade de espaço livre para reservar no volume em que o ponto final do servidor está localizado. Por exemplo, se o espaço livre do volume estiver definido como 50% em um volume com um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será disposta em camadas para os Arquivos do Azure. Independentemente de as camadas na nuvem estarem habilitadas, o Compartilhamento de Arquivos do Azure sempre terá uma cópia completa dos dados no Grupo de Sincronização.

Para adicionar o ponto final do servidor, selecione **Criar**. Os arquivos agora serão mantidos em sincronia entre o Compartilhamento de Arquivos do Azure e o Windows Server. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Execute os comandos a seguir do PowerShell para criar o ponto de extremidade do servidor e substitua `<your-server-endpoint-path>` e `<your-volume-free-space>` pelos valores desejados.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Configurar configurações de firewall e rede virtual

### <a name="portal"></a>Portal
Se você quiser configurar sua sincronização de arquivos Do Azure para funcionar com configurações de firewall e rede virtual, faça o seguinte:

1. A partir do portal Azure, navegue até a conta de armazenamento que deseja proteger.
1. Selecione o botão **Firewalls e redes virtuais** no menu esquerdo.
1. Selecione **redes selecionadas** em **Permitir acesso a partir de**.
1. Certifique-se de que o IP ou a rede virtual dos servidores esteja listado na seção apropriada.
1. Certifique-se **de que permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento.**
1. Selecione **Salvar** para salvar suas configurações.

![Configurando configurações de firewall e rede virtual para trabalhar com a sincronização do Arquivo Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Integração com a Sincronização de arquivos do Azure
As etapas recomendadas para se integrar à Sincronização de arquivos do Azure pela primeira vez com zero tempo de inatividade e ainda preservar a fidelidade do arquivo completo e a lista de controle de acesso (ACL) são as seguintes:
 
1. Implantar um Serviço de sincronização de armazenamento.
2. Criar um grupo de sincronização.
3. Instalar o agente de Sincronização de arquivos do Azure no servidor com o conjunto de dados completo.
4. Registrar esse servidor e criar um ponto de extremidade do servidor no compartilhamento. 
5. Permitir que a sincronização faça o upload completo ao compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).  
6. Após a conclusão do upload inicial, instalar o agente de Sincronização de arquivos do Azure em cada um dos servidores restantes.
7. Criar novos compartilhamentos de arquivo em cada um dos servidores restantes.
8. Criar pontos de extremidade do servidor em novos compartilhamentos de arquivo com a política de camadas de nuvem, se desejado. (Essa etapa requer que haja armazenamento adicional disponível para a configuração inicial.)
9. Deixe o agente Azure File Sync fazer uma restauração rápida do namespace completo sem a transferência real de dados. Após a sincronização completa de namespace, o mecanismo de sincronização preencherá o espaço de disco local com base na política de camadas de nuvem do ponto de extremidade do servidor. 
10. Certifique-se de que a sincronização seja concluída e teste a sua topologia conforme desejado. 
11. Redirecionar usuários e aplicativos para esse novo compartilhamento.
12. Outra opção é excluir compartilhamentos duplicados nos servidores.
 
Caso não tenha armazenamento extra para a integração inicial e gostaria de anexar aos compartilhamentos existentes, você pode pré-propagar os dados nos compartilhamentos de arquivos do Azure. Essa abordagem é sugerida se e somente se você puder aceitar o tempo de inatividade e não garante absolutamente nenhuma alteração de dados em compartilhamentos do servidor durante o processo de migração inicial. 
 
1. Certifique-se de que os dados em qualquer um dos servidores não podem ser alterar durante o processo de onboarding.
2. O arquivo Azure pré-semente compartilha com os dados do servidor usando qualquer ferramenta de transferência de dados sobre o SMB, por exemplo, Robocopy, cópia direta do SMB. Como o AzCopy não baixa dados pelo SMB, ele não pode ser usado para pré-propagação.
3. Crie a topologia da Sincronização de arquivos do Azure com os pontos de extremidade desejados do servidor apontando para os compartilhamentos existentes.
4. Deixe a sincronização concluir o processo de reconciliação em todos os pontos de extremidade. 
5. Assim que a reconciliação for concluída, você pode abrir compartilhamentos para alterações.
 
Atualmente, a abordagem pré-semeada tem algumas limitações - 
- A fidelidade total nos arquivos não é preservada. Por exemplo, arquivos perdem ACLs e carimbo de data/hora.
- Alterações de dados no servidor antes de a topologia de sincronização estar totalmente ativa e em execução podem causar conflitos nos pontos de extremidade do servidor.  
- Depois que o ponto final da nuvem é criado, o Azure File Sync executa um processo para detectar os arquivos na nuvem antes de iniciar a sincronização inicial. O tempo necessário para concluir esse processo varia dependendo dos vários fatores, como velocidade da rede, largura de banda disponível e número de arquivos e pastas. Para obter uma estimativa aproximada na versão prévia, o processo de detecção é executado a aproximadamente 10 arquivos por segundo. Portanto, mesmo se a pré-propagação for executada rapidamente, o tempo geral para obter um sistema totalmente em execução pode ser significativamente maior quando os dados forem pré-propagados na nuvem.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Restauração de autoatendimento através de versões anteriores e VSS (Volume Shadow Copy Service)

> [!IMPORTANT]
> As informações a seguir só podem ser usadas com a versão 9 (ou acima) do agente de sincronização de armazenamento. Versões inferiores a 9 não terão os cmdlets StorageSyncSelfService.

Versões anteriores é um recurso do Windows que permite utilizar instantâneos VSS do lado do servidor de um volume para apresentar versões restauráveis de um arquivo a um cliente SMB.
Isso permite um cenário poderoso, comumente referido como restauração de autoatendimento, diretamente para os trabalhadores de informações, em vez de depender da restauração de um admin de TI.

Os instantâneos VSS e as versões anteriores funcionam independentemente do Azure File Sync. No entanto, a hierarquida de nuvem deve ser definida como um modo compatível. Muitos pontos finais do servidor Azure File Sync podem existir no mesmo volume. Você tem que fazer a seguinte chamada powershell por volume que tenha até mesmo um ponto final de servidor onde você planeja ou está usando hierarquing na nuvem.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Os instantâneos VSS são tirados de um volume inteiro. Por padrão, até 64 instantâneos podem existir para um determinado volume, concedido que há espaço suficiente para armazenar os instantâneos. O VSS lida com isso automaticamente. O cronograma padrão de instantâneos leva dois instantâneos por dia, de segunda a sexta-feira. Esse cronograma é configurável através de uma tarefa agendada para windows. O cmdlet PowerShell acima faz duas coisas:
1. Ele configura o hierarquiserimento em nuvem do Azure File Syncs no volume especificado para ser compatível com as versões anteriores e garante que um arquivo possa ser restaurado a partir de uma versão anterior, mesmo que tenha sido hierárquico na nuvem no servidor. 
2. Ele habilita o cronograma PADRÃO DO VSS. Em seguida, você pode decidir modificá-lo mais tarde. 

> [!Note]  
> Há dois aspectos importantes a serem observados:
>- Se você usar o parâmetro -Force e o VSS estiver habilitado no momento, ele substituirá o cronograma de instantâneos VSS atual e irá substituí-lo pelo cronograma padrão. Certifique-se de salvar sua configuração personalizada antes de executar o cmdlet.
> - Se você estiver usando este cmdlet em um nó de cluster, você também deve executá-lo em todos os outros nós no cluster! 

Para ver se a compatibilidade de restauração de autoatendimento está ativada, você pode executar o cmdlet a seguir.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Ele listará todos os volumes no servidor, bem como o número de dias compatíveis de hierarquica na nuvem para cada um. Esse número é calculado automaticamente com base no máximo de instantâneos possíveis por volume e no cronograma padrão de instantâneos. Assim, por padrão, todas as versões anteriores apresentadas a um trabalhador de informações podem ser usadas para restaurar. O mesmo é verdade se você alterar o cronograma padrão para tirar mais instantâneos.
No entanto, se você alterar o cronograma de uma forma que resultará em um instantâneo disponível no volume mais antigo do que o valor dos dias compatíveis, os usuários não poderão usar este snapshot mais antigo (versão anterior) para restaurar a partir de.

> [!Note]
> A ativação da restauração de autoatendimento pode ter um impacto no consumo e na conta de armazenamento do Azure. Esse impacto é limitado a arquivos atualmente hierárquicos no servidor. A ativação desse recurso garante que haja uma versão de arquivo disponível na nuvem que possa ser referenciada através de uma entrada de versões anteriores (snapshot VSS).
>
> Se você desativar o recurso, o consumo de armazenamento do Azure diminuirá lentamente até que a janela de dias compatíveis tenha passado. Não há como acelerar isso. 

O número máximo padrão de instantâneos VSS por volume (64) bem como o cronograma padrão para tomá-los, resulta em um máximo de 45 dias de versões anteriores que um trabalhador de informações pode restaurar, dependendo de quantos instantâneos VSS você pode armazenar em seu volume.

Se max. 64 instantâneos VSS por volume não é a configuração correta para você, você pode [alterar esse valor através de uma chave de registro](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Para que o novo limite entre em vigor, você precisa executar novamente o cmdlet para permitir a compatibilidade da versão anterior em todos os volumes que foi anteriormente habilitado, com o sinalizador -Force para levar em conta o novo número máximo de instantâneos VSS por volume. Isso resultará em um número recém-calculado de dias compatíveis. Por favor, note que essa alteração só terá efeito em arquivos recém-hierárquicos e sobreporá quaisquer personalizações no cronograma VSS que você possa ter feito.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrar uma implantação de replicação do DFS (DFS-R) para sincronização de arquivos do Azure
Para migrar uma implantação de DFS-R para sincronização de arquivos do Azure:

1. Crie um grupo de sincronização para representar a topologia de DFS-R que você estiver substituindo.
2. Inicialize no servidor que tiver o conjunto completo de dados em sua topologia de DFS-R para migrar. Instale a sincronização de arquivos do Azure nesse servidor.
3. Registre o servidor e crie um ponto de extremidade do servidor para o primeiro servidor a ser migrado. Não habilite a camada de nuvem.
4. Permita que todos os dados se sincronizem ao seu compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).
5. Instale e registre o agente de sincronização de arquivos do Azure em cada um dos servidores restantes do DFS-R.
6. Desabilite o DFS-R. 
7. Crie um ponto de extremidade do servidor em cada um dos servidores do DFS-R. Não habilite a camada de nuvem.
8. Certifique-se de que a sincronização seja concluída e teste a sua topologia conforme desejado.
9. Desative o DFS-R.
10. Agora, a camada de nuvem pode ser ativada em qualquer ponto de extremidade do servidor, conforme desejado.

Para obter mais informações, consulte [Interoperabilidade de sincronização de arquivos do Azure com o sistema de arquivos distribuído (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Próximas etapas
- [Adicionar/remover um ponto de extremidade do Servidor de Sincronização de arquivos do Azure](storage-sync-files-server-endpoint.md)
- [Registrar/cancelar o registro de um servidor com a Sincronização de arquivos do Azure](storage-sync-files-server-registration.md)
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
