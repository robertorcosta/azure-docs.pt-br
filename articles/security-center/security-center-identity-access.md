---
title: Monitorar identidade e acesso na Central de Segurança do Azure | Microsoft Docs
description: Aprenda a usar os recursos de identidade e acesso na Central de Segurança do Azure para monitorar a atividade de acesso e os problemas com identidade do usuário.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 313f10c77da499f4c405134028b975900d2d2a9e
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590227"
---
# <a name="monitor-identity-and-access"></a>Monitorar a identidade e acesso

> [!TIP]
> Desde março de 2020, as recomendações de identidade e acesso da Central de Segurança do Azure estão incluídas em todas as assinaturas do tipo de preço gratuito. Se você tiver assinaturas do tipo de preço gratuito, a classificação de segurança delas será afetada, pois elas não tiveram a identidade e segurança de acesso avaliadas anteriormente. 

Quando o Security Center identifica possíveis vulnerabilidades de segurança, ele cria recomendações que guiam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defender a rede e mais sobre como defender seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. Hoje em dia, com mais dados e aplicativos se mudando para a nuvem, a identidade se tornou o novo perímetro.

Ao monitorar suas atividades de identidade, você poderá tomar medidas proativas antes que um incidente ocorra ou realizar ações de reação para interromper uma tentativa de ataque. Por exemplo, a Central de Segurança pode sinalizar contas preteridas (contas desnecessárias e que têm o acesso bloqueado pelo Azure AD (Azure Active Directory)) para remoção. 

Exemplos de recomendações que você pode conferir na seção de segurança de recursos **Identidade e acesso** da Central de Segurança do Azure incluem:

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- Um máximo de três proprietários deve ser designado para sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- As contas preteridas devem ser removidas de sua assinatura

Para obter mais informações sobre essas recomendações, bem como uma lista completa das recomendações que você pode ver aqui, confira [Recomendações de identidade e acesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> Se a sua assinatura tiver mais de 600 contas, o Security Center não poderá executar as recomendações de identidade em relação à sua assinatura. As recomendações não executadas são listadas em "avaliações indisponíveis" abaixo.
A Central de Segurança não pode executar as recomendações de identidade em relação aos agentes de administração de um parceiro do provedor de soluções de nuvem (CSP).
>


Todas as recomendações de identidade e acesso estão disponíveis dentro de dois controles de segurança na página **Recomendações**:

- Gerenciar acesso e permissões 
- Habilitar MFA

![Os dois controles de segurança com as recomendações relacionadas a identidade e acesso](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Habilitar MFA (autenticação multifator)

Habilitar a MFA requer [permissões de locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Se você tiver uma edição Premium do AD, habilite a MFA com o [Acesso Condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md).

- Se você estiver usando o AD Free Edition, habilite **os padrões de segurança** em Azure Active Directory conforme descrito na [documentação do AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam a outros tipos de recurso do Azure, confira os seguintes artigos:

- [Protegendo suas máquinas e aplicativos na Central de segurança do Azure](security-center-virtual-machine-protection.md)
- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
- [Proteger seus dados na Central de segurança do Azure e o serviço do SQL Azure](security-center-sql-service-recommendations.md)