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
ms.openlocfilehash: 6d66648680aa14baa53372732df52a6c247a0117
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483756"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Criando e mesclando CSR no Key Vault

O Azure Key Vault dá suporte ao armazenamento de certificado digital emitido por qualquer autoridade de certificação de sua escolha no cofre de chaves. Ele é compatível com a criação da solicitação de assinatura de certificado com um par de chaves pública/privada. Esse certificado pode ser assinado por qualquer Autoridade de Certificação de sua escolha. Pode ser AC corporativa interna ou AC pública externa. Uma solicitação de assinatura de certificado (também CSR ou solicitação de certificação) é uma mensagem que é enviada pelo usuário para uma AC (autoridade de certificação) a fim de solicitar a emissão de um certificado digital.

Para obter mais informações gerais sobre certificados, confira [Certificados do Azure Key Vault](./about-certificates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>Adição de certificado no Key Vault emitido por uma AC com parceria
Parceiros do Key Vault com as duas autoridades de certificação a seguir para simplificar a criação de certificados. 

|Provedor|Tipo de certificado|Configuração  
|--------------|----------------------|------------------|  
|DigiCert|O Key Vault oferece certificados SSL OV ou EV com DigiCert| [Guia de integração](./how-to-integrate-certificate-authority.md)
|GlobalSign|O Key Vault oferece certificados SSL OV ou EV com GlobalSign| [Guia de integração](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>Adição de certificado no Key Vault emitido por uma AC sem parceria

As etapas a seguir ajudarão você a criar um certificado de autoridades de certificação que não tem parceria com o Key Vault (por exemplo, GoDaddy não é uma AC confiável do cofre de chaves) 


### <a name="azure-powershell"></a>Azure PowerShell



1. Primeiro, **crie a política de certificação**. O Key Vault não registrará nem renovará o certificado do emissor em nome do usuário, pois a AC escolhida neste cenário não é compatível e, portanto, o IssuerName está definido como Desconhecido.

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > Se estiver usando um RDN (nome diferenciado relativo) que tenha uma vírgula (,) no valor, use aspas simples e coloque o valor que contém o caractere especial entre aspas duplas. Exemplo: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. Neste exemplo, o valor `OU` é lido como **Docs, Contoso**. Esse formato funciona para todos os valores que contêm uma vírgula.

2. Criar uma **solicitação de assinatura de certificado**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Obter a **solicitação do CSR assinada pela AC** A `$csr.CertificateSigningRequest` é a solicitação de assinatura de certificado codificada em base4 para o certificado. Você pode usar esse blob e despejar no site de solicitação de certificado do emissor. Essa etapa varia de AC para AC; o melhor seria pesquisar as diretrizes da sua autoridade de certificação sobre como executar essa etapa. Você também pode usar ferramentas como certreq ou openssl para obter a solicitação de certificado assinada e concluir o processo de geração de um certificado.


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

   ![Captura de tela que realça o botão Baixar CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.  Pegue o arquivo .csr na AC para que a solicitação seja assinada.
9.  Depois que a solicitação for assinada pela autoridade de certificação, retorne o arquivo de certificado para **mesclar a solicitação assinada** na mesma tela de Operação de Certificado.

A solicitação de certificado foi mesclada com êxito.

> [!NOTE]
> Se os valores de RDN tiverem vírgulas, você também poderá adicioná-los no campo **Entidade** colocando o valor entre aspas duplas, conforme mostrado na etapa 4.
> Exemplo de entrada para "Entidade": `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com` Neste exemplo, o RDN `OU` contém um valor com uma vírgula no nome. A saída resultante de `OU` é **Docs, Contoso**.


## <a name="adding-more-information-to-csr"></a>Adicionar mais informações ao CSR

Se desejar adicionar mais informações ao criar o CSR, por exemplo – 
    - País:
    - Cidade/localidade:
    - Estado/província:
    - Organização:
    - Unidade Organizacional: É possível adicionar todas essas informações ao criar um CSR definindo isso em subjectName.

Exemplo
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> Se você estiver solicitando um certificado DV com todos esses detalhes no CSR, a AC poderá rejeitar a solicitação, pois ela talvez não possa validar todas as informações na solicitação. Se você estivesse solicitando um certificado OV, seria mais apropriado adicionar todas essas informações no CSR.


## <a name="troubleshoot"></a>Solucionar problemas

- **Tipo de erro "A chave pública do certificado de entidade final no conteúdo do certificado X.509 especificado não corresponde à parte pública da chave privada especificada. Verifique se o certificado é válido"** Esse erro poderá ocorrer se você não estiver mesclando o CSR com a mesma solicitação de CSR iniciada. Cada vez que um CSR é criado, ele cria uma chave privada que deve ser correspondida ao mesclar a solicitação assinada.
    
- Quando é mesclado, o CSR mescla toda a cadeia?
    Sim, ele mescla toda a cadeia, desde que o usuário tenha fornecido o arquivo p7b para mesclagem.

- Se o certificado emitido estiver no status "desabilitado" no portal do Azure, prossiga para exibir a **Operação de Certificado** para examinar a mensagem de erro para esse certificado.

Para obter mais informações, veja [Operações de certificado na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

- **Tipo de erro "O nome da entidade fornecida não é um nome X500 válido"** Esse erro pode ocorrer quando você inclui um "caractere especial" nos valores de SubjectName. Confira as observações nas instruções do portal do Azure e do PowerShell, respectivamente. 

## <a name="next-steps"></a>Próximas etapas

- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
