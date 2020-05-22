---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594395"
---
#### <a name="local-proxy"></a>Proxy local

Você pode configurar um proxy para a extensão Visual Studio Code do Servidor Dinâmico que roteia todas as solicitações para `/api` para o ponto de extremidade da API em execução em `http://127.0.0.1:7071/api`.

1. Abra o arquivo _.vscode/settings.json_.

1. Adicione as configurações a seguir para especificar um proxy para o Servidor Dinâmico.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   É melhor salvar essa configuração no arquivo de configurações do projeto, não no arquivo de configurações do usuário.

   O uso das configurações do projeto garante que o proxy não seja aplicado a todos os outros projetos abertos no Visual Studio Code.
