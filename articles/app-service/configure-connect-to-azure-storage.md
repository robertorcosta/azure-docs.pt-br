---
title: Adicionar armazenamento personalizado (contêiner Windows)
description: Saiba como anexar o compartilhamento personalizado de rede em um contêiner personalizado do Windows no Azure App Service. Compartilhar arquivos entre aplicativos, gerenciar conteúdo estático remotamente e acessar localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120676"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configure arquivos Azure em um contêiner do Windows no serviço de aplicativos

> [!NOTE]
> Este artigo se aplica a contêineres windows personalizados. Para implantar no App Service no _Linux,_ consulte [Servir conteúdo do Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Este guia mostra como acessar o Armazenamento Azure em Contêineres Windows. Apenas [compartilhamentos de arquivos azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e [arquivos premium são](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) suportados. Você usa compartilhamentos de arquivos do Azure neste how-to. Os benefícios incluem conteúdo seguro, portabilidade de conteúdo, acesso a vários aplicativos e vários métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).
- [Um aplicativo de contêiner windows existente no Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Criar compartilhamento de arquivos Do Zure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Enviar arquivos para o compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> O Azure Files é um armazenamento não padrão e cobrado separadamente, não incluído no aplicativo web. Ele não suporta o uso da configuração do Firewall devido a limitações de infra-estrutura.
>

## <a name="limitations"></a>Limitações

- O Armazenamento do Azure em contêineres windows está **em pré-visualização** e **não é suportado** para **cenários de produção.**
- O armazenamento do Azure em contêineres do Windows suporta a montagem apenas **de contêineres azure Files** (Read /Write).
- O Armazenamento do Azure em contêineres windows não é **suportado** no momento para trazer seus próprios cenários de código nos planos do Windows App Service.
- O Armazenamento do Azure em contêineres do Windows **não suporta** o uso da configuração **do Firewall** de armazenamento devido a limitações de infra-estrutura.
- O Armazenamento do Azure em contêineres do Windows permite especificar **até cinco** pontos de montagem por aplicativo.
- O Azure Storage montado em um aplicativo não é acessível através de pontos finais do App Service FTP/FTPs. Use [o explorador de armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/).
- O Azure Storage é cobrado de forma independente e **não está incluído** no seu aplicativo web. Saiba mais sobre [os preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Armazenamento de link para seu aplicativo Web (versão prévia)

 Para montar um Compartilhamento de Arquivos Azure em um diretório [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) no aplicativo App Service, você usa o comando. O tipo de armazenamento deve ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Você deve fazer isso para qualquer outro diretório que você deseja estar vinculado a um compartilhamento de arquivos do Azure.

## <a name="verify"></a>Verificar

Uma vez que um compartilhamento de Arquivos Do Azure esteja vinculado a um aplicativo web, você pode verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Próximas etapas

- [Migrar um aplicativo ASP.NET para o Azure App Service usando um contêiner do Windows (Preview)](app-service-web-tutorial-windows-containers-custom-fonts.md).
