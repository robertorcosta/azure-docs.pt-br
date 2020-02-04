---
title: incluir arquivo
description: incluir arquivo
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772403"
---
### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

Usando sua chave de runtime e o ponto de extremidade de runtime, crie variáveis de ambiente para autenticação e acesso:

* `LUIS_RUNTIME_KEY` – a chave de recurso de runtime para autenticar as solicitações.
* `LUIS_RUNTIME_ENDPOINT` – o ponto de extremidade de runtime associado à chave.
* `LUIS_APP_ID` – a ID do aplicativo IoT do LUIS pública é `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production` ou `staging`

Se pretender usar este início rápido para acessar seu aplicativo, você precisará executar etapas adicionais:
* Criar o aplicativo e obter a ID do aplicativo
* Atribuir a chave de runtime ao aplicativo no portal do LUIS
* Testar a URL com o navegador, com o qual você pode acessar o aplicativo

Use as instruções para seu sistema operacional.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Depois de adicionar as variáveis de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Depois de adicionar as variáveis de ambiente, execute `source ~/.bashrc` na janela do console para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite seu `.bash_profile` e adicione a variável de ambiente:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Depois de adicionar as variáveis de ambiente, execute `source .bash_profile` na janela do console para que as alterações entrem em vigor.

***
