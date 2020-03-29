---
title: incluir arquivo
description: incluir arquivo para páginas de aterrissagem de cenários confidenciais do cliente (daemon, web app, Web API)
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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773373"
---
## <a name="register-secrets-or-certificates"></a>Registre segredos ou certificados

Quanto a qualquer solicitação de cliente confidencial, você precisa registrar um segredo ou certificado. Você pode registrar seus segredos de aplicativo através da experiência interativa no [portal Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) ou usando ferramentas de linha de comando (como o PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registre os segredos do cliente usando o portal de registro de aplicativos

O gerenciamento de credenciais de clientes acontece na página **de segredos de Certificados &** para um aplicativo:

![Certificados & página de segredos](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- O segredo do aplicativo (também chamado de segredo do cliente) é gerado pelo Azure AD durante o registro do aplicativo cliente confidencial. Essa geração acontece quando você seleciona **novo cliente secreto.** Nesse ponto, você deve copiar a seqüência secreta na área de transferência para uso em seu aplicativo, antes de selecionar **Salvar**. Esta seqüência não será apresentada por mais tempo.
- Durante o registro da inscrição, você usa o botão **Upload certificado** para carregar o certificado. O Azure AD suporta apenas certificados que estão diretamente registrados no aplicativo e não seguem cadeias de certificados.

Para obter detalhes, consulte [Quickstart: Configure um aplicativo cliente para acessar APIs da Web | Adicione credenciais ao seu aplicativo](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Registre segredos do cliente usando o PowerShell

Alternativamente, você pode registrar seu aplicativo no Azure AD usando ferramentas de linha de comando. A amostra [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) mostra como registrar um aplicativo secreto ou certificado com um aplicativo Azure AD:

- Para obter detalhes sobre como registrar um segredo de aplicativo, consulte [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Para obter detalhes sobre como registrar um certificado com um aplicativo, consulte [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
