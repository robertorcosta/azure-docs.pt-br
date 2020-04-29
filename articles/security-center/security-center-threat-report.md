---
title: Relatório de inteligência de ameaças da Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda a usar os Relatórios de Inteligência de Ameaças da Central de Segurança do Azure durante uma investigação para obter mais informações sobre um alerta de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: f8b4063d87fa9a89dccd42eddea644609bd6ff27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921242"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de Inteligência de Ameaças da Central de Segurança do Azure
Este documento explica como os Relatórios Inteligentes de Ameças da Central de Segurança do Azure podem ajudá-lo a saber mais sobre uma ameaça que gerou um alerta de segurança.

## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de inteligência de ameaças?
A proteção contra ameaças da central de segurança funciona monitorando as informações de segurança de seus recursos do Azure, da rede e das soluções de parceiros conectadas. Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças. Para obter mais informações, consulte [como a central de segurança do Azure detecta e responde às ameaças](security-center-alerts-overview.md#detect-threats).

Quando a Central de Segurança identifica uma ameaça, ele dispara um [alerta de segurança](security-center-managing-and-responding-alerts.md), que contém informações sobre um evento específico, incluindo sugestões de correção detalhadas. Para ajudar as equipes de resposta a incidentes a investigar e a corrigir ameaças, a Central de Segurança inclui um relatório de inteligência de ameaças que contém informações sobre a ameaça detectada, incluindo informações como:

* Identidade ou associações do invasor (se essas informações estiverem disponíveis)
* Objetivos dos invasores
* Campanhas de ataque atuais e históricas (se essas informações estiverem disponíveis)
* Táticas, ferramentas e procedimentos dos invasores
* Indicadores associados de comprometimento (IoC), como URLs e hashes de arquivo
* Vitimologia, que é a prevalência do setor e geográfica para auxiliar você na determinação se seus recursos do Azure estão em risco
* Informações de atenuação e correção

> [!NOTE]
> A quantidade de informações em qualquer relatório específico varia; o nível de detalhes baseia-se na atividade e na prevalência do malware.
>
>

A Central de Segurança tem três tipos de relatórios de ameaça, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

* **Relatório do grupo de atividades**: fornece aprofundamento nos invasores, seus objetivos e táticas.
* **Relatório de campanha**: concentra-se em detalhes de campanhas de ataque específicas.
* **Relatório de Resumo de Ameaças**: abrange todos os itens dos dois relatórios anteriores.

Esse tipo de informação é muito útil durante o processo de resposta a incidentes, em que há uma investigação em andamento para compreender a origem do ataque, as motivações do invasor e o que fazer para atenuar esse problema no futuro.

## <a name="how-to-access-the-threat-intelligence-report"></a>Como acessar o relatório de inteligência de ameaças?
Você pode examinar os alertas atuais observando o bloco **Alertas de segurança** . Abra o portal do Azure e siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No painel Central de Segurança, você verá o bloco **Alertas de segurança** .
2. Clique no bloco para abrir a folha **Alertas de segurança** que contém mais detalhes sobre os alertas e clique no alerta de segurança sobre o qual você deseja obter mais informações.

    ![Alertas de segurança](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Nesse caso, a folha **processo suspeito executado** mostra os detalhes sobre o alerta, conforme mostrado na figura abaixo:

    ![Detalhes do alerta de segurança](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. A quantidade de informações disponíveis para cada alerta de segurança varia de acordo com o tipo de alerta. No campo **relatórios** , você tem um link para o relatório de inteligência contra ameaças. Clique nele e outra janela do navegador será exibida com o arquivo PDF.

   ![Seleção de armazenamento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Aqui você pode baixar o PDF para esse relatório e ler mais sobre o problema de segurança detectado e executar ações com base nas informações fornecidas.

## <a name="see-also"></a>Confira também
Neste documento, você aprendeu como os Relatórios de Inteligência de Ameaças da Central de Segurança do Azure podem ajudar durante uma investigação sobre alertas de segurança. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Guia de planejamento e operações da central de segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerenciar e responder aos alertas de segurança.
* [Manipulando incidente de segurança na central de segurança do Azure](security-center-incident.md)