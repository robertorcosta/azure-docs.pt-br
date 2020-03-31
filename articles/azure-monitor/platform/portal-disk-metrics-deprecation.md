---
title: Depreciação de métricas de disco no portal Azure | Microsoft Docs
description: Saiba quais métricas de disco foram depreciadas e como atualizar seus alertas métricos para usar novas métricas.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299797"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Depreciação de métricas de disco no portal Azure

Métricas depreciadas relacionadas ao disco serão removidas em breve do portal Azure. Uma nova versão de cada métrica depreciada está disponível para você usar. Este artigo mostra quais métricas são novas e como atualizar seus alertas métricos para usá-los.

## <a name="list-of-new-metrics"></a>Lista de novas métricas

Esta tabela mapeia cada métrica depreciada para sua nova métrica correspondente. 

|Métrica preterida|Nova métrica (substituição)|
|----|----|
|QD do disco de dados (preterido)|Profundidade da fila de discos de dados (visualização)|
|Leitura de disco de dados Bytes/Seg (preterido)|Bytes de Leitura do Disco de Dados/s (Visualização)|
|Operações de leitura de disco de dados/seg (preterido)|Operações de Leitura do Disco de Dados/s (Visualização)|
|Bytes de gravação de disco de dados/seg (preteridos)|Bytes de Gravação do Disco de Dados/s (Visualização)|
|Operações de gravação de disco de dados/seg (preterido)|Operações de Gravação do Disco de Dados/s (Visualização)|
|OS QD (Preterido)|Profundidade da fila do SISTEMA OPERACIONAL (Visualização)|
|OS Ler Bytes/Seg (Preterido)|Os Ler Bytes/Seg (Visualização)|
|Operações de leitura do SISTEMA OPERACIONAL/Seg (Preteridas)|Operações de leitura do SISTEMA OPERACIONAL/Seg (Visualização)|
|OS Write Bytes/Sec (Preterido)|OS Write Bytes/Sec (Visualização)|
|Operações de gravação do SISTEMA OPERACIONAL/Seg (preteridos)|Operações de gravação/seg do SISTEMA OPERACIONAL (Visualização)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrar métricas em seus alertas métricos

Atualize seus alertas métricos para usar novas métricas.

1. No portal Azure, procure **por Alertas**. Em seguida, na seção **Serviços,** escolha **Alertas**.

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na página **Alertas,** escolha o botão **Gerenciar regras de alerta.** 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Na lista de paradas do **grupo Recursos,** selecione a caixa de seleção **Máquinas Virtuais** e, na lista de paradas do **tipo Sinal,** selecione a caixa de seleção **Métricas.** 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Na lista de métricas, identifique condições que se relacionam com discos. Clique no nome da regra. 

   O nome aparece como um hiperlink na coluna **Nome** da tabela.

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Na seção **Condições** da página de **gerenciamento de regras,** clique na condição do alerta. 

   A condição aparece como um hiperlink.  

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   A página **Configurar lógica de sinal** é exibida e as configurações da condição aparecem na seção Lógica **Alerta** dessa página.

6. Faça um registro dessas configurações, pois elas desaparecerão quando você remover a métrica depreciada.

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Considere capturar essas configurações em uma captura de tela ou em um arquivo de texto. 

7. Clique no **link Voltar para sinalizar seleção.**

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na página **Configurar lógica de sinal,** escolha a métrica de substituição apropriada (nova métrica). Use a [tabela](#update-metrics) que aparece anteriormente neste artigo para identificar o nome da nova métrica.

   > [!TIP] 
   > Comece a digitar na barra de pesquisa para reduzir a lista de nomes métricos. 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Escolha o **botão Feito.** 

   > [!div class="mx-imgBorder"]
   > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Comprometa suas alterações escolhendo o botão **Salvar.** 

    > [!div class="mx-imgBorder"]
    > ![Descrição da imagem](./media/portal-disk-metrics-deprecation/save-new-metric.png)






