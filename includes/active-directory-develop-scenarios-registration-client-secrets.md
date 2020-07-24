---
title: incluir arquivo
description: arquivo de inclusão para páginas de aterrissagem do cenário de cliente confidencial (daemon, aplicativo Web, API da Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102439"
---
## <a name="register-secrets-or-certificates"></a>Registrar segredos ou certificados

Para qualquer aplicativo cliente confidencial, você precisa registrar um segredo ou certificado. Você pode registrar os segredos do aplicativo por meio da experiência interativa no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) ou usando ferramentas de linha de comando (como o PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrar segredos do cliente usando o portal de registro de aplicativos

O gerenciamento de credenciais do cliente ocorre na página **certificados & segredos** de um aplicativo:

![Página de segredos de certificados &](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Você cria um *segredo do cliente* selecionando **novo segredo do cliente** no registro do aplicativo no portal do Azure. Ao criar um segredo do cliente, você _deve_ registrar a cadeia de caracteres do segredo antes de navegar para fora do painel **certificados & segredos** . A cadeia de caracteres do segredo nunca é exibida novamente.
- Durante o registro do aplicativo, você usa o botão **carregar certificado** para carregar o certificado. O Azure AD dá suporte apenas a certificados diretamente registrados no aplicativo e não seguem cadeias de certificados.

Para obter detalhes, consulte [início rápido: configurar um aplicativo cliente para acessar APIs Web | Adicione credenciais ao seu aplicativo](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).

### <a name="register-client-secrets-by-using-powershell"></a>Registrar segredos do cliente usando o PowerShell

Como alternativa, você pode registrar seu aplicativo com o Azure AD usando ferramentas de linha de comando. O exemplo [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) mostra como registrar um segredo do aplicativo ou um certificado com um aplicativo do Azure AD:

- Para obter detalhes sobre como registrar um segredo do aplicativo, consulte [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Para obter detalhes sobre como registrar um certificado com um aplicativo, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
