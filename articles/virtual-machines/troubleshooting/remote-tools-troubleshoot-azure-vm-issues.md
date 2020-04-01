---
title: Use ferramentas remotas para solucionar problemas do VM do Azure | Microsoft Docs
description: Saiba mais sobre psExec, scripts PowerShell e outras ferramentas remotas que você pode usar para solucionar problemas remotos do Azure VM sem usar RDP.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: d29b2b7c2b9194f20afe4c74d117847f0e343b12
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422599"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Use ferramentas remotas para solucionar problemas do VM do Azure

Quando você soluciona problemas em uma máquina virtual Azure (VM), você pode se conectar à VM usando as ferramentas remotas que são discutidas neste artigo em vez de usar o RDP (Remote Desktop Protocol).

## <a name="serial-console"></a>Console serial

Use um [console serial para Azure Virtual Machines](serial-console-windows.md) para executar comandos na VM remota do Azure.

## <a name="remote-cmd"></a>CMD remoto

Baixe o [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Conecte-se à VM executando o seguinte comando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* O comando deve ser executado em um computador que está na mesma rede virtual.
>* DIP ou HostName podem \<ser usados para substituir> de computador.
>* O parâmetro -s garante que o comando seja chamado usando a conta do sistema (permissão de administrador).
>* PsExec usa portas TCP 135 e 445. Como resultado, as duas portas devem ser abertas no firewall.

## <a name="run-command"></a>Executar comando

Para obter mais informações sobre como usar o recurso de comando executar para executar scripts na VM, consulte [Executar scripts PowerShell em sua VM windows com comando de execução](../windows/run-command.md).

## <a name="custom-script-extension"></a>Extensão de script personalizado

Você pode usar o recurso Custom Script Extension para executar um script personalizado na VM de destino. Para usar esse recurso, as seguintes condições devem ser atendidas:

* A VM tem conectividade.
* O Azure Virtual Machine Agent está instalado e está funcionando como esperado na VM.
* A extensão não foi instalada anteriormente na VM.
 
  A extensão injeta o script apenas na primeira vez que é usado. Se você usar esse recurso mais tarde, a extensão reconhece que ele já foi usado e não faz o upload do novo script.

Carregue seu script para uma conta de armazenamento e gere seu próprio contêiner. Em seguida, execute o seguinte script do PowerShell do Azure em um computador que tenha conectividade com a VM.

### <a name="for-classic-deployment-model-vms"></a>Para vMs modelo de implantação clássica

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Para VMs do Azure Resource Manager

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>PowerShell remoto

>[!NOTE]
>A porta TCP 5986 (HTTPS) deve estar aberta para que você possa usar essa opção.
>
>Para as VMs do Azure Resource Manager, você deve abrir a porta 5986 no grupo de segurança de rede (NSG). Para mais informações, consulte Grupos de segurança. 
>
>Para VMs RDFE, você deve ter um terminal que tenha uma porta privada (5986) e uma porta pública. Então, você também tem que abrir aquela porta voltada para o público no NSG.

### <a name="set-up-the-client-computer"></a>Configurar o computador cliente

Para usar o PowerShell para conectar-se à VM remotamente, primeiro você precisa configurar o computador cliente para permitir a conexão. Para isso, adicione a VM à lista de hosts confiáveis do PowerShell executando o seguinte comando, conforme apropriado.

Para adicionar uma VM à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Para adicionar várias VMs à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Para adicionar todos os computadores à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Habilitar RemotePS na VM

Para VMs criados usando o modelo clássico de implantação, use a Extensão de Script Personalizado para executar o seguinte script:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Para VMs do Azure Resource Manager, use comandos de execução do portal para executar o script EnableRemotePS:

![Executar comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Conectar-se à VM

Execute o seguinte comando com base na localização do computador cliente:

* Fora da rede virtual ou implantação

  * Para uma VM criada usando o modelo clássico de implantação, execute o seguinte comando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Para uma VM do Azure Resource Manager, adicione primeiro um nome DNS ao endereço IP público. Para obter etapas detalhadas, consulte [Criar um nome de domínio totalmente qualificado no portal do Azure para uma VM do Windows](../windows/portal-create-fqdn.md). Em seguida, execute o seguinte comando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Dentro da rede virtual ou implantação, execute o seguinte comando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Definir o sinalizador SkipCaCheck ignora o requisito para importar um certificado para a VM quando você inicia a sessão.

Você também pode usar o cmdlet Invoke-Command para executar um script na VM remotamente.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registro remoto

>[!NOTE]
>A porta TCP 135 ou 445 deve estar aberta para usar esta opção.
>
>Para VMs do Azure Resource Manager, você tem que abrir a porta 5986 no NSG. Para mais informações, consulte Grupos de segurança. 
>
>Para VMs RDFE, você deve ter um terminal que tenha uma porta privada 5986 e uma porta pública. Você também tem que abrir aquela porta voltada para o público no NSG.

1. De outra VM na mesma rede virtual, abra o editor de registro (regedit.exe).

2. Selecione **Registro** > **de rede de conexão de arquivos**.

   ![Editor de registro](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Localize a VM de destino por **nome de host** ou IP **dinâmico** (preferível) inserindo-o na caixa Enter the object **para selecionar** a caixa.

   ![Digite o nome do objeto para selecionar caixa](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Digite as credenciais para a VM de destino.

5. Faça as alterações necessárias no registro.

## <a name="remote-services-console"></a>Console de serviços remotos

>[!NOTE]
>As portas TCP 135 ou 445 devem estar abertas para usar essa opção.
>
>Para VMs do Azure Resource Manager, você tem que abrir a porta 5986 no NSG. Para mais informações, consulte Grupos de segurança. 
>
>Para VMs RDFE, você deve ter um terminal que tenha uma porta privada 5986 e uma porta pública. Você também tem que abrir aquela porta voltada para o público no NSG.

1. De outra VM na mesma rede virtual, abra uma instância de **Services.msc**.

2. Clique com botão direito **serviços (Local)**.

3. Selecione **Conectar-se a outro computador**.

   ![Serviço remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Digite o IP dinâmico da VM de destino.

   ![IP dinâmico de entrada](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Faça as alterações necessárias para os serviços.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o cmdlet Enter-PSSession, consulte [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Para obter mais informações sobre a extensão de script personalizado para Windows usando o modelo de implantação clássico, consulte [Extensão de script personalizado para Windows](../extensions/custom-script-classic.md).
- PsExec é parte do [conjunto PSTools](https://download.sysinternals.com/files/PSTools.zip).
- Para obter mais informações sobre o PSTools Suite, consulte [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


