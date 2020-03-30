---
title: Configure reivindicações de grupo para aplicativos com o Azure Active Directory | Microsoft Docs
description: Informações sobre como configurar reivindicações de grupo para uso com o Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408395"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Configure reivindicações de grupo para aplicativos com o Azure Active Directory (Visualização Pública)

O Azure Active Directory pode fornecer a um grupo de usuários informações de membros em tokens para uso em aplicativos.  Dois padrões principais são suportados:

- Grupos identificados pelo identificador de objeto do Azure Active Directory (OID) (Geralmente Disponível)
- Grupos identificados por atributos sAMAccountName ou GroupSID para grupos e usuários sincronizados do Active Directory (AD) (Visualização Pública)

> [!IMPORTANT]
> Há uma série de ressalvas a serem anotadas para esta funcionalidade de visualização:
>
>- O suporte ao uso de atributos sAMAccountName e de identificador de segurança (SID) sincronizados a partir de locais foi projetado para permitir a movimentação de aplicativos existentes do AD FS e outros provedores de identidade. Os grupos gerenciados no Azure AD não contêm os atributos necessários para emitir essas reivindicações.
>- Em organizações maiores, o número de grupos dos grupos que um usuário é membro pode exceder o limite que o Azure Active Directory adicionará a um token. 150 grupos para um token SAML, e 200 para um JWT. Isso pode levar a resultados imprevisíveis. Se seus usuários tiverem um grande número de membros de grupo, recomendamos usar a opção de restringir os grupos emitidos em reclamações aos grupos relevantes para o aplicativo.  
>- Para o desenvolvimento de novos aplicativos, ou nos casos em que o aplicativo pode ser configurado para ele, e onde o suporte a grupo aninhado não é necessário, recomendamos que a autorização no aplicativo seja baseada em funções de aplicativo em vez de grupos.  Isso limita a quantidade de informações que precisam entrar no token, são mais seguras e separam a atribuição do usuário da configuração do aplicativo.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Reclamações em grupo para aplicativos migrados de AD FS e outros provedores de identidade

Muitos aplicativos configurados para autenticar com OD FS dependem de informações de membros de grupo na forma de atributos do grupo Windows AD.   Esses atributos são o grupo sAMAccountName, que pode ser qualificado por nome de domínio, ou o Identificador de Segurança do Grupo Windows (GroupSID).  Quando o aplicativo é federado com AD FS, o AD FS usa a função TokenGroups para recuperar as associações do grupo para o usuário.

Um aplicativo que foi movido do AD FS precisa de reclamações no mesmo formato. As reivindicações de grupo e função podem ser emitidas do Azure Active Directory contendo o sAMAccountName qualificado de domínio ou o GroupSID sincronizado do Active Directory em vez do objectID do Azure Active Directory do grupo.

Os formatos suportados para reivindicações de grupo são:

- **ObjectId do Grupo de Diretório Ativo do Azure** (Disponível para todos os grupos)
- **sAMAccountName** (Disponível para grupos sincronizados no Active Directory)
- **NetbiosDomain\sAMAccountName** (Disponível para grupos sincronizados do Active Directory)
- **DNSDomainName\sAMAccountName** (Disponível para grupos sincronizados no Active Directory)
- **Identificador de segurança do grupo no local** (disponível para grupos sincronizados no Active Directory)

> [!NOTE]
> Os atributos SID do grupo sAMAccountName e On Premises estão disponíveis apenas em objetos de grupo sincronizados do Active Directory.   Eles não estão disponíveis em grupos criados no Azure Active Directory ou Office365.   Os aplicativos configurados no Azure Active Directory para obter atributos sincronizados do grupo no local obtê-los apenas para grupos sincronizados.

## <a name="options-for-applications-to-consume-group-information"></a>Opções para aplicativos para consumir informações de grupo

Os aplicativos podem chamar o ponto final dos grupos do MS Graph para obter informações de grupo para o usuário autenticado. Esta chamada garante que todos os grupos de que um usuário é membro estejam disponíveis mesmo quando há um grande número de grupos envolvidos.  A enumeração do grupo é então independente das limitações de tamanho do token.

No entanto, se um aplicativo existente espera consumir informações de grupo por meio de reclamações, o Azure Active Directory pode ser configurado com vários formatos de sinistros diferentes.  Considere as seguintes opções:

