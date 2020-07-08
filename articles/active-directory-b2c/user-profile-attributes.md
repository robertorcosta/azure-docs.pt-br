---
title: Atributos de perfil de usuário no Azure Active Directory B2C
description: Saiba mais sobre os atributos do tipo de recurso de usuário que são suportados pelo perfil de usuário do diretório do Azure AD B2C. Saiba mais sobre atributos internos, extensões e como os atributos são mapeados no Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738550"
---
# <a name="user-profile-attributes"></a>Atributos de perfil do usuário

Seu perfil do usuário do diretório do Azure Active Directory B2C é fornecido com um conjunto interno de atributos, como nome, sobrenome, cidade, CEP e número de telefone. Você pode estender o perfil do usuário com seus próprios dados de aplicativo sem a necessidade de um armazenamento de dados externo. A maioria dos atributos que podem ser usados com perfis de usuário do Azure AD B2C também tem suporte do Microsoft Graph. Este artigo descreve atributos de perfil de usuário do Azure AD B2C com suporte. Ele também anota os atributos que não têm suporte do Microsoft Graph, bem como os atributos do Microsoft Graph que não devem ser usados com Azure AD B2C.

> [!IMPORTANT]
> Você não deve usar atributos internos ou de extensão para armazenar dados pessoais confidenciais, como credenciais de conta, números de identificação governamental, dados de titulares de cartão, dados de contas financeiras, informações de saúde ou informações de histórico confidenciais.

Você também pode integrar com sistemas externos. Por exemplo, você pode usar o Azure AD B2C para autenticação, mas delegar para um CRM (gerenciamento de relacionamento com o cliente) externo ou a um banco de dados de fidelidade do cliente como a fonte de verdade dos dados do cliente. Para obter mais informações, consulte a solução de [perfil remoto](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile).

