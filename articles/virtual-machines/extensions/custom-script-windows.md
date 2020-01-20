---
title: Extensão de script personalizado do Azure para Windows
description: Automatizar tarefas de configuração de VM do Windows usando a Extensão de Script Personalizado
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 058099ceca886f375e6add07033174bf80d5b647
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156532"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para o Windows

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de pós-implantação, instalação de software ou quaisquer outras tarefas de configuração ou gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. A extensão de script personalizado se integra com modelos de Azure Resource Manager e pode ser executada usando o CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Este documento detalha como usar a Extensão de Script Personalizado usando o módulo do Azure PowerShell e modelos do Azure Resource Manager, além de detalhar as etapas da solução de problemas em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Não use a Extensão de Script Personalizado para executar Update-AzVM com a mesma VM como seu parâmetro, pois ela aguardará por si própria.  

### <a name="operating-system"></a>Sistema Operacional

A extensão de script personalizado para Windows será executada no OSs de extensão com suporte da extensão, para obter mais informações, consulte os [sistemas operacionais com suporte da extensão do Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Local do script

Você pode configurar a extensão para usar suas credenciais de armazenamento de BLOBs do Azure para acessar o armazenamento de BLOBs do Azure. O local do script pode estar em qualquer lugar, desde que a VM possa ser roteada para esse ponto de extremidade, como o GitHub ou um servidor de arquivos interno.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se você precisar baixar um script externamente, como do GitHub ou do armazenamento do Azure, as portas de grupo de segurança de rede e firewall adicionais precisam ser abertas. Por exemplo, se o seu script estiver localizado no armazenamento do Azure, você poderá permitir o acesso usando as marcas do serviço NSG do Azure para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o seu script estiver em um servidor local, talvez você ainda precise que as portas de grupo de segurança de rede e firewall adicionais precisem ser abertas.

### <a name="tips-and-tricks"></a>dicas e truques

* A taxa de falha mais alta para essa extensão é devido a erros de sintaxe no script, teste o script é executado sem erros e também Coloque em log adicional no script para facilitar a localização de onde ele falhou.
* Grave scripts que são idempotentes. Isso garante que, se eles forem executados novamente acidentalmente, não causarão alterações no sistema.
* Assegure-se de que os scripts não exigirão a entrada do usuário quando forem executados.
* É permitido que o script seja executado em até 90 minutos e um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações dentro do script, pois essa ação causará problemas com outras extensões que estão sendo instaladas. Após a reinicialização, a extensão não continuará depois de reiniciar.
* Se você tiver um script que causará uma reinicialização, instale aplicativos e execute scripts, você poderá agendar a reinicialização usando uma tarefa agendada do Windows ou usar ferramentas como as extensões DSC, chefe ou Puppet.
* A extensão executará um script somente uma vez. Se você quiser executar um script em cada inicialização, use a extensão pra criar uma Tarefa Agendada do Windows.
* Se você quiser agendar quando um script será executado, use a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script for executado, você só verá um status da extensão 'em transição' no portal do Azure ou no CLI. Se quiser atualizações de status mais frequentes de um script em execução, será necessário criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo para servidores proxy. No entanto, é possível usar uma ferramenta de transferência de arquivos que dá suporte a servidores proxy no script, como a *Curl*
* Esteja ciente dos locais de diretório não padrão nos quais os scripts ou comandos podem confiar e mantenha uma lógica para lidar com essa situação.
* A extensão de script personalizado será executada na conta LocalSystem

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo do Azure Resource Manager.

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> a propriedade managedIdentity **não deve** ser usada em conjunto com as propriedades StorageAccountName ou storageAccountKey

> [!NOTE]
> Somente uma versão de uma extensão pode ser instalada em uma VM em um ponto no tempo, especificar o script personalizado duas vezes no mesmo modelo do Resource Manager para a mesma VM falhará.

> [!NOTE]
> Podemos usar esse esquema dentro do recurso VirtualMachine ou como um recurso autônomo. O nome do recurso deve estar nesse formato "virtualMachineName/ExtensionName", se essa extensão for usada como um recurso autônomo no modelo ARM. 

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | cadeia de caracteres |
| type | CustomScriptExtension | cadeia de caracteres |
| typeHandlerVersion | 1,10 | int |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matriz |
| carimbo de data/hora (exemplo) | 123456789 | Inteiro de 32 bits |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | cadeia de caracteres |
| storageAccountName (por exemplo) | examplestorageacct | cadeia de caracteres |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | cadeia de caracteres |
| managedIdentity (por exemplo,) | {} ou {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} ou {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | objeto JSON |

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

#### <a name="property-value-details"></a>Detalhes de valor de propriedade

* `commandToExecute`: (**necessária**, cadeia de caracteres) o script de ponto de entrada a ser executado. Use esse campo se o comando contiver segredos, como senhas, ou se os fileUris diferenciarem maiúsculas de minúsculas.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs dos arquivos a serem baixados.
* `timestamp` (opcional, inteiro de 32 bits) use esse campo apenas para disparar uma nova execução do script, alterando o valor desse campo.  Qualquer valor inteiro é aceitável. Só deve ser diferente do valor anterior.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` deverão ser URLs para Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto JSON) a [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para baixar arquivo (s)
  * `clientId`: (opcional, Cadeia de caracteres) a ID do cliente da identidade gerenciada
  * `objectId`: (opcional, Cadeia de caracteres) a ID de objeto da identidade gerenciada

Os valores a seguir podem ser definidos nas configurações públicas ou protegidas. A extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos tanto nas configurações protegidas quanto nas públicas.

* `commandToExecute`

O uso de configurações públicas talvez seja útil para depuração, mas é recomendável que você use configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM conforme elas foram enviadas, ou seja, se as configurações foram criptografadas, elas são salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no runtime.

####  <a name="property-managedidentity"></a>Propriedade: managedIdentity

O CustomScript (versão 1,10 em diante) dá suporte à [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para baixar arquivo (s) de URLs fornecidas na configuração "fileuris". Ele permite que o CustomScript acesse BLOBs ou contêineres privados do armazenamento do Azure sem que o usuário precise passar segredos como tokens SAS ou chaves de conta de armazenamento.

Para usar esse recurso, o usuário deve adicionar uma identidade atribuída pelo [usuário](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-user-assigned-identity) ou com o [sistema](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-system-assigned-identity) à VM ou VMSS em que se espera que CustomScript seja executado e [conceder acesso de identidade gerenciada ao contêiner ou BLOB de armazenamento do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Para usar a identidade atribuída pelo sistema na VM/VMSS de destino, defina o campo "managedidentity" como um objeto JSON vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Para usar a identidade atribuída pelo usuário na VM/VMSS de destino, configure o campo "managedidentity" com a ID do cliente ou a ID de objeto da identidade gerenciada.

> Exemplos:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> a propriedade managedIdentity **não deve** ser usada em conjunto com as propriedades StorageAccountName ou storageAccountKey

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON, que é detalhado na seção anterior, pode ser usado em um modelo de Azure Resource Manager para executar a extensão de script personalizado durante a implantação. Os exemplos a seguir mostram como usar a extensão de Script personalizado:

* [Tutorial: implantar extensões de máquina virtual com modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Implante o aplicativo de duas camadas no Windows e no banco de dados SQL do Azure](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzVMCustomScriptExtension` pode ser usado para adicionar a Extensão de Script Personalizado a uma máquina virtual existente. Para obter mais informações, confira [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Mais exemplos

### <a name="using-multiple-scripts"></a>Usando vários scripts

Neste exemplo, você tem três scripts que são usados para criar seu servidor. O **commandToExecute** chama o primeiro script e, em seguida, você tem opções sobre como os outros são chamados. Por exemplo, você pode ter um script mestre que controla a execução, com o tratamento de erros, o registro em log e o gerenciamento de estado corretos. Os scripts são baixados no computador local para execução. Por exemplo, no `1_Add_Tools.ps1` você chamaria `2_Add_Features.ps1` adicionando `.\2_Add_Features.ps1` ao script e repetiria esse processo para os outros scripts definidos no `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Executando scripts de um compartilhamento local

Neste exemplo, talvez você queira usar um servidor SMB local para o local do script. Ao fazer isso, você não precisa fornecer outras configurações, exceto **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar o script personalizado mais de uma vez com a CLI

Se você quiser executar a extensão do script personalizado mais de uma vez, poderá executar essa ação somente sob estas condições:

* O parâmetro de **nome** da extensão é o mesmo que a implantação anterior da extensão.
* Atualize a configuração, caso contrário, o comando não será executado novamente. É possível adicionar uma propriedade dinâmica ao comando, como um carimbo de data/hora.

Como alternativa, você pode definir a propriedade [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) como **true**.

### <a name="using-invoke-webrequest"></a>Usando Invoke-WebRequest

Se você estiver usando [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) em seu script, será necessário especificar o parâmetro `-UseBasicParsing` ou, caso contrário, receberá o seguinte erro ao verificar o status detalhado:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>VMs clássicas

Para implantar a extensão de script personalizado em VMs clássicas, você pode usar o portal do Azure ou os cmdlets Azure PowerShell clássicos.

### <a name="azure-portal"></a>Portal do Azure

Navegue até o recurso da VM clássica. Selecione **extensões** em **configurações**.

Clique em **+ Adicionar** e, na lista de recursos, escolha **extensão de script personalizado**.

Na página **instalar extensão** , selecione o arquivo local do PowerShell e preencha os argumentos e clique em **OK**.

### <a name="powershell"></a>PowerShell

Use o cmdlet [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) pode ser usado para adicionar a extensão de script personalizado a uma máquina virtual existente.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o módulo do Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A saída da extensão é registrada em arquivos encontrados na seguinte pasta na máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados para a seguinte pasta na máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

em que `<n>` é um inteiro decimal que pode ser alterado entre as execuções da extensão.  O valor `1.*` corresponde ao valor `typeHandlerVersion` atual e real da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o comando `commandToExecute`, a extensão definirá esse diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Esse processo permite o uso de caminhos relativos para localizar os arquivos baixados por meio da propriedade `fileURIs`. Veja a tabela abaixo para obter exemplos.

Como o caminho absoluto do download pode variar ao longo do tempo, é melhor optar por caminhos de arquivo/script relativos na cadeia de caracteres `commandToExecute`, sempre que possível. Por exemplo:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

As informações de caminho após o primeiro segmento de URI são mantidas para os arquivos baixados por meio da lista de propriedades `fileUris`.  Conforme mostrado na tabela a seguir, os arquivos baixados são mapeados em subdiretórios de download para refletir a estrutura dos valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Arquivos Baixados

| URI no fileUris | Localização baixada relativa | Local baixado absoluto <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> os caminhos de diretório absolutos são alterados durante o tempo de vida da VM, mas não dentro de uma única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Você também pode registrar um incidente de Suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
