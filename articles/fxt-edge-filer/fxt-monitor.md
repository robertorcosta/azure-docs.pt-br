---
title: Monitorar o Azure FXT Edge Filer
description: Como monitorar o status do hardware para o cache de armazenamento híbrido Do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254879"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitore o status do hardware do arquivo de borda Azure FXT

O sistema de cache de cache de armazenamento híbrido Azure FXT Edge Filer tem várias luzes de status incorporadas ao chassi para ajudar os administradores a entender como o hardware está funcionando.

## <a name="system-health-status"></a>Estado de saúde do sistema

Para monitorar as operações de cache em um nível mais alto, use a página dashboard do painel de **controle** do software, conforme descrito no [Guia do Painel de Controle](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Hardware status LEDs

Esta seção explica as várias luzes de status incorporadas ao hardware Do Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>LEDs de status do disco rígido

![imagem do disco rígido dianteiro, horizontal, com as etiquetas de chamada 2 (canto superior esquerdo), 1 (canto inferior esquerdo) e 3 (lado direito)](media/fxt-monitor/fxt-drive-callouts.png)

Cada operadora de unidade tem dois LEDs de status: um indicador de atividade (1) e um indicador de status (2). 

* O LED de atividade (1) acende quando a unidade está em uso.  
* O LED de status (2) indica a condição da unidade usando os códigos na tabela abaixo.

| Estado do LED de status da unidade              | Significado  |
|-------------------------------------|----------------------------------------------------------|
| Pisca verde duas vezes por segundo      | Identificando unidade *ou* <br> Preparando unidade para remoção  |
| Desligado (sem iluminação)                         | Sistema não terminou inicialização *ou* <br>A unidade está pronta para ser removida |
| Flashes verde, âmbar, e fora       | A falha na unidade é prevista   |
| Pisca âmbar quatro vezes por segundo | Falha na unidade   |
| Verde sólido                         | A unidade está online |

O lado direito da unidade (3) é rotulado com a capacidade da unidade e outras informações.

Os números das unidades estão impressos no espaço entre elas. No Azure FXT Edge Filer, a unidade 0 é aquela no canto superior esquerdo e a unidade 1, a que fica imediatamente abaixo dela. A numeração continua nesse padrão. 

![foto de um compartimento de disco rígido no gabinete do FXT mostrando os números das unidades e os rótulos de capacidade](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Painel de controle esquerdo

O painel de controle frontal esquerdo possui vários indicadores de LED de status (1) e um grande indicador de saúde do sistema iluminado (2). 

![painel de status esquerdo, com 1 indicadores de status de rotulagem à esquerda, e 2 rotulando a luz indicadora de saúde do sistema grande à direita](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicadores de status do painel de controle 

Os indicadores de status à esquerda mostram uma luz âmbar sólida se houver um erro nesse sistema. A tabela abaixo descreve possíveis causas e soluções para os erros. 

Se você ainda tiver o erro depois de tentar essas soluções, [entre em contato com o suporte](fxt-support-ticket.md) para obter ajuda. 

| ícone | Descrição | Condição de erro | Soluções possíveis |
|----------------|---------------|--------------------|----------------------|
| ![ícone de unidade](media/fxt-monitor/fxt-hd-icon.jpg) | Status da unidade | Erro de unidade | Verifique o registro de eventos do sistema para determinar se a unidade tem um erro ou <br>Executar o teste de diagnóstico on-line apropriado; reiniciar o sistema e executar diagnósticos incorporados (ePSA), ou <br>Se as unidades estiverem configuradas em uma matriz RAID, reinicie o sistema e insira o programa utilitário de configuração do adaptador host |
|![ícone de temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Estado da temperatura | Erro térmico - por exemplo, um ventilador falhou ou a temperatura ambiente está fora de alcance | Verifique as seguintes condições endereçadas: <br>Um ventilador de resfriamento está faltando ou falhou <br>A tampa do sistema, a mortalha de ar, o módulo de memória em branco ou o suporte de enchimento traseiro são removidos <br>A temperatura ambiente é muito alta <br>O fluxo de ar externo está obstruído |
|![ícone de eletricidade](media/fxt-monitor/fxt-electric-icon.jpg) | Estado elétrico | Erro elétrico - por exemplo, tensão fora do alcance, PSU com falha ou um regulador de tensão com falha |  Verifique o registro de eventos do sistema ou mensagens do sistema para verificar o problema específico. Se houver um problema de UPA, verifique o LED de status da UPE e recoloque a UPA se necessário. | 
|![ícone de memória](media/fxt-monitor/fxt-memory-icon.jpg) | Status da memória | Erro de memória | Verifique o registro de eventos do sistema ou mensagens do sistema para verificar a localização da memória com falha; recoloque o módulo de memória. |
|![Ícone PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Status PCIe | Erro de cartão PCIe | Reiniciar o sistema; atualizar drivers de cartão PCIe; reinstalar o cartão |


### <a name="system-health-status-indicator"></a>Indicador de estado de saúde do sistema

O botão de luz grande à direita do painel de controle esquerdo indica o estado geral do sistema e também é usado como uma luz de localizador de unidade no modo de ID do sistema.

Pressione o botão saúde e ID do sistema para alternar entre o modo de ID do sistema e o modo de saúde do sistema.

|Estado de estado de saúde do sistema | Condição |
|-------------------------------------------|-----------------------------------------------|
| Azul sólido | Funcionamento normal: o sistema é ligado, operando normalmente, e o modo de ID do sistema não está ativo. <br/>Pressione o botão de saúde e ID do sistema se quiser mudar para o modo de ID do sistema. |
| Azul piscando | O modo ID do sistema está ativo. Pressione o botão de saúde do sistema e do sistema de iD se quiser mudar para o modo de saúde do sistema. |
| Âmbar sólido | O sistema está em modo de segurança. Se o problema persistir, entre [em contato com o Serviço e suporte ao cliente da Microsoft.](fxt-support-ticket.md) |
| Âmbar piscando | Falha no sistema. Verifique o registro de eventos do sistema para obter mensagens de erro específicas. Para obter informações sobre as mensagens de evento e erro geradas pelo firmware do sistema e agentes que monitoram os componentes do sistema, consulte a página De pesquisa de código de erro em qrl.dell.com. |


