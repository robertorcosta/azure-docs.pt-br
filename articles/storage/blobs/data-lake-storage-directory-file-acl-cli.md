---
title: Use o Azure CLI para arquivos & ACLs no Azure Data Lake Storage Gen2 (visualização)
description: Use o Azure CLI para gerenciar diretórios e listas de controle de acesso de arquivos e diretórios (ACL) em contas de armazenamento que tenham um namespace hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486127"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Use o Azure CLI para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2 (visualização)

Este artigo mostra como usar a [CLI (Azure Command-Line Interface, interface de linha de comando)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que tenham um namespace hierárquico. 

> [!IMPORTANT]
> A `storage-preview` extensão que é destaque neste artigo está atualmente em visualização pública.

[Mapeamento da amostra](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 para Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Dê feedback](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tenha hns (namespace) hierárquico habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Versão `2.0.67` Azure CLI ou superior.

## <a name="install-the-storage-cli-extension"></a>Instale a extensão CLI de armazenamento

1. Abra o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)ou, se você [tiver instalado](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o Azure CLI localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Verifique se a versão do Azure `2.0.67` CLI que foi instalada é ou superior usando o seguinte comando.

   ```azurecli
    az --version
   ```
   Se a sua versão do Azure CLI for menor do que, `2.0.67`instale uma versão posterior. Consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Instale a extensão `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Conecte-se à conta

1. Se você estiver usando o Azure CLI localmente, execute o comando login.

   ```azurecli
   az login
   ```

   Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

   Caso contrário, abra uma [https://aka.ms/devicelogin](https://aka.ms/devicelogin) página do navegador e digite o código de autorização exibido em seu terminal. Em seguida, faça login com as credenciais da sua conta no navegador.

   Para saber mais sobre os diferentes métodos de autenticação, confira Entrar com a CLI do Azure.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua `<subscription-id>` o valor do espaço reservado pelo ID da sua assinatura.

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos funciona como um recipiente para seus arquivos. Você pode criar um `az storage container create` usando o comando. 

Este exemplo cria um `my-file-system`sistema de arquivos chamado .

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de `az storage blob directory create` diretório usando o comando. 

Este exemplo adiciona um `my-directory` diretório nomeado `my-file-system` a um sistema de `mystorageaccount`arquivos chamado localizado em uma conta nomeada .

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Mostrar propriedades do diretório

Você pode imprimir as propriedades de um diretório `az storage blob show` no console usando o comando.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um `az storage blob directory move` diretório usando o comando.

Este exemplo renomeia um `my-directory` diretório do `my-new-directory`nome para o nome .

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório `az storage blob directory delete` usando o comando.

Este exemplo exclui um `my-directory`diretório chamado . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Verifique se existe um diretório

Determine se existe um diretório específico no `az storage blob directory exist` sistema de arquivos usando o comando.

Este exemplo revela se `my-directory` existe um `my-file-system` diretório nomeado no sistema de arquivos. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um `az storage blob directory download` diretório usando o comando.

Este exemplo baixa um `upload.txt` arquivo nomeado `my-directory`de um diretório chamado . 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Este exemplo baixa um diretório inteiro.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um `az storage blob directory list` diretório usando o comando.

Este exemplo lista o conteúdo `my-directory` de um diretório `my-file-system` nomeado que está localizado `mystorageaccount`no sistema de arquivos de uma conta de armazenamento nomeada . 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um arquivo para um diretório

Carregue um arquivo para um `az storage blob directory upload` diretório usando o comando.

Este exemplo faz upload `upload.txt` de um `my-directory`arquivo nomeado para um diretório chamado . 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Este exemplo carrega um diretório inteiro.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Você pode imprimir as propriedades de um `az storage blob show` arquivo no console usando o comando.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Renomear ou mover um arquivo

Renomeie ou mova `az storage blob move` um arquivo usando o comando.

Este exemplo renomeia um `my-file.txt` arquivo `my-file-renamed.txt`do nome para o nome .

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Excluir um arquivo

Exclua um arquivo `az storage blob delete` usando o comando.

Este exemplo exclui um arquivo chamado`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Gerenciar permissões

Você pode obter, definir e atualizar permissões de acesso de diretórios e arquivos.

> [!NOTE]
> Se você estiver usando o Azure Active Directory (Azure AD) para autorizar comandos, certifique-se de que seu principal de segurança tenha sido designado para a [função Deproprietário de dados do Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte o controle de [acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Obtenha permissões de diretório e arquivo

Obtenha a ACL **directory** de um `az storage blob directory access show` diretório usando o comando.

Este exemplo obtém a ACL de um diretório e, em seguida, imprime a ACL para o console.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Obtenha as permissões **file** de acesso `az storage blob access show` de um arquivo usando o comando. 

Este exemplo obtém a ACL de um arquivo e, em seguida, imprime a ACL para o console.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Neste exemplo, o usuário possuinte tem permissões de leitura, gravação e execução. O grupo de dono só leu e executou permissões. Para obter mais informações sobre listas de controle de acesso, consulte [controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Definir permissões de diretório e arquivo

Use `az storage blob directory access set` o comando para definir a ACL de **um diretório**. 

Este exemplo define a ACL em um diretório para o usuário próprio, possuindo grupo ou outros usuários e, em seguida, imprime a ACL para o console.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo define a ACL *padrão* em um diretório para o usuário próprio, possuidor de grupo ou outros usuários e, em seguida, imprime a ACL para o console.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Use `az storage blob access set` o comando para definir a acl de um **arquivo**. 

Este exemplo define a ACL em um arquivo para o usuário que possui, possui grupo ou outros usuários e, em seguida, imprime a ACL para o console.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
A imagem a seguir mostra a saída após definir a ACL de um arquivo.

![Obter saída ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Neste exemplo, o usuário próprio e o grupo possuidores têm apenas permissões de leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre listas de controle de acesso, consulte [controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Atualizar as permissões do diretório e do arquivo

Outra maneira de definir essa `az storage blob directory access update` permissão é usar o comando ou. `az storage blob access update` 

Atualize a ACL de um diretório `-permissions` ou arquivo definindo o parâmetro para a forma curta de uma ACL.

Este exemplo atualiza a ACL de **um diretório**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo atualiza a ACL de um **arquivo**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Você também pode atualizar o usuário e o grupo de `--owner` `group` um diretório ou arquivo, definindo os ou parâmetros para a entidade ID ou Nome Principal do Usuário (UPN) de um usuário. 

Este exemplo muda o proprietário de um diretório. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Este exemplo altera o proprietário de um arquivo. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Gerenciar metadados definidos pelo usuário

Você pode adicionar metadados definidos pelo usuário a `az storage blob directory metadata update` um arquivo ou diretório usando o comando com um ou mais pares de valor de nome.

Este exemplo adiciona metadados definidos pelo `my-directory` usuário para um diretório chamado diretório.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Este exemplo mostra todos os metadados `my-directory`definidos pelo usuário para diretório chamado .

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Confira também

* [Amostra](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Fornecer feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Código-fonte](https://github.com/Azure/azure-cli-extensions/tree/master/src)

