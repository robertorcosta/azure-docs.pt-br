---
title: Exigir MFA de redes não confiáveis-Azure Active Directory
description: Saiba como configurar uma política de acesso condicional no Azure Active Directory (Azure AD) para para tentativas de acesso de redes não confiáveis.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379998"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Como: exigir MFA para acesso de redes não confiáveis com acesso condicional   

O Azure AD (Azure Active Directory) permite o logon único em dispositivos, aplicativos e serviços em qualquer lugar. Os usuários podem acessar seus aplicativos na nuvem não apenas na rede de sua organização, mas também em qualquer localização não confiável da Internet. Uma melhor prática comum para acesso em redes não confiáveis é exigir o MFA (autenticação multifator).

Este artigo fornece as informações necessárias para configurar uma política de acesso condicional que requer MFA para acesso de redes não confiáveis. 

## <a name="prerequisites"></a>pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os [conceitos básicos](overview.md) do acesso condicional do Azure AD 
- As [práticas recomendadas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure

## <a name="scenario-description"></a>Descrição do cenário

Para dominar o equilíbrio entre segurança e produtividade, pode ser suficiente que você apenas exija uma senha para entradas na rede de sua organização. No entanto, para o acesso em uma localização de rede não confiável, há um risco maior de que as entradas não sejam feitas por usuários legítimos. Para resolver esse problema, você pode bloquear o acesso em redes não confiáveis. Como alternativa, também é possível exigir MFA (autenticação multifator) para obter garantia adicional de que uma tentativa foi feita pelo proprietário legítimo da conta. 

Com o acesso condicional do Azure AD, você pode atender a esse requisito com uma única política que concede acesso: 

- Para aplicativos na nuvem selecionados
- Para usuários e grupos selecionados  
- Exigir autenticação multifator 
- Quando o acesso é originado de: 
   - Um local que não é confiável

## <a name="implementation"></a>Implementação

O desafio desse cenário é converter o *acesso de um local de rede não confiável* em uma condição de acesso condicional. Em uma política de acesso condicional, você pode configurar a [condição de locais](location-condition.md) para tratar de cenários relacionados a locais de rede. A condição de locais permite que você selecione locais nomeados, que são agrupamentos lógicos de intervalos de endereços IP, países e regiões.  

Normalmente, sua organização possui um ou mais intervalos de endereços, por exemplo, 199.30.16.0-199.30.16.15.
É possível configurar um local nomeado por:

- Especificando esse intervalo (199.30.16.0/28) 
- Atribuindo um nome descritivo, como **Rede Corporativa** 

Em vez de tentar definir quais são todos os locais confiáveis ou não confiáveis, é possível:

- Incluir qualquer localização 

   ![Acesso Condicional](./media/untrusted-networks/02.png)

- Excluir todos os locais confiáveis 

   ![Acesso Condicional](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Implantação de política

Com a abordagem descrita neste artigo, agora você pode configurar uma política de acesso condicional para locais não confiáveis. Para garantir que sua política funcione conforme o esperado, a melhor prática é testá-la antes de distribuí-la em produção. O ideal é usar um locatário de teste para verificar se a nova política funciona conforme o esperado. Para obter mais informações, confira [Como implantar uma nova política](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre o acesso condicional, consulte [o que é o acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
