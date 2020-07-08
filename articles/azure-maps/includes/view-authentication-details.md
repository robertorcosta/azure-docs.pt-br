---
title: Exibir detalhes de autenticação do Azure Maps
description: Use o portal do Azure para exibir detalhes de autenticação do Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988302"
---
Você pode exibir os detalhes de autenticação da conta do Azure Maps na portal do Azure. Lá, em sua conta, no menu **configurações** , selecione **autenticação**.

![Detalhes de autenticação](../media/how-to-manage-authentication/how-to-view-auth.png)

Depois que uma conta do Azure Maps é criada, o valor do Azure Maps `x-ms-client-id` está presente na página de detalhes de portal do Azure autenticação. Esse valor representa a conta que será usada para solicitações da API REST. Esse valor deve ser armazenado na configuração do aplicativo e recuperado antes de fazer solicitações HTTP ao usar a autenticação do Azure AD com o Azure Maps.
