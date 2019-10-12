---
title: Controles de aplicativo adaptáveis na Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda a usar controles de aplicativo adaptáveis na Central de Segurança do Azure para colocar aplicativos em execução em VMs do Azure na lista de permissões.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2019
ms.author: memildin
ms.openlocfilehash: 46ab2fc5c796d960de8b1c5e3391a6356563b50a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202805"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Controles de aplicativo adaptáveis na Central de Segurança do Azure
Saiba como configurar o controle de aplicativo na Central de Segurança do Azure usando este passo a passo.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>O que são controles de aplicativo adaptáveis na Central de Segurança?
O controle de aplicativo adaptável é uma solução inteligente, automatizada e de ponta a ponta da central de segurança do Azure, que ajuda você a controlar quais aplicativos podem ser executados em suas VMs do Azure e não Azure (Windows e Linux). Entre outros benefícios, isso ajuda a proteger suas VMs contra malware. A central de segurança usa o aprendizado de máquina para analisar os aplicativos em execução em suas VMs e cria uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você:

- Bloquear ou alertar sobre tentativas de executar aplicativos mal-intencionados, incluindo aqueles que, de outra forma, poderiam ser perdidos por soluções antimalware.
- Cumpra a política de segurança da sua organização que impõe o uso somente de software licenciado.
- Evite o uso de softwares indesejados em seu ambiente.
- Evite a execução de aplicativos antigos e sem suporte.
- Impeça o uso de ferramentas de software específicas que não são permitidas em sua organização.
- Habilite a TI a controlar o acesso a dados confidenciais pelo uso do aplicativo.

> [!NOTE]
> Para VMs não Azure e Linux, os controles de aplicativo adaptáveis têm suporte somente no modo de auditoria.

## <a name="how-to-enable-adaptive-application-controls"></a>Como habilitar os controles de aplicativo adaptáveis?
Os controles de aplicativos adaptativos ajudam você a definir um conjunto de aplicativos que podem ser executados em grupos configurados de VMs. Esse recurso está disponível para as janelas do Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e servidores e VMs Linux. Use as etapas a seguir para configurar suas listas de permissões de aplicativo:

