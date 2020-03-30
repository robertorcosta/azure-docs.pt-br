---
title: Problemas de diagnóstico de desktop virtual do Windows - Azure
description: Como usar o recurso de diagnóstico do Windows Virtual Desktop para diagnosticar problemas.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254255"
---
# <a name="identify-and-diagnose-issues"></a>Identificar e diagnosticar problemas

O Windows Virtual Desktop oferece um recurso de diagnóstico que permite ao administrador identificar problemas através de uma única interface. As funções do Windows Virtual Desktop registram uma atividade de diagnóstico sempre que um usuário interage com o sistema. Cada registro contém informações relevantes, como as funções de desktop virtual do Windows envolvidas na transação, mensagens de erro, informações do inquilino e informações do usuário. As atividades diagnósticas são criadas por ações administrativas e de usuário final, e podem ser categorizadas em três baldes principais:

* Atividades de assinatura de feed: o usuário final aciona essas atividades sempre que eles tentam se conectar ao seu feed através de aplicativos de Desktop Remoto da Microsoft.
* Atividades de conexão: o usuário final aciona essas atividades sempre que tenta se conectar a uma área de trabalho ou RemoteApp através de aplicativos de desktop remoto da Microsoft.
* Atividades de gerenciamento: o administrador aciona essas atividades sempre que executa operações de gerenciamento no sistema, como criar pools de host, atribuir usuários a grupos de aplicativos e criar atribuições de funções.
  
As conexões que não alcançam o Windows Virtual Desktop não aparecerão nos resultados dos diagnósticos porque o serviço de função de diagnóstico em si faz parte do Windows Virtual Desktop. Problemas de conexão do Windows Virtual Desktop podem acontecer quando o usuário final está enfrentando problemas de conectividade de rede.

