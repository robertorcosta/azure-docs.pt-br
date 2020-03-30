---
title: Backends e backend pools em Azure Front Door| Microsoft Docs
description: Este artigo descreve quais pools backends e backend estão na configuração front door.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293464"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Backends e backend pools na porta da frente do Azure
Este artigo descreve conceitos sobre como mapear a implantação do seu aplicativo com o Azure Front Door. Ele também explica os diferentes termos na configuração front door em torno de backends de aplicativos.

## <a name="backends"></a>Back-ends
Um backend é igual à instância de implantação de um aplicativo em uma região. A Porta da Frente suporta backends tanto do Azure quanto do Não-Azure, de modo que a região não está restrita apenas às regiões do Azure. Além disso, pode ser o seu datacenter local ou uma instância de aplicativo em outra nuvem.

Os backends do Front Door referem-se ao nome do host ou IP público do seu aplicativo, que pode atender às solicitações do cliente. Os backends não devem ser confundidos com seu nível de banco de dados, nível de armazenamento e assim por diante. Os backends devem ser vistos como o ponto final público do seu backend de apêndice. Quando você adiciona um backend em um pool de back-end da porta da frente, você também deve adicionar o seguinte:

- **Tipo de host backend**. O tipo de recurso que você deseja adicionar. O Front Door suporta a autodescoberta de backends de aplicativos do serviço de aplicativo, serviço de nuvem ou armazenamento. Se você quiser um recurso diferente no Azure ou mesmo um backend não-Azure, selecione **Host Personalizado**.

    >[!IMPORTANT]
    >Durante a configuração, as APIs não validam se o backend estiver inacessível nos ambientes da Porta da Frente. Certifique-se de que a porta da frente pode alcançar seu backend.

- **Assinatura e nome de host do Backend**. Se você não tiver selecionado **host personalizado** para o tipo de host backend, selecione seu backend escolhendo a assinatura apropriada e o nome correspondente do host backend na ui.

