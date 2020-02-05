---
title: Soluções VMware do Azure (AVS)-gateways de VPN
description: Saiba mais sobre o AVS de VPN site a site e os conceitos de VPN ponto a site
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024850"
---
# <a name="vpn-gateways-overview"></a>Visão geral de gateways de VPN

Um gateway de VPN é usado para enviar tráfego criptografado entre uma rede de região AVS em um local ou computador pela Internet pública. Cada região pode ter um gateway de VPN, que pode dar suporte a várias conexões. Quando você cria várias conexões com o mesmo gateway de VPN, todos os túneis de VPN compartilham a largura de banda de gateway disponível.

A AVS fornece dois tipos de gateways de VPN:

* Gateway de VPN site a site
* Gateway de VPN ponto a site

## <a name="site-to-site-vpn-gateway"></a>Gateway de VPN site a site

Um gateway de VPN site a site é usado para enviar tráfego criptografado entre uma rede de região AVS e um datacenter local. Use essa conexão para definir o intervalo de sub-redes/CIDR, para o tráfego de rede entre a rede local e a rede da região AVS.

O gateway de VPN permite que você consuma serviços do local em sua nuvem privada de AVS e serviços em sua nuvem privada de AVS da rede local. A AVS fornece um servidor VPN baseado em políticas para estabelecer a conexão de sua rede local.

Casos de uso para VPN site a site:

* Acessibilidade da sua nuvem privada de AVS privado de qualquer estação de trabalho em sua rede local.
* Uso de seu Active Directory local como uma fonte de identidade do vCenter.
* Transferência conveniente de modelos de VM, ISOs e outros arquivos de seus recursos locais para seu vCenter de nuvem privada de AVS.
* Acessibilidade de cargas de trabalho em execução na sua nuvem privada de AVS da sua rede local.

![Topologia de conexão VPN site a site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parâmetros criptográficos

Uma conexão VPN site a site usa os seguintes parâmetros criptográficos padrão para estabelecer uma conexão segura. Ao criar uma conexão do dispositivo VPN local, use qualquer um dos parâmetros a seguir com suporte no gateway de VPN local.

#### <a name="phase-1-proposals"></a>Propostas da fase 1

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Versão IKE | IKEv1 | IKEv1 | IKEv1 |
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo Diffie Hellman (grupo DH) | 2 | 2 | 2 |
| Tempo de vida | 28.800 segundos | 28.800 segundos | 28.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Propostas da fase 2

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo de sigilo contínuo (grupo PFS) | Nenhum | Nenhum | Nenhum |
| Tempo de vida | 1\.800 segundos | 1\.800 segundos | 1\.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Defina TCP MSS fixação MSS às 1200 em seu dispositivo VPN. Ou, se os dispositivos VPN não oferecerem suporte a MSS fixação MSS, você poderá definir, como alternativa, o MTU na interface de túnel como 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Gateway de VPN ponto a site

Uma VPN ponto a site é usada para enviar tráfego criptografado entre uma rede de região AVS e um computador cliente. A VPN ponto a site é a maneira mais fácil de acessar sua rede de nuvem privada da AVS, incluindo suas VMs de nuvem privada do AVS e de carga de trabalho. Use a conectividade VPN ponto a site se você estiver se conectando à nuvem privada da AVS remotamente.

## <a name="next-steps"></a>Próximos passos

* [Configurar gateway de VPN](vpn-gateway.md)
