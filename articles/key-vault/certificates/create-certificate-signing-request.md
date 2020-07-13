---
title: Criando e mesclando CSR no Azure Key Vault
description: Criando e mesclando CSR no Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: ae2d6259bac6a2034edc98de9b0405f32f17fbc3
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849489"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Criando e mesclando CSR no Key Vault

O Azure Key Vault dá suporte ao armazenamento de certificado digital emitido por qualquer autoridade de certificação de sua escolha no cofre de chaves. Ele é compatível com a criação da solicitação de assinatura de certificado com um par de chaves pública/privada. Esse certificado pode ser assinado por qualquer Autoridade de Certificação de sua escolha. Pode ser AC corporativa interna ou AC pública externa. Uma solicitação de assinatura de certificado (também CSR ou solicitação de certificação) é uma mensagem que é enviada pelo usuário para uma AC (autoridade de certificação) a fim de solicitar a emissão de um certificado digital.

Para obter mais informações gerais sobre certificados, confira [Certificados do Azure Key Vault](/azure/key-vault/certificates/about-certificates).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Adição de certificado no Key Vault emitido por uma autoridade de certificação não confiável

As etapas a seguir ajudarão você a criar um certificado de autoridades de certificação que não tem parceria com o Key Vault (por exemplo, GoDaddy não é uma AC confiável do cofre de chaves) 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Primeiro, **crie a política de certificação**. O Key Vault não registrará nem renovará o certificado do emissor em nome do usuário, pois a AC escolhida neste cenário não é compatível e, portanto, o IssuerName está definido como Desconhecido.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Criar uma **solicitação de assinatura de certificado**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Obter a **solicitação do CSR assinada pela AC** A `$certificateOperation.CertificateSigningRequest` é a solicitação de assinatura de certificado codificada em base4 para o certificado. Você pode usar esse blob e despejar no site de solicitação de certificado do emissor. Essa etapa varia de AC para AC; o melhor seria pesquisar as diretrizes da sua autoridade de certificação sobre como executar essa etapa. Você também pode usar ferramentas como certreq ou openssl para obter a solicitação de certificado assinada e concluir o processo de geração de um certificado.


4. **Mesclando a solicitação assinada** no Key Vault Depois que a solicitação de certificado for assinada pelo emissor, você poderá retornar o certificado assinado e mesclá-lo com o par de chaves pública/privada inicial criado no Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Agora a solicitação de certificado foi mesclada com êxito.

### <a name="azure-portal"></a>Portal do Azure

1.  Para gerar a CSR para a autoridade de certificação de sua preferência, navegue até o cofre de chaves ao qual você deseja adicionar o certificado.
2.  Na página de propriedades do Key Vault, selecione **Certificados**.
3.  Selecione a guia **Gerar/Importar**.
4.  Na tela **Criar um certificado**, escolha os seguintes valores:
    - **Método de Criação de Certificado:** Generate.
    - **Nome do Certificado:** ContosoManualCSRCertificate.
    - **Tipo de CA (Autoridade de Certificação):** Certificado emitido por uma Autoridade de Certificação não integrada
    - **Assunto:** `"CN=www.contosoHRApp.com"`
    - Selecione os outros valores conforme desejado. Clique em **Criar**.

    ![Propriedades do certificado](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Você verá que o certificado já foi adicionado na lista de Certificados. Selecione esse certificado que você acabou de criar. O estado atual do certificado será "desabilitado", pois ele ainda não foi emitido pela autoridade de certificação.
7. Clique na guia **Operação de Certificado** e selecione **Baixar CSR**.
 ![Propriedades do certificado](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Pegue o arquivo .csr na AC para que a solicitação seja assinada.
9.  Depois que a solicitação for assinada pela autoridade de certificação, retorne o arquivo de certificado para **mesclar a solicitação assinada** na mesma tela de Operação de Certificado.

Agora a solicitação de certificado foi mesclada com êxito.

## <a name="troubleshoot"></a>Solucionar problemas

Se o certificado emitido estiver no status "desabilitado" no portal do Azure, prossiga para exibir a **Operação de Certificado** para examinar a mensagem de erro para esse certificado.

Para obter mais informações, veja [Operações de certificado na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Próximas etapas

- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
