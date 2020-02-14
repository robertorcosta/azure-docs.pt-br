---
title: Definir um perfil técnico SAML em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico SAML em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3a3df4d3a955926381a664ab872e03ae987f0839
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197962"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico SAML em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade SAML 2,0. Este artigo descreve as especificações de um perfil técnico para interagir com um provedor de declarações compatível com esse protocolo padronizado. Com um perfil de técnico SAML, pode-se federar com um provedor de identidade baseado em SAML,como o [ADFS](identity-provider-adfs2016-custom.md) e o [Salesforce](identity-provider-salesforce-custom.md). Essa federação permite que usuários entrem com suas redes sociais existentes ou identidades corporativas.

## <a name="metadata-exchange"></a>Troca de metadados

Metadados são informações usadas no protocolo SAML para expor a configuração de uma entidade SAML, como um provedor de serviços ou o provedor de identidade. Metadados definem o local dos serviços, como entrada e saída, certificados, método de entrada e muito mais. O provedor de identidade usa os metadados para saber como se comunicar com o Azure AD B2C. Os metadados são configurados em formato XML e podem ser assinados com uma assinatura digital para que a outra parte possa validar a integridade dos metadados. Quando o Azure AD B2C federa com um provedor de identidade SAML, ele atua como provedor de serviços iniciando uma solicitação SAML e aguardando uma resposta SAML. E, em alguns casos, aceita autenticação SAML não solicitada, que também é conhecida como provedor de identidade iniciado.

Os metadados podem ser configurados em ambas as partes como "Metadados estáticos" ou "Metadados dinâmicos". No modo estático, você copia os metadados completos de uma parte e define-os em outra parte. No modo dinâmico, você define a URL para os metadados enquanto a outra parte lê a configuração dinamicamente. Os princípios são os mesmos, você define os metadados do perfil técnico do Azure AD B2C no seu provedor de identidade e define os metadados do provedor de identidade no Azure AD B2C.

Cada provedor de identidade SAML tem diferentes etapas para expor e definir o provedor de serviços, neste caso, o Azure AD B2C, e definir os metadados do Azure AD B2C no provedor de identidade. Confira a documentação do provedor de identidade para obter orientação sobre como fazer isso.

O exemplo a seguir mostra um endereço de URL para os metadados do SAML de um perfil técnico do Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **nome do locatário seu** com o nome do locatário, como fabrikam.b2clogin.com.
- **your-policy** pelo nome da política. Use a política em que você configura o perfil técnico do provedor SAML ou uma política que herda dessa política.
- **seu perfil técnico** com seu nome de perfil técnico do provedor de identidade SAML.

## <a name="digital-signing-certificates-exchange"></a>Troca de certificados de autenticação digital

Para criar uma relação de confiança entre o Azure AD B2C e o seu provedor de identidade SAML, você precisa fornecer um certificado X509 válido com a chave privada. Faça upload do certificado com a chave privada (arquivo .pfx) para o repositório de chaves de política do Azure AD B2C. O Azure AD B2C assina digitalmente a solicitação de entrada SAML usando o certificado que você fornecer.

O certificado é usado dos seguintes modos:

- O Azure AD B2C gera e assina uma solicitação SAML, usando a chave privada do Azure AD B2C do certificado. A solicitação SAML é enviada ao provedor de identidade, que valida a solicitação usando a chave pública do Azure AD B2C do certificado. O certificado público do Azure AD B2C é acessível por meio de metadados do perfil técnico. Como alternativa, você pode fazer upload manualmente do arquivo .cer para seu provedor de identidade SAML.
- O provedor de identidade assina os dados enviados para o Azure AD B2C usando a chave privada do provedor de identidade do certificado. O Azure AD B2C valida os dados usando o certificado público do provedor de identidade. Cada provedor de identidade tem diferentes etapas para a configuração. Confira a documentação dos seu provedor de identidade para obter orientação sobre como fazer isso. No Azure AD B2C, sua política precisa acessar a chave pública do certificado usando metadados do provedor de identidade.

Um certificado autoassinado é aceitável para a maioria dos cenários. Para ambientes de produção, é recomendável usar um certificado X509 emitido por uma autoridade de certificação. Além disso, conforme descrito posteriormente neste documento, para um ambiente de não produção é possível desabilitar a assinatura do SAML em ambos os lados.

O diagrama a seguir mostra a troca de metadados e do certificado:

