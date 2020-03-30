---
title: Solução Azure VMware por Gateways CloudSimple - VPN
description: Conheça os conceitos de VPN e VPN ponto a ponto do Cloud-to-Site
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024850"
---
# <a name="vpn-gateways-overview"></a>Visão geral dos gateways VPN

Um gateway VPN é usado para enviar tráfego criptografado entre uma rede de região CloudSimple em um local local ou um computador pela internet pública.  Cada região pode ter um gateway VPN, que pode suportar várias conexões. Quando você cria várias conexões com o mesmo gateway de VPN, todos os túneis de VPN compartilham a largura de banda de gateway disponível.

O CloudSimple fornece dois tipos de gateways VPN:

* Gateway VPN site-to-site
* Gateway VPN ponto a ponto

## <a name="site-to-site-vpn-gateway"></a>Gateway VPN site-to-site

Um gateway VPN site-to-site é usado para enviar tráfego criptografado entre uma rede de região CloudSimple e um datacenter local. Use essa conexão para definir o intervalo de sub-redes/CIDR, para tráfego de rede entre sua rede local e a rede de região CloudSimple.

O gateway VPN permite que você consuma serviços a partir de locais em sua Nuvem Privada e serviços em sua Nuvem Privada a partir da rede local.  O CloudSimple fornece um servidor VPN baseado em políticas para estabelecer a conexão a partir de sua rede local.

Use casos para VPN site-to-site:

* Acessibilidade do seu VCenter de Nuvem Privada a partir de qualquer estação de trabalho em sua rede local.
* Use seu Active Directory no local como fonte de identidade do vCenter.
* Transferência conveniente de modelos de VM, ISOs e outros arquivos de seus recursos no local para o seu vCenter privado em nuvem.
* Acessibilidade de cargas de trabalho em execução em sua Nuvem Privada a partir de sua rede local.

![Topologia de conexão VPN site-to-site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parâmetros criptográficos

Uma conexão VPN site-to-site usa os seguintes parâmetros criptográficos padrão para estabelecer uma conexão segura.  Quando você criar uma conexão a partir do seu dispositivo VPN no local, use qualquer um dos seguintes parâmetros que são suportados pelo seu gateway VPN no local.

#### <a name="phase-1-proposals"></a>Propostas da Fase 1

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Versão IKE | IKEv1 | IKEv1 | IKEv1 |
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo Diffie Hellman (Grupo DH) | 2 | 2 | 2 |
| Tempo de vida | 28.800 segundos | 28.800 segundos | 28.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Propostas da Fase 2

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo de Sigilo Avançado Perfeito (Grupo PFS) | Nenhum | Nenhum | Nenhum |
| Tempo de vida | 1.800 segundos | 1.800 segundos | 1.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Defina o Grampo TCP MSS em 1200 no seu dispositivo VPN. Ou se seus dispositivos VPN não suportam a fixação mSS, você pode, alternativamente, definir o MTU na interface do túnel para 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Gateway VPN ponto a ponto

Uma VPN ponto a ponto é usada para enviar tráfego criptografado entre uma rede de região CloudSimple e um computador cliente.  O Point-to-Site VPN é a maneira mais fácil de acessar sua rede private cloud, incluindo seu vCenter privado em nuvem e VMs de carga de trabalho.  Use conectividade VPN ponto a ponto se estiver se conectando à Nuvem Privada remotamente.

## <a name="next-steps"></a>Próximas etapas

* [Configurar o gateway VPN](vpn-gateway.md)
