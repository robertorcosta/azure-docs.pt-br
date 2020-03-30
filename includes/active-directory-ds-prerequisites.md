---
title: incluir arquivo
description: Incluir arquivo com os pré-requisitos
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426843"
---
> [!IMPORTANT]
> **Habilite a sincronização de hash de senha para Azure Active Directory Domain Services, antes de concluir as tarefas neste artigo.**
>
> Siga as instruções a seguir, dependendo do tipo de usuários em seu diretório do Microsoft Azure Active Directory. Se você tiver uma combinação de contas de usuário somente em nuvem e sincronizadas em seu diretório do Microsoft Azure Active Directory conclua ambos os conjuntos de instruções. Você pode não ser capaz de realizar as seguintes operações no caso de você estar tentando usar uma conta b2B Guest (exemplo , seu gmail ou MSA de um provedor de identidade diferente que permitimos) porque não temos a senha para esses usuários sincronizados com domínio gerenciado como estes são contas de hóspedes no diretório. As informações completas sobre essas contas, incluindo suas senhas, estariam fora do Azure AD e, como essas informações não estão no Azure AD, portanto, ela nem sequer é sincronizada com o domínio gerenciado. 
> - [Instruções para contas de usuário somente na nuvem](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruções para as contas de usuário sincronizadas de um diretório local](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
