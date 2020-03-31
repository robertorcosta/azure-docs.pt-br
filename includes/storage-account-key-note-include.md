---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460570"
---
## <a name="protect-your-access-keys"></a>Proteja suas chaves de acesso

As chaves de acesso à sua conta de armazenamento são semelhantes a uma senha raiz para sua conta de armazenamento. Tenha sempre cuidado para proteger suas chaves de acesso. Use o Azure Key Vault para gerenciar e girar suas chaves com segurança. Evite distribuir chaves de acesso a outros usuários, codificando-as ou salvando-as em qualquer lugar em texto simples que seja acessível a outros. Gire suas chaves se você acredita que elas podem ter sido comprometidas.

Se possível, use o Azure Active Directory (Azure AD) para autorizar solicitações de armazenamento Blob e Fila em vez de Chave Compartilhada. O Azure AD oferece segurança superior e facilidade de uso sobre a Chave Compartilhada. Para obter mais informações sobre como autorizar o acesso aos dados com o Azure AD, consulte [Autorizar o acesso a blobs e filas do Azure usando o Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