![troca de metadados e do certificado](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Criptografia digital

Para criptografar a declaração de resposta SAML, o provedor de identidade sempre usa uma chave pública de um certificado de criptografia em um perfil técnico do Azure AD B2C. Quando o Azure AD B2C precisa descriptografar os dados, ele usa a parte privada do certificado de criptografia.

Para criptografar a declaração de resposta SAML:

1. Faça upload do certificado X509 válido com a chave privada (arquivo .pfx) para o repositório de chaves de política do Azure AD B2C.
2. Adicione um elemento **CryptographicKey** com um identificador de `SamlAssertionDecryption` à coleção **CryptographicKeys** do perfil técnico. Defina **StorageReferenceId** para o nome da chave de política que você criou na etapa 1.
3. Defina os metadados do perfil técnico **WantsEncryptedAssertions** para `true`.
4. Atualize o provedor de identidade com os novos metadados de perfil técnico do Azure AD B2C. Você deve ver **KeyDescriptor** com a propriedade **use** definida como `encryption` contendo a chave pública do certificado.

O exemplo a seguir mostra a seção de criptografia de perfil técnico do Azure AD B2C dos metadados:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento Protocol precisa ser definido como `SAML2`.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade SAML na seção `AttributeStatement`. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que você defina o atributo `DefaultValue`.

Para ler a declaração SAML **NamedId** no **Assunto** como uma declaração normalizada, defina a declaração **PartnerClaimType** em `assertionSubjectName`. Verifique se o **NameId** é o primeiro valor na declaração XML. Quando definir mais de uma asserção, o Azure AD B2C escolherá o valor do assunto da última declaração.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade do Facebook:

- A Declaração **issuerUserId** é mapeada para a Declaração **assertionSubjectName** .
- A declaração **first_name** é mapeada para a declaração **givenName**.
- A declaração **last_name** é mapeada para a declaração **surname**.
- A declaração **displayName** sem mapeamento de nome.
- A declaração **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **identityProvider** que contém o nome do provedor de identidade.
- A declaração **authenticationSource** com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| PartnerEntity | Sim | URL dos metadados do provedor de identidade SAML. Copie os metadados do provedor de identidade e adicione-o dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Não | Indica se o perfil técnico requer que todas as solicitações de autenticação de saída sejam assinadas. Valores possíveis: `true` ou `false`. O valor padrão é `true`. Quando o valor é definido como `true`, a chave de criptografia **SamlMessageSigning** deve ser especificada e todas as solicitações de autenticação de saída devem estar assinadas. Se o valor for definido como `false`, os parâmetros **SigAlg** e **Signature** (cadeia de caracteres de consulta ou parâmetro de postagem) serão omitidos da solicitação. Esses metadados também controlam o atributo **AuthnRequestsSigned** dos metadados, que é a saída nos metadados do perfil técnico do Azure AD B2C que é compartilhado com o provedor de identidade. Azure AD B2C não assinará a solicitação se o valor de **WantsSignedRequests** nos metadados do perfil técnico estiver definido como `false` e os metadados do provedor de identidade **WantAuthnRequestsSigned** estiverem definidos como `false` ou não especificados. |
| XmlSignatureAlgorithm | Não | O método que o Azure AD B2C usa para assinar a solicitação SAML. Esse metadado controla o valor do parâmetro **SigAlg** (cadeia de caracteres de consulta ou parâmetro de postagem) na solicitação SAML. Valores possíveis: `Sha256`, `Sha384`, `Sha512` ou `Sha1`. Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo com suporte do seu certificado. |
| WantsSignedAssertions | Não | Indica se o perfil técnico exige que todas as declarações de entrada estejam assinadas. Valores possíveis: `true` ou `false`. O valor padrão é `true`. Se o valor for definido como `true`, todas as declarações da seção `saml:Assertion` enviadas pelo provedor de identidade para o Azure AD B2C deverão estar assinadas. Se o valor for definido como `false`, o provedor de identidade não deverá assinar as declarações, mas, mesmo se isso acontecer, o Azure AD B2C não validará a assinatura. Esses metadados também controlam o **WantsAssertionsSigned** do sinalizador de metadados, que é a saída nos metadados do perfil técnico do Azure AD B2C que é compartilhado com o provedor de identidade. Se você desabilitar a validação de declarações, também deverá desabilitar a validação de assinatura de resposta (para obter mais informações, confira **ResponsesSigned**). |
| ResponsesSigned | Não | Valores possíveis: `true` ou `false`. O valor padrão é `true`. Se o valor for definido como `false`, o provedor de identidade não deverá assinar a resposta SAML, mas, mesmo se isso acontecer, o Azure AD B2C não validará a assinatura. Se o valor for definido como `true`, a resposta SAML enviada pelo provedor de identidade para o Azure AD B2C será assinada e deverá ser validada. Se você desabilitar a validação da resposta SAML, também deverá desabilitar a validação de assinatura da declaração (para obter mais informações, confira **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Não | Indica se o perfil técnico exige que todas as declarações de entrada estejam criptografadas. Valores possíveis: `true` ou `false`. O valor padrão é `false`. Se o valor for definido como `true`, as declarações enviadas pelo provedor de identidade para o Azure AD B2C deverão ser assinadas e a chave de criptografia **SamlAssertionDecryption** deverá ser especificada. Se o valor for definido como `true`, os metadados do perfil técnico do Azure AD B2C incluirão a seção de **criptografia**. O provedor de identidade lê os metadados e criptografa a declaração de resposta SAML com a chave pública que é fornecida nos metadados do perfil técnico do Azure AD B2C. Se você habilitar a criptografia de declarações, também precisará desabilitar a validação de assinatura de resposta (para obter mais informações, confira **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Não | Indica se um perfil de sessão de logon único que foi iniciado por um perfil de provedor de identidade SAML está habilitado. Valores possíveis: `true` ou `false`. O padrão é `false`. No fluxo iniciado pelo provedor de identidade, o usuário é autenticado externamente e uma resposta não solicitada é enviada para o Azure AD B2C, que, em seguida, consome o token, executa as etapas de orquestração e envia uma resposta para o aplicativo de terceira parte confiável. |
| NameIdPolicyFormat | Não | Especifica as restrições d o identificador de nome a ser usado para representar o assunto solicitado. Se omitido, qualquer tipo de identificador com suporte pelo provedor de identidade para o assunto solicitado pode ser usado. Por exemplo, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** pode ser usado com **NameIdPolicyAllowCreate**. Examine a documentação do provedor de identidade para obter orientação sobre qual nome há suporte para políticas de ID. |
| NameIdPolicyAllowCreate | Não | Ao usar **NameIdPolicyFormat**, também se pode especificar a `AllowCreate` propriedade do **NameIDPolicy**. O valor de metadados é `true` ou `false` para indicar se o provedor de identidade tem permissão para criar uma nova conta durante o fluxo de entrada. Confira a documentação do provedor de identidade para obter orientação sobre como fazer isso. |
| AuthenticationRequestExtensions | Não | Elementos de extensão de mensagem do protocolo opcional são acordados entre o Azure AD BC e o provedor de identidade. A extensão é apresentada no formato XML. Adicione os dados XML dentro do elemento CDATA `<![CDATA[Your IDP metadata]]>`. Verifique a documentação do provedor de identidade para ver se o elemento de extensões é suportado. |
| IncludeAuthnContextClassReferences | Não | Especifique uma ou mais referências de URI que identifica as classes de contexto de autenticação. Por exemplo, para permitir que um usuário entre com o nome de usuário e senha apenas, defina o valor `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Para permitir a entrada com o nome de usuário e senha ao longo de uma sessão protegida (SSL/TLS), especifique `PasswordProtectedTransport`. Examine a documentação do provedor de identidade para obter orientação sobre os URIs **AuthnContextClassRef** suportados. Especifique vários URIs como uma lista delimitada por vírgula. |
| IncludeKeyInfo | Não | Indica se a solicitação de autenticação SAML contém a chave pública do certificado quando a associação é definida como `HTTP-POST`. Valores possíveis: `true` ou `false`. |
| IncludeClaimResolvingInClaimsHandling  | Não | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina isso como `true`. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém os seguintes atributos:

| Atributo |Obrigatório | DESCRIÇÃO |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar as mensagens de SAML. O Azure AD B2C usa essa chave para assinar as solicitações e enviá-las para o provedor de identidade. |
| SamlAssertionDecryption |Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para descriptografar as mensagens de SAML. Esse certificado deve ser fornecido pelo provedor de identidade. O Azure AD B2C usa esse certificado para descriptografar os dados enviados pelo provedor de identidade. |
| MetadataSigning |Não | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar os metadados de SAML. O Azure AD B2C usa essa chave para assinar os metadados.  |

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de como trabalhar com provedores de identidade SAML no Azure AD B2C:

- [Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas](identity-provider-adfs2016-custom.md)
- [Entrar usando contas do Salesforce via SAML](identity-provider-salesforce-custom.md)
