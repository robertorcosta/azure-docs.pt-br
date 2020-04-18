---
title: Personalização de mapeamentos de atributos Azure AD | Microsoft Docs
description: Saiba quais são os mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure e como você pode modificá-los para atender às necessidades de negócios.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639752"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalização de mapeamentos de atributos de provisionamento de usuários para aplicativos SaaS no Azure Active Directory

O Microsoft Azure AD oferece suporte para provisionamento de usuários para aplicativos SaaS de terceiros, como Salesforce, G Suite e outros. Se você habilitar o provisionamento de usuários para um aplicativo SaaS de terceiros, o portal Azure controlará seus valores de atributos através de mapeamentos de atributos.

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de usuário Do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos junto com os Usuários, como Grupos.

Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Assim, você pode alterar ou excluir mapeamentos de atributos existentes ou criar novos mapeamentos de atributos.

## <a name="editing-user-attribute-mappings"></a>Editar mapeamentos de atributos de usuário

Siga estas etapas para acessar o recurso **Mapeamentos** do provisionamento do usuário:

1. Faça login no portal do Diretório Ativo do [Azure](https://aad.portal.azure.com).
1. Selecione **aplicações Enterprise** no painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo aplicativos que foram adicionados da galeria.
1. Selecione qualquer aplicativo para carregar seu painel de gerenciamento de aplicativos, onde você pode visualizar relatórios e gerenciar as configurações do aplicativo.
1. Selecione **Provisionamento** para gerenciar as configurações de provisionamento de contas de usuário para o aplicativo selecionado.
1. Expandir **mapeamentos** para visualizar e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino. Se o aplicativo de destino o suportar, esta seção permite que você configure opcionalmente o provisionamento de grupos e contas de usuário.

   ![Use mapeamentos para visualizar e editar atributos do usuário](./media/customize-application-attributes/21.png)

1. Selecione uma configuração **mapeamentos** para abrir a tela **de mapeamento de atributos** relacionada. Alguns mapeamentos de atributos são necessários por um aplicativo SaaS para funcionar corretamente. Para os atributos necessários, o recurso **Excluir** não está disponível.

   ![Use o Mapeamento de Atributos para configurar mapeamentos de atributos para aplicativos](./media/customize-application-attributes/22.png)

   Nesta captura de tela, você pode ver que o atributo **Username** de um objeto gerenciado no Salesforce é preenchido com o valor **userPrincipalName** do objeto de diretório ativo do Azure vinculado.

1. Selecione um **mapeamento de atributos** existente para abrir a tela **Editar atributo.** Aqui você pode editar os atributos do usuário que fluem entre o Azure AD e o aplicativo de destino.

   ![Use Editar atributo para editar atributos do usuário](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo

Com mapeamentos de atributo, você controla como os atributos são preenchidos em um aplicativo SaaS de terceiro.
Há quatro tipos diferentes de mapeamento com suporte:

- **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto vinculado no AD do Azure.
- **Constante** – o atributo de destino é preenchido com uma seqüência específica especificada.
- **Expressão** - o atributo de destino é preenchido com base no resultado de uma expressão como script.
  Para saber mais, consulte [Escrever expressões para mapeamentos de atributo no Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Nenhum** - o atributo de destino é deixado sem modificação. No entanto, se o atributo de destino estiver sempre vazio, ele será preenchido com o valor Padrão que você especificar.

Juntamente com esses quatro tipos básicos, mapeamentos personalizados de atributos suportam o conceito de uma atribuição de valor **padrão** opcional. A atribuição de valor padrão garante que um atributo de destino seja preenchido com um valor se não houver um valor no Azure AD ou no objeto-alvo. A configuração mais comum é deixar isso em branco.

### <a name="understanding-attribute-mapping-properties"></a>Noções básicas de propriedades de mapeamento de atributo

Na seção anterior, você já foi introduzido na propriedade do tipo mapeamento de atributos.
Junto com essa propriedade, os mapeamentos de atributos também suportam os seguintes atributos:

- **Atributo de origem** - O atributo de usuário do sistema de origem (exemplo: Azure Active Directory).
- **Atributo de destino** – O atributo do usuário no sistema de destino (exemplo: ServiceNow).
- **Valor padrão se nulo (opcional)** - O valor que será passado para o sistema de destino se o atributo de origem for nulo. Esse valor só será provisionado quando um usuário for criado. O "valor padrão quando nulo" não será provisionado ao atualizar um usuário existente. Se, por exemplo, você quiser provisionar todos os usuários existentes no sistema de destino com um título de trabalho específico (quando ele é nulo no sistema de origem), você pode usar a seguinte [expressão](../app-provisioning/functions-for-customizing-application-data.md): Switch (IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Certifique-se de substituir o "Valor padrão" pelo que você gostaria de fornecer quando estiver nulo no sistema de origem. 
- **Combine objetos usando este atributo** – Se esse mapeamento deve ser usado para identificar exclusivamente os usuários entre os sistemas de origem e destino. É normalmente definido no usuárioPrincipalName ou atributo de e-mail no Azure AD, que normalmente é mapeado para um campo de nome de usuário em um aplicativo de destino.
- **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando há múltiplos, eles são avaliados na ordem definida por este campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.
- **Aplicar esse mapeamento**
  - **Sempre** – Aplique este mapeamento em ações de criação e atualização de usuários.
  - **Somente durante a criação** - Aplique este mapeamento somente em ações de criação de usuários.

## <a name="matching-users-in-the-source-and-target--systems"></a>Usuários correspondentes nos sistemas de origem e destino
O serviço de provisionamento Azure AD pode ser implantado em ambos os cenários "greenfield" (onde os usuários não saem no sistema de destino) e cenários de "brownfield" (onde os usuários já existem no sistema de destino). Para apoiar ambos os cenários, o serviço de provisionamento utiliza o conceito de atributos correspondentes. Os atributos correspondentes permitem determinar como identificar exclusivamente um usuário na origem e corresponder ao usuário no destino. Como parte do planejamento de sua implantação, identifique o atributo que pode ser usado para identificar exclusivamente um usuário nos sistemas de origem e destino. Coisas a notar:

- **Os atributos correspondentes devem ser únicos:** Os clientes geralmente usam atributos como userPrincipalName, mail ou object ID como atributo correspondente.
- **Vários atributos podem ser usados como atributos correspondentes:** Você pode definir vários atributos a serem avaliados ao corresponder os usuários e a ordem em que eles são avaliados (definido como precedência correspondente na ui). Se, por exemplo, você definir três atributos como atributos correspondentes e um usuário for único após avaliar os dois primeiros atributos, o serviço não avaliará o terceiro atributo. O serviço avaliará os atributos correspondentes na ordem especificada e deixará de avaliar quando uma correspondência for encontrada.  
- **O valor na origem e no alvo não precisa corresponder exatamente:** O valor no alvo pode ser alguma função simples do valor na fonte. Assim, pode-se ter um atributo emailAddress na origem e o usuárioPrincipalName no destino, e corresponder por uma função do atributo emailAddress que substitui alguns caracteres por algum valor constante.  
- **A correspondência com base em uma combinação de atributos não é suportada:** A maioria dos aplicativos não suporta consultas com base em duas propriedades. Portanto, não é possível combinar com base em uma combinação de atributos. É possível avaliar propriedades individuais após a outra.
- **Todos os usuários devem ter um valor para pelo menos um atributo correspondente:** Se você definir um atributo correspondente, todos os usuários devem ter um valor para esse atributo no sistema de origem. Se, por exemplo, você definir userPrincipalName como o atributo correspondente, todos os usuários devem ter um userPrincipalName. Se você definir vários atributos de correspondência (por exemplo, extensãoAttribute1 e e-mail), nem todos os usuários devem ter o mesmo atributo correspondente. Um usuário poderia ter uma extensãoAttribute1, mas não e-mail, enquanto outro usuário poderia ter e-mail, mas nenhuma extensãoAttribute1. 
- **O aplicativo de destino deve suportar a filtragem no atributo correspondente:** Os desenvolvedores de aplicativos permitem filtrar um subconjunto de atributos em sua API de usuário ou grupo. Para aplicativos na galeria, garantimos que o mapeamento de atributo padrão seja para um atributo que a API do aplicativo de destino suporta filtragem. Ao alterar o atributo de correspondência padrão para o aplicativo de destino, verifique a documentação da API de terceiros para garantir que o atributo possa ser filtrado.  

## <a name="editing-group-attribute-mappings"></a>Editar mapeamentos de atributos de grupo

Um número selecionado de aplicativos, como ServiceNow, Box e G Suite, suporta a capacidade de provisionar objetos de grupo e objetos do Usuário. Os objetos de grupo podem conter propriedades de grupo, como nomes de exibição e aliases de e-mail, juntamente com membros do grupo.

![Exemplo mostra ServiceNow com objetos de grupo e usuário provisionados](./media/customize-application-attributes/24.png)

O provisionamento em grupo pode ser opcionalmente habilitado ou desativado selecionando o mapeamento de grupo em **Mapeamentos**e configuração **Habilitado** para a opção desejada na tela Mapeamento de **atributos.**

Os atributos provisionados como parte dos objetos de Grupo podem ser personalizados da mesma maneira como os objetos de Usuário, como descrito anteriormente. 

> [!TIP]
> O provisionamento dos objetos de grupo (propriedades e membros) é um conceito distinto de [atribuir grupos](../manage-apps/assign-user-or-group-access-portal.md) para um aplicativo. É possível atribuir um grupo a um aplicativo, mas somente provisionar os objetos de usuário contidos no grupo. O provisionamento de objetos de grupo completos não é necessário para usar grupos em atribuições.

## <a name="editing-the-list-of-supported-attributes"></a>Editando a lista de atributos com suporte

Os atributos de usuário que têm suporte para um determinado aplicativo são pré-configurados. A maioria das APIs de gerenciamento de usuários do aplicativo não suporta a detecção de esquemas. Assim, o serviço de provisionamento Ad do Azure não é capaz de gerar dinamicamente a lista de atributos suportados fazendo chamadas para o aplicativo.

No entanto, alguns aplicativos suportam atributos personalizados, e o serviço de provisionamento Azure AD pode ler e gravar em atributos personalizados. Para inserir suas definições no portal Azure, selecione a caixa de seleção **De mostrar opções avançadas** na parte inferior da tela mapeamento de **atributos** e, em seguida, selecione **Editar lista de atributos para** o seu aplicativo.

Os aplicativos e sistemas que dão suporte à personalização da lista de atributos incluem:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (As extensões de referência e diretório personalizado do[Microsoft Graph REST API v1.0](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) são suportadas)
- Aplicativos que dão suporte ao [SCIM 2.0](https://tools.ietf.org/html/rfc7643), onde os atributos definidos no [esquema principal](https://tools.ietf.org/html/rfc7643) precisam ser adicionados

> [!NOTE]
> Editar a lista de atributos com suporte é recomendado apenas para os administradores que personalizaram o esquema de seus aplicativos e sistemas e que tenham conhecimento de antemão de como seus atributos personalizados foram definidos. Às vezes, isso requer familiaridade com as APIs e as ferramentas dos desenvolvedores fornecidas por um aplicativo ou sistema.

Ao editar a lista de atributos com suporte, as seguintes propriedades são fornecidas:

- **Nome** - O nome do sistema do atributo, conforme definido no esquema do objeto de destino.
- **Tipo** - O tipo de dados que o atributo armazena, conforme definido no esquema do objeto-alvo, que pode ser um dos seguintes tipos:
  - *Binário* - O atributo contém dados binários.
  - *Booliano* - O atributo contém um valor Verdadeiro ou Falso.
  - *DateTime* - O atributo contém uma cadeia de caracteres de data.
  - *Número inteiro* - O atributo contém um número inteiro.
  - *Referência* - O atributo contém uma ID que faz referência a um valor armazenado em outra tabela no aplicativo de destino.
  - *Cadeia de caracteres* - O atributo contém uma cadeia de caracteres de texto.
- **Chave primária?** - Se o atributo é definido como um campo-chave primário no esquema do objeto-alvo.
- **Necessário?** - Se o atributo é necessário para ser preenchido no aplicativo de destino ou no sistema.
- **Multi-valor?** - Se o atributo suporta múltiplos valores.
- **Diferenciar maiúsculas e minúsculas?** - Se os valores dos atributos são avaliados de forma sensível ao caso.
- **Expressão de API** - Não use, a menos que seja instruído a fazê-lo pela documentação de um conector de provisionamento específico (como workday).
- **Atributo objeto referenciado** - Se for um atributo tipo referência, este menu permite que você selecione a tabela e o atributo no aplicativo de destino que contém o valor associado ao atributo. Por exemplo, se você tiver um atributo chamado "Departamento" cujo valor armazenado faz referência a um objeto em uma tabela separada de "Departamentos", selecione "Departments.Name". As tabelas de referência e os campos de ID primários suportados para um determinado aplicativo são pré-configurados e atualmente não podem ser editados usando o portal Azure, mas podem ser editados usando a [API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisionamento de um atributo de extensão personalizado para um aplicativo compatível com SCIM
O SCIM RFC define um esquema de usuário e grupo principal, ao mesmo tempo em que permite extensões ao esquema para atender às necessidades do seu aplicativo. Para adicionar um atributo personalizado a um aplicativo SCIM:
   1. Faça login no [portal do Azure Active Directory,](https://aad.portal.azure.com)selecione **Aplicativos Corporativos,** selecione seu aplicativo e, em seguida, **selecione Provisionamento**.
   2. Em **Mapeamentos,** selecione o objeto (usuário ou grupo) para o qual você gostaria de adicionar um atributo personalizado.
   3. Na parte inferior da página, selecione **Mostrar opções avançadas**.
   4. Selecione **Editar lista de atributos para AppName**.
   5. Na parte inferior da lista de atributos, digite informações sobre o atributo personalizado nos campos fornecidos. Em seguida, **selecione Adicionar atributo**.

Para aplicações SCIM, o nome do atributo deve seguir o padrão mostrado no exemplo abaixo. O "CustomExtensionName" e "CustomAttribute" podem ser personalizados de acordo com os requisitos do aplicativo, por exemplo:  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extension:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:value

Essas instruções só são aplicáveis a aplicativos habilitados para SCIM. Aplicativos como ServiceNow e Salesforce não são integrados com o Azure AD usando SCIM e, portanto, não exigem esse namespace específico ao adicionar um atributo personalizado.

Atributos personalizados não podem ser atributos referenciais ou atributos de vários valores. Atualmente, os atributos personalizados de extensão de vários valores são suportados apenas para aplicativos na galeria.  
 
**Exemplo de representação de um usuário com um atributo de extensão:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Provisionamento de uma função para um aplicativo SCIM
Use as etapas abaixo para provisionar funções para um usuário para o seu aplicativo. Observe que a descrição abaixo é específica para aplicativos SCIM personalizados. Para aplicativos de galeria, como Salesforce e ServiceNow, use os mapeamentos de funções pré-definidos. As balas abaixo descrevem como transformar o atributo AppRoleAssignments para o formato que seu aplicativo espera.

- Mapear um appRoleAssignment no Azure AD para uma função em seu aplicativo requer que você transforme o atributo usando uma [expressão](../app-provisioning/functions-for-customizing-application-data.md). O atributo appRoleAssignment **não deve ser mapeado diretamente** para um atributo de função sem usar uma expressão para analisar os detalhes da função. 

- **SingleAppRoleAssignment** 
  - **Quando usar:** Use a expressão SingleAppRoleAssignment para provisionar uma única função para um usuário e para especificar a função principal. 
  - **Como configurar:** Use as etapas descritas acima para navegar até a página de mapeamentos de atributos e use a expressão SingleAppRoleAssignment para mapear o atributo funções. Existem três atributos de função para escolher: (funções[eq primária "True"].display, roles[primary eq "True].type e roles[primary eq "True"].value). Você pode optar por incluir qualquer ou todos os atributos da função em seus mapeamentos. Se você quiser incluir mais de um, basta adicionar um novo mapeamento e incluí-lo como o atributo de destino.  
  
  ![Adicionar singleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Itens a serem considerados**
    - Certifique-se de que várias funções não são atribuídas a um usuário. Não podemos garantir qual papel será provisionado.
    
  - **Saída de exemplo** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **Quando usar:** Use a expressão AppRoleAssignmentsComplex para provisionar várias funções para um usuário. 
  - **Como configurar:** Editar a lista de atributos suportados conforme descrito acima para incluir um novo atributo para funções: 
  
    ![Adicionar funções](./media/customize-application-attributes/add-roles.png)<br>

    Em seguida, use a expressão AppRoleAssignmentsComplex para mapear o atributo de função personalizada, conforme mostrado na imagem abaixo:

    ![Adicionar AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Itens a serem considerados**
    - Todos os papéis serão provisionados como primário = falso.
    - O POST contém o tipo de função. A solicitação PATCH não contém tipo. Estamos trabalhando no envio do tipo em solicitações POST e PATCH.
    
  - **Saída de exemplo** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Provisionamento de um atributo multi-valor
Certos atributos, como números de telefone e e-mails, são atributos de vários valores onde você pode precisar especificar diferentes tipos de números de telefone ou e-mails. Use a expressão abaixo para atributos de vários valores. Ele permite especificar o tipo de atributo e mapear isso para o atributo correspondente do usuário Azure AD para o valor. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restaurando os atributos padrão e mapeamentos de atributos

Se você precisar recomeçar e redefinir seus mapeamentos existentes de volta ao seu estado padrão, você pode selecionar a caixa de seleção **de mapeamentos padrão de Restaurar** e salvar a configuração. Isso define todos os mapeamentos e filtros de escopo como se o aplicativo fosse adicionado ao seu inquilino Azure AD da galeria de aplicativos.

A seleção dessa opção forçará efetivamente uma ressincronização de todos os usuários enquanto o serviço de provisionamento estiver em execução.

> [!IMPORTANT]
> Recomendamos fortemente que **o status de provisionamento** seja definido para **Off** antes de invocar esta opção.

## <a name="what-you-should-know"></a>O que você deve saber

- O Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, apenas os objetos que precisam de atualização são processados durante um ciclo de sincronização.
- A atualização de mapeamentos de atributo tem impacto no desempenho de um ciclo de sincronização. Uma atualização a uma configuração de mapeamento de atributo exige que todos os objetos gerenciados sejam reavaliados.
- Uma prática recomendada é manter o número de alterações consecutivas em seus mapeamentos de atributos no mínimo.
- Adicionar um atributo de foto a ser provisionado a um aplicativo não é suportado hoje, pois você não pode especificar o formato para sincronizar a foto. Você pode solicitar o recurso no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- O atributo IsSoftDeleted é frequentemente parte dos mapeamentos padrão de um aplicativo. IsSoftdeleted pode ser verdadeiro em um dos quatro cenários (o usuário está fora de escopo devido a ser não atribuído do aplicativo, o usuário está fora de escopo devido a não atender a um filtro de escopo, o usuário foi excluído suavemente no Azure AD, ou a propriedade AccountEnabled é definida como falsa no usuário). 
- O serviço de provisionamento Azure AD não suporta provisionamento de valores nulos

## <a name="next-steps"></a>Próximas etapas

- [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
- [Escrevendo expressões para mapeamentos de atributo](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtros de escopo para provisionamento de usuários](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](use-scim-to-provision-users-and-groups.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
