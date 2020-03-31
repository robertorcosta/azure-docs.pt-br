---
title: Sirva conteúdo do Azure Storage para contêineres Linux
description: Saiba como anexar compartilhamento de rede personalizado ao seu contêiner Linux no Azure App Service. Compartilhar arquivos entre aplicativos, gerenciar conteúdo estático remotamente e acessar localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297910"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Servir conteúdo do Armazenamento do Microsoft Azure no Serviço de Aplicativo no Linux

> [!NOTE]
> Este artigo se aplica a contêineres Linux. Para implantar em contêineres windows personalizados, consulte [Configurar arquivos azure em um contêiner do Windows no Serviço de Aplicativos](../configure-connect-to-azure-storage.md). O Azure Storage in App Service no Linux é um recurso **de pré-visualização.** Este recurso não é **suportado para cenários de produção.**
>

Este guia mostra como anexar o Azure Storage ao App Service no Linux. Os benefícios incluem conteúdo protegido, portabilidade de conteúdo, armazenamento persistente, acesso a vários aplicativos e vários métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).
- Um aplicativo existente [no Aplicativo Linux](https://docs.microsoft.com/azure/app-service/containers/).
- [Uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Um [compartilhamento de arquivos e diretório do Azure](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Limitações do armazenamento azure com serviço de aplicativo

- O Azure Storage with App Service está **em pré-visualização** para o App Service no Linux e no Web App for Containers. Não é **suportado** para **cenários de produção.**
- O Azure Storage with App Service suporta a montagem de **contêineres de arquivos Azure** (Read /Write) e **recipientes Azure Blob** (Somente leitura)
- O Azure Storage with App Service **não suporta** o uso da configuração do **Storage Firewall** devido a limitações de infra-estrutura.
- O Azure Storage com o App Service permite especificar **até cinco** pontos de montagem por aplicativo.
- O Azure Storage montado em um aplicativo não é acessível através de pontos finais do App Service FTP/FTPs. Use [o explorador de armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/).
- O Azure Storage não está **incluído** no seu aplicativo web e é cobrado separadamente. Saiba mais sobre [os preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> As configurações do App Service usando o Azure Blob Storage serão lidas apenas em fevereiro de 2020. [Saiba mais](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Configure seu aplicativo com o Azure Storage

Depois de criar sua [conta de armazenamento Azure, compartilhamento de arquivos e diretório,](#prerequisites)agora você pode configurar seu aplicativo com o Azure Storage.

Para montar uma conta de armazenamento em um diretório [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) no aplicativo App Service, use o comando. Tipo de armazenamento pode ser AzureBlob ou AzureFiles. O AzureFiles é usado neste exemplo.


> [!CAUTION]
> O diretório especificado como o caminho de montagem em seu aplicativo web deve estar vazio. Qualquer conteúdo armazenado neste diretório será excluído quando um suporte externo for adicionado. Se você estiver migrando arquivos para um aplicativo existente, faça um backup do seu aplicativo e de seu conteúdo antes de começar.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Você deve fazer isso para qualquer outro diretório que deseja vincular a uma conta de armazenamento.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verifique o link de armazenamento do Azure para o aplicativo web

Depois que um contêiner de armazenamento estiver vinculado a um aplicativo da Web, você poderá verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Use o armazenamento Azure no Docker Compose

O Azure Storage pode ser montado com aplicativos de vários contêineres usando o id personalizado. Para ver o nome de [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)id personalizado, execute .

No seu arquivo *docker-compose.yml,* mapeie a `volumes` opção para `custom-id`. Por exemplo: 

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Próximas etapas

- [Configure aplicativos web no Azure App Service](../configure-common.md).

