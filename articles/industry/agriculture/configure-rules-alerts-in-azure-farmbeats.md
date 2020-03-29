---
title: Configurar regras e gerenciar alertas
description: Descreve como configurar regras e gerenciar alertas no FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482979"
---
# <a name="configure-rules-and-manage-alerts"></a>Configurar regras e gerenciar alertas

O Azure FarmBeats permite criar regras baseadas na lógica de negócios, além dos dados dos sensores que fluem dos sensores e dispositivos implantados em sua fazenda. As regras disparam alertas no sistema sempre que os valores dos sensores cruzam um valor limiar. Ao visualizar e analisar os alertas criados após os valores de limite, você pode agir rapidamente em quaisquer problemas e obter soluções necessárias.

## <a name="create-rule"></a>Criar regra

1. Na página inicial, vá para **Regras**.
2. Selecione **Nova regra**. A janela Nova regra é exibida.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Digite o **nome da regra** e a descrição da **regra** e, em seguida, selecione uma fazenda no menu suspenso **Selecionar Fazenda.**
4. Digite o nome da sua fazenda para selecionar a seção Fazenda e **Condições** aparece na mesma janela.  

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Em **Condições,** insira os valores de **Medida,** **Operador** e **Valor.**
6. Digite o nome da medida no menu suspenso **Medida.**
7. Selecione **+Adicionar Condição** para adicionar mais condições à regra.
8. Selecione o **nível de gravidade**.
9. Em **Ação,** ligue o botão de alternação ativado pelo **e-mail** para ativar alertas de e-mail.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Digite os **endereços de e-mail** para os quais deseja enviar o alerta de e-mail, juntamente com o **Assunto de E-mail** e **Notas Adicionais**.  
11. No Status de **regra,** ligue o botão Alternado **Ativado** para ativar ou desativar a regra.
    Você pode visualizar o número de dispositivos que serão afetados pela regra.
12. Selecione **Aplicar** para criar a regra.

## <a name="view-rule"></a>Ver regra

A página **Fazenda** exibe a lista de regras disponíveis. Selecione um **nome de regra**. Uma janela exibe os seguintes detalhes aplicáveis à regra selecionada:
 - Nome da regra
 - Vincule-se à fazenda à qual a regra está associada
 - Data da criação
 - Data da última atualização
 - Nível de severidade
 - Status da regra
 - Lista de condições  
 - Número de dispositivos afetados pela regra

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Editar regra

Para editar uma regra, siga estas etapas:

1. Na página inicial, selecione **Regras** no menu de navegação à esquerda.
   A janela de regras é exibida.
2. Selecione a regra para a qual deseja editar.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Selecione **Editar** na barra de ação, a janela **Editar regra** é exibida.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Altere o **nome da regra**e a descrição da **regra** e selecione uma fazenda no menu suspenso **Selecionar Fazenda.**
5. Digite o nome da sua fazenda para selecionar a fazenda e **As condições** aparecerão na mesma janela.  
6. Em **Condições,** **edite Medida,** **Operador** e **Valor**.
7. Digite o nome da medida no menu suspenso **Medida.**
8. Selecione **+Adicionar Condição** para adicionar/editar condições às regras.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selecione o **Nível de Gravidade .**  
10. Em **Ação,** ligue o botão de alternação ativado pelo **e-mail** para ativar alertas de e-mail.
11. Edite os **endereços de e-mail** que você deseja enviar o alerta de e-mail, juntamente com o **Assunto de e-mail** e **Notas Adicionais**.  
12. No Status de **regra,** ligue o botão Alternado **Ativado** para ativar ou desativar a regra.
Você pode visualizar o número de dispositivos que serão afetados por esta regra.
13. Selecione **Aplicar** para editar a regra.

## <a name="change-rule-status"></a>Alterar o status da regra

Para alterar o status de uma regra, siga estas etapas:

1. Na página inicial, selecione **Regras** no menu de navegação à esquerda. A janela de regras é exibida.
2. Selecione a regra para a qual deseja alterar o status.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selecione **'Alterar status'** da barra de ação. A **janela Alterar status** é exibida.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Alterar o status da regra usando o botão alternar **status** de alteração.
   Você pode visualizar o número de dispositivos que serão afetados pela Regra.
4. Selecione **Aplicar** para alterar o status da regra.

## <a name="delete-rule"></a>Excluir regra

Para excluir uma regra, siga estas etapas:

1. Na página inicial, selecione **Regras** no menu de navegação à esquerda. A janela de regras é exibida.
2. Selecione a regra para a qual deseja excluir.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Selecione **Excluir** na barra de ação.

    ![Projeto do FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. A caixa de diálogo Regra de **exclusão** é exibida. Selecione **Excluir**.