1. Abra o painel **Central de Segurança**.
2. No painel esquerdo, selecione **Controles de aplicativo adaptáveis** localizado em **Proteção de nuvem avançada**.

    ![Defesa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

A página **Controles de aplicativo adaptativo** aparece.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

A seção **Grupos de VMs** contém três guias:

* **Configurado**: lista de grupos contendo as VMs que foram configuradas com controle de aplicativo.
* **Recomendado**: lista de grupos para os quais o controle de aplicativos é recomendado. A Central de Segurança usa o aprendizado de máquina para identificar as VMs que são boas candidatas a controle de aplicativo com base na consistência com que elas executam os mesmos aplicativos.
* **Nenhuma recomendação**: lista de grupos contendo VMs sem nenhuma recomendação de controle de aplicativo. Por exemplo, VMs que sempre têm aplicativos mudando e que ainda não estão estáveis.

> [!NOTE]
> A Central de Segurança usa um algoritmo de clustering proprietário para criar grupos de VMs, certificando-se de que VMs semelhantes obtenham a melhor política de controle de aplicativos recomendada.
>
>

### <a name="configure-a-new-application-control-policy"></a>Configurar uma nova política de controle de aplicativo
1. Clique na guia **Recomendado** para obter uma lista de grupos com as recomendações de controle de aplicativo:

   ![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista inclui:

   - **Nome do grupo**: O nome da assinatura e do grupo
   - **VMs e computadores**: O número de máquinas virtuais no grupo
   - **Estado**: o estado das recomendações
   - **Severidade**: o nível de severidade das recomendações

2. Clique em um grupo para abrir o **criar regras de controle de aplicativo** opção.

   ![Regras de controle de aplicativo](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. Em **Selecionar VMs**, examine a lista de VMs recomendadas e desmarque aquelas às quais você não deseja aplicar uma política de adição de aplicativos à lista de permissões. Em seguida, você verá duas listas:

   - **Aplicativos recomendados**: uma lista de aplicativos que são frequentes nas VMs desse grupo e que devem ser autorizados a executar.
   - **Mais aplicativos**: uma lista de aplicativos que são menos frequentes nas VMs desse grupo ou que são conhecidos como exploráveis (veja mais abaixo) e recomendados para revisão.

4. Examine os aplicativos em cada uma das listas e desmarque qualquer que não deseje aplicar. Cada lista inclui:

   - **NOME**: as informações do certificado ou o caminho completo de um aplicativo
   - **TIPOS DE ARQUIVOS**: o tipo de arquivo de aplicativo. Isso pode ser EXE, Script, MSI ou qualquer permutação desses tipos.
   - **Explorável**: um ícone de aviso indica se um aplicativo específico pode ser usado por um invasor para ignorar uma lista de permissões de aplicativo. É recomendável examinar esses aplicativos antes da aprovação.
   - **USUÁRIOS**: os usuários devem ter permissão para executar um aplicativo

5. Após concluir suas seleções, selecione **Criar**. <br>
   Depois de selecionar criar, a central de segurança do Azure cria automaticamente as regras apropriadas sobre a solução de lista de permissões de aplicativo interna disponível em Windows Servers (AppLocker).

> [!NOTE]
> - A Central de Segurança conta com um mínimo de duas semanas de dados para criar uma linha de base e preencher as recomendações exclusivas por grupo de VMs. Os novos clientes da camada standard da Central de Segurança devem esperar que seus grupos de máquinas virtuais sejam exibidos primeiro na guia *sem recomendações*.
> - Os controles de aplicativo adaptáveis da Central de Segurança não oferecem suporte a máquinas virtuais para as quais uma política AppLocker já está habilitada por um GPO ou uma política de segurança local.
> -  Como prática recomendada de segurança, a Central de Segurança sempre tentará criar uma regra de editor para os aplicativos selecionados para serem permitidos, e somente se um aplicativo não tiver informações do editor (também não assinadas), uma regra de caminho será criada para o caminho completo do aplicativo específico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Editando e monitorado um grupo configurado com controle de aplicativo

1. Para editar e monitorar um grupo configurado com uma política de lista de permissões de aplicativo, retorne à página de **controles de aplicativo adaptáveis** e selecione **configurado** em **grupos de VMs**:

   ![Grupos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista inclui:

   - **Nome do grupo**: o nome da assinatura e do grupo
   - **VMs e computadores**: o número de máquinas virtuais no grupo
   - **Modo**: O modo de auditoria registrará tentativas de execução de aplicativos que não estão na lista de permissões; Impor não permitirá que os aplicativos sejam executados, a menos que estejam na lista de permissões
   - **Alertas**: as violações atuais existentes

2. Clicar em um grupo para fazer alterações na **Editar política de controle de aplicativo** página.

   ![Proteção](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Em **Modo de proteção**, você pode escolher entre as seguintes opções:

   - **Auditoria**: nesse modo, a solução de controle de aplicativo não impõe as regras e faz apenas auditoria da atividade nas VMs protegidas. Isso é recomendado nos casos em que você deseja primeiro observar o comportamento geral antes de bloquear a execução de um aplicativo na VM de destino.
   - **Impor**: nesse modo, a solução de controle de aplicativo impoe as regras e verifica se os aplicativos que não têm permissão para execução estão bloqueados.

   > [!NOTE]
   > -  **Impor** o modo de proteção está desabilitado até nova ordem.
   > - Como mencionado anteriormente, por padrão, uma nova política de controle de aplicativo sempre é configurada no modo *Auditoria*. 
   >

4. Em **Extensão de política**, adicione qualquer caminho de aplicativo que deseja permitir. Depois de adicionar esses caminhos, a central de segurança atualiza a política de lista de permissões de aplicativo nas VMs dentro do grupo selecionado de VMS e cria as regras apropriadas para esses aplicativos, além das regras que já estão em vigor.

5. Examinar as violações atuais existentes listados na **alertas recentes** seção. Clique em cada linha para ser redirecionado para a página **Alertas** na Central de Segurança do Azure e exibir todos os alertas que foram detectados pela Central de Segurança do Azure nas VMs associadas.
   - **Alertas**: quaisquer violações registradas.
   - **Nº de VMs**: o número de máquinas virtuais com este tipo de alerta.

6. Em **Regras de permissões de editores**, **Regras de lista de permissões de editores** e **Regras de lista de permissões em hash**, você pode ver quais regras de lista de permissões estão configuradas nas VMs de um grupo, de acordo com o tipo de coleção de regras. Para cada regra, você pode ver:

   - **Regra**: Os parâmetros específicos de acordo com os quais um aplicativo é examinado pelo AppLocker para determinar se um aplicativo pode ser executado.
   - **Tipo de arquivo**: Os tipos de arquivo que são cobertos por uma regra específica. Pode ser um dos seguintes: EXE, Script, MSI ou qualquer permutação desses tipos.
   - **Usuários**: Nome ou número de usuários que têm permissão para executar um aplicativo que é coberto por uma regra de lista de permissões do aplicativo.

   ![Regras de lista de permissões](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Clique nos três pontos no final de cada linha se quiser excluir a regra específica ou editar os usuários permitidos.

8. Depois de fazer alterações em uma política de **controles de aplicativos adaptativos**, clique em **Salvar**.

### <a name="not-recommended-list"></a>Lista de não recomendados

O Security Center recomenda apenas políticas de lista de permissões de aplicativos para máquinas virtuais que executam um conjunto estável de aplicativos. As recomendações não serão criadas se os aplicativos nas VMs associadas sofrerem mudança.

![Recomendação](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista contém:
- **Nome do grupo**: o nome da assinatura e do grupo
- **VMs e computadores**: o número de máquinas virtuais no grupo

A Central de Segurança do Azure permite definir uma política de lista de permissões de aplicativos em grupos de VMs não recomendados também. Siga os mesmos princípios descritos anteriormente para configurar também uma política de lista de permissões de aplicativos nesses grupos.

## <a name="move-a-vm-from-one-group-to-another"></a>Mover uma VM de um grupo para outro

 Quando você move uma VM de um grupo para outro, a política de controle de aplicativo aplicada a ela muda para as configurações do grupo para o qual você o moveu. Você também pode mover uma VM de um grupo configurado para um grupo não configurado, o que resulta na remoção de qualquer política de controle de aplicativo aplicada anteriormente à VM.

 1. Na página **controles de aplicativo adaptáveis** , na guia **configurada** , clique no grupo ao qual a VM que será movida pertence atualmente.
1. Clique em **VMs e computadores configurados**.
1. Clique nos três pontos na linha da VM para mover e clique em **mover**. A janela **mover computador para um grupo diferente** é aberta.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Selecione o grupo para o qual mover a VM e clique em **mover computador**e clique em **salvar**.

    ![Proteção](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Certifique-se de clicar em **salvar** depois de clicar em **mover computador**. Se você não clicar em **salvar**, o computador não será movido.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a usar o controle de aplicativo adaptável na central de segurança do Azure para aplicativos de lista de permissões em execução no Azure e em VMs não Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
