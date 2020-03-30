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
ms.openlocfilehash: 698fab470cdc8b8d04fa4319fd71c31b58d1c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066885"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para o Windows

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para configuração de pós-implantação, instalação de software ou qualquer outra configuração ou tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. A extensão de script personalizado integra-se aos modelos do Azure Resource Manager e pode ser executada usando o azure CLI, PowerShell, Portal Azure ou a API Azure Virtual Machine REST.

Este documento detalha como usar a Extensão de Script Personalizado usando o módulo do Azure PowerShell e modelos do Azure Resource Manager, além de detalhar as etapas da solução de problemas em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Não use a Extensão de Script Personalizado para executar Update-AzVM com a mesma VM como seu parâmetro, pois ela aguardará por si própria.  

### <a name="operating-system"></a>Sistema operacional

A extensão de script personalizado para Windows será executada na extensão suportada OSs, para obter mais informações, consulte este [azure Extension suportado sistemas operacionais](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Local do script

Você pode configurar a extensão para usar suas credenciais de armazenamento Azure Blob para acessar o armazenamento Do Zure Blob. A localização do script pode estar em qualquer lugar, desde que a VM possa ser direcionada para esse ponto final, como o GitHub ou um servidor de arquivos interno.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se você precisar baixar um script externamente, como do GitHub ou do Azure Storage, então as portas adicionais de firewall e do Network Security Group precisam ser abertas. Por exemplo, se o seu script estiver localizado no Azure Storage, você poderá permitir o acesso usando as tags de serviço do Azure NSG para [armazenamento.](../../virtual-network/security-overview.md#service-tags)

Se o seu script estiver em um servidor local, então você ainda pode precisar de firewall adicional e as portas do Grupo de Segurança de Rede precisam ser abertas.

### <a name="tips-and-tricks"></a>Dicas e truques

* A maior taxa de falha para esta extensão é devido a erros de sintaxe no script, teste as corridas de script sem erro e também coloque login adicional no script para facilitar a busca onde ele falhou.
* Escreva scripts que são idempotentes. Isso garante que, se eles forem executados novamente acidentalmente, não causará alterações no sistema.
* Assegure-se de que os scripts não exigirão a entrada do usuário quando forem executados.
* É permitido que o script seja executado em até 90 minutos e um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações dentro do script, pois essa ação causará problemas com outras extensões que estão sendo instaladas. Após a reinicialização, a extensão não continuará depois de reiniciar.
* Se você tiver um script que causará uma reinicialização, então instale aplicativos e execute scripts, você pode agendar a reinicialização usando uma tarefa programada do Windows ou usar ferramentas como extensões DSC, Chef ou Puppet.
* A extensão executará um script somente uma vez. Se você quiser executar um script em cada inicialização, use a extensão pra criar uma Tarefa Agendada do Windows.
* Se você quiser agendar quando um script será executado, use a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script for executado, você só verá um status da extensão 'em transição' no portal do Azure ou no CLI. Se quiser atualizações de status mais frequentes de um script em execução, será necessário criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo para servidores proxy. No entanto, é possível usar uma ferramenta de transferência de arquivos que dá suporte a servidores proxy no script, como a *Curl*
* Esteja ciente dos locais de diretório não padrão nos quais os scripts ou comandos podem confiar e mantenha uma lógica para lidar com essa situação.
* A extensão de script personalizada será executada a conta localsystem

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
> gerenciadoAPropriedade de identidade **não deve** ser usada em conjunto com o armazenamentoContaNome ou armazenamentoPropriedades da AccountKey

> [!NOTE]
> Apenas uma versão de uma extensão pode ser instalada em uma VM em um ponto no tempo, especificando o script personalizado duas vezes no mesmo modelo do Gerenciador de recursos para a mesma VM falhará.

> [!NOTE]
> Podemos usar este esquema dentro do recurso VirtualMachine ou como um recurso autônomo. O nome do recurso deve estar neste formato "virtualMachineName/extensionName", se essa extensão for usada como um recurso autônomo no modelo ARM. 

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1,10 | INT |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matriz |
| carimbo de data/hora (exemplo) | 123456789 | Inteiro de 32 bits |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| gerenciadoIdentidade (por exemplo) | { } ou { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } ou { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | objeto json |

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

#### <a name="property-value-details"></a>Detalhes de valor de propriedade

* `commandToExecute`: (**necessária**, cadeia de caracteres) o script de ponto de entrada a ser executado. Use esse campo se o comando contiver segredos, como senhas, ou se os fileUris diferenciarem maiúsculas de minúsculas.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs dos arquivos a serem baixados.
* `timestamp` (opcional, inteiro de 32 bits) use esse campo apenas para disparar uma nova execução do script, alterando o valor desse campo.  Qualquer valor inteiro é aceitável. Só deve ser diferente do valor anterior.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` deverão ser URLs para Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto json) a [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para download de arquivos(s)
  * `clientId`: (opcional, string) o ID do cliente da identidade gerenciada
  * `objectId`: (opcional, string) o ID do objeto da identidade gerenciada

Os valores a seguir podem ser definidos nas configurações públicas ou protegidas. A extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos tanto nas configurações protegidas quanto nas públicas.

* `commandToExecute`

Usar configurações públicas talvez seja útil para depuração, mas é recomendável que você use configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM quando foram enviadas, ou seja, se as configurações foram criptografadas, elas serão salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no runtime.

####  <a name="property-managedidentity"></a>Propriedade: gerenciadoIdentidade

CustomScript (versão 1.10 em diante) suporta [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para baixar arquivos de URLs fornecidos na configuração "fileUris". Ele permite que o CustomScript acesse blobs ou contêineres privados do Azure Storage sem que o usuário tenha que passar segredos como tokens SAS ou chaves de conta de armazenamento.

Para usar esse recurso, o usuário deve adicionar uma identidade [atribuída ao sistema](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) ou atribuída ao [usuário](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) à VM ou VMSS onde o CustomScript deverá ser executado e conceder o acesso de identidade gerenciado ao contêiner [ou blob do Azure Storage](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Para usar a identidade atribuída ao sistema no VM/VMSS de destino, defina o campo "identidade gerenciada" como um objeto json vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Para usar a identidade atribuída pelo usuário no VM/VMSS de destino, configure o campo "identidade gerenciada" com o ID do cliente ou o ID do objeto da identidade gerenciada.

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
> gerenciadoAPropriedade de identidade **não deve** ser usada em conjunto com o armazenamentoContaNome ou armazenamentoPropriedades da AccountKey

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON, que é detalhado na seção anterior, pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Script Personalizado durante a implantação. Os exemplos a seguir mostram como usar a extensão de Script personalizado:

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

Neste exemplo, você tem três scripts que são usados para construir seu servidor. O **comandoToExecute** chama o primeiro script, então você tem opções sobre como os outros são chamados. Por exemplo, você pode ter um script mestre que controla a execução, com o manuseio de erros correto, registro e gerenciamento de estado. Os scripts são baixados para a máquina local para execução. Por exemplo, `1_Add_Tools.ps1` você `2_Add_Features.ps1` chamaria `.\2_Add_Features.ps1` adicionando ao script e repetiria esse `$settings`processo para os outros scripts definidos em .

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

Neste exemplo, você pode querer usar um servidor SMB local para sua localização de script. Ao fazer isso, você não precisa fornecer outras configurações, exceto **commandToExecute**.

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

* O parâmetro de extensão **Name** é o mesmo da implantação anterior da extensão.
* Atualize a configuração caso contrário, o comando não será reexecutado. É possível adicionar uma propriedade dinâmica ao comando, como um carimbo de data/hora.

Alternativamente, você pode definir a propriedade [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) como **true**.

### <a name="using-invoke-webrequest"></a>Usando Invoke-WebRequest

Se você estiver usando [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) em seu script, você deve especificar o parâmetro `-UseBasicParsing` ou então você receberá o seguinte erro ao verificar o status detalhado:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais

Para implantar a extensão de script personalizada em um conjunto de escalas, consulte [Add-AzVmsSExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>VMs clássicas

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para implantar a Extensão de Script Personalizado em VMs clássicos, você pode usar o portal Azure ou os cmdlets Clássicos do Azure PowerShell.

### <a name="azure-portal"></a>Portal do Azure

Navegue até o recurso Clássico VM. Selecione **Extensões** em **Configurações**.

Clique **+ Adicionar** e na lista de recursos escolha **Extensão de script personalizado**.

Na página **de extensão Instalar,** selecione o arquivo PowerShell local e preencha quaisquer argumentos e clique em **Ok**.

### <a name="powershell"></a>PowerShell

Use o [cmdlet Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) pode ser usado para adicionar a extensão Script personalizado a uma máquina virtual existente.

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

### <a name="troubleshoot"></a>Solução de problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o módulo do Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A saída de extensão é registrada em arquivos encontrados na seguinte pasta na máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados na seguinte pasta na máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

em que `<n>` é um inteiro decimal que pode ser alterado entre as execuções da extensão.  O valor `1.*` corresponde ao valor `typeHandlerVersion` atual e real da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o comando `commandToExecute`, a extensão definirá esse diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Esse processo permite o uso de caminhos relativos para localizar os arquivos baixados por meio da propriedade `fileURIs`. Veja a tabela abaixo para obter exemplos.

Como o caminho absoluto do download pode variar ao longo do tempo, é melhor optar por caminhos de arquivo/script relativos na cadeia de caracteres `commandToExecute`, sempre que possível. Por exemplo: 

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

As informações do caminho após o primeiro segmento `fileUris` URI são mantidas para arquivos baixados através da lista de propriedades.  Conforme mostrado na tabela a seguir, os arquivos baixados são mapeados em subdiretórios de download para refletir a estrutura dos valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Arquivos Baixados

| URI no fileUris | Localização baixada relativa | Localização baixada absoluta <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Os caminhos absolutos do diretório mudam ao longo da vida útil da VM, mas não dentro de uma única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Se você precisar de mais ajuda em qualquer ponto deste artigo, você pode entrar em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Você também pode registrar um incidente de Suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
