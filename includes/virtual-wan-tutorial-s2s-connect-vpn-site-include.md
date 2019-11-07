---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bcc4af837cc28a18345e49cd39ddf88d24e44dd8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488885"
---
1. Selecione **Conectar sites VPN** para abrir a página **Conectar sites **.

    ![connect](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "conectar")

   Preencha os seguintes campos:

   * Insira uma chave pré-compartilhada. Se você não inserir uma chave, o Azure gerará uma automaticamente para você.
   * Selecione as configurações de Protocolo e IPsec. Consulte os detalhes de IPSec padrão/personalizado (adicione o link para a página)
   * Selecione a opção apropriada para **Propagar Rota Padrão**. A opção **Habilitar** permite que o hub virtual propague uma rota padrão aprendida para essa conexão. Esse sinalizador habilita a propagação da rota padrão para uma conexão somente se já foi aprendida pelo hub de WAN Virtual como resultado da implantação de um firewall no hub ou se a opção de túnel forçado está habilitada em outro site conectado. A rota padrão não se origina no hub da WAN virtual.

2. Selecione **Conectar**.
3. Dentro de alguns minutos, o site mostrará o status de conexão e conectividade.

   ![status](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Status da conexão:** Esse é o status do recurso do Azure para a conexão entre o site VPN e o gateway de VPN do hub do Azure. Quando essa operação do plano de controle for bem-sucedida, o gateway de VPN do Azure e o dispositivo VPN local continuarão a estabelecer conectividade.

   **Status da conectividade:** Esse é o status de conectividade real (caminho de dados) entre o gateway de VPN do Azure no hub e o site VPN. Pode mostrar qualquer um dos seguintes estados:

    * **Desconhecido**: Esse estado é normalmente visto quando os sistemas de back-end estão trabalhando para fazer a transição para outro status.
    * **Conectando**: O gateway de VPN do Azure está tentando entrar em contato com o site VPN local real.
    * **Conectado:** A conectividade é estabelecida entre o gateway de VPN do Azure e o site VPN local.
    * **Desconectado**: Esse status é visto quando ocorre uma desconexão por qualquer motivo (local ou no Azure).
4. Em um site VPN do hub, você também pode fazer o seguinte: 

   * Edite ou exclua a conexão VPN.
   * Exclua o site no portal do Azure.
   * Baixe uma configuração específica do branch para obter detalhes sobre o lado do Azure usando o menu de contexto (...) ao lado do site. Se você quiser baixar a configuração para todos os sites conectados em seu hub, selecione **Baixar configuração de VPN** no menu superior.