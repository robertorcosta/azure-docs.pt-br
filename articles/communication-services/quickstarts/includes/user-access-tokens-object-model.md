---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943237"
---
## <a name="object-model"></a>Modelo de objeto

Os Tokens de Acesso do Usuário permitem que você autorize os usuários do seu aplicativo a se conectarem diretamente ao seu recurso dos Serviços de Comunicação do Azure. Você gera esses tokens no seu servidor, transmite-os de volta para seu aplicativo cliente e os usa para inicializar as bibliotecas de clientes dos Serviços de Comunicação. Para conseguir isso, você deve criar um ponto de extremidade de serviço confiável que possa autenticar seus usuários e emitir tokens de acesso do usuário. Esse serviço deve manter um mapeamento persistente entre as identidades de usuário do aplicativo e os usuários dos Serviços de Comunicação do Azure.

A classe CommunicationIdentityClient fornece toda a funcionalidade relacionada ao usuário e ao token de acesso. Você cria a instância dela com uma cadeia de conexão e a usa para gerenciar usuários e emitir tokens de acesso.

> [!WARNING]
> Tokens são dados confidenciais, pois concedem acesso aos recursos do usuário. Portanto, é essencial proteger os tokens para que não sejam comprometidos. Você deve criar um ponto de extremidade de serviço confiável que só emite tokens de acesso a usuários autorizados do seu aplicativo. Se você armazena em cache tokens de acesso do usuário em um repositório de backup, é altamente recomendável usar a criptografia.

### <a name="access-token-scopes"></a>Escopos do Token de Acesso

Os escopos permitem que você especifique a funcionalidade exata dos Serviços de Comunicações do Azure que um token de acesso do usuário poderá autorizar. Os escopos são aplicados a tokens de acesso do usuário individuais. Os Serviços de Comunicação do Azure dão suporte aos seguintes escopos para tokens de acesso do usuário:

| Name   | Descrição                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Concede a capacidade de participar de um chat                                         |
| `VoIP` | Concede a capacidade de fazer e receber chamadas VOIP usando a biblioteca de clientes de chamada* |
| `Pstn` | Concede a capacidade de fazer chamadas PSTN usando a biblioteca de clientes de chamada*           |

\* Ainda não há suporte para o recurso. Ele estará disponível em breve

Se você quiser remover a capacidade do usuário de acessar algumas funcionalidades específicas, primeiro você deverá [revogar tokens de acesso existentes](#revoke-user-access-tokens) que possam incluir escopos indesejados antes de emitir um novo token com um conjunto de escopos mais limitado.
