---
title: Azure Data Lake Storage Gen2 PowerShell para arquivos & ACLs
description: Use os cmdlets do PowerShell para gerenciar diretórios e listas de controle de acesso (ACL) de arquivos e diretórios em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: c859176857f64559b9a2994c9cfc2d4ec5f61e57
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691087"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o PowerShell para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o PowerShell para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado. 

[Gen1 para mapeamento](#gen1-gen2-map) | de Gen2[forneça comentários](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem o namespace hierárquico (HNS) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * O .NET Framework é 4.7.2 ou superior instalado. Consulte [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versão `5.1` do PowerShell ou superior.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

1. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Instale o módulo **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Conectar-se à conta

Abra uma janela de comando do Windows PowerShell e entre em sua assinatura do Azure com o `Connect-AzAccount` comando e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento na qual você deseja criar e gerenciar diretórios. Neste exemplo, substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Em seguida, escolha como você deseja que seus comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: obter autorização usando o Azure Active Directory (AD)

Com essa abordagem, o sistema garante que sua conta de usuário tenha as atribuições de RBAC (controle de acesso baseado em função) apropriadas e permissões de ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: obter autorização usando a chave da conta de armazenamento

Com essa abordagem, o sistema não verifica as permissões de RBAC ou de ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos atua como um contêiner para seus arquivos. Você pode criar um usando o `New-AzDatalakeGen2FileSystem` cmdlet. 

Este exemplo cria um sistema de arquivos `my-file-system`chamado.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório usando o `New-AzDataLakeGen2Item` cmdlet. 

Este exemplo adiciona um diretório chamado `my-directory` a um sistema de arquivos.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Este exemplo adiciona o mesmo diretório, mas também define as permissões, umask, valores de propriedade e valores de metadados. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostrar propriedades do diretório

Este exemplo obtém um diretório usando o `Get-AzDataLakeGen2Item` cmdlet e, em seguida, imprime valores de propriedade no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório usando `Move-AzDataLakeGen2Item` o cmdlet.

Este exemplo renomeia um diretório do nome `my-directory` para o nome. `my-new-directory`

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Use o `-Force` parâmetro se você quiser substituir sem prompts.

Este exemplo move um diretório chamado `my-directory` para um subdiretório de `my-directory-2` nome. `my-subdirectory` 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório usando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo exclui um diretório chamado `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Você pode usar o `-Force` parâmetro para remover o arquivo sem um prompt.

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um diretório usando o `Get-AzDataLakeGen2ItemContent` cmdlet.

Este exemplo baixa um arquivo chamado `upload.txt` de um diretório chamado `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um diretório usando o `Get-AzDataLakeGen2ChildItem` cmdlet. Você pode usar o parâmetro `-OutputUserPrincipalName` opcional para obter o nome (em vez da ID de objeto) de usuários.

Este exemplo lista o conteúdo de um diretório chamado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

O exemplo a seguir lista `ACL`as `Permissions` `Owner` propriedades `Group`,, e de cada item no diretório. O `-FetchProperty` parâmetro é necessário para obter valores para a `ACL` propriedade. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Para listar o conteúdo de um sistema de arquivos, `-Path` omita o parâmetro do comando.

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Carregue um arquivo em um diretório usando o `New-AzDataLakeGen2Item` cmdlet.

Este exemplo carrega um arquivo chamado `upload.txt` em um diretório chamado. `my-directory` 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Este exemplo carrega o mesmo arquivo, mas, em seguida, define as permissões, umask, valores de propriedade e valores de metadados do arquivo de destino. Este exemplo também imprime esses valores no console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Este exemplo obtém um arquivo usando o `Get-AzDataLakeGen2Item` cmdlet e, em seguida, imprime valores de propriedade no console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Excluir um arquivo

Exclua um arquivo usando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo exclui um arquivo chamado `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Você pode usar o `-Force` parâmetro para remover o arquivo sem um prompt.

## <a name="manage-access-permissions"></a>Gerenciar permissões de acesso

Você pode obter, definir e atualizar as permissões de acesso de sistemas de arquivos, diretórios e arquivos. Essas permissões são capturadas em listas de controle de acesso (ACLs).

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar comandos, verifique se a entidade de segurança recebeu a função de [proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte [controle de acesso em Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Obter uma ACL

Obtenha a ACL de um diretório ou arquivo usando o `Get-AzDataLakeGen2Item`cmdlet.

Este exemplo obtém a ACL de um **sistema de arquivos** e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo obtém a ACL de um **diretório**e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Este exemplo obtém a ACL de um **arquivo** e, em seguida, imprime a ACL no console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Neste exemplo, o usuário proprietário tem permissões de leitura, gravação e execução. O grupo proprietário tem apenas permissões de leitura e execução. Para obter mais informações sobre listas de controle de acesso, consulte [Access Control in Azure data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Definir uma ACL

Use o `set-AzDataLakeGen2ItemAclObject` cmdlet para criar uma ACL para o usuário proprietário, o grupo proprietário ou outros usuários. Em seguida, use `Update-AzDataLakeGen2Item` o cmdlet para confirmar a ACL.

Este exemplo define a ACL em um **sistema de arquivos** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo define a ACL em um **diretório** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Este exemplo define a ACL em um **arquivo** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem a seguir mostra a saída depois de definir a ACL de um arquivo.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o usuário proprietário e o grupo proprietário têm apenas permissões de leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre listas de controle de acesso, consulte [Access Control in Azure data Lake Storage Gen2](data-lake-storage-access-control.md).


### <a name="set-acls-on-all-items-in-a-file-system"></a>Definir ACLs em todos os itens em um sistema de arquivos

Você pode usar o `Get-AzDataLakeGen2Item` e o `-Recurse` parâmetro junto com o `Update-AzDataLakeGen2Item` cmdlet para recursivamente definir a ACL para diretórios e arquivos em um sistema de arquivos. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl

$Token = $Null
do
{
     $items = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -ContinuationToken $Token    
     if($items.Length -le 0) { Break;}
     $items | Update-AzDataLakeGen2Item -Acl $acl
     $Token = $items[$items.Count -1].ContinuationToken;
}
While ($Token -ne $Null) 
```

### <a name="add-or-update-an-acl-entry"></a>Adicionar ou atualizar uma entrada de ACL

Primeiro, obtenha a ACL. Em seguida, use `set-AzDataLakeGen2ItemAclObject` o cmdlet para adicionar ou atualizar uma entrada de ACL. Use o `Update-AzDataLakeGen2Item` cmdlet para confirmar a ACL.

Este exemplo cria ou atualiza a ACL em um **diretório** para um usuário.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Remover uma entrada de ACL

Este exemplo remove uma entrada de uma ACL existente.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapeamento de Gen1 para Gen2

A tabela a seguir mostra como os cmdlets usados para Data Lake Storage Gen1 são mapeados para os cmdlets para Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Anotações |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Por padrão, o cmdlet Get-AzDataLakeGen2ChildItem lista apenas os itens filho de primeiro nível. O parâmetro-Recurse lista os itens filhos recursivamente. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Os itens de saída do cmdlet Get-AzDataLakeGen2Item têm estas propriedades: ACL, proprietário, grupo, permissão.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|O cmdlet Get-AzDataLakeGen2FileContent baixa o conteúdo do arquivo para o arquivo local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Esse cmdlet carrega o novo conteúdo do arquivo de um arquivo local.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|O cmdlet Update-AzDataLakeGen2Item atualiza apenas um único item e não recursivamente. Se desejar atualizar recursivamente, liste os itens usando o cmdlet Get-AzDataLakeStoreChildItem e, em seguida, pipeline para o cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|O cmdlet Get-AzDataLakeGen2Item relatará um erro se o item não existir.|

## <a name="see-also"></a>Consulte também

* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Cmdlets do PowerShell do Armazenamento](/powershell/module/az.storage)
