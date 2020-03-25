---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694489"
---
## <a name="set-up-authentication"></a>Configurar a autenticação

Para acessar o serviço, você precisa fornecer uma chave de conta, um token de acesso ou um token de autenticação do Azure Active Directory. Leia também mais sobre isso na [página sobre o conceito de autenticação](/azure/spatial-anchors/concepts/authentication).

### <a name="account-keys"></a>Chaves de conta

Chaves de conta são uma credencial que permite que o aplicativo seja autenticado no serviço Âncoras Espaciais do Azure. A finalidade pretendida das chaves de conta é ajudar você com uma rápida introdução. Especialmente durante a fase de desenvolvimento da integração do aplicativo às Âncoras Espaciais do Azure. Assim, você pode usar chaves de conta inserindo-as em seus aplicativos cliente durante o desenvolvimento. À medida que você progride para além do desenvolvimento, é altamente recomendável migrar para um mecanismo de autenticação que seja de nível de produção, com suporte de tokens de acesso ou da autenticação de usuário do Azure Active Directory. Para obter uma chave de conta para desenvolvimento, visite a sua conta das Âncoras Espaciais do Azure e navegue até a guia "Chaves".
