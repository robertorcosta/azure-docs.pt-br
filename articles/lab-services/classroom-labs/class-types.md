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
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 27619a69a1f7fbded8ce6430afc2b8e9a8b4a00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296720"
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

Em uma classe de hacking ético, os alunos podem aprender técnicas modernas para se defenderem de vulnerabilidades. Cada aluno recebe uma máquina virtual de host do Windows Server com duas máquinas virtuais aninhadas – uma com uma imagem do [Metasploitable3](https://github.com/rapid7/metasploitable3) e outra com uma imagem do [Kali Linux](https://www.kali.org/). A máquina virtual com Metasploitable é usada para fins de exploração.  A máquina virtual com Kali Linux fornece acesso às ferramentas necessárias para executar tarefas forenses.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar uma classe de hacking ético](class-type-ethical-hacking.md).

## <a name="database-management"></a>Gerenciamento de banco de dados
Os conceitos de bancos de dados são um dos cursos introdutórios ensinados na maioria dos departamentos de ciência da computação na faculdade. Você pode configurar um laboratório para uma classe básica de gerenciamento de bancos de dados nos Azure Lab Services. Por exemplo, você pode configurar um modelo de máquina virtual em um laboratório com um servidor de banco de dados [MySQL](https://www.mysql.com/) ou um servidor [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019).

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar gerenciamento de banco de dados para bancos de dados relacionais](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Python e Jupyter Notebooks
Você pode configurar uma máquina de modelo nos Serviços de Laboratório do Azure com as ferramentas necessárias para ensinar aos alunos como usar os [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io). O Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código-fonte executável [Python](https://www.python.org/) em uma única tela chamada notebook. A execução de um notebook resulta em um registro linear de entradas e saídas.  Essas saídas podem incluir texto, tabelas de informações, gráficos de dispersão e muito mais.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar ciência de dados com Python e Jupyter Notebooks](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Desenvolvimento de aplicativos móveis com o Android Studio
Você pode configurar um laboratório nos Serviços de Laboratório do Azure para ministrar uma aula introdutória de desenvolvimento de aplicativos móveis. Esta classe se concentra nos aplicativos para dispositivos móveis Android que podem ser publicados na [Google Play Store](https://play.google.com/store/apps).  Os alunos aprendem como usar o [Android Studio](https://developer.android.com/studio) para criar aplicativos.  [O emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) é usado para testar o aplicativo localmente.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar o desenvolvimento de aplicativos móveis com o Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Configurar um laboratório voltado para o aprendizado profundo em processamento em idioma natural usando o Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Script de Shell no Linux](class-type-shell-scripting-linux.md)
- [Hacking ético](class-type-ethical-hacking.md)