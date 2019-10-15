---
title: Tutorial da Central de segurança do Azure - Proteger seus recursos com a Central de Segurança do Azure | Microsoft Docs
description: Este tutorial mostra como configurar uma política de acesso Just-In-Time de VM e uma política de controle de aplicativo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 8cb07f3447e50528a94811f33a2142086f698586
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996327"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: Proteger seus recursos com a Central de Segurança do Azure
A Central de Segurança limita a exposição a ameaças por meio de controles de acesso e de aplicativo a fim de bloquear atividades mal-intencionadas. O acesso de VM (máquina virtual) JIT (Just-In-Time) reduz a exposição a ataques permitindo que você negue o acesso persistente às VMs. Em vez disso, você fornece acesso controlado e auditado às VMs somente quando for necessário. Controles de aplicativo adaptáveis ajudam a proteger VMs contra malware, controlando quais aplicativos podem ser executados em suas VMs. A Central de Segurança usa o aprendizado de máquina para analisar os processos em execução na VM e ajuda a aplicar regras de lista de permissões usando essa inteligência.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Configurar uma política de acesso de VM Just-In-Time
> * Configurar uma política de controle de aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer os recursos abordados neste tutorial, você deve estar em um tipo de preço da Central de Segurança Padrão. Você pode experimentar a Central de Segurança Standard sem nenhum custo. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Integração da sua assinatura do Azure à Central de Segurança Standard](security-center-get-started.md) orienta você sobre como fazer upgrade para Standard.

## <a name="manage-vm-access"></a>Gerenciar acesso à VM
O acesso JIT à VM pode ser usado para bloquear o tráfego de entrada às suas VMs do Azure, reduzindo a exposição aos ataques, fornecendo acesso fácil para conectar às VMs quando necessário.

As portas de gerenciamento não precisam ficar abertas o tempo todo. Elas só precisam ser abertas enquanto você estiver conectado à VM, por exemplo, para realizar tarefas de manutenção ou gerenciamento. Quando o Just-In-Time está habilitado, a Central de Segurança usa as regras do NSG (Grupo de Segurança de Rede), que restringem o acesso às portas de gerenciamento, de modo que elas não se tornem alvo de invasores.

1. No menu principal da Central de Segurança, selecione **Acesso Just-In-Time à VM** em **DEFESA AVANÇADA DE NUVEM**.

   ![Acesso à VM Just-In-Time][1]

   O **Acesso à VM Just-In-Time** fornece informações sobre o estado das suas VMs:

   - **Configurado**: VMs que foram configuradas para dar suporte ao acesso JIT à VM.
   - **Recomendado**: VMs que podem oferecer suporte ao acesso JIT à VM, mas não foram configuradas para isso.
   - **Nenhuma recomendação** – as razões que podem fazer com que uma VM não seja recomendada são:

     - NSG ausente: a solução Just-In-Time exige que um NSG esteja em vigor.
     - VM Clássica: o acesso Just-In-Time à VM pela Central de Segurança atualmente oferece suporte apenas às VMs implantadas por meio do Azure Resource Manager.
     - Outros: uma VM entra nessa categoria se a solução Just-In-Time estiver desativada na política de segurança da assinatura ou do grupo de recursos, ou se estiver faltando um IP público da VM e ela não tiver um NSG em vigor.

2. Selecione uma VM recomendada e clique em **Habilitar JIT em 1 VM** para configurar uma política Just-In-Time para essa VM:

   Você pode salvar as portas padrão recomendadas pela Central de Segurança ou pode adicionar e configurar uma nova porta na qual você deseja habilitar a solução Just-In-Time. Neste tutorial, vamos adicionar uma porta selecionando **Adicionar**.

   ![Adicionar configuração de porta][2]

3. Em **Adicionar configuração de porta**, identifique:

   - A porta
   - O tipo de protocolo
   - IPs de origem permitidos: intervalos de IP com permissão para obter acesso mediante solicitação aprovada
   - Tempo máximo da solicitação: período máximo durante o qual uma porta específica pode ser aberta

