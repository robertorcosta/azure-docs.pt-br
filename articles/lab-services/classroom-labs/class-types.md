---
title: Tipos de classe de exemplo no Azure Lab Services | Microsoft Docs
description: Fornece alguns tipos de classes para os quais você pode configurar laboratórios usando o Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0e8b203240b4b2d6b67534ab52a7cd4ccf5df571
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976593"
---
# <a name="class-types-overview---azure-lab-services"></a>Visão geral dos tipos de classes – Azure Lab Services
O Azure Lab Services permite que você configure rapidamente ambientes de laboratório de sala de aula na nuvem. Os artigos desta seção fornecem diretrizes sobre como configurar vários tipos de laboratórios de sala de aula usando o Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Aprendizado profundo no processamento em idioma natural
Configure um laboratório voltado para o aprendizado profundo em NLP (processamento em idioma natural) usando o Azure Lab Services. O NLP (processamento de idioma natural) é uma forma de IA (inteligência artificial) que habilita os computadores com tradução, reconhecimento de fala e outras funcionalidades de reconhecimento vocal. Os alunos que fazem uma aula de NLP obtêm uma VM (máquina virtual) do Linux para saber como aplicar algoritmos de rede neural para desenvolver modelos de aprendizado profundo que são usados para analisar a linguagem humana escrita. 

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório voltado para o aprendizado profundo no processamento em idioma natural usando o Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Script de Shell no Linux
Configure um laboratório para ensinar o script de shell no Linux. O script é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de exemplo, a classe abrange scripts do Bash tradicionais e scripts avançados. Scripts avançados são scripts que combinam comandos do Bash e o Ruby. Essa abordagem permite que o Ruby passe os dados e que os comandos do Bash interajam com o shell. 

Os alunos que fazem essas aulas de script obtêm uma máquina virtual do Linux para aprender os conceitos básicos do Linux e também se familiarizarem com os scripts do shell Bash. A máquina virtual do Linux é fornecida com o acesso à Área de Trabalho Remota habilitado e com os editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Script de shell no Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Hacking ético 
Você pode configurar um laboratório para uma classe que se concentra no lado da perícia de hacking ético. O teste de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta obter acesso ao sistema ou rede para demonstrar vulnerabilidades que um invasor mal-intencionado pode explorar. 

Em uma classe de hacking ético, os alunos podem aprender técnicas modernas para se defenderem de vulnerabilidades. Cada aluno obtém uma máquina virtual de host do Windows Server que tem duas máquinas virtuais aninhadas – uma máquina virtual com uma imagem **Metaspoiltable** e outra máquina virtual com uma imagem [Kali Linux](https://www.kali.org/). A máquina virtual Metasploitable é usada para fins de exploração e a máquina virtual Kali fornece acesso às ferramentas necessárias para executar tarefas forenses.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar uma classe de hacking ético](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos: 

- [Configurar um laboratório voltado para o aprendizado profundo em processamento em idioma natural usando o Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Script de shell no Linux](class-type-shell-scripting-linux.md)