---
title: Como integrar o Windows Admin Center com o Azure Security Center | Microsoft Docs
description: Este artigo explica como integrar o Azure Security Center com o Windows Admin Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5467794bf246fab4ff7ded9c445dbeee0c4093b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139616"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integre o Azure Security Center com o Windows Admin Center

O Windows Admin Center é uma ferramenta de gerenciamento para seus servidores Windows. É um único local para os administradores do sistema acessarem a maioria das ferramentas administrativas mais usadas. A partir do Windows Admin Center, você pode embarcar diretamente em seus servidores on-prem no Azure Security Center. Em seguida, você pode visualizar um resumo de suas recomendações de segurança e alertas diretamente na experiência do Windows Admin Center.

> [!NOTE]
> Sua assinatura do Azure e o espaço de trabalho log analytics associado precisam ter o nível padrão do Security Center ativado para permitir a integração do Windows Admin Center.
> O nível padrão é gratuito nos primeiros 30 dias se você não o tiver usado anteriormente na assinatura e no espaço de trabalho. Para obter mais informações, consulte [a página de informações sobre preços](security-center-pricing.md).
>

Quando você tiver embarcado com sucesso um servidor do Windows Admin Center para o Azure Security Center, você pode:

* Veja alertas de segurança e recomendações dentro da extensão do Security Center no Windows Admin Center
* Veja a postura de segurança e recupere informações detalhadas adicionais de seus servidores gerenciados do Windows Admin Center no Security Center dentro do portal Azure (ou através de uma API)

Ao combinar essas duas ferramentas, o Security Center torna-se seu único painel de vidro para visualizar todas as suas informações de segurança, qualquer que seja o recurso: proteger o Seu Centro de Administradores do Windows gerenciado servidores on-prem, suas VMs e quaisquer cargas de trabalho adicionais do PaaS.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Onboarding Windows Admin Center gerenciou servidores no Security Center

1. No Windows Admin Center, selecione um de seus servidores e, no painel **Ferramentas,** selecione a extensão do Azure Security Center:

    ![Extensão do Azure Security Center no Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se o servidor já estiver a bordo do Security Center, a janela de configuração não aparecerá.

1. Clique **em Entrar no Azure e configurar**.
    ![Onboarding Windows Admin Center extensão para O Centro de Segurança do Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Siga as instruções para conectar seu servidor ao Security Center. Depois de inserir os detalhes necessários e confirmados, o Security Center faz as alterações de configuração necessárias para garantir que todas as seguintes são verdadeiras:
    * Um Gateway Azure está registrado.
    * O servidor tem um espaço de trabalho para relatar e uma assinatura associada.
    * A solução padrão de Log Analytics de nível do Security Center está habilitada no espaço de trabalho. Esta solução fornece os recursos de nível padrão do Security Center para *todos os* servidores e máquinas virtuais que reportam a este espaço de trabalho.
    * O preço padrão de nível do Security Center para a Máquina Virtual está habilitado na assinatura.
    * O Microsoft Monitoring Agent (MMA) está instalado no servidor e configurado para reportar ao espaço de trabalho selecionado. Se o servidor já reportar a outro espaço de trabalho, ele também será configurado para reportar ao espaço de trabalho recém-selecionado.

    > [!NOTE]
    > Pode levar algum tempo depois de embarcar para que as recomendações apareçam. De fato, dependendo da atividade do servidor, você pode não receber *nenhum* alerta. Para gerar alertas de teste para testar seus alertas estão funcionando corretamente, siga as instruções no [procedimento de validação de alertas](security-center-alert-validation.md).


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Visualização de recomendações e alertas de segurança no Windows Admin Center

Uma vez a bordo, você pode visualizar seus alertas e recomendações diretamente na área do Azure Security Center do Windows Admin Center. Clique em uma recomendação ou um alerta para visualizá-los no portal Azure. Lá, você receberá informações adicionais e aprenderá como corrigir problemas.

[![Recomendações e alertas do Security Center como visto no Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Visualizando recomendações de segurança e alertas para servidores gerenciados do Windows Admin Center no Security Center
Do Azure Security Center:

* Para visualizar as recomendações de segurança para todos os seus servidores do Windows Admin Center, abra **o Compute & Apps** e clique na guia **VMs e Computadores.** Filtre a lista pelo recurso "Server" como mostrado aqui:

    [![Exibir recomendações de segurança para servidores gerenciados do Windows Admin Center](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Para exibir alertas de segurança para todos os seus servidores do Windows Admin Center, abra **alertas de segurança**. Clique **em Filtrar** e garantir que **apenas** "Non-Azure" esteja selecionado:

    ![Filtrar alertas de segurança para servidores gerenciados do Windows Admin Center](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Exibir alertas de segurança para servidores gerenciados do Windows Admin Center](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)