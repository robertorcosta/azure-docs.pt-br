---
title: Adicionar atributos personalizados aos fluxos dos usuários do Azure AD
description: Saiba mais sobre como personalizar os atributos para seus fluxos dos usuários de inscrição por autoatendimento.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594705"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definir atributos personalizados para fluxos dos usuários (versão prévia)
|     |
| --- |
| O recurso de atributos de usuário personalizados é a versão prévia pública de um recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Para cada aplicativo, você pode ter requisitos diferentes para as informações que deseja coletar durante a inscrição. O Azure AD é fornecido com um conjunto interno de informações armazenadas em atributos, como Nome, Sobrenome, Cidade e CEP. Com o Azure AD, você pode estender o conjunto de atributos armazenados em uma conta de convidado quando o usuário externo se inscreve por meio de um fluxo do usuário.

Você pode criar atributos personalizados no portal do Azure e usá-los nos seus fluxos dos usuários de inscrição por autoatendimento. Você também pode ler e gravar esses atributos usando a [API do Microsoft Graph](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). A API do Microsoft Graph dá suporte à criação e à atualização de um usuário com atributos de extensão. Atributos de extensão na API do Graph são nomeados usando a convenção `extension_<Application-client-id>_attributename`. Por exemplo:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Encontre a `<Application-client-id>` na página **Registros de aplicativo** ao lado da **ID do Aplicativo (cliente)** . Essa ID é específica do seu locatário.

## <a name="create-a-custom-attribute"></a>Como criar um atributo personalizado

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **Atributos de usuário personalizados (Versão Prévia)** . Os atributos de usuário disponíveis serão listados.

   ![Selecionar atributos de usuário para inscrição](media/user-flow-add-custom-attributes/user-attributes.png)

5. Para adicionar um atributo, selecione **Adicionar**.
6. No painel **Adicionar um atributo**, insira os seguintes valores:

   - **Nome**: forneça um nome para o atributo personalizado (por exemplo, "Tamanho do calçado").
   - **Tipo de Dados**: escolha um tipo de dados (**cadeia de caracteres**, **booliano** ou **Int**).
   - **Descrição**: opcionalmente, insira uma descrição do atributo personalizado para uso interno. Essa descrição não fica visível para o usuário.

   ![Adicionar um atributo](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Selecione **Criar**.

O atributo personalizado agora está disponível na lista de atributos do usuário e para uso nos fluxos dos usuários. Um atributo personalizado só é criado na primeira vez que é usado em qualquer fluxo do usuário, não quando você o adiciona à lista de atributos de usuário.

Depois que você cria um usuário com um fluxo do usuário que usa o atributo personalizado recém-criado, o objeto pode ser consultado no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Agora você deve ver **ShoeSize** na lista de atributos coletados durante a jornada de inscrição, e vê-lo no token enviado de volta ao seu aplicativo. Para obter informações sobre como incluir essas declarações no token enviado novamente para o aplicativo, confira [Como configurar declarações opcionais de extensão de diretório](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims)

## <a name="next-steps"></a>Próximas etapas

[Adicionar um fluxo do usuário de inscrição por autoatendimento a um aplicativo](self-service-sign-up-user-flow.md)