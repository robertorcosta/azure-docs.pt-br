---
title: Crie traços de desempenho do lado do cliente
description: Práticas recomendadas para perfil de desempenho do lado do cliente usando WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681305"
---
# <a name="create-client-side-performance-traces"></a>Crie traços de desempenho do lado do cliente

Existem muitas razões pelas quais o desempenho da renderização remota do Azure pode não ser tão bom quanto desejado. Além do desempenho de renderização pura no servidor em nuvem, especialmente a qualidade da conexão de rede tem uma influência significativa na experiência. Para fazer o perfil do servidor, consulte as [consultas de desempenho](../overview/features/performance-queries.md)do lado do servidor do capítulo .

Este capítulo se concentra em como identificar possíveis gargalos do lado do cliente através de *traços de desempenho*.

## <a name="getting-started"></a>Introdução

Se você é novo na funcionalidade de rastreamento de desempenho do Windows, esta seção mencionará os termos e aplicativos mais fundamentais para que você comece.

### <a name="installation"></a>Instalação

Os aplicativos usados para fazer rastreamento com ARR são ferramentas de propósito geral que podem ser usadas para todo o desenvolvimento do Windows. Eles são fornecidos através do [Windows Performance Toolkit](https://docs.microsoft.com/windows-hardware/test/wpt/). Para obter este kit de ferramentas, baixe o [Kit de Avaliação e Implantação do Windows](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologia

Ao procurar informações sobre traços de desempenho, você inevitavelmente encontrará uma série de termos. Os mais importantes são:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** significa [ **E**vent **T**racing for **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). É simplesmente o nome abrangente para a eficiente instalação de rastreamento em nível de kernel que é incorporada ao Windows. Chama-se rastreamento *de eventos,* pois os aplicativos que suportam o ETW emitirão eventos para registrar ações que possam ajudar a rastrear problemas de desempenho. Por padrão, o sistema operacional já emite eventos para coisas como acessos de disco, switches de tarefas e tal. Aplicativos como arr também emitem eventos personalizados, por exemplo, sobre quadros descartados, defasagem de rede etc.

**ETL** significa **E**vent **T**race **L**ogging. Isso simplesmente significa que um rastreamento foi coletado (registrado) e, portanto, é normalmente usado como extensão de arquivo para arquivos que armazenam os dados de rastreamento. Assim, quando você faz um rastreamento, \*você normalmente terá um arquivo .etl depois.

**WPR** significa [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) e é o nome do aplicativo que inicia e interrompe a gravação de traços de eventos. O WPR pega\*um arquivo de perfil (.wprp) que configura quais eventos exatos a serem logados. Tal `wprp` arquivo é fornecido com o ARR SDK. Ao fazer rastreamentos em um PC desktop, você pode iniciar o WPR diretamente. Ao fazer um rastreamento no HoloLens, você normalmente passa pela interface web.

**WPA** significa [ **W**indows **P**erformance **A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) e é o \*nome do aplicativo gui que é usado para abrir arquivos .etl e peneirar os dados para identificar problemas de desempenho. O WPA permite classificar os dados por vários critérios, exibir os dados de várias maneiras, investigar detalhes e correlacionar informações.

Embora os rastreamentos de ETL possam ser criados em qualquer dispositivo Windows (PC local, HoloLens, servidor de nuvem, etc.), eles são normalmente salvos em disco e analisados com WPA em um PC desktop. Arquivos ETL podem ser enviados para outros desenvolvedores para que eles tenham uma olhada. Esteja ciente de que informações confidenciais, como caminhos de arquivos e endereços IP, podem ser capturadas em vestígios de ETL. Você pode usar o ETW de duas maneiras: para gravar vestígios ou analisar vestígios. Os traços de gravação são diretos e exigem uma configuração mínima. Analisar os traços, por outro lado, requer uma compreensão decente tanto da ferramenta WPA quanto do problema que você está investigando. O material geral para o aprendizado da WPA será entregue abaixo, bem como orientações sobre como interpretar traços específicos do ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Gravando um rastreamento em um PC local

Para identificar problemas de desempenho do ARR, você deve preferir fazer um rastreamento diretamente em um HoloLens, porque essa é a única maneira de obter um instantâneo das características de desempenho verdadeiras. No entanto, se você especificamente quer fazer um rastreamento sem as restrições de desempenho do HoloLens ou você só quer aprender a usar wpa e não precisa de um rastreamento realista, aqui está como fazê-lo.

### <a name="wpr-configuration"></a>Configuração WPR

1. Inicie o Gravador de Desempenho do [Windows](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) a partir do *menu inicial*.
1. Expandir **mais opções**
1. Clique **em Adicionar perfis...**
1. Selecione o arquivo *AzureRemoteRenderingNetworkProfiling.wprp*. Você pode encontrar este arquivo no ARR SDK em *Tools/ETLProfiles*.
   O perfil agora será listado em WPR em *medições personalizadas*. Certifique-se de que é o único perfil habilitado.
1. Expandir *a triagem de primeiro nível:*
    * Se tudo o que você deseja fazer é capturar um rastreamento rápido dos eventos de rede ARR, **desative** essa opção.
    * Se você precisar correlacionar eventos de rede ARR com outras características do sistema, como o uso de CPU ou memória, **então habilite** essa opção.
    * Se você habilitar essa opção, o rastreamento provavelmente será de vários gigabytes em tamanho e levará muito tempo para salvar e abrir no WPA.

Depois, sua configuração wpr deve ficar assim:

![Configuração WPR](./media/wpr.png)

### <a name="recording"></a>Gravação

Clique **em Começar** a gravar um rastreamento. Você pode começar e parar de gravar a qualquer momento; você não precisa fechar sua aplicação antes de fazê-lo. Como você pode ver, você não precisa especificar qual aplicativo rastrear, pois o ETW sempre registrará um rastreamento para todo o sistema. O `wprp` arquivo especifica quais tipos de eventos gravar.

Clique **em Salvar** para parar a gravação e especificar onde armazenar o arquivo ETL.

Agora você tem um arquivo ETL que você pode abrir diretamente no WPA ou enviar para outra pessoa.

## <a name="recording-a-trace-on-a-hololens"></a>Gravando um traço em um HoloLens

Para gravar um rastreamento em um HoloLens, inicie seu dispositivo e insira seu endereço IP em um navegador para abrir o Portal do *Dispositivo*.

![Portal de Dispositivos](./media/wpr-hl.png)

1. À esquerda, navegue até *Performance > Performance Tracing*.
1. Selecione **perfis personalizados**
1. Clique **em Procurar...**
1. Selecione o arquivo *AzureRemoteRenderingNetworkProfiling.wprp*. Você pode encontrar este arquivo no ARR SDK em *Tools/ETLProfiles*.
1. Clique **em Iniciar rastreamento**
1. Os HoloLens estão gravando um rastro. Certifique-se de ativar os problemas de desempenho que você deseja investigar. Em seguida, clique em **Stop Trace**.
1. O traço será listado na parte inferior da página web. Clique no ícone de disco no lado direito para baixar o arquivo ETL.

Agora você tem um arquivo ETL que você pode abrir diretamente no WPA ou enviar para outra pessoa.

## <a name="analyzing-traces-with-wpa"></a>Analisando traços com WPA

### <a name="wpa-basics"></a>Noções básicas de WPA

O Windows Performance Analyzer é a ferramenta padrão para abrir arquivos ETL e inspecionar os rastreamentos. Uma explicação de como a WPA funciona está fora de escopo para este artigo. Para começar, veja esses recursos:

* Assista aos [vídeos introdutórios](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) para uma primeira visão geral.
* O wpa em si tem uma guia *Getting Started,* que explica passos comuns. Veja os tópicos disponíveis. Especialmente em "View Data" você recebe uma rápida introdução de como criar gráficos para dados específicos.
* Há excelentes informações [neste site,](https://randomascii.wordpress.com/2015/09/24/etw-central/)no entanto, nem tudo é relevante para iniciantes.

### <a name="graphing-data"></a>Dados gráficos

Para começar com o rastreamento arr, as seguintes peças são boas de se conhecer.

![Gráfico de desempenho](./media/wpa-graph.png)

A imagem acima mostra uma tabela de dados de rastreamento e uma representação gráfica dos mesmos dados.

Na mesa na parte inferior, observe a barra amarela (dourada) e a barra azul. Você pode arrastar essas barras e colocá-las em qualquer posição.

Todas as **colunas à esquerda da barra amarela** são interpretadas como **chaves**. As chaves são usadas para estruturar a árvore na janela superior esquerda. Aqui temos duas *colunas-chave,* "Nome do Provedor" e "Nome da Tarefa". Consequentemente, a estrutura da árvore na janela superior esquerda tem dois níveis de profundidade. Se você reordenar as colunas ou adicionar ou remover colunas da área-chave, a estrutura na exibição da árvore será mudada.

**Colunas à direita da barra azul** são usadas para o **visor** gráfico na janela superior direita. Na maioria das vezes apenas a primeira coluna é usada, mas alguns modos gráficos requerem várias colunas de dados. Para que os gráficos de linha funcionem, o *modo de agregação* nessa coluna deve ser definido. Use 'Avg' ou 'Max'. O modo de agregação é usado para determinar o valor do gráfico em um determinado pixel, quando um pixel cobre uma faixa com vários eventos. Isso pode ser observado definindo a agregação para 'Soma' e, em seguida, ampliando e acessando.

As colunas no meio não têm significado especial.

![Modo de exibição de eventos](./media/wpa-event-view.png)

No *Generic Events View Editor* você pode configurar todas as colunas a serem exibidas, o modo de agregação, a classificação e quais colunas são usadas como chaves ou para gráficos. No exemplo acima, o **Campo 2** está habilitado e o Campo 3 - 6 está desativado. O campo 2 é tipicamente o primeiro campo *de dados personalizado* de um evento ETW e, portanto, para eventos ARR "FrameStatistics", que representam algum valor de latência da rede. Habilite outras colunas "Campo" para ver outros valores deste evento.

### <a name="presets"></a>Predefinições

Para analisar adequadamente um rastreamento, você precisará descobrir seu próprio fluxo de trabalho e exibição de dados preferidos. No entanto, para obter uma visão geral rápida sobre os eventos específicos do ARR, incluímos o perfil da Plataforma de Proteção de Software do Windows e arquivos predefinições na pasta *Ferramentas/ETLProfiles*. Para carregar um perfil completo, selecione *Perfis > Aplicar...* na barra de menus WPA, ou abra o painel *Minhas Predefinições* *(Janela > Minhas Predefinições)* e selecione *Importar*. O primeiro configurará uma configuração WPA completa como na imagem abaixo. Este último só disponibilizará predefinições para as várias configurações de exibição disponíveis e permitirá que você abra rapidamente uma exibição para olhar para um pedaço específico de dados de eventos ARR.

![Predefinições](./media/wpa-arr-trace.png)

A imagem acima mostra visualizações de vários eventos específicos de ARR, além de uma visão da utilização geral da CPU.

## <a name="next-steps"></a>Próximas etapas

* [Consultas de desempenho em parte do servidor](../overview/features/performance-queries.md)
