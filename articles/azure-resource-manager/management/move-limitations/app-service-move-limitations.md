---
title: Mover recursos do Azure App Service
description: Use o Azure Resource Manager para transferir os recursos do App Service para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655781"
---
# <a name="move-guidance-for-app-service-resources"></a>Mova orientação para os recursos do App Service

Este artigo descreve as etapas para mover os recursos do App Service. Existem requisitos específicos para transferir recursos do App Service para uma nova assinatura.

## <a name="move-across-subscriptions"></a>Mova-se através de assinaturas

Ao mover um Web App através de assinaturas, a seguinte orientação se aplica:

- O grupo de recursos de destino não pode ter nenhum recurso de Serviço de Aplicativo existente. Os recursos de Serviço de Aplicativo incluem:
    - Aplicativos Web
    - Planos do Serviço de Aplicativo
    - Certificados TLS/SSL carregados ou importados
    - Ambientes de Serviço de Aplicativo
- Todos os recursos de Serviço de Aplicativo no grupo de recursos devem ser movidos juntos. Observe que os ambientes de serviço de aplicativos não podem ser movidos para um novo Grupo de Recursos nem para uma nova assinatura.
- Você pode mover um certificado vinculado a uma web sem excluir as vinculações TLS, desde que o certificado seja movido com todos os outros recursos do grupo de recursos.
- Recursos do Serviço de Aplicativo podem ser movidos apenas no grupo de recursos no qual eles foram originalmente criados. Se um recurso do App Service não estiver mais em seu grupo de recursos original, mova-o de volta para seu grupo de recursos original. Em seguida, mova o recurso através de assinaturas.

Se você não se lembra do grupo de recursos original, você pode encontrá-lo através de diagnósticos. Para o seu aplicativo web, **selecione Diagnosticar e resolver problemas**. Em seguida, **selecione Configuração e Gerenciamento**.

![Selecione diagnósticos](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **opções de migração**.

![Selecione opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para obter etapas recomendadas para mover o aplicativo web.

![Selecione as etapas recomendadas](./media/app-service-move-limitations/recommended-steps.png)

Você vê as ações recomendadas a serem tomadas antes de mover os recursos. As informações incluem o grupo de recursos original para o aplicativo web.

![Recomendações](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Suporte de movimentação

Para determinar quais recursos do App Service podem ser movidos, consulte o status de suporte ao movimento para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).
