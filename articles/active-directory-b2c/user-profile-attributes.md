---
title: Atributos de perfil de usuário no Azure Active Directory B2C
description: Saiba mais sobre os atributos do tipo de recurso de usuário que são suportados pelo perfil de usuário do Azure AD B2C Directory. Saiba mais sobre atributos internos, extensões e como os atributos são mapeados para Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057300"
---
# <a name="user-profile-attributes"></a>Atributos de perfil do usuário

Seu perfil de usuário de diretório do Azure Active Directory (Azure AD) B2C é fornecido com um conjunto interno de atributos, como nome, sobrenome, cidade, CEP e número de telefone fornecidos. Você pode estender o perfil do usuário com seus próprios dados de aplicativo sem a necessidade de um armazenamento de dados externo. A maioria dos atributos que podem ser usados com Azure AD B2C perfis de usuário também tem suporte do Microsoft Graph. Este artigo descreve Azure AD B2C atributos de perfil de usuário com suporte. Ele também anota os atributos que não têm suporte pelo Microsoft Graph, bem como Microsoft Graph atributos que não devem ser usados com Azure AD B2C.

> [!IMPORTANT]
> Você não deve usar atributos internos ou de extensão para armazenar dados pessoais confidenciais, como credenciais de conta, números de identificação governamental, dados de titulares de cartão, dados de contas financeiras, informações de saúde ou informações de histórico confidenciais.

Você também pode integrar com sistemas externos. Por exemplo, você pode usar Azure AD B2C para autenticação, mas delegar a um CRM (gerenciamento de relacionamento com o cliente) ou a um banco de dados de fidelidade do cliente como a fonte de autorização do cliente. Para obter mais informações, consulte a solução de [perfil remoto](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) .