A tabela a seguir lista os atributos do [tipo de recurso de usuário](https://docs.microsoft.com/graph/api/resources/user) que são suportados pelo perfil de usuário do diretório do Azure AD B2C. Ela contém as seguintes informações sobre cada atributo:

- Nome do atributo usado pelo Azure AD B2C (seguido pelo nome do Microsoft Graph em parênteses, se for diferente)
- Tipo de dados de atributo
- Descrição do atributo
- Se o atributo estiver disponível no portal do Azure
- Se o atributo pode ser usado em um fluxo de usuário
- Se o atributo pode ser usado em uma política personalizada de [perfil técnico do Azure Active Directory](active-directory-technical-profile.md) e em qual seção (&lt;InputClaims&gt;, &lt;OutputClaims&gt; ou &lt;PersistedClaims&gt;)

|Nome     |Type     |Descrição|Portal do Azure|Fluxos de usuário|Política personalizada|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Se a conta de usuário está habilitada ou desabilitada: **true** se a conta estiver habilitada; caso contrário, **false**.|Sim|Não|Persistente, Saída|
|ageGroup        |String|O grupo de idade do usuário. Valores possíveis: null, Undefined, Minor, Adult, NotAdult.|Sim|Não|Persistente, Saída|
|alternativeSecurityId ([Identidades](manage-user-accounts-graph-api.md#identities-property))|String|Uma identidade de usuário único do provedor de identidade externo.|Não|Não|Entrada, Persistente, Saída|
|alternativeSecurityIds ([Identidades](manage-user-accounts-graph-api.md#identities-property))|coleção securityid alternativa|Uma coleção de identidades de usuário de provedores de identidade externos.|Não|Não|Persistente, Saída|
|city            |String|A cidade em que o usuário está localizado. Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|consentProvidedForMinor|String|Se o consentimento foi fornecido a um menor. Valores permitidos: null, granted, denied ou notRequired.|Sim|Não|Persistente, Saída|
|country         |String|O país/região em que o usuário está localizado. Exemplo: "EUA" ou "Reino Unido". Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|createdDateTime|Datetime|A data em que o objeto do usuário foi criado. Somente leitura.|Não|Não|Persistente, Saída|
|creationType    |String|Se a conta de usuário foi criada como uma conta local para um locatário do Azure Active Directory B2C, o valor será LocalAccount ou nameCoexistence. Somente leitura.|Não|Não|Persistente, Saída|
|dateOfBirth     |Data|Data de nascimento.|Não|Não|Persistente, Saída|
|department      |String|O nome do departamento no qual o usuário trabalha. Comprimento máximo de 64.|Sim|Não|Persistente, Saída|
|displayName     |String|O nome de exibição do usuário. Comprimento máximo de 256.|Sim|Sim|Persistente, Saída|
|facsimileTelephoneNumber<sup>1</sup>|String|O número de telefone do aparelho de fax comercial do usuário.|Sim|Não|Persistente, Saída|
|givenName       |String|O nome (primeiro nome) do usuário. Comprimento máximo de 64.|Sim|Sim|Persistente, Saída|
|jobTitle        |String|O cargo do usuário. Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|immutableId     |String|Um identificador normalmente usado para usuários migrados do Active Directory local.|Não|Não|Persistente, Saída|
|legalAgeGroupClassification|String|Classificação de grupo de faixa etária. Somente leitura e calculado com base nas propriedades ageGroup e consentProvidedForMinor. Valores permitidos: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult e adult.|Sim|Não|Persistente, Saída|
|legalCountry<sup>1</sup>  |String|País/Região para fins legais.|Não|Não|Persistente, Saída|
|mail            |String|O endereço SMTP para o usuário, por exemplo "bob@contoso.com". Somente leitura.|Não|Não|Persistente, Saída|
|mailNickName    |String|O alias de email para o usuário. Comprimento máximo de 64.|Não|Não|Persistente, Saída|
|mobile (mobilePhone) |String|O número do telefone celular principal do usuário. Comprimento máximo de 64.|Sim|Não|Persistente, Saída|
|netId           |String|ID do Net.|Não|Não|Persistente, Saída|
|objectId        |String|Um GUID (identificador global exclusivo) que é o identificador exclusivo do usuário. Exemplo: 12345678-9abc-def0-1234-56789abcde. Somente leitura, Imutável.|Somente leitura|Sim|Entrada, Persistente, Saída|
|otherMails      |Coleção de Cadeias de Caracteres|Uma lista de endereços de e-mail adicionais do usuário. Exemplo: ["bob@contoso.com", "Robert@fabrikam.com"].|Sim (E-mail alternativo)|Não|Persistente, Saída|
|password        |String|A senha da conta local durante a criação do usuário.|Não|Não|Persistente|
|passwordPolicies     |String|Política da senha. É uma cadeia de caracteres que consiste em um nome de política diferente separado por vírgula. isto é "DisablePasswordExpiration, DisableStrongPassword".|Não|Não|Persistente, Saída|
|physicalDeliveryOfficeName (officeLocation)|String|A localização do escritório na sede da empresa do usuário. Comprimento máximo de 128.|Sim|Não|Persistente, Saída|
|postalCode      |String|O CEP do endereço postal do usuário. O CEP é específico do país/região do usuário. No Estados Unidos da América, esse atributo contém o código ZIP. Comprimento máximo de 40.|Sim|Não|Persistente, Saída|
|preferredLanguage    |String|O idioma preferencial do usuário. Deve seguir o código ISO 639-1. Exemplo: "en-US".|Não|Não|Persistente, Saída|
|refreshTokensValidFromDateTime|Datetime|Todos os tokens de atualização emitidos antes desse horário são inválidos e os aplicativos receberão um erro ao usar um token de atualização inválido para adquirir um novo token de acesso. Se isso acontecer, o aplicativo precisará adquirir um novo token de atualização fazendo uma solicitação para o ponto de extremidade de autorização. Somente leitura.|Não|Não|Saída|
|signInNames ([Identidades](manage-user-accounts-graph-api.md#identities-property)) |String|O nome de entrada exclusivo do usuário da conta local de qualquer tipo no diretório. Use-o para obter um usuário com valor de entrada sem especificar o tipo de conta local.|Não|Não|Entrada|
|signInNames.userName ([Identidades](manage-user-accounts-graph-api.md#identities-property)) |String|O nome de usuário exclusivo do usuário da conta local no diretório. Use-o para criar ou obter um usuário com um nome de usuário específico para entrada. Especificar isso só no PersistedClaims durante a operação do Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, Persistente, Saída|
|signInNames.phoneNumber ([Identidades](manage-user-accounts-graph-api.md#identities-property)) |String|O número de telefone exclusivo do usuário da conta local no diretório. Use isso para criar ou obter um usuário com um número de telefone específico para entrada. Especificar isso só no PersistedClaims durante a operação do Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, Persistente, Saída|
|signInNames.emailAddress ([Identidades](manage-user-accounts-graph-api.md#identities-property))|String|O endereço de e-mail exclusivo do usuário da conta local no diretório. Use-o para criar ou obter um usuário com um e-mail específico para entrada. Especificar isso só no PersistedClaims durante a operação do Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, Persistente, Saída|
|state           |String|O estado ou a província no endereço do usuário. Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|streetAddress   |String|O endereço da sede da empresa do usuário. Comprimento máximo de 1024.|Sim|Sim|Persistente, Saída|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|Um número de telefone secundário do usuário, que é usado para autenticação multifator.|Sim|Não|Persistente, Saída|
|strongAuthenticationEmailAddress<sup>1</sup>|String|O endereço SMTP do usuário. Exemplo: "bob@contoso.com". Esse atributo é usado para entrar com a política de nome de usuário, para armazenar o endereço de e-mail do usuário. O endereço de e-mail é usado em um fluxo de redefinição de senha.|Sim|Não|Persistente, Saída|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|O número de telefone principal do usuário, usado para autenticação multifator.|Sim|Não|Persistente, Saída|
|sobrenome         |String|O sobrenome do usuário (nome de família ou último nome). Comprimento máximo de 64.|Sim|Sim|Persistente, Saída|
|telephoneNumber (primeira entrada de businessPhones)|String|O número de telefone principal do sede da empresa do usuário.|Sim|Não|Persistente, Saída|
|userPrincipalName    |String|O UPN (nome principal do usuário) do usuário. O UPN é um nome de entrada de um usuário, no estilo da Internet, para o usuário com base no padrão RFC 822 da Internet. O domínio deve estar presente na coleção de domínios verificados do locatário. Essa propriedade é necessária ao criar uma conta. Imutável.|Não|Não|Entrada, Persistente, Saída|
|usageLocation   |String|Necessário para usuários que receberão licenças devido ao requisito legal de verificar a disponibilidade de serviços em países/regiões. Não permite valor nulo. Um código de país/região de duas letras (padrão ISO 3166). Exemplos: "US", "JP" e "GB".|Sim|Não|Persistente, Saída|
|userType        |String|Um valor de cadeia de caracteres que pode ser usado para classificar os tipos de usuário em seu diretório. O valor deve ser Member. Somente leitura.|Somente leitura|Não|Persistente, Saída|
|userState (externalUserState)<sup>2</sup>|String|Exclusivamente para a conta Azure AD B2B, indica se o convite é PendingAcceptance ou Accepted.|Não|Não|Persistente, Saída|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Mostra o carimbo de data/hora para a alteração mais recente da propriedade UserState.|Não|Não|Persistente, Saída|
|<sup>1 </sup>Não tem suporte do Microsoft Graph<br><sup>2 </sup>Não deve ser usado com Azure AD B2C||||||


## <a name="extension-attributes"></a>Atributos de extensão

Em geral, você precisará criar seus próprios atributos, como nos seguintes casos:

- Um aplicativo voltado para o cliente precisa persistir para um atributo, como **LoyaltyNumber**.
- Um provedor de identidade tem um identificador exclusivo do usuário que deve ser salvo como **uniqueUserGUID**.
- Um percurso do usuário personalizado precisa persistir para um estado do usuário, como **migrationStatus**.

O Azure AD B2C estende o conjunto de atributos armazenados em cada conta de usuário. Os atributos de extensão [estender o esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) dos objetos de usuário no diretório. Os atributos de extensão só podem ser registrados em um objeto de aplicativo, mesmo que possam conter dados para um usuário. O atributo de extensão é anexado ao aplicativo chamado b2c-extensions-app. Não modifique esse aplicativo, pois ele é usado pelo Azure AD B2C para armazenar dados do usuário. Você pode encontrar esse aplicativo em Registros de aplicativo do Azure Active Directory.

> [!NOTE]
> - Até 100 atributos de extensão podem ser gravados em qualquer conta de usuário.
> - Se o aplicativo b2c-extensions-app for excluído, esses atributos de extensão serão removidos de todos os usuários junto com os dados que eles contêm.
> - Se um atributo de extensão for excluído pelo aplicativo, ele será removido de todas as contas de usuário e os valores serão excluídos.
> - O nome subjacente do atributo de extensão é gerado no formato "Extension_" + ID do Aplicativo + "_" + Nome do atributo. Por exemplo, se você criar um atributo de extensão LoyaltyNumber e a ID do Aplicativo do b2c-extensions-app for 831374b3-bd50-41bf-aa54-263ec9e050fc, o nome do atributo de extensão subjacente será: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Você usa o nome subjacente ao executar consultas do API do Graph para criar ou atualizar contas de usuário.

Os tipos de dados a seguir têm suporte ao definir uma propriedade em uma extensão de esquema:

|Tipo de propriedade |Comentários  |
|--------------|---------|
|Boolean    | Os valores possíveis: **true** ou **false**. |
|Datetime   | Deve ser especificado no formato ISO 8601. Será armazenado em UTC.   |
|Integer    | Valor de 32 bits.               |
|String     | Máximo de 256 caracteres.     |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os atributos de extensão:
- [Extensões de esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definir atributos personalizados com fluxo dos usuários](user-flow-custom-attributes.md)
- [Definir atributos personalizados com política personalizada](custom-policy-custom-attributes.md)
