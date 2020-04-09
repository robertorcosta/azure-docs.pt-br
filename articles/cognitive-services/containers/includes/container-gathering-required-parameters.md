---
title: Coleta de parâmetros necessários
services: cognitive-services
author: aahill
manager: nitinme
description: Os parâmetros para todos os contêineres dos Serviços Cognitivos
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875020"
---
## <a name="gathering-required-parameters"></a>Coleta de parâmetros necessários

Existem três parâmetros primários para todos os contêineres dos Serviços Cognitivos que são necessários. O contrato de licença do usuário final (EULA) deve estar presente com um valor de `accept`. Além disso, tanto uma URL endpoint quanto uma chave aPI são necessárias.

### <a name="endpoint-uri-endpoint_uri"></a>URI de ponto final`{ENDPOINT_URI}`

O valor **URI endpoint** está disponível na página *Visão Geral* do portal Azure do recurso correspondente do Serviço Cognitivo. Navegue até a página *Visão Geral,* paire `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> sobre o Ponto Final e um ícone aparecerá. Copie e use quando necessário.

![Reúna o uri ponto final para uso posterior](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chaves`{API_KEY}`

Esta chave é usada para iniciar o contêiner e está disponível na página Keys do portal Azure do recurso correspondente do Serviço Cognitivo. Navegue *Keys* até a página Chaves `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> e clique no ícone.

![Obter uma das duas chaves para uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de Serviço Cognitivo. Não compartilhe suas chaves. Armazená-los com segurança, por exemplo, usando o Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada de API. Ao regenerar a primeira tecla, você pode usar a segunda chave para acesso contínuo ao serviço.