---
title: Executar scripts personalizados em VMs do Linux no Azure
description: Automatizar tarefas de configuração de VM do Linux usando a Extensão de Script Personalizado v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: 9a53cae61e48a8d0aa19b138d4084ca257ea705b
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299236"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Usar a Versão 2 da Extensão de Script Personalizado do Azure com máquinas virtuais do Linux
A Versão 2 da Extensão de Script Personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Você pode fazer o download de scripts a partir do Armazenamento do Microsoft Azure ou outro local acessível da internet, ou você pode fornecê-los para o runtime da extensão. 

A extensão de Script personalizado se integra com os modelos do Azure Resource Manager. Você também pode executá-la usando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de máquinas virtuais do Azure.

Este artigo fornece detalhes sobre como usar a extensão de Script personalizado da CLI do Azure, e como executar a extensão usando um modelo do Azure Resource Manager. Este artigo também fornece as etapas de solução de problemas para os sistemas do Linux.


Há duas Extensões do Script Personalizado do Linux:
* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Alterne entre implantações novas e existentes para usar a nova versão 2. A nova versão tem o objetivo de ser uma substituição perfeita. Assim, a migração é tão fácil quanto alterar o nome e versão. Você não precisa alterar a configuração de extensão.


### <a name="operating-system"></a>Sistema operacional

