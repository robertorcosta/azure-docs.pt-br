---
title: Problema ao configurar o logon único federado para um aplicativo inexistente na galeria| Microsoft Docs
description: Aborda alguns dos problemas comuns que você pode encontrar ao configurar o logon único federado para seu aplicativo SAML personalizado que não está listado na Galeria do Aplicativo Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76711883"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problema ao configurar o logon único federado para um aplicativo inexistente na galeria

Se você encontrar um problema ao configurar um aplicativo. Verifique se seguiu todas as etapas do artigo [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Azure Active Directory.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância do aplicativo

Para adicionar uma segunda instância de um aplicativo você precisará:

-   Configurar um identificador exclusivo para a segunda instância. Não é possível configurar o mesmo identificador usado para a primeira instância.

-   Configurar um certificado diferente daquele usado para a primeira instância.

Se o aplicativo não tiver suporte para nenhum dos anteriores, não será possível configurar uma segunda instância.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde é possível configurar o formato EntityID (Identificador de Usuário)

Não é possível selecionar o formato EntityID (Identificador de Usuário) que o Azure Active Directory envia para o aplicativo na resposta, após a autenticação do usuário.

O Azure Active Directory seleciona o formato para o atributo NameID (Identificador de Usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, visite o artigo [protocolo SAML de logon único](../develop/single-sign-on-saml-protocol.md#authnrequest) na seção NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Onde obter os metadados do aplicativo ou o certificado do Azure AD

Para baixar o certificado ou metadados do aplicativo Azure Active Directory, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **Certificado de Autenticação SAML** e, em seguida, clique no valor da coluna **Download**. Dependendo do aplicativo que exigir a configuração de logon único, você verá a opção para baixar o Certificado ou o XML de Metadados.

O Azure AD não fornece uma URL para obter os metadados. Os metadados apenas podem ser recuperados como um arquivo XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Não sei como personalizar declarações SAML enviadas para um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas para seu aplicativo, consulte [Mapeamento de declarações no Azure Active Directory](../develop/active-directory-claims-mapping.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
[Gerenciamento de aplicativos com o Active Directory do Azure](what-is-application-management.md)
