---
title: Atributos de perfil de usuário no Azure Active Directory B2C
description: Conheça os atributos do tipo de recurso do usuário que são suportados pelo perfil de usuário do diretório Azure AD B2C. Descubra sobre atributos, extensões e como atributos são atribuídos ao Microsoft Graph.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057300"
---
# <a name="user-profile-attributes"></a>Atributos do perfil do usuário

O perfil de usuário do diretório B2C do Azure Active Directory (Azure AD) vem com um conjunto integrado de atributos, como nome, sobrenome, cidade, código postal e número de telefone. Você pode estender o perfil do usuário com os dados do seu próprio aplicativo sem precisar de um armazenamento de dados externo. A maioria dos atributos que podem ser usados com perfis de usuários Azure AD B2C também são suportados pelo Microsoft Graph. Este artigo descreve atributos de perfil de usuário AD B2C suportados. Ele também observa os atributos que não são suportados pelo Microsoft Graph, bem como atributos do Microsoft Graph que não devem ser usados com o Azure AD B2C.

> [!IMPORTANT]
> Você não deve usar atributos integrados ou de extensão para armazenar dados pessoais confidenciais, como credenciais de conta, números de identificação do governo, dados de titulares de cartão, dados de contas financeiras, informações de saúde ou informações confidenciais de fundo.

