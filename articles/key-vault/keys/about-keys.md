---
title: Sobre chaves – Azure Key Vault
description: Visão geral dos detalhes da interface REST e do desenvolvedor do Azure Key Vault para as chaves.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8ac5b0976e677cc48fb3fe47c50797b07acdbc44
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91708859"
---
# <a name="about-keys"></a>Sobre chaves

O Azure Key Vault fornece dois tipos de recursos para armazenar e gerenciar chaves de criptografia:

|Tipo de recurso|Métodos de proteção da chave|URL base do ponto de extremidade do plano de dados|
|--|--|--|
| **Cofres** | Protegida por software<br/><br/>e<br/><br/>Protegida por HSM (com SKU Premium)</li></ul> | https://{vault-name}.vault.azure.net |
| **Pools HSM gerenciados** | Protegida por HSM | https://{hsm-name}.managedhsm.azure.net |
||||

- **Cofres** – os cofres oferecem uma solução de gerenciamento de chaves altamente disponível, de baixo custo, fácil de implantar, multilocatário (quando disponível) adequada para os cenários de aplicativos de nuvem mais comuns.
- **HSMs gerenciados** – o HSM gerenciado oferece HSMs altamente disponíveis, de locatário único, com resiliência de zona (quando disponível), para armazenar e gerenciar suas chaves de criptografia. É mais adequado para aplicativos e cenários de uso que lidam com chaves de alto valor. Também ajuda a alcançar os requisitos mais rigorosos de segurança, conformidade e regulamentação. 

> [!NOTE]
> Os cofres também permitem que você armazene e gerencie vários tipos de objetos, como segredos, certificados e chaves de conta de armazenamento, além das chaves criptográficas.

As chaves de criptografia no Key Vault são representadas como objetos de chave da Web JSON [JWK]. As especificações do JSON (JavaScript Object Notation) e do JOSE (JavaScript Object Signing and Encryption) são:

