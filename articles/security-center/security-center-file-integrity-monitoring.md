---
title: Monitoramento de Integridade de Arquivo na Central de Segurança do Azure | Microsoft Docs
description: Saiba como configurar o FIM (Monitoramento de Integridade de Arquivo) na Central de Segurança do Azure usando este passo a passo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 910d98558e5b949a76202cce48c2a210531d5c35
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459786"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoramento de integridade de arquivo na Central de Segurança do Azure
Saiba como configurar o FIM (Monitoramento de Integridade de Arquivo) na Central de Segurança do Azure usando este passo a passo.


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Disponível|
|Refere|Camada padrão|
|Funções e permissões necessárias:|O **proprietário do espaço de trabalho** pode habilitar/desabilitar o fim (para obter mais informações, consulte [funções do Azure para log Analytics](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)).<br>O **leitor** pode exibir os resultados.|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) China gov, outros gov|
|||





## <a name="what-is-fim-in-security-center"></a>O que é FIM na Central de Segurança?
FIM (Monitoramento de Integridade de Arquivo), também conhecido como monitoramento de alterações, examina os arquivos e Registros do sistema operacional, aplicativos e outras alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do arquivo é diferente da última verificação do arquivo. Você pode aproveitar essa comparação para determinar se foram feitas modificações válidas ou suspeitas em seus arquivos.

O Monitoramento de Integridade de Arquivo da Central de Segurança valida a integridade dos arquivos do Windows, o Registro do Windows e os arquivos do Linux. Você seleciona os arquivos que deseja monitorar habilitando o FIM. A Central de Segurança monitora os arquivos com o FIM habilitado para atividades como:

- Criação e remoção de arquivo e Registro
- Modificações de arquivo (alterações de tamanho do arquivo, listas de controle de acesso e hash do conteúdo)
- Modificações de registro (alterações de tamanho, listas de controle de acesso, tipo e conteúdo)

A Central de Segurança recomenda entidades para serem monitoradas, nas quais você pode facilmente habilitar o FIM. Você também pode definir suas próprias políticas de FIM ou entidades para serem monitoradas. Este passo a passo mostra como fazer isso.

> [!NOTE]
> O recurso de monitoramento de integridade de arquivo (FIM) funciona para computadores e VMs com Windows e Linux e está disponível na camada Standard da central de segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança. O FIM carrega dados no espaço de trabalho do Log Analytics. Encargos de dados se aplicam, com base na quantidade de dados que você carregar. Consulte [Preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) para saber mais.

O FIM usa a solução de Controle de Alterações do Azure para controlar e identificar as alterações em seu ambiente. Quando o monitoramento de integridade de arquivo estiver habilitado, você terá um recurso de **controle de alterações** do tipo **solução**. Para obter detalhes de frequência de coleta de dados, consulte [Detalhes de coleta de dados do Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) para Controle de Alterações do Azure.

> [!NOTE]
> Se você remover o recurso de **controle de alterações** , desabilitará também o recurso de monitoramento de integridade de arquivo na central de segurança.

## <a name="which-files-should-i-monitor"></a>Quais arquivos devo monitorar?
Você deve pensar sobre os arquivos que são críticos para seu sistema e aplicativos ao escolher quais arquivos monitorar. Considere a possibilidade de escolher os arquivos que você não pretende alterar sem planejamento. Escolher arquivos que são alterados com frequência por aplicativos ou sistema operacional (como arquivos de log e arquivos de texto) cria muito ruído que torna difícil de identificar um ataque.

A central de segurança fornece a seguinte lista de itens recomendados para monitorar com base em padrões de ataque conhecidos. Isso inclui arquivos e chaves do registro do Windows. Todas as chaves estão em HKEY_LOCAL_MACHINE ("HKLM" na tabela).

|**Arquivos do Linux**|**Arquivos do Windows**|**Chave do registro do Windows**|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|Pastas HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|/sbin|C:\Windows\System32\userinit.exe|Pastas do Shell HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Arquivos de Programas\microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||Pastas HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|||Pastas do Shell HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|

## <a name="using-file-integrity-monitoring"></a>Usando o Monitoramento de Integridade de Arquivo
1. Abra o painel **Central de Segurança**.
2. No painel esquerdo, em **Defesa da nuvem avançada**, selecione **Monitoramento de Integridade de Arquivo**.
![Painel da Central de Segurança][1]

