---
title: Extensão do DSC do Azure para Linux
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
ms.openlocfilehash: e2faf444aa411f0e60f1b5c7b1f811abc2f6b63a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176682"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão de DSC para Linux (Microsoft.OSTCExtensions.DSCForLinux)

A Configuração de Estado Desejado (DSC) é uma plataforma de gerenciamento que permite gerenciar a equipe de TI e a infraestrutura de desenvolvimento com a configuração como código.

> ! Observe que a extensão de DSC para Linux e a [extensão de máquina virtual Azure monitor para Linux](/azure/virtual-machines/extensions/oms-linux) atualmente apresentam um conflito e não tem suporte em uma configuração lado a lado.  Isso significa que você não deve usar as duas soluções juntas na mesma VM.

A extensão DSCForLinux é publicada e suportada pela Microsoft. A extensão instala o agente OMI e DSC em máquinas virtuais do Azure. Extensão de DSC também pode fazer as seguintes ações


- Registrar a VM do Linux para a conta de automação do Azure para efetuar pull das configurações do serviço de automação do Azure (registrar ExtensionAction)
- Enviar por push as configurações do MOF para a VM do Linux (enviar por Push ExtensionAction)
- Aplicar a configuração de MOF Meta para a VM do Linux para configurar o servidor de Pull para efetuar pull de configuração de nó (Pull ExtensionAction)
- Instalar os módulos de DSC personalizados à VM do Linux (instalar ExtensionAction)
- Remover os módulos de DSC personalizados à VM do Linux (Remover ExtensionAction)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão DSC Linux dá suporte a todas as [distribuições do Linux endossadas no Azure](/azure/virtual-machines/linux/endorsed-distros) exceto:

| Distribuição | Versão |
|---|---|
| Debian | todas as versões |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão DSCFoxLinux requer que a máquina virtual de destino esteja conectada à Internet. Por exemplo, a extensão de registro exige conectividade com o serviço de automação. Para outras ações, como Efetuar Pull, Efetuar Push, Instalar, é preciso ter conectividade com o armazenamento do azure/github. Ele depende de configurações fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="11-public-configuration"></a>1.1 Configuração pública

Aqui estão todos os parâmetros de configuração pública com suporte:

* `FileUri`: (opcional, cadeia de caracteres) o uri do MOF Meta/arquivo MOF personalizado/arquivo ZIP do arquivo de recurso.
* `ResourceName`: (opcional, cadeia de caracteres) o nome do módulo de recurso personalizado
* `ExtensionAction`: (opcional, cadeia de caracteres) Especifica o que faz uma extensão. Valores válidos: Registrar, Efetuar Push, Efetuar Pull, Instalar, Remover. Se não for especificado, ele é considerado como a ação de enviar por Push por padrão.
* `NodeConfigurationName`: (opcional, cadeia de caracteres) o nome de uma configuração de nó a ser aplicada.
* `RefreshFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) que DSC tenta obter a configuração do servidor de pull. 
       Se a configuração no servidor de pull for diferente da atual no nó de destino, ela é copiada para o repositório pendente e aplicada.
* `ConfigurationMode`: (opcional, cadeia de caracteres) Especifica como DSC deve aplicar a configuração. Os valores válidos são: ApplyOnly, ApplyandMonitior e ApplyandAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) DSC garante que a configuração está no estado desejado.

> [!NOTE]
> Se você estiver usando uma versão < 2.3, parâmetro modo é o mesmo que ExtensionAction. O modo parece ser um termo sobrecarregado. Portanto, para evitar a confusão, ExtensionAction está sendo usado na versão 2.3 em diante. Para compatibilidade com versões anteriores, a extensão dá suporte ao modo e ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1.2 Configuração protegida

Aqui estão todos os parâmetros de configuração protegidos com suporte:

* `StorageAccountName`: (opcional, cadeia de caracteres): o nome da conta de armazenamento que contém o arquivo
* `StorageAccountKey`: (opcional, cadeia de caracteres): a chave da conta de armazenamento que contém o arquivo
* `RegistrationUrl`: (opcional, cadeia de caracteres) a URL da conta de automação do Azure
* `RegistrationKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de automação do Azure


## <a name="scenarios"></a>Cenários

### <a name="register-to-azure-automation-account"></a>Registre sua conta da Automação do Azure
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

formato powershell
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração do MOF (na conta de armazenamento do Azure) à VM

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

formato powershell
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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração do MOF (no armazenamento público) à VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

formato powershell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração do MOF meta (na conta de armazenamento do Azure) à VM

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

formato powershell
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
formato powershell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Instalar um módulo de recurso personalizado (arquivo ZIP na conta de armazenamento do Azure) para a máquina virtual
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

formato powershell
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Instalar um módulo de recurso personalizado (arquivo ZIP no armazenamento público) para a máquina virtual
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
formato powershell
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
formato powershell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem configuração pós-implantação, tal como integração à Automação Azure. 

É o modelo do Resource Manager [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais detalhes sobre os modelos do Resource Manager, visite [Criação de Modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

### <a name="21-using-azure-cliazure-cli"></a>2.1. Usando [**CLI do Azure**] [azure-cli]
Antes de implantar a extensão DSCForLinux, você deve configurar seu `public.json` e `protected.json`, acordo com os diferentes cenários na seção 3.

#### <a name="211-classic"></a>2.1.1. Clássico
O modo clássico também é chamado de modo de gerenciamento de serviços do Azure. Você pode alternar para ele, executando:
```
$ azure config mode asm
```

Você pode implantar Extensão DSCForLinux executando:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Para saber a versão mais recente da extensão disponível, execute:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Gerenciador de Recursos
Você pode mudar para o modo do Gerenciador de Recursos do Azure executando:
```
$ azure config mode arm
```

Você pode implantar Extensão DSCForLinux executando:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> No modo Azure Resource Manager, `azure vm extension list` não está disponível agora.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Usando [**o Azure PowerShell**][Azure-powershell]

#### <a name="221-classic"></a>2.2.1 Clássico

Você pode fazer logon sua conta do Azure (modo de gerenciamento de serviços do Azure), executando:

```powershell>
Add-AzureAccount
```

E implantar Extensão DSCForLinux executando:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Você precisa mudar o conteúdo de $privateConfig e $publicConfig de acordo com os diferentes cenários na seção acima 
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

#### <a name="222resource-manager"></a>2.2.2.Gerenciador de Recursos

Você pode fazer logon sua conta do Azure (modo de Gerenciador de Recurso do Azure), executando:

```powershell>
Login-AzAccount
```

Clique [**AQUI**](../../azure-resource-manager/manage-resources-powershell.md) para saber mais sobre como usar o Azure PowerShell com o Azure Resource Manager.

Você pode implantar Extensão DSCForLinux executando:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Você precisa mudar o conteúdo de $privateConfig e $publicConfig de acordo com os diferentes cenários na seção acima 
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

### <a name="troubleshoot"></a>Solucionar problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Código de erro: 51 representa a distribuição sem suporte ou ação de extensão sem suporte.
Em alguns casos, o Linux DSC extensão Falha ao instalar a OMI quando uma versão superior do OMI já existe na máquina. [resposta de erro: (000003)Downgrade não permitido]



### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).
