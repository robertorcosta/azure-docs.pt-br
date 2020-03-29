---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371762"
---
Uma assinatura de acesso compartilhado (SAS) permite que você conceda acesso limitado a contêineres e blobs em sua conta de armazenamento. Quando você cria um SAS, você especifica suas restrições, incluindo quais recursos do Azure Storage um cliente pode acessar, quais permissões eles têm sobre esses recursos e quanto tempo o SAS é válido.

Toda SAS é assinada com uma chave. Você pode assinar um SAS de duas maneiras:

- Com uma chave criada usando credenciais do Azure Active Directory (Azure AD). Um SAS que é assinado com credenciais Azure AD é uma *delegação de usuários* SAS.
- Com a chave da conta de armazenamento. Tanto um *SAS de serviço* quanto um *SAS de conta* são assinados com a chave da conta de armazenamento.

Uma delegação de usuários SAS oferece segurança superior a um SAS que é assinado com a chave da conta de armazenamento. A Microsoft recomenda o uso de uma delegação de usuários SAS quando possível. Para obter mais informações, consulte [Grant acesso limitado a dados com assinaturas de acesso compartilhado (SAS)](../articles/storage/common/storage-sas-overview.md).
