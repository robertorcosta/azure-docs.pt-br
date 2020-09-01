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
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147340"
---
 Azure Machine Learning clusters de computação também oferecem suporte a [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o acesso aos recursos do Azure sem incluir credenciais em seu código. Há dois tipos de identidades gerenciadas:

* Uma **identidade gerenciada atribuída pelo sistema** é habilitada diretamente no cluster de computação Azure Machine Learning. O ciclo de vida de uma identidade atribuída pelo sistema está diretamente ligado ao cluster de computação. Se o cluster de computação for excluído, o Azure limpará automaticamente as credenciais e a identidade no Azure AD.
* Uma **identidade gerenciada atribuída pelo usuário** é um recurso autônomo do Azure fornecido por meio do serviço de identidade gerenciada do Azure. Você pode atribuir uma identidade gerenciada atribuída pelo usuário a vários recursos e ela persiste pelo tempo desejado.