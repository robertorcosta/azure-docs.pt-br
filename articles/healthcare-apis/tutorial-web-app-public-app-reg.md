---
title: Tutorial do aplicativo Web – Configuração do aplicativo cliente
description: Este tutorial apresenta as etapas para registrar um aplicativo público para preparar-se para implantar um aplicativo Web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870448"
---
# <a name="client-application-registration"></a>Registro de aplicativo cliente
No tutorial anterior, você implantou e configurou sua API do Azure para FHIR. Agora que você tem a configuração da API do Azure para FHIR, vamos registrar um aplicativo cliente público. Você pode ler todo o guia de instruções passo a passo para [registrar um aplicativo cliente público](register-public-azure-ad-client-app.md) para obter mais detalhes ou solução de problemas, mas nós apresentamos as principais etapas para este tutorial abaixo.

1. Navegue para o Azure Active Directory
1. Selecione **Registro de Aplicativo** --> **Novo Registro**
1. Dê um nome ao seu aplicativo e configure o URI de redirecionamento para https://www.getpostman.com/oauth2/callback


![Registro de aplicativo cliente](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Configurações de aplicativo cliente
Depois que o aplicativo cliente for registrado, copie a ID do Aplicativo (cliente) da página Visão Geral. Você precisará desse valor posteriormente ao acessar o cliente.

![Copiar a ID do aplicativo](media/tutorial-web-app/app-id.png)

Em seguida, defina as opções de autenticação corretas. Selecione **Autenticação** no menu à esquerda. Marque as caixas **Token de Acesso** e **Token da ID**. Você também pode configurar o URI de redirecionamento em preparação para quando criar seu aplicativo Web na quarta parte deste tutorial. Para fazer isso, adicione https://\<WEB-APP-NAME>.azurewebsites.net à lista de URI de redirecionamento. Se você escolher um nome diferente durante a etapa de [gravar seu aplicativo Web](tutorial-web-app-write-web-app.md), será necessário voltar e atualizar isso.

![Configurações de autenticação do aplicativo](media/tutorial-web-app/app-authentication.png)

Agora que você configurou a autenticação correta, defina as permissões da API. 
1. Selecione **Permissões de API** e clique em **Adicionar uma permissão**
1. Em **APIs que a minha organização usa**, pesquise as APIs de Saúde do Azure
1. Selecione **user_impersonation** e clique em **adicionar permissões**

## <a name="next-steps"></a>Próximas etapas
Agora você tem um aplicativo cliente público. No próximo tutorial, veremos como testar e obter acesso a esse aplicativo por meio do Postman.

>[!div class="nextstepaction"]
>[Testar aplicativo cliente no Postman](tutorial-web-app-test-postman.md)
