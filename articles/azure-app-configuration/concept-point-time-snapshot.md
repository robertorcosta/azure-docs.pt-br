---
title: Recuperar pares de valor-chave a partir de um ponto no tempo
titleSuffix: Azure App Configuration
description: Recupere pares antigos de valor de chave usando instantâneos point-in-time na configuração do aplicativo Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523642"
---
# <a name="point-in-time-snapshot"></a>Instantâneo pontual

A configuração do aplicativo Azure mantém um registro de alterações feitas em pares de valores-chave. Este registro fornece uma linha do tempo de alterações de valor de chave. Você pode reconstruir a história de qualquer valor-chave e fornecer seu valor passado a qualquer momento nos sete dias anteriores. Usando esse recurso, você pode "viajar no tempo" para trás e recuperar um valor-chave antigo. Por exemplo, você pode recuperar as configurações usadas antes da implantação mais recente, a fim de reverter o aplicativo para a configuração anterior.

## <a name="key-value-retrieval"></a>Recuperação de par chave-valor

Você pode usar o Azure PowerShell para recuperar valores-chave passados.  Use `az appconfig revision list`, adicionando parâmetros apropriados para recuperar os valores necessários.  Especifique a instância de configuração do`--name {app-config-store-name}`aplicativo Azure fornecendo o nome da loja ( ) ou usando uma seqüência de conexões ().`--connection-string {your-connection-string}` Restringir a saída especificando um ponto`--datetime`específico no tempo ( ) e especificando`--top`o número máximo de itens a retornar ( ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Recupere todas as alterações gravadas em seus valores-chave.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Recupere todas as alterações gravadas para a chave `environment` e os rótulos `test` e `prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Recuperar todas as alterações registradas `environment:prod`no espaço-chave hierárquico .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Recupere todas as alterações gravadas para a chave `color` em um ponto específico no tempo.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Recupere as últimas 10 alterações registradas em `key`seus `label`valores-chave e retorne apenas os valores para , e `last-modified` carimbo de hora.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
