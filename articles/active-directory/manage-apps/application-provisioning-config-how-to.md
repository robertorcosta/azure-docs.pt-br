---
title: Como configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD
description: Como você pode rapidamente configurar provisionamento e desprovisionamento de conta de usuário avançado para aplicativos já listados na Galeria de Aplicativos do Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 8aa8265733128ed3b2ad5ddf16dcebb122b0f054
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443432"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Como configurar provisionamento do usuário para um aplicativo de galeria do Azure AD

*Provisionamento de contas de usuário* é o ato de criar, atualizar e/ou desabilitar os registros de conta de usuário no armazenamento de perfil do usuário local do aplicativo. A maioria dos aplicativos de nuvem e SaaS armazenam a função de usuários e as permissões no próprio armazenamento de perfil de usuário local do usuário e a presença desse registro de usuário no armazenamento local do usuário é *necessária* para o logon único e o acesso ao trabalho.

No portal do Azure, a guia de **provisionamento** no painel de navegação à esquerda de um aplicativo empresarial exibe quais modos de provisionamento tem suporte para esse aplicativo. Existem dois valores válidos:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configurar um aplicativo para o provisionamento manual

Provisionamento *manual* significa que as contas de usuário devem ser criadas manualmente usando métodos fornecidos pelo aplicativo. Isso pode significar fazendo logon em um portal administrativo para que o aplicativo e adicionar usuários usando uma interface do usuário baseada na web. Ou ele pode carregar uma planilha com detalhes da conta de usuário, usando um mecanismo fornecido pelo aplicativo. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar quais mecanismos estão disponíveis.

Se *manual* for o único modo mostrado para um determinado aplicativo, isso significa que ainda não há nenhum conector de provisionamento do Azure ad automático para o aplicativo. Ou isso significa que o aplicativo não oferece suporte aos pré-requisitos para a API de gerenciamento de usuários da Microsoft, que é usada para criar um conector de provisionamento automatizado.

Se você quiser solicitar suporte para provisionamento automático de um determinado aplicativo, você pode preencher uma solicitação usando as [Solicitações de Aplicativos do Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configurar um aplicativo para o provisionamento automático

*Automático* significa que o conector de provisionamento do Azure AD foi desenvolvido para este aplicativo. Para saber mais sobre o serviço de provisionamento do Azure AD e como ele funciona, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Para obter mais informações sobre como provisionar usuários e grupos a um aplicativo específico, consulte [Gerenciamento de provisionamento de conta de usuário para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

As etapas reais necessárias para habilitar e configurar o provisionamento automático variam de acordo com o aplicativo.

> [!NOTE]
> Você deve começar encontrando o tutorial de instalação específicas de configuração de provisionamento para o seu aplicativo e seguir as etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento. 

Tutoriais de aplicativos podem ser encontrados em [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Uma coisa importante a considerar ao configurar o provisionamento é examinar e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Isso inclui a definição da "propriedade correspondente" que é usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Consulte o link nas *próximas etapas* para obter mais informações sobre mapeamentos de atributo.

## <a name="next-steps"></a>Próximos passos
[Personalizar os mapeamentos de atributos de provisionamento de usuário para aplicativos SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

