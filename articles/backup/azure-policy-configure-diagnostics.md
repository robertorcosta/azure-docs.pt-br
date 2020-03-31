---
title: Configure as configurações do Vault Diagnostics em escala
description: Configure as configurações do Log Analytics Diagnostics para todos os cofres em um determinado escopo usando a Diretiva Azure
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584499"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Configure as configurações do Vault Diagnostics em escala

A solução de relatórios fornecida pelo Azure Backup aproveita o Log Analytics (LA). Para que os dados de qualquer cofre seja enviado para Los Angeles, uma [configuração de diagnóstico](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) precisa ser criada para esse cofre.

Muitas vezes, adicionar uma configuração de diagnóstico manualmente por cofre pode ser uma tarefa complicada. Além disso, qualquer novo cofre criado também precisa ter configurações de diagnóstico ativadas para poder visualizar relatórios para este cofre. 

Para simplificar a criação de configurações de diagnóstico em escala (com LA como destino), o Azure Backup fornece uma [política azure incorporada](https://docs.microsoft.com/azure/governance/policy/). Esta política adiciona uma configuração de diagnóstico de LA a todos os cofres em um determinado grupo de assinatura ou recurso. As seções a seguir fornecem instruções sobre como usar esta política.

## <a name="supported-scenarios"></a>Cenários com suporte

* A diretiva pode ser aplicada de uma só vez a todos os cofres do Recovery Services em uma assinatura específica (ou a um grupo de recursos dentro da assinatura). O usuário que atribuir a diretiva precisa ter acesso 'Proprietário' à assinatura à qual a diretiva é atribuída.

* O ESPAÇO DE TRABALHO LA, conforme especificado pelo usuário (para o qual os dados de diagnóstico serão enviados) pode estar em uma assinatura diferente dos cofres aos quais a política é atribuída. O usuário precisa ter acesso 'Leitor', 'Contribuinte' ou 'Proprietário' à assinatura na qual existe o espaço de trabalho LA especificado.

* O escopo do Management Group não tem suporte.

* A política incorporada não está disponível atualmente nas nuvens nacionais.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Atribuindo a política incorporada a um escopo

Para atribuir a política para cofres no escopo necessário, siga as etapas abaixo:

1. Faça login no portal Azure e navegue até o **Painel de Políticas.**
2. Selecione **Definições** no menu à esquerda para obter uma lista de todas as políticas incorporadas em Recursos do Azure.
3. Filtrar a lista para **Categoria=Monitoramento**. Localize a diretiva nomeada **[Visualização]: Implante configurações de diagnóstico para o cofre de serviços de recuperação para o espaço de trabalho do Log Analytics para categorias específicas de recursos**.

![Lâmina de definição de política](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Clique no nome da apólice. Você será redirecionado para a definição detalhada desta política.

![Definição detalhada de políticas](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Clique no botão **Atribuir** na parte superior da lâmina. Isso o redireciona para a lâmina **Deatribuição de** política.

6. Em **Basics,** clique nos três apontados ao lado do campo **Escopo.** Isso abre uma lâmina de contexto correta onde você pode selecionar a assinatura para a política a ser aplicada. Você também pode selecionar opcionalmente um grupo de recursos, de modo que a diretiva seja aplicada apenas para cofres em um determinado grupo de recursos.

![Noções básicas de atribuição de políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Em **Parâmetros,** insira as seguintes informações:

* **Nome do perfil** - O nome que será atribuído às configurações de diagnóstico criadas pela diretiva.
* **Espaço de trabalho do Log Analytics** - O Espaço de Trabalho do Log Analytics ao qual a configuração de diagnóstico deve ser associada. Os dados de diagnóstico de todos os cofres no escopo da atribuição de políticas serão empurrados para o espaço de trabalho LA especificado.

* **Nome da tag de exclusão (opcional) e valor da tag de exclusão (opcional)** - Você pode optar por excluir cofres contendo um determinado nome de tag e valor da atribuição de diretiva. Por exemplo, se você **não** quiser que uma configuração de diagnóstico seja adicionada aos cofres que tenham uma tag 'isTest' definida no valor 'sim', você deve inserir 'isTest' no campo **Nome da tag de exclusão** e 'sim' no campo Valor da tag de **exclusão.** Se qualquer (ou ambos) desses dois campos forem deixados vazios, a política será aplicada a todos os cofres relevantes, independentemente das etiquetas que contiverem.

![Parâmetros de atribuição de políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Crie uma tarefa de remediação** - Uma vez que a diretiva é atribuída a um escopo, quaisquer novos cofres criados nesse escopo obtêm automaticamente as configurações de diagnóstico de LA configuradas (dentro de 30 minutos a partir do momento da criação do cofre). Para adicionar uma configuração de diagnóstico aos cofres existentes no escopo, você pode acionar uma tarefa de remediação na hora da atribuição da diretiva. Para ativar uma tarefa de remediação, selecione a caixa de seleção **Criar uma tarefa de remediação**. 

![Remediação de Atribuição de Políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navegue até a guia **'Revisar+Criar'** e clique **em Criar**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>quais condições a tarefa de remediação se aplicará a um cofre?

A tarefa de remediação é aplicada a cofres que não estão em conformidade de acordo com a definição da política. Um cofre não está em conformidade se satisfizer qualquer uma das seguintes condições:

* Nenhuma configuração de diagnóstico está presente para o cofre.
* As configurações de diagnóstico estão presentes para o cofre, mas nenhuma das configurações tem **todos os** eventos específicos de recursos habilitados com LA como destino e **recurso específico** selecionado no alternador. 

Assim, mesmo que um usuário tenha um cofre com o evento AzureBackupReport ativado no modo AzureDiagnostics (que é suportado por Relatórios de Backup), a tarefa de remediação ainda se aplicará a este cofre, uma vez que o modo específico de recursos é a maneira recomendada de criar configurações de diagnóstico, [daqui para frente](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event).

Além disso, se um usuário tiver um cofre com apenas um subconjunto dos seis eventos específicos de recursos ativados, a tarefa de remediação será aplicada para este cofre, uma vez que os Relatórios de backup funcionarão como esperado apenas se todos os seis eventos específicos de recursos estiverem habilitados.

> [!NOTE]
>
> Se um cofre tiver uma configuração de diagnóstico existente com um **subconjunto de** categorias específicas de recursos habilitadas, configurados para enviar dados para um espaço de trabalho LA específico, diga 'Workspace X', então a tarefa de remediação falhará (apenas para esse cofre) se o destino LA Workspace fornecido na atribuição De política for o **mesmo** 'Workspace X'. 
>
>Isso ocorre porque, se os eventos habilitados por duas configurações diferentes de diagnóstico no mesmo recurso **se sobrepõem** de alguma forma, então as configurações não podem ter o mesmo ESPAÇO de trabalho la que o destino. Você terá que resolver manualmente essa falha, navegando até o cofre relevante e configurando uma configuração de diagnóstico com um espaço de trabalho LA diferente como destino.
>
> Observe que a tarefa de remediação **não** falhará se a configuração de diagnóstico existente como apenas o AzureBackupReport habilitado com o Workspace X como destino, já que, neste caso, não haverá sobreposição entre os eventos habilitados pela configuração existente e os eventos habilitados pela configuração criada pela tarefa de remediação.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como usar relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports)
* [Saiba mais sobre a Política do Azure](https://docs.microsoft.com/azure/governance/policy/)
* [Use a Diretiva Azure para habilitar automaticamente o backup para todas as VMs em um escopo de dado](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
