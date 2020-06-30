---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095125"
---
#### <a name="no-response-from-the-backend-service"></a>Sem resposta do serviço de back-end

Ao testar localmente, verifique se o serviço de back-end está em execução e está usando a porta correta.

Se estiver testando no **Aplicativo de API do Azure**, verifique se o serviço está em execução e foi implantado e se foi iniciado sem erros.

Verifique se você especificou corretamente o endereço básico no **[Postman](https://www.postman.com/downloads)** ou na configuração de aplicativos móveis durante o teste por meio do cliente. O endereço básico deve ser indicativo de `https://<api_name>.azurewebsites.net/` ou `https://localhost:5001/` ao testar localmente.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Não recebimento de notificações no Android após iniciar ou parar uma sessão de depuração

Registre-se novamente depois de iniciar ou parar uma sessão de depuração. O depurador fará com que um novo token do **Firebase** seja gerado. A instalação do hub de notificação também deve ser atualizada.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Recebimento de um código de status 401 do serviço de back-end

Valide se você está definindo o cabeçalho de solicitação **apikey** e se esse valor corresponde ao que você configurou para o serviço de back-end.

Se você receber esse erro ao testar localmente, verifique se o valor de chave definido na configuração do cliente corresponde ao valor de configuração de usuário **Authentication:ApiKey** usado pela [API](#create-the-api-app).

Se você estiver testando com um **Aplicativo de API**, verifique se o valor da chave no arquivo de configuração do cliente corresponde à configuração de aplicativo de **Authentication:ApiKey** que você está usando no [Aplicativo de API](#create-the-api-app).

> [!NOTE]
> Se você tiver criado ou alterado essa configuração depois de ter implantado o serviço de back-end, deverá reiniciar o serviço para que isso entre em vigor.

Se você optou por não concluir a seção [Autenticar clientes usando uma Chave de API](#authenticate-clients-using-an-api-key-optional), verifique se você não aplicou o atributo **Authorize** à classe **NotificationsController**.

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Recebimento de um código de status 404 do serviço de back-end

Valide se o ponto de extremidade e o método de solicitação HTTP estão corretos. Por exemplo, os pontos de extremidade devem ser:

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

Ou, ao testar localmente:

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

Ao especificar o endereço básico no aplicativo cliente, verifique se ele termina com um `/`. O endereço básico deve ser indicativo de `https://<api_name>.azurewebsites.net/` ou `https://localhost:5001/` ao testar localmente.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Não é possível registrar e uma mensagem de erro do hub de notificação é exibida

Verifique se o dispositivo de teste tem conectividade de rede. Em seguida, determine o código de status de resposta HTTP definindo um ponto de interrupção para inspecionar o valor da propriedade **StatusCode** no **HttpResponse**.

Examine as sugestões de solução de problemas anteriores, quando aplicável com base no código de status.

Defina um ponto de interrupção nas linhas que retornam esses códigos de status específicos para a respectiva API. Em seguida, tente chamar o serviço de back-end ao depurar localmente.

Valide se o serviço de back-end está funcionando conforme o esperado via **[Postman](https://www.postman.com/downloads)** usando o conteúdo apropriado. Use o conteúdo real criado pelo código do cliente para a plataforma em questão.

Examine as seções de configuração específicas da plataforma para garantir que nenhuma etapa tenha sido perdida. Verifique se os valores adequados estão sendo resolvidos para as variáveis `installation id` e `token` para a plataforma correta.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Mensagem de erro Não é possível resolver uma ID para o dispositivo é exibida

Examine as seções de configuração específicas da plataforma para garantir que nenhuma etapa tenha sido perdida.