- **Cabeçalho de host backend**. O valor do cabeçalho do host enviado para o backend para cada solicitação. Para obter mais informações, consulte [o cabeçalho do host Backend](#hostheader).

- **Prioridade**. Atribua prioridades aos seus diferentes backends quando você quiser usar um backend de serviço primário para todo o tráfego. Além disso, forneça backups se os backends primários ou de backup estiverem indisponíveis. Para obter mais informações, consulte [Prioridade](front-door-routing-methods.md#priority).

- **Peso**. Atribua pesos aos seus diferentes backends para distribuir o tráfego através de um conjunto de backends, uniformemente ou de acordo com os coeficientes de peso. Para obter mais informações, consulte [Pesos](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Cabeçalho de host backend

As solicitações encaminhadas pelo Front Door para um backend incluem um campo de cabeçalho de host que o backend usa para recuperar o recurso direcionado. O valor para esse campo geralmente vem do URI de back-end e tem o host e a porta.

Por exemplo, uma `www.contoso.com` solicitação feita para terá o cabeçalho do host www.contoso.com. Se você usar o portal Azure para configurar seu backend, o valor padrão para este campo será o nome de host do backend. Se o seu backend for contoso-westus.azurewebsites.net, no portal Azure, o valor autopovoado para o cabeçalho de host backend será contoso-westus.azurewebsites.net. No entanto, se você usar modelos do Azure Resource Manager ou outro método sem definir explicitamente este campo, o Front Door enviará o nome do host de entrada como o valor para o cabeçalho do host. Se a solicitação foi\.feita para www contoso.com, e seu backend for contoso-westus.azurewebsites.net que tenha\.um campo de cabeçalho vazio, a Porta da Frente definirá o cabeçalho do host como www contoso.com.

A maioria dos backends de aplicativos (Azure Web Apps, Blob storage e Cloud Services) exige que o cabeçalho do host corresponda ao domínio do backend. No entanto, o host frontend que rotas para o seu backend usará um nome de host diferente, como www.contoso.net.

Se o backend exigir que o cabeçalho do host corresponda ao nome do host backend, certifique-se de que o cabeçalho de host backend inclua o backend do nome do host.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurando o cabeçalho de host do back-end para o back-end

Para configurar o campo **de cabeçalho do host backend** para um backend na seção pool backend:

1. Abra o recurso front door e selecione o pool de backend com o backend para configurar.

2. Adicione um backend se você não tiver feito isso, ou edite um existente.

3. Defina o campo de cabeçalho do host backend como um valor personalizado ou deixe-o em branco. O nome de host para a solicitação recebida será usado como o valor do cabeçalho do host.

## <a name="backend-pools"></a>Piscinas de backend
Um pool de backend no Front Door refere-se ao conjunto de backends que recebem tráfego semelhante para o seu aplicativo. Em outras palavras, é um agrupamento lógico de instâncias de aplicativos em todo o mundo que recebem o mesmo tráfego e respondem com comportamento esperado. Esses backends são implantados em diferentes regiões ou dentro da mesma região. Todos os backends podem estar no modo de implantação Ativo/Ativo ou no que é definido como configuração Ativa/Passiva.

Um pool de backend define como os diferentes backends devem ser avaliados através de sondas de saúde. Também define como ocorre o balanceamento de carga entre eles.

### <a name="health-probes"></a>Investigações de integridade
O Front Door envia solicitações periódicas de teste HTTP/HTTPS para cada um dos seus backends configurados. As solicitações de teste determinam a proximidade e a saúde de cada backend para carregar o equilíbrio das solicitações do usuário final. As configurações do teste de saúde para um pool de backend definem como pesquisamos o estado de saúde dos backends de aplicativos. As seguintes configurações estão disponíveis para configuração de balanceamento de carga:

- **Caminho**: A URL usada para solicitações de teste para todos os backends no pool de backend. Por exemplo, se um de seus backends for contoso-westus.azurewebsites.net e o caminho estiver definido como /probe/test.aspx, então os ambientes da Porta da Frente, assumindo que o protocolo esteja definido como HTTP, enviarão solicitações de teste de saúde para http\://contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protocolo**: Define se deve enviar as solicitações do teste de saúde da Porta da Frente para seus backends com o protocolo HTTP ou HTTPS.

- **Método**:O método HTTP a ser usado para o envio de sondas de saúde. As opções incluem GET ou HEAD (padrão).
    > [!NOTE]
    > Para reduzir a carga e o custo em seus backends, o Front Door recomenda o uso de solicitações de HEAD para sondas de saúde.

- **Intervalo (segundos):** Define a freqüência das sondas de saúde para as extremidades traseiras ou os intervalos em que cada um dos ambientes da Porta da Frente envia uma sonda.

    >[!NOTE]
    >Para failovers mais rápidos, defina o intervalo como um valor mais baixo. Quanto menor o valor, maior o volume de testes de saúde que seus backends recebem. Por exemplo, se o intervalo for definido para 30 segundos com, digamos, 100 POPs da Porta da Frente globalmente, cada backend receberá cerca de 200 solicitações de teste por minuto.

Para obter mais informações, consulte [sondas de saúde](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Configurações de balanceamento de carga
As configurações de balanceamento de carga para o pool de backend definem como avaliamos os testes de saúde. Essas configurações determinam se o backend é saudável ou insalubre. Eles também verificam como equilibrar o tráfego entre diferentes backends no pool de backend. As seguintes configurações estão disponíveis para configuração de balanceamento de carga:

- **Tamanho da amostra**. Identifica quantas amostras de sondas de saúde precisamos considerar para avaliação de saúde backend.

- **Tamanho da amostra bem-sucedido**. Define o tamanho da amostra como mencionado anteriormente, o número de amostras bem sucedidas necessárias para chamar o backend de saudável. Por exemplo, suponha que um intervalo de teste de saúde da Porta da Frente é de 30 segundos, o tamanho da amostra é de 5 e o tamanho da amostra bem sucedido é de 3. Cada vez que avaliamos as sondas de saúde para o seu backend, olhamos para as últimas cinco amostras ao longo de 150 segundos (5 x 30). Pelo menos três sondas bem sucedidas são necessárias para declarar o backend como saudável.

- **Sensibilidade à latência (latência adicional)**. Define se deseja que a Porta da Frente envie a solicitação para backends dentro do intervalo de sensibilidade à medição de latência ou encaminhe a solicitação para o backend mais próximo.

Para obter mais informações, consulte [O método de roteamento baseado em latência menos latência](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Próximas etapas

- [Crie um perfil da Porta da Frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)