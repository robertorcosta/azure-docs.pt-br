---
title: Azure Defender para Serviço de Aplicativo – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Serviço de Aplicativo.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c53f7e712668f32766feaf76d6a08582bda9af22
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449101"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introdução ao Azure Defender para Serviço de Aplicativo

O Serviço de Aplicativo do Azure é uma plataforma totalmente gerenciada para criar e hospedar aplicativos Web e APIs sem se preocupar em ter de gerenciar a infraestrutura. Ele fornece gerenciamento, monitoramento e informações operacionais para atender aos requisitos de desempenho, segurança e conformidade em nível empresarial. Para obter mais informações, confira [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/).

O **Azure Defender para Serviço de Aplicativo** usa a escala da nuvem para identificar os ataques direcionados a aplicativos em execução no Serviço de Aplicativo. Os invasores investigam aplicativos Web para encontrar e explorar pontos fracos. Antes de serem roteadas para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways, onde são inspecionadas e registradas. Esses dados são usados para identificar explorações e invasores e para aprender novos padrões que serão usados posteriormente.

Usando a visibilidade que o Azure tem como um provedor de nuvem, a Central de Segurança analisa os logs internos do Serviço de Aplicativo para identificar a metodologia de ataque em vários alvos. Por exemplo, a metodologia inclui verificação generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs e mostra padrões de rastreamento para pontos de extremidade semelhantes em vários hosts. Os ataques estão procurando uma página ou um plug-in vulnerável e não podem ser identificados no ponto de vista de um único host.


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Versão Prévia|
|Preço:|O [Azure Defender para Serviço de Aplicativo](azure-defender.md) é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Planos do Serviço de Aplicativo compatíveis:|![Sim](./media/icons/yes-icon.png) Básico, Padrão, Premium, Isolado ou Linux<br>![Não](./media/icons/no-icon.png) Gratuito, Compartilhado ou Consumo<br>[Saiba mais sobre os Planos do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>O que o Azure Defender para Serviço de Aplicativo protege?

Com o Plano do Serviço de Aplicativo habilitado, a Central de Segurança avalia os recursos cobertos pelo Plano do Serviço de Aplicativo e gera recomendações de segurança com base nas descobertas. A Central de Segurança protege a instância da VM na qual o Serviço de Aplicativo está sendo executado e a interface de gerenciamento. Ele também monitora solicitações e respostas enviadas para e de seus aplicativos em execução no Serviço de Aplicativo.

Se você estiver executando um plano do Serviço de Aplicativo baseado no Windows, a Central de Segurança também terá acesso às áreas restritas e VMs subjacentes. Juntamente com os dados de log mencionados acima, a infraestrutura pode fornecer as informações, desde um novo ataque que está ocorrendo no mundo até comprometimentos nas máquinas dos clientes. Portanto, mesmo que a Central de Segurança seja implantada depois que um aplicativo Web for explorado, ela poderá detectar ataques contínuos.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteger os seus aplicativos Web e APIs do Serviço de Aplicativo do Azure
Para proteger seu Plano do Serviço de Aplicativo do Azure com o Azure Defender para Serviço de Aplicativo:

- Verifique se você tem um Plano do Serviço de Aplicativo compatível, associado aos computadores dedicados. Os planos compatíveis são listados acima em [Disponibilidade](#availability).

- Habilitar o **Azure Defender** na sua assinatura (opcionalmente, você pode habilitar apenas o plano **Azure Defender para Serviço de Aplicativo**), conforme descrito nos [Preços da Central de Segurança do Azure](security-center-pricing.md)

A Central de Segurança é integrada de maneira nativa ao Serviço de Aplicativo, eliminando a necessidade de implantação e integração – A integração é transparente.

>[!NOTE]
> A página preços e configurações lista uma série de instâncias para a **Quantidade de Recursos**. Isso representa o número total de instâncias de computação, em todos os Planos do Serviço de Aplicativo desta assinatura, em execução no momento em que você abriu a página de tipo de preço.
>
> O Serviço de Aplicativo do Azure oferece uma série de planos. O Plano do Serviço de Aplicativo define o conjunto de recursos de computação para um aplicativo Web ser executado. Eles são equivalentes aos farms de servidores na hospedagem Web convencional. Um ou mais aplicativos podem ser configurados para ser executado nos mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicativo).
>
>Para validar a contagem, vá para "Planos do Serviço de Aplicativo", no portal do Azure, no qual você pode ver o número de instâncias de computação usadas em cada plano. 



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para Serviço de Aplicativo. 

Para obter material relacionado, consulte os seguintes artigos: 

- Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).
- Para obter uma lista dos alertas do Serviço de Aplicativo do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureappserv).
- Para saber mais sobre os planos do Serviço de Aplicativo, confira [Planos do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Habilitar o Azure Defender](security-center-pricing.md)