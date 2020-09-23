---
title: Azure defender para serviço de aplicativo-os benefícios e recursos
description: Saiba mais sobre os benefícios e recursos do Azure defender para o serviço de aplicativo.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8d3eec85ba72a4b5ac43bd3988694e833abfe73b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933725"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introdução ao Azure defender para o serviço de aplicativo

Azure App serviço é uma plataforma totalmente gerenciada para criar e hospedar seus aplicativos Web e APIs sem se preocupar em ter de gerenciar a infraestrutura. Ele fornece gerenciamento, monitoramento e informações operacionais para atender aos requisitos de desempenho, segurança e conformidade de nível empresarial. Para obter mais informações, consulte [Azure app Service](https://azure.microsoft.com/services/app-service/).

O **Azure defender para o serviço de aplicativo** usa a escala da nuvem para identificar ataques direcionados a aplicativos em execução no serviço de aplicativo. Os invasores investigam aplicativos Web para encontrar e explorar pontos fracos. Antes de serem roteadas para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways, onde são inspecionadas e registradas. Esses dados são usados para identificar explorações e invasores e para aprender novos padrões que serão usados posteriormente.

Usando a visibilidade que o Azure tem como um provedor de nuvem, a Central de Segurança analisa os logs internos do Serviço de Aplicativo para identificar a metodologia de ataque em vários alvos. Por exemplo, a metodologia inclui verificação generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs e mostra padrões de rastreamento para pontos de extremidade semelhantes em vários hosts. Os ataques estão procurando uma página ou um plug-in vulnerável e não podem ser identificados no ponto de vista de um único host.


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Versão Prévia|
|Refere|O [Azure defender para serviço de aplicativo](azure-defender.md) é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Planos do serviço de aplicativo com suporte:|![Sim ](./media/icons/yes-icon.png) básico, Standard, Premium, isolado ou Linux<br>![Sem ](./media/icons/no-icon.png) uso gratuito, compartilhado ou de consumo<br>[Saiba mais sobre os planos do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>O que o Azure defender para o serviço de aplicativo protege?

Com o plano do serviço de aplicativo habilitado, a central de segurança avalia os recursos cobertos pelo seu plano do serviço de aplicativo e gera recomendações de segurança com base em suas descobertas. A central de segurança protege a instância de VM na qual seu serviço de aplicativo está em execução e a interface de gerenciamento. Ele também monitora solicitações e respostas enviadas para e de seus aplicativos em execução no Serviço de Aplicativo.

Se você estiver executando um plano do Serviço de Aplicativo baseado no Windows, a Central de Segurança também terá acesso às áreas restritas e VMs subjacentes. Juntamente com os dados de log mencionados acima, a infraestrutura pode fornecer as informações, desde um novo ataque que está ocorrendo no mundo até comprometimentos nas máquinas dos clientes. Portanto, mesmo que a Central de Segurança seja implantada depois que um aplicativo Web for explorado, ela poderá detectar ataques contínuos.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteger os seus aplicativos Web e APIs do Serviço de Aplicativo do Azure
Para proteger seu plano de serviço Azure App com o Azure defender para o serviço de aplicativo:

- Verifique se você tem um plano do serviço de aplicativo com suporte associado a computadores dedicados. Os planos com suporte estão listados acima em [disponibilidade](#availability).

- Habilite o **Azure defender** em sua assinatura (opcionalmente, você pode habilitar apenas o **Azure defender para** o plano do serviço de aplicativo), conforme descrito em [preços da central de segurança do Azure](security-center-pricing.md)

A central de segurança é integrada nativamente com o serviço de aplicativo, eliminando a necessidade de implantação e integração-o é transparente.

>[!NOTE]
> A página preços e configurações lista um número de instâncias para a **quantidade de recursos**. Isso representa o número total de instâncias de computação, em todos os planos do serviço de aplicativo nesta assinatura, em execução no momento em que você abriu a página tipo de preço.
>
> O Azure App Service oferece uma variedade de planos. Seu plano do serviço de aplicativo define o conjunto de recursos de computação para um aplicativo Web a ser executado. Eles são equivalentes aos farms de servidores na hospedagem Web convencional. Um ou mais aplicativos podem ser configurados para ser executado nos mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicativo).
>
>Para validar a contagem, vá para "planos do serviço de aplicativo" no portal do Azure, no qual você pode ver o número de instâncias de computação usadas por cada plano. 



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure defender para o serviço de aplicativo. 

Para obter material relacionado, consulte os seguintes artigos: 

- Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).
- Para obter uma lista dos alertas do Serviço de Aplicativo do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureappserv).
- Para saber mais sobre os planos do Serviço de Aplicativo, confira [Planos do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Habilitar o Azure Defender](security-center-pricing.md)