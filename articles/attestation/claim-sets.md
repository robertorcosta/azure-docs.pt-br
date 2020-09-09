---
title: Conjuntos de declarações do Atestado do Azure
description: Os conjuntos de declarações do Atestado do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 555dccbd3c2dfe61bac5891514deface6f8a877d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236796"
---
# <a name="claim-sets"></a>Conjuntos de declarações

As declarações geradas no processo de atestado de enclaves por meio do Atestado do Microsoft Azure podem ser divididas nas categorias abaixo:

- **Declarações de entrada**: declarações geradas pelo Atestado do Microsoft Azure após a análise das evidências de atestado.

- **Declarações de saída**: declarações criadas como uma saída pelo Atestado do Azure. Ela contém todas as declarações que devem terminar no token de atestado.

- **Declarações de propriedade**: declarações criadas como uma saída pelo Atestado do Azure. Ela contém todas as declarações que representam as propriedades do token de atestado, como a codificação do relatório, a duração da validade do relatório etc.

Veja abaixo as declarações definidas pelo RFC JWT e usadas pelo Atestado do Azure no objeto de resposta:

- **Declaração "iss" (Emissor)** : a declaração "iss" (emissor) identifica a entidade de segurança que emitiu o JWT. O processamento dessa declaração geralmente é específico do aplicativo. O valor "iss" é uma cadeia de caracteres que diferencia maiúsculas de minúsculas contendo um valor StringOrURI.
- **Declaração "iat" (Emitido em)** : a declaração "iat" (emitido em) identifica a hora em que o JWT foi emitido. Essa declaração pode ser usada para determinar a idade do JWT. O valor dela PRECISA ser um número que contenha um valor NumericDate.
- **Declaração "exp" (Hora de Expiração)** : A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT NÃO DEVE ser aceito para processamento. O processamento da declaração "exp" exige que a data/a hora atuais SEJAM anteriores à data/à hora de expiração listadas na declaração "exp".

  Observação: Uma margem de cinco minutos é adicionada à hora da emissão (iat) para considerar a distorção do relógio.
- **Declaração "nbf" (Não Anterior)** : a declaração "nbf" (não anterior) identifica a hora antes da qual o JWT NÃO SERÁ aceito para processamento. O processamento da declaração "nbf" exige que a data/a hora atuais SEJAM posteriores ou iguais à data/à hora não anteriores listadas na declaração "nbf".
  Observação: Uma margem de cinco minutos é adicionada à hora da emissão (iat) para considerar a distorção do relógio.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Declarações emitidas pelo Atestado do Azure em enclaves do SGX

### <a name="incoming-claims"></a>Declarações de entrada 

- **$is-debuggable**: um booliano que indica se o enclave tem ou não a depuração habilitada
- **$sgx-mrsigner**: valor codificado em hexa do campo “mrsigner” da cotação
- **$sgx-mrenclave**: valor codificado em hexa do campo “mrenclave” da cotação
- **$product-id**
- **$svn**: número de versão de segurança codificado na cotação 
- **$tee**: tipo de enclave 

### <a name="outgoing-claims"></a>Declarações de saída

- **is-debuggable**: um booliano que indica se o enclave tem ou não a depuração habilitada
- **sgx-mrsigner**: valor codificado em hexa do campo “mrsigner” da cotação
- **sgx-mrenclave**: valor codificado em hexa do campo “mrenclave” da cotação
- **product-id**
- **svn**: número de versão de segurança codificado na cotação 
- **tee**: tipo de enclave 
- **maa-ehd**:  versão codificada em Base64Url dos “Dados Contidos no Enclave” especificados na solicitação de atestado 
- **aas-ehd**:  versão codificada em Base64Url dos “Dados Contidos no Enclave” especificados na solicitação de atestado 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Declarações emitidas pelo Atestado do Azure em enclaves da VBS

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Declarações de entrada (também podem ser usadas como declarações de saída)

- **aikValidated**:  valor booliano que contém informações que indicam se o certificado AIK (chave de identidade de atestado) foi validado ou não.
- **aikPubHash**:  cadeia de caracteres que contém o base64(SHA256(chave pública AIK em formato DER)).
- **tpmVersion**:   valor inteiro que contém a versão principal do TPM (Trusted Platform Module).
- **secureBootEnabled**: valor booliano que indica se a inicialização segura está habilitada.
- **iommuEnabled**:  valor booliano que indicar se a Iommu (unidade de gerenciamento de memória de entrada-saída) está habilitada.
- **bootDebuggingDisabled**: valor booliano que indica se a depuração de inicialização está desabilitada.
- **notSafeMode**:  valor booliano que indica se o Windows não está sendo executado no modo de segurança.
- **notWinPE**:  valor booliano que indica se o Windows não está sendo executado no modo WinPE.
- **vbsEnabled**:  valor booliano que indica se a VBS está habilitada.
- **vbsReportPresent**:  valor booliano que indica se o relatório de enclave da VBS está disponível.
- **enclaveAuthorId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de autor do enclave, o identificador de autor do módulo primário do enclave.
- **enclaveImageId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de imagem do enclave, o identificador de imagem do módulo primário do enclave.
- **enclaveOwnerId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de proprietário do enclave, o identificador do proprietário do enclave.
- **enclaveFamilyId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de família do enclave. O identificador de família do módulo primário do enclave.
- **enclaveSvn**:  valor inteiro que contém o número de versão de segurança do módulo primário do enclave.
- **enclavePlatformSvn**:  valor inteiro que contém o número de versão de segurança da plataforma que hospeda o enclave.
- **enclaveFlags**:  a declaração enclaveFlags é um valor inteiro que contém sinalizadores que descrevem a política de runtime do enclave.
  
### <a name="outgoing-claims"></a>Declarações de saída

- **policy_hash**:  valor de cadeia de caracteres que contém o hash SHA256 do texto da política computado por BASE64URL(SHA256(BASE64URL(UTF8(texto da política)))).
- **policy_signer**:  valor de cadeia de caracteres que contém um JWK com a chave pública ou a cadeia de certificados presente no cabeçalho da política assinada.
- **ver (versão)** :  valor de cadeia de caracteres que contém a versão do relatório. Atualmente, 1.0.
- **Declaração cnf (Confirmação)** :  a declaração "cnf" é usada para identificar a chave de prova de posse. A declaração de confirmação, conforme definido no RFC 7800, contém a parte pública da chave do enclave atestada representada como um objeto JWK (Chave da Web JSON) (RFC 7517).
- **rp_data (dados de terceira parte confiável)** :  dados de terceira parte confiável, se houver, especificados na solicitação, usados pela terceira parte confiável como um nonce para garantir a atualização do relatório.
- **Declaração "jti" (ID do JWT)** : a declaração "jti" (ID do JWT) fornece um identificador exclusivo para o JWT. O valor do identificador é atribuído de uma forma que garanta que haja uma probabilidade insignificante de que o mesmo valor seja acidentalmente atribuído a outro objeto de dados.

### <a name="property-claims"></a>Declarações de propriedade

- **report_validity_in_minutes**: uma declaração de inteiro que representa o tempo durante o qual o token é válido.
  - **Valor padrão (hora)** : um dia em minutos.
  - **Valor máximo (hora)** : um ano em minutos.
- **omit_x5c**: uma declaração booliana que indica se o Atestado do Azure deve omitir o certificado usado para fornecer a prova de autenticidade do serviço. Se essa declaração for verdadeira, x5t será adicionado ao token de atestado. Se ela for falsa (padrão), x5c será adicionado ao token de atestado.

## <a name="next-steps"></a>Próximas etapas
- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)
