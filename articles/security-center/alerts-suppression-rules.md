---
title: Usar regras de supressão de alertas para suprimir falsos positivos ou outros alertas de segurança indesejados na central de segurança do Azure.
description: Este artigo explica como usar as regras de supressão da central de segurança do Azure para ocultar alertas de segurança indesejados.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 5a5d6a96bd73a67c2611e7f334eba3daeda031ba
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007694"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>Suprimindo alertas da proteção contra ameaças da central de segurança do Azure

Esta página explica como você pode usar as regras de supressão de alertas para suprimir falsos positivos ou outros alertas de segurança indesejados na central de segurança do Azure.

## <a name="availability"></a>Disponibilidade

- Estado da versão: **Visualização**
- Funções necessárias: administrador de segurança e proprietário podem criar/excluir regras. Leitor de segurança e leitor podem exibir regras.
- Nuvens: todos (global, nacional, governo e soberanas)


## <a name="introduction-to-suppression-rules"></a>Introdução às regras de supressão

Os componentes de proteção contra ameaças da central de segurança do Azure detectam ameaças em qualquer área do ambiente e geram alertas de segurança.

Quando um único alerta não é interessante ou relevante, você pode descartá-lo manualmente. Como alternativa, use o recurso de regras de supressão para ignorar automaticamente alertas semelhantes no futuro. Normalmente, você usaria uma regra de supressão para:

- suprimir alertas que você identificou como falsos positivos

- suprimir alertas que estão sendo disparados com muita frequência para serem úteis

Suas regras de supressão definem os critérios para os quais os alertas devem ser automaticamente ignorados.

> [!CAUTION]
> Suprimir alertas de segurança reduz a proteção contra ameaças da central de segurança. Você deve verificar cuidadosamente o impacto potencial de qualquer regra de supressão e monitorá-la ao longo do tempo.

[![Página de alertas de segurança da central de segurança do Azure com opções de supressão de alertas](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>Criando uma regra de supressão

Há algumas maneiras pelas quais você pode criar regras para suprimir alertas de segurança indesejados:

- Para suprimir alertas no nível do grupo de gerenciamento, use Azure Policy

- Para suprimir alertas no nível de assinatura, você pode usar o portal do Azure ou a API REST, conforme explicado abaixo

As regras de supressão só podem ignorar alertas que já foram disparados nas assinaturas selecionadas.

Para criar uma regra diretamente no portal do Azure:

1. Na página alertas de segurança da central de segurança:

    - Localize o alerta específico que você não deseja mais ver e, no menu de reticências (...) do alerta, selecione **criar regra de supressão**:

        [![Opção * * Criar regra de supressão * *](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - Ou então, selecione o link **regras de supressão** na parte superior da página e, na página regras de supressão, selecione **criar nova regra de supressão**:

        ![Botão Criar nova regra de supressão * *](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. No painel nova regra de supressão, insira os detalhes da nova regra.

    - Sua regra pode ignorar o alerta em **todos os recursos** para que você não obtenha nenhum alerta como este no futuro. 
    
    - Sua regra pode ignorar o alerta **em critérios específicos** – quando ele se relaciona a um endereço IP específico, nome do processo, conta de usuário, recurso do Azure ou local.

    > [!TIP]
    > Se você abriu a página nova regra a partir de um alerta específico, o alerta e a assinatura serão automaticamente configurados em sua nova regra. Se você usou o link **criar nova regra de supressão** , as assinaturas selecionadas corresponderão ao filtro atual no Portal.

    [![Painel de criação de regras de supressão](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. Insira os detalhes da regra:

    - **Nome** -um nome para a regra. Os nomes de regra devem começar com uma letra ou um número, ter entre 2 e 50 caracteres e não conter símbolos diferentes de traços (-) ou sublinhados (_). 
    - **Estado** -habilitado ou desabilitado.
    - **Motivo** : selecione um dos motivos internos ou "outros" se eles não atenderem às suas necessidades.
    - **Data de validade** -uma data e hora de término para a regra. As regras podem ser executadas por até seis meses.

1. Opcionalmente, teste a regra usando o botão **simular** para ver quantos alertas teriam sido ignorados se essa regra estivesse ativa.

1. Salve a regra. 

## <a name="editing-suppression-rules"></a>Editando regras de supressão

Para editar as regras que você criou, use a página regras de supressão.

1. Na página alertas de segurança da central de segurança, selecione o link **regras de supressão** na parte superior da página.

1. A página regras de supressão é aberta, listando todas as regras disponíveis de acordo com as assinaturas selecionadas no momento. 

    [![Lista de regras de supressão](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Para editar uma única regra, abra o menu de reticências (...) da regra e selecione **Editar**.

1. Faça as alterações necessárias e selecione **aplicar**. 

## <a name="deleting-suppression-rules"></a>Excluindo regras de supressão

Para excluir uma ou mais regras que você criou, use a página regras de supressão.

1. Na página alertas de segurança da central de segurança, selecione o link **regras de supressão** na parte superior da página.

1. A página regras de supressão é aberta, listando todas as regras disponíveis de acordo com as assinaturas selecionadas no momento. 

1. Para excluir uma única regra, abra o menu de reticências (...) da regra e selecione **excluir**.

1. Para excluir várias regras, marque as caixas de seleção das regras a serem excluídas e selecione **excluir**.

    ![Excluindo uma ou mais regras de supressão](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>Exibindo alertas que foram suprimidos

Os alertas que corresponderem às regras de supressão habilitadas ainda serão gerados, mas seu estado será definido como **ignorado**. Você pode ver o estado na portal do Azure ou, no entanto, acessar os alertas de segurança da central de segurança. 

> [!TIP]
> O [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) não criará incidentes para alertas suprimidos. Para outros SIEMs, você pode filtrar alertas suprimidos usando o estado dos alertas (' ignorado ').

Use o filtro da central de segurança para exibir alertas que foram ignorados por suas regras.

* Na página alertas de segurança da central de segurança, abra as opções de filtro e selecione **ignorado**.  

   [![Exibindo alertas ignorados](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>Usando a API para criar e gerenciar regras de supressão

Você pode criar, exibir ou excluir regras de supressão de alerta por meio da API REST da central de segurança. 

Os métodos HTTP relevantes para regras de supressão na API REST são:

- **Put**: para criar ou atualizar uma regra de supressão em uma assinatura especificada.

- **Obter**:

    - Para listar todas as regras configuradas para uma assinatura especificada. Esse método retorna uma matriz das regras aplicáveis.

    - Para obter os detalhes de uma regra específica em uma assinatura especificada. Esse método retorna uma regra de supressão.

    - Para simular o impacto de uma regra de supressão ainda na fase de design. Essa chamada identifica qual dos seus alertas existentes teria sido ignorado se a regra estivesse ativa.

- **Excluir**: exclui uma regra existente (mas não altera o status dos alertas já ignorados por ela).

Para obter detalhes completos e exemplos de uso, consulte a [documentação da API](https://docs.microsoft.com/api/securitycenter/). 


## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu as regras de supressão na central de segurança do Azure que descartam automaticamente os alertas indesejados.

Para obter mais informações sobre alertas de segurança na central de segurança do Azure, consulte as seguintes páginas:

- [Alertas de segurança e a intenção de Kill Chain](alerts-reference.md) -um guia de referência para os alertas de segurança que você pode ver no módulo de proteção contra ameaças da central de segurança do Azure.
- [Proteção contra ameaças na central de segurança do Azure](threat-protection.md) -uma descrição dos vários aspectos do seu ambiente monitorado pelo módulo proteção contra ameaças da central de segurança do Azure.