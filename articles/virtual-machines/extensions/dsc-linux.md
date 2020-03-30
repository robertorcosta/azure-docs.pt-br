---
title: Extensão Do Azure DSC para Linux
description: Instala os pacotes OMI e DSC para permitir que uma VM do Linux do Azure ser configurada usando a Configuração de Estado Desejado.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250615"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão DSC para Linux (Microsoft.OSTCExtensions.DSCForLinux)

A Configuração de Estado Desejada (DSC) é uma plataforma de gerenciamento que você pode usar para gerenciar sua infra-estrutura de TI e desenvolvimento com configuração como código.

> [!NOTE]
> A extensão DSC para Linux e a extensão da [máquina virtual do Azure Monitor para Linux](/azure/virtual-machines/extensions/oms-linux) atualmente apresentam um conflito e não são suportadas em uma configuração lado a lado. Não use as duas soluções juntas na mesma VM.

A extensão DSCForLinux é publicada e suportada pela Microsoft. A extensão instala o agente OMI e DSC em máquinas virtuais do Azure. A extensão do DSC também pode fazer as seguintes ações:


- Registre o VM Linux em uma conta do Azure Automation para puxar configurações do serviço de automação do Azure (Register ExtensionAction).
- Empurre as configurações do MOF para o Linux VM (Push ExtensionAction).
- Aplique a configuração meta MOF ao Linux VM para configurar um servidor de tração para puxar a configuração do nó (Pull ExtensionAction).
- Instale módulos DSC personalizados no Linux VM (Install ExtensionAction).
- Remova módulos DSC personalizados do Linux VM (Remove ExtensionAction).

 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão DSC Linux dá suporte a todas as [distribuições do Linux endossadas no Azure](/azure/virtual-machines/linux/endorsed-distros) exceto:

| Distribuição | Versão |
|---|---|
| Debian | Todas as versões |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão DSCForLinux exige que a máquina virtual de destino esteja conectada à internet. Por exemplo, a extensão Registrar requer conectividade com o serviço de Automação. Para outras ações como Pull, Pull, Install requer conectividade com o Azure Storage e o GitHub. Depende das configurações fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="public-configuration"></a>Configuração pública

Aqui estão todos os parâmetros de configuração pública com suporte:

* `FileUri`: (opcional, string) O uri do arquivo MOF, do arquivo meta MOF ou do arquivo zip de recurso personalizado.
* `ResourceName`: (opcional, string) O nome do módulo de recurso personalizado.
* `ExtensionAction`: (opcional, cadeia de caracteres) Especifica o que faz uma extensão. Os valores válidos são Registrar, Empurrar, Puxar, Instalar e Remover. Se não for especificado, é considerado uma Ação de Pressão por padrão.
* `NodeConfigurationName`: (opcional, string) O nome de uma configuração de nó para aplicar.
* `RefreshFrequencyMins`: (opcional, int) Especifica quantas vezes (em minutos) o DSC tenta obter a configuração do servidor pull. 
       Se a configuração no servidor de tração difere da atual no nó de destino, ela será copiada para o armazenamento pendente e aplicada.
* `ConfigurationMode`: (opcional, cadeia de caracteres) Especifica como DSC deve aplicar a configuração. Os valores válidos são ApplyOnly, ApplyAndMonitor e ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) DSC garante que a configuração está no estado desejado.

> [!NOTE]
> Se você usar uma versão antes do 2.3, o parâmetro de modo é o mesmo que ExtensionAction. O modo parece ser um termo sobrecarregado. Para evitar confusão, extensionaction é usado a partir da versão 2.3 em diante. Para compatibilidade com versões anteriores, a extensão dá suporte ao modo e ExtensionAction. 
>

### <a name="protected-configuration"></a>Configuração protegida

Aqui estão todos os parâmetros de configuração protegidos com suporte:

* `StorageAccountName`: (opcional, string) O nome da conta de armazenamento que contém o arquivo
* `StorageAccountKey`: (opcional, string) A chave da conta de armazenamento que contém o arquivo
* `RegistrationUrl`: (opcional, string) A URL da conta Azure Automation
* `RegistrationKey`: (opcional, string) A chave de acesso da conta Azure Automation


## <a name="scenarios"></a>Cenários

### <a name="register-an-azure-automation-account"></a>Registre uma conta do Azure Automation
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplique um arquivo de configuração MOF (em uma conta de armazenamento Do Zure) à VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplique um arquivo de configuração MOF (em armazenamento público) à VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Formato PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplique um arquivo de configuração meta MOF (em uma conta de armazenamento Do Zure) à VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração do MOF meta (no armazenamento público) à VM
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instale um módulo de recurso personalizado (um arquivo zip em uma conta de armazenamento do Azure) na VM
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instale um módulo de recurso personalizado (um arquivo zip em armazenamento público) na VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Remover um módulo de recurso personalizado de VM
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Os modelos são ideais quando você implanta uma ou mais máquinas virtuais que requerem configuração pós-implantação, como o onboarding para o Azure Automation. 

É o modelo do Resource Manager [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais informações sobre o modelo do Azure Resource Manager, consulte [Os modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).


## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

### <a name="use-azure-cliazure-cli"></a>Use [Azure CLI][azure-cli]
Antes de implantar a extensão DSCForLinux, configure seu `public.json` e `protected.json` de acordo com os diferentes cenários na seção 3.

#### <a name="classic"></a>Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O modo clássico de implantação também é chamado de modo de gerenciamento de serviços do Azure. Você pode alternar para ele, executando:
```
$ azure config mode asm
```

Você pode implantar a extensão DSCForLinux executando:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Para saber a versão mais recente da extensão disponível, execute:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Gerenciador de Recursos
Você pode mudar para o modo do Gerenciador de Recursos do Azure executando:
```
$ azure config mode arm
```

Você pode implantar a extensão DSCForLinux executando:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> No modo Azure `azure vm extension list` Resource Manager, não está disponível por enquanto.
>

### <a name="use-azure-powershellazure-powershell"></a>Use [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Clássico

Você pode fazer login na sua conta do Azure no modo de Gerenciamento de Serviços do Azure executando:

```powershell>
Add-AzureAccount
```

E implante a extensão DSCForLinux executando:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Alterar o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários da seção anterior.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Gerenciador de Recursos

Você pode fazer login na sua conta do Azure no modo Azure Resource Manager executando:

```powershell>
Login-AzAccount
```

Para saber mais sobre como usar o Azure PowerShell com o Azure Resource Manager, consulte [Gerenciar os recursos do Azure usando o Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Você pode implantar a extensão DSCForLinux executando:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Alterar o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários da seção anterior.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do portal Azure e usando o Cli do Azure. Para ver o estado de implantação das extensões para uma determinada VM, execute o seguinte comando usando o Cli do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Código de erro: 51 representa distribuição não suportada ou ação de extensão não suportada.
Em alguns casos, a extensão DSC Linux falha em instalar o OMI quando uma versão superior do OMI já existe na máquina. [resposta de erro: não permitido de Downgrade (000003)]



### <a name="support"></a>Suporte

Se você precisar de mais ajuda em qualquer momento deste artigo, entre em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Alternativamente, você pode registrar um incidente de suporte ao Azure. Vá para o site de suporte do [Azure](https://azure.microsoft.com/support/options/)e **selecione Obter suporte**. Para obter informações sobre como usar o Suporte ao Azure, leia o [FaQ de suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).
