---
title: Portal de inscrição de autoatendimento para a Colaboração B2B – Azure AD
description: A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 794a13a3f863c732d4e7ed8cedcbd73f7cbc0d0b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272100"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal de autoatendimento para inscrição na colaboração B2B do Azure AD

Os clientes podem fazer muito com os recursos internos expostos por meio do [Portal do Azure](https://portal.azure.com) e o [Painel de Acesso de Aplicativos](https://myapps.microsoft.com) para usuários finais. Porém, você pode precisar personalizar o fluxo de trabalho de integração para usuários B2B para se adequar às necessidades da sua organização. Você pode fazer isso com [a API de convite](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Como uma organização que está convidando pode não saber antecipadamente quem são os colaboradores externos individuais que precisam de acesso aos seus recursos. Você precisará de uma maneira para os usuários de empresas parceiras inscreverem-se com um conjunto de políticas que você, como organização convidando, controla. Este cenário é possível por meio de APIs. Há um [projeto de exemplo no GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) que faz exatamente isso.

Esse projeto no Github mostra como as organizações podem usar as APIs e fornecer uma capacidade de inscrição para autoatendimento baseada em política para seus parceiros confiáveis, com regras que determinam os aplicativos que podem acessar. Os usuários do parceiro podem obter acesso aos recursos quando necessário. Podem fazer isso com segurança, sem a necessidade de convidar a organização para convidá-los manualmente. É possível implantar facilmente o projeto em uma assinatura do Azure de sua escolha.

## <a name="as-is-code"></a>Código no estado em que se encontra

Esse código é disponibilizado como um exemplo para demonstrar o uso da API de convite do Azure Active Directory B2B. Ele deve ser personalizado por sua equipe de desenvolvimento ou um parceiro e deverá ser revisado antes de você implantar em um cenário de produção.

## <a name="next-steps"></a>Próximas etapas

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Licenciamento da colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](faq.md)
