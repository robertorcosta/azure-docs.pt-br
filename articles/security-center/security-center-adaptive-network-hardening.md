---
title: Endurecimento de rede adaptativa no Azure Security Center | Microsoft Docs
description: Aprenda a usar padrões de tráfego reais para endurecer as regras dos grupos de segurança da rede (NSG) e melhorar ainda mais sua postura de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385071"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Endurecimento de rede adaptativa no Centro de Segurança Azure
Saiba como configurar o Endurecimento de rede adaptativa no Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>O que é o Hardening de Rede Adaptativa?
Aplicar grupos de segurança de [rede (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) para filtrar o tráfego de e para os recursos, melhora sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui através do NSG é um subconjunto das regras do NSG definidas. Nesses casos, melhorar ainda mais a postura de segurança pode ser alcançado através do endurecimento das regras do NSG, com base nos padrões reais de tráfego.

O Endurecimento de Rede Adaptativa fornece recomendações para endurecer ainda mais as regras do NSG. Ele usa um algoritmo de aprendizado de máquina que fatores no tráfego real, configuração confiável conhecida, inteligência de ameaças e outros indicadores de compromisso e, em seguida, fornece recomendações para permitir o tráfego apenas a partir de tuplas ip/portas específicas.

Por exemplo, digamos que a regra existente do NSG é permitir o tráfego a partir de 140.20.30.30.10/24 na porta 22. A recomendação do Adaptive Network Hardening, com base na análise, seria estreitar o alcance e permitir o tráfego a partir de 140.23.30.10/29 – que é uma faixa IP mais estreita, e negar todo o tráfego para aquela porta.

>[!TIP]
> As recomendações de endurecimento de rede adaptativa são suportadas apenas nas seguintes portas específicas (tanto para UDP quanto para TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Visão de endurecimento de rede](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Exibir alertas e regras de endurecimento de rede adaptativa

1. No Security Center, selecione **O endurecimento de** -> **rede adaptativa de rede**. As VMs da rede estão listadas em três guias separadas:
   * **Recursos insalubres**: VMs que atualmente possuem recomendações e alertas que foram acionados pela execução do algoritmo de Endurecimento de Rede Adaptativa. 
   * **Recursos saudáveis**: VMs sem alertas e recomendações.
   * **Recursos não digitalizados**: VMs que o algoritmo de endurecimento de rede adaptativa não pode ser executado por uma das seguintes razões:
      * **VMs são VMs clássicas**: Apenas vMs do Azure Resource Manager são suportados.
      * **Não há dados suficientes**: Para gerar recomendações precisas de endurecimento de tráfego, o Security Center requer pelo menos 30 dias de dados de tráfego.
      * **A VM não é protegida pelo padrão ASC**: Apenas as VMs definidas para o nível de preços padrão do Security Center são elegíveis para este recurso.

     ![recursos insalubres](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Na guia **Recursos insalubres,** selecione uma VM para visualizar seus alertas e as regras de endurecimento recomendadas a serem aplicadas.

    ![alertas de endurecimento](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Revise e aplique regras recomendadas de Endurecimento de Rede Adaptativa

1. Na guia **Recursos insalubres,** selecione uma VM. Os alertas e as regras recomendadas de endurecimento estão listados.

     ![regras de endurecimento](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > A guia **Regras** lista as regras que o Adaptive Network Hardening recomenda adicionar. A guia **Alertas** lista os alertas gerados devido ao tráfego, fluindo para o recurso, que não está dentro da faixa de IP permitida nas regras recomendadas.

2. Se você quiser alterar alguns dos parâmetros de uma regra, você pode modificá-la, conforme explicado em [Modificar uma regra](#modify-rule).
   > [!NOTE]
   > Você também pode [excluir](#delete-rule) ou [adicionar](#add-rule) uma regra.

3. Selecione as regras que deseja aplicar no NSG e clique **em Impor**.

      > [!NOTE]
      > As regras impostas são adicionadas aos NSGs que protegem a VM. (Uma VM pode ser protegida por um NSG associado à sua NIC, ou à sub-rede em que a VM reside, ou ambos)

    ![impor regras](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Modificar uma <a name ="modify-rule"> </a> regra

Você pode querer modificar os parâmetros de uma regra que foi recomendada. Por exemplo, você pode querer alterar as faixas de IP recomendadas.

Algumas diretrizes importantes para modificar uma regra de endurecimento de rede adaptativa:

* Você pode modificar os parâmetros das regras de "permitir". 
* Você não pode mudar as regras de "permitir" que se tornem regras de "negar". 

  > [!NOTE]
  > A criação e modificação das regras de "negar" é feita diretamente no NSG. Para obter mais informações, consulte [Criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* A **Negar todas as** regras de tráfego é o único tipo de regra de "negar" que seria listada aqui, e não pode ser modificada. Você pode, no entanto, excluí-lo (ver [Excluir uma regra](#delete-rule)).
  > [!NOTE]
  > A **Negar todas as** regras de tráfego é recomendada quando, como resultado da execução do algoritmo, o Security Center não identifica o tráfego que deve ser permitido, com base na configuração existente do NSG. Portanto, a regra recomendada é negar todo o tráfego para a porta especificada. O nome deste tipo de regra é exibido como "*Sistema Gerado*". Após a aplicação desta regra, seu nome real no NSG será uma seqüência composta pelo protocolo, direção de tráfego, "DENY" e um número aleatório.

*Para modificar uma regra de endurecimento de rede adaptativa:*

1. Para modificar alguns dos parâmetros de uma regra, na guia **Regras,** clique nos três terros (...) no final da linha da regra e clique em **Editar**.

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Na janela **Editar regra,** atualize os detalhes que deseja alterar e clique **em Salvar**.

   > [!NOTE]
   > Depois de clicar **em Salvar,** você alterou com sucesso a regra. *No entanto, você não o aplicou ao NSG.* Para aplicá-la, você deve selecionar a regra na lista e clicar em **Impor** (conforme explicado na próxima etapa).

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Para aplicar a regra atualizada, na lista, selecione a regra atualizada e clique **em Impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Adicione uma <a name ="add-rule"> </a> nova regra

Você pode adicionar uma regra de "permitir" que não foi recomendada pelo Security Center.

> [!NOTE]
> Apenas regras de "permitir" podem ser adicionadas aqui. Se você quiser adicionar regras de "negar", você pode fazê-lo diretamente no NSG. Para obter mais informações, consulte [Criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Para adicionar uma regra de endurecimento de rede adaptativa:*

1. Clique em **Adicionar regra** (localizada no canto superior esquerdo).

   ![adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Na **janela Nova regra,** digite os detalhes e clique **em Adicionar**.

   > [!NOTE]
   > Depois de clicar **em Adicionar,** você adicionou com sucesso a regra e ela está listada com as outras regras recomendadas. No entanto, você não aplicou no NSG. Para ativá-la, você deve selecionar a regra na lista e clicar em **Impor** (conforme explicado na próxima etapa).

3. Para aplicar a nova regra, na lista, selecione a nova regra e clique **em Impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Exclua <a name ="delete-rule"> </a> uma regra

Quando necessário, você pode excluir uma regra recomendada para a sessão atual. Por exemplo, você pode determinar que a aplicação de uma regra sugerida pode bloquear o tráfego legítimo.

*Para excluir uma regra de endurecimento de rede adaptativa para a sessão atual:*

1. Na guia **Regras,** clique nos três terros (...) no final da linha da regra e clique em **Excluir**.  

    ![regras de endurecimento](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)