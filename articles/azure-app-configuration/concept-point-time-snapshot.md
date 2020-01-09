---
title: Instantâneo no momento da configuração do Azure App
description: Uma visão geral de como funciona o instantâneo pontual na Configuração de Aplicativo do Azure
services: azure-app-configuration
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4db52ce1897aa5a2b809cb7044b9764baffd0767
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495265"
---
# <a name="point-in-time-snapshot"></a>Instantâneo pontual

A Configuração de Aplicativo do Azure manterá os registros de horas precisas quando um novo par chave-valor for criado e, em seguida, modificado. Esses registros formam uma linha do tempo completa nas alterações de pares chave-valor. Um repositório de Configuração de Aplicativos pode reconstruir o histórico de qualquer par chave-valor e reproduzir seu valor anterior em qualquer momento determinado, até o presente. Com esse recurso, é possível “voltar no tempo” e recuperar um par chave-valor antigo. Por exemplo, você pode obter as configurações de ontem, logo antes da implantação mais recente, para recuperar uma configuração anterior e reverter o aplicativo.

## <a name="key-value-retrieval"></a>Recuperação de par chave-valor

Para recuperar os pares chave-valor anteriores, especifique um horário no qual os pares chave-valor sejam instantâneos no cabeçalho HTTP de uma chamada à API REST. Por exemplo:

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

Atualmente, a Configuração de Aplicativo mantém sete dias de histórico de alterações.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
