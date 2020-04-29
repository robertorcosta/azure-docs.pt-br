---
title: Adicionar armazenamento personalizado (contêiner do Windows)
description: Saiba como anexar um compartilhamento de rede personalizado em um contêiner do Windows personalizado no serviço Azure App. Compartilhar arquivos entre aplicativos, gerenciar conteúdo estático remotamente e acessar localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77120676"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurar arquivos do Azure em um contêiner do Windows no serviço de aplicativo

> [!NOTE]
> Este artigo se aplica a contêineres personalizados do Windows. Para implantar o serviço de aplicativo no _Linux_, consulte [fornecer conteúdo do armazenamento do Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

Este guia mostra como acessar o armazenamento do Azure em contêineres do Windows. Somente [compartilhamentos de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e [compartilhamentos de arquivos Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) têm suporte. Você usa compartilhamentos de arquivos do Azure nestas instruções. Os benefícios incluem conteúdo seguro, portabilidade de conteúdo, acesso a vários aplicativos e vários métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).
- [Um aplicativo de contêiner do Windows existente no serviço Azure App](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Criar compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Carregar arquivos no compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Os arquivos do Azure são armazenamento não padrão e cobrados separadamente, não incluídos no aplicativo Web. Ele não dá suporte ao uso da configuração de firewall devido a limitações de infraestrutura.
>

## <a name="limitations"></a>Limitações

- O armazenamento do Azure em contêineres do Windows está **em visualização** e **não tem suporte** para **cenários de produção**.
- O armazenamento do Azure em contêineres do Windows dá suporte à montagem de **contêineres de arquivos do Azure** (leitura/gravação) somente.
- No momento, o armazenamento do Azure em contêineres do Windows **não tem suporte** para trazer seus próprios cenários de código nos planos do serviço de aplicativo do Windows.
- O armazenamento do Azure em contêineres do Windows **não dá suporte ao** uso da configuração de **Firewall de armazenamento** devido a limitações de infraestrutura.
- O armazenamento do Azure em contêineres do Windows permite especificar **até cinco** pontos de montagem por aplicativo.
- O armazenamento do Azure montado em um aplicativo não pode ser acessado por pontos de extremidade de FTP/FTPs do serviço de aplicativo. Use o [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).
- O armazenamento do Azure é cobrado de forma independente e **não é incluído** em seu aplicativo Web. Saiba mais sobre os [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Armazenamento de link para seu aplicativo Web (versão prévia)

 Para montar um compartilhamento de arquivos do Azure em um diretório em seu aplicativo do serviço de aplicativo [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) , use o comando. O tipo de armazenamento deve ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Você deve fazer isso para todos os outros diretórios que você deseja que sejam vinculados a um compartilhamento de arquivos do Azure.

## <a name="verify"></a>Verificar

Depois que um compartilhamento de arquivos do Azure estiver vinculado a um aplicativo Web, você poderá verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Próximas etapas

- [Migre um aplicativo ASP.net para Azure app serviço usando um contêiner do Windows (versão prévia)](app-service-web-tutorial-windows-containers-custom-fonts.md).