-   [Chave da Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Criptografia de Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos da Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Assinatura da Web JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

As especificações de base JWK/JWA também são estendidas para habilitar tipos de chave exclusivos para as implementações do Azure Key Vault e de HSM Gerenciado. 

As chaves protegidas por HSM (também conhecidas como chaves HSM) são processadas em um HSM (módulo de segurança de hardware) e sempre permanecem no limite de proteção do HSM. 

- Os cofres usam HSMs validados **FIPS 140-2 Nível 2** para proteger as chaves HSM na infraestrutura de back-end do HSM compartilhado. 
- Os pools de HSM gerenciados usam módulos HSM validados **FIPS 140-2 Nível 3** para proteger suas chaves. Cada pool de HSM é uma instância de locatário única isolada com seu próprio [domínio de segurança](../managed-hsm/security-domain.md) que fornece isolamento de criptografia completo em relação a todos os outros pools de HSM que compartilham a mesma infraestrutura de hardware.

Essas chaves são protegidas em pools de HSM de locatário único. Você pode importar uma RSA, EC e uma chave simétrica em forma flexível ou exportar de um dispositivo HSM com suporte. Você também pode gerar chaves em pools de HSM. Quando você importa chaves HSM usando o método descrito na [especificação de BYOK (Bring Your Own Key)](../keys/byok-specification.md), isso habilita o material de chave de transporte seguro em pools do HSM Gerenciado. 

Para obter mais informações sobre fronteiras geográficas, consulte [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-protection-methods-and-algorithms"></a>Tipos de chave, métodos de proteção e algoritmos

O Key Vault é compatível com RSA, EC e chaves simétricas. 

### <a name="hsm-protected-keys"></a>Chaves protegidas por HSM

|Tipo de chave|Cofres (somente SKU Premium)|Pools HSM gerenciados|
|--|--|--|--|
**EC-HSM**: Chave de curva elíptica|HSM FIPS 140-2 Nível 2|HSM FIPS 140-2 Nível 3
**RSA-HSM**: Chave RSA|HSM FIPS 140-2 Nível 2|HSM FIPS 140-2 Nível 3
**oct-HSM**: Simétrica|Sem suporte|HSM FIPS 140-2 Nível 3
||||

### <a name="software-protected-keys"></a>Chaves protegidas por software

|Tipo de chave|Cofres|Pools HSM gerenciados|
|--|--|--|--|
**RSA**: chave RSA "protegida por software"|FIPS 140-2 Nível 1|Sem suporte
**EC**: chave de Curva Elíptica "protegida por software"|FIPS 140-2 Nível 1|Sem suporte
||||

### <a name="supported-algorithms"></a>Algoritmos compatíveis

|Tipos/dimensões/curvas de chave| Criptografar/Descriptografar<br>(Encapsular/Desencapsular) | Assinar/Verificar | 
| --- | --- | --- |
|EC-P256, EC-P256K, EC-P384, EC-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128 bits, 256 bits| AES-KW<br>AES-GCM<br>AES-CBC| NA| 
|||

###  <a name="ec-algorithms"></a>Algoritmos EC
 Os seguintes identificadores de algoritmo são compatíveis com chaves EC-HSM

#### <a name="curve-types"></a>Tipos de curva

-   **P-256** - A curva NIST P-256, definida em [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256_K** -curva a SEC SECP256K1, definida em [s 2: Parâmetros de Domínio de Curva Elíptica Recomendados](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - A curva NIST P-384, definida em [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - A curva NIST P-521, definida em [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - resumos de ECDSA para SHA-256 e as chaves criadas com a curva p-256. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - resumos de ECDSA para SHA-256 e as chaves criadas com curva P-256_K. Este algoritmo está pendente de padronização.
-   **ES384** - resumos de ECDSA para SHA-384 e as chaves criadas com a curva p-384. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA para SHA-512 resumos e as chaves criadas com a curva p-521. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Os seguintes identificadores de algoritmo são compatíveis com chaves RSA e RSA-HSM  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, CRIPTOGRAFAR/DESCRIPTOGRAFAR

-   **RSA1_5** - criptografia de chave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES usando Preenchimento de Criptografia Assimétrica Ideal (OAEP) [RFC3447], com os parâmetros padrão especificados por RFC 3447 na seção 2.1. Esses parâmetros padrão estão usando uma função de hash de SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  
-  **RSA-OAEP-256** – RSAES que usa o Preenchimento de Criptografia Assimétrica Ideal com uma função de hash SHA-256 e uma função de geração de máscara MGF1 com SHA-256

#### <a name="signverify"></a>SIGN/VERIFY

-   **PS256**: RSASSA-PSS com SHA-256 e MGF1 com SHA-256, conforme descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384**: RSASSA-PSS com SHA-384 e MGF1 com SHA-384, conforme descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512**: RSASSA-PSS com SHA-512 e MGF1 com SHA-512, conforme descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 usando SHA-256. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5 usando SHA-384. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 usando SHA-512. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-512 e deve ter 64 bytes de comprimento.  
-   **RSNULL** – consulte [RFC2437](https://tools.ietf.org/html/rfc2437), um caso de uso especializado para permitir determinados cenários de TLS.  

###  <a name="symmetric-key-algorithms"></a>Algoritmos de chave simétrica
- **AES-KW** – encapsulamento de chave AES ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- **AES-GCM** – criptografia AES no Modo de Contador Galois ([NIST SP800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC** – criptografia AES no Modo Encadeamento de Blocos de Criptografia ([NIST SP800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> A implementação AES-GCM atual e as APIs correspondentes são experimentais. A implementação e as APIs podem ser alteradas substancialmente nas iterações futuras. 

##  <a name="key-operations"></a>Operações de chave

O HSM gerenciado é compatível com as seguintes operações em objetos chave:  

-   **Criar**: Permite que um cliente crie uma chave no Key Vault. O valor da chave é gerado pelo Key Vault e armazenado e não é liberado para o cliente. Chaves assimétricas podem ser criadas no Key Vault.  
-   **Import**: Permite que um cliente importe uma chave existente para o Key Vault. Chaves assimétricas chaves podem ser importadas para o Key Vault através de diversos métodos diferentes de empacotamento dentro de um constructo JWK. 
-   **Atualização**: Permite que um cliente com permissões suficientes modifique os metadados (atributos de chave) associados com uma chave armazenada anteriormente no Key Vault.  
-   **Excluir**: Permite que um cliente com permissões suficientes exclua uma chave do Key Vault.  
-   **Lista**: Permite que um cliente liste todas as chaves em um determinado Key Vault.  
-   **Versões da lista**: Permite que um cliente liste todas as versões de uma determinada chave em um determinado Key Vault.  
-   **Obter**: Permite que um cliente recupere as partes públicas de uma determinada chave em um Key Vault.  
-   **Backup**: Exporta uma chave de forma protegida.  
-   **Restaurar**: Importa uma chave de backup anterior.  

Para obter mais informações, veja [Operações de chave na referência de API REST do Key Vault](/rest/api/keyvault).  

Quando uma chave tiver sido criada no Key Vault, as seguintes operações criptográficas podem ser executadas usando a chave:  

-   **Assinar e Verificar**: Estritamente, essa operação é, estritamente, "assinar hash" ou "verificar hash", já que o Key Vault não oferece suporte a hash de conteúdo como parte da criação da assinatura. Aplicativos devem fazer hash dos dados para se conectarem localmente e, em seguida, solicitar ao Key Vault a assinatura do hash. A verificação de hashes assinados é suportada como uma operação conveniente para aplicativos que podem não ter acesso ao material de chave [público]. Para melhor desempenho do aplicativo, as operações VERIFY devem ser executadas localmente.  
-   **Chave de Criptografia / Encapsulamento**: Uma chave armazenada no Key Vault pode ser usada para proteger outra chave, normalmente uma chave de criptografia de conteúdo simétrico (CEK). Quando a chave no Key Vault é assimétrica, a criptografia de chave é usada. Por exemplo, RSA-OAEP e as operações ENCAPSULAR CHAVE/DESENCAPSULAR CHAVE são equivalentes a CRIPTOGRAFAR/DESCRIPTOGRAFAR. Quando a chave no Key Vault for simétrica, é usado o encapsulamento de chave. Por exemplo, AES-KW. A operação ENCAPSULAR CHAVE é suportada como uma conveniência para aplicativos que podem não ter acesso ao material de chave [público]. Para melhor desempenho do aplicativo, as operações ENCAPSULAR CHAVE devem ser executadas localmente.  
-   **Criptografar e descriptografar**: Uma chave armazenada no Key Vault pode ser usada para criptografar ou descriptografar um único bloco de dados. O tamanho do bloco é determinado pelo tipo de chave e algoritmo de criptografia selecionado. A operação Criptografar é fornecida por conveniência para aplicativos que podem não ter acesso ao material de chave [público]. Para melhor desempenho do aplicativo, as operações ENCRYPT devem ser executadas localmente.  

Enquanto o uso de chaves assimétricas na operação ENCAPSULAR CHAVE/DESENCAPSULAR CHAVE possa parecer supérfluo (já que esta operação é equivalente a CRIPTOGRAFAR/DESCRIPTOGRAFAR), o uso de operações distintas é importante. A distinção fornece semântica e separação de autorização dessas operações e consistência quando outros tipos de chave são suportados pelo serviço.  

O Key Vault não dá suporte a operações de exportação. Quando uma chave é fornecida no sistema não pode ser extraída ou ter seu material de chave modificado. No entanto, os usuários do Key Vault podem exigir sua chave para outros casos de uso, tais como após ela ter sido excluída. Nesse caso, eles podem usar as operações de BACKUP e RESTAURAÇÃO para exportar/importar a chave de forma protegida. As chaves criadas pela operação de BACKUP não são utilizáveis fora do Key Vault. Como alternativa, a operação de IMPORTAR pode ser usada em várias instâncias do Key Vault.  

Os usuários podem restringir qualquer uma das operações de criptografia que o Key Vault suporta, por chave, usando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [Chave de Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Atributos de chave

Além do material de chave, os seguintes atributos podem ser especificados. Em uma solicitação JSON, a palavra-chave de atributos e as chaves, '{' '}' são necessárias, mesmo se não há nenhum atributo especificado.  

- *habilitado*: booliano, opcional, o padrão é **true**. Especifica se a chave está habilitada e pode ser utilizada para operações de criptografia. O atributo *habilitado* é usado em conjunto com *nbf* e *exp*. Quando ocorre uma operação entre *nbf* e *exp*, ele só será permitido se *habilitado* estiver definido como **true**. As operações fora da janela *nbf* / *exp* não são automaticamente permitidas, exceto para determinados tipos de operação em [determinadas condições](#date-time-controlled-operations).
- *nbf*: IntDate, opcional, o padrão é agora. O atributo *nbf* (not before) identifica o tempo anterior que a chave NÃO DEVE ser usada para operações de criptografia, exceto para determinados tipos de operação em [determinadas condições](#date-time-controlled-operations). O processamento do atributo *nbf* requer que a data/hora atual DEVE ser posterior ou igual a data/hora não-anterior listada no atributo *nbf*. O Key Vault PODE fornecer alguma reserva pequena, normalmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *exp*: IntDate, opcional, o padrão é “infinito”. O atributo *exp* (expiration time) identifica o tempo de expiração em que ou depois que a chave NÃO DEVE ser usada para operações de criptografia, exceto para determinados tipos de operação em [determinadas condições](#date-time-controlled-operations). O processamento do atributo *exp* requer que a data/hora atual DEVE ser anterior a data/hora de expiração listada no atributo *exp*. O Key Vault PODE fornecer alguma reserva pequena, geralmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  

Há mais atributos somente leitura que são incluídos em qualquer resposta que inclui os atributos de chave:  

- *criado*: IntDate, opcional. O atributo *criado* indica quando esta versão da chave foi criada. O valor é nulo para chaves criadas antes da adição deste atributo. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *atualizado*: IntDate, opcional. O atributo *atualizado* indica quando esta versão da chave foi atualizada. O valor é nulo para chaves que foram atualizadas antes da adição deste atributo. Seu valor DEVE ser um número que contenha um valor de IntDate.  

Para obter mais informações sobre IntDate e outros tipos de dados, confira [Sobre chaves, segredos e certificados: [Tipos de dados](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operações de data e hora controladas

Chaves expiradas e ainda não válidas, fora da janela *nbf* / *exp*, funcionarão para operações **decrypt**, **unwrap** e **verify** (não retornarão 403, Proibido). A lógica para usar o estado de não válido ainda é permitir que uma chave seja testada antes do uso de produção. A lógica para usar o estado expirado é permitir operações de recuperação de dados que foram criados quando a chave era válida. Além disso, você pode desabilitar o acesso a uma chave usando políticas de Cofre de Chaves, ou atualizando o atributo de chave *habilitado* para **false**.

Para obter mais informações sobre tipos de dados, consulte [Tipos de dados](../general/about-keys-secrets-certificates.md#data-types).

Para obter mais informações sobre outros atributos possíveis, consulte a [Chave de Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Marcas de chave

Você pode especificar mais metadados específicos do aplicativo na forma de marcas. O Key Vault oferece suporte a até 15 marcas, cada uma delas pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

> [!NOTE] 
> As marcas são legíveis por um chamador se elas têm a permissão *list* ou *get* para a chave.

##  <a name="key-access-control"></a>Controle de acesso a chave

O controle de acesso para chaves gerenciados pelo Cofre de Chaves é fornecido no nível de um Cofre de Chaves que atua como o contêiner de chaves. A política de controle de acesso para chaves é diferente da política de controle de acesso para segredos no mesmo Key Vault. Os usuários podem criar um ou mais cofres para armazenar chaves e são solicitados a manter a segmentação e gerenciamento de chaves apropriados do cenário. O controle de acesso para chaves é independente do controle de acesso para segredos.  

As seguintes permissões podem ser concedidas, por usuário / serviço, na entrada de controle de acesso de chaves em um cofre. Essas permissões refletem com maior exatidão as operações permitidas em um objeto de chave.  Permitir acesso a uma entidade de serviço no cofre de chaves é uma operação avulsa e permanecerá a mesma para todas as assinaturas do Azure. Você poderá usá-la para implantar quantos certificados desejar. 

- Permissões para operações de gerenciamento de chaves
  - *obter*: Ler a parte pública de uma chave, além de seus atributos
  - *lista*: Listar as chaves ou versões de uma chave armazenada em um cofre de chaves
  - *atualizar*: Atualizar os atributos de uma chave
  - *criar*: Criar novas chaves
  - *importar*: Importar uma chave para um cofre de chaves
  - *excluir*: Excluir o objeto de chave
  - *recuperar*: Recuperar uma chave excluída
  - *backup*: Fazer backup de uma chave em um Key Vault
  - *restaurar*: Restaurar um backup da chave em um cofre de chave

- Permissões para operações criptográficas
  - *descriptografar*: Usar a chave para desproteger uma sequência de bytes
  - *criptografar*: Usar a chave para proteger uma sequência arbitrária de bytes
  - *unwrapKey*: Usar a chave para desproteger uma chave simétrica encapsulada
  - *wrapKey*: Usar a chave para proteger uma chave simétrica
  - *verificar*: Usar a chave para verificar resumos  
  - *sign*: Use a chave para assinar resumos
    
- Permissões para operações com privilégio
  - *limpar*: Limpar (exclui permanentemente) uma chave excluída

Para obter mais informações sobre como trabalhar com chaves, consulte [Operações de chave na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Próximas etapas
- [Sobre o Key Vault](../general/overview.md)
- [Sobre o HSM Gerenciado](../managed-hsm/overview.md)
- [Sobre segredos](../secrets/about-secrets.md)
- [Sobre certificados](../certificates/about-certificates.md)
- [Visão geral da API REST do Key Vault](../general/about-keys-secrets-certificates.md)
- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
