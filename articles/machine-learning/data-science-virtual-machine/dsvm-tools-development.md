---
title: Ferramentas de desenvolvimento
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e ambientes de desenvolvimento integrado disponíveis na Máquina Virtual de Data Science.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282674"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na Máquina Virtual de Data Science do Azure

A Máquina Virtual de Ciência de Dados (DSVM) reúne várias ferramentas populares em um ambiente de desenvolvimento integrado altamente produtivo (IDE). Aqui estão algumas ferramentas oferecidas na DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de propósito geral      |
| Versões DSVM suportadas      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Usos típicos      | Desenvolvimento de software    |
| Como ele está configurado e instalado no DSVM?      | Carga de Trabalho de Ciência de Dados (ferramentas Python e R), carga de trabalho do Azure (Hadoop, Data Lake), Node.js, ferramentas do SQL Server, [Azure Machine Learning para o Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usá-lo e executá-lo      | Atalho de`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`área de trabalho (). Graficamente, abra o Visual Studio usando o ícone da área de trabalho ou o menu **Iniciar.** Pesquise programas (tecla do logotipo do Windows + S), seguido de **Visual Studio**. Nesse local, você pode criar projetos em linguagens como C#, Python, R e Node.js.   |
| Ferramentas relacionadas no DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Você poderá receber uma mensagem informando que o período de avaliação expirou. Insira suas credenciais de conta da Microsoft. Ou crie uma nova conta gratuita para obter acesso ao Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de propósito geral      |
| Versões DSVM suportadas      | Windows, Linux     |
| Usos típicos      | Editor de código e a integração de Git   |
| Como usá-lo e executá-lo      | Atalho de`C:\Program Files (x86)\Microsoft VS Code\Code.exe`área de trabalho (`code`) no Windows, atalho de desktop ou terminal ( ) no Linux    |
| Ferramentas relacionadas no DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| O que é?   | IDE do cliente para linguagem R   |
| Versões DSVM suportadas      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento em R     |
| Como usá-lo e executá-lo      | Atalho de`C:\Program Files\RStudio\bin\rstudio.exe`área de trabalho`/usr/bin/rstudio`( ) no Windows, atalho de desktop ( ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| O que é?   | IDE do cliente para linguagem R   |
| O que é?   | IDE baseado na Web para R    |
| Versões DSVM suportadas      | Linux      |
| Usos típicos      |  Desenvolvimento em R     |
| Como usá-lo e executá-lo      | Habilite o serviço com _o systemctl habilitar o servidor rstudio_e, em seguida, inicie o serviço com _o rstudio-server de início de sistemactl_. Em seguida, faça login no\/RStudio Server em http: /your-vm-ip:8787.       |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE do cliente para a linguagem Julia   |
| Versões DSVM suportadas      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento em Julia     |
| Como usá-lo e executá-lo      | Atalho de`C:\JuliaPro-0.5.1.1\Juno.bat`área de trabalho`/opt/JuliaPro-VERSION/Juno`( ) no Windows, atalho de desktop ( ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | IDE do cliente para a linguagem Python    |
| Versões DSVM suportadas      | Windows 2019, Linux      |
| Usos típicos      |  Desenvolvimento em Python     |
| Como usá-lo e executá-lo      | Atalho de`C:\Program Files\tk`área de trabalho ( ) no Windows. Atalho de`/usr/bin/pycharm`área de trabalho ( ) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
