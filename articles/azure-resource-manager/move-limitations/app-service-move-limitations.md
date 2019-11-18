---
title: Mover Azure App recursos do serviço
description: Use Azure Resource Manager para mover os recursos do serviço de aplicativo para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150901"
---
# <a name="move-guidance-for-app-service-resources"></a>Mover diretrizes para recursos do serviço de aplicativo

As etapas para mover os recursos do serviço de aplicativo são diferentes se você estiver movendo os recursos dentro de uma assinatura ou para uma nova assinatura.

## <a name="move-in-same-subscription"></a>Mover na mesma assinatura

Ao mover um aplicativo Web _dentro da mesma assinatura_, você não pode mover os certificados SSL de terceiros. No entanto, você pode mover um aplicativo Web para o novo grupo de recursos sem mover o respectivo certificado SSL de terceiros, mantendo a funcionalidade SSL do aplicativo.

Se você deseja mover o certificado SSL com o aplicativo Web, siga estas etapas:

1. Excluir o certificado de terceiros do aplicativo Web, mas manter uma cópia do seu certificado
2. Mova o aplicativo Web.
3. Carregue o certificado de terceiros para o aplicativo Web movido.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Ao mover um aplicativo Web _entre assinaturas_, as seguintes limitações se aplicam:

- O grupo de recursos de destino não pode ter nenhum recurso de Serviço de Aplicativo existente. Os recursos de Serviço de Aplicativo incluem:
    - Aplicativos Web
    - Planos do Serviço de Aplicativo
    - Certificados SSL importados ou carregados
    - Ambientes de Serviço de Aplicativo
- Todos os recursos de Serviço de Aplicativo no grupo de recursos devem ser movidos juntos.
- Recursos do Serviço de Aplicativo podem ser movidos apenas no grupo de recursos no qual eles foram originalmente criados. Se um recurso do serviço de aplicativo não estiver mais em seu grupo de recursos original, mova-o de volta para seu grupo de recursos original. Em seguida, mova o recurso entre assinaturas.

Se você não se lembrar do grupo de recursos original, poderá encontrá-lo por meio de diagnóstico. Para seu aplicativo Web, selecione **diagnosticar e resolver problemas**. Em seguida, selecione **configuração e gerenciamento**.

![Selecionar diagnóstico](./media/app-service-move-limitations/select-diagnostics.png)

Selecione **Opções de migração**.

![Selecionar opções de migração](./media/app-service-move-limitations/select-migration.png)

Selecione a opção para obter as etapas recomendadas para mover o aplicativo Web.

![Selecione as etapas recomendadas](./media/app-service-move-limitations/recommended-steps.png)

Você vê as ações recomendadas a serem executadas antes de mover os recursos. As informações incluem o grupo de recursos original para o aplicativo Web.

![Recomendações](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Mover Certificado do Serviço de Aplicativo

Você pode mover seu Certificado do Serviço de Aplicativo para um novo grupo de recursos ou assinatura. Se seu Certificado do Serviço de Aplicativo estiver associado a um aplicativo Web, você deverá executar algumas etapas antes de mover os recursos para uma nova assinatura. Exclua a associação de SSL e o certificado particular do aplicativo Web antes de mover os recursos. O Certificado do Serviço de Aplicativo não precisa ser excluído, apenas o certificado particular no aplicativo Web.

## <a name="move-support"></a>Suporte de movimentação

Para determinar quais recursos do serviço de aplicativo podem ser movidos, consulte mover status de suporte para:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).