O **Monitoramento de Integridade do Arquivo** é aberto.
  ![Painel da Central de Segurança][2]

As informações a seguir são fornecidas para cada workspace:

- Número total de alterações que ocorreram na última semana (você poderá ver um traço "-" se o FIM não estiver habilitado no workspace)
- Número total de computadores e VMs se reportando para o workspace
- Localização geográfica do workspace
- Assinatura do Azure sob a qual o workspace está

Os botões a seguir também podem ser mostrados para um workspace:

- ![Ícone Habilitar][3] Indica que o FIM não está habilitado para o workspace. Selecionar o workspace permite que você habilite o FIM em todos os computadores no workspace.
- ![Ícone do plano ][4] de atualização indica que o espaço de trabalho ou a assinatura não está em execução na camada Standard da central de segurança. Para usar o recurso do FIM, sua assinatura deve estar em execução na Standard.  Selecionar o workspace permite que você faça a atualização para Standard. Para saber mais sobre a camada Standard e como atualizar, consulte [atualizar para a camada Standard da central de segurança para aumentar a segurança](security-center-pricing.md).
- Um espaço em branco (não há nenhum botão) significa que o FIM já está habilitado no workspace.

Em **Monitoramento de Integridade de Arquivo**, você pode selecionar um workspace para habilitar o FIM para ele, exibir o painel do Monitoramento de Integridade de Arquivo para esse workspace ou [atualizar](security-center-pricing.md) o workspace para Standard.

## <a name="enable-fim"></a>Habilitar o FIM
Para habilitar o FIM em um workspace:

1. Em **Monitoramento de Integridade de Arquivo**, selecione um workspace com o botão **habilitar**.
2. **Habilitar o monitoramento de integridade de arquivo** é aberta exibindo o número de computadores Windows e Linux no workspace.

   ![Habilitar o monitoramento de integridade de arquivo][5]

   As configurações recomendadas para Windows e Linux também são listadas.  Expanda **Arquivos do Windows**, **Registro** e **Arquivos do Linux** para ver a lista completa de itens recomendados.

3. Desmarque qualquer entidade recomendada à qual não deseja aplicar o FIM.
4. Selecione **Aplicar o monitoramento de integridade de arquivo** para habilitar o FIM.

> [!NOTE]
> Você pode alterar as configurações a qualquer momento. Consulte Editar entidades monitoradas abaixo para saber mais.


## <a name="view-the-fim-dashboard"></a>Exibir o painel do FIM
O painel do **Monitoramento de integridade de arquivo** é exibido para workspaces em que o FIM está habilitado. O painel do FIM é aberto depois de habilitar o FIM em um workspace ou quando você seleciona um workspace na janela **Monitoramento de Integridade de Arquivo** que já tenha o FIM habilitado.

![Painel do Monitoramento de Integridade de Arquivo][6]

O painel do FIM de um espaço de trabalho exibe os seguintes detalhes:

- Número total de computadores conectados ao workspace
- Número total de alterações que ocorreram durante o período selecionado
- Uma divisão do tipo de alteração (arquivos, Registro)
- Uma divisão da categoria da alteração (modificado, adicionado, removido)

Selecionar Filtro na parte superior do painel permite que você aplique o período para o qual deseja ver as alterações.

![Filtro de período][7]

A guia **Computadores** (mostrada acima) lista todos os computadores que se reportam para esse workspace. Para cada computador, o painel lista:

- Total de alterações que ocorreram durante o período selecionado
- Uma divisão do total de alterações como alterações de arquivo ou do Registro

A **pesquisa de logs** é aberta quando você insere um nome de computador no campo de pesquisa ou seleciona um computador listado na guia computadores. a pesquisa de log exibe todas as alterações feitas durante o período de tempo selecionado para o computador. Você pode expandir uma alteração para obter mais informações.

![Pesquisa de log][8]

A guia **Alterações** (mostrada abaixo) lista todas as alterações no workspace durante o período selecionado. Para cada entidade que foi alterado, o painel lista:

- Computador em que a alteração ocorreu
- Tipo de alteração (Registro ou arquivo)
- Categoria da alteração (modificado, adicionado, removido)
- Data e hora da alteração

![Alterações no workspace][9]

**Detalhes da alteração** abre quando você insere uma alteração no campo de pesquisa ou seleciona uma entidade listada na guia **Alterações**.

