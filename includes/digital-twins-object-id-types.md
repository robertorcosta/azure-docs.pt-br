---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307256"
---
O `objectIdType` (ou **tipo de identificador de objeto**) refere-se ao tipo de identidade que é dada a uma função. Além dos tipos `DeviceId` e `UserDefinedFunctionId`, os tipos de identificador de objeto correspondem às propriedades dos objetos do Azure Active Directory.

A tabela a seguir contém os tipos de identificador de objeto com suporte no Gêmeos Digitais do Azure:

| Digite | DESCRIÇÃO |
| --- | --- |
| UserId | Atribui uma função a um usuário. |
| deviceId | Atribui uma função a um dispositivo. |
| DomainName | Atribui uma função a um nome de domínio. Cada usuário com o nome de domínio especificado possui os direitos de acesso da função correspondente. |
| TenantId | Atribui uma função a um locatário. Cada usuário que pertence ao ID de locatário do Microsoft Azure Active Directory especificado tem os direitos de acesso da função correspondente. |
| ServicePrincipalId | Atribui uma função a uma ID de objeto de entidade de serviço. |
| UserDefinedFunctionId | Atribui uma função a uma UDF (função definida pelo usuário). |