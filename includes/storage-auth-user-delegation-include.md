---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118083"
---
## <a name="about-the-user-delegation-sas"></a>Sobre a delegação de usuários SAS

Um token SAS para acesso a um contêiner ou blob pode ser protegido usando credenciais Azure AD ou uma chave de conta. Um SAS protegido com credenciais Azure AD é chamado de delegação de usuário SAS, porque o token OAuth 2.0 usado para assinar o SAS é solicitado em nome do usuário.

A Microsoft recomenda que você use credenciais Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave da conta, que pode ser mais facilmente comprometida. Quando o design do aplicativo exigir assinaturas de acesso compartilhadas, use as credenciais do Azure AD para criar uma delegação de usuários SAS para maior segurança. Para obter mais informações sobre a delegação de usuários SAS, consulte [Criar uma delegação de usuários SAS](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Qualquer cliente que possua um SAS válido pode acessar dados em sua conta de armazenamento conforme permitido por esse SAS. É importante proteger um SAS de uso malicioso ou não intencional. Use discrição na distribuição de um SAS e tenha um plano para revogar um SAS comprometido.

Para obter mais informações sobre assinaturas de acesso compartilhada, consulte [O acesso limitado ao Grant aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS)](../articles/storage/common/storage-sas-overview.md).
