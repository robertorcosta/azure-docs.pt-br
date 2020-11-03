---
title: Recomendações de segurança para o Azure Key Vault
description: Recomendações de segurança para o Azure Key Vault. Implementar essas diretrizes ajudará a atender as obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: f96df14fb754578b58fb67ac116ac79cff3ffdcb
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289844"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Recomendações de segurança para o Azure Key Vault

Este artigo contém as recomendações de segurança do Azure Key Vault. Implementar essas recomendações ajudará a atender as obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada. Para obter mais informações sobre o que a Microsoft faz para atender às responsabilidades do provedor de serviços, leia [Responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algumas das recomendações incluídas neste artigo podem ser monitoradas automaticamente pela Central de Segurança do Azure. A Central de Segurança do Azure é a primeira linha de defesa na proteção dos recursos no Azure. Ela analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como solucioná-las.

- Para obter mais informações sobre as recomendações da Central de Segurança do Azure, confira [Recomendações de segurança na Central de Segurança do Azure](../../security-center/security-center-recommendations.md).
- Para obter informações sobre a Central de Segurança do Azure, confira [O que é a Central de Segurança do Azure?](../../security-center/security-center-introduction.md)

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
|Habilitar exclusão reversível | A [exclusão reversível](soft-delete-overview.md) permite recuperar cofres excluídos e objetos de cofre |  - |
| Limite o acesso para dados do cofre  | Siga o princípio de privilégios mínimos e limite quais membros da organização têm acesso aos dados do cofre |  - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Limitar o número de usuários com acesso de colaborador | Se um usuário tiver permissões de Colaborador para um plano de gerenciamento do cofre de chaves, o usuário pode conceder a si mesmo o acesso ao plano de dados, definindo uma política de acesso do Key Vault. Você deve controlar rigorosamente quem tem acesso da função de Colaborador a seus cofres de chaves. Somente os usuários com a necessidade de acessar pessoas autorizadas podem acessar e gerenciar seus cofres. Você pode ler [Proteger o acesso a um cofre de chaves](secure-your-key-vault.md)) | - |

## <a name="monitoring"></a>Monitoramento

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
 Os logs de diagnóstico no Key Vault devem estar habilitados | Ativar os logs e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. | [Sim](../../security-center/security-center-identity-access.md) |
| Restringir quem pode acessar os logs do cofre de chaves do Azure | [Logs do cofre de chaves](logging.md)) salva as informações sobre as atividades executadas em seu cofre, como criação ou exclusão de cofres, chaves, segredos e pode ser usado durante uma investigação |  - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
|Limitar a exposição da rede | O acesso à rede deve ser limitado às redes virtuais usadas por soluções que exigem acesso ao cofre. Examine as informações sobre [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md)) | - |

## <a name="next-steps"></a>Próximas etapas

Verifique com seu provedor de aplicativos se há requisitos de segurança adicionais. Para obter mais informações sobre como desenvolver aplicativos seguros, confira [Documentação de desenvolvimento seguro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).