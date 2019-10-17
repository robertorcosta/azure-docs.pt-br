---
title: Executar o Azure Functions de um pacote | Microsoft Docs
description: Faça com que o Azure Functions Runtime execute suas funções montando um arquivo de pacote de implantação que contém os arquivos de projeto do aplicativo de funções.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 549768473460dcb97b66c3589d71c02039220605
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389962"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Executar o Azure Functions de um arquivo de pacote

No Azure, você pode executar suas funções diretamente de um arquivo de pacote de implantação no aplicativo de funções. A outra opção é implantar os arquivos no diretório `d:\home\site\wwwroot` do aplicativo de funções.

Este artigo descreve os benefícios de executar suas funções de um pacote. Ele também mostra como habilitar essa funcionalidade em seu aplicativo de funções.

> [!IMPORTANT]
> Ao implantar suas funções em um aplicativo de funções do Linux em um [plano Premium](functions-scale.md#premium-plan), você deve sempre executar a partir do arquivo de pacote e [publicar seu aplicativo usando o Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios da execução de um arquivo de pacote
  
Há vários benefícios na execução de um arquivo de pacote:

+ Reduz o risco de problemas de bloqueio de cópia de arquivo.
+ Pode ser implantado em um aplicativo de produção (com reinicialização).
+ Você pode ter certeza dos arquivos que estão em execução no seu aplicativo.
+ Melhora o desempenho das [implantações do Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir os tempos de inicialização a frio, particularmente para as funções de JavaScript com árvores de pacote npm grandes.

Para saber mais, veja [este comunicado](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Habilitando as funções para execução de um pacote

Para habilitar seu aplicativo de funções para execução de um pacote, basta adicionar a configuração `WEBSITE_RUN_FROM_PACKAGE` às configurações do aplicativo de funções. A configuração `WEBSITE_RUN_FROM_PACKAGE` pode ter um dos seguintes valores:

| Value  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicativos de funções em execução no Windows. Execute de um arquivo de pacote na pasta `d:\home\data\SitePackages` do seu aplicativo de funções. Se não estiver [implantando com a implantação de zip](#integration-with-zip-deployment), essa opção exigirá que a pasta também tenha um arquivo chamado `packagename.txt`. Esse arquivo contém apenas o nome do arquivo de pacote na pasta, sem espaços em branco. |
|**`<url>`**  | Localização de um arquivo de pacote específico que você deseja executar. Ao usar o armazenamento de Blobs, você deve usar um contêiner privado com uma [SAS (Assinatura de Acesso Compartilhado)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para habilitar o tempo de execução do Functions para acessar o pacote. Você pode usar o [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote para sua conta de armazenamento de Blobs.         |

> [!CAUTION]
> Ao executar um aplicativo de funções no Windows, a opção de URL externa gera um pior desempenho de inicialização a frio. Ao implantar seu aplicativo de funções no Windows, você deve definir `WEBSITE_RUN_FROM_PACKAGE` como `1` e publicar com a implantação de zip.

O exemplo a seguir mostra um aplicativo de funções configurado para ser executado de um arquivo .zip hospedado no armazenamento de Blobs do Azure:

![Configuração de aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> No momento, apenas arquivos de pacote .zip têm suporte.

## <a name="integration-with-zip-deployment"></a>Integração com a implantação do zip

A [implantação de zip][Zip deployment for Azure Functions] é um recurso do serviço de Azure app que permite que você implante seu projeto de aplicativo de funções no diretório `wwwroot`. O projeto é empacotado como um arquivo de implantação .zip. As mesmas APIs podem ser usadas para implantar seu pacote na pasta `d:\home\data\SitePackages`. Com o valor de configuração de aplicativo `WEBSITE_RUN_FROM_PACKAGE` de `1`, as APIs de implantação zip copiam seu pacote para a pasta `d:\home\data\SitePackages` em vez de extrair os arquivos para `d:\home\site\wwwroot`. Ele também cria o arquivo `packagename.txt`. Após uma reinicialização, o pacote é montado em `wwwroot` como um sistema de arquivos somente leitura. Para obter mais informações sobre a implantação do zip, consulte [Implantação de zip para o Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Adicionando a configuração WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Solução de Problemas

- Executar do pacote torna `wwwroot` somente leitura, portanto, você receberá um erro ao gravar arquivos nesse diretório.
- Não há suporte para formatos tar e gzip.
- Esse recurso não compõe o cache local.
- Para obter um desempenho de inicialização a frio aprimorado, use a opção de zip local (`WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
