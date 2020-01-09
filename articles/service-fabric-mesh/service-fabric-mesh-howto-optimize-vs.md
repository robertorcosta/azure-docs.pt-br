---
title: Otimizar o Visual Studio para o Azure Service Fabric malha
description: Esse artigo mostra como otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric para que sua primeira execução de depuração (F5) seja muito mais rápida.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497986"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric

Esse artigo mostra como otimizar o desempenho do Visual Studio para projetos da malha do Service Fabric para que sua primeira execução de depuração (F5) seja muito mais rápida.  

## <a name="change-visual-studio-settings"></a>Alterar configurações do Visual Studio
 
No Visual Studio,em **Ferramentas** > **Opções**  > **Ferramentas da malha do Service Fabric** > **Geral**, é possível ajustar as configurações a seguir:

- **Extrair imagens necessárias do Docker na abertura do projeto** torna sua primeira execução de depuração (F5) mais rápida iniciando o processo de download de imagem durante o carregamento do projeto.  
- **Implantar o aplicativo na abertura do projeto** pode tornar sua primeira execução de depuração (F5) mais rápida iniciando o processo de implantação assim que o projeto é aberto.  
- **Remover o aplicativo no fechamento do projeto** recupera recursos (CPU, RAM) alocados para o aplicativo removendo o aplicativo de malha quando o projeto é fechado.  

Quando você vê mensagens na janela de saída das Ferramentas do Service Fabric que o Visual Studio está 'extraindo imagens', 'aquecendo' ou 'removendo o aplicativo', elas se referem às configurações acima. Você pode desativar essas configurações.

## <a name="next-steps"></a>Próximos passos

Leia o [Tutorial de Depurar um aplicativo de malha](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)