---
title: Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança | Microsoft Docs
description: " Saiba como usar as políticas de segurança e as recomendações da Central de Segurança do Azure para ajudar a atenuar um ataque de segurança. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603274"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança
Você pode reduzir as chances de um evento de segurança significativo configurando uma política de segurança e implementando as recomendações fornecidas pela Central de Segurança do Azure. Esse artigo mostra como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança. 

O Security Center executa automaticamente varreduras contínuas para analisar o estado de segurança dos recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que guiam você pelo processo de configuração dos controles de segurança necessários. O Security Center atualiza suas recomendações dentro de 24 horas, com as seguintes exceções:

- As recomendações de configuração de segurança do sistema operacional são atualizadas em até 48 horas
- As recomendações de problemas de proteção de endpoint são atualizadas em até 8 horas

## <a name="scenario"></a>Cenário
Este cenário mostra como usar a Central de Segurança para ajudar a reduzir as chances de um incidente de segurança monitorando as recomendações da Central de Segurança e executando ações. O cenário usa a empresa fictícia Contoso e funções apresentadas no [guia de planejamento e operações](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) da Central de Segurança. Neste cenário, estamos nos concentrando nas funções das seguintes personas:

![Funções de cenário](./media/security-center-using-recommendations/scenario-roles.png)

A Contoso migrou recentemente alguns dos seus recursos locais para o Azure. A Contoso deseja proteger seus recursos e reduzir a vulnerabilidade de seus recursos na nuvem.

## <a name="use-azure-security-center"></a>Usar a Central de Segurança do Azure
David, da segurança de TI da Contoso, já optou por integrar a Central de Segurança nas assinaturas da Contoso à Central de Segurança do Azure para evitar e detectar vulnerabilidades de segurança. 

A Central de Segurança analisa o estado de segurança dos recursos do Azure da Contoso e aplica as políticas de segurança padrão automaticamente. Quando o Security Center identifica potenciais vulnerabilidades de segurança, ele cria **recomendações** com base nos controles definidos na política de segurança. 

David executa o nível padrão do Azure Security, em todas as suas assinaturas para obter o conjunto completo de recomendações e recursos de segurança disponíveis. Jeff também embarca em todos os seus servidores locais existentes que ainda não foram migrados para a nuvem para que eles possam tirar proveito do suporte híbrido do Security Center em seus servidores [Windows](quick-onboard-windows-computer.md) e [Linux.](quick-onboard-linux-computer.md)

Jeff é proprietário da carga de trabalho de nuvem. Jeff é responsável por aplicar controles de segurança de acordo com as políticas de segurança da Contoso. 

Jeff realiza as seguintes tarefas:

- Monitorar as recomendações de segurança fornecidas pela Central de Segurança
- Avalie as recomendações de segurança e decida se elas devem aplicar ou rejeitar as recomendações.
- Aplicar as recomendações de segurança

### <a name="remediate-threats-using-recommendations"></a>Corrigir ameaças usando recomendações
Como parte de suas atividades diárias de monitoramento, Jeff entra no Azure e abre o Security Center. 

1. Jeff seleciona as assinaturas da carga de trabalho.

2. Jeff verifica o **Secure Score** para obter uma imagem geral de quão seguras são as assinaturas e vê que a pontuação é 548.

3. Jeff precisa decidir com quais recomendações deseja lidar primeiro. Assim, Jeff clica em Secure Score e começa a lidar com recomendações com base no quanto melhora seu [impacto no Secure Score](security-center-secure-score.md).

4. Como Jeff tem muitos servidores e VMs conectadas, ele decide se concentrar em **Computação e aplicativos**.

5. Quando Jeff **clica em Computação e aplicativos,** eles vêem uma lista de recomendações e as manipulam de acordo com o impacto do Secure Score.

6. Jeff tem várias VMs voltadas para a Internet, e como suas portas estão expostas, eles estão preocupados que um invasor possa ganhar controle sobre os servidores. Assim, Jeff opta por usar [**o acesso de VM just-in-time**](security-center-just-in-time.md).

Jeff continua a seguir as recomendações de alta e média prioridade, tomando decisões quanto à implementação. Para cada recomendação, Jeff analisa as informações detalhadas fornecidas pelo Security Center para entender quais recursos são impactados, qual é o impacto do Secure Score, o que cada recomendação significa e as etapas de remediação de como mitigar cada problema.

## <a name="conclusion"></a>Conclusão
Monitorar as recomendações na Central de Segurança ajuda a eliminar vulnerabilidades de segurança antes de uma violação ocorrer. Quando você remedia recomendações, seu Secure Score e a postura de segurança de suas cargas de trabalho melhoram. A Central de Segurança detecta automaticamente os novos recursos que você implanta, os avalia em relação à sua política de segurança e fornece novas recomendações para protegê-los.


## <a name="next-steps"></a>Próximas etapas
Certifique-se de que você tem em vigor um processo de monitoramento em que você verifica regularmente as recomendações na Central de Segurança para que possa proteger seus recursos ao longo do tempo.

Esse cenário mostrou como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança.

Saiba como responder a ameaças [com o gerenciamento e a resposta a alertas de segurança.](security-center-managing-and-responding-alerts.md)
