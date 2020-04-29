---
title: Usar CLI do Azure para arquivos & ACLs no Azure Data Lake Storage Gen2 (versão prévia)
description: Use o CLI do Azure para gerenciar diretórios e listas de controle de acesso (ACL) de arquivos e diretórios em contas de armazenamento que têm um namespace hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77486127"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Usar CLI do Azure para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2 (versão prévia)

Este artigo mostra como usar a [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm um namespace hierárquico. 

> [!IMPORTANT]
> A `storage-preview` extensão que está em destaque neste artigo está atualmente em visualização pública.

[Sample](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 de exemplo para mapeamento](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | de Gen2[fornecer comentários](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem o namespace hierárquico (HNS) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * CLI do Azure versão `2.0.67` ou superior.

## <a name="install-the-storage-cli-extension"></a>Instalar a extensão da CLI de armazenamento

1. Abra o [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)ou, se você tiver [instalado](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o CLI do Azure localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Verifique se a versão do CLI do Azure que tem o instalado `2.0.67` é ou superior usando o comando a seguir.

   ```azurecli
    az --version
   ```
   Se sua versão do CLI do Azure for menor que `2.0.67`, instale uma versão posterior. Consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Instale a extensão `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Conectar-se à conta

1. Se você estiver usando CLI do Azure localmente, execute o comando de logon.

   ```azurecli
   az login
   ```

   Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

   Caso contrário, abra uma página do [https://aka.ms/devicelogin](https://aka.ms/devicelogin) navegador em e insira o código de autorização exibido no seu terminal. Em seguida, entre com suas credenciais de conta no navegador.

   Para saber mais sobre os diferentes métodos de autenticação, confira Entrar com a CLI do Azure.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos atua como um contêiner para seus arquivos. Você pode criar um usando o `az storage container create` comando. 

Este exemplo cria um sistema de arquivos `my-file-system`chamado.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório usando o `az storage blob directory create` comando. 

Este exemplo adiciona um diretório chamado `my-directory` a um sistema de arquivos `my-file-system` chamado que está localizado em uma conta `mystorageaccount`chamada.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mostrar propriedades do diretório

Você pode imprimir as propriedades de um diretório no console do usando o `az storage blob show` comando.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório usando `az storage blob directory move` o comando.

Este exemplo renomeia um diretório do nome `my-directory` para o nome. `my-new-directory`

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório usando o `az storage blob directory delete` comando.

Este exemplo exclui um diretório chamado `my-directory`. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Verificar se existe um diretório

Determine se um diretório específico existe no sistema de arquivos usando o `az storage blob directory exist` comando.

Este exemplo revela se um diretório chamado `my-directory` existe no sistema `my-file-system` de arquivos. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um diretório usando o `az storage blob directory download` comando.

Este exemplo baixa um arquivo chamado `upload.txt` de um diretório chamado `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Este exemplo baixa um diretório inteiro.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um diretório usando o `az storage blob directory list` comando.

Este exemplo lista o conteúdo de um diretório chamado `my-directory` que está localizado no sistema `my-file-system` de arquivos de uma conta de armazenamento `mystorageaccount`denominada. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Carregue um arquivo em um diretório usando o `az storage blob directory upload` comando.

Este exemplo carrega um arquivo chamado `upload.txt` em um diretório chamado. `my-directory` 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Este exemplo carrega um diretório inteiro.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Você pode imprimir as propriedades de um arquivo no console usando o `az storage blob show` comando.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Renomear ou mover um arquivo

Renomeie ou mova um arquivo usando `az storage blob move` o comando.

Este exemplo renomeia um arquivo do nome `my-file.txt` para o nome. `my-file-renamed.txt`

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Excluir um arquivo

Exclua um arquivo usando o `az storage blob delete` comando.

Este exemplo exclui um arquivo chamado`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Gerenciar permissões

Você pode obter, definir e atualizar as permissões de acesso de diretórios e arquivos.

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar comandos, verifique se a entidade de segurança recebeu a função de [proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte [controle de acesso em Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Obter permissões de diretório e arquivo

Obtenha a ACL de um **diretório** usando o `az storage blob directory access show` comando.

Este exemplo obtém a ACL de um diretório e, em seguida, imprime a ACL no console.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Obtenha as permissões de acesso de um **arquivo** usando o `az storage blob access show` comando. 

Este exemplo obtém a ACL de um arquivo e, em seguida, imprime a ACL no console.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Neste exemplo, o usuário proprietário tem permissões de leitura, gravação e execução. O grupo proprietário tem apenas permissões de leitura e execução. Para obter mais informações sobre listas de controle de acesso, consulte [Access Control in Azure data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Definir permissões de diretório e arquivo

Use o `az storage blob directory access set` comando para definir a ACL de um **diretório**. 

Este exemplo define a ACL em um diretório para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo define a ACL *padrão* em um diretório para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Use o `az storage blob access set` comando para definir a ACL de um **arquivo**. 

Este exemplo define a ACL em um arquivo para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
A imagem a seguir mostra a saída depois de definir a ACL de um arquivo.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Neste exemplo, o usuário proprietário e o grupo proprietário têm apenas permissões de leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre listas de controle de acesso, consulte [Access Control in Azure data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Atualizar permissões de diretório e arquivo

Outra maneira de definir essa permissão é usar o `az storage blob directory access update` comando ou `az storage blob access update` . 

Atualize a ACL de um diretório ou arquivo definindo o `-permissions` parâmetro como a forma abreviada de uma ACL.

Este exemplo atualiza a ACL de um **diretório**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo atualiza a ACL de um **arquivo**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Você também pode atualizar o usuário proprietário e o grupo de um diretório ou arquivo definindo os `--owner` parâmetros `group` ou para a ID da entidade ou nome UPN de um usuário. 

Este exemplo altera o proprietário de um diretório. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo altera o proprietário de um arquivo. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Gerenciar metadados definidos pelo usuário

Você pode adicionar metadados definidos pelo usuário a um arquivo ou diretório usando o `az storage blob directory metadata update` comando com um ou mais pares nome-valor.

Este exemplo adiciona metadados definidos pelo usuário para um diretório chamado `my-directory` Directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Este exemplo mostra todos os metadados definidos pelo usuário para o `my-directory`diretório chamado.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Confira também

* [Amostra](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Fornecer feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Código-fonte](https://github.com/Azure/azure-cli-extensions/tree/master/src)

