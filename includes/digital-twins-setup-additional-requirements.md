---
author: baanders
description: incluir arquivo para possíveis requisitos adicionais na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 230304677b78f00b2d1288c846f8bf704cd8a497
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407397"
---
É possível que sua organização exija ações adicionais de proprietários de assinatura para configurar com êxito um registro de aplicativo (e, portanto, concluir a configuração de uma instância gêmeos do Azure digital utilizável). As etapas necessárias podem variar dependendo das configurações específicas da sua organização.

Aqui estão algumas atividades potenciais comuns que um proprietário pode precisar executar. Essas e outras operações podem ser executadas na página [*aplicativo Azure ad registros*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) no portal do Azure.
* Conceda consentimento de administrador para o registro do aplicativo. Sua organização pode ter *consentimento de administrador necessário* ativado globalmente no Azure ad para todos os registros de aplicativo em sua assinatura. Nesse caso, o proprietário precisará selecionar esse botão para sua empresa na página de *permissões de API* do registro de aplicativo para que o registro do aplicativo seja válido:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="Exibição do portal do botão "conceder consentimento de administrador" em permissões de API":::
  - Se o consentimento tiver sido concedido com êxito, a entrada para o gêmeos digital do Azure deverá mostrar um valor de *status* de _concedido para **(sua empresa)** _
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="Exibição do portal do consentimento do administrador concedido para a empresa em permissões de API":::
* Ativar acesso de cliente público
* Definir URLs de resposta específicas para acesso à Web e à área de trabalho
* Permitir fluxos de autenticação OAuth2 implícitos

Para obter mais informações sobre o registro de aplicativo e suas opções de configuração diferentes, consulte [*registrar um aplicativo com a plataforma de identidade da Microsoft*](https://docs.microsoft.com/graph/auth-register-app-v2).

Agora você tem uma instância de gêmeos digital do Azure pronta para uso, atribuiu permissões para gerenciá-la e configurou permissões para um aplicativo cliente acessá-la.