---
title: incluir arquivo
description: incluir arquivo
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 12/06/2019
ms.openlocfilehash: 4d32980e825f12c76b5c8bf8df0673fa82065751
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460407"
---
1. No [portal do Azure](https://ms.portal.azure.com/), selecione **Azure Active Directory** > **Registros de aplicativo** > **Novo registro**.

   [![Novo registro do aplicativo no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Seu aplicativo aparecerá aqui depois que você registrá-lo.

1. Dê um nome ao aplicativo e selecione **Somente contas neste diretório organizacional** para especificar os **Tipos de conta com suporte** que podem acessar a API. Escolha um URI válido para redirecionar os usuários após a autenticação e depois **Registre**.

   [![Criar o aplicativo no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. As informações importantes do aplicativo Azure Active Directory são exibidas na folha **Visão geral** do aplicativo listado. Selecione o aplicativo em **Aplicativos próprios** e depois **Visão geral**.

   [![Copie a ID do aplicativo](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Cópia sua **ID de aplicativo (cliente)** para usar em seu aplicativo cliente.

1. A folha **Autenticação** especifica as configurações de autenticação importantes. 

    1. Os **URIs de Redirecionamento** devem corresponder ao endereço fornecido pela solicitação de autenticação:

        * Para aplicativos hospedados em um ambiente de desenvolvimento local, selecione **Cliente público (móvel e desktop)** . Defina **Tipo de cliente padrão** como "sim".
        * Para aplicativos de página única hospedados no Serviço de Aplicativo do Azure, selecione **Web**.

    1. Habilite o fluxo de concessão implícita verificando **tokens de acesso** ou **tokens de ID**.

   [![criar URIs de redirecionamento e configurar a concessão implícita](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Clique em **Save** (Salvar).

1. Selecione **Certificados e segredos** e, em seguida, **Novo segredo do cliente** para criar uma senha de aplicativo que o cliente possa usar para provar sua identidade.

   [![Criar um novo segredo do cliente](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   A senha do segredo do cliente será exibida. Copie a chave para o seu editor de texto favorito.

   > [!NOTE]
   > Em vez disso, você tem a capacidade de importar um certificado. Para melhorar a segurança, recomenda-se ter um certificado. Para usar um certificado, selecione **Carregar certificado**.

1. Associe seu aplicativo do Azure Active Directory ao Azure Time Series Insights. Selecione **Permissões de API** > **Adicionar uma permissão** > **APIs usadas por minha organização**. 

    [![Associar uma API ao seu aplicativo do Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digite `Azure Time Series Insights` na barra em seguida e selecione `Azure Time Series Insights`.

1. Em seguida, especifique o tipo de permissão de API exigido por seu aplicativo. Por padrão, **Permissões delegadas** estará realçado. Escolha um tipo de permissão e selecione **Adicionar permissões**.

    [![Especifique o tipo de permissão de API exigido por seu aplicativo](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)