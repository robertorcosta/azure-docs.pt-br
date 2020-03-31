---
title: Fornecer reivindicações opcionais para aplicativos AD do Azure | Azure
titleSuffix: Microsoft identity platform
description: Como adicionar reivindicações personalizadas ou adicionais aos tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136510"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: Fornecer reivindicações opcionais para o seu aplicativo Azure AD

Os desenvolvedores de aplicativos podem usar reivindicações opcionais em seus aplicativos Azure AD para especificar quais reivindicações eles querem em tokens enviados para seu aplicativo. 

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para o aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse as declarações personalizadas para o aplicativo.

Para obter as listas de reclamações padrão, consulte o [token de acesso](access-tokens.md) e [id_token](id-tokens.md) documentação de reclamações. 

Embora as reivindicações opcionais sejam suportadas em tokens de formato v1.0 e v2.0, bem como tokens SAML, eles fornecem a maior parte de seu valor ao passar de v1.0 para v2.0. Um dos objetivos do ponto final da plataforma de [identidade v2.0 microsoft](active-directory-appmodel-v2-overview.md) é tamanhos de tokens menores para garantir o desempenho ideal dos clientes. Como resultado, várias declarações anteriormente incluídas em tokens de acesso e ID não estão mais presentes nos tokens v2.0 e devem ser solicitadas especificamente, por aplicativo.

**Tabela 1: Aplicabilidade**

| Tipo de Conta | Tokens v1.0 | Tokens v2.0  |
|--------------|---------------|----------------|
| Conta pessoal da Microsoft  | N/D  | Com suporte |
| Conta do AD do Azure      | Com suporte | Com suporte |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 e v2.0 opcionais conjunto de sinistros

