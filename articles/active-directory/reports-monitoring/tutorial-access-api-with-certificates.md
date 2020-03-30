---
title: Tutorial para API de reportagem ad com certificados | Microsoft Docs
description: Este tutorial explica como usar a API de Relatório do Azure AD com credenciais de certificado para obter dados de diretórios sem intervenção do usuário.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d723af5d994006c4ae4f90905ede73fa87326bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014259"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutorial: Obter dados usando a API de Relatório do Azure Active Directory com certificados

As [APIs de relatório do Azure AD](concept-reporting-api.md) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação. Se você quiser acessar a API de Relatório do Azure AD sem intervenção do usuário, deverá configurar o acesso para usar certificados.

Neste tutorial, você aprende como usar um certificado de teste para acessar a API do Graph para relatórios. Não recomendamos o uso de certificados de teste em um ambiente de produção. 

## <a name="prerequisites"></a>Pré-requisitos

1. Para acessar dados de entrada, verifique se você tem um locatário do Azure Active Directory com uma licença premium (P1/P2). Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory. Observe que, se você não tiver dados de atividades antes da atualização, serão necessários alguns dias para que os dados sejam exibidos nos relatórios depois que você atualizar para uma licença premium. 

2. Crie ou alterne para uma conta de usuário na função de **administrador global**, **administrador de segurança**, **leitor de segurança** ou **leitor de relatório** para o locatário. 

3. Cumpra os [pré-requisitos para acessar a API de Relatório do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Baixe e instale o [PowerShell V2 do Azure AD](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Instale o [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Esse módulo fornece vários cmdlets do utilitário, incluindo:
    - As bibliotecas ADAL necessárias para autenticação
    - Tokens de acesso do usuário, chaves de aplicativo e certificados usando ADAL
    - Resultados paginados de manipulação da API do Graph

6. Se é a primeira vez que você está usando o módulo, execute **Install-MSCloudIdUtilsModule**, caso contrário, importe-o usando o comando do Powershell **Import-Module**. Sua sessão deve ser semelhante ![a esta tela: Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Use o commandlet do Powershell **New-SelfSignedCertificate** para criar um certificado de teste.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Use o commandlet **Export-Certificate** para exportá-lo a um arquivo de certificado.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obter dados usando a API de Relatório do Azure AD com certificados

1. Navegue até o [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** e, em seguida, selecione **Registros de aplicativo** e escolha o aplicativo na lista. 

2. Selecione **Teclas de** > **configurações** e **selecione Enviar tecla pública**.

3. Selecione o arquivo de certificado da etapa anterior e selecione **Salvar**. 

4. Anote a ID do Aplicativo e a impressão digital do certificado que você acabou de registrar no aplicativo. Para encontrar a impressão digital, na página do aplicativo no portal, acesse **Configurações** e clique em **Chaves**. A impressão digital estará na lista **Chaves Públicas**.

5. Abra o manifesto do aplicativo no editor de manifesto sequencial e substitua a propriedade *keyCredentials* pelas novas informações de certificado usando o esquema a seguir. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Salve o manifesto. 
  
7. Agora, você pode obter um token de acesso para a API Graph usando este certificado. Use o cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** do módulo MSCloudIdUtils PowerShell, passando a ID do aplicativo e a impressão digital obtida na etapa anterior. 

   ![Portal do Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Use o token de acesso em seu script Powershell para consultar a API do Graph. Use o cmdlet **Invoke-MSCloudIdMSGraphQuery** do MSCloudIDUtils para enumerar o ponto de extremidade de directoryAudits e signins. Esse cmdlet manipula resultados com várias páginas e envia esses resultados para o pipeline do PowerShell.

9. Consulte o ponto de extremidade directoryAudits para recuperar os logs de auditoria. 
   ![Azure portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Consulte o ponto de extremidade signins para recuperar os logs de entrada.
    ![Azure portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Agora, você pode optar por exportar esses dados para um CSV e salvá-lo em um sistema SIEM. Você pode também encapsular o script em uma tarefa agendada para obter dados do Azure AD do seu locatário periodicamente sem a necessidade de armazenar as chaves de aplicativo no código-fonte. 

## <a name="next-steps"></a>Próximas etapas

* [Tenha uma primeira impressão das APIs de relatórios](concept-reporting-api.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
