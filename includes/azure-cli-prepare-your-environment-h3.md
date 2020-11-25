---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01e1f956270ebbc9b9d5d0ebdfdff52875cafedf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000038"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>Preparar o ambiente para a CLI do Azure

- Use o [Azure Cloud Shell](../articles/cloud-shell/quickstart.md) usando o ambiente bash.

   [![Inserir inicialização](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)   
- Se preferir, [instale](/cli/azure/install-azure-cli) a CLI do Azure para executar comandos de referência da CLI.
   - Se estiver usando uma instalação local, entre com a CLI do Azure usando o comando [az login](/cli/azure/reference-index#az-login).  Para concluir o processo de autenticação, siga as etapas exibidas no terminal.  Confira [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli) para obter outras opções de entrada.
  - Quando solicitado, instale as extensões da CLI do Azure no primeiro uso.  Para obter mais informações sobre extensões, confira [Usar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).
  - Execute [az version](/cli/azure/reference-index?#az_version) para localizar a versão e as bibliotecas dependentes que estão instaladas. Para fazer a atualização para a versão mais recente, execute [az upgrade](/cli/azure/reference-index?#az_upgrade).