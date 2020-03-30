---
title: Adicione azulejos ao seu painel | Microsoft Docs
description: Como construtor, aprenda a configurar o painel de aplicativos Padrão Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 49b41715d95a5f210e6e70faf09aa016d1478728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158706"
---
# <a name="configure-the-application-dashboard"></a>Configure o painel de aplicativos

O **Dashboard** é a página que carrega quando os usuários que têm acesso ao aplicativo navegam para a URL do aplicativo. Se você criou seu aplicativo a partir de um dos **Modelos de Aplicativo,** seu aplicativo terá um painel pré-definido para iniciar. Se você criou seu aplicativo a partir do modelo de **aplicativo Legado,** seu painel estará em branco para iniciar.

> [!NOTE]
> Os usuários podem [criar vários dashboards,](howto-create-personal-dashboards.md) além do painel de aplicativos padrão. Esses dashboards podem ser pessoais apenas para o usuário ou compartilhados entre todos os usuários do aplicativo. 

## <a name="add-tiles"></a>Adicionar blocos

A captura de tela a seguir mostra o painel de instrumentos em um aplicativo criado a partir do modelo **De aplicativo personalizado.** Para personalizar o painel padrão do aplicativo, **selecione Editar** no canto superior esquerdo da página.

> [!div class="mx-imgBorder"]
> ![Painel para aplicações com base no modelo "Sample Contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

A seleção **Editar** abre o painel da biblioteca do painel de painel. A biblioteca contém os azulejos e os primitivos do painel que você pode usar para personalizar o painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Biblioteca de painéis](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Por exemplo, você pode adicionar uma telha **de telemetria** para a temperatura atual do dispositivo. Para fazer isso:
1. Selecione um **modelo de dispositivo**
1. Selecione uma **instância de dispositivo** para o dispositivo que você deseja ver em um painel de instrumentos. Em seguida, você verá uma lista das propriedades do dispositivo que podem ser usadas no azulejo.
1. Para criar o azulejo no painel, clique em **Temperatura** e arraste-o para a área do painel. Você também pode clicar na caixa de seleção ao lado de **Temperature** e clicar **em Combinar**. A captura de tela a seguir mostra a seleção de um modelo de dispositivo e uma instância do dispositivo, em seguida, criando um azulejo de telemetria de temperatura no painel.
1. Selecione **Salvar** no canto superior esquerdo para salvar o azulejo no painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](media/howto-add-tiles-to-your-dashboard/device-details.png)

Agora, quando um operador visualiza o painel de aplicativos padrão, ele vê o novo azulejo com a **temperatura** do dispositivo. Cada ladrilho tem um gráfico pré-selecionado, gráfico, etc. que será exibido quando o azulejo for criado. No entanto, os usuários podem optar por editar e alterar essa visualização. 

> [!div class="mx-imgBorder"]
> ![Guia "Painel" com as configurações e propriedades exibidas para o bloco](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Editar telhas

Para editar um azulejo no painel, primeiro clique em **Editar** no canto superior esquerdo da página, que abrirá o modo de edição para o painel de instrumentos e todas as suas telhas. 

> [!div class="mx-imgBorder"]
> ![Tela do painel com modo de edição ativado para um azulejo selecionado](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Em seguida, clique no ícone **Engrenagem** no canto superior direito do azulejo que deseja editar. Aqui você pode editar aspectos do azulejo incluindo seu título, sua visualização, agregação, etc.

> [!div class="mx-imgBorder"]
> ![Dropdown para configurações de agregação de ladrilhos](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Você também pode alterar a visualização do gráfico clicando no ícone **Régua** no bloco.

> [!div class="mx-imgBorder"]
> ![Dropdown para configurações de visualização de ladrilhos](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tipos de azulejos

A tabela a seguir resume o uso de telhas no Azure IoT Central:
 
| Bloco | Painel | Descrição
| ----------- | ------- | ------- |
| Conteúdo | Painéis de conjunto de aplicativos e dispositivos |As telhas suportadas pelo Markdown são telhas clicáveis que exibem o texto do título e da descrição. Você também pode usar este azulejo como um link para permitir que um usuário navegue até uma URL relacionada ao seu aplicativo.|
| Imagem | Painéis de conjunto de aplicativos e dispositivos |Os ladrilhos de imagem exibem uma imagem personalizada e podem ser clicáveis. Use um bloco de imagens para adicionar gráficos a um painel de instrumentos e, opcionalmente, permita que um usuário navegue para uma URL relevante para o seu aplicativo.|
| Rótulo | Painéis de aplicativos |Os azulejos de etiqueta exibem texto personalizado em um painel. Você pode escolher o tamanho do texto. Use um azulejo de etiqueta para adicionar informações relevantes ao painel de controle, tais descrições, detalhes de contato ou ajuda.|
| Mapeamento | Painéis de conjunto de aplicativos e dispositivos |As telhas do mapa exibem a localização e o estado de um dispositivo em um mapa. Por exemplo, você pode exibir onde um dispositivo está e se seu ventilador está ligado.|
| Gráfico de linhas | Painéis de aplicativos e dispositivos |As telhas do gráfico de linha exibem um gráfico de medição agregada para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de linha que mostra a temperatura média e a pressão de um dispositivo para a última hora.|
| Gráfico de Barras | Painéis de aplicativos e dispositivos |As telhas do gráfico de barras exibem um gráfico de medidas agregadas para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de barras que mostra a temperatura média e a pressão de um dispositivo durante a última hora.|
| Gráfico de pizza | Painéis de conjunto de aplicativos e dispositivos |As telhas do gráfico de torta exibem um gráfico de medidas agregadas para um dispositivo por um período de tempo.|
| Mapa de Calor | Painéis de conjunto de aplicativos e dispositivos |As telhas do Mapa de Calor exibem informações sobre o conjunto do dispositivo, representados como cores.|
| História do evento | Painéis de aplicativos e dispositivos |Os ladrilhos do Histórico de Eventos exibem os eventos de um dispositivo durante um período de tempo. Por exemplo, você pode usá-lo para mostrar todas as mudanças de temperatura de um dispositivo durante a última hora.|
| Histórico de Estado | Painéis de aplicativos e dispositivos |Os ladrilhos do histórico do estado exibem os valores de medição por um período de tempo. Por exemplo, você pode usá-lo para mostrar os valores de temperatura de um dispositivo durante a última hora.|
| KPI | Painéis de aplicativos e dispositivos | As telhas KPI exibem uma telemetria agregada ou medição de eventos por um período de tempo. Por exemplo, você pode usá-lo para mostrar a temperatura máxima alcançada para um dispositivo durante a última hora.|
| Último valor conhecido | Painéis de aplicativos e dispositivos |Os últimos ladrilhos de valor conhecidoexibem o valor mais recente para uma telemetria ou medição de estado. Por exemplo, você pode usar este azulejo para exibir as medidas mais recentes de temperatura, pressão e umidade para um dispositivo.|

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar o painel de aplicativos padrão do Azure IoT Central, você pode [aprender como criar um painel de controle pessoal](howto-create-personal-dashboards.md).