4. Selecione **OK** para salvar.

## <a name="harden-vms-against-malware"></a>Proteger VMs contra malware
Os controles de aplicativo adaptáveis ajudam você a definir um conjunto de aplicativos que podem ser executados em grupos de recursos configurados que, entre outros benefícios, ajuda você a proteger suas VMs contra malware. A Central de Segurança usa o aprendizado de máquina para analisar os processos em execução na VM e ajuda a aplicar regras de lista de permissões usando essa inteligência.

1. Volte para o menu principal da Central de Segurança. Em **DEFESA AVANÇADA DE NUVEM**, selecione **Controles de aplicativo adaptáveis**.

   ![Controles de aplicativo adaptáveis][3]

   A seção **Grupos de Recursos** contém três guias:

   - **Configurado**: lista de grupos de recursos contendo as VMs que foram configuradas com controle de aplicativo.
   - **Recomendados**: lista de grupos de recursos para os quais o controle de aplicativos é recomendado.
   - **Sem recomendações**: lista de grupos de recursos contendo VMs sem nenhuma recomendação de controle de aplicativo. Por exemplo, VMs que sempre têm aplicativos mudando e que ainda não estão estáveis.

2. Selecione a guia **Recomendado** para obter uma lista de grupos de recursos com as recomendações de controle de aplicativo.

   ![Recomendações de controle de aplicativo][4]

3. Selecione um grupo de recursos para abrir a opção **Criar regras de controle de aplicativo**. Em **Selecionar VMs**, revise a lista de VMs recomendadas e desmarque as que não devem receber o controle de aplicativo. Em **Selecionar processos para regras de lista de permissões**, revise a lista de aplicativos recomendados e desmarque o que não deve ser aplicado. A lista inclui:

   - **NAME**: o caminho completo do aplicativo
   - **PROCESSOS**: quantos aplicativos residem em cada caminho
   - **COMUM**: "Sim" indica que esses processos foram executados na maioria das VMs no grupo de recursos
   - **EXPLORÁVEL**: um ícone de aviso indica se os aplicativos podem ser usados por um invasor para ignorar a lista de permissões de aplicativos. É recomendável examinar esses aplicativos antes da aprovação.

4. Após concluir suas seleções, selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar recursos
Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e os guias de início rápido subsequentes, continue executando a camada Standard e mantenha o provisionamento automático habilitado. Se você não planejar continuar ou quiser retornar para a camada Gratuita:

1. Retorne ao menu principal da Central de Segurança e selecione a **Política de segurança**.
2. Selecione a assinatura ou a política que você deseja retornar para Gratuita. A **Política de segurança** abre.
3. Em **COMPONENTES DE POLÍTICA**, selecione **Tipo de preços**.
4. Selecione **Gratuita** para alterar a assinatura da camada Standard para a camada Gratuita.
5. Clique em **Salvar**.

Se quiser desabilitar o provisionamento automático:

1. Retorne ao menu principal da Central de Segurança e selecione **Política de segurança**.
2. Selecione a assinatura em que você deseja desabilitar o provisionamento automático.
3. Em **Política de segurança – Coleta de dados**, selecione **Desativar** em **Integração** para desabilitar o provisionamento automático.
4. Clique em **Salvar**.

>[!NOTE]
> Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure em que o agente tenha sido provisionado. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.
>

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a limitar sua exposição a ameaças fazendo o seguinte:

> [!div class="checklist"]
> * Configurando uma política de acesso Just-In-Time à VM para fornecer acesso controlado e auditado às VMs somente quando for necessário
> * Configurando uma política de controles de aplicativo adaptável para controlar quais aplicativos podem ser executados em suas VMs

Avance para o próximo tutorial para saber mais sobre como responder a incidentes de segurança.

> [!div class="nextstepaction"]
> [Tutorial: Responder a alertas de segurança](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
