---
title: Proteger as mensagens de B2B com certificados
description: Adicionar certificados para ajudar a proteger mensagens B2B em aplicativos lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: c1b48ae8191e2e5313d9037c791eca73c8a55691
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191395"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Melhorar a segurança de mensagens B2B usando certificados

Quando você precisar manter a comunicação B2B confidencial, poderá aumentar a segurança para comunicação B2B em seus aplicativos de integração corporativa, especificamente aplicativos lógicos, adicionando certificados à sua conta de integração. Certificados são documentos digitais que verificam as identidades dos participantes em comunicações eletrônicas e ajudam a proteger a comunicação das seguintes maneiras:

* Criptografa o conteúdo da mensagem.
* Assina digitalmente mensagens.

Você pode usar esses tipos de certificados em seus aplicativos de integração corporativa:

* [Certificados públicos](https://en.wikipedia.org/wiki/Public_key_certificate), que você deve comprar de uma [CA (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) pública da Internet, mas não requer chaves. 

* Certificados privados ou [*certificados autoassinados*](https://en.wikipedia.org/wiki/Self-signed_certificate), que você cria e emite, mas também exigem chaves privadas. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Para usar um *certificado público* em aplicativos lógicos com recursos de B2B, você deve carregá-lo em sua conta de integração. Depois de definir as propriedades nos [contratos](logic-apps-enterprise-integration-agreements.md) criados, o certificado fica disponível para ajudá-lo a proteger suas mensagens B2B.

1. Entre no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **Todos os recursos**. Na caixa de pesquisa, insira o nome da conta de integração e selecione a conta de integração desejada.

   ![Localize e selecione a conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Em **Componentes**, escolha o bloco **Certificados**.

   ![Escolher “Certificados”](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Em **Certificados**, escolha **Adicionar**. Em **Adicionar Certificado**, forneça esses detalhes ao certificado. Quando terminar, escolha **OK**.

   | Propriedade | Valor | DESCRIÇÃO | 
   |----------|-------|-------------|
   | **Nome** | <*certificate-name*> | O nome do certificado, que é "publicCert" neste exemplo | 
   | **Tipo de certificado** | Público | O tipo do certificado |
   | **Certificado** | <*certificate-file-name*> | Para localizar e selecionar o arquivo de certificado que você quer enviar, escolha o ícone da pasta ao lado da caixa **Certificado**. |
   ||||

   ![Escolha "Adicionar", forneça detalhes do certificado](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Após o Azure validar a seleção, o Azure carregará o certificado.

   ![Azure exibe novo certificado](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Para usar um *certificado privado* em aplicativos lógicos com recursos de B2B, você deve carregá-lo em sua conta de integração. Você também precisa ter uma chave privada que seja adicionada primeiro ao [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Depois de definir as propriedades nos [contratos](logic-apps-enterprise-integration-agreements.md) criados, o certificado fica disponível para ajudá-lo a proteger suas mensagens B2B.

> [!NOTE]
> Para certificados privados, certifique-se de adicionar um certificado público correspondente que aparece nas configurações de **envio e recebimento** [do contrato AS2](logic-apps-enterprise-integration-as2.md) para assinar e criptografar mensagens.

1. [Adicionar sua chave privada no Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) e **Nome de Chave**.
   
2. Autorize os Aplicativos Lógicos do Azure a executarem operações no Azure Key Vault. Para conceder acesso à entidade de serviço de Aplicativos Lógicos, use o comando do PowerShell, [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), por exemplo:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Entre no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **Todos os recursos**. Na caixa de pesquisa, insira o nome da conta de integração e selecione a conta de integração desejada.

   ![Localizar sua conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Em **Componentes**, escolha o bloco **Certificados**.  

   ![Escolher o bloco Certificados](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Em **Certificados**, escolha **Adicionar**. Em **Adicionar Certificado**, forneça esses detalhes ao certificado. Quando terminar, escolha **OK**.

   | Propriedade | Valor | DESCRIÇÃO | 
   |----------|-------|-------------|
   | **Nome** | <*certificate-name*> | O nome do certificado, que é "privateCert" neste exemplo | 
   | **Tipo de certificado** | Privado | O tipo do certificado |
   | **Certificado** | <*certificate-file-name*> | Para localizar e selecionar o arquivo de certificado que você quer enviar, escolha o ícone da pasta ao lado da caixa **Certificado**. Ao usar um cofre de chaves para a chave privada, o arquivo carregado será o certificado público. | 
   | **Grupo de recursos** | <*integration-account-resource-group*> | O grupo de recursos da conta de integração, que é "MyResourceGroup" neste exemplo | 
   | **Key Vault** | <*key-vault-name*> | O nome do cofre de chaves do Azure |
   | **Nome da chave** | <*key-name*> | O nome da chave |
   ||||

   ![Escolha "Adicionar", forneça detalhes do certificado](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Após o Azure validar a seleção, o Azure carregará o certificado.

   ![Azure exibe novo certificado](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Próximas etapas

* [Criar um contrato de B2B](logic-apps-enterprise-integration-agreements.md)
