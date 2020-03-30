---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448619"
---
Um certificado SSL adequado garante que você esteja enviando informações criptografadas para o servidor certo. Além da criptografia, o certificado também permite a autenticação. Você pode carregar seu próprio certificado SSL confiável através da interface PowerShell do dispositivo.

1. [Conecte-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Use `Set-HcsCertificate` o cmdlet para carregar o certificado. Quando solicitado, forneça os seguintes parâmetros:

   - `CertificateFilePath`- Caminho para o compartilhamento que contém o arquivo do certificado no formato *.pfx.*
   - `CertificatePassword`- Uma senha usada para proteger o certificado.
   - `Credentials`- Nome de usuário para acessar o compartilhamento que contém o certificado. Forneça a senha para o compartilhamento de rede quando solicitado.

     O exemplo a seguir mostra o uso deste cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

