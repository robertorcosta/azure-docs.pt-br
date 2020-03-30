---
title: Relacionamentos de âncora e busca de caminhos
description: Conheça o modelo conceitual por trás das relações de âncora. Aprenda a conectar âncoras dentro de um espaço e usar a API próxima para cumprir um cenário de descoberta de caminhos.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270610"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relacionamentos de âncora e busca de caminhos em âncoras espaciais do Azure

Usando relacionamentos de âncora, você pode criar âncoras conectadas em um espaço e, em seguida, fazer perguntas como essas:

* Há âncoras por perto?
* Quão longe eles estão?

## <a name="examples"></a>Exemplos

Você pode usar âncoras conectadas em casos como estes:

* Um trabalhador precisa completar uma tarefa que envolve visitar vários locais em uma fábrica industrial. A fábrica tem âncoras espaciais em cada local. Um HoloLens ou aplicativo móvel ajuda a orientar o trabalhador de um local para o outro. O aplicativo primeiro pede as âncoras espaciais próximas e, em seguida, guia o trabalhador para o próximo local. O aplicativo mostra visualmente a direção geral e a distância até o próximo local.

* Um museu cria âncoras espaciais em exposições públicas. Juntos, essas âncoras formam um passeio de uma hora pelas exposições públicas essenciais do museu. Em uma exposição pública, os visitantes podem abrir o aplicativo de realidade mista do museu em seu dispositivo móvel. Em seguida, eles apontam sua câmera de telefone ao redor do espaço para ver a direção geral e distância para os outros monitores públicos no passeio. À medida que um usuário caminha em direção a uma exibição pública, o aplicativo atualiza a direção e a distância gerais para ajudar a orientar o usuário.

## <a name="set-up-way-finding"></a>Configurar a localização de caminhos

Um aplicativo que usa a direção da linha de visão e a distância entre âncoras para fornecer orientação está usando *a localização de maneiras*. A busca por caminhos é diferente da navegação turn-by-turn. Na navegação turn-by-turn, os usuários são guiados em torno de paredes, através de portas e entre pisos. Com a busca de caminhos, o usuário recebe dicas sobre a direção geral do destino. Mas a inferência ou conhecimento do espaço também ajuda o usuário a navegar pela estrutura até o destino.

Para construir uma experiência de busca de caminhos, primeiro prepare um espaço para a experiência e desenvolva um aplicativo com o qual os usuários interajam. Estes são os passos conceituais:

1. **Planeje o espaço**: Decida quais locais dentro do espaço farão parte da experiência de encontrar caminhos. Em nossos cenários, o supervisor da fábrica ou o coordenador de turismo do museu podem decidir quais locais incluir na experiência de encontrar caminhos.
2. **Conectar âncoras**: Visite os locais escolhidos para criar âncoras espaciais. Você pode fazer isso em um modo de admin do aplicativo do usuário final ou em um aplicativo totalmente diferente. Você vai conectar ou relacionar cada âncora com as outras. O serviço mantém essas relações.
3. **Inicie a experiência do usuário final**: Os usuários executam o aplicativo para localizar uma âncora, que pode estar em qualquer um dos locais escolhidos. Seu design geral deve determinar os locais onde os usuários podem inserir a experiência.
4. **Encontre âncoras próximas**: Depois que o usuário encontrar uma âncora, o aplicativo pode solicitar âncoras próximas. Este procedimento retorna uma pose entre o dispositivo e essas âncoras.
5. **Orientar o usuário**: O aplicativo pode usar a pose para cada uma dessas âncoras para orientar sobre a direção geral e distância do usuário. Por exemplo, o feed da câmera no aplicativo pode mostrar um ícone e uma seta para representar cada destino potencial, como mostra a imagem a seguir.
6. **Refinar a orientação**: À medida que o usuário caminha, o aplicativo pode calcular periodicamente uma nova pose entre o dispositivo e a âncora de destino. O aplicativo continua a refinar as dicas de orientação que ajudam o usuário a chegar ao destino.

    ![Um exemplo de como um aplicativo pode mostrar orientação de encontrar caminhos](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Conectar âncoras

Para construir uma experiência de busca de caminhos, primeiro você precisa colocar as âncoras nos locais escolhidos. Nesta seção, assumiremos que o admin do aplicativo já terminou este trabalho.

### <a name="connect-anchors-in-a-single-session"></a>Conecte âncoras em uma única sessão

Para conectar âncoras:

1. Caminhe até o primeiro local e crie o Anchor A usando uma CloudSpatialAnchorSession.
2. Caminhe até o segundo local. A plataforma MR/AR subjacente rastreia o movimento.
3. Crie o Anchor B usando a mesma CloudSpatialAnchorSession. As âncoras A e B estão agora conectadas. O serviço de Âncoras Espaciais mantém essa relação.
4. Continue o procedimento para as âncoras restantes.

### <a name="connect-anchors-in-multiple-sessions"></a>Conecte âncoras em várias sessões

Você pode conectar âncoras espaciais ao longo de várias sessões. Usando este método, você pode criar e conectar algumas âncoras de uma só vez e, em seguida, criar e conectar mais âncoras.

Para conectar âncoras ao longo de várias sessões:

1. O aplicativo cria algumas âncoras em uma CloudSpatialAnchorSession.
2. Em um momento diferente, o aplicativo localiza uma dessas âncoras (por exemplo, Anchor A) usando uma nova CloudSpatialAnchorSession.
3. Caminhe até um novo local. A plataforma de realidade mista ou realidade aumentada subjacente acompanha o movimento.
4. Crie o Anchor C usando a mesma CloudSpatialAnchorSession. As âncoras A, B e C estão agora conectadas. O serviço de Âncoras Espaciais mantém essa relação.

Você pode continuar este procedimento para mais âncoras e mais sessões ao longo do tempo.

### <a name="verify-anchor-connections"></a>Verifique conexões de âncora

O aplicativo pode verificar se duas âncoras estão conectadas emitindo uma consulta para âncoras próximas. Quando o resultado da consulta contém a âncora de destino, a conexão âncora é verificada. Se as âncoras não estão conectadas, o aplicativo pode tentar conectá-las novamente.

Aqui estão algumas razões pelas quais as âncoras podem não se conectar:

* A plataforma de realidade mista ou realidade aumentada subjacente perdeu o rastreamento durante o processo de conexão de âncoras.
* Devido a um erro de rede durante a comunicação com o serviço De âncoras Espaciais, a conexão de âncora não pôde ser persistida.

### <a name="find-sample-code"></a>Encontre o código de exemplo

Para encontrar um código de exemplo que mostre como conectar âncoras e fazer consultas próximas, consulte [aplicativos de amostra de âncoras espaciais](https://github.com/Azure/azure-spatial-anchors-samples).
