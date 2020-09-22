---
title: Recuperar o nome de usuário e a senha para se conectar ao controlador de dados Arc
description: Recuperar o nome de usuário e a senha para se conectar ao controlador de dados Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933174"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Recuperar o nome de usuário e a senha para se conectar ao controlador de dados Arc

Você pode estar em uma situação em que você precisa recuperar o nome de usuário e a senha para o controlador de dados. Esses são os comandos de que você precisa ao executar o. 

```console
azdata login
```

Se você for o administrador do kubernetes para o cluster. Assim, você tem os privilégios para executar comandos a serem recuperados do segredo kubernetes armazena as informações que o arco do Azure persiste lá.

> [!NOTE]
>  Se você usou um nome diferente para o namespace em que o controlador de dados foi criado, certifique-se de alterar o `-n arc` parâmetro nos comandos abaixo para usar o nome do namespace para o qual você criou o controlador de dados.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Execute o seguinte comando para recuperar o nome de usuário:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Execute o seguinte comando para recuperar a senha:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Execute o seguinte comando para recuperar o nome de usuário:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Execute o seguinte comando para recuperar a senha:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Próximas etapas

Experimente outros [cenários](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios)