![Detalhes da alteração][10]

## <a name="edit-monitored-entities"></a>Editar entidades monitoradas

1. Volte para o **painel do Monitoramento de Integridade de Arquivo** e selecione **Configurações**.

   ![Configurações][11]

   **Configuração do Workspace** abre exibindo três guias: **Registro do Windows**, **Arquivos do Windows** e **Arquivos do Linux**. Cada guia lista as entidades que você pode editar nessa categoria. Para cada entidade listada, a Central de Segurança identifica se o FIM está habilitado (true) ou não está habilitado (false).  Editar a entidade permite que você habilite ou desabilite o FIM.

   ![Configuração do workspace][12]

2. Selecione uma proteção de identidade. Neste exemplo, selecionamos um item no Registro do Windows. **Editar para Controle de Alterações** é aberto.

   ![Editar ou controlar alterações][13]

Em **Editar para Controle de Alterações** você pode:

- Habilitar (True) ou desabilitar (false) o monitoramento de integridade de arquivo
- Fornecer ou alterar o nome da entidade
- Fornecer ou alterar o valor ou o caminho
- Excluir a entidade, descartar a alteração ou salvar a alteração

## <a name="add-a-new-entity-to-monitor"></a>Adicionar uma nova entidade a ser monitorada
1. Volte para o **painel do Monitoramento de integridade de arquivo** e selecione **Configurações** na parte superior. **Configuração do Workspace** é aberto.
2. Em **Configuração do Workspace**, selecione a guia para o tipo de entidade que você deseja adicionar: Registro do Windows, Arquivos do Windows ou Arquivos do Linux. Neste exemplo, selecionamos **Arquivos do Linux**.

   ![Adicionar um novo item a ser monitorado][14]

3. Selecione **Adicionar**. **Adicionar para Controle de Alterações** é aberto.

   ![Inserir as informações solicitadas][15]

4. Na página **Adicionar**, digite as informações solicitadas e selecione **Salvar**.

## <a name="disable-monitored-entities"></a>Desabilitar entidades monitoradas
1. Retorne para o painel do **Monitoramento de Integridade de Arquivo**.
2. Selecione um workspace em que o FIM está habilitado no momento. Um workspace estará habilitado para o FIM se o botão Habilitar ou o botão Atualizar Plano estiver ausente.

   ![Selecione um workspace em que o FIM está habilitado][16]

3. Em Monitoramento de Integridade de Arquivo, selecione **Configurações**.

   ![Selecionar configurações][17]

4. Em **Configuração do Workspace**, selecione um grupo em que **Habilitado** está definido como true.

   ![Configuração do Workspace][18]

5. Na janela **Editar para Controle de Alterações**, defina **Habilitado** como False.

   ![Definir Habilitado como false][19]

6. Clique em **Salvar**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Pasta e o caminho de monitoramento usando caracteres curinga

Use caracteres curinga para simplificar o rastreamento em diretórios. As seguintes regras se aplicam quando você configura o monitoramento de pasta usando caracteres curinga:
-   Caracteres curinga são necessários para acompanhar vários arquivos.
-   Caracteres curinga só podem ser usados no último segmento de um caminho, como C:\folder\file ou /etc/*.conf
-   Se uma variável de ambiente incluir um caminho que não é válido, a validação terá êxito, mas o caminho falhará quando o inventário for executado.
-   Ao definir o caminho, evite caminhos gerais, como c:\*. * que resultarão em muitas pastas sendo percorridas.

## <a name="disable-fim"></a>Desabilitar o FIM
Você pode desabilitar o FIM. O FIM usa a solução de Controle de Alterações do Azure para controlar e identificar as alterações em seu ambiente. Desabilitando o FIM, você pode remover a solução de Controle de Alterações do workspace selecionado.

1. Para desabilitar o FIM, retorne para o painel do **Monitoramento de Integridade de Arquivo**.
2. Selecione um workspace.
3. Em **Monitoramento de Integridade de Arquivo**, selecione **Desabilitar**.

   ![Desabilitar o FIM][20]

4. Selecione **Remover** para desabilitar.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o FIM (monitoramento de integridade de arquivo) na central de segurança. Para saber mais sobre a Central de Segurança, confira as páginas seguintes:

* [Configurando políticas de segurança](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciar recomendações de segurança](security-center-recommendations.md): saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Blog de Segurança do Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png