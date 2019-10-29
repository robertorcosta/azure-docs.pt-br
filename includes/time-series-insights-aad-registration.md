---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 09/24/2019
ms.openlocfilehash: 7e67b9e1c611d14b78db53f8662fb4e1f493a8df
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990190"
---
> [!IMPORTANT]
> * A nova folha **Azure Active Directory** > **Registros do aplicativo** substitui a folha herdada **Azure Active Directory** > **Registros de aplicativo (Herdado)** de maio de 2019.
> * Registros de aplicativo criados ou exibidos na folha herdada serão exibidos automaticamente na nova folha.
> * Para obter informações abrangentes sobre como migrar para a nova experiência de Registro de Aplicativo do Azure, leia o [Guia de treinamento de registros de aplicativo do Azure](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) e [Início Rápido do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. No [portal do Azure](https://ms.portal.azure.com/), selecione **Azure Active Directory** > **Registros de aplicativo** > **Novo registro**.

   [![Novo registro do aplicativo no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > O novo painel de Registro de aplicativo do Azure Active Directory permite que você filtre os aplicativos exibidos selecionando os **Aplicativos próprios**.

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

    1. Habilite o fluxo de concessão implícita marcando **Tokens de ID**.

   [![Criar um novo segredo do cliente](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

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