- Ao usar a adesão ao grupo para fins de autorização de aplicativo, é preferível usar o Grupo ObjectID. O Group ObjectID é imutável e único no Azure Active Directory e está disponível para todos os grupos.
- Se usar o grupo local sAMAccountName para autorização, use nomes qualificados de domínio;  há menos chance de nomes se chocarem. sAMAccountName pode ser único dentro de um domínio do Active Directory, mas se mais de um domínio do Active Directory estiver sincronizado com um inquilino do Azure Active Directory, há a possibilidade de mais de um grupo ter o mesmo nome.
- Considere usar [funções de aplicativo](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) para fornecer uma camada de indireção entre a associação do grupo e o aplicativo.   O aplicativo, então, toma decisões de autorização interna com base em moluscos de função no token.
- Se o aplicativo estiver configurado para obter atributos de grupo sincronizados do Active Directory e um Grupo não contiver esses atributos, ele não será incluído nas reivindicações.
- As reivindicações em grupo em tokens incluem grupos aninhados, exceto quando usam a opção de restringir as reivindicações do grupo a grupos atribuídos ao aplicativo.  Se um usuário for membro do GroupB e o GroupB for membro do GroupA, então as reivindicações do grupo para o usuário conterão tanto o GroupA quanto o GroupB. Quando os usuários de uma organização têm um grande número de membros de grupos, o número de grupos listados no token pode aumentar o tamanho do token.  O Azure Active Directory limita o número de grupos que ele emitirá em um token para 150 para afirmações SAML e 200 para JWT.  Se um usuário for membro de um número maior de grupos, os grupos serão omitidos e um link para o ponto final do Gráfico para obter informações de grupo será incluído.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Pré-requisitos para o uso de atributos de grupo sincronizados a partir do Active Directory

As reivindicações de membros do grupo podem ser emitidas em tokens para qualquer grupo se você usar o formato ObjectId. Para usar reivindicações de grupo em formatos diferentes do grupo ObjectId, os grupos devem ser sincronizados a partir do Active Directory usando o Azure AD Connect.

Existem duas etapas para configurar o Azure Active Directory para emitir nomes de grupos para Grupos de Diretórios Ativos.

1. **Sincronizar nomes de grupo do Active Directory** Antes que o Azure Active Directory possa emitir os nomes do grupo ou no grupo de instalações SID em reclamações de grupo ou de função, os atributos necessários precisam ser sincronizados a partir do Active Directory.  Você deve estar executando a versão 1.2.70 do Azure AD Connect ou posterior.   Versões anteriores do Azure AD Connect do que 1.2.70 sincronizarão os objetos de grupo do Active Directory, mas não incluirão os atributos de nome de grupo necessários.  Atualize para a versão atual.

2. **Configure o registro do aplicativo no Azure Active Directory para incluir reivindicações de grupo em tokens** As reivindicações de grupo podem ser configuradas na seção Aplicações Corporativas do portal ou usando o Manifesto de Aplicativo na seção Registros de Aplicativos.  Para configurar reivindicações de grupo no manifesto do aplicativo, consulte "Configurando o registro do aplicativo de diretório ativo do Azure para atributos de grupo" abaixo.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Adicionar reivindicações de grupo a tokens para aplicativos SAML usando a configuração SSO

Para configurar reivindicações de grupo para um aplicativo SAML galeria ou não-galeria, abra **aplicativos corporativos,** clique no aplicativo na lista, selecione **Configuração de Único sinal**e selecione **Atributos de usuário & Reclamações**.

Clique em **Adicionar uma reivindicação de grupo**  

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Use os botões de rádio para selecionar quais grupos devem ser incluídos no token

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Seleção | Descrição |
|----------|-------------|
| **Todos os grupos** | Emite grupos de segurança e listas de distribuição e funções.  |
| **Grupos de segurança** | Emite grupos de segurança dos estados que o usuário é membro da reivindicação dos grupos |
| **Funções de diretório** | Se o usuário tiver as funções de diretório atribuídas, ela será emitida como uma reivindicação de 'wids' (os grupos afirmam que não serão emitidos) |
| **Grupos atribuídos ao aplicativo** | Emite apenas os grupos que são explicitamente atribuídos ao aplicativo e o usuário é um membro do |

Por exemplo, para emitir todos os Grupos de segurança dos estados de como membro, selecione Grupos de segurança

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Para emitir grupos usando atributos active directory sincronizados do Active Directory em vez de ObjectIDs Azure AD selecione o formato necessário a partir do suspenso. Somente grupos sincronizados do Active Directory serão incluídos nas reivindicações.

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Para emitir apenas grupos atribuídos ao aplicativo, selecione **Grupos atribuídos ao aplicativo**

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Os grupos atribuídos ao aplicativo serão incluídos no token.  Outros grupos dos que o usuário é membro serão omitidos.  Com esta opção, grupos aninhados não estão incluídos e o usuário deve ser um membro direto do grupo atribuído ao aplicativo.

Para alterar os grupos atribuídos ao aplicativo, selecione o aplicativo na lista **Aplicativos Corporativos** e clique em **Usuários e Grupos** no menu de navegação à esquerda do aplicativo.

