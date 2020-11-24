---
title: incluir arquivo
description: incluir arquivo
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555335"
---
 Azure Machine Learning clusters de computação também oferecem suporte a [identidades gerenciadas](../articles/active-directory/managed-identities-azure-resources/overview.md) para autenticar o acesso aos recursos do Azure sem incluir credenciais em seu código. Há dois tipos de identidades gerenciadas:

* Uma **identidade gerenciada atribuída pelo sistema** é habilitada diretamente no cluster de computação Azure Machine Learning. O ciclo de vida de uma identidade atribuída pelo sistema está diretamente ligado ao cluster de computação. Se o cluster de computação for excluído, o Azure limpará automaticamente as credenciais e a identidade no Azure AD.
* Uma **identidade gerenciada atribuída pelo usuário** é um recurso autônomo do Azure fornecido por meio do serviço de identidade gerenciada do Azure. Você pode atribuir uma identidade gerenciada atribuída pelo usuário a vários recursos e ela persiste pelo tempo desejado.