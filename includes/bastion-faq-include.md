---
title: incluir arquivo
description: incluir arquivo
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922e8a71a22db975685cb82bbd51a125c619ccf2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989478"
---
### <a name="regions"></a>Quais regiões estão disponíveis?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>É necessário ter um IP público em minha máquina virtual?

Ao conectar-se a uma VM usando o Azure Bastion, NÃO é preciso um IP público na Máquina Virtual do Azure a que você está se conectando. O serviço do Bastion abrirá o RDP/SSH/sessão/conexão para sua máquina virtual usando o IP privado da máquina virtual, dentro de sua rede virtual.

### <a name="is-ipv6-supported"></a>Há compatibilidade com o IPv6?

No momento, não há compatibilidade com o IPv6. O Azure Bastion só é compatível com o IPv4.

### <a name="rdpssh"></a>Eu preciso de um cliente de RDP ou SSH?

Você não precisa de um cliente de RDP ou SSH para acessar o RDP/SSH para sua máquina virtual do Azure no portal do Azure. Use o [portal do Azure](https://portal.azure.com) para ter acesso de RDP/SSH à sua máquina virtual diretamente no navegador.

### <a name="agent"></a>Eu preciso ter um agente em execução na máquina virtual do Azure?

Não é necessário instalar um agente ou um software no navegador ou na máquina virtual do Azure. O serviço do Bastion é sem agente e não requer software adicional para RDP/SSH.

### <a name="browsers"></a>Quais navegadores são compatíveis?

Use o navegador Microsoft Edge ou o Google Chrome no Windows. Para o Apple Mac, use o navegador Google Chrome. O Microsoft Edge Chromium também é compatível com o Windows e o Mac, respectivamente.

### <a name="roles"></a>É necessário ter alguma função para acessar uma máquina virtual?

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="pricingpage"></a>Quais são os preços?

Para saber mais, confira a [página de preço](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Por que recebo a mensagem de erro "Sua sessão expirou" antes de iniciar a sessão do Bastion?

Uma sessão deve ser iniciada somente por meio do portal do Azure. Entre no portal do Azure e inicie sua sessão novamente. Se você acessar a URL diretamente em outra guia ou sessão do navegador, esse erro será esperado. Ele ajuda a garantir que sua sessão seja mais segura e que ela possa ser acessada somente por meio do portal do Azure.

### <a name="keyboard"></a>Quais layouts de teclado são compatíveis durante a sessão remota do Bastion?

Atualmente, o Azure Bastion é compatível com o layout de teclado QWERTY em inglês dentro da VM.  Estamos trabalhando para dar suporte a layouts de teclado de outras localidades.

### <a name="udr"></a>As sub-redes do Azure Bastion são compatíveis com UDR (roteamento definido pelo usuário)?

Não. As sub-redes do Azure Bastion não são compatíveis com UDR.
Para cenários que incluem o Azure Bastion e o Firewall do Azure ou a NVA (solução de virtualização de rede) na mesma rede virtual, você não precisa forçar o tráfego de uma sub-rede do Azure Bastion para o Firewall do Azure, pois a comunicação entre o Azure Bastion e suas VMs é privada. Para saber mais, confira [Acessar VMs por trás do Firewall do Azure com o Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>A sessão RDP do Azure Bastion é compatível com transferência de arquivos?

Estamos trabalhando duro para adicionar novos recursos. Atualmente, a transferência de arquivos não é compatível, mas faz parte de nosso roteiro para o futuro. Fique à vontade para compartilhar seus comentários sobre novos recursos na [página Comentários sobre o Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).
