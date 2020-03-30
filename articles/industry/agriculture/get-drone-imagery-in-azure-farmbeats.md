---
title: Obter imagens do drone
description: Este artigo descreve como obter imagens de drones de parceiros.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132046"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Obtenha imagens de drones de parceiros de drones

Este artigo descreve como você pode trazer dados ortomosaicos de seus parceiros de imagens de drones para o Azure FarmBeats Datahub. Um ortomosaico é uma ilustração aérea ou imagem que é geométricamente corrigida e costurada a partir de dados coletados por um drone.

Atualmente, os seguintes parceiros de imagens são suportados.

  ![Parceiros de imagens de drones da FarmBeats](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

A integração de dados de imagens de drones com o Azure FarmBeats ajuda você a obter dados ortomosaicos dos voos de drones que você conduz em sua fazenda no datahub. Depois que os dados estão disponíveis, você pode visualizá-los no FarmBeats Accelerator. Os dados podem ser usados para fusão de dados e inteligência artificial e construção de modelos de machine learning.

## <a name="before-you-begin"></a>Antes de começar

  - Certifique-se de que você instalou o Azure FarmBeats. Para obter informações sobre como instalar o FarmBeats, consulte [Instalar OZure FarmBeats](install-azure-farmbeats.md).
  - Certifique-se de que você tem a fazenda para a qual você quer imagens de drone definidas em seu sistema FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Habilite a integração de imagens de drones com o FarmBeats

Forneça as seguintes informações ao provedor de dispositivos para permitir a integração com o FarmBeats:
 - Ponto de extremidade de API
 - ID do locatário
 - ID do Cliente
 - Segredo do cliente

Siga estas etapas.

1. Baixe este [script](https://aka.ms/farmbeatspartnerscript)e extraia-o para sua unidade local. Dois arquivos estão dentro do arquivo zip.
2. Entre no [portal do Azure](https://portal.azure.com/) e abra o Azure Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal.

    ![Abra o Azure Cloud Shell na barra superior direita do portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Certifique-se de que o ambiente está definido como **PowerShell**.

    ![Configuração do PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Faça upload dos dois arquivos que você baixou da etapa 1 na sua instância Cloud Shell.

    ![Carregar arquivos](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Vá para o diretório onde os arquivos foram carregados. Por padrão, eles são carregados para o diretório inicial o nome de usuário.
6. Execute o seguinte script:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Siga as instruções na tela para capturar os valores de API Endpoint, ID do inquilino, ID do cliente, Client Secret e EventHub Connection String.

    Depois de inserir as credenciais necessárias no sistema de software de drones do parceiro, você pode importar todas as fazendas do sistema FarmBeats. Então você pode usar os detalhes da fazenda para fazer o seu planejamento de trajeto de voo e coleta de imagens de drones.

    Depois que as imagens brutas são processadas pelo software dos provedores de drones, o sistema de software de drone carrega o ortomosaico costurado e outras imagens processadas no datahub.

## <a name="view-drone-imagery"></a>Ver imagens de drones

Depois que os dados são enviados para o datahub FarmBeats, você pode consultar o Scene Store usando APIs do FarmBeats Datahub.

Alternativamente, você pode ver a última imagem de drone na página Detalhes da **Fazenda.** Para ver a imagem, siga os passos.

1. Selecione a fazenda para a qual suas imagens foram enviadas. A página de detalhes **da Fazenda** é exibida.
2. Desça até a seção mais recente **do Precision Maps.**
3. Veja a imagem na seção **Imagens de Drone.**

    ![Seção de Imagens de Drones](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Baixe imagens de drones

Quando você seleciona a seção Imagens de Drone, um pop-up é aberto para mostrar uma imagem de alta resolução do ortomosaico do drone.

![Ortomosaico de alta resolução](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Veja todos os mapas de drones

Arquivos e imagens enviados pelo provedor de drones aparecem na seção **Mapas.** Selecione a seção **Mapas,** filtre por **Farm**e selecione os arquivos apropriados para exibir e baixar.

  ![Seção Mapas](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Próximas etapas

Aprenda a usar as [APIs](rest-api-in-azure-farmbeats.md) do FarmBeats Datahub para obter imagens de drone.
