---
title: Azure Funções geo-desastre recuperação e alta disponibilidade
description: Como usar regiões geográficas para redundância e falhar em Funções Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080235"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Funções recuperação de desastres geográficos

Quando regiões inteiras do Azure ou data centers experimentam tempo de inatividade, é fundamental que a computação continue processando em uma região diferente.  Este artigo explicará algumas das estratégias que você pode usar para implantar funções para permitir a recuperação de desastres.

## <a name="basic-concepts"></a>Conceitos básicos

As funções do Azure são executadas em uma região específica.  Para obter maior disponibilidade, você pode implantar as mesmas funções em várias regiões.  Quando em várias regiões você pode ter suas funções em execução no padrão *ativo/ativo* ou padrão *ativo/passivo.*  

* Ativa/ativa. Ambas as regiões estão ativas e recebendo eventos (duplicados ou rotacionais). Ativo/ativo é recomendado para funções HTTPS em combinação com a Porta Frontal do Azure.
* Ativo/passivo. Uma região está ativa e recebendo eventos, enquanto uma secundária está ociosa.  Quando o failover é necessário, a região secundária é ativada e assume o processamento.  Isso é recomendado para funções não-HTTP, como Service Bus e Event Hubs.

Leia como [executar aplicativos em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) para obter mais informações sobre implantações em várias regiões.

## <a name="activeactive-for-https-functions"></a>Ativo/ativo para funções HTTPS

Para alcançar implantações ativas/ativas de funções, requer algum componente que possa coordenar os eventos entre ambas as regiões.  Para funções HTTPS, essa coordenação é realizada usando [a Porta frontal do Azure](../frontdoor/front-door-overview.md).  O Azure Front Door pode fazer solicitações HTTPS de rota e round-robin entre várias funções regionais.  Também verifica periodicamente a saúde de cada ponto final.  Se uma função regional parar de responder a verificações de saúde, o Azure Front Door irá tirá-lo da rotação e apenas encaminhar o tráfego para funções saudáveis.  

![Arquitetura para porta e função frontal do Azure](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Ativo/ativo para funções não-HTTPS

Você ainda pode obter implantações ativas/ativas para funções não-HTTPS.  No entanto, você precisa considerar como as duas regiões irão interagir ou coordenar uma com a outra.  Se você implantasse o mesmo aplicativo de função em duas regiões, cada uma acionando na mesma fila de Ônibus de Serviço, eles agiriam como consumidores concorrentes na desfila de fila.  Embora isso signifique que cada mensagem está sendo processada apenas por uma das instâncias, também significa que ainda há um único ponto de falha no único Ônibus de Serviço.  Se você implantar duas filas de Bus de Serviço (uma em uma região primária, uma em uma região secundária) e os dois aplicativos de função apontaram para sua fila de região, o desafio agora vem na forma como as mensagens de fila são distribuídas entre as duas regiões.  Muitas vezes isso significa que cada editor tenta publicar uma mensagem para *ambas as* regiões, e cada mensagem é processada por ambos os aplicativos de função ativa.  Embora isso crie um padrão ativo/ativo, ele cria outros desafios em torno da duplicação da computação e quando ou como os dados são consolidados.  Por essas razões, recomenda-se que os gatilhos não-HTTPS usem o padrão ativo/passivo.

## <a name="activepassive-for-non-https-functions"></a>Ativo/passivo para funções não-HTTPS

Ativo/passivo fornece uma maneira para apenas uma única função processar cada mensagem, mas fornece um mecanismo para falhar em uma região secundária em caso de desastre.  A Azure Functions trabalha ao lado [da georecuperação da Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md) e da georecuperação do [Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md).

Usando os gatilhos do Azure Event Hubs como exemplo, o padrão ativo/passivo envolveria o seguinte:

* O Azure Event Hub foi implantado em uma região primária e secundária.
* Geo-desastre habilitado para emparelhar o Hub de Eventos primário e secundário.  Isso também cria um "alias" que você pode usar para se conectar a hubs de eventos e mudar de principal para secundário sem alterar as informações de conexão.
* Aplicativos de função implantados em uma região primária e secundária.
* Os aplicativos de função estão acionando na seqüência de conexão *direta* (não-alias) para seu respectivo hub de eventos. 
* Os editores do centro de eventos devem publicar a seqüência de conexões alias. 

![Arquitetura de exemplo passivo ativo](media/functions-geo-dr/active-passive.png)

Antes do failover, os editores que enviarem para o alias compartilhado serão encaminhados para o hub de eventos principal.  O aplicativo de função principal está ouvindo exclusivamente o hub de eventos primários.  O aplicativo de função secundária será passivo e ocioso.  Assim que o failover for iniciado, os editores que enviarem para o alias compartilhado agora serão encaminhados para o centro de eventos secundários.  O aplicativo de função secundária agora se tornará ativo e começará a ser acionado automaticamente.  O failover efetivo para uma região secundária pode ser conduzido inteiramente a partir do hub de eventos, com as funções se tornando ativas apenas quando o respectivo hub de eventos estiver ativo.

Leia mais sobre informações e considerações para failover com [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) e [hubs de eventos.](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar porta da frente do Azure](../frontdoor/quickstart-create-front-door.md)
* [Considerações sobre failover do Event Hubs](../event-hubs/event-hubs-geo-dr.md#considerations)
