---
title: A ferramenta de inspeção ArrInspector
description: Manual do usuário da ferramenta ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680070"
---
# <a name="the-arrinspector-inspection-tool"></a>A ferramenta de inspeção ArrInspector

O ArrInspector é uma ferramenta baseada na Web usada para inspecionar uma sessão de renderização remota do Azure em execução. Ele deve ser usado para fins de depuração, para inspecionar a estrutura da cena que está sendo renderizada, mostrar as mensagens de registro e monitorar o desempenho ao vivo no servidor.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Conectando-se ao ArrInspector

Depois de obter o nome `mixedreality.azure.com`de host (terminando em ) do servidor ARR, conecte-se usando [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Esta função `StartArrInspector.html` cria um no dispositivo no qual o aplicativo está sendo executado. Para iniciar o ArrInspector, abra esse arquivo com um navegador (Edge, Firefox ou Chrome) em um PC. O arquivo só é válido por 24 horas.

Se o aplicativo `ConnectToArrInspectorAsync` que chama já estiver sendo executado em um PC:

* Se você estiver usando a integração Unity, ela pode ser lançada automaticamente para você.
* Caso contrário, você encontrará o arquivo em *Pastas\\de Usuário LocalAppData\\[your_app]\\AC\\Temp*.

Se o aplicativo estiver sendo executado em um HoloLens:

1. Acesse os HoloLens usando o [Portal do Dispositivo Windows](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Vá para *System > File Explorer*.
1. Navegue *até\\as pastas do usuário LocalAppData\\[your_app]\\\\AC Temp*.
1. Salve *StartArrInspector.html* no seu PC.
1. Abra *StartArrInspector.html* para carregar o ArrInspector da sessão.

## <a name="the-performance-panel"></a>O painel Desempenho

![O Painel de Desempenho](./media/performance-panel.png)

Este painel mostra gráficos de todos os valores de desempenho por quadro expostos pelo servidor. Os valores atualmente incluem o tempo de quadro, FPS, CPU e uso de memória, estatísticas de memória como uso geral de RAM, contagem de objetos, etc.

Para visualizar um desses parâmetros, clique no botão **Adicionar novo** e selecione um dos valores disponíveis mostrados na caixa de diálogo. Esta ação adiciona um novo gráfico de rolagem ao painel, traçando os valores em tempo real. À sua direita você pode ver o valor *mínimo,* *máximo* e *atual.*

Você pode panar o gráfico, arrastando seu conteúdo com o mouse, no entanto, panorâmica horizontalmente só é possível quando ArrInspector está no estado pausado.

Segurar CTRL enquanto arrasta, permite que você amplie. O zoom horizontal também pode ser controlado com o controle deslizante na parte inferior.

O intervalo vertical é calculado por padrão com base nos valores exibidos atualmente, e os valores min e max são mostrados nas caixas de texto à direita. Quando os valores são definidos manualmente, digitando-os diretamente na caixa de texto, ou por panorâmica/zoom, o gráfico usará esses valores. Para restaurar o enquadramento vertical automático, clique no ícone no canto superior direito.

![faixa vertical](./media/vertical-range.png)

## <a name="the-log-panel"></a>O painel Log

![Painel de log](./media/log-panel.png)

O painel de log mostra uma lista de mensagens de log geradas no lado do servidor. Na conexão, ele aparecerá até 200 mensagens de log anteriores, e imprimirá novas à medida que elas acontecerem.

Você pode filtrar a lista `[Error/Warning/Info/Debug]` com base no tipo de registro usando os botões na parte superior.
![Botões do filtro de log](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>O painel De captura de dados de tempo

![Captura de dados de tempo](./media/timing-data-capture.png)

Este painel é usado para capturar informações de tempo do servidor e baixá-los. O arquivo usa o [formato JSON do Chrome Tracing](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Para inspecionar os dados, abra `Chrome://tracing` o Chrome na URL e arraste e solte o arquivo baixado na página. Os dados de tempo são coletados continuamente em um buffer de anel de tamanho fixo. Quando escrito, a captura inclui apenas informações sobre o passado imediato, o que significa alguns segundos a poucos minutos.

## <a name="the-scene-inspection-panel"></a>O painel de inspeção de cena

![Painel de Inspeção de Cena](./media/scene-inspection-panel.png)

Este painel mostra a estrutura da cena renderizada. A hierarquia do objeto está à esquerda, o conteúdo do objeto selecionado está à direita. O painel é somente leitura e é atualizado em tempo real.

## <a name="the-vm-debug-information-panel"></a>O painel de informações de depuração vm

![Painel de informações sobre depuração de VM](./media/state-debugger-panel.png)

Este painel oferece alguma funcionalidade de depuração.

### <a name="restart-service"></a>Reiniciar o serviço

O botão **Reiniciar serviço** reinicia o tempo de execução na máquina virtual à a que o arrInspector está conectado. Qualquer cliente conectado será desconectado e a página arrInspector deve ser recarregada para se conectar ao serviço reiniciado.

### <a name="collect-debug-information"></a>Coletar informações de depuração

O botão **Coletar informações de depuração para VM** abre uma caixa de diálogo que permite acionar a instância ARR para coletar informações de depuração na VM:

![Diálogo de informações sobre depuração de VM](./media/state-debugger-dialog.png)

As informações de depuração ajudam a equipe de renderização remota do Azure a analisar quaisquer problemas que ocorram em uma instância ARR em execução. A caixa de diálogo tem um campo de texto para fornecer detalhes adicionais, por exemplo, etapas para reproduzir um problema.

Depois de clicar no botão **Iniciar coleta,** a caixa de diálogo será fechada e o processo de coleta começará. A coleta das informações sobre a VM pode levar alguns minutos.

![VM Debug Information collection em andamento](./media/state-debugger-panel-in-progress.png)

Assim que a coleta estiver concluída, você receberá uma notificação na janela ArrInspector. Esta notificação contém um ID que identifica essa coleção em particular. Certifique-se de salvar este ID para passá-lo para a equipe de renderização remota do Azure.

![VM Debug Information collection sucesso](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Você não pode baixar ou acessar informações de depuração de VM. Apenas a equipe de Renderização Remota do Azure tem acesso aos dados coletados. Você precisa entrar em contato conosco e enviar o ID de coleta junto, para que investiguemos o problema que você está vendo.

## <a name="pause-mode"></a>Modo pausa

No canto superior direito, um switch permite pausar a atualização ao vivo dos painéis. Este modo pode ser útil para inspecionar cuidadosamente um estado específico.

![Modo pausa](./media/pause-mode.png)

Ao reativar a atualização ao vivo, todos os painéis são redefinidos.

## <a name="host-configuration"></a>Configuração do host

Por padrão, a ferramenta se conecta ao servidor ARR que está sendo executado no mesmo host que serve o ArrInspector. No entanto, você pode configurá-lo para inspecionar outro servidor, assumindo que ele está executando uma instância ARR com a porta de ferramentas aberta.

Para isso, acesse o menu principal à esquerda da barra de cabeçalho e selecione *Configuração Host*. Clique **em Adicionar novo host**e digite o nome e o nome do host. Para *o nome do host,* use apenas o nome de host que termina em `.mixedreality.azure.com`, não inclua `http://` ou uma porta.

![Configuração do host](./media/host-configuration.png)

Para mudar rapidamente de um host para outro, use o drop-down no canto superior direito.

![Host Combo](./media/host-switch-combo.png)

A lista de host saem armazenadas no armazenamento local do navegador, por isso será preservada ao reabrir o mesmo navegador.