O conjunto de declarações opcionais disponíveis por padrão para uso pelos aplicativos é listado abaixo. Para adicionar declarações opcionais personalizadas para o aplicativo, confira [Extensões de Diretório](#configuring-directory-extension-optional-claims), abaixo. Ao adicionar reivindicações ao **token de acesso,** as reivindicações se aplicam aos tokens de acesso solicitados *para* o aplicativo (uma API web), não reivindicações solicitadas *pelo* aplicativo. Não importa como o cliente acessa sua API, os dados certos estão presentes no token de acesso que é usado para autenticar contra sua API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída em JWTs para tokens v1.0 e v2.0, mas não para tokens SAML, exceto quando indicado na coluna Tipo de Token. As contas dos consumidores suportam um subconjunto dessas reclamações, marcado na coluna "Tipo de usuário".  Muitas das reclamações listadas não se aplicam aos usuários `tenant_ctry` de consumo (eles não têm inquilino, portanto não tem valor).  

**Tabela 2: v1.0 e v2.0 opcionais**

| Nome                       |  Descrição   | Tipo de token | Tipo de Usuário | Observações  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez. Confira especificações de OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do locatário do recurso | JWT        |           | |
| `home_oid`                 | Para usuários convidados, a ID de objeto do usuário no locatário inicial do usuário.| JWT        |           | |
| `sid`                      | ID de sessão, usado para a saída de usuário por sessão. | JWT        |  Contas pessoais e Azure AD.   |         |
| `platf`                    | Plataforma de dispositivos    | JWT        |           | Restrito aos dispositivos gerenciados que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Originado de PrimaryAuthoritativeEmail do usuário      | JWT        |           |         |
| `verified_secondary_email` | Originado de SecondaryAuthoritativeEmail do usuário   | JWT        |           |        |
| `enfpolids`                | IDs de política aplicada. Uma lista de IDs de política que foram avaliadas para o usuário atual. | JWT |  |  |
| `vnet`                     | Informações de especificador de VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | O país do usuário | JWT |  | O Azure AD retorna a declaração opcional `ctry` se ela estiver presente e o valor da declaração é um código de país padrão de duas letras, como FR, JP, SZ e assim por diante. |
| `tenant_ctry`              | País do locatário de recursos | JWT | | |
| `xms_pdl`          | Local dos dados preferido   | JWT | | Para os inquilinos multi-Geo, a localização de dados preferida é o código de três letras mostrando a região geográfica em que o usuário está. Para obter mais informações, consulte a [documentação do Azure AD Connect sobre a localização de dados preferidos](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Por exemplo: `APC` para Pacífico Asiático. |
| `xms_pl`                   | Idioma preferido do usuário  | JWT ||O idioma preferido do usuário, se definido. Originado de seu locatário inicial, em cenários de acesso de convidado. Tem o formato II-PP ("en-us"). |
| `xms_tpl`                  | Idioma preferido do locatário| JWT | | O idioma preferido do locatário do recurso, se definido. Com o formato II (“en”). |
| `ztdid`                    | ID de implantação de zero toque | JWT | | A identidade do dispositivo usada para o [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O email endereçável para este usuário, se o usuário tiver um.  | JWT, SAML | MSA | Esse valor é incluído por padrão, se o usuário é um convidado no locatário.  Para usuários gerenciados (os usuários dentro do inquilino), ele deve ser solicitado através desta reclamação opcional ou, somente no v2.0, com o escopo OpenID.  Para usuários gerenciados, o endereço de email deve ser definido [portal de administração do Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Formatação opcional para reclamações em grupo |JWT, SAML| |Usado em conjunto com a configuração GroupMembershipClaims no manifesto do [aplicativo,](reference-app-manifest.md)que também deve ser definido. Para obter detalhes, consulte [as reivindicações do Grupo](#configuring-groups-optional-claims) abaixo. Para obter mais informações sobre reivindicações de grupo, consulte [Como configurar reivindicações de grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Status da conta de usuários no locatário. | JWT, SAML | | Se o usuário for um membro do locatário, o valor será `0`. Se eles forem convidado, o valor é `1`. |
| `upn`                      | Declaração UserPrincipalName. | JWT, SAML  |           | Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais a fim de modificar seu comportamento, no caso do usuário convidado.  |

## <a name="v20-specific-optional-claims-set"></a>v2.0 conjunto de sinistros opcionais específicos

Essas reivindicações estão sempre incluídas em tokens AD v1.0 Azure, mas não estão incluídas em tokens v2.0, a menos que solicitado. Essas reivindicações são aplicáveis apenas para JWTs (tokens de ID e Tokens de Acesso). 

**Tabela 3: reivindicações opcionais v2.0 somente**

| Declaração JWT     | Nome                            | Descrição                                | Observações |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP com o qual o cliente se conectou.   |       |
| `onprem_sid`  | Identificador de Segurança Local |                                             |       |
| `pwd_exp`     | Hora da Expiração da Senha        | A data e a hora em que a senha expira. |       |
| `pwd_url`     | Alterar URL da Senha             | Uma URL que o usuário pode acessar para alterar sua senha.   |   |
| `in_corp`     | Dentro da Rede Corporativa        | Indica se o cliente está se conectando da rede corporativa. Se não estiverem, a reivindicação não está incluída.   |  Baseado nas configurações de [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) na Autenticação Multifator.    |
| `nickname`    | Apelido                        | Um nome adicional para o usuário. O apelido é separado do primeiro ou sobrenome. Requer `profile` o escopo.| 
| `family_name` | Sobrenome                       | Fornece o sobrenome, sobrenome ou nome da família do usuário conforme definido no objeto do usuário. <br>"family_name":"Barros" | Suportado em MSA e Azure AD. Requer `profile` o escopo.   |
| `given_name`  | Nome                      | Fornece o primeiro ou "dado" nome do usuário, conforme definido no objeto do usuário.<br>"given_name": "Davi"                   | Suportado em MSA e Azure AD .  Requer `profile` o escopo. |
| `upn`         | Nome UPN | Um identificador para o usuário que pode ser usado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para dados de chave. | Ver [propriedades adicionais](#additional-properties-of-optional-claims) abaixo para a configuração da declaração. Requer `profile` o escopo.|

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar o modo como a declaração é retornada. Essas propriedades adicionais são usadas principalmente para ajudar a migração de aplicativos locais com expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não podem manipular marcas (`#`) no UPN)

**Tabela 4: Valores para configurar sinistros opcionais**

| Nome da propriedade  | Nome de Propriedade Adicional | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usada para respostas SAML e JWT e para tokens v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o UPN de convidado conforme armazenado no locatário do recurso. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | O mesmo que acima, exceto`#`que as marcas de`_`hash ( ) são substituídas por sublinhados ( ), por exemplo`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais

    ```json
        "optionalClaims": 
         {
             "idToken": [ 
            { 
                      "name": "upn", 
                      "essential": false,
                  "additionalProperties": [ "include_externally_authenticated_upn"]  
                    }
                 ]
        }
    ```

Este objeto OptionalClaims faz com que o token de ID seja devolvido ao cliente para incluir uma reclamação upn com as informações adicionais de inquilino doméstico e inquilino de recursos. A `upn` reclamação só é alterada no token se o usuário for um convidado no inquilino (que usa um IDP diferente para autenticação). 

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

> [!IMPORTANT]
> Os tokens de acesso são **sempre** gerados usando o manifesto do recurso, não do cliente.  Assim, na `...scope=https://graph.microsoft.com/user.read...` solicitação o recurso é a API do Microsoft Graph.  Assim, o token de acesso é criado usando o manifesto da API do Microsoft Graph, não o manifesto do cliente.  Alterar o manifesto para o seu aplicativo nunca fará com que os tokens para a API do Microsoft Graph pareçam diferentes.  Para validar que `accessToken` suas alterações estão em vigor, solicite um token para sua aplicação, não outro aplicativo.  


Você pode configurar reivindicações opcionais para sua aplicação através da interface do usuário ou manifesto de aplicativo.

1. Vá para o [portal Azure.](https://portal.azure.com) Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **Gerenciar,** selecione **registros do Aplicativo**.
1. Selecione o aplicativo para o aplicativo que deseja configurar reivindicações opcionais na lista.

**Configuração de reivindicações opcionais através da ui:**

[![Mostra como configurar reivindicações opcionais usando a ui](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Na seção **Gerenciar,** selecione **Configuração de Token (visualização)**.
2. Selecione **Adicionar reivindicação opcional**.
3. Selecione o tipo de token que deseja configurar.
4. Selecione as reivindicações opcionais a serem adicionais.
5. Clique em **Adicionar**.

**Configuração de reclamações opcionais através do manifesto do aplicativo:**

[![Mostra como configurar reivindicações opcionais usando o manifesto do aplicativo](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Na seção **Gerenciar,** **selecione 'Manifestar ''''''''''''''** Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto. Opcionalmente, você pode selecionar **Baixar** e editar o manifesto localmente e, em seguida, usar **Carregar** para reaplicá-lo ao seu aplicativo. Para obter mais informações sobre o manifesto do aplicativo, consulte o [artigo manifesto do aplicativo Azure AD](reference-app-manifest.md).

    A entrada do manifesto do aplicativo a seguir adiciona as reivindicações opcionais auth_time, ipaddr e upn aos tokens ID, access e SAML.

    ```json
        "optionalClaims":  
           {
              "idToken": [
                    {
                          "name": "auth_time", 
                          "essential": false
                     }
              ],
              "accessToken": [
                     {
                            "name": "ipaddr", 
                            "essential": false
                      }
              ],
              "saml2Token": [
                      {
                            "name": "upn", 
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
    ```

2. Ao terminar, clique em **Salvar**. Agora, as reivindicações opcionais especificadas serão incluídas nos tokens para sua aplicação.    


### <a name="optionalclaims-type"></a>Tipo OptionalClaims

Declara as declarações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar as declarações opcionais a serem retornadas em cada um dos três tipos de tokens (token de ID, token de acesso, token SAML 2) que pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto de declarações opcionais a serem retornadas em cada tipo de token diferente. A propriedade OptionalClaims da entidade do aplicativo é um objeto OptionalClaims.

**Tabela 5: Propriedades do tipo OptionalClaims**

| Nome        | Type                       | Descrição                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de ID JWT. |
| `accessToken` | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As declarações opcionais retornadas no token SAML.   |

### <a name="optionalclaim-type"></a>Tipo de Reclamação Opcional

Contém uma declaração opcional associada a um aplicativo ou uma entidade de segurança. As propriedades idToken, accessToken e saml2Token do tipo [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) são uma coleção de OptionalClaim.
Caso haja suporte por uma declaração específica, você também poderá modificar o comportamento de OptionalClaim usando o campo AdditionalProperties.

**Tabela 6: Propriedades do tipo OptionalClaim**

| Nome                 | Type                    | Descrição                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da declaração opcional.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A origem (objeto de diretório) da declaração. Há declarações predefinidas e definidas pelo usuário de propriedades de extensão. Se o valor de origem for nulo, a declaração será uma declaração opcional predefinida. Se o valor de origem for um usuário, o valor na propriedade name será a propriedade de extensão do objeto de usuário. |
| `essential`            | Edm.Boolean             | Se o valor for true, a declaração especificada pelo cliente será necessária para garantir uma experiência de autorização sem problemas para a tarefa específica solicitada pelo usuário final. O valor padrão é false.                                                                                                             |
| `additionalProperties` | Coleção (Edm.String) | Propriedades adicionais da declaração. Se uma propriedade existir na coleção, ela modificará o comportamento da declaração opcional especificado na propriedade name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configuração de reivindicações opcionais de extensão de diretório

Além do conjunto padrão de sinistros opcionais, você também pode configurar tokens para incluir extensões. Para obter mais informações, consulte [a documentação de propriedade do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) - observe que o esquema e as extensões abertas não são suportados por reclamações opcionais, apenas as extensões de diretório estilo AAD-Graph. Esse recurso é útil para anexar informações adicionais do usuário que o aplicativo pode usar; por exemplo, um identificador adicional ou uma opção de configuração importante que o usuário configurou. Veja a parte inferior desta página para um exemplo.

> [!NOTE]
> - As extensões do esquema do diretório são um recurso somente do Azure AD, portanto, se o manifesto do aplicativo solicitar uma extensão personalizada e um usuário do MSA fizer login em seu aplicativo, essas extensões não serão devolvidas.

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Ao configurar reclamações opcionais de extensão de diretório usando o manifesto `extension_<appid>_<attributename>`do aplicativo, use o nome completo da extensão (no formato: ). Deve `<appid>` coincidir com o ID do pedido solicitando a solicitação. 

No JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Em tokens SAML, essas declarações serão emitidas com o seguinte formato de URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Configuração de grupos de reivindicações opcionais

   > [!NOTE]
   > A capacidade de emitir nomes de grupos para usuários e grupos sincronizados a partir de locais é A Visualização Pública.

Esta seção abrange as opções de configuração em reivindicações opcionais para alterar os atributos de grupo usados em reclamações de grupo do objectID do grupo padrão para atributos sincronizados a partir do Windows Active Directory on-premises. Você pode configurar grupos de reivindicações opcionais para sua aplicação através da interface do usuário ou manifesto de aplicativo.

> [!IMPORTANT]
> Para obter mais detalhes, incluindo ressalvas importantes para a visualização pública de reivindicações de grupos de atributos locais, consulte [Configure reivindicações de grupo para aplicativos com a Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Configuração de grupos de reivindicações opcionais através da UI:**
1. Entre no [portal do Azure](https://portal.azure.com)
1. Depois de autenticar, escolha o inquilino azure AD selecionando-o no canto superior direito da página
1. Selecione **o Diretório Ativo do Azure** no menu à esquerda
1. Na seção **Gerenciar,** selecione **registros do Aplicativo**
1. Selecione o aplicativo para o aplicativo que deseja configurar reivindicações opcionais na lista
1. Na seção **Gerenciar,** selecione **Configuração de Token (visualização)**
2. Selecionar **adicionar reivindicação de grupos**
3. Selecione os tipos de grupo a retornar (**Todos os grupos,** **SecurityGroup**ou **DiretórioRole**). A opção **Todos os Grupos** inclui **SecurityGroup,** **DirectoryRole**e **DistributionList**
4. Opcional: clique nas propriedades específicas do tipo de token para modificar o valor de reivindicação de grupos para conter atributos do grupo de premissas ou para alterar o tipo de solicitação para uma função
5. Clique em **Salvar**

**Configuração de grupos de reivindicações opcionais através do manifesto do aplicativo:**
1. Entre no [portal do Azure](https://portal.azure.com)
1. Depois de autenticar, escolha o inquilino azure AD selecionando-o no canto superior direito da página
1. Selecione **o Diretório Ativo do Azure** no menu à esquerda
1. Selecione o aplicativo para o aplicativo que deseja configurar reivindicações opcionais na lista
1. Na seção **Gerenciar,** selecione **'Manifestar',**
3. Adicione a seguinte entrada usando o editor de manifesto:

   Os valores válidos são:

   - "Todos" (essa opção inclui SecurityGroup, DirectoryRole e DistributionList)
   - "SecurityGroup"
   - "DiretórioRole"

   Por exemplo: 

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Por padrão, os ObjectIDs do grupo serão emitidos no valor da reivindicação do grupo.  Para modificar o valor da solicitação para conter atributos do grupo de premissas ou alterar o tipo de solicitação para função, use a configuração OptionalClaims da seguinte forma:

3. Definir reivindicações opcionais de configuração de nome de grupo.

   Se você quiser agrupar no token para conter os atributos do grupo AD no local na seção de reclamações opcionais, especifique a qual tipo de token a reivindicação opcional deve ser aplicada, o nome da reclamação opcional solicitada e quaisquer propriedades adicionais desejadas.  Vários tipos de tokens podem ser listados:

   - idToken para o token ID OIDC
   - acessarToken para o token de acesso OAuth
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

   | Esquema de sinistros opcionais | Valor |
   |----------|-------------|
   | **Nome:** | Devem ser "grupos" |
   | **Fonte:** | Não usado. Omita ou especifique nulo |
   | **Essencial:** | Não usado. Omita ou especifique falso |
   | **Additionalproperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Em propriedades adicionais são necessários apenas um de "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Se mais de um estiver presente, o primeiro é usado e qualquer outro ignorado.

   Alguns aplicativos exigem informações de grupo sobre o usuário na reivindicação da função.  Para alterar o tipo de solicitação para de uma reivindicação de grupo para uma reivindicação de função, adicione "emit_as_roles" a propriedades adicionais.  Os valores do grupo serão emitidos na reivindicação da função.

   > [!NOTE]
   > Se "emit_as_roles" for usado, quaisquer funções de aplicativo configuradas que o usuário é atribuído não aparecerão na reivindicação de função

**Exemplos:**

1) Emita grupos como nomes de grupo em tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

    
    **Configuração de UI:**

    [![Mostra como configurar reivindicações opcionais usando a ui](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Entrada do manifesto do aplicativo:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Emita nomes de grupos a serem devolvidos no formato netbiosDomain\sAMAccountName como a reivindicação de funções em Tokens SAML e OIDC ID

    **Configuração de UI:**

    [![Mostra como configurar reivindicações opcionais usando a ui](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Entrada do manifesto do aplicativo:**
    
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
    ``` 
     

## <a name="optional-claims-example"></a>Exemplo de declarações opcional

Nesta seção, você pode examinar um cenário para ver como usar o recurso opcional de declarações para o aplicativo.
Há várias opções disponíveis para atualizar as propriedades na configuração de identidade do aplicativo para habilitar e configurar declarações opcionais:
-    Você pode usar a ia de **configuração de token (visualização)** (veja exemplo abaixo)
-    Você pode usar o **Manifesto** (veja exemplo abaixo). Primeiro leia o [documento Noções básicas sobre o manifesto do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) para obter uma introdução ao manifesto.
-   Também é possível escrever um aplicativo que usa a [API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) do Microsoft Graph para atualizar seu aplicativo. O [tipo OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) no guia de referência da API do Microsoft Graph pode ajudá-lo a configurar as reclamações opcionais.

**Exemplo:** No exemplo abaixo, você usará a interface do **usuário de configuração de Token (preview)** e **manifest** para adicionar reivindicações opcionais aos tokens de acesso, ID e SAML destinados ao seu aplicativo. Diferentes reivindicações opcionais serão adicionadas a cada tipo de token que o aplicativo pode receber:
-    Os tokens de ID agora contêm o UPN para usuários federados no formato completo (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    Os tokens de acesso que outros clientes solicitam para este aplicativo agora incluirão a reivindicação auth_time
-    Agora os tokens SAML contêm a extensão do esquema de diretório skypeId (neste exemplo, a ID de aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237). Os tokens SAML vão expor a ID do Skype como `extension_skypeId`.

**Configuração de UI:**

1. Entre no [portal do Azure](https://portal.azure.com)

1. Depois de autenticar, escolha o inquilino azure AD selecionando-o no canto superior direito da página.

1. Selecione **Diretório ativo do Azure** no menu à esquerda.

1. Na seção **Gerenciar,** selecione **registros do Aplicativo**.

1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.

1. Na seção **Gerenciar,** clique em **Configuração de Token (visualização)**.

1. Selecione **Adicionar reivindicação opcional,** selecione o tipo de token **DeD,** selecione **upn** na lista de reclamações e clique em **Adicionar**.

1. Selecione **Adicionar reivindicação opcional,** selecione o tipo de token **De acesso,** selecione **auth_time** na lista de reclamações e clique em **Adicionar**.

1. Na tela de visão geral da configuração do token, clique no ícone do lápis ao lado de **upn,** clique no alternador **autenticado externamente** e clique em **Salvar**.

1. Selecione **Adicionar reivindicação opcional,** selecione o tipo de token **SAML,** selecione **extn.skypeID** na lista de reclamações (apenas aplicável se você criou um objeto de usuário Azure AD chamado skypeID) e clique em **Adicionar**.

    [![Mostra como configurar reivindicações opcionais usando a ui](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuração do manifesto:**
1. Faça login no [portal Azure](https://portal.azure.com).
1. Depois de autenticar, escolha o inquilino azure AD selecionando-o no canto superior direito da página.
1. Selecione **Diretório ativo do Azure** no menu à esquerda.
1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.
1. Na seção **Gerenciar,** clique em **Manifesto** para abrir o editor de manifesto inline.
1. Você pode editar diretamente o manifesto usando esse editor. O manifesto segue o esquema para a [entidade Aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e formata automaticamente o manifesto uma vez salvo. Novos elementos serão adicionados para o `OptionalClaims` propriedade.

    ```json
            "optionalClaims": {
                "idToken": [ 
                     { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                     }
                     ],
                "accessToken": [ 
                      {
                        "name": "auth_time", 
                        "essential": false
                      }
                     ],
            "saml2Token": [ 
                  { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
                  }
                 ]
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
