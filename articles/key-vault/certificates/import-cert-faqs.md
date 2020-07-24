---
title: Perguntas frequentes-Azure Key Vault importação de certificado
description: Perguntas frequentes-Azure Key Vault importação de certificado
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095742"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Perguntas frequentes-Azure Key Vault importação de certificado

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Como importar um certificado no Azure Key Vault?

Importar certificado – para a operação de importação, o cofre de chaves do Azure aceita dois formatos de certificado PEM e PFX. Embora existam arquivos PEM apenas com a parte pública, o Azure Key Vault requer e aceita apenas um PEM ou PFX na pasta de arquivos e com uma chave privada. Siga o [tutorial para importar o certificado](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Depois de importar o certificado protegido por senha para o cofre de chaves e, em seguida, baixá-lo, não consigo ver a senha associada ao certificado?
    
O certificado protegido carregado após o armazenamento no cofre de chaves não salvará a senha associada a ele. Isso só é necessário uma vez durante a operação de importação. Embora esse seja um conceito por design, você sempre pode obter o certificado como um segredo e converter de Base64 em PFX adicionando a senha anterior por meio de [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Como posso resolver ' erro de parâmetro inadequado '? Quais são os formatos de certificado com suporte para importação no Key Vault?

Ao importar o certificado, você precisa garantir que a chave seja incluída no próprio arquivo. Se você tiver a chave privada separadamente em um formato diferente, precisará combinar a chave com o certificado. Algumas autoridades de certificação fornecem certificados em formatos diferentes, portanto, antes de importar o certificado, verifique se eles estão no formato. PEM ou. pfx e se a chave usada é RSA ou ECC. Consulte isso para revisar requisitos de [certificado](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) e [requisitos de chave de certificado](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection).

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Erro ao importar o certificado por meio do Portal "Algo deu errado". Como posso investigar ainda mais?
    
Para exibir um erro mais descritivo, importe o arquivo de certificado por meio do [CLI do Azure](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) ou do [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Como posso resolver ' tipo de erro: acesso negado ou o usuário não está autorizado a importar o certificado '?
    
Essa operação requer a permissão certificados/importar. Navegue para o local do Key Vault. Você precisará conceder as permissões adequadas ao usuário em políticas de acesso. Navegue até o Key Vault> políticas de acesso > adicionar política de acesso > selecione permissões de certificado (ou como você deseja as permissões) > principal > Search para e, em seguida, adicione o email do usuário. [Leia mais sobre as políticas de acesso relacionadas ao certificado](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Como resolver o ' tipo de erro: conflito ao criar um certificado '?
    
O nome do certificado deve ser exclusivo. O certificado com o mesmo nome pode estar no estado excluído reversível, também, de acordo com a [composição de um certificado](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) no cofre de chaves do Azure, se houver outra chave ou segredo no Key Vault com o mesmo nome que você está tentando especificar para o certificado, ele falhará e será necessário remover essa chave ou segredo ou usar um nome diferente para o certificado. [Exibir certificado excluído](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Por que estou recebendo o ' tipo de erro: o comprimento do caractere é muito longo '?
    
* O comprimento do nome da entidade do certificado tem um limite de caracteres de 200 caracteres
* O comprimento da senha do certificado tem um limite de caracteres de 200 caracteres

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Posso importar um certificado expirado no cofre de chaves do Azure?
    
Não, os certificados PFX expirados não serão importados para Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Como posso converter meu certificado em um formato adequado?

Você pode pedir à sua autoridade de certificação para fornecer o certificado no formato necessário, mas também há ferramentas de terceiros que podem ajudá-lo a converter para o formato adequado. no entanto, a Microsoft não será capaz de aconselhar mais sobre como obter o certificado no formato desejado.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Posso importar certificados de CAs que não são de parceiros?
Sim, você pode importar certificados de qualquer AC, mas o Key Vault não será capaz de renovar automaticamente esses certificados. Você poderá definir notificações por email para ser notificado sobre a expiração do certificado.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Se eu importar um certificado de uma autoridade de certificação de parceiro, o recurso de renovação automática ainda funcionará?
Sim, você precisa certificar-se de que, uma vez carregado, você especifique a rotação autogiro na política de emissão do certificado. Além disso, as alterações serão refletidas até a próxima versão do ciclo ou do certificado.


## <a name="next-steps"></a>Próximas etapas

- [Azure Key Vault certificados](/azure/key-vault/certificates/about-certificates)
