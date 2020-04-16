---
title: Recomendações de segurança para Azure Key Vault
description: Recomendações de segurança para o Azure Key Vault. A implementação desta orientação ajudará você a cumprir suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: b7bd85508851df7c7a664862b02141bf4d3bea1f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429844"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Recomendações de segurança para Azure Key Vault

Este artigo contém recomendações de segurança para o Azure Key Vault. A implementação dessas recomendações ajudará você a cumprir suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do provedor de serviços, leia [responsabilidades compartilhadas para computação em nuvem.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)

Algumas das recomendações incluídas neste artigo podem ser monitoradas automaticamente pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa na proteção de seus recursos no Azure. Ele analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, fornece recomendações sobre como lidar com eles.

- Para obter mais informações sobre as recomendações do Azure Security Center, consulte [recomendações de segurança no Azure Security Center](../../security-center/security-center-recommendations.md).
- Para obter informações sobre o Azure Security Center, consulte [o Centro de Segurança do Azure?](../../security-center/security-center-intro.md)

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
|Habilite a exclusão suave | [Exclusão suave](overview-soft-delete.md)) permite que você recupere cofres excluídos e objetos de cofre |  - |
| Limitar o acesso aos dados do cofre  | Siga o princípio de menor privilégio e limite que os membros da sua organização têm acesso aos dados do cofre |  - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Limitar o número de usuários com acesso ao contribuinte | Se um usuário tiver permissões de Contribuinte para um plano de gerenciamento de cofre chave, o usuário poderá conceder-se acesso ao plano de dados definindo uma política de acesso do Key Vault. Você deve controlar com firmeismo quem tem acesso ao papel contribuinte aos seus cofres principais. Certifique-se de que apenas aqueles com necessidade de acesso a pessoas autorizadas podem acessar e gerenciar seus cofres. Você pode ler [acesso seguro a um cofre de chaves](secure-your-key-vault.md)) | - |

## <a name="monitoring"></a>Monitoramento

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
 Os logs de diagnóstico no Key Vault devem ser habilitados | Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. | [Sim](../../security-center/security-center-identity-access.md) |
| Restringir quem pode acessar seus registros de cofres do Azure Key | [Registros do Cofre de Chaves)](logging.md) salvar informações sobre as atividades realizadas em seu cofre, como criação ou exclusão de cofres, chaves, segredos e pode ser usado durante uma investigação |  - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
|Limitar a exposição da rede | O acesso à rede deve ser limitado às redes virtuais utilizadas por soluções que requerem acesso ao cofre. Revisar informações sobre [os pontos finais de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md)) | - |

## <a name="next-steps"></a>Próximas etapas

Verifique com o provedor de aplicativos se há requisitos adicionais de segurança. Para obter mais informações sobre o desenvolvimento de aplicativos seguros, consulte [Documentação de Desenvolvimento Seguro](../../security/fundamentals/abstract-develop-secure-apps.md).
