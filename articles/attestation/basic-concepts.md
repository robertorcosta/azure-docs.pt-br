---
title: Conceitos básicos do Atestado do Azure
description: Conceitos básicos do Atestado do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a4ab8372e23e3621f7d73f8dbc38957c809acc9c
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236792"
---
# <a name="basic-concepts"></a>Conceitos básicos

Veja abaixo alguns conceitos básicos relacionados ao Atestado do Microsoft Azure.

## <a name="json-web-token-jwt"></a>JWT (Token Web JSON)

O [JWT](https://jwt.io/) (Token Web JSON) é um método [RFC7519](https://tools.ietf.org/html/rfc7519) padrão aberto usado para transmitir informações com segurança entre as partes como um objeto JSON (JavaScript Object Notation). É possível verificar essas informações e confiar nelas porque elas são assinadas digitalmente. Os JWTs podem ser assinados por meio de um segredo ou um par de chaves pública/privada.

## <a name="json-web-key-jwk"></a>Chave da Web JSON (JWK)

A [JWK](https://tools.ietf.org/html/rfc7517) (Chave da Web JSON) é uma estrutura de dados JSON que representa uma chave de criptografia. Essa especificação também define uma estrutura de dados JSON do Conjunto de JWK que representa um conjunto de JWKs.

## <a name="attestation-provider"></a>Provedor de atestado

O provedor de atestado pertence ao provedor de recursos do Azure chamado Microsoft.Attestation. O provedor de recursos é um ponto de extremidade de serviço que fornece o contrato REST do Atestado do Azure e é implantado por meio do [Azure Resource Manager](../azure-resource-manager/management/overview.md). Cada provedor de atestado respeita uma política específica e detectável. 

Os provedores de atestado são criados com uma política padrão para cada tipo de TEE (observe que o enclave da VBS não tem nenhuma política padrão). Confira os [exemplos de uma política de atestado](policy-examples.md) para obter mais detalhes sobre a política padrão do SGX.

### <a name="regional-default-provider"></a>Provedor padrão regional

O Atestado do Azure fornece um provedor padrão em cada região. Os clientes podem optar por usar o provedor padrão para o atestado ou criar provedores próprios com políticas personalizadas. Os provedores padrão podem ser acessados por qualquer usuário do Azure AD, e a política associada a um provedor padrão não pode ser alterada.

| Região | URI do atestado | 
|--|--|
| Sul do Reino Unido | https://shareduks.uks.attest.azure.net | 
| Leste dos EUA 2 | https://sharedeus2.eus2.attest.azure.net | 
| Centro dos EUA | https://sharedcus.cus.attest.azure.net | 
| Leste dos EUA| https://sharedeus.eus.attest.azure.net | 
| Canadá Central | https://sharedcac.cac.attest.azure.net | 

## <a name="attestation-request"></a>Solicitação de atestado

A solicitação de atestado é um objeto JSON serializado enviado pelo aplicativo cliente ao provedor de atestado. O objeto de solicitação para o enclave do SGX tem duas propriedades: 
- “Quote”: o valor da propriedade “Quote” é uma cadeia de caracteres que contém uma representação codificada em Base64URL da cotação do atestado
- “EnclaveHeldData”: o valor da propriedade “EnclaveHeldData” é uma cadeia de caracteres que contém uma representação codificada em Base64URL dos dados contidos no enclave.

O Atestado do Azure validará a “Cotação” fornecida do TEE e garantirá que o hash SHA256 dos Dados Contidos no Enclave fornecido seja expresso nos primeiros 32 bytes do campo reportData na cotação. 

## <a name="attestation-policy"></a>Política de atestado

A política de atestado é usada para processar as evidências de atestado e pode ser configurada pelos clientes. No centro do Atestado do Azure está um mecanismo de política, que processa as declarações que constituem as evidências. As políticas são usadas para determinar se o Atestado do Azure deverá emitir um token de atestado com base nas evidências (ou não) e, portanto, endossa o Atestador (ou não). Da mesma forma, a falha na aprovação em todas as políticas fará com que nenhum token JWT seja emitido.

Se a política padrão do TEE no provedor de atestado não atender às necessidades, os clientes poderão criar políticas personalizadas em uma das regiões com suporte no Atestado do Azure. O gerenciamento de políticas é um recurso importante fornecido aos clientes pelo Atestado do Azure. As políticas serão específicas do TEE e podem ser usadas para identificar enclaves ou adicionar declarações ao token de saída ou modificar declarações em um token de saída. 

Confira os [exemplos de uma política de atestado](policy-examples.md) para obter o conteúdo e os exemplos de política padrão.

## <a name="benefits-of-policy-signing"></a>Benefícios da assinatura de política

Uma política de atestado é o que, em última análise, determina se um token de atestado será emitido pelo Atestado do Azure. A política também determina as declarações a serem geradas no token de atestado. É, portanto, de suma importância que a política avaliada pelo serviço seja, de fato, a política escrita pelo administrador e que ela não tenha sido adulterada nem modificada por entidades externas. 

O modelo de confiança define o modelo de autorização do provedor de atestado para definir e atualizar a política.  Há suporte para dois modelos: um baseado na autorização do Azure AD e outro baseado na posse de chaves de criptografia gerenciadas pelo cliente (conhecidas como modelo isolado).  O modelo isolado permitirá que o Atestado do Azure garanta que a política enviada pelo cliente não esteja adulterada.

No modelo isolado, o administrador cria um provedor de atestado especificando um conjunto de certificados X.509 de autenticação confiável em um arquivo. Depois, o administrador poderá adicionar uma política assinada ao provedor de atestado. Durante o processamento da solicitação de atestado, o Atestado do Azure validará a assinatura da política usando a chave pública representada pelo parâmetro “jwk” ou “x5c” no cabeçalho.  O Atestado do Azure também verificará se a chave pública do cabeçalho da solicitação está na lista de certificados de autenticação confiáveis associados ao provedor de atestado. Dessa forma, a terceira parte confiável (Atestado do Azure) pode confiar em uma política assinada com os certificados X.509 reconhecidos por ele. 

Confira os [exemplos de certificado de signatário de política](policy-signer-examples.md) para obter exemplos.

## <a name="attestation-token"></a>Token de atestado

A resposta do Atestado do Azure será uma cadeia de caracteres JSON cujo valor contém o JWT. O Atestado do Azure empacotará as declarações e gerará um JWT assinado. A operação de assinatura é executada por meio de um certificado autoassinado com o nome da entidade correspondente ao elemento AttestUri do provedor de atestado.

A API Obter Metadados do OpenID retorna uma resposta de Configuração do OpenID, conforme especificado pelo [protocolo de Descoberta do OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). A API recupera metadados sobre os certificados de autenticação em uso pelo Atestado do Azure.

Exemplo de JWT gerado para um enclave do SGX:

```
{
  “alg”: “RS256”,
  “jku”: “https://tradewinds.us.attest.azure.net/certs”,
  “kid”: “f1lIjBlb6jUHEUp1/Nh6BNUHc6vwiUyMKKhReZeEpGc=”,
  “typ”: “JWT”
}.{
  “maa-ehd”: <input enclave held data>,
  “exp”: 1568187398,
  “iat”: 1568158598,
  “is-debuggable”: false,
  “iss”: “https://tradewinds.us.attest.azure.net”,
  “nbf”: 1568158598,
  “product-id”: 4639,
  “sgx-mrenclave”: “”,
  “sgx-mrsigner”: “”,
  “svn”: 0,
  “tee”: “sgx”
}.[Signature]
```
Declarações como “exp”, “iat”, “iss” e “nbf” são definidas pelo [RFC JWT](https://tools.ietf.org/html/rfc7517) e as restantes são geradas pelo Atestado do Azure. Confira as [declarações emitidas pelo Atestado do Azure](claim-sets.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)
