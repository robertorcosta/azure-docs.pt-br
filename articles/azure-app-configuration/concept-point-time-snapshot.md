---
title: Recuperar pares de chave-valor de um ponto no tempo
titleSuffix: Azure App Configuration
description: Recuperar pares de chave-valor antigos usando instantâneos de ponto no tempo na configuração do Azure App
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523642"
---
# <a name="point-in-time-snapshot"></a>Instantâneo pontual

Azure App configuração mantém um registro das alterações feitas em pares chave-valor. Esse registro fornece uma linha do tempo das alterações de chave-valor. Você pode reconstruir o histórico de qualquer valor-chave e fornecer seu valor passado a qualquer momento nos sete dias anteriores. Usando esse recurso, você pode retroceder "tempo-de-viagem" e recuperar um valor de chave antigo. Por exemplo, você pode recuperar as definições de configuração usadas antes da implantação mais recente a fim de reverter o aplicativo para a configuração anterior.

## <a name="key-value-retrieval"></a>Recuperação de par chave-valor

Você pode usar Azure PowerShell para recuperar valores de chave anteriores.  Use `az appconfig revision list` , adicionando parâmetros apropriados para recuperar os valores necessários.  Especifique a instância de configuração de Azure App fornecendo o nome do repositório ( `--name {app-config-store-name}` ) ou usando uma cadeia de conexão ( `--connection-string {your-connection-string}` ). Restrinja a saída especificando um ponto no tempo específico ( `--datetime` ) e especificando o número máximo de itens a serem retornados ( `--top` ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Recupere todas as alterações gravadas para seus valores de chave.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Recupere todas as alterações gravadas para a chave `environment` e os rótulos `test` e `prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Recupere todas as alterações gravadas no espaço de chave hierárquica `environment:prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Recupere todas as alterações gravadas para a chave `color` em um momento específico.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Recupere as 10 últimas alterações gravadas em seus valores de chave e retorne apenas os valores para `key` , `label` e `last-modified` carimbo de data/hora.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
