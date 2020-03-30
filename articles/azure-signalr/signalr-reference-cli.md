---
title: Exemplos de CLI do Azure - Serviço Azure SignalR
description: Siga amostras reais para aprender a Cli do Azure para o Azure SignalR Service. Você aprenderá como criar o SignalR Service com mais serviços do Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158133"
---
# <a name="azure-cli-reference"></a>Referência da CLI do Azure

A tabela a seguir inclui links para scripts bash para o Serviço do Azure SignalR do Microsoft Azure usando a CLI do Azure.

| | |
|-|-|
|**Criar**||
| [Criar um novo Serviço SignalR e grupo de recurso](scripts/signalr-cli-create-service.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório.  |
|**Integrar**||
| [Criar um novo SignalR Service e o aplicativo Web configurado para usar o SignalR](scripts/signalr-cli-create-with-app-service.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório. Também adiciona um novo plano de Web App e App Service para hospedar um ASP.NET Web App que usa o Serviço SignalR. O aplicativo web é configurado com uma Configuração de Aplicativo para se conectar ao novo recurso de serviço SignalR. |
| [Criar um novo SignalR Service e aplicativo Web configurado para usar o SignalR e o GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Cria um novo recurso do Serviço Azure SignalR em um novo grupo de recursos com um nome aleatório. Também adiciona um novo Azure Web App e um plano de hospedagem para hospedar um ASP.NET Aplicativo Web Core que usa o Serviço SignalR. O aplicativo web está configurado com configurações de aplicativo para a cadeia de caracteres de conexão para o novo recurso de serviço SignalR e os segredos de cliente para oferecer suporte a [autenticação GitHub](https://developer.github.com/v3/guides/basics-of-authentication/) conforme demonstrado no [tutorial de autenticação](signalr-concept-authenticate-oauth.md). O aplicativo Web também é configurado para usar uma fonte de implantação do repositório git local. |
| | |
