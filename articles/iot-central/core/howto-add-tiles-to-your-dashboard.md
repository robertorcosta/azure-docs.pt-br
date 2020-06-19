---
title: Adicionar blocos ao seu painel | Microsoft Docs
description: Como construtor, saiba como configurar o painel de aplicativo padrão do Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659003"
---
# <a name="configure-the-application-dashboard"></a>Configurar o painel de aplicativo

O **Painel** é a página que é carregada quando os usuários que têm acesso ao aplicativo navegam até a URL do aplicativo. Se você criou seu aplicativo a partir de um dos **Modelos de Aplicativo**, seu aplicativo terá um painel predefinido para iniciar. Se você criou seu aplicativo a partir de um modelo de **Aplicativo personalizado**, seu painel mostrará algumas dicas sobre como começar.

> [!NOTE]
> Os usuários podem [criar vários painéis](howto-create-personal-dashboards.md) além do painel de aplicativo padrão. Esses painéis podem ser pessoais somente para o usuário ou compartilhados entre todos os usuários do aplicativo.  

## <a name="add-tiles"></a>Adicionar blocos

A captura de tela a seguir mostra o painel em um aplicativo criado com base no modelo de **Aplicativo Personalizado**. Para personalizar o painel padrão para seu aplicativo, selecione **Editar** na parte superior esquerda da página.

