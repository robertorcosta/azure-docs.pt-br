---
title: Diagnosticar problemas de desempenho gráfico Sumido - Azure
description: Este artigo descreve como usar contadores gráficos RemoteFX em sessões de protocolo de desktop remota sustais para diagnosticar problemas de desempenho com gráficos no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127562"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticar problemas de desempenho gráfico na área de trabalho remota

Para diagnosticar problemas de qualidade de experiência com suas sessões remotas, os contadores foram fornecidos na seção RemoteFX Graphics do Performance Monitor. Este artigo ajuda você a identificar e corrigir gargalos de desempenho relacionados a gráficos durante as sessões de RDP (Remote Desktop Protocol, protocolo de desktop remoto) usando esses contadores.

## <a name="find-your-remote-session-name"></a>Encontre seu nome de sessão remota

Você precisará do seu nome de sessão remota para identificar os contadores de desempenho gráficos. Siga as instruções nesta seção para identificar sua instância de cada contador.

1. Abra o prompt de comando do Windows a partir de sua sessão remota.
2. Execute o comando **qwinsta** e encontre o nome da sessão.
    - Se sua sessão estiver hospedada em uma máquina virtual de várias sessões (VM): sua instância de cada contador será sufixada pelo mesmo número que sufixa seu nome de sessão, como "rdp-tcp 37".
    - Se sua sessão estiver hospedada em uma VM que suporta unidades de processamento de gráficos virtuais (vGPU): sua instância de cada contador será armazenada no servidor em vez de em sua VM. Suas instâncias contrárias incluem o nome VM em vez do número no nome da sessão, como "Win8 Enterprise VM".

>[!NOTE]
> Embora os contadores tenham RemoteFX em seus nomes, eles incluem gráficos de desktop remotos em cenários vGPU também.

## <a name="access-performance-counters"></a>Contadores de desempenho de acesso

Depois de determinar o nome da sessão remota, siga estas instruções para coletar os contadores de desempenho do RemoteFX Graphics para a sessão remota.

1. Selecione **Iniciar** > monitor de desempenho**de ferramentas** > **administrativas**.
2. Na caixa de diálogo Monitor de **desempenho,** expanda **as ferramentas de monitoramento,** selecione **Monitor de desempenho**e selecione **Adicionar**.
3. Na caixa de diálogo Adicionar contadores, da lista **Contadores Disponíveis,** expanda a seção para **Gráficos** RemoteFX.
4. Selecione os contadores a serem monitorados.
5. Nas **instâncias da** lista de objetos selecionados, selecione as instâncias específicas a serem monitoradas para os contadores selecionados e, em seguida, **selecione Adicionar**. Para selecionar todas as contra-instâncias disponíveis, selecione **Todas as instâncias**.
6. Depois de adicionar os contadores, selecione **OK**.

Os contadores de desempenho selecionados aparecerão na tela do Monitor de Desempenho.

>[!NOTE]
>Cada sessão ativa em um host tem sua própria instância de cada contador de desempenho.

## <a name="diagnose-issues"></a>Diagnosticar problemas

Problemas de desempenho relacionados a gráficos geralmente se enquadram em quatro categorias:

- Baixa taxa de quadros
- Barracas aleatórias
- Alta latência de entrada
- Má qualidade do quadro

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Abordando baixa taxa de quadros, bancas aleatórias e latência de entrada alta

Primeiro verifique o contador De quadros/segundo. Ele mede o número de quadros disponibilizados ao cliente. Se esse valor for menor do que o contador Input Frames/Second, os quadros serão ignorados. Para identificar o gargalo, use os contadores Frames Skipped/Second.

Existem três tipos de quadros ignorados/segundo contadores:

- Quadros ignorados/segundo (recursos insuficientes do servidor)
- Quadros ignorados/segundo (recursos de rede insuficientes)
- Quadros ignorados/segundo (recursos insuficientes do cliente)

Um alto valor para qualquer um dos contadores Frames Skipped/Second implica que o problema está relacionado com o recurso que o contador rastreia. Por exemplo, se o cliente não decodificar e apresentar quadros na mesma taxa que o servidor fornece os quadros, o contador Quadros Skipped/Second (Recursos Insuficientes do Cliente) será alto.

Se o contador Descamação/Segundo corresponder ao contador Input Frames/Segundo, ainda assim você notará atraso ou paralisação incomuns, o Tempo médio de codificação pode ser o culpado. Codificação é um processo síncrono que ocorre no servidor no cenário de sessão única (vGPU) e no VM no cenário de várias sessões. O tempo médio de codificação deve ser inferior a 33 ms. Se o Tempo Médio de Codificação estiver abaixo de 33 ms, mas você ainda tiver problemas de desempenho, pode haver um problema com o aplicativo ou sistema operacional que você está usando.

Para obter mais informações sobre o diagnóstico de problemas relacionados ao aplicativo, consulte [os contadores](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)de desempenho de atraso de entrada do usuário .

Como o RDP suporta um tempo médio de codificação de 33 ms, ele suporta uma taxa de quadros de entrada de até 30 quadros/segundo. Observe que 33 ms é a taxa máxima de quadros suportada. Em muitos casos, a taxa de quadros experimentada pelo usuário será menor, dependendo da frequência com que um quadro é fornecido ao RDP pela fonte. Por exemplo, tarefas como assistir a um vídeo exigem uma taxa completa de quadros de entrada de 30 quadros/segundo, mas tarefas menos intensivas computacionalmente como editar um documento raramente resultam em um valor muito menor para Quadros de Entrada/Segundo sem degradação na qualidade da experiência do usuário.

### <a name="addressing-poor-frame-quality"></a>Abordando a má qualidade do quadro

Use o contador frame quality para diagnosticar problemas de qualidade de quadro. Este contador expressa a qualidade do quadro de saída como uma porcentagem da qualidade do quadro de origem. A perda de qualidade pode ser devido ao RemoteFX, ou pode ser inerente à fonte gráfica. Se o RemoteFX causou a perda de qualidade, o problema pode ser a falta de recursos de rede ou servidor para enviar conteúdo de maior fidelidade.

## <a name="mitigation"></a>Atenuação

Se os recursos do servidor estiverem causando o gargalo, tente uma das seguintes abordagens para melhorar o desempenho:

- Reduza o número de sessões por host.
- Aumente a memória e compute os recursos no servidor.
- Solte a resolução da conexão.

Se os recursos de rede estiverem causando o gargalo, tente uma das seguintes abordagens para melhorar a disponibilidade da rede por sessão:

- Reduza o número de sessões por host.
- Use uma rede de largura de banda mais alta.
- Solte a resolução da conexão.

Se os recursos do cliente estiverem causando o gargalo, tente uma das seguintes abordagens para melhorar o desempenho:

- Instale o cliente de desktop remoto mais recente.
- Aumente a memória e computação de recursos na máquina cliente.

> [!NOTE]
> No momento, não suportamos o contador Source Frames/Second. Por enquanto, o contador Quadros de Origem/Segundo sempre exibirá 0.

## <a name="next-steps"></a>Próximas etapas

- Para criar uma máquina virtual Azure otimizada para GPU, consulte [Configure graphics processing unit (GPU) aceleração para o ambiente Windows Virtual Desktop](configure-vm-gpu.md).
- Para obter uma visão geral das faixas de solução de problemas e escalonamento, consulte Visão geral, feedback e suporte de [solução de problemas.](troubleshoot-set-up-overview.md)
- Para saber mais sobre o serviço, consulte o [ambiente Windows Desktop](environment-setup.md).
