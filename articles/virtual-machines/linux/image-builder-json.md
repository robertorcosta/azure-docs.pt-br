---
title: Criar um modelo de Construtor de Imagens Azure (visualização)
description: Aprenda a criar um modelo para usar com o Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246782"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Visualização: Crie um modelo de Construtor de Imagens Azure 

O Azure Image Builder usa um arquivo .json para passar informações para o serviço Image Builder. Neste artigo vamos repassar as seções do arquivo json, para que você possa construir o seu próprio. Para ver exemplos de arquivos .json completos, consulte o [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Este é o formato de modelo básico:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versão de API

O `type` é o tipo de `"Microsoft.VirtualMachineImages/imageTemplates"`recurso, que deve ser . O `apiVersion` será alterado ao longo do tempo `"2019-05-01-preview"` à medida que a API muda, mas deve ser para visualização.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

O local é a região onde a imagem personalizada será criada. Para a visualização do Image Builder, as seguintes regiões são suportadas:

- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmPerfil
Por padrão, o Image Builder usará uma VM de compilação "Standard_D1_v2", você pode substituir isso, por exemplo, se você quiser personalizar uma imagem para uma VM GPU, você precisa de um tamanho de VM gpu. Isso é opcional.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Por padrão, o Image Builder não mudará o tamanho da imagem, ele usará o tamanho da imagem de origem. Você pode aumentar o tamanho do Disco do SO (Win e Linux), isso é opcional, e um valor de 0 significa deixar o mesmo tamanho da imagem de origem. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Se você não especificar nenhuma propriedade VNET, o Image Builder criará seu próprio VNET, IP público e NSG. O IP público é usado para que o serviço se comunique com a VM de construção, no entanto, se você não quiser um IP público ou quiser que o Image Builder tenha acesso aos seus recursos VNET existentes, como servidores de configuração (DSC, Chef, Puppet, Ansible), compartilhamentos de arquivos etc. , então você pode especificar um VNET. Para obter mais informações, revise a [documentação da rede,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)isso é opcional.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Marcas

Estes são pares de chaves/valor que você pode especificar para a imagem gerada.

## <a name="depends-on-optional"></a>Depende de (opcional)

Esta seção opcional pode ser usada para garantir que as dependências sejam concluídas antes de prosseguir. 

```json
    "dependsOn": [],
```

Para obter mais informações, consulte [Definir dependências de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identidade
Por padrão, o Image Builder suporta o uso de scripts ou a cópia de arquivos de vários locais, como o armazenamento GitHub e a zure. Para usá-los, eles devem ser acessíveis publicamente.

Você também pode usar uma identidade gerenciada atribuída pelo usuário do Azure, definida por você, para permitir que o Image Builder acesse o Azure Storage, desde que a identidade tenha sido concedida um mínimo de 'Leitor de dados blob de armazenamento' na conta de armazenamento do Azure. Isso significa que você não precisa tornar as bolhas de armazenamento acessíveis externamente ou configurar tokens SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Para um exemplo completo, consulte [Usar uma identidade gerenciada atribuída pelo usuário do Azure para acessar arquivos no Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Suporte ao Construtor de Imagens para uma identidade atribuída ao usuário: • Suporta apenas uma única identidade • Não suporta nomes de domínio personalizados

Para saber mais, [veja O que é identidades gerenciadas para os recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Para obter mais informações sobre a implantação desse recurso, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM Azure usando o Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: fonte

A `source` seção contém informações sobre a imagem de origem que será usada pelo Image Builder.

A API requer um 'SourceType' que define a fonte para a compilação de imagens, atualmente existem três tipos:
- PlatformImage - indicou que a imagem de origem é uma imagem do Marketplace.
- ManagedImage - use isso ao partir de uma imagem gerenciada regularmente.
- SharedImageVersion - isso é usado quando você está usando uma versão de imagem em uma Galeria de Imagens Compartilhadas como fonte.

### <a name="iso-source"></a>Fonte ISO
Estamos depreciando essa funcionalidade do construtor de imagens, pois agora existem [imagens rhel bring your own subscription](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos), por favor, revise os cronogramas abaixo:
    * 31 de março de 2020 - Modelos de imagem com fontes ISO RHEL agora serão aceitos mais pelo provedor de recursos.
    * 30 de abril de 2020- Os modelos de imagem que contenham fontes ISO RHEL não serão mais processados.

### <a name="platformimage-source"></a>Fonte PlatformImage 
O Azure Image Builder suporta imagens do Windows Server e do cliente e do Linux Azure Marketplace, veja [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) a lista completa. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


As propriedades aqui são as mesmas que são usadas para criar VM's, usando AZ CLI, executar o abaixo para obter as propriedades: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Você pode usar 'mais recente' na versão, a versão é avaliada quando a compilação de imagens ocorre, não quando o modelo é enviado. Se você usar essa funcionalidade com o destino da Galeria de Imagens Compartilhadas, poderá evitar reenviar o modelo e reexecutar a compilação de imagens em intervalos, para que suas imagens sejam recriadas a partir das imagens mais recentes.

### <a name="managedimage-source"></a>Fonte ManagedImage

Define a imagem de origem como uma imagem gerenciada existente de um VHD ou VM generalizado. A imagem gerenciada de origem deve ser de um sistema operacional suportado e estar na mesma região que o modelo do Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

O `imageId` deve ser o ResourceId da imagem gerenciada. Use `az image list` para listar imagens disponíveis.


### <a name="sharedimageversion-source"></a>Fonte SharedImageVersion
Define a imagem de origem como uma versão de imagem existente em uma Galeria de Imagens Compartilhadas. A versão de imagem deve ser de um sistema operacional suportado, e a imagem deve ser replicada na mesma região que o modelo azure Image Builder. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

O `imageVersionId` deve ser o ResourceId da versão de imagem. Use [a lista de versões de imagem az sig](/cli/azure/sig/image-version#az-sig-image-version-list) para listar versões de imagem.

## <a name="properties-buildtimeoutinminutes"></a>Propriedades: buildTimeoutInMinutes

Por padrão, o Construtor de Imagens será executado por 240 minutos. Depois disso, ele vai cronometrar e parar, quer a compilação da imagem esteja completa ou não. Se o tempo de intervalo for atingido, você verá um erro semelhante a este:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se você não especificar um valor buildTimeoutInMinutes ou defini-lo como 0, isso usará o valor padrão. Você pode aumentar ou diminuir o valor, até o máximo de 960mins (16hrs). Para windows, não recomendamos definir isso abaixo de 60 minutos. Se você encontrar que está acertando o tempo, revise os [logs,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)para ver se a etapa de personalização está esperando algo como a entrada do usuário. 

Se você achar que precisa de mais tempo para as personalizações serem concluídas, defina isso para o que você acha que precisa, com um pouco de sobrecarga. Mas, não configurá-lo muito alto porque você pode ter que esperar por ele para o tempo antes de ver um erro. 


## <a name="properties-customize"></a>Propriedades: personalizar

O Image Builder suporta vários 'personalizadores'. Personalização são funções usadas para personalizar sua imagem, como executar scripts ou reiniciar servidores. 

Ao `customize`usar: 
- Você pode usar vários personalizadores, mas `name`eles devem ter um único .
- Os personalizadores são executados na ordem especificada no modelo.
- Se um personalizador falhar, todo o componente de personalização falhará e reportará um erro.
- É fortemente aconselhável que você teste o script completamente antes de usá-lo em um modelo. Depurar o script em sua própria VM será mais fácil.
- Não coloque dados confidenciais nos scripts. 
- Os locais do script precisam ser acessíveis ao público, a menos que você esteja usando [o MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
A seção personalizar é uma matriz. O Azure Image Builder executará os personalizadores em ordem seqüencial. Qualquer falha em qualquer personalizador falhará no processo de compilação. 
 
 
### <a name="shell-customizer"></a>Personalizador shell

O personalizador shell suporta a execução de scripts shell, estes devem ser acessíveis publicamente para o IB acessá-los.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Suporte ao SISTEMA OPERACIONAL: Linux 
 
Personalizar propriedades:

- **tipo** – Shell 
- **nome** - nome para acompanhar a personalização 
- **scriptUri** - URI para a localização do arquivo 
- **inline** - matriz de comandos shell, separados por commas.
- **sha256Checksum** - Valor do sha256 checksum do arquivo, você gera isso localmente e, em seguida, o Image Builder fará check-e validar.
    * Para gerar o sha256Checksum, usando um terminal no Mac/Linux executado:`sha256sum <fileName>`


Para que os comandos sejam executados com privilégios de super usuário, eles devem ser prefixados com `sudo`.

> [!NOTE]
> Ao executar o personalizador shell com a fonte RHEL ISO, você precisa garantir que suas primeiras alças de shell de personalização registrem-se com um servidor de direito Red Hat antes que qualquer personalização ocorra. Uma vez que a personalização esteja concluída, o script deve cancelar o registro com o servidor de direito.

### <a name="windows-restart-customizer"></a>Personalizador de reinicialização do Windows 
O personalizador Restart permite que você reinicie uma VM do Windows e espere que ela volte a funcionar, isso permite que você instale um software que requer uma reinicialização.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Suporte ao SISTEMA OPERACIONAL: Windows
 
Personalizar propriedades:
- **Tipo**: WindowsRestart
- **comando restart -** Comando para executar a reinicialização (opcional). O padrão é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – Comando para verificar se a reinicialização foi bem sucedida (opcional). 
- **restartTimeout** - Reinicie o tempo de saída especificado como uma seqüência de magnitude e unidade. Por exemplo, `5m` (5 `2h` minutos) ou (2 horas). O padrão é: '5m'

### <a name="linux-restart"></a>Reinicialização do Linux  
Não há personalizador linux Restart, no entanto, se você estiver instalando drivers ou componentes que requerem uma reinicialização, você pode instalá-los e invocar uma reinicialização usando o personalizador Shell, há um tempo de 20min SSH para a VM de compilação.

### <a name="powershell-customizer"></a>Personalizador PowerShell 
O personalizador shell suporta a execução de scripts PowerShell e comando inline, os scripts devem ser acessíveis publicamente para o IB acessá-los.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Suporte ao SO: Windows e Linux

Personalizar propriedades:

- **tipo** – PowerShell.
- **scriptUri** - URI para a localização do arquivo de script PowerShell. 
- **inline** – Comandos inline a serem executados, separados por commas.
- **validExitCodes** – Códigos opcionais e válidos que podem ser retornados do comando script/inline, isso evitará a falha relatada do comando script/inline.
- **runElevated** – Opcional, booleano, suporte para executar comandos e scripts com permissões elevadas.
- **sha256Checksum** - Valor do sha256 checksum do arquivo, você gera isso localmente e, em seguida, o Image Builder fará check-e validar.
    * Para gerar o sha256Checksum, usando um PowerShell no Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Personalizador de arquivos

O personalizador de arquivos permite que o construtor de imagens baixe um arquivo de um armazenamento GitHub ou Azure. Se você tiver um pipeline de compilação de imagens que dependa de artefatos de construção, você pode definir o personalizador de arquivos para baixar a partir do compartilhamento de compilação e mover os artefatos para a imagem.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Suporte ao SO: Linux e Windows 

Propriedades do personalizador de arquivos:

- **sourceUri** - um ponto final de armazenamento acessível, este pode ser o armazenamento GitHub ou Azure. Você só pode baixar um arquivo, não um diretório inteiro. Se você precisar baixar um diretório, use um arquivo compactado e, em seguida, descompactá-lo usando os personalizadores Shell ou PowerShell. 
- **destino** – este é o caminho completo de destino e nome do arquivo. Qualquer caminho e subdiretórios referenciados devem existir, use os personalizadores Shell ou PowerShell para configurá-los previamente. Você pode usar os personalizadores de script para criar o caminho. 

Isso é suportado por diretórios do Windows e caminhos linux, mas há algumas diferenças: 
- Os sO's do Linux – o único caminho para o quais o construtor de imagens pode escrever é /tmp.
- Windows – Sem restrição de caminho, mas o caminho deve existir.
 
 
Se houver um erro ao tentar baixar o arquivo ou colocá-lo em um diretório especificado, a etapa de personalização falhará e isso estará no customization.log.

> [!NOTE]
> O personalizador de arquivos é adequado apenas para pequenos downloads de arquivos, < 20MB. Para downloads maiores de arquivos use um script ou comando inline, `curl`o `Invoke-WebRequest`código de uso para baixar arquivos, como, Linux `wget` ou , Windows, .

Os arquivos no personalizador de arquivos podem ser baixados do Azure Storage usando [o MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Personalizador de atualização do Windows
Este personalizador é construído na [comunidade Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) for Packer, que é um projeto de código aberto mantido pela comunidade Packer. A Microsoft testa e valida o provisionador com o serviço Image Builder, e apoiará a investigação de problemas com ele e trabalhará para resolver problemas, no entanto, o projeto de código aberto não é suportado oficialmente pela Microsoft. Para obter documentação detalhada e ajuda com o Provisionador de atualização do Windows, consulte o repositório do projeto.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Suporte ao SO: Windows

Personalizar propriedades:
- **tipo** – WindowsUpdate.
- **searchCriteria** - Opcional, define qual tipo de atualizações estão instaladas (Recomendado, Importante etc.), BrowseOnly=0 e IsInstalled=0 (Recomendado) é o padrão.
- **filtros** – Opcional, permite especificar um filtro para incluir ou excluir atualizações.
- **updateLimit** – Opcional, define quantas atualizações podem ser instaladas, padrão 1000.
 
 

### <a name="generalize"></a>Generalizar 
Por padrão, o Azure Image Builder também executará o código 'deprovisionamento' no final de cada fase de personalização de imagem, para 'generalizar' a imagem. Generalizar é um processo onde a imagem é configurada para que possa ser reutilizada para criar várias VMs. Para VMs do Windows, o Azure Image Builder usa o Sysprep. Para Linux, o Azure Image Builder executa 'waagent -deprovision'. 

Os comandos que os usuários do Image Builder generalizam podem não ser adequados para todas as situações, então o Azure Image Builder permitirá que você personalize este comando, se necessário. 

Se você estiver migrando a personalização existente e estiver usando diferentes comandos Sysprep/waagent, poderá usar os comandos genéricos do Image Builder e, se a criação do VM falhar, use seus próprios comandos Sysprep ou waagent.

Se o Azure Image Builder criar uma imagem personalizada do Windows com sucesso e você criar uma VM a partir dela, então descobrir que a criação de VM falha ou não é concluída com sucesso, você precisará rever a documentação do Windows Server Sysprep ou levantar uma solicitação de suporte com o Equipe de suporte ao serviço ao cliente do Windows Server Sysprep, que pode solucionar problemas e aconselhar sobre o uso correto do Sysprep.


#### <a name="default-sysprep-command"></a>Comando Sysprep padrão
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Comando de desprovision do Linux padrão

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Substituindo os Comandos
Para substituir os comandos, use os provisionadores de script PowerShell ou Shell para criar os arquivos de comando com o nome exato do arquivo e colocá-los nos diretórios corretos:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

O Construtor de imagens lerá esses comandos, estes serão escritos nos logs AIB, 'customização.log'. Consulte [a solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) sobre como coletar logs.
 
## <a name="properties-distribute"></a>Propriedades: distribuir

O Azure Image Builder suporta três metas de distribuição: 

- **managedImage** - imagem gerenciada.
- **compartilhadaImage** - Galeria de Imagens Compartilhadas.
- **VHD** - VHD em uma conta de armazenamento.

Você pode distribuir uma imagem para ambos os tipos de destino na mesma configuração, por favor, veja [exemplos](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Como você pode ter mais de um destino para distribuir, o Image Builder mantém um `runOutputName`estado para cada meta de distribuição que pode ser acessada consultando o .  O `runOutputName` é um objeto que você pode consultar a distribuição pós-distribuição para obter informações sobre essa distribuição. Por exemplo, você pode consultar a localização do VHD, ou regiões onde a versão de imagem foi replicada, ou a versão sig image criada. Esta é uma propriedade de cada alvo de distribuição. O `runOutputName` deve ser exclusivo de cada alvo de distribuição. Aqui está um exemplo, isso é consultar uma distribuição da Galeria de Imagens Compartilhadas:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Saída:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuir: managedImage

A saída de imagem será um recurso de imagem gerenciado.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuir propriedades:
- **tipo** – gerenciadoImage 
- **imageId** – ID de recurso da imagem de\<destino, formato esperado:\</assinaturas/ assinaturaId>/resourceGroups/destinationResourceGroupName>/providers/Microsoft.Compute/images/\<imageName>
- **localização** - localização da imagem gerenciada.  
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **arteartefatosTags** - O usuário opcional especificou as tags de par de valor-chave especificadas.
 
 
> [!NOTE]
> O grupo de recursos de destino deve existir.
> Se você quiser que a imagem seja distribuída para uma região diferente, ela aumentará o tempo de implantação . 

### <a name="distribute-sharedimage"></a>Distribuir: compartilhadoImage 
O Azure Shared Image Gallery é um novo serviço de gerenciamento de imagens que permite gerenciar a replicação, a versão e o compartilhamento de imagens personalizadas da região da imagem. O Azure Image Builder suporta a distribuição com este serviço, para que você possa distribuir imagens para regiões suportadas por Galerias de Imagens Compartilhadas. 
 
Uma galeria de imagens compartilhadas é composta por: 
 
- Galeria - Contêiner para várias imagens compartilhadas. Uma galeria está implantada em uma região.
- Definições de imagem - um agrupamento conceitual para imagens. 
- Versões de imagem - este é um tipo de imagem usado para implantar um VM ou conjunto de escala. As versões de imagem podem ser replicadas para outras regiões onde as VMs precisam ser implantadas.
 
Antes de ser distribuído na Galeria de Imagens, você deve criar uma galeria e uma definição de imagem, ver [imagens compartilhadas](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuir propriedades para galerias de imagens compartilhadas:

- **tipo** - compartilhadoImagem  
- **galleryImageId** – ID da galeria de imagens compartilhadas. O formato\<é: /subscriptions/ subscriptionId\<>/resourceGroups/ resourceGroupName>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **arteartefatosTags** - O usuário opcional especificou as tags de par de valor-chave especificadas.
- **replicaçãoRegiões** - Matriz de regiões para replicação. Uma das regiões deve ser a região onde a Galeria está implantada.
 
> [!NOTE]
> Você pode usar o Azure Image Builder em uma região diferente para a galeria, mas o serviço Azure Image Builder precisará transferir a imagem entre os data centers e isso levará mais tempo. O Image Builder irá automaticamente versá-la, com base em um inteiro monótono, não é possível especificá-la atualmente. 

### <a name="distribute-vhd"></a>Distribuição: VHD  
Você pode sair para um VHD. Em seguida, você pode copiar o VHD e usá-lo para publicar no Azure MarketPlace ou usar com o Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Suporte ao SISTEMA OPERACIONAL: Windows e Linux

Distribuir parâmetros de VHD:

- **tipo** - VHD.
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **tags** - O usuário opcional especificou as tags de par de valor-chave especificadas.
 
O Azure Image Builder não permite que o usuário especifique um `runOutputs` local de conta de armazenamento, mas você pode consultar o status do para obter a localização.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Uma vez criado o VHD, copie-o para um local diferente, o mais rápido possível. O VHD é armazenado em uma conta de armazenamento no grupo de recursos temporários criado quando o modelo de imagem é submetido ao serviço Azure Image Builder. Se você excluir o modelo de imagem, então você perderá o VHD. 
 
## <a name="next-steps"></a>Próximas etapas

Existem arquivos sample .json para diferentes cenários no [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
