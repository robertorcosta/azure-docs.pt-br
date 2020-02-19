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
ms.openlocfilehash: 80204b6f156981ab3ecb8f348f3ce7ea077a6836
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443526"
---
# <a name="class-types-overview---azure-lab-services"></a>Visão geral dos tipos de classes – Azure Lab Services

O Azure Lab Services permite que você configure rapidamente ambientes de laboratório de sala de aula na nuvem. Os artigos desta seção fornecem diretrizes sobre como configurar vários tipos de laboratórios de sala de aula usando o Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Aprendizado profundo no processamento em idioma natural

Configure um laboratório voltado para o aprendizado profundo em NLP (processamento em idioma natural) usando o Azure Lab Services. O NLP (processamento de idioma natural) é uma forma de IA (inteligência artificial) que habilita os computadores com tradução, reconhecimento de fala e outras funcionalidades de reconhecimento vocal. Os alunos que fazem uma aula de NLP obtêm uma VM (máquina virtual) do Linux para saber como aplicar algoritmos de rede neural para desenvolver modelos de aprendizado profundo que são usados para analisar a linguagem humana escrita.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório voltado para o aprendizado profundo no processamento de linguagem natural usando Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Script de Shell no Linux

Configure um laboratório para ensinar o script de shell no Linux. O script é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de exemplo, a classe abrange scripts do Bash tradicionais e scripts avançados. Scripts avançados são scripts que combinam comandos do Bash e o Ruby. Essa abordagem permite que o Ruby passe os dados e que os comandos do Bash interajam com o shell.

Os alunos que fazem essas aulas de script obtêm uma máquina virtual do Linux para aprender os conceitos básicos do Linux e também se familiarizarem com os scripts do shell Bash. A máquina virtual do Linux é fornecida com o acesso à Área de Trabalho Remota habilitado e com os editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [shell scripting no Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Hacking ético

Você pode configurar um laboratório para uma classe que se concentra no lado da perícia de hacking ético. O teste de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta obter acesso ao sistema ou rede para demonstrar vulnerabilidades que um invasor mal-intencionado pode explorar.

Em uma classe de hacking ético, os alunos podem aprender técnicas modernas para se defenderem de vulnerabilidades. Cada aluno recebe uma máquina virtual de host do Windows Server com duas máquinas virtuais aninhadas – uma com uma imagem do [Metasploitable3](https://github.com/rapid7/metasploitable3) e outra com uma imagem do [Kali Linux](https://www.kali.org/). A máquina virtual com Metasploitable é usada para fins de exploração.  A máquina virtual com Kali Linux fornece acesso às ferramentas necessárias para executar tarefas forenses.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para ensinar a aula ética de hackers](class-type-ethical-hacking.md).

## <a name="database-management"></a>Gerenciamento de banco de dados
Os conceitos de bancos de dados são um dos cursos introdutórios ensinados na maioria dos departamentos de ciência da computação na faculdade. Você pode configurar um laboratório para uma classe de gerenciamento de bancos de dados básico no Azure Lab Services. Por exemplo, você pode configurar um modelo de máquina virtual em um laboratório com um servidor de banco de dados [MySQL](https://www.mysql.com/) ou um servidor [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) .

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para ensinar o gerenciamento de bancos de dados relacionais](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Notebooks Python e Jupyter
Você pode configurar um computador de modelo no Azure Lab Services com as ferramentas necessárias para ensinar os alunos a usar [notebooks Jupyter](http://jupyter-notebook.readthedocs.io). O Jupyter notebooks é um projeto de código-fonte aberto que permite combinar facilmente o código-fonte de Rich Text [e executável em](https://www.python.org/) uma única tela chamada notebook. A execução de um bloco de anotações resulta em um registro linear de entradas e saídas.  Essas saídas podem incluir texto, tabelas de informações, gráficos de dispersão e muito mais.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para ensinar ciência de dados com notebooks Python e Jupyter](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Desenvolvimento de aplicativos móveis com o Android Studio
Você pode configurar um laboratório no Azure Lab Services para ensinar uma classe introdutória de desenvolvimento de aplicativos móveis. Essa classe se concentra em aplicativos móveis Android que podem ser publicados no [Google Play Store](https://play.google.com/store/apps).  Os alunos aprendem a usar [Android Studio](https://developer.android.com/studio) para criar aplicativos.  O [emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) é usado para testar o aplicativo localmente.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para ensinar o desenvolvimento de aplicativos móveis com o Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Configurar um laboratório voltado para o aprendizado profundo em processamento em idioma natural usando o Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Script de shell no Linux](class-type-shell-scripting-linux.md)
- [Hacking ético](class-type-ethical-hacking.md)