Você também pode se integrar com sistemas externos. Por exemplo, você pode usar o Azure AD B2C para autenticação, mas delegar a um CRM (customer relationship management, gerenciamento externo de relacionamento com o cliente) ou banco de dados de fidelização de clientes como a fonte autorizada de dados do cliente. Para obter mais informações, consulte a solução de [perfil remoto.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

A tabela abaixo lista os atributos do [tipo de recurso](https://docs.microsoft.com/graph/api/resources/user) do usuário que são suportados pelo perfil de usuário do diretório Azure AD B2C. Ele fornece as seguintes informações sobre cada atributo:

- Nome de atributo usado pelo Azure AD B2C (seguido pelo nome Microsoft Graph em parênteses, se diferente)
- Tipo de dados de atributo
- Descrição do atributo
- Se o atributo estiver disponível no portal Azure
- Se o atributo pode ser usado em um fluxo de usuário
- Se o atributo pode ser usado em uma política personalizada,&lt;o&gt;perfil &lt;técnico&gt;do &lt; [Azure AD](active-directory-technical-profile.md) e em qual seção (InputClaims, OutputClaims ou PersistedClaims)&gt;

|Nome     |Type     |Descrição|Portal do Azure|Fluxos de usuário|Política personalizada|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Se a conta de usuário está ativada ou desativada: **verdade** se a conta estiver ativada, caso **contrário, falsa**.|Sim|Não|Persistido, Saída|
|ageGroup        |String|A faixa etária do usuário. Valores possíveis: nulo, Indefinido, Menor, Adulto, Não Adulto.|Sim|Não|Persistido, Saída|
|alternativaSecurityId[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|String|Uma única identidade de usuário do provedor de identidade externa.|Não|Não|Entrada, Persisted, Saída|
|alternativasSecurityIds[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|coleção de segurança alternativaId|Uma coleção de identidades de usuários de provedores de identidade externos.|Não|Não|Persistido, Saída|
|city            |String|A cidade em que o usuário está localizado. Comprimento máximo 128.|Sim|Sim|Persistido, Saída|
|consentProvidedForMinor|String|Se o consentimento foi fornecido para um menor. Valores permitidos: nulos, concedidos, negados ou não.|Sim|Não|Persistido, Saída|
|country         |String|O país/região em que o usuário está localizado. Exemplo: "EUA" ou "Reino Unido". Comprimento máximo 128.|Sim|Sim|Persistido, Saída|
|createdDateTime|Datetime|A data em que o objeto do usuário foi criado. Somente leitura.|Não|Não|Persistido, Saída|
|creationType    |String|Se a conta de usuário foi criada como uma conta local para um inquilino B2C do Azure Active Directory, o valor será LocalAccount ou nameCoexistence. Somente leitura.|Não|Não|Persistido, Saída|
|dateOfBirth     |Data|Data de nascimento.|Não|Não|Persistido, Saída|
|department      |String|O nome do departamento em que o usuário trabalha. Comprimento máximo 64.|Sim|Não|Persistido, Saída|
|displayName     |String|O nome de exibição para o usuário. Comprimento máximo 256.|Sim|Sim|Persistido, Saída|
|facsímileTelefoneNúmero<sup>1</sup>|String|O número de telefone do aparelho de fax comercial do usuário.|Sim|Não|Persistido, Saída|
|givenName       |String|O nome especificado do usuário. Comprimento máximo 64.|Sim|Sim|Persistido, Saída|
|jobTitle        |String|O cargo do usuário. Comprimento máximo 128.|Sim|Sim|Persistido, Saída|
|immutableId     |String|Um identificador que normalmente é usado para usuários migrados do Active Directory no local.|Não|Não|Persistido, Saída|
|LegalAgeGroupClassification|String|Classificação da faixa etária legal. Somente leitura e calculado com base no ageGroup e no consentProvidedForMinor properties. Valores permitidos: nulo, menorComOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult and adult.|Sim|Não|Persistido, Saída|
|legalPaís<sup>1</sup>  |String|País para fins legais.|Não|Não|Persistido, Saída|
|mail            |String|O endereço SMTP para o usuário, por exemplo, "bob@contoso.com". Somente leitura.|Não|Não|Persistido, Saída|
|mailNickName    |String|O alias de email do usuário. Comprimento máximo 64.|Não|Não|Persistido, Saída|
|celular (mobilePhone) |String|O número de telefone celular principal do usuário. Comprimento máximo 64.|Sim|Não|Persistido, Saída|
|Netid           |String|ID Líquido.|Não|Não|Persistido, Saída|
|objectId        |String|Um identificador globalmente único (GUID) que é o identificador exclusivo para o usuário. Exemplo: 12345678-9abc-def0-1234-56789abcde. Só leitura, imutável.|Somente leitura|Sim|Entrada, Persisted, Saída|
|otherMails      |Coleção de Cadeias de Caracteres|Uma lista de endereços de email adicionais do usuário. Exemplo: ["bob@contoso.comRobert@fabrikam.com", "", ".".|Sim (E-mail alternativo)|Não|Persistido, Saída|
|password        |String|A senha da conta local durante a criação do usuário.|Não|Não|Persistentes|
|passwordPolicies     |String|Política da senha. É uma seqüência que consiste em diferentes nomes de política separados por comma. ou seja, "DesativarPasswordExpiration, DisableStrongPassword".|Não|Não|Persistido, Saída|
|físicoDeliveryOfficeName (officeLocation)|String|O local do escritório na central de negócios do usuário. Comprimento máximo 128.|Sim|Não|Persistido, Saída|
|postalCode      |String|O CEP do endereço postal do usuário. O CEP é específico do país/região do usuário. Nos Estados Unidos da América, este atributo contém o CEP. Comprimento máximo 40.|Sim|Não|Persistido, Saída|
|preferredLanguage    |String|O idioma preferencial do usuário. Deve seguir o Código ISO 639-1. Exemplo: "en-US".|Não|Não|Persistido, Saída|
|refreshTokensValidFromDateTime|Datetime|Quaisquer tokens de atualização emitidos antes desse momento são inválidos, e os aplicativos terão um erro ao usar um token de atualização inválido para adquirir um novo token de acesso. Se isso acontecer, o aplicativo precisará adquirir um novo token de atualização, fazendo uma solicitação ao ponto final autorizado. Somente leitura.|Não|Não|Saída|
|signInNames[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |String|O nome de login exclusivo do usuário da conta local de qualquer tipo no diretório. Use isso para obter um usuário com valor de login sem especificar o tipo de conta local.|Não|Não|Entrada|
|signInNames.userName[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |String|O nome de usuário exclusivo do usuário da conta local no diretório. Use isso para criar ou obter um usuário com um nome de usuário de login específico. Especificar isso apenas em PersistedClaims durante a operação patch removerá outros tipos de signInNames. Se você quiser adicionar um novo tipo de signInNames, você também precisa persistir no signInNames existente.|Não|Não|Entrada, Persisted, Saída|
|signInNames.phoneNumber[(Identidades)](manage-user-accounts-graph-api.md#identities-property) |String|O número de telefone único do usuário da conta local no diretório. Use isso para criar ou obter um usuário com um número de telefone de login específico. Especificar isso apenas em PersistedClaims durante a operação patch removerá outros tipos de signInNames. Se você quiser adicionar um novo tipo de signInNames, você também precisa persistir no signInNames existente.|Não|Não|Entrada, Persisted, Saída|
|signInNames.emailAddress[(Identidades)](manage-user-accounts-graph-api.md#identities-property)|String|O endereço de e-mail exclusivo do usuário da conta local no diretório. Use isso para criar ou obter um usuário com um endereço de e-mail específico de login. Especificar isso apenas em PersistedClaims durante a operação patch removerá outros tipos de signInNames. Se você quiser adicionar um novo tipo de signInNames, você também precisa persistir no signInNames existente.|Não|Não|Entrada, Persisted, Saída|
|state           |String|O estado ou município no endereço do usuário. Comprimento máximo 128.|Sim|Sim|Persistido, Saída|
|streetAddress   |String|O endereço da central de negócios do usuário. Comprimento máximo 1024.|Sim|Sim|Persistido, Saída|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|O número de telefone secundário do usuário, usado para autenticação multifatorial.|Sim|Não|Persistido, Saída|
|strongAuthenticationEmailAddress<sup>1</sup>|String|O endereço SMTP para o usuário. Exemplo:bob@contoso.com" Este atributo é usado para login com política de nome de usuário, para armazenar o endereço de e-mail do usuário. O endereço de e-mail então usado em um fluxo de redefinição de senha.|Sim|Não|Persistido, Saída|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|O número de telefone principal do usuário, usado para autenticação multifatorial.|Sim|Não|Persistido, Saída|
|sobrenome         |String|O sobrenome do usuário (nome ou sobrenome da família). Comprimento máximo 64.|Sim|Sim|Persistido, Saída|
|número de telefone (primeira entrada de businessPhones)|String|O número de telefone principal da central de negócios do usuário.|Sim|Não|Persistido, Saída|
|userPrincipalName    |String|O UPN (nome principal do usuário) do usuário. O UPN é um nome de logon no estilo da Internet para o usuário com base no padrão da Internet RFC 822. O domínio deve estar presente na coleção de domínios verificados pelo inquilino. Essa propriedade é necessária quando uma conta é criada. Imutável.|Não|Não|Entrada, Persisted, Saída|
|usageLocation   |String|Obrigatório para os usuários que serão atribuídos certificados por exigência legal para verificar se há disponibilidade de serviços em países. Não permite valor nulo. Um código de duas letras do país (norma ISO 3166). Exemplos: "EUA", "JP" e "GB".|Sim|Não|Persistido, Saída|
|userType        |String|Um valor de string que pode ser usado para classificar os tipos de usuário em seu diretório. O valor deve ser membro. Somente leitura.|Somente leitura|Não|Persistido, Saída|
|userState (externalUserState)<sup>2</sup>|String|Apenas para conta Azure AD B2B, indica se o convite é Pendenteaceita ou Aceito.|Não|Não|Persistido, Saída|
|userStateChangedOn (externoUserStateChangeDateTime)<sup>2</sup>|Datetime|Mostra o carimbo de data e hora da última alteração na propriedade UserState.|Não|Não|Persistido, Saída|
|<sup>1</sup> Não suportado pelo Microsoft Graph<br><sup>2</sup> Não deve ser usado com Azure AD B2C||||||


## <a name="extension-attributes"></a>Atributos de extensão

Muitas vezes você precisará criar seus próprios atributos, como nos seguintes casos:

- Um aplicativo voltado para o cliente precisa persistir para um atributo como **O Número de Lealdade**.
- Um provedor de identidade tem um identificador exclusivo do usuário que deve ser salvo como **uniqueUserGUID**.
- Uma jornada de usuário personalizada precisa persistir para um estado de um usuário, como **migrationStatus**.

O Azure AD B2C estende o conjunto de atributos armazenados em cada conta de usuário. Os atributos de [extensão estendem o esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) dos objetos do usuário no diretório. Os atributos de extensão só podem ser registrados em um objeto de aplicativo, mesmo que eles possam conter dados para um usuário. O atributo de extensão é anexado ao aplicativo chamado b2c-extensions-app. Não modifique este aplicativo, pois ele é usado pelo Azure AD B2C para armazenar dados do usuário. Você pode encontrar este aplicativo nos registros do Aplicativo do Diretório Ativo do Azure.

> [!NOTE]
> - Até 100 atributos de extensão podem ser gravados em qualquer conta de usuário.
> - Se o aplicativo b2c-extensions-app for excluído, esses atributos de extensão serão removidos de todos os usuários, juntamente com quaisquer dados que contenham.
> - Se um atributo de extensão for excluído pelo aplicativo, ele será removido de todas as contas de usuário e os valores serão excluídos.
> - O nome subjacente do atributo de extensão é gerado no formato "Extension_" + ID do aplicativo + "_" + Nome do atributo. Por exemplo, se você criar um atributo de extensão LoyaltyNumber, e o iD do aplicativo de extensão b2c-extensões for 831374b3-bd50-41bf-aa54-263ec9e050fc, o nome do atributo de extensão subjacente será: extension_831374b3bd5041bfaa54263ec9e050fc_ Número de lealdade. Você usa o nome subjacente quando executa consultas de API do Gráfico para criar ou atualizar contas de usuário.

Os seguintes tipos de dados são suportados ao definir uma propriedade em uma extensão de esquema:

|Tipo de propriedade |Comentários  |
|--------------|---------|
|Boolean    | Valores possíveis: **verdadeiros** ou **falsos**. |
|Datetime   | Deve ser especificada no formato ISO 8601. Será armazenada em UTC.   |
|Integer    | Valor de 32 bits.               |
|String     | Máximo de 256 caracteres.     |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre atributos de extensão:
- [Extensões de esquema](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Defina atributos personalizados com o fluxo do usuário](user-flow-custom-attributes.md)
- [Defina atributos personalizados com política personalizada](custom-policy-custom-attributes.md)
