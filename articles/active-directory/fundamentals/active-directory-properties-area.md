---
title: Adicionar informações de privacidade de sua organização – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar informações de privacidade de sua organização à área de Propriedades do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876177"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Adicionar informações de privacidade de sua organização usando o Azure Active Directory
Este artigo explica como um administrador de inquilinos pode adicionar informações relacionadas à privacidade ao inquilino do Azure Active Directory (Azure AD) de uma organização, através do portal Azure.

Recomendamos fortemente que você adicione seu contato global de privacidade e a declaração de privacidade da sua organização, para que seus funcionários internos e convidados externos possam rever suas políticas. Como as declarações de privacidade são exclusivamente criadas e personalizadas para cada empresa, é altamente recomendável que você entre em contato com um advogado para obter assistência.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Adicionar suas informações de privacidade no Azure AD
Você adiciona informações de privacidade da sua organização na área **Propriedades** do Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Para acessar a área de Propriedades e adicionar suas informações de privacidade

1.    Entre no portal do Azure como um administrador de locatário.

2.    Na barra de navegação à esquerda, selecione **Azure Active Directory**e, em seguida, selecione **Propriedades**.

    A área **Propriedades** é exibida.

    ![A área de Propriedades do Azure AD realçando a área de informações de privacidade](media/active-directory-properties-area/properties-area.png)

3.    Adicione as informações de privacidade de seus funcionários:

    - **Contato técnico.** Digite o endereço de email da pessoa de contato para suporte técnico dentro de sua organização.
    
    - **Contato de privacidade global.** Digite o endereço de email da pessoa de contato para consultas sobre a privacidade dos dados pessoais. Essa pessoa é também quem a Microsoft contatará se houver uma violação de segurança. Se não houver nenhuma pessoa listada aqui, a Microsoft entrará em contato com seus administradores globais.

    - **URL da política de privacidade.** Digite o link para o documento da sua organização que descreve como sua organização lida com a privacidade de dados de hóspedes internos e externos.

        >[!Important]
        >Se você não incluir sua própria declaração de privacidade ou seu contato de privacidade, seus hóspedes externos verão texto na caixa **Depermissões de Revisão** que diz, ** < _seu nome org_> não forneceu links para seus termos para você revisar**. Por exemplo, um usuário convidado verá essa mensagem quando receber um convite para acessar uma organização por meio de colaboração B2B.

        ![Caixa Permissões de Revisão de Colaboração B2B com mensagem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
- [Resgate do convite de colaboração do Azure Active Directory B2B](../b2b/redemption-experience.md)
- [Adicionar ou alterar as informações de perfil de um usuário no Azure Active Directory](active-directory-users-profile-azure-portal.md)