Consulte o documento [Atribuir um usuário ou grupo a um aplicativo corporativo](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) para obter detalhes sobre a atribuição de grupo gerenciada aos aplicativos.

### <a name="advanced-options"></a>Opções Avançadas

A forma como as reivindicações de grupo são emitidas pode ser modificada pelas configurações em opções Avançadas

Personalize o nome da reivindicação de grupo: Se selecionado, um tipo de reclamação diferente pode ser especificado para reclamações de grupo.   Digite o tipo de solicitação no campo Nome e no espaço de nome opcional para a reclamação no campo namespace.

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Alguns aplicativos exigem que as informações de membros do grupo apareçam na reivindicação 'função'. Você pode, opcionalmente, emitir os grupos do usuário como funções, verificando a caixa 'Emit groups a role claims'.

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Se a opção de emitir dados de grupo como funções for usada, apenas grupos aparecerão na reivindicação de funções.  Quaisquer funções de aplicativo atribuídas pelo usuário não aparecerão na reivindicação de função.

### <a name="edit-the-group-claims-configuration"></a>Editar a configuração de reivindicações de grupo

Uma vez que uma configuração de reclamação de grupo tenha sido adicionada à configuração Atributos de usuário & Claims, a opção de adicionar uma reclamação de grupo será acinzentada.  Para alterar a configuração de reivindicação de grupo, clique na reivindicação de grupo na lista **de reclamações adicionais.**

![afirma UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Configure o registro do aplicativo Azure AD para atributos de grupo

As reclamações em grupo também podem ser configuradas na seção [Reclamações Opcionais](../../active-directory/develop/active-directory-optional-claims.md) do Manifesto de [Aplicativo](../../active-directory/develop/reference-app-manifest.md).

1. No portal ->Azure Active Directory -> Application Registrations->Select Application->Manifest

2. Habilitar reivindicações de membros de grupo alterando o grupoMembershipClaim

Os valores válidos são:

| Seleção | Descrição |
|----------|-------------|
| **"Todos"** | Emite grupos de segurança, listas de distribuição e funções |
| **"SecurityGroup"** | Emite grupos de segurança dos estados que o usuário é membro da reivindicação dos grupos |
| **"DiretórioRole** | Se o usuário tiver as funções de diretório atribuídas, ela será emitida como uma reivindicação de 'wids' (os grupos afirmam que não serão emitidos) |
| **"ApplicationGroup** | Emite apenas os grupos que são explicitamente atribuídos ao aplicativo e o usuário é um membro do |

   Por exemplo: 

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Por padrão, os ObjectIDs do grupo serão emitidos no valor da reivindicação do grupo.  Para modificar o valor da solicitação para conter atributos do grupo de premissas ou alterar o tipo de solicitação para função, use a configuração OptionalClaims da seguinte forma:

3. Definir reivindicações opcionais de configuração de nome de grupo.

   Se você quiser que os grupos no token contenham os atributos do grupo AD no local, especifique a qual solicitação opcional de tipo de token deve ser aplicada na seção de reclamações opcionais.  Vários tipos de tokens podem ser listados:

   - idToken para o token ID OIDC
   - accessToken para o token de acesso OAuth/OIDC
   - Saml2Token para tokens SAML.

   > [!NOTE]
   > O tipo Saml2Token aplica-se aos tokens de formato SAML1.1 e SAML2.0

   Para cada tipo de token relevante, modifique os grupos que afirmam usar a seção OpcionalDesem devida no manifesto. O esquema OptionalClaims é o seguinte:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Esquema de Sinistros Opcionais | Valor |
   |----------|-------------|
   | **Nome:** | Devem ser "grupos" |
   | **Fonte:** | Não usado. Omita ou especifique nulo |
   | **Essencial:** | Não usado. Omita ou especifique falso |
   | **Additionalproperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Em propriedades adicionais são necessários apenas um de "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se mais de um estiver presente, o primeiro é usado e qualquer outro ignorado.

   Alguns aplicativos exigem informações de grupo sobre o usuário na reivindicação da função.  Para alterar o tipo de solicitação para de uma reivindicação de grupo para uma reivindicação de função, adicione "emit_as_roles" a propriedades adicionais.  Os valores do grupo serão emitidos na reivindicação da função.

   > [!NOTE]
   > Se "emit_as_roles" for usado, quaisquer funções de aplicativo configuradas que o usuário é atribuído não aparecerão na reivindicação de função

### <a name="examples"></a>Exemplos

Emita grupos como nomes de grupo em tokens de acesso OAuth no formato dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Para emitir nomes de grupo a serem retornados no formato netbiosDomain\samAccountName como a reivindicação de funções em Tokens SAML e OIDC ID:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Próximas etapas

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Configurar declarações de função](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
