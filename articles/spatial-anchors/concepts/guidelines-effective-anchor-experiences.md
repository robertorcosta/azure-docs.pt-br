---
title: Diretrizes para experiências de âncora eficazes
description: Diretrizes e considerações para criar e localizar âncoras efetivamente usando âncoras espaciais do Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270515"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Crie uma experiência de âncora eficaz usando âncoras espaciais do Azure

Este artigo fornece diretrizes e considerações para ajudá-lo a criar e localizar âncoras efetivamente usando âncoras espaciais.

## <a name="good-anchors"></a>Boas âncoras

Âncoras espaciais ajudam a criar boas âncoras. É importante investir tempo para educar ou orientar os usuários na sua experiência de usuário (UX) para criar boas âncoras. Ao investir na criação de boas âncoras na frente, você ajuda os usuários finais a encontrar âncoras de forma confiável:

- Em diferentes dispositivos.
- Em vários momentos.
- Em diferentes condições de iluminação.
- Das perspectivas desejadas dentro do espaço.

## <a name="static-and-dynamic-locations"></a>Locais estáticos e dinâmicos

Parte da concepção da experiência de âncora é escolher os locais. Os locais serão estáticos e definidos por um administrador do espaço? Ou serão dinâmicos e definidos pelo usuário?

Um gerente de loja de varejo pode querer uma experiência estática na loja para atrair os usuários a visitar. Mas o desenvolvedor de um jogo de tabuleiro de realidade mista provavelmente gostaria de deixar os usuários escolherem onde jogar.

Para locais estáticos, você pode ensinar os administradores a gastar tempo curando o espaço com boas âncoras.

Para locais dinâmicos, você deve pensar em como ensinar ou orientar os usuários em seu UX para criar boas âncoras.

## <a name="stable-visual-features"></a>Características visuais estáveis

Os sistemas de rastreamento visual usados em dispositivos de realidade mista e realidade aumentada dependem de recursos visuais do ambiente. Para obter a experiência mais confiável:

- *Crie* âncoras em locais com características visuais estáveis (ou seja, recursos que não mudam com frequência).

- *Não* crie âncoras em grandes superfícies em branco que não tenham características distintas.

- *Não* crie âncoras em materiais altamente reflexivos.

- *Não* crie âncoras em superfícies onde o padrão se repete, como tapete ou papel de parede.

