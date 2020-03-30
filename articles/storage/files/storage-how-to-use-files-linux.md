---
title: Usar o Arquivos do Azure com o Linux | Microsoft Docs
description: Saiba como montar um compartilhamento de arquivos do Azure com SMB no Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2dc78c25c2cf63a510b9451c8d694795cd8a91eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060954"
---
# <a name="use-azure-files-with-linux"></a>Usar o Arquivos do Azure com o Linux
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem de fácil acesso da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o [cliente de kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas maneiras de montar um compartilhamento de arquivos do Azure: sob demanda com o comando `mount` e na inicialização criando uma entrada em `/etc/fstab`.

A maneira recomendada de montar um compartilhamento de arquivos Azure no Linux é usando O SMB 3.0. Por padrão, o Azure Files requer criptografia em trânsito, que só é suportada pelo SMB 3.0. O Azure Files também suporta O SMB 2.1, que não suporta criptografia em trânsito, mas você não pode montar compartilhamentos de arquivos Do Zure com o SMB 2.1 de outra região do Azure ou no local por razões de segurança. A menos que seu aplicativo exija especificamente o SMB 2.1, há pouca razão para usá-lo, já que as distribuições Linux mais populares e lançadas recentemente suportam o SMB 3.0:  

| | SMB 2.1 <br>(Montagens em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens de região cruzada e locais) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | Mais de 10 |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Se você estiver usando uma distribuição Linux não listada na tabela acima, você pode verificar se sua distribuição Linux suporta SMB 3.0 com criptografia verificando a versão do kernel Linux. SMB 3.0 com criptografia foi adicionado ao kernel Linux versão 4.11. O `uname` comando retornará a versão do kernel Linux em uso:

```bash
uname -r
```

## <a name="prerequisites"></a>Pré-requisitos
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Certifique-se de que o pacote cifs-utils está instalado.**  
    O pacote cifs-utils pode ser instalado usando o gerenciador de pacotes na distribuição do Linux escolhida. 

    Em distribuições **Ubuntu** e **Debian**, use o gerenciador de pacotes do `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    No **Fedora**, **Red Hat Enterprise Linux 8+** e **CentOS 8 +**, use o gerenciador de `dnf` pacotes:

    ```bash
    sudo dnf install cifs-utils
    ```

    Nas versões mais antigas do Red `yum` Hat Enterprise **Linux** e Do **CentOS,** use o gerenciador de pacotes:

    ```bash
    sudo yum install cifs-utils 
    ```

    No **openSUSE**, use o gerenciador de pacotes do `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    Em outras distribuições, use o gerenciador de pacotes apropriado ou [compile do código-fonte](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **A versão mais recente da Interface de Linha de Comando do Azure (CLI).** Para obter mais informações sobre como instalar o Azure CLI, consulte [Instalar o Cli do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e selecionar o sistema operacional. Se você preferir usar o módulo Azure PowerShell no PowerShell 6+, você pode, no entanto, as instruções abaixo são apresentadas para o Azure CLI.

* **Verifique se a porta 445 está aberta**: o SMB se comunica pela porta TCP 445, por isso confira se o firewall não está bloqueando as portas TCP 445 do computador cliente.  Substitua **<>do grupo de recursos** e<>de conta de **armazenamento**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Se a conexão foi bem sucedida, você deve ver algo semelhante à seguinte saída:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Se você não conseguir abrir a porta 445 em sua rede corporativa ou for impedido de fazê-lo por um isp, você poderá usar uma conexão VPN ou ExpressRoute para trabalhar em torno da porta 445. Para obter mais informações, consulte [considerações de rede para acesso direto ao compartilhamento de arquivos do Azure](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Montagem do compartilhamento de arquivos Do Zure
Para usar um compartilhamento de arquivos Do Zure com sua distribuição Linux, você deve criar um diretório para servir como ponto de montagem para o compartilhamento de arquivos Do Zure. Um ponto de montagem pode ser criado em qualquer lugar do seu sistema Linux, mas é comum criar isso abaixo de /mnt. Após o ponto de `mount` montagem, você usa o comando para acessar o compartilhamento de arquivos do Azure.

Você pode montar o mesmo compartilhamento de arquivos Azure em vários pontos de montagem, se desejar.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar o compartilhamento de arquivos do Azure sob demanda com `mount`
1. **Criar uma pasta para o ponto de montagem**: Substituir `<your-resource-group>` `<your-storage-account>`, e `<your-file-share>` com as informações apropriadas para o seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Use o comando mount para montar o compartilhamento de arquivos Azure**. No exemplo abaixo, as permissões locais de arquivos e pastas do Linux são padrão 0755, que significa ler, escrever e executar para o proprietário (com base no proprietário do arquivo/diretório Linux), ler e executar para usuários em grupo de proprietários e ler e executar para outros no sistema. Você pode `uid` usar `gid` as opções e montar para definir o ID do usuário e o ID de grupo para a montagem. Você também `dir_mode` pode `file_mode` usar e definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte [notação numérica UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > O comando de montagem acima é montado com SMB 3.0. Se a sua distribuição Linux não suportar smb 3.0 com criptografia ou se ele só suporta SMB 2.1, você só pode montar a partir de uma VM Azure dentro da mesma região que a conta de armazenamento. Para montar o compartilhamento de arquivos do Azure em uma distribuição Linux que não suporta SMB 3.0 com criptografia, você precisará desativar a [criptografia em trânsito para a conta de armazenamento](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Quando tiver terminado de usar o compartilhamento de arquivos do Azure, você pode usar `sudo umount $mntPath` para desmontar o compartilhamento.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para o compartilhamento de arquivos do Azure com `/etc/fstab`
1. **Crie uma pasta para o ponto de montagem**: Uma pasta para um ponto de montagem pode ser criada em qualquer lugar do sistema de arquivos, mas é comum a convenção criar isso em /mnt. Por exemplo, o comando a seguir `<your-resource-group>`cria `<your-storage-account>`um `<your-file-share>` novo diretório, substitui e com as informações apropriadas para o seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Crie um arquivo de credencial para armazenar o nome de usuário (o nome de conta de armazenamento) e a senha (a chave de conta de armazenamento) para o compartilhamento de arquivos.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Alterar as permissões no arquivo de credenciais para que raiz possa ler ou modificar o arquivo de senha.** Como a chave da conta de armazenamento é essencialmente uma senha de super administrador para a conta de armazenamento, é importante definir as permissões no arquivo de modo que somente o usuário root possa acessar, para que usuários com privilégios mais baixos não possam recuperar a chave da conta de armazenamento.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Use o seguinte comando para anexar `/etc/fstab`a seguinte linha a **: No exemplo abaixo, o arquivo linux local e as permissões de pasta padrão 0755, que significa ler, escrever e executar para o proprietário (com base no proprietário do arquivo/diretório Linux), ler e executar para usuários em grupo de proprietários e ler e executar para outros no sistema. Você pode `uid` usar `gid` as opções e montar para definir o ID do usuário e o ID de grupo para a montagem. Você também `dir_mode` pode `file_mode` usar e definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte [notação numérica UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > O comando de montagem acima é montado com SMB 3.0. Se a sua distribuição Linux não suportar smb 3.0 com criptografia ou se ele só suporta SMB 2.1, você só pode montar a partir de uma VM Azure dentro da mesma região que a conta de armazenamento. Para montar o compartilhamento de arquivos do Azure em uma distribuição Linux que não suporta SMB 3.0 com criptografia, você precisará desativar a [criptografia em trânsito para a conta de armazenamento](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Como proteger o Linux
Para montar um compartilhamento de arquivos Azure no Linux, a porta 445 deve estar acessível. Muitas organizações bloqueiam a porta 445 devido aos riscos de segurança inerentes ao protocolo SMB 1. SMB 1, também conhecido como CIFS (Common Internet File System), é um protocolo de sistema de arquivos legado incluído com muitas distribuições Linux. O SMB 1 é um protocolo desatualizado, ineficiente e, o mais importante, não seguro. A boa notícia é que o Azure Files não suporta SMB 1, e a partir da versão 4.18 do kernel Linux, o Linux torna possível desativar o SMB 1. Sempre [recomendamos desabilitar](https://aka.ms/stopusingsmb1) o SMB 1 em seus clientes Linux antes de usar ações de arquivos SMB na produção.

Começando com o kernel 4.18 do Linux, o módulo de kernel SMB, chamado `cifs` por razões herdadas, expõe um novo parâmetro de módulo (muitas vezes referido como *parm* por várias documentações externas), chamado `disable_legacy_dialects`. Embora introduzido no kernel 4.18 do Linux, alguns fornecedores voltaram atrás nessa mudança para kernels mais antigos que eles suportam. Para conveniência, a tabela a seguir detalha a disponibilidade deste parâmetro de módulo em distribuições Linux comuns.

| Distribuição | Pode desativar o SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Não |
| Ubuntu 18.04 | Sim |
| Ubuntu 19.04+ | Sim |
| Debian 8-9 | Não |
| Debian 10+ | Sim |
| Fedora 29+ | Sim |
| CentOS 7 | Não | 
| CentOS 8+ | Sim |
| Red Hat Enterprise Linux 6.x-7.x | Não |
| Red Hat Enterprise Linux 8+ | Sim |
| openSUSE Leap 15.0 | Não |
| openSUSE Leap 15.1+ | Sim |
| openSUSE Tumbleweed | Sim |
| SUSE Linux Enterprise 11.x-12.x | Não |
| SUSE Linux Enterprise 15 | Não |
| SUSE Linux Enterprise 15.1 | Não |

Você pode verificar se sua distribuição `disable_legacy_dialects` Linux suporta o parâmetro do módulo através do seguinte comando.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Este comando deve enviar a seguinte mensagem:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Antes de desativar o SMB 1, você deve verificar se o módulo SMB não está carregado no momento no seu sistema (isso acontece automaticamente se você tiver montado um compartilhamento de SMB). Você pode fazer isso com o seguinte comando, que não deve ser produzido se o SMB não estiver carregado:

```bash
lsmod | grep cifs
```

Para descarregar o módulo, primeiro desmonte `umount` todas as ações de SMB (usando o comando como descrito acima). Você pode identificar todos os compartilhamentos de SMB montados em seu sistema com o seguinte comando:

```bash
mount | grep cifs
```

Uma vez desmontado todos os compartilhamentos de arquivos SMB, é seguro descarregar o módulo. Você pode fazer isso com o comando `modprobe` :

```bash
sudo modprobe -r cifs
```

Você pode carregar manualmente o módulo com `modprobe` SMB 1 descarregado usando o comando:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Finalmente, você pode verificar se o módulo SMB foi carregado com o `/sys/module/cifs/parameters`parâmetro olhando para os parâmetros carregados em :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Para desativar persistentemente o SMB 1 nas distribuições baseadas no Ubuntu e no Debian, você deve criar `/etc/modprobe.d/local.conf` um novo arquivo (se você ainda não tiver opções personalizadas para outros módulos) chamado com a configuração. Você pode fazer isso com o seguinte comando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Você pode verificar se isso funcionou carregando o módulo SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Comentários
Usuários do Linux, queremos ouvir sua opinião!

O grupo do Arquivos do Azure para usuários do Linux oferece um fórum para que você possa compartilhar comentários à medida que você avalia e adota o Armazenamento de Arquivos no Linux. Envie um email para [Usuários do Linux do Arquivos do Azure](mailto:azurefiles@microsoft.com) para ingressar no grupo de usuários.

## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure:

* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Perguntas Frequentes](../storage-files-faq.md)
* [Solução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
