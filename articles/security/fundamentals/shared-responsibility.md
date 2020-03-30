---
title: Responsabilidade compartilhada na nuvem - Microsoft Azure
description: Entenda o modelo de responsabilidade compartilhada e quais tarefas de segurança são tratadas pelo provedor de nuvem e quais tarefas são tratadas por você.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518363"
---
# <a name="shared-responsibility-in-the-cloud"></a>Responsabilidade compartilhada na nuvem

Ao considerar e avaliar os serviços de nuvem pública, é fundamental entender o modelo de responsabilidade compartilhada e quais tarefas de segurança são tratadas pelo provedor de nuvem e quais tarefas são tratadas por você. As responsabilidades de carga de trabalho variam dependendo se a carga de trabalho está hospedada no Software as a Service (SaaS), Plataforma como serviço (PaaS), Infra-estrutura como serviço (IaaS) ou em um datacenter local

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
Em um datacenter no local, você é dono de toda a pilha. À medida que você se move para a nuvem, algumas responsabilidades são transferidas para a Microsoft. O diagrama a seguir ilustra as áreas de responsabilidade entre você e a Microsoft, de acordo com o tipo de implantação da sua pilha.

![Zonas de responsabilidade](./media/shared-responsibility/shared-responsibility.png)

Para todos os tipos de implantação de nuvem, você possui seus dados e identidades. Você é responsável por proteger a segurança de seus dados e identidades, dos recursos locais e dos componentes da nuvem que você controla (que varia por tipo de serviço).

Independentemente do tipo de implantação, as seguintes responsabilidades são sempre retidas por você:

- Dados
- Pontos de extremidade
- Conta
- Gerenciamento de acesso

## <a name="cloud-security-advantages"></a>Vantagens da segurança na nuvem
A nuvem oferece vantagens significativas para resolver desafios de segurança da informação de longa data. Em um ambiente local, as organizações provavelmente têm responsabilidades não atendidas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente em que os invasores podem explorar vulnerabilidades em todas as camadas.

O diagrama a seguir mostra uma abordagem tradicional onde muitas responsabilidades de segurança não são cumpridas devido a recursos limitados. Na abordagem habilitada para nuvem, você é capaz de transferir responsabilidades de segurança do dia a dia para o seu provedor de nuvem e realocar seus recursos.

![Vantagens de segurança da era da nuvem](./media/shared-responsibility/cloud-enabled-security.png)

Na abordagem habilitada para nuvem, você também é capaz de aproveitar os recursos de segurança baseados em nuvem para obter mais eficácia e usar inteligência na nuvem para melhorar seu tempo de detecção e resposta de ameaças. Ao deslocar responsabilidades para o provedor de nuvem, as organizações podem obter mais cobertura de segurança, que permite que elas realocar recursos de segurança e orçamento para outras prioridades de negócios.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a divisão de responsabilidade entre você e a Microsoft em uma implantação SaaS, PaaS e IaaS, consulte [responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
