---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471476"
---
Se você quiser que os usuários sejam solicitados para um segundo fator de autenticação antes de conceder acesso, você pode configurar o MFA (Multi-Factor Authentication, autenticação multifatorial) do Azure. Você pode configurar o MFA por usuário, ou pode aproveitar o MFA via [Conditional Access](../articles/active-directory/conditional-access/overview.md).

* O MFA por usuário pode ser ativado a um custo adicional. Ao habilitar o MFA por usuário, o usuário será solicitado para autenticação de segundo fator contra todos os aplicativos vinculados ao inquilino Azure AD. Consulte [a Opção 1](#peruser) para obter etapas.
* O Acesso Condicional permite um controle mais fino sobre como um segundo fator deve ser promovido. Ele pode permitir a atribuição de MFA apenas para VPN e excluir outros aplicativos vinculados ao inquilino Azure AD. Consulte [a Opção 2](#conditional) para obter etapas.