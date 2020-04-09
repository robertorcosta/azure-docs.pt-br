---
title: Plano de continuidade dos negócios – QnA Maker
description: O principal objetivo do plano de continuidade de negócios é criar um ponto de extremidade resiliente da base de conhecimento que garantiria a total ausência de tempo de inatividade para o bot ou aplicativo que a estivesse consumindo.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887054"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Criar um plano de continuidade de negócios para o seu serviço QnA Maker

O principal objetivo do plano de continuidade de negócios é criar um ponto de extremidade resiliente da base de conhecimento que garantiria a total ausência de tempo de inatividade para o bot ou aplicativo que a estivesse consumindo.

## <a name="business-continuity-with-traffic-manager"></a>Continuidade de negócios com o gerente de tráfego

> [!div class="mx-imgBorder"]
> ![Plano de backup do QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível como representada acima é a seguinte:

1. Configurar dois [serviços QnA Maker](set-up-qnamaker-service-azure.md) em paralelo nas [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Faça backup](../../../app-service/manage-backup.md) do serviço principal do QnA Maker App e [restaure-o](../../../app-service/web-sites-restore.md) na configuração secundária. Isso garantirá que ambas as configurações funcionem com o mesmo nome de host e chaves.

3. Mantenha os índices de pesquisa azure primários e secundários em sincronia. Use a amostra do GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como restaurar os índices do Azure de backup.

4. Fazer backup do Application Insights usando [exportação contínua](../../../application-insights/app-insights-export-telemetry.md).

5. Depois que as pilhas primárias e secundárias forem configuradas, use o [gerenciador de tráfego](../../../traffic-manager/traffic-manager-overview.md) para configurar os dois pontos de extremidade e configurar um método de roteamento.

6. Você precisaria criar um TLS (Transport Layer Security, segurança de camada de transporte), anteriormente conhecido como Secure Sockets Layer (SSL), certificado para o ponto final do gerenciador de tráfego. [Vincule o certificado TLS/SSL](../../../app-service/configure-ssl-bindings.md) em seus serviços do App.

7. Por fim, use o ponto de extremidade do gerenciador de tráfego em seu bot ou aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolher capacidade](./improve-knowledge-base.md)