> [!div class="mx-imgBorder"]
> ![Painel para aplicativos com base no modelo “Exemplo Contoso”](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Selecionar **Editar** abre o painel da biblioteca. A biblioteca contém os blocos e os primitivos de painel que você pode usar para personalizar o painel.

> [!div class="mx-imgBorder"]
> ![Biblioteca de painéis](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Por exemplo, você pode adicionar um bloco de **Telemetria** para a temperatura atual do dispositivo. Para fazer isso:

1. Selecione um **Modelo de dispositivo**
1. Selecione um dispositivo em **Dispositivos** para o dispositivo que você deseja ver em um bloco do painel. Em seguida, você verá uma lista das propriedades do dispositivo que podem ser usadas no bloco.
1. Para criar o bloco no painel, clique em **Temperatura** e arraste para a área do painel. Você também pode clicar na caixa de seleção ao lado de **Temperatura** e clicar em **Adicionar bloco**. A captura de tela a seguir mostra a seleção de um modelo de dispositivo e uma instância de dispositivo, criando um bloco de Telemetria de temperatura no painel.
1. Selecione **Salvar** na parte superior esquerda para salvar o bloco no painel.

> [!div class="mx-imgBorder"]
> ![Formulário “Configurar detalhes do dispositivo” com detalhes para configurações e propriedades](media/howto-add-tiles-to-your-dashboard/device-details.png)

Agora, quando um operador exibe o painel de aplicativo padrão, ele vê o novo bloco com a **Temperatura** para o dispositivo. Cada bloco tem um gráfico pré-selecionado, um gráfico, etc. que será exibido quando o bloco for criado. No entanto, os usuários podem optar por editar e alterar essa visualização.  

> [!div class="mx-imgBorder"]
> ![Guia “Painel” com as configurações e propriedades exibidas para o bloco](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Editar blocos

Para editar um bloco no painel, primeiro clique **Editar** na parte superior esquerda da página, que abrirá o modo de edição para o painel e todos os seus blocos.  

> [!div class="mx-imgBorder"]
> ![Tela do painel com o modo de edição ativado para um bloco selecionado](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Em seguida, clique no ícone de **Engrenagem** no canto superior direito do bloco que você deseja editar. Aqui, você pode editar aspectos do bloco, incluindo seu título, sua visualização, agregação, etc.

> [!div class="mx-imgBorder"]
> ![Lista suspensa para configurações de agregação de bloco](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Você também pode alterar a visualização do gráfico clicando no ícone de **Régua** no bloco.

> [!div class="mx-imgBorder"]
> ![Lista suspensa para configurações de visualização de bloco](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tipos de bloco

A tabela a seguir resume o uso de blocos no Azure IoT Central:

| Bloco | Painel | Descrição
| ----------- | ------- | ------- |
| Conteúdo | Painéis de conjunto de dispositivos e aplicativos |Os blocos com suporte de markdown são blocos clicáveis que exibem texto de título e descrição. Você também pode usar esse bloco como um bloco de link para permitir que um usuário navegue até uma URL relacionada ao seu aplicativo.|
| Imagem | Painéis de conjunto de dispositivos e aplicativos |Os blocos de imagem exibem uma imagem personalizada e podem ser clicáveis. Use um bloco de imagem para adicionar elementos gráficos a um painel e, opcionalmente, permitir que um usuário navegue até uma URL relevante para seu aplicativo.|
| Rótulo | Painéis de aplicativo |Os blocos de rótulo exibem um texto personalizado em um painel. Você pode escolher o tamanho do texto. Use um bloco de rótulo para adicionar informações relevantes ao painel, como descrições, detalhes de contato ou ajuda.|
| Mapeamento | Painéis de dispositivos e aplicativos |Os blocos de mapa exibem o local de um dispositivo em um mapa. Você também pode exibir até 100 pontos do histórico de localização de um dispositivo. Por exemplo, você pode exibir uma rota de amostra de onde um dispositivo esteve na última semana.|
| Gráfico de linhas | Painéis de dispositivos e aplicativos |Os blocos de gráfico de linhas exibem um gráfico de medida de agregação para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de linhas que mostra a temperatura média e a pressão de um dispositivo na última hora.|
| Gráfico de Barras | Painéis de dispositivos e aplicativos |Os blocos de gráfico de barras exibem um gráfico de medidas de agregação para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de barras que mostra a temperatura média e a pressão de um dispositivo na última hora.|
| Gráfico de pizza | Painéis de conjunto de dispositivos e aplicativos |Os blocos de gráfico de pizza exibem um gráfico de medidas de agregação para um dispositivo por um período de tempo.|
| Mapa de Calor | Painéis de conjunto de dispositivos e aplicativos |Os blocos de mapa de calor exibem informações sobre o dispositivo, representadas por cores.|
| Histórico de eventos | Painéis de dispositivos e aplicativos |Os blocos de histórico de eventos exibem os eventos de um dispositivo durante um período de tempo. Por exemplo, você pode usá-lo para mostrar todas as alterações de temperatura de um dispositivo durante a última hora.|
| Histórico de Estado | Painéis de dispositivos e aplicativos |Os blocos de histórico de estado exibem os valores de medida para um período de tempo. Por exemplo, você pode usá-los para mostrar os valores de temperatura de um dispositivo durante a última hora.|
| KPI | Painéis de dispositivos e aplicativos | Os blocos de KPI exibem uma telemetria agregada ou uma medida de evento para um período de tempo. Por exemplo, você pode usá-los para mostrar a temperatura máxima atingida para um dispositivo durante a última hora.|
| Último valor conhecido | Painéis de dispositivos e aplicativos |Os últimos blocos de valor conhecidos exibem o valor mais recente de uma medição de telemetria ou de estado. Por exemplo, você pode usar esses blocos para exibir as medidas mais recentes de temperatura, pressão e umidade de um dispositivo. |
| Propriedade | Painéis de dispositivos e aplicativos | Os blocos de propriedades exibem o valor atual para propriedades e propriedades de nuvem de um dispositivo. Por exemplo, você pode usar esse bloco para exibir as propriedades do dispositivo, como o fabricante ou a versão do firmware de um dispositivo. |

### <a name="customizing-visualizations"></a>Personalizar visualizações

Para gráficos de linhas, gráficos de barras e gráficos de pizza, você pode personalizar as cores exibidas por diferentes telemetrias em seu gráfico. Para fazer isso, selecione o ícone de paleta ao lado da telemetria que você deseja personalizar e escolha uma cor.

> [!div class="mx-imgBorder"]
> ![Lista suspensa para configurações de exibição de cor de telemetria](media/howto-add-tiles-to-your-dashboard/color-customization.png)

Para telemetrias ou propriedades que são do tipo cadeia de caracteres, você poderá escolher como deseja visualizar o texto. Por exemplo, se o dispositivo enviar uma URL como telemetria de cadeia de caracteres, você poderá visualizar essa URL como um link clicável. Se a URL fizer referência a uma imagem, você poderá renderizar a imagem em um último valor conhecido ou bloco de propriedade. Você poderá alterar a maneira como a telemetria de cadeia de caracteres é exibida selecionando a engrenagem ao lado do nome da telemetria. Dessa forma, você poderá mostrar o texto como um texto, link ou uma imagem.

> [!div class="mx-imgBorder"]
> ![Lista suspensa para configurações de visualização de cadeia de caracteres](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar seu painel de aplicativo padrão do Azure IoT Central, você pode [aprender a criar um painel pessoal](howto-create-personal-dashboards.md).
