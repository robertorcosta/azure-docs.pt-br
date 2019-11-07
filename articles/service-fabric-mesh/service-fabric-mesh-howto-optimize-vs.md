---
title: Otimizar o desempenho do Visual Studio para projetos da malha do Azure Service Fabric | Microsoft Docs
description: Esse artigo mostra como otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric para que sua primeira execução de depuração (F5) seja muito mais rápida.
services: service-fabric-mesh
keywords: otimizar o desempenho de depuração
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 04aeaa6b008b50789f4380e4bb98beba3957c2e9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663426"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric

Esse artigo mostra como otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric para que sua primeira execução de depuração (F5) seja muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar configurações do Visual Studio
 
No Visual Studio,em **Ferramentas** > **Opções**  > **Ferramentas da malha do Service Fabric** > **Geral**, é possível ajustar as configurações a seguir:

- **Extrair imagens necessárias do Docker na abertura do projeto** torna sua primeira execução de depuração (F5) mais rápida iniciando o processo de download de imagem durante o carregamento do projeto.  
- **Implantar o aplicativo na abertura do projeto** pode tornar sua primeira execução de depuração (F5) mais rápida iniciando o processo de implantação assim que o projeto é aberto.  
- **Remover o aplicativo no fechamento do projeto** recupera recursos (CPU, RAM) alocados para o aplicativo removendo o aplicativo de malha quando o projeto é fechado.  

Quando você vê mensagens na janela de saída das Ferramentas do Service Fabric que o Visual Studio está 'extraindo imagens', 'aquecendo' ou 'removendo o aplicativo', elas se referem às configurações acima. Você pode desativar essas configurações.

## <a name="next-steps"></a>Próximas etapas

Leia o [Tutorial de Depurar um aplicativo de malha](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)