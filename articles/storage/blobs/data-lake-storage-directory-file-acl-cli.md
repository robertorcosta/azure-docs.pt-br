---
title: Usar a CLI do Azure para arquivos e ACLs no Azure Data Lake Storage Gen2
description: Use a CLI do Azure para gerenciar diretórios e listas de controle de acesso (ACL) e arquivos nas contas de armazenamento que têm um namespace hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: bbae67a4861d67526eb1cf4eb2bfb5d131f8e57b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649767"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usar a CLI do Azure para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar a [CLI (Interface de Linha de Comando) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm um namespace hierárquico. 

[Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Fornecer comentários](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar um.
> * CLI do Azure versão `2.6.0` ou superior

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Certifique-se de ter a versão correta da CLI do Azure instalada

1. Abra o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) ou, se você [instalou](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a CLI do Azure localmente, abra um aplicativo de console de comando, como Windows PowerShell.

2. Verifique se a versão da CLI do Azure instalada é `2.6.0` ou superior usando o comando a seguir.

   ```azurecli
    az --version
   ```
   Se sua versão da CLI do Azure for inferior a `2.6.0`, instale uma versão posterior. Confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Conectar à conta

1. Se você estiver usando a CLI do Azure localmente, execute o comando de logon.

   ```azurecli
   az login
   ```

   Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

   Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal. Em seguida, entre com suas credenciais de conta no navegador.

   Para saber mais sobre os diferentes métodos de autenticação, confira [Autorizar acesso a dados de BLOB ou fila com a CLI do Azure](../common/authorize-data-operations-cli.md).

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

> [!NOTE]
> O exemplo apresentado neste artigo mostra a autorização do Azure Active Directory (AD). Para saber mais sobre os métodos de autorização, confira [Autorizar o acesso a dados de blob ou fila com a CLI do Azure](../common/authorize-data-operations-cli.md).

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos atua como um contêiner para seus arquivos. Você pode criar um usando o comando `az storage fs create`. 

Este exemplo cria um sistema de arquivos chamado `my-file-system`.

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>Mostrar as propriedades do sistema de arquivos

Você pode imprimir as propriedades de um sistema de arquivos no console do usando o comando `az storage fs show`.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>Listar o conteúdo do sistema de arquivos

Liste o conteúdo de um diretório usando o comando `az storage fs file list`.

Este exemplo lista o conteúdo de um sistema de arquivos chamado `my-file-system`.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>Excluir um sistema de arquivos

Exclui um sistema de arquivos usando o comando `az storage fs delete`.

Este exemplo exclui um sistema de arquivos chamado `my-file-system`. 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório usando o comando `az storage fs directory create`. 

Este exemplo adiciona um diretório chamado `my-directory` a um sistema de arquivos chamado `my-file-system` que está localizado em uma conta chamada `mystorageaccount`.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Obter propriedades do diretório

Você pode imprimir as propriedades de um diretório no console do usando o comando `az storage fs directory show`.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório usando o comando `az storage fs directory move`.

Este exemplo renomeia um diretório do nome `my-directory` para o nome `my-new-directory` no mesmo sistema de arquivos.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Este exemplo move um diretório para um sistema de arquivos chamado `my-second-file-system`.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclui um diretório usando o comando `az storage fs directory delete`.

Este exemplo exclui um diretório chamado `my-directory`. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Verificar se há um diretório

Determine se um diretório específico existe no sistema de arquivos usando o comando `az storage fs directory exists`.

Este exemplo revela se um diretório chamado `my-directory` existe no sistema de arquivos `my-file-system`. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um diretório usando o comando `az storage fs file download`.

Este exemplo baixa um arquivo chamado `upload.txt` de um diretório chamado `my-directory`. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um diretório usando o comando `az storage fs file list`.

Este exemplo lista o conteúdo de um diretório chamado `my-directory` que está localizado no sistema de arquivos `my-file-system` de uma conta de armazenamento chamada `mystorageaccount`. 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Carregue um arquivo em um diretório usando o comando `az storage fs directory upload`.

Este exemplo carrega um arquivo chamado `upload.txt` em um diretório chamado `my-directory`. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Você pode imprimir as propriedades de um arquivo no console do usando o comando `az storage fs file show`.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Renomear ou mover um arquivo

Renomeie ou mova um arquivo usando o comando `az storage fs file move`.

Este exemplo renomeia um arquivo do nome `my-file.txt` para o nome `my-file-renamed.txt`.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Excluir um arquivo

Exclua um arquivo usando o comando `az storage fs file delete`.

Este exemplo exclui um arquivo chamado `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Gerenciar permissões

Você pode obter, definir e atualizar as permissões de acesso de diretórios e arquivos.

> [!NOTE]
> Se você estiver usando o Azure AD (Azure Active Directory) para autorizar comandos, confira se a entidade de segurança foi atribuída com a [função de Proprietário dos dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Obter uma ACL

Obtenha a ACL de um **diretório** usando o comando `az storage fs access show`.

Este exemplo obtém a ACL de um diretório e, em seguida, imprime a ACL no console.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Obtenha as permissões de acesso de um **arquivo**  usando o comando `az storage fs access show`. 

Este exemplo obtém a ACL de um arquivo e, em seguida, imprime a ACL no console.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Neste exemplo, o usuário proprietário tem permissões de leitura, gravação e execução. O grupo proprietário tem permissões de somente leitura e execução. Para obter mais informações sobre as listas de controle de acesso, confira [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Definir uma ACL

Use o comando `az storage fs access set` para definir a ACL de um **diretório**. 

Este exemplo define a ACL em um diretório para o usuário proprietário, o grupo proprietário ou outros usuários e imprime a ACL no console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo define a ACL *padrão* em um diretório para o usuário proprietário, o grupo proprietário ou outros usuários e imprime a ACL no console.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Use o comando `az storage fs access set` para definir a ACL de um **arquivo**. 

Este exemplo define a ACL em um arquivo para o usuário proprietário, o grupo proprietário ou outros usuários e imprime a ACL no console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

A imagem a seguir mostra a saída depois de definir a ACL de um arquivo.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Neste exemplo, o usuário proprietário e o grupo proprietário têm permissões de somente leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre as listas de controle de acesso, confira [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Atualizar uma ACL

Outra maneira de definir esta permissão é usar o comando `az storage fs access set`. 

Atualize a ACL de um diretório ou arquivo definindo o parâmetro `-permissions` como a forma abreviada de uma ACL.

Este exemplo atualiza a ACL de um **diretório**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo atualiza a ACL de um **arquivo**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Você também pode atualizar o usuário proprietário e o grupo de um diretório ou arquivo definindo os parâmetros `--owner` ou `group` para a ID da entidade ou o UPN (Nome Principal do Usuário) de um usuário. 

Este exemplo altera o proprietário de um diretório. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo altera o proprietário de um arquivo. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>Confira também

* [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Fornecer feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


