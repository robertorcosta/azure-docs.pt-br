---
title: Adicionar blocos ao seu painel | Microsoft Docs
description: Como um construtor, saiba como configurar o painel padrão do aplicativo IoT Central do Azure.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310293"
---
# <a name="configure-the-application-dashboard"></a>Configurar o painel do aplicativo

O **painel** é a página que é carregada quando os usuários que têm acesso ao aplicativo navegam até a URL do aplicativo. Se você criou seu aplicativo a partir de um dos **modelos de aplicativo**, seu aplicativo terá um painel predefinido para iniciar. Se você criou seu aplicativo do modelo de aplicativo de **aplicativo herdado** , seu painel ficará em branco para iniciar.

> [!NOTE]
> Os usuários podem [criar vários painéis](howto-create-personal-dashboards.md) além do painel de aplicativo padrão. Esses painéis podem ser pessoais somente para o usuário ou compartilhados entre todos os usuários do aplicativo. 

## <a name="add-tiles"></a>Adicionar blocos

A captura de tela a seguir mostra o painel em um aplicativo criado a partir do modelo de **aplicativo personalizado** . Para personalizar o painel padrão para seu aplicativo, selecione **Editar** na parte superior esquerda da página.

> [!div class="mx-imgBorder"]
> ![Painel para aplicativos com base no modelo "exemplo de contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

A seleção de **Editar** abre o painel Biblioteca de painéis. A biblioteca contém os blocos e os primitivos de painel que você pode usar para personalizar o painel.

> [!div class="mx-imgBorder"]
> ![Biblioteca de painéis](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Por exemplo, você pode adicionar um bloco de **telemetria** para a temperatura atual do dispositivo. Para fazer isso:
1. Selecionar um **modelo de dispositivo**
1. Selecione uma **instância de dispositivo** para o dispositivo que você deseja ver em um bloco do Dashboard. Em seguida, você verá uma lista das propriedades do dispositivo que podem ser usadas no bloco.
1. Para criar o bloco no painel, clique em **temperatura** e arraste-o para a área painel. Você também pode clicar na caixa de seleção ao lado de **temperatura** e clicar em **combinar**. A captura de tela a seguir mostra a seleção de um modelo de dispositivo e uma instância de dispositivo, criando um bloco de telemetria de temperatura no painel.
1. Selecione **salvar** na parte superior esquerda para salvar o bloco no painel.

> [!div class="mx-imgBorder"]
> ![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](media/howto-add-tiles-to-your-dashboard/device-details.png)

Agora, quando um operador exibir o painel de aplicativo padrão, ele verá o novo bloco com a **temperatura** para o dispositivo. Cada bloco tem um gráfico pré-selecionado, um gráfico, etc. que será exibido quando o bloco for criado. No entanto, os usuários podem optar por editar e alterar essa visualização. 

> [!div class="mx-imgBorder"]
> ![Guia "Painel" com as configurações e propriedades exibidas para o bloco](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Editar blocos

Para editar um bloco no painel, primeiro clique em **Editar** na parte superior esquerda da página, que abrirá o modo de edição para o painel e todos os seus blocos. 

> [!div class="mx-imgBorder"]
> ![Tela do painel com o modo de edição ativado para um bloco selecionado](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Em seguida, clique no ícone de **engrenagem** no canto superior direito do bloco que você deseja editar. Aqui você pode editar aspectos do bloco, incluindo seu título, sua visualização, agregação, etc.

> [!div class="mx-imgBorder"]
> ![Lista suspensa para configurações de agregação de bloco](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Você também pode alterar a visualização do gráfico clicando no ícone de **régua** no bloco.

> [!div class="mx-imgBorder"]
> ![Lista suspensa para configurações de visualização de bloco](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tipos de bloco

A tabela a seguir resume o uso de blocos no Azure IoT Central:
 
| Bloco | Painel | Descrição
| ----------- | ------- | ------- |
| Conteúdo | Painéis de conjunto de aplicativos e dispositivos |Os blocos com suporte de redução são blocos clicáveis que exibem texto de título e descrição. Você também pode usar esse bloco como um bloco de link para permitir que um usuário navegue para uma URL relacionada ao seu aplicativo.|
| Imagem | Painéis de conjunto de aplicativos e dispositivos |Os blocos de imagem exibem uma imagem personalizada e podem ser clicáveis. Use um bloco de imagem para adicionar elementos gráficos a um painel e, opcionalmente, permitir que um usuário navegue para uma URL relevante para seu aplicativo.|
| Rotular | Painéis de aplicativo |Blocos de rótulo exibem texto personalizado em um painel. Você pode escolher o tamanho do texto. Use um bloco de rótulo para adicionar informações relevantes ao painel, tais descrições, detalhes de contato ou ajuda.|
| Mapeamento | Painéis de aplicativo e dispositivo |Os blocos de mapa exibem o local de um dispositivo em um mapa. Você também pode exibir até 100 pontos do histórico de localização de um dispositivo. Por exemplo, você pode exibir uma rota amostrada de onde um dispositivo esteve na última semana.|
| Gráfico de linhas | Painéis de aplicativo e dispositivo |Os blocos de gráfico de linhas exibem um gráfico de medida de agregação para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de linhas que mostra a temperatura média e a pressão de um dispositivo na última hora.|
| Gráfico de Barras | Painéis de aplicativo e dispositivo |Os blocos de gráfico de barras exibem um gráfico de medidas agregadas para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de barras que mostra a temperatura média e a pressão de um dispositivo na última hora.|
| Gráfico de pizza | Painéis de conjunto de aplicativos e dispositivos |Os blocos de gráfico de pizza exibem um gráfico de medidas agregadas para um dispositivo por um período de tempo.|
| Mapa de Calor | Painéis de conjunto de aplicativos e dispositivos |Os blocos de mapa de calor exibem informações sobre o conjunto de dispositivos, representadas como cores.|
| Histórico de eventos | Painéis de aplicativo e dispositivo |Os blocos de histórico de eventos exibem os eventos de um dispositivo durante um período de tempo. Por exemplo, você pode usá-lo para mostrar todas as alterações de temperatura de um dispositivo durante a última hora.|
| Histórico de Estado | Painéis de aplicativo e dispositivo |Blocos de histórico de estado exibem os valores de medida para um período de tempo. Por exemplo, você pode usá-lo para mostrar os valores de temperatura de um dispositivo durante a última hora.|
| KPI | Painéis de aplicativo e dispositivo | Os blocos de KPI exibem uma telemetria agregada ou uma medida de evento para um período de tempo. Por exemplo, você pode usá-lo para mostrar a temperatura máxima atingida para um dispositivo durante a última hora.|
| Último valor conhecido | Painéis de aplicativo e dispositivo |Os últimos blocos de valor conhecidos exibem o valor mais recente de uma medição de telemetria ou estado. Por exemplo, você pode usar esse bloco para exibir as medidas mais recentes de temperatura, pressão e umidade de um dispositivo.|

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar seu painel de aplicativo padrão do Azure IoT Central, você pode [aprender a criar um painel pessoal](howto-create-personal-dashboards.md).
