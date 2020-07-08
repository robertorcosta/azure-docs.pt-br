---
title: Integração de Gateway de Aplicativo do com a Central de segurança do Azure | Microsoft Docs
description: Esta página fornece informações sobre como o Gateway de Aplicativo se integra à Central de segurança do Azure.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277183"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Visão geral da integração entre o Gateway de Aplicativo e a Central de Segurança do Azure

Saiba como o Gateway de Aplicativo e a Central de segurança ajudam a proteger os recursos do aplicativo Web. O Firewall do aplicativo Web do gateway de aplicativo (WAF) integra-se à [central de segurança](../security-center/security-center-intro.md) para fornecer uma visão direta para prevenir, detectar e responder a ameaças a aplicativos Web desprotegidos em seu ambiente.

## <a name="overview"></a>Visão geral

O WAF do Gateway de Aplicativo é uma recomendação na Central de segurança para proteger aplicativos Web contra explorações e vulnerabilidades. Recursos da Web que não estão protegidos por WAF são exibidos na central de segurança como recomendações de severidade alta. Recomendações para firewalls de aplicativo Web são mostradas na página **Visão geral** em **Aplicativos**.

![integração com a central de segurança][1]

Clicando em uma recomendação sobre o firewall do aplicativo Web abre uma nova página mostrando os detalhes da recomendação.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adicionar o firewall do aplicativo Web a um recurso existente

Navegue até **todos os serviços**  >  **segurança + identidade**  >  **central de segurança** e na **central de segurança-visão geral**, clique em **aplicativos**. Em **Central de Segurança - Aplicativos**, a tabela contém uma lista de aplicativos que a Central de Segurança detectou na sua assinatura.

![aplicativos Web][3]

Ao clicar em um aplicativo Web com um problema crítico, você obtém a página **Integridade da segurança do aplicativo**. Na imagem abaixo, está o aplicativo Web que não está protegido por um firewall do aplicativo Web. 

![recursos Web não protegidos][2]

Clique em **Adicionar um firewall do aplicativo Web** em **Recomendações** para abrir a página **Adicionar um Firewall do aplicativo Web**.

Se você não tiver um Gateway de Aplicativo existente ou quiser criar um novo, clique em **Criar novo** e em **Criar um novo Firewall do aplicativo Web** e clique em **Microsoft - Gateway de Aplicativo**. Isso leva você para as etapas para criar um gateway de aplicativo. Neste ponto, seu aplicativo Web é adicionado como um recurso protegido, agora a Central de segurança controla o que esse recurso está protegido por um firewall do aplicativo Web. Isso não o adiciona como um membro do pool de back-end.

Se você tiver um gateway de aplicativo existente, é possível escolhê-lo em **Usar solução existente**

![Página para adicionar um firewall do aplicativo Web][4]

Adicionar um aplicativo Web a um gateway de aplicativo através da Central de Segurança do Azure não adiciona o recurso como um membro do pool de back-end. Isso deve ser feito diretamente no recurso de gateway do aplicativo.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adicionar um recurso a um firewall do aplicativo Web existente

Navegue até **todos os serviços**  >  **segurança + identidade**  >  **central de segurança** e na **central de segurança-visão geral**, clique em **soluções de parceiros**. Gateways de aplicativo com reconhecimento da Central de Segurança do Azure existentes na página **Soluções de Parceiros**.

![soluções de parceiros][7]

Clique em **Vincular Aplicativo** para abrir **Vincular Aplicativos**, aqui você terá as opções para selecionar os aplicativos existentes. Escolha os aplicativos para serem protegidos e clique em **OK**. Isso não adiciona o aplicativo Web ao pool de back-end do gateway de aplicativo. Isso define os recursos como um recurso protegido para que a Central de segurança possa controlá-lo. Para adicionar o recurso como um membro do pool de back-end, isso deve ser feito no gateway de aplicativo, na página atual, você pode clicar em **Console de soluções** para ser levado ao recurso de gateway de aplicativo onde você pode adicionar o aplicativo Web ao pool de back-end.

![aplicativos de soluções de parceiros][6]

## <a name="finalize-configuration"></a>Finalizar a configuração

A Central de segurança controla os aplicativos adicionados a um gateway de aplicativo como um recurso protegido.  Ela monitora a integridade desse recurso e garante que ele seja protegido por um gateway de aplicativo. A próxima etapa é adicionar o IP privado, o IP público ou a NIC de sua máquina virtual para o pool de back-end do gateway de aplicativo. Até que isso seja feito, uma recomendação adicional de **Finalizar a proteção do aplicativo** é mostrada até que o recurso seja adicionado.

![Página para adicionar um firewall do aplicativo Web][5]

## <a name="security-alerts"></a>Alertas de segurança

Na central de segurança, navegue até **detecção**  >  **alertas de segurança**.  Aqui você encontra alertas do WAF para seus gateways de aplicativo. Os alertas são divididos pela regra do WAF.

![alertas de segurança][8]

A seleção de uma regra fornecerá uma lista de alertas para essa regra WAF específica. Cada alerta mostra detalhes adicionais sobre a descoberta. Os detalhes fornecem um link para o gateway de aplicativo.
 
![detalhes do alerta][9]

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar o Firewall do aplicativo Web em um gateway de aplicativo existente, visite [criar ou atualizar um gateway de aplicativo Azure com o Firewall do aplicativo Web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png