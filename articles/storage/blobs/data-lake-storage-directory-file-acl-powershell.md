---
title: Azure Data Lake Storage Gen2 PowerShell para arquivos & ACLs (visualização)
description: Use cmdlets PowerShell para gerenciar diretórios e listas de controle de acesso de arquivos e diretórios (ACL) em contas de armazenamento que tenham hns (names) hierárquico habilitado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 9b0e0b39b7ac7d7834c9cdcbd79ba45b024c823a
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992004"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Use o PowerShell para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2 (visualização)

Este artigo mostra como usar o PowerShell para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm hns (namespace) hierárquico ativado. 

> [!IMPORTANT]
> O módulo PowerShell que é apresentado neste artigo está atualmente em visualização pública.

[Mapeamento de Gen1 para Gen2](#gen1-gen2-map) | [Dê feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tenha hns (namespace) hierárquico habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * O Quadro .NET é 4.7.2 ou superior instalado. Consulte [Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versão `5.1` PowerShell ou superior.

## <a name="install-powershell-modules"></a>Instalar módulos do PowerShell

1. Verifique se a versão do PowerShell `5.1` que foi instalada é ou superior usando o seguinte comando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar sua versão do PowerShell, consulte [Atualizando o Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Instale o módulo **PowershellGet** mais recente. Em seguida, feche e reabra o console PowerShell.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force 
   ```

3. Instale o módulo de visualização **Az.Storage.**

   ```powershell
   Install-Module az.storage -RequiredVersion 1.13.3-preview -Repository PSGallery -AllowClobber -AllowPrerelease -Force 
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [Instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Conecte-se à conta

Abra uma janela de comando do Windows PowerShell e, `Connect-AzAccount` em seguida, faça login na sua assinatura do Azure com o comando e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para assinatura da conta de armazenamento em que você deseja criar e gerenciar diretórios. Neste exemplo, substitua o valor do `<subscription-id>` espaço reservado pelo ID da sua assinatura.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Em seguida, escolha como deseja que seus comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: Obter autorização usando o Azure Active Directory (AD)

Com essa abordagem, o sistema garante que sua conta de usuário tenha as atribuições e permissões de Controle de Acesso (RBAC) baseadas em função apropriadas. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: Obter autorização usando a chave da conta de armazenamento

Com essa abordagem, o sistema não verifica permissões RBAC ou ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos funciona como um recipiente para seus arquivos. Você pode criar um `New-AzDatalakeGen2FileSystem` usando o cmdlet. 

Este exemplo cria um `my-file-system`sistema de arquivos chamado .

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de `New-AzDataLakeGen2Item` diretório usando o cmdlet. 

Este exemplo adiciona um `my-directory` diretório nomeado a um sistema de arquivos.

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

Este exemplo obtém um `Get-AzDataLakeGen2Item` diretório usando o cmdlet e, em seguida, imprime valores de propriedade para o console.

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

Renomeie ou mova um `Move-AzDataLakeGen2Item` diretório usando o cmdlet.

Este exemplo renomeia um `my-directory` diretório do `my-new-directory`nome para o nome .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Use `-Force` o parâmetro se quiser substituir sem solicitações.

Este exemplo move um `my-directory` diretório nomeado para `my-directory-2` `my-subdirectory`um subdiretório de nomeado . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório `Remove-AzDataLakeGen2Item` usando o cmdlet.

Este exemplo exclui um `my-directory`diretório chamado . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Você pode `-Force` usar o parâmetro para remover o arquivo sem um prompt.

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um `Get-AzDataLakeGen2ItemContent` diretório usando o cmdlet.

Este exemplo baixa um `upload.txt` arquivo nomeado `my-directory`de um diretório chamado . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um `Get-AzDataLakeGen2ChildItem` diretório usando o cmdlet. Você pode usar o `-OutputUserPrincipalName` parâmetro opcional para obter o nome (em vez do ID do objeto) dos usuários.

Este exemplo lista o conteúdo `my-directory`de um diretório chamado .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

O exemplo a `ACL` `Permissions`seguir `Group`lista `Owner` as propriedades de cada item no diretório. O `-FetchProperty` parâmetro é necessário para `ACL` obter valores para a propriedade. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Para listar o conteúdo de um sistema `-Path` de arquivos, omita o parâmetro do comando.

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um arquivo para um diretório

Carregue um arquivo para um `New-AzDataLakeGen2Item` diretório usando o cmdlet.

Este exemplo faz upload `upload.txt` de um `my-directory`arquivo nomeado para um diretório chamado . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Este exemplo carrega o mesmo arquivo, mas depois define as permissões, umask, valores de propriedade e valores de metadados do arquivo de destino. Este exemplo também imprime esses valores para o console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Este exemplo obtém um `Get-AzDataLakeGen2Item` arquivo usando o cmdlet e, em seguida, imprime valores de propriedade para o console.

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

Exclua um arquivo `Remove-AzDataLakeGen2Item` usando o cmdlet.

Este exemplo exclui um `upload.txt`arquivo chamado . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Você pode `-Force` usar o parâmetro para remover o arquivo sem um prompt.

## <a name="manage-access-permissions"></a>Gerenciar permissões de acesso

Você pode obter, definir e atualizar permissões de acesso de sistemas de arquivos, diretórios e arquivos.

> [!NOTE]
> Se você estiver usando o Azure Active Directory (Azure AD) para autorizar comandos, certifique-se de que seu principal de segurança tenha sido designado para a [função Deproprietário de dados do Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte o controle de [acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-permissions"></a>Obter permissões

Obtenha a ACL de um diretório `Get-AzDataLakeGen2Item`ou arquivo usando o cmdlet.


Este exemplo obtém a ACL de um sistema de **arquivos** e, em seguida, imprime a ACL para o console.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo obtém a ACL de um **diretório**e, em seguida, imprime a ACL para o console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Este exemplo obtém a ACL de um **arquivo** e, em seguida, imprime a ACL para o console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Neste exemplo, o usuário possuinte tem permissões de leitura, gravação e execução. O grupo de dono só leu e executou permissões. Para obter mais informações sobre listas de controle de acesso, consulte [controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-or-update-permissions"></a>Definir ou atualizar permissões

Use `set-AzDataLakeGen2ItemAclObject` o cmdlet para criar uma ACL para o usuário dono, grupo próprio ou outros usuários. Em seguida, `Update-AzDataLakeGen2Item` use o cmdlet para cometer a ACL.

Este exemplo define a ACL em um **sistema de arquivos** para o usuário próprio, possuindo grupo ou outros usuários e, em seguida, imprime a ACL para o console.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo define a ACL em um **diretório** para o usuário próprio, possuindo grupo ou outros usuários e, em seguida, imprime a ACL para o console.

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
Este exemplo define a ACL em um **arquivo** para o usuário que possui, possui grupo ou outros usuários e, em seguida, imprime a ACL para o console.

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

A imagem a seguir mostra a saída após definir a ACL de um arquivo.

![Obter saída ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o usuário próprio e o grupo possuidores têm apenas permissões de leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre listas de controle de acesso, consulte [controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).


### <a name="set-permissions-on-all-items-in-a-file-system"></a>Defina permissões em todos os itens de um sistema de arquivos

Você pode `Get-AzDataLakeGen2Item` usar `-Recurse` o parâmetro e `Update-AzDataLakeGen2Item` o parâmetro juntamente com o cmdlet para recursivamente definir a ACL de todos os diretórios e arquivos em um sistema de arquivos. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapeamento de Gen1 para Gen2

A tabela a seguir mostra como os cmdlets usados para data lake storage gen1 mapeiam para os cmdlets para Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Observações |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Por padrão, o cmdlet Get-AzDataLakeGen2ChildItem lista apenas os itens de primeiro nível do filho. O parâmetro -Recurse lista itens infantis recursivamente. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemProprietário<br>Get-AzDataLakeLakeStoreItemPermissão|Get-AzDataLakeGen2Item|Os itens de saída do cmdlet Get-AzDataLakeGen2Item têm essas propriedades: Acl, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemConteúdo|Get-AzDataLakeGen2Conteúdodearquivos|O get-AzDataLakeGen2FileContent cmdlet baixe conteúdo de arquivo para arquivo local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Este cmdlet carrega o novo conteúdo do arquivo de um arquivo local.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeLakeStoreItemProprietário<br>Set-AzDataLakeLakeLakeItemPermissão<br>Set-AzDataLakeStoreItemAcl|Atualização-AzDataLakeGen2Item|O update-AzDataLakeGen2Item cmdlet atualiza apenas um único item, e não recursivamente. Se quiser atualizar recursivamente, liste itens usando o cmdlet Get-AzDataLakeStoreChildItem e, em seguida, pipeline para o cmdlet Update-AzDataLakeGen2Item.|
|Teste-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|O cmdlet Get-AzDataLakeGen2Item reportará um erro se o item não existir.|



## <a name="see-also"></a>Confira também

* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Usando o Azure PowerShell com armazenamento Azure](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Cmdlets PowerShell de armazenamento](/powershell/module/az.storage).

