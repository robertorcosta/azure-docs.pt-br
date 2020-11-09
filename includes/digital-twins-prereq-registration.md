---
author: baanders
description: incluir arquivo dos Gêmeos Digitais do Azure – pré-requisito para definir um registro de aplicativo
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124826"
---
Para autenticar todos os recursos usados neste artigo, você precisará configurar um **registro de aplicativo** do [Azure AD (Azure Active Directory)](../articles/active-directory/fundamentals/active-directory-whatis.md). Siga as instruções descritas em [*Como criar um registro de aplicativo*](../articles/digital-twins/how-to-create-app-registration.md) para configurar isso. 

Quando você tiver um registro de aplicativo, precisará da **_ID do Aplicativo (cliente)_** e da **_ID do Diretório (locatário)_** do registro ([encontre-as no portal do Azure](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Anote esses valores para usá-los posteriormente a fim de permitir acesso às APIs dos Gêmeos Digitais do Azure.