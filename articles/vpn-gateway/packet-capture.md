---
title: 'Gateway Azure VPN: Configure capturas de pacotes'
description: Saiba mais sobre as funcionalidades de captura de pacotes que você pode usar em gateways VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353512"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Configurar capturas de pacotes para gateways VPN

Problemas relacionados à conectividade e desempenho são muitas vezes complexos e levam tempo e esforço significativos apenas para reduzir a causa do problema. A capacidade de captura de pacotes ajuda muito a reduzir o tempo para reduzir o escopo do problema para certas partes da rede, como se o problema está do lado do cliente da rede, do lado Azure da rede ou em algum lugar no meio. Uma vez que o problema foi reduzido, é muito mais eficiente depurar e tomar medidas corretivas.

Existem algumas ferramentas comumente disponíveis para captura de pacotes. No entanto, obter capturas de pacotes relevantes usando essas ferramentas é muitas vezes complicado, especialmente quando se trabalha com cenários de tráfego de alto volume. Os recursos de filtragem fornecidos por uma captura de pacote de gateway VPN tornam-se um grande diferencial. Você pode usar uma captura de pacote de gateway VPN, além de ferramentas de captura de pacotes comumente disponíveis.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Recursos de filtragem de captura de pacotes de gateway VPN

As capturas de pacotes de gateway VPN podem ser executadas no gateway ou em uma conexão específica, dependendo das necessidades do cliente. Você também pode executar capturas de pacotes em vários túneis ao mesmo tempo. Você pode capturar tráfego de direção única ou bidirecional, tráfego IKE e ESP e pacotes internos, juntamente com a filtragem em um gateway VPN.

O uso de 5 filtros de tuplas (sub-rede de origem, sub-rede de destino, porta de origem, porta de destino, protocolo) e bandeiras TCP (SYN, ACK, FIN, URG, PSH, RST) é útil ao isolar problemas em um tráfego de alto volume.

Você pode usar apenas uma opção por propriedade durante a execução da captura de pacotes.

## <a name="setup-packet-capture-using-powershell"></a>Captura de pacote de configuração usando o PowerShell

Veja os exemplos abaixo para que os comandos PowerShell iniciem e interrompam as capturas de pacotes. Para obter mais informações sobre opções de parâmetros (como criar filtro), consulte este [documento](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)PowerShell .

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Iniciar captura de pacotes para um gateway VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Parâmetro opcional **-FilterData** pode ser usado para aplicar filtro.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Parar a captura de pacotes para um gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Iniciar a captura de pacotes para uma conexão de gateway VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Parâmetro opcional **-FilterData** pode ser usado para aplicar filtro.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Parar a captura de pacotes em uma conexão de gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considerações-chave

- A execução de capturas de pacotes pode afetar o desempenho. Lembre-se de interromper a captura do pacote quando não for necessário.
- A duração mínima sugerida de captura do pacote é de 600 segundos. Ter uma duração de captura de pacote mais curta pode não fornecer dados completos devido a problemas de sincronização entre vários componentes no caminho.
- Os arquivos de dados de captura de pacotes são gerados no formato PCAP. Use wireshark ou outros aplicativos comumente disponíveis para abrir arquivos PCAP.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o VPN Gateway, consulte [Sobre o GATEWAY VPN](vpn-gateway-about-vpngateways.md)
