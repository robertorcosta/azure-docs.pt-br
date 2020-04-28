---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78185821"
---
## <a name="guidelines-for-using-javascript"></a>Diretrizes para usar JavaScript

Siga estas diretrizes ao personalizar a interface do seu aplicativo usando JavaScript:

- Não associe um evento de clique nos elementos HTML `<a>`.
- Não use uma dependência no código do Azure AD B2C ou comentários.
- Não altere a ordem ou a hierarquia dos elementos HTML do Azure AD B2C. Use uma política do Azure AD B2C para controlar a ordem dos elementos da UI (interface do usuário).
- Você pode chamar qualquer serviço RESTful com estas considerações:
    - Talvez seja necessário definir o CORS do serviço RESTful para permitir chamadas HTTP do lado do cliente.
    - Verifique se o serviço RESTful é seguro e usa apenas o protocolo HTTPS.
    - Não use o JavaScript diretamente para chamar pontos de extremidade do Azure AD B2C.
- É possível incorporar o JavaScript ou vincular a arquivos JavaScript externos. Ao usar um arquivo JavaScript externo, certifique-se de usar a URL absoluta e não uma URL relativa.
- Estruturas do JavaScript:
    - O Azure AD B2C usa uma versão específica do jQuery. Não inclua outra versão do jQuery. Usar mais de uma versão na mesma página causa problemas.
    - Não há suporte para uso de RequireJS.
    - O Azure AD B2C não dá suporte para a maioria das estruturas do JavaScript.
- As configurações do Azure AD B2C podem ser lidas chamando objetos `window.SETTINGS`, `window.CONTENT`, como o idioma da interface do usuário atual. Não altere o valor desses objetos.
- Para personalizar a mensagem de erro do Azure AD B2C, use a localização em uma política.
- Se algo puder ser obtido usando uma política, geralmente é a maneira recomendada.