Para começar, [baixe e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para usar em sua sessão PowerShell se você ainda não tiver. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnosticar problemas com o PowerShell

O Windows Virtual Desktop Diagnostics usa apenas um cmdlet PowerShell, mas contém muitos parâmetros opcionais para ajudar a reduzir e isolar problemas. As seções a seguir listam os cmdlets que você pode executar para diagnosticar problemas. A maioria dos filtros pode ser aplicada em conjunto. Os valores listados entre `<tenantName>`parênteses, tais como, devem ser substituídos pelos valores que se aplicam à sua situação.

>[!IMPORTANT]
>O recurso de diagnóstico é para solução de problemas de um usuário único. Todas as consultas usando o PowerShell devem incluir os parâmetros *-UserName* ou *-ActivityID.* Para obter recursos de monitoramento, use o Log Analytics. Consulte [Use o Log Analytics para obter](diagnostics-log-analytics.md) mais informações sobre como enviar dados de diagnóstico para o seu espaço de trabalho. 

### <a name="filter-diagnostic-activities-by-user"></a>Filtrar atividades de diagnóstico pelo usuário

O parâmetro **-UserName** retorna uma lista de atividades de diagnóstico iniciadas pelo usuário especificado, conforme mostrado no exemplo a seguir cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

O parâmetro **-UserName** também pode ser combinado com outros parâmetros opcionais de filtragem.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrar atividades de diagnóstico por tempo

Você pode filtrar a lista de atividades de diagnóstico retornadas com os parâmetros **-StartTime** e **-EndTime.** O **parâmetro -StartTime** retornará uma lista de atividades diagnósticas a partir de uma data específica, conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

O parâmetro **-EndTime** pode ser adicionado a um cmdlet com o parâmetro **-StartTime** para especificar um período específico de tempo para o qual você deseja receber resultados. O exemplo a seguir, o CMDLET retornará uma lista de atividades diagnósticas entre 1º de agosto e 10 de agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Os parâmetros **-StartTime** e **-EndTime** também podem ser combinados com outros parâmetros opcionais de filtragem.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrar atividades de diagnóstico por tipo de atividade

Você também pode filtrar atividades de diagnóstico por tipo de atividade com o parâmetro **-ActivityType.** O cmdlet a seguir retornará uma lista de conexões do usuário final:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

O cmdlet a seguir retornará uma lista de tarefas de gerenciamento de administradores:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

O **cmdlet Get-RdsDiagnosticActivities** não suporta atualmente especificar o Feed como o ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrar atividades de diagnóstico por resultado

Você pode filtrar a lista de atividades de diagnóstico retornada por resultado com o parâmetro **-Outcome.** O exemplo a seguir, o cmdlet retornará uma lista de atividades de diagnóstico bem-sucedidas.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

O exemplo a seguir, o cmdlet retornará uma lista de atividades de diagnóstico falhadas.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

O **parâmetro -Outcome** também pode ser combinado com outros parâmetros opcionais de filtragem.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recupere uma atividade de diagnóstico específica por id de atividade

O parâmetro **-ActivityId** retorna uma atividade de diagnóstico específica se existir, como mostrado no exemplo a seguir cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Exibir mensagens de erro para uma atividade com falha por iD de atividade

Para visualizar as mensagens de erro para uma atividade com falha, você deve executar o cmdlet com o parâmetro **-Detailed.** Você pode visualizar a lista de erros executando o **cmdlet Select-Object.**

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Recupere atividades de diagnóstico detalhadas

O **parâmetro -Detalhado** fornece detalhes adicionais para cada atividade de diagnóstico retornada. O formato de cada atividade varia dependendo do seu tipo de atividade. O parâmetro **-Detalhado** pode ser adicionado a qualquer consulta **Get-RdsDiagnosticActivities,** conforme mostrado no exemplo a seguir.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Cenários de erro comuns

Os cenários de erro são categorizados internamente ao serviço e externos ao Windows Virtual Desktop.

* Problema interno: especifica cenários que não podem ser mitigados pelo administrador do inquilino e precisam ser resolvidos como um problema de suporte. Ao fornecer feedback através da Comunidade de Tecnologia de Desktop Virtual do [Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)inclua o ID de atividade e o período de tempo aproximado de quando o problema ocorreu.
* Questão Externa: relacionar-se com cenários que podem ser mitigados pelo administrador do sistema. Estes são externos ao Windows Virtual Desktop.

A tabela a seguir lista erros comuns que seus admins podem encontrar.

>[!NOTE]
>Esta lista inclui erros mais comuns e é atualizada em uma cadência regular. Para garantir que você tenha as informações mais atualizadas, certifique-se de verificar este artigo pelo menos uma vez por mês.

### <a name="external-management-error-codes"></a>Códigos de erro de gerenciamento externo

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|3|Acesso não autorizado|O usuário que tentou executar o cmdlet powershell administrativo ou não tem permissões para fazê-lo ou digitou mal seu nome de usuário.|
|1000|Inquilinonão encontrado|O nome do inquilino que você inseriu não corresponde a nenhum inquilino existente. Revise o nome do inquilino para erros de digitação e tente novamente.|
|1006|InquilinonãopoderemoverRemovedHasSessionHostPools|Você não pode excluir um inquilino desde que contenha objetos. Exclua os pools de host de sessão primeiro e tente novamente.|
|2000|HostPoolNão Encontrado|O nome da piscina de host que você inseriu não corresponde a nenhuma piscina de host existente. Revise o nome do pool de host para erros de digitação e tente novamente.|
|2005|HostPoolnãopoderemoverremoveraplicativosGrupos|Você não pode excluir uma piscina de host, desde que contenha objetos. Remova todos os grupos de aplicativos na piscina de host primeiro.|
|2004|HostpoolnãopoderemoverHasSessionHosts|Remova todas as sessões dos anfitriões antes de excluir o pool de host de sessão.|
|5001|SessãoHostNãoencontrado|O anfitrião da sessão que você perguntou pode estar offline. Verifique o status da piscina de hospedagem.|
|5008|SessionHostUserSesExist |Você deve assinar todos os usuários no host de sessão antes de executar sua atividade de gerenciamento pretendida.|
|6000|AppGroupNotFound|O nome do grupo de aplicativo que você inseriu não corresponde a nenhum grupo de aplicativos existente. Revise o nome do grupo do aplicativo para erros de digitação e tente novamente.|
|6022|Aplicativo remotonão encontrado|O nome do RemoteApp que você inseriu não corresponde a nenhum RemoteApps. Revise o nome do RemoteApp para erros de digitação e tente novamente.|
|6010|PublishedItemsExist|O nome do recurso que você está tentando publicar é o mesmo que um recurso que já existe. Mude o nome do recurso e tente novamente.|
|7002|NameNotValidWhiteSpace|Não use espaço branco no nome.|
|8000|InvalidAuthorizationRoleScope|O nome da função que você inseriu não corresponde a nenhum nome de função existente. Revise o nome da função para erros de digitação e tente novamente. |
|8001|UserNotFound |O nome de usuário que você inseriu não corresponde a nenhum nome de usuário existente. Revise o nome para erros de digitação e tente novamente.|
|8005|UsuárioNão Foundinaad |O nome de usuário que você inseriu não corresponde a nenhum nome de usuário existente. Revise o nome para erros de digitação e tente novamente.|
|8008|Consentimento do inquilinonecessário|Siga as instruções [aqui](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) para fornecer consentimento para o seu inquilino.|

### <a name="external-connection-error-codes"></a>Códigos de erro de conexão externa

|Código numérico|Código do erro|Solução sugerida|
|---|---|---|
|-2147467259|Falha de conexãoFalha derelacionamentocomoadTrusted|O host de sessão não está corretamente unido ao Diretório Ativo.|
|-2146233088|ConexãoFalhadousuárioTemsessãovalidbutRdShIsUnhealthy|As conexões falharam porque o host de sessão não está disponível. Verifique a saúde do anfitrião da sessão.|
|-2146233088|ConexãoFalha do clienteDesconexãoDesconexão|Se você vir esse erro com freqüência, certifique-se de que o computador do usuário esteja conectado à rede.|
|-2146233088|ConexãoFailedNoHealthyRdshDisponível|A sessão que o usuário anfitrião tentou conectar não é saudável. Depurar a máquina virtual.|
|-2146233088|ConexãoUsuário falhadoNãoautorizado|O usuário não tem permissão para acessar o aplicativo ou desktop publicado. O erro pode aparecer depois que o admin removeu os recursos publicados. Peça ao usuário para atualizar o feed no aplicativo Remote Desktop.|
|2|FileNotFound|O aplicativo que o usuário tentou acessar está instalado incorretamente ou definido como um caminho incorreto.|
|3|InvalidCredentials|O nome de usuário ou senha que o usuário inseriu não corresponde a nenhum nome de usuário ou senha existente. Revise as credenciais para erros de digitação e tente novamente.|
|8|Conexão quebrada|A conexão entre Cliente e Gateway ou Servidor caiu. Nenhuma ação é necessária a menos que aconteça inesperadamente.|
|14|Desconexão de rede inesperada|A conexão com a rede caiu. Peça ao usuário para se conectar novamente.|
|24|ReverseConnectFailed|A máquina virtual host não tem linha de visão direta para rd gateway. Certifique-se de que o endereço IP do Gateway possa ser resolvido.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre funções no Windows Virtual Desktop, consulte [o ambiente Windows Virtual Desktop](environment-setup.md).

Para ver uma lista de cmdlets PowerShell disponíveis para Windows Virtual Desktop, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).