A Extensão de Script Personalizado para Linux será executada nos SOs de extensão com suporte. Para obter mais informações, confira este [artigo](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Local do script

Você pode utilizar a extensão para usar suas credenciais do Armazenamento de Blobs do Azure para acessar esse armazenamento. Como alternativa, o local do script pode ser qualquer lugar, desde que a VM possa rotear para esse ponto de extremidade, como GitHub, servidor de arquivos interno, etc.

### <a name="internet-connectivity"></a>Conectividade com a Internet
Se você precisar fazer o download um script externamente, como do GitHub ou do Armazenamento do Azure, será necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede. Por exemplo, se o script estiver localizado no Armazenamento do Azure, você poderá permitir acesso usando Marcas de Serviço do NSG do Azure para [Armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Se o script estiver em um servidor local, ainda poderá ser necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede.

### <a name="tips-and-tricks"></a>Dicas e truques
* A taxa de falha mais alta para esta extensão acontece devido a erros de sintaxe no script. Teste as execuções de script sem erros e também insira um registro em log adicional no script para facilitar a localização da falha.
* Escreva scripts idempotentes, para que se forem executados mais de uma vez por acidente, eles não causem alterações no sistema.
* Verifique se os scripts não exigem entrada do usuário quando são executados.
* É permitido que o script seja executado em até 90 minutos. Um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações no script, pois isso causará problemas com outras extensões que estão sendo instaladas e, após a reinicialização, a extensão será interrompida. 
* Se você tiver um script que causará uma reinicialização, instale aplicativos e execute scripts, etc. Você deve agendar a reinicialização usando um trabalho cron ou usando ferramentas como DSC, ou chefe, Puppet Extensions.
* A extensão executará um script somente uma vez. Se quiser executar um script em cada inicialização, então você poderá usar [imagem de inicialização de nuvem](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) e um módulo [Scripts Por Inicialização](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot). Como alternativa, você pode usar o script para criar uma unidade de serviço do sistema.
* Se quiser agendar quando um script será executado, você deverá usar a extensão para criar um trabalho Cron. 
* Quando o script for executado, você só verá um status da extensão 'em transição' no portal do Azure ou no CLI. Se quiser atualizações de status mais frequentes de um script em execução, você precisará criar sua própria solução.
* Extensão de Script Personalizado não oferece nativamente suporte a servidores proxy. No entanto, é possível usar uma ferramenta de transferência de arquivos que oferece suporte a servidores proxy no seu script, como *Curl*. 
* Lembre-se de locais de diretório não padrão nos quais seus scripts ou comandos podem confiar e ter lógica para lidar com isso.
*  Ao implantar o script personalizado em instâncias de VMSS de produção, é recomendável implantar por meio do modelo JSON e armazenar sua conta de armazenamento de script onde você tem controle sobre o token SAS. 


## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo do Azure Resource Manager. 

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
        "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> a propriedade managedIdentity **não deve** ser usada em conjunto com as propriedades StorageAccountName ou storageAccountKey

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publicador | Microsoft.Compute.Extensions | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.1 | INT |
| fileUris (por exemplo) | https://github.com/MyProject/Archive/MyPythonScript.py | matriz |
| commandToExecute (por exemplo) | Python MyPythonScript.py \<My-param1 > | string |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (exemplo) | false | booleano |
| carimbo de data/hora (exemplo) | 123456789 | Inteiro de 32 bits |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| managedIdentity (por exemplo,) | {} ou {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} ou {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | objeto JSON |

### <a name="property-value-details"></a>Detalhes de valor de propriedade
* `apiVersion`: a apiVersion mais atualizada pode ser encontrada usando o [Gerenciador de recursos](https://resources.azure.com/) ou de CLI do Azure usando o seguinte comando `az provider list -o json`
* `skipDos2Unix`: (opcional, booliano) ignore a conversão de dos2unix das URLs de arquivos baseado no script ou do script.
* `timestamp`(opcional, inteiro de 32 bits) use este campo somente para disparar uma nova execução do script ao alterar o valor desse campo.  Qualquer valor inteiro é aceitável. Só deve ser diferente do valor anterior.
  * `commandToExecute`(**obrigatório** se o script não for definido, cadeia de caracteres) o script de ponto de entrada a ser executado. Use este campo se o comando tiver segredos, como senhas.
* `script`: (**necessário** se commandToExecute não for definido, cadeia de caracteres) um script codificado em Base64 (e opcionalmente compactado com Gzip) executado pelo /bin/sh.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs dos arquivos a serem baixados.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` deverão ser URLs para Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto JSON) a [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para baixar arquivo (s)
  * `clientId`: (opcional, Cadeia de caracteres) a ID do cliente da identidade gerenciada
  * `objectId`: (opcional, Cadeia de caracteres) a ID de objeto da identidade gerenciada


Os valores a seguir podem ser definidos nas configurações públicas ou protegidas. A extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos tanto nas configurações protegidas quanto nas públicas.
* `commandToExecute`
* `script`
* `fileUris`

Usar as configurações públicas pode ser útil para depuração, mas é altamente recomendável usar as configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM no estado em que foram enviadas, ou seja, se foram criptografadas, elas serão salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no runtime.

#### <a name="property-skipdos2unix"></a>Propriedade: skipDos2Unix

O valor padrão é false, o que significa que a conversão de dos2unix **foi** executada.

A versão anterior do CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, seria converter automaticamente arquivos DOS para arquivos do UNIX ao traduzir `\r\n` para `\n`. Essa translação ainda existe e está ativada por padrão. Essa conversão é aplicada a todos os arquivos baixados do fileUris ou à configuração de script com base em qualquer um dos critérios a seguir.

* Se a extensão for uma dos `.sh`, `.txt`, `.py` ou `.pl`, ela será convertida. A configuração de script sempre corresponderá a esse critério, pois ele é considerado um script executado com /bin/sh e é salvo como script.sh na VM.
* Se o arquivo começar com `#!`.

A conversão de dos2unix poderá ser ignorada ao definir o skipDos2Unix como true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propriedade: script

CustomScript dá suporte à execução de um script definido pelo usuário. As configurações de script para combinar commandToExecute e fileUris em uma única configuração. Em vez de ter que configurar um arquivo para download do armazenamento do Azure ou do gist do GitHub, você pode simplesmente codificar o script como uma configuração. O script pode ser usado para commandToExecute e fileUris substituídos.

O script **deve** ser codificado em Base64.  O script pode, **opcionalmente**, ser compactado com Gzip. A configuração de script pode ser usada nas configurações públicas ou protegidas. O tamanho máximo dos dados do parâmetro do script é 256 KB. Se o script exceder esse tamanho, não será executado.

Por exemplo, considerando o seguinte script salvo no arquivo /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A configuração correta do script de CustomScript deve ser construída ao utilizar a saída do comando a seguir.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

O script pode, opcionalmente, ser compactado com Gzip para reduzir o tamanho (na maioria dos casos). (CustomScript detecta automaticamente o uso de compactação gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript usa o algoritmo a seguir para executar um script.

 1. declarar que o comprimento do valor do script não excede 256 KB.
 1. Base64 decodifica o valor do script
 1. _tentativa_ de efetuar gunzip no valor decodificado em Base64
 1. gravar o valor decodificado (e opcionalmente descompactado) para o disco (/var/lib/waagent/custom-script/#/script.sh)
 1. execute o script usando _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Propriedade: managedIdentity

O CustomScript (versão 2,1 em diante) dá suporte à [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para baixar arquivo (s) de URLs fornecidas na configuração "fileuris". Ele permite que o CustomScript acesse BLOBs ou contêineres privados do armazenamento do Azure sem que o usuário precise passar segredos como tokens SAS ou chaves de conta de armazenamento.

Para usar esse recurso, o usuário deve adicionar uma identidade atribuída pelo [usuário](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) ou com o [sistema](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) à VM ou VMSS em que se espera que CustomScript seja executado e [conceder acesso de identidade gerenciada ao contêiner ou BLOB de armazenamento do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Para usar a identidade atribuída pelo sistema na VM/VMSS de destino, defina o campo "managedidentity" como um objeto JSON vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Para usar a identidade atribuída pelo usuário na VM/VMSS de destino, configure o campo "managedidentity" com a ID do cliente ou a ID de objeto da identidade gerenciada.

> Exemplos:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> a propriedade managedIdentity **não deve** ser usada em conjunto com as propriedades StorageAccountName ou storageAccountKey

## <a name="template-deployment"></a>Implantação de modelo
Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Script Personalizado durante uma implantação de modelo do Azure Resource Manager. Um modelo de exemplo que inclui a Extensão de Script Personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure
Quando você estiver usando a CLI do Azure para executar a extensão de Script personalizado, crie um arquivo ou arquivos de configuração. No mínimo, você deve ter 'commandToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionalmente, você pode especificar as configurações no comando como uma cadeia de caracteres do formato JSON. Isso permite especificar a configuração durante a execução e sem um arquivo de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos de CLI do Azure

#### <a name="public-configuration-with-script-file"></a>Configuração pública com o arquivo de script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Configuração pública sem arquivo de script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Arquivos de configuração protegida e pública

Você usa um arquivo de configuração pública para especificar o URI do arquivo de script. Você usa um arquivo de configuração protegida para especificar o comando a ser executado.

Arquivo de configuração pública:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Arquivo de configuração protegida:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>solução de problemas
Quando a extensão de script personalizado é executada, o script é criado ou baixado em um diretório semelhante ao exemplo a seguir. A saída do comando também é salva nesse diretório nos arquivos `stdout` e `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Para solucionar problemas, primeiro verifique o Log de Agente do Linux, confira a extensão executada, verifique:

```bash
/var/log/waagent.log 
```

Você deve procurar a execução da extensão, a exibição será algo assim:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Alguns pontos a serem observados:
1. Habilitar é quando o comando é iniciado.
2. O download está relacionado ao download do pacote de extensão CustomScript do Azure, não aos arquivos de script especificados no fileUris.


A extensão de script do Azure produz um log, que pode ser encontrado aqui:

```bash
/var/log/azure/custom-script/handler.log
```

Você deve procurar a execução individual; ela terá uma aparência semelhante a:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```
Aqui, você pode ver:
* O início do comando Habilitar é este log
* As configurações passadas para a extensão
* A extensão baixando o arquivo e o resultado disso.
* O comando em execução e o resultado.

Você também pode recuperar o estado de execução da extensão de Script personalizado usando a CLI do Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída se parece com o seguinte texto:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Próximas etapas
Para ver o código, os problemas atuais e as versões, consulte [custom-script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux).