A tabela a seguir lista os atributos do [tipo de recurso de usuário](https://docs.microsoft.com/graph/api/resources/user) que são suportados pelo perfil de usuário do Azure ad B2C Directory. Ele fornece as seguintes informações sobre cada atributo:

- Nome do atributo usado pelo Azure AD B2C (seguido pelo nome do Microsoft Graph entre parênteses, se for diferente)
- Tipo de dados de atributo
- Descrição do atributo
- Se o atributo estiver disponível no portal do Azure
- Se o atributo puder ser usado em um fluxo de usuário
- Se o atributo puder ser usado em um [perfil técnico do Azure ad](active-directory-technical-profile.md) de política personalizada e em qual&lt;seção&gt;( &lt;InputClaims&gt;, OutputClaims &lt;ou&gt;PersistedClaims)

|Nome     |Type     |Descrição|Portal do Azure|Fluxos de usuário|Política personalizada|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Se a conta de usuário está habilitada ou desabilitada: **true** se a conta estiver habilitada; caso contrário, **false**.|Sim|Não|Persistido, saída|
|ageGroup        |String|O grupo de idade do usuário. Valores possíveis: nulo, indefinido, secundário, adulto, não adulto.|Sim|Não|Persistido, saída|
|alternativeSecurityId ([identidades](manage-user-accounts-graph-api.md#identities-property))|String|Uma identidade de usuário único do provedor de identidade externa.|Não|Não|Entrada, persistida, saída|
|alternativeSecurityIds ([identidades](manage-user-accounts-graph-api.md#identities-property))|coleção securityid alternativa|Uma coleção de identidades de usuário de provedores de identidade externos.|Não|Não|Persistido, saída|
|city            |String|A cidade em que o usuário está localizado. Comprimento máximo de 128.|Sim|Sim|Persistido, saída|
|consentProvidedForMinor|String|Se o consentimento foi fornecido para um secundário. Valores permitidos: nulo, concedido, negado ou não obrigatório.|Sim|Não|Persistido, saída|
|country         |String|O país/região em que o usuário está localizado. Exemplo: "US" ou "UK". Comprimento máximo de 128.|Sim|Sim|Persistido, saída|
|createdDateTime|Datetime|A data em que o objeto de usuário foi criado. Somente leitura.|Não|Não|Persistido, saída|
|creationType    |String|Se a conta de usuário tiver sido criada como uma conta local para um locatário Azure Active Directory B2C, o valor será LocalAccount ou nameCoexistence. Somente leitura.|Não|Não|Persistido, saída|
|dateOfBirth     |Data|Data de nascimento.|Não|Não|Persistido, saída|
|department      |String|O nome do departamento em que o usuário trabalha. Comprimento máximo de 64.|Sim|Não|Persistido, saída|
|displayName     |String|O nome de exibição do usuário. Comprimento máximo de 256.|Sim|Sim|Persistido, saída|
|facsimileTelephoneNumber<sup>1</sup>|String|O número de telefone do aparelho de fax comercial do usuário.|Sim|Não|Persistido, saída|
|givenName       |String|O nome especificado do usuário. Comprimento máximo de 64.|Sim|Sim|Persistido, saída|
|jobTitle        |String|O cargo do usuário. Comprimento máximo de 128.|Sim|Sim|Persistido, saída|
|immutableId     |String|Um identificador que é normalmente usado para usuários migrados do Active Directory local.|Não|Não|Persistido, saída|
|legalAgeGroupClassification|String|Classificação de grupo de idade legal. Somente leitura e calculado com base nas propriedades ageGroup e consentProvidedForMinor. Valores permitidos: NULL, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, não adulto e adulto.|Sim|Não|Persistido, saída|
|Atributos<sup>1</sup>  |String|País para fins legais.|Não|Não|Persistido, saída|
|mail            |String|O endereço SMTP para o usuário, por exemplo, "bob@contoso.com". Somente leitura.|Não|Não|Persistido, saída|
|mailNickName    |String|O alias de email do usuário. Comprimento máximo de 64.|Não|Não|Persistido, saída|
|dispositivos móveis (mobilePhone) |String|O número de telefone celular principal do usuário. Comprimento máximo de 64.|Sim|Não|Persistido, saída|
|Com e/o           |String|ID do .net.|Não|Não|Persistido, saída|
|objectId        |String|Um GUID (identificador global exclusivo) que é o identificador exclusivo do usuário. Exemplo: 12345678-9abc-def0-1234-56789abcde. Somente leitura, imutável.|Somente leitura|Sim|Entrada, persistida, saída|
|otherMails      |Coleção de Cadeias de Caracteres|Uma lista de endereços de email adicionais do usuário. Exemplo: ["bob@contoso.com", "Robert@fabrikam.com"].|Sim (email alternativo)|Não|Persistido, saída|
|password        |String|A senha da conta local durante a criação do usuário.|Não|Não|Persistentes|
|passwordPolicies     |String|Política da senha. É uma cadeia de caracteres que consiste em um nome de política diferente separado por vírgula. ou seja, "DisablePasswordExpiration, DisableStrongPassword".|Não|Não|Persistido, saída|
|physicalDeliveryOfficeName (officeLocation)|String|O local do escritório na central de negócios do usuário. Comprimento máximo de 128.|Sim|Não|Persistido, saída|
|postalCode      |String|O CEP do endereço postal do usuário. O CEP é específico do país/região do usuário. Nos Estados Unidos da América, este atributo contém o CEP. Comprimento máximo de 40.|Sim|Não|Persistido, saída|
|preferredLanguage    |String|O idioma preferencial do usuário. Deve seguir o código ISO 639-1. Exemplo: "en-US".|Não|Não|Persistido, saída|
|refreshTokensValidFromDateTime|Datetime|Todos os tokens de atualização emitidos antes dessa hora são inválidos e os aplicativos receberão um erro ao usar um token de atualização inválido para adquirir um novo token de acesso. Se isso acontecer, o aplicativo precisará adquirir um novo token de atualização fazendo uma solicitação para o ponto de extremidade de autorização. Somente leitura.|Não|Não|Saída|
|signInNames ([identidades](manage-user-accounts-graph-api.md#identities-property)) |String|O nome de entrada exclusivo do usuário da conta local de qualquer tipo no diretório. Use isso para obter um usuário com valor de entrada sem especificar o tipo de conta local.|Não|Não|Entrada|
|signInNames. userName ([identidades](manage-user-accounts-graph-api.md#identities-property)) |String|O nome de usuário exclusivo da conta local no diretório. Use isso para criar ou obter um usuário com um nome de logon específico. Especificar isso no PersistedClaims sozinho durante a operação do patch removerá outros tipos de signInNames. Se você quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, persistida, saída|
|signInNames. phoneNumber ([identidades](manage-user-accounts-graph-api.md#identities-property)) |String|O número de telefone exclusivo do usuário da conta local no diretório. Use isso para criar ou obter um usuário com um número de telefone de entrada específico. Especificar isso no PersistedClaims sozinho durante a operação do patch removerá outros tipos de signInNames. Se você quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, persistida, saída|
|signInNames. emailAddress ([identidades](manage-user-accounts-graph-api.md#identities-property))|String|O endereço de email exclusivo do usuário da conta local no diretório. Use isso para criar ou obter um usuário com um endereço de email de entrada específico. Especificar isso no PersistedClaims sozinho durante a operação do patch removerá outros tipos de signInNames. Se você quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, persistida, saída|
|state           |String|O estado ou município no endereço do usuário. Comprimento máximo de 128.|Sim|Sim|Persistido, saída|
|streetAddress   |String|O endereço da central de negócios do usuário. Comprimento máximo de 1024.|Sim|Sim|Persistido, saída|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|O número de telefone secundário do usuário, usado para autenticação multifator.|Sim|Não|Persistido, saída|
|strongAuthenticationEmailAddress<sup>1</sup>|String|O endereço SMTP do usuário. Exemplo: "bob@contoso.com" este atributo é usado para entrar com a política de nome de usuário, para armazenar o endereço de email do usuário. O endereço de email é usado em um fluxo de redefinição de senha.|Sim|Não|Persistido, saída|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|O número de telefone principal do usuário, usado para autenticação multifator.|Sim|Não|Persistido, saída|
|sobrenome         |String|O sobrenome do usuário (nome ou sobrenome da família). Comprimento máximo de 64.|Sim|Sim|Persistido, saída|
|telephoneNumber (primeira entrada de businessPhones)|String|O número de telefone principal da central de negócios do usuário.|Sim|Não|Persistido, saída|
|userPrincipalName    |String|O UPN (nome principal do usuário) do usuário. O UPN é um nome de logon no estilo da Internet para o usuário com base no padrão da Internet RFC 822. O domínio deve estar presente na coleção de domínios verificados do locatário. Essa propriedade é necessária quando uma conta é criada. Imutável.|Não|Não|Entrada, persistida, saída|
|usageLocation   |String|Obrigatório para os usuários que serão atribuídos certificados por exigência legal para verificar se há disponibilidade de serviços em países. Não permite valor nulo. Um código de duas letras do país (norma ISO 3166). Exemplos: "US", "JP" e "GB".|Sim|Não|Persistido, saída|
|userType        |String|Um valor de cadeia de caracteres que pode ser usado para classificar tipos de usuário em seu diretório. O valor deve ser membro. Somente leitura.|Somente leitura|Não|Persistido, saída|
|userState (externalUserState)<sup>2</sup>|String|Somente para conta B2B do Azure AD, indica se o convite é PendingAcceptance ou aceito.|Não|Não|Persistido, saída|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Mostra o carimbo de data/hora para a alteração mais recente na propriedade UserState.|Não|Não|Persistido, saída|
|<sup>1</sup> Sem suporte pelo Microsoft Graph<br><sup>2</sup> Não deve ser usado com Azure AD B2C||||||


## <a name="extension-attributes"></a>Atributos de extensão

Em geral, você precisará criar seus próprios atributos, como nos seguintes casos:

- Um aplicativo voltado para o cliente precisa manter um atributo como **LoyaltyNumber**.
- Um provedor de identidade tem um identificador exclusivo do usuário que deve ser salvo como **uniqueUserGUID**.
- Uma jornada de usuário personalizado precisa persistir para um estado de um usuário, como **migrationStatus**.

O Azure AD B2C estende o conjunto de atributos armazenados em cada conta de usuário. Os atributos de extensão [estendem o esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) dos objetos de usuário no diretório. Os atributos de extensão só podem ser registrados em um objeto de aplicativo, mesmo que possam conter dados para um usuário. O atributo de extensão é anexado ao aplicativo chamado B2C-Extensions-app. Não modifique esse aplicativo, pois ele é usado pelo Azure AD B2C para armazenar dados do usuário. Você pode encontrar esse aplicativo em Azure Active Directory Registros de aplicativo.

> [!NOTE]
> - Até 100 atributos de extensão podem ser gravados em qualquer conta de usuário.
> - Se o aplicativo B2C-Extensions-App for excluído, esses atributos de extensão serão removidos de todos os usuários junto com os dados que eles contêm.
> - Se um atributo de extensão for excluído pelo aplicativo, ele será removido de todas as contas de usuário e os valores serão excluídos.
> - O nome subjacente do atributo de extensão é gerado no formato "Extension_" + ID do aplicativo + "_" + nome do atributo. Por exemplo, se você criar um atributo de extensão LoyaltyNumber e a ID do aplicativo B2C-Extensions-App for 831374b3-BD50-41bf-aa54-263ec9e050fc, o nome do atributo de extensão subjacente será: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Você usa o nome subjacente ao executar API do Graph consultas para criar ou atualizar contas de usuário.

Os tipos de dados a seguir têm suporte ao definir uma propriedade em uma extensão de esquema:

|Tipo de propriedade |Comentários  |
|--------------|---------|
|Boolean    | Valores possíveis: **true** ou **false**. |
|Datetime   | Deve ser especificada no formato ISO 8601. Será armazenada em UTC.   |
|Integer    | Valor de 32 bits.               |
|String     | Máximo de 256 caracteres.     |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre atributos de extensão:
- [Extensões de esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definir atributos personalizados com o fluxo do usuário](user-flow-custom-attributes.md)
- [Definir atributos personalizados com política personalizada](custom-policy-custom-attributes.md)
