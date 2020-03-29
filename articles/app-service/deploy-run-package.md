---
title: Execute seu aplicativo a partir de um pacote ZIP
description: Implante o pacote ZIP do seu aplicativo com atomicidade. Melhore a previsibilidade e a confiabilidade do comportamento do seu aplicativo durante o processo de implantação do ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920715"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Execute seu aplicativo no Azure App Service diretamente de um pacote ZIP

No [Azure App Service,](overview.md)você pode executar seus aplicativos diretamente a partir de um arquivo zip de implantação. Este artigo mostra como ativar essa funcionalidade em seu aplicativo.

Todos os outros métodos de implantação no App Service têm algo em comum: seus arquivos são implantados no *D:\home\site\wwwroot* em seu aplicativo (ou */home/site/wwwroot* para aplicativos Linux). Uma vez que o mesmo diretório é usado pelo seu aplicativo em tempo de execução, é possível que a implantação falhe por causa de conflitos de bloqueio de arquivos, e para o aplicativo se comportar de forma imprevisível porque alguns dos arquivos ainda não estão atualizados.

Em contraste, quando você é executado diretamente a partir de um pacote, os arquivos no pacote não são copiados para o diretório *wwwroot.* Em vez disso, o próprio pacote ZIP é montado diretamente como o diretório wwwroot somente *leitura.* Existem vários benefícios para correr diretamente de um pacote:

- Elimina conflitos de bloqueio de arquivos entre implantação e tempo de execução.
- Garante que apenas aplicativos totalmente implantados estejam sendo executados a qualquer momento.
- Pode ser implantado em um aplicativo de produção (com reinicialização).
- Melhora o desempenho das implantações do Azure Resource Manager.
- Pode reduzir os tempos de inicialização a frio, particularmente para as funções de JavaScript com árvores de pacote npm grandes.

> [!NOTE]
> Atualmente, apenas arquivos de pacote ZIP são suportados.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Habilite a execução do pacote

A `WEBSITE_RUN_FROM_PACKAGE` configuração do aplicativo permite a execução de um pacote. Para configurá-lo, execute o seguinte comando com o Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`permite que você execute seu aplicativo de um pacote local para o seu aplicativo. Você também pode [ser executado a partir de um pacote remoto](#run-from-external-url-instead).

## <a name="run-the-package"></a>Executar o pacote

A maneira mais fácil de executar um pacote em seu Serviço de Aplicativo é com o comando Azure CLI [az webapp origin de configuração de configuração zip.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) Por exemplo: 

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Como `WEBSITE_RUN_FROM_PACKAGE` a configuração do aplicativo está definida, esse comando não extrai o conteúdo do pacote para o diretório *D:\home\site\wwwroot* do seu aplicativo. Em vez disso, ele carrega o arquivo ZIP como está para *D:\home\data\SitePackages*, e cria um *packagename.txt* no mesmo diretório, que contém o nome do pacote ZIP para carregar em tempo de execução. Se você carregar seu pacote ZIP de uma maneira diferente (como [FTP),](deploy-ftp.md)você precisa criar o diretório *D:\home\data\Site\SitePackages* e o arquivo *packagename.txt* manualmente.

O comando também reinicia o aplicativo. Como `WEBSITE_RUN_FROM_PACKAGE` está definido, o App Service monta o pacote carregado como o diretório *wwwroot* somente leitura e executa o aplicativo diretamente daquele diretório montado.

## <a name="run-from-external-url-instead"></a>Em vez disso, execute-se a partir de URL externo

Você também pode executar um pacote de uma URL externa, como o Azure Blob Storage. Você pode usar o [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote para sua conta de armazenamento de Blobs. Você deve usar um contêiner de armazenamento privado com um [SAS (SAS) de assinatura de acesso compartilhado](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o tempo de execução do App Service acesse o pacote com segurança. 

Depois de carregar seu arquivo no armazenamento Blob e ter `WEBSITE_RUN_FROM_PACKAGE` uma URL SAS para o arquivo, defina a configuração do aplicativo para a URL. O exemplo a seguir faz isso usando o Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se você publicar um pacote atualizado com o mesmo nome para armazenamento Blob, você precisa reiniciar seu aplicativo para que o pacote atualizado seja carregado no App Service.

## <a name="troubleshooting"></a>Solução de problemas

- Ser executado diretamente `wwwroot` a partir de um pacote torna-se somente leitura. Seu aplicativo receberá um erro se tentar gravar arquivos para este diretório.
- Os formatos TAR e GZIP não são suportados.
- Este recurso não é compatível com [cache local](overview-local-cache.md).
- Para melhorar o desempenho de partida a`WEBSITE_RUN_FROM_PACKAGE`frio, use a opção Zip local (=1).

## <a name="more-resources"></a>Mais recursos

- [Implantação contínua do Serviço de Aplicativos Azure](deploy-continuous-deployment.md)
- [Implantar código com um arquivo ZIP ou WAR](deploy-zip.md)