![Exemplos de um bom ambiente para âncoras e um ambiente ruim para âncoras](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Várias perspectivas de visualização

Ao criar uma âncora, pense nas pessoas que mais tarde tentarão localizar a âncora.

Considere, por exemplo, uma âncora no meio de uma sala que tem duas portas. Você provavelmente quer permitir que os usuários entrem na sala de qualquer porta. Ao criar a âncora, você precisará escanear sua posição de ambas as portas. Você altera perspectivas para capturar dados do ambiente ao redor da âncora para que os usuários possam localizar a âncora de qualquer porta.

Em geral, ao criar uma âncora, escaneie-a a partir das perspectivas das pessoas que tentarão localizá-la. Então, se você está colocando conteúdo virtual em uma escultura ao ar livre, faz sentido andar ao redor da escultura, enquanto digitaliza, enquanto você cria a âncora. Se sua âncora está no canto de uma sala, só há uma direção para se aproximar dela. Ao criar esta âncora, você pode escaneá-la apenas a partir desta perspectiva.

## <a name="multiple-anchors"></a>Múltiplas âncoras

A iluminação pode fazer a diferença nos recursos visuais que um aplicativo detecta. Âncoras criadas em luz natural forte podem ser difíceis de localizar em luz artificial, e vice-versa.

Se você tem esse problema, ele pode ajudar a criar duas âncoras. No mesmo local, crie uma âncora à luz do dia e outra em luz artificial. Seu aplicativo pode então consultar as duas âncoras. Quando uma das âncoras estiver localizada, o aplicativo terá uma pose para a âncora.

Da mesma forma, em ambientes onde os recursos visuais mudam porque a maioria dos objetos se move, várias âncoras podem ajudar. Quando uma âncora se torna muito difícil de encontrar por causa de mudanças significativas no ambiente, você pode substituir a âncora por uma nova. Você pode fazer isso, por exemplo, em uma loja de varejo onde o layout é atualizado a cada poucos meses.

## <a name="targets-and-rooms"></a>Alvos e quartos

Em muitos casos, uma âncora é um ponto de entrada para a experiência do seu aplicativo. Você vai querer passar por esta etapa de forma rápida e confiável para que os usuários possam entrar em sua experiência. Gastar tempo em como os usuários encontrarão suas âncoras é um passo importante para o design. É útil pensar em encontrar âncoras em termos de dois cenários amplos: *alvos* e *salas.*

### <a name="targets"></a>Destinos

No cenário alvo, a localização de uma âncora é bem conhecida. Por exemplo, em um aplicativo fictício de pintura de realidade mista, um usuário coloca uma tela virtual na parede. Ela instrui os outros usuários na sala a apontar seus dispositivos para o mesmo lugar na parede para localizar a âncora e começar a experiência.

Outro exemplo de um cenário-alvo pode ser um sinal em uma cafeteria que diz: "Procure por negócios". A cafeteria colocou uma âncora aqui. À medida que os usuários escaneiam o sinal, eles localizam a âncora e entram na experiência de realidade aumentada para encontrar ofertas no café.

No cenário alvo, fotos podem ajudar. Se você mostrar aos usuários uma foto do alvo pretendido em seu dispositivo, eles podem identificar rapidamente o que fazer a varredura no mundo real. Por exemplo, você pode ajudar seus usuários a chegar dentro da área geral de um alvo pretendido usando GPS. Quando o usuário chega, seu aplicativo mostra uma foto do alvo. O usuário olha ao redor do espaço, encontra o alvo e verifica a âncora.

![Ilustração de uma âncora, mostrando uma foto do alvo no dispositivo móvel de um usuário](./media/start-here-edit.png)

### <a name="rooms"></a>Quartos

No cenário da sala, os usuários entram em um espaço simplesmente sabendo que há uma âncora aqui em algum lugar. Os usuários escaneiam o espaço com seu dispositivo e localizam rapidamente a âncora.

Essa experiência normalmente exige que você crie âncoras bem curados, como discutido em Várias perspectivas de visualização. Se você escaneou a sala de muitas perspectivas quando criou a âncora, os usuários podem digitalizar quase em qualquer lugar quando tentarem localizá-la.

![Ilustração de como um usuário pode escanear uma sala para encontrar uma âncora](./media/scan-room.png)

Essencialmente, você gasta mais tempo digitalizando o espaço quando cria a âncora para que os usuários posteriores possam digitalizar e localizar a âncora rapidamente. À medida que você cria sua experiência, você precisará considerar esta importante troca.

O exemplo do aplicativo de pintura de realidade mista que discutimos anteriormente não funciona bem como um cenário de sala. Aqui, o usuário que coloca a âncora quer que outros participem da experiência rapidamente. Os usuários não querem esperar para começar a experiência até que o quarto esteja bem digitalizado. Como todos os usuários sabem exatamente onde apontar seu dispositivo para localizar as âncoras, este exemplo funciona melhor como um cenário de destino.

## <a name="anchor-location"></a>Localização da âncora

Os sistemas de rastreamento visual dependem dos recursos visuais em um ambiente. Quanto mais recursos visuais uma varredura inclui, maior a probabilidade de encontrar uma âncora.

Siga as diretrizes gerais nesta seção para construir um UX que incentive uma varredura útil do ambiente.

Primeiro, se o usuário não localizar uma âncora em poucos segundos, o aplicativo deve encorajar os usuários a mover o dispositivo para capturar mais perspectivas. O aplicativo também pode incentivar os usuários a se moverem pelo ambiente para procurar a âncora de mais perspectivas. Quanto mais perspectivas de recurso o dispositivo ver, melhor.

Para cenários-alvo, peça ao usuário para se mover ao redor do alvo para visualizá-lo de diferentes perspectivas. Em outras palavras, peça ao usuário para capturar o alvo a partir de novas perspectivas até que a âncora esteja localizada.

Para cenários de quarto, peça ao usuário para escanear lentamente a sala. Por exemplo, peça ao usuário para se virar para capturar 180 graus ou até 360 graus da sala. Ou peça ao usuário para ver a sala de uma nova perspectiva.

O método mais significativo é escanear pela sala. Uma varredura em toda a sala captura mais características visuais do ambiente do que uma varredura de uma parede próxima, por exemplo. Uma varredura de uma parede próxima não capturará tantas características visuais úteis do ambiente.

Não é útil mover repetidamente o dispositivo de um lado para o outro quando se procura uma âncora. Isso simplesmente captura os mesmos pontos da mesma perspectiva.

## <a name="experience-tests"></a>Testes de experiência

Neste artigo, discutimos diretrizes gerais. Com âncoras espaciais, você está escrevendo aplicativos que interagem com o mundo real. Por causa disso, você deve dedicar tempo para testar os cenários âncoras do seu aplicativo em ambientes reais. Isso é especialmente verdadeiro para ambientes que representam onde você espera que seus usuários usem o aplicativo.
