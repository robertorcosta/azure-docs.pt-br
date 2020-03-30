---
title: Remediar recomendações no Azure Security Center | Microsoft Docs
description: Este artigo explica como corrigir recomendações no Azure Security Center para proteger seus recursos e cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603505"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Recomendações de correção na Central de Segurança do Azure

Recomendações dão sugestões de como proteger melhor seus recursos. Você implementa uma recomendação seguindo as etapas de remediação previstas na recomendação.

## <a name="remediation-steps"></a>Etapas de remediação<a name="remediation-steps"></a>

Depois de revisar todas as recomendações, decida qual remediar primeiro. Recomendamos que você use o [impacto secure score](security-center-recommendations.md#monitor-recommendations) para ajudar a priorizar o que fazer primeiro.

1. Na lista, clique na recomendação.

1. Siga as instruções na seção **de etapas de Remediação.** Cada recomendação tem seu próprio conjunto de instruções. A captura de tela a seguir mostra etapas de remediação para configurar aplicativos para permitir apenas tráfego em HTTPS.

    ![Detalhes da recomendação](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Uma vez concluída, uma notificação aparece informando se a remediação foi bem sucedida.

## <a name="quick-fix-remediation"></a>Correção de Correção Rápida<a name="one-click"></a>

O Quick Fix permite que você remedia rapidamente uma recomendação sobre vários recursos. Só está disponível para recomendações específicas. O Quick Fix simplifica a remediação e permite que você aumente rapidamente seu Secure Score, melhorando a segurança do seu ambiente.

Para implementar a correção quick fix:

1. Da lista de recomendações que possuem o **Quick Fix!** rótulo, clique na recomendação.

    [![Selecione Correção rápida!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na guia **Recursos insalubres,** selecione os recursos em que deseja implementar a recomendação e clique em **Remediar**.

    > [!NOTE]
    > Alguns dos recursos listados podem ser desativados, porque você não tem as permissões apropriadas para modificá-los.

1. Na caixa de confirmação, leia os detalhes e implicações da remediação.

    ![Correção rápida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > As implicações estão listadas na caixa cinza na janela **Recursos de correção** que se abre após clicar em **Remediar**. Eles listam quais mudanças acontecem ao prosseguir com a correção quick fix.

1. Insira os parâmetros relevantes, se necessário, e aprove a remediação.

    > [!NOTE]
    > Pode levar vários minutos após a conclusão da remediação para ver os recursos na guia **Recursos Saudáveis.** Para visualizar as ações de remediação, verifique o [registro de atividades](#activity-log).

1. Uma vez concluída, uma notificação aparece informando se a remediação foi bem sucedida.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Login de correção rápida no registro de atividades<a name="activity-log"></a>

A operação de remediação usa uma implantação de modelo ou chamada de API REST PATCH para aplicar a configuração no recurso. Essas operações estão registradas no [registro de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Recomendações com correção de correção rápida

|Recomendação|Implicação|
|---|---|
|A auditoria em servidores SQL deve ser ativada|Essa ação permitirá a auditoria SQL nesses servidores e em seus bancos de dados. <br>**Observação**: <ul><li>Para cada região dos servidores SQL selecionados, uma conta de armazenamento para salvar logs de auditoria será criada e compartilhada por todos os servidores dessa região.</li><li>Para garantir uma auditoria adequada, não exclua ou renomeie o grupo de recursos ou as contas de armazenamento.</li></ul>|
|A Segurança de Dados Avançada deve ser habilitada nas instâncias gerenciadas do SQL|Essa ação permitirá o ADS (SQL Advanced Data Security, segurança de dados avançados) do SQL nas instâncias selecionadas gerenciadas pelo SQL. <br>**Observação**: <ul><li>Para cada região e grupo de recursos das instâncias gerenciadas sql selecionadas, uma conta de armazenamento para salvar resultados de varredura será criada e compartilhada por todas as instâncias daquela região.</li><li> O ADS é cobrado a US$ 15 por instância gerenciada pelo SQL.</li></ul>|
|A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL|Essa ação permitirá a avaliação de vulnerabilidade sql nas instâncias gerenciadas pelo SQL selecionadas. <br>**Observação**:<ul><li>A Avaliação de Vulnerabilidade sql faz parte do pacote ADS (SQL Advanced Data Security). Se o ADS ainda não estiver habilitado, ele será ativado automaticamente na instância gerenciada.</li><li>Para cada região e grupo de recursos das instâncias gerenciadas sql selecionadas, uma conta de armazenamento para armazenar resultados de varredura será criada e compartilhada por todas as instâncias daquela região.</li><li>O ADS é cobrado a $15 por servidor SQL.</li></ul>||
|A segurança avançada de dados deve ser ativada em seus servidores SQL|Essa ação permitirá o ADS (Advanced Data Security, segurança de dados avançado) nesses servidores selecionados e em seus bancos de dados. <br>**Observação**:<ul><li>Para cada região e grupo de recursos dos servidores SQL selecionados, uma conta de armazenamento para armazenar resultados de varredura será criada e compartilhada por todos os servidores daquela região.<</li><li>O ADS é cobrado a $15 por servidor SQL.</li></ul>||
|A avaliação de vulnerabilidades deve ser ativada em seus servidores SQL|Essa ação permitirá a Avaliação de Vulnerabilidade sql nesses servidores selecionados e seus bancos de dados. <br>**Observação**:<ul><li>A Avaliação de Vulnerabilidade sql faz parte do pacote ADS (SQL Advanced Data Security). Se o ADS ainda não estiver habilitado, ele será ativado automaticamente no servidor SQL.</li><li>Para cada região e grupo de recursos dos servidores SQL selecionados, uma conta de armazenamento para armazenar resultados de varredura será criada e compartilhada por todas as instâncias daquela região.</li><li>O ADS é cobrado a $15 por servidor SQL.</li></ul>||
|A criptografia de dados transparente em bancos de dados SQL deve ser ativada|Essa ação permite o TDE (SQL Database Database Transparent Data Encryption, criptografia de dados transparente) nos bancos de dados selecionados. <br>**Nota**: Por padrão, as chaves TDE gerenciadas pelo serviço serão usadas.
|A transferência segura para contas de armazenamento deve ser habilitada|Esta ação atualiza a segurança da sua conta de armazenamento para permitir apenas solicitações por conexões seguras. (HTTPS). <br>**Observação**:<ul><li>Quaisquer solicitações usando HTTP serão rejeitadas.</li><li>Quando você estiver usando o serviço de arquivos Azure, a conexão sem criptografia falhará, incluindo cenários usando SMB 2.1, SMB 3.0 sem criptografia e alguns sabores do cliente Linux SMB. Saiba mais.</li></ul>|
|Aplicativo Web deve ser acessível somente por HTTPS|Esta ação redirecionará todo o tráfego de HTTP para HTTPS, nos recursos selecionados. <br>**Observação**:<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os usuários que têm um domínio personalizado precisam verificar se configuraram um certificado SSL.</li><li>Certifique-se de que os firewalls de aplicativos web e de pacotes protejam o serviço do aplicativo, permitam o encaminhamento de sessões HTTPS.</li></ul>|
|O aplicativo de funções deve ser acessível apenas por HTTPS|Esta ação redirecionará todo o tráfego de HTTP para HTTPS, nos recursos selecionados. <br>**Observação**:<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os usuários que têm um domínio personalizado precisam verificar se configuraram um certificado SSL.</li><li>Certifique-se de que os firewalls de aplicativos web e de pacotes protejam o serviço do aplicativo, permitam o encaminhamento de sessões HTTPS.</li></ul>|
|O aplicativo de API só deve estar acessível via HTTPS|Esta ação redirecionará todo o tráfego de HTTP para HTTPS, nos recursos selecionados. <br>**Observação**:<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os usuários que têm um domínio personalizado precisam verificar se configuraram um certificado SSL.</li><li>Certifique-se de que os firewalls de aplicativos web e de pacotes protejam o serviço do aplicativo, permitam o encaminhamento de sessões HTTPS.</li></ul>|
|Depuração remota deve ser desativada para o aplicativo da Web|Esta ação desativa a depuração remota.|
|A depuração remota deve ser desativada para o aplicativo de funções|Esta ação desativa a depuração remota.|
|A depuração remota deve ser desligada para o aplicativo de API|Esta ação desativa a depuração remota.|
|O CORS não deve permitir que todos os recursos acessem seu aplicativo Web|Essa ação bloqueia que outros domínios acessem seu Aplicativo Web. Para permitir domínios específicos, insira-os no campo Origens Permitidas (separados por commas). <br>**Nota**: Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: 'Origens permitidas'|
|O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo de funções|Esta ação bloqueia outros domínios de acessar seu aplicativo de função. Para permitir domínios específicos, insira-os no campo Origens Permitidas (separados por commas). <br>**Nota**: Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: 'Origens permitidas'|
|O CORS não deve permitir que todos os recursos tenham acesso ao seu Aplicativo de API|Essa ação bloqueia que outros domínios acessem seu aplicativo de API. Para permitir domínios específicos, insira-os no campo Origens Permitidas (separados por commas). <br>**Nota**: Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: 'Origens permitidas'|
|O agente de monitoramento deve ser ativado em suas máquinas virtuais|Esta ação instala um agente de monitoramento nas máquinas virtuais selecionadas. Selecione um espaço de trabalho para o agente relatar.<ul><li>Se sua política de atualização estiver definida como automática, ela será implantada em novas instâncias existentes.</li><li>Se sua política de atualização estiver definida como manual e você quiser instalar o agente nas instâncias existentes, selecione a opção caixa de seleção. [Saiba mais](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Os logs de diagnóstico no Key Vault deve estar habilitados|Esta ação permite registros de diagnóstico em cofres-chave. Os registros e métricas de diagnóstico são salvos no espaço de trabalho selecionado.|
|Os registros de diagnóstico no ônibus de serviço devem ser ativados|Esta ação permite registros de diagnóstico no ônibus de serviço. Os registros e métricas de diagnóstico são salvos no espaço de trabalho selecionado.|

## <a name="next-steps"></a>Próximas etapas

Neste documento, foi mostrado como remediar recomendações no Security Center. Para saber mais sobre o Security Center, consulte os seguintes tópicos:

* [Definindo políticas de segurança no Azure Security Center](tutorial-security-policy.md) - Saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
