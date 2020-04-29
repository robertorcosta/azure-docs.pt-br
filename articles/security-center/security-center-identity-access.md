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
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481952"
---
# <a name="monitor-identity-and-access"></a>Monitorar a identidade e acesso

> [!TIP]
> De março de 2020, as recomendações de identidade e acesso da central de segurança do Azure estão incluídas em todas as assinaturas no tipo de preço gratuito. Se você tiver assinaturas na camada gratuita, sua pontuação segura será afetada, pois elas não foram avaliadas anteriormente para sua identidade e segurança de acesso. 

Quando o Security Center identifica possíveis vulnerabilidades de segurança, ele cria recomendações que guiam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defender a rede e mais sobre como defender seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. Hoje em dia, com mais dados e aplicativos se mudando para a nuvem, a identidade se tornou o novo perímetro.

Monitorando as atividades de identidade, você pode tomar ações proativas antes que um incidente ocorra ou ações reativas para interromper uma tentativa de ataque. Por exemplo, a central de segurança pode sinalizar contas preteridas (contas que não são mais necessárias e impedida de entrar por Azure Active Directory) para remoção. 

Exemplos de recomendações que podem ser exibidas na seção segurança de recursos de **identidade e acesso** da central de segurança do Azure incluem:

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- Um máximo de três proprietários deve ser designado para sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- As contas preteridas devem ser removidas de sua assinatura

Para obter mais informações sobre essas recomendações, bem como uma lista completa das recomendações que você pode ver aqui, consulte [recomendações de identidade e acesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> Se a sua assinatura tiver mais de 600 contas, o Security Center não poderá executar as recomendações de identidade em relação à sua assinatura. As recomendações que não são executadas são listadas em "avaliações não disponíveis" abaixo.
A Central de Segurança não pode executar as recomendações de identidade em relação aos agentes de administração de um parceiro do provedor de soluções de nuvem (CSP).
>


Todas as recomendações de identidade e acesso estão disponíveis dentro de dois controles de segurança na página **recomendações** :

- Gerenciar acesso e permissões 
- Habilitar MFA

![Os dois controles de segurança com as recomendações relacionadas à identidade e ao acesso](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Habilitar a autenticação multifator (MFA)

Habilitar a MFA requer [permissões de locatário do Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Se você tiver uma edição Premium do AD, habilite a MFA usando o [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Os usuários da edição gratuita do AD podem habilitar os **padrões de segurança** no Azure Active Directory conforme descrito na [documentação do AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) , mas a recomendação da central de segurança para habilitar a MFA ainda será exibida.


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes artigos:

- [Protegendo suas máquinas e aplicativos na Central de Segurança do Azure](security-center-virtual-machine-protection.md)
- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
- [Proteger seus dados na Central de segurança do Azure e o serviço do SQL Azure](security-center-sql-service-recommendations.md)