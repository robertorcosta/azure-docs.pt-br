---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512783"
---
Vamos criar um segredo chamado **mySecret**, com um valor de **Êxito!** . Um segredo pode ser uma senha, uma cadeia de conexão SQL ou qualquer outra informação que você precise manter em segurança e disponível para o aplicativo. 

Para adicionar um segredo ao cofre de chaves recém-criado, use o comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) da CLI do Azure:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```