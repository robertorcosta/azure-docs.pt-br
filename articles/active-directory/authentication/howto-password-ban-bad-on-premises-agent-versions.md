---
title: Password protection agent release history - Azure Active Directory
description: Lançamento de versão de documentos e o comportamento de histórico de alterações
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6458f31b21d09e54afb080ffc73598903a9831e0
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381726"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Histórico de versão do agente de Proteção de Senha do Azure AD

## <a name="121250"></a>1.2.125.0

Release date: 3/22/2019

* Fix minor typo errors in event log messages
* Update EULA agreement to final General Availability version

> [!NOTE]
> Build 1.2.125.0 is the General Availability build. Thank you again to everyone has provided feedback on the product!

## <a name="121160"></a>1.2.116.0

Release date: 3/13/2019

* The Get-AzureADPasswordProtectionProxy and Get-AzureADPasswordProtectionDCAgent cmdlets now report software version and the current Azure tenant with the following limitations:
  * Software version and Azure tenant data are only available for DC agents and proxies running version 1.2.116.0 or later.
  * Azure tenant data may not be reported until a re-registration (or renewal) of the proxy or forest has occurred.
* The Proxy service now requires that .NET 4.7 is installed.
  * .NET 4.7 should already be installed on a fully updated Windows Server. If this is not the case, download and run the installer found at [The .NET Framework 4.7 offline installer for Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * On Server Core systems it may be necessary to pass the /q flag to the .NET 4.7 installer to get it to succeed.
* The Proxy service now supports automatic upgrade. Automatic upgrade uses the Microsoft Azure AD Connect Agent Updater service which is installed side-by-side with the Proxy service. Automatic upgrade is on by default.
* Automatic upgrade can be enabled or disabled using the Set-AzureADPasswordProtectionProxyConfiguration cmdlet. The current setting can be queried using the Get-AzureADPasswordProtectionProxyConfiguration cmdlet.
* The service binary for the DC agent service has been renamed to AzureADPasswordProtectionDCAgent.exe.
* The service binary for the Proxy service has been renamed to AzureADPasswordProtectionProxy.exe. Firewall rules may need to be modified accordingly if a third-party firewall is in-use.
  * NOTE: if an http proxy config file was being used in a previous Proxy install, it will need to be renamed (from *proxyservice.exe.config* to *AzureADPasswordProtectionProxy.exe.config*) after this upgrade.
* All time-limited functionality checks have been removed from the DC agent.
* Minor bugs fixes and logging improvements.

## <a name="12650"></a>1.2.65.0

Release date: 2/1/2019

Alterações:

* Agora há compatibilidade com o serviço de agente e proxy do DC no Server Core. Mininimum OS requirements are unchanged from before: Windows Server 2012 for DC agents, and Windows Server 2012 R2 for proxies.
* Os cmdlets Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora são compatíveis com os modos de autenticação do Azure baseados em código.
* O cmdlet Get-AzureADPasswordProtectionDCAgent ignorará os pontos de conexão de serviço inválidos e/ou danificados. Isso corrige o bug em que os controladores de domínio, às vezes, apareciam várias vezes na saída.
* O cmdlet Get-AzureADPasswordProtectionSummaryReport ignorará os pontos de conexão de serviço inválidos e/ou danificados. Isso corrige o bug em que os controladores de domínio, às vezes, apareciam várias vezes na saída.
* O módulo do PowerShell Proxy agora é registrado de %ProgramFiles%\WindowsPowerShell\Modules. A variável de ambiente PSModulePath do computador não é mais modificada.
* Foi adicionado um novo cmdlet Get-AzureADPasswordProtectionProxy para auxiliar na descoberta de proxies registrados em um domínio ou floresta.
* O agente do DC usa uma nova pasta no compartilhamento sysvol para replicar políticas de senha e outros arquivos.

   Localização da pasta antiga:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nova localização da pasta:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Essa alteração foi feita para evitar avisos de “GPO órfão” falso-positivos.)

   > [!NOTE]
   > Nenhuma migração ou compartilhamento de dados será feita entre a pasta antiga e a nova pasta. As versões mais antigas do agente do DC continuarão a usar o local antigo até serem atualizadas para esta versão ou posterior. Depois que todos os agentes do DC estiverem executando a versão 1.2.65.0 ou posterior, a pasta sysvol antiga poderá ser excluída manualmente.

* O serviço de proxy e do agente do DC agora detectarão e excluirão cópias danificadas de seus respectivos pontos de conexão de serviço.
* Cada agente do DC excluirá periodicamente pontos de conexão de serviço danificado e obsoletos em seu domínio, para pontos de conexão do serviço de proxy e do agente do DC. Os pontos de conexão do serviço de proxy e do agente do DC são considerados obsoletos caso seu carimbo de data/hora de pulsação tenha mais de sete dias.
* O agente do DC agora renovará o certificado da floresta conforme necessário.
* O serviço de proxy agora renovará o certificado de proxy conforme necessário.
* Atualizações para o algoritmo de validação de senha: a lista de senhas banidas global e a lista de senhas banidas específicas do cliente (se configuradas) são combinadas antes de validações de senha. Uma determinada senha agora pode ser rejeitada (falha ou somente auditoria) se ele contiver tokens na lista global e na específica do cliente. A documentação do log de eventos foi atualizada para refletir isso. Consulte [Monitorar Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Correções de desempenho e robustez
* Log aprimorado

> [!WARNING]
> Funcionalidade de tempo limitado: o serviço de agente do DC nesta versão (1.2.65.0) parará o processamento de solicitações de validação de senha a partir de 1º de setembro de 2019.  Serviços de agente do DC em versões anteriores (consulte a lista abaixo) parará de processar a partir de 1º de julho de 2019. O serviço de agente do DC em todas as versões registrará 10021 eventos para o log de eventos do administrador nos dois meses anteriores a esses prazos. Todas as restrições de limite de tempo serão removidas na próxima versão com disponibilidade geral. O serviço de agente de Proxy não tem limitação de tempo em nenhuma versão, mas ainda deve ser atualizada para a versão mais recente para aproveitar todas as correções de bug subsequentes e outras melhorias.

## <a name="12250"></a>1.2.25.0

Data do lançamento: 11/01/2018

Correções:

* Serviço de agente e o proxy do controlador de domínio não deve falhar devido a falhas de relação de confiança de certificado.
* Serviço de agente e o proxy do controlador de domínio têm correções adicionais para computadores em conformidade com FIPS.
* Serviço de proxy agora funcionará corretamente em um ambiente de chamadas rede somente 1.2 TLS.
* Menor de desempenho e correções de robustez
* Log aprimorado

Alterações:

* O nível de sistema operacional mínimo exigido para o serviço Proxy é agora o Windows Server 2012 R2. O nível de sistema operacional mínimo exigido para o serviço do agente DC permanece no Windows Server 2012.
* Agora, o serviço de Proxy requer o .NET versão 4.6.2.
* O algoritmo de validação de senha usa uma tabela de normalização de caracteres expandida. Isso pode resultar na rejeição de senhas aceitas em versões anteriores.

## <a name="12100"></a>1.2.10.0

Data do lançamento: 17/08/2018

Correções:

* Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora dão suporte a autenticação multifator
* Register-AzureADPasswordProtectionProxy requer um WS2012 ou domínio posterior no domínio para evitar erros de criptografia.
* O serviço de agente do controlador de domínio é mais confiável sobre como solicitar uma nova política de senha do Azure na inicialização.
* O serviço de agente do DC solicitará uma nova política de senha do Azure a cada hora se necessário, mas agora vai fazer isso em uma hora de início selecionada aleatoriamente.
* O serviço de agente do controlador de domínio não fará com que um atraso indefinido no novo anúncio do controlador de domínio quando instalado em um servidor antes da sua promoção como uma réplica.
* O serviço de agente do controlador de domínio agora respeitará a definição de configuração "Habilitar a proteção por senha no Active Directory do Windows Server"
* Ambos os instaladores de agente e o proxy do controlador de domínio agora serão compatível com a atualização in-loco ao atualizar para versões futuras.

> [!WARNING]
> Atualização in-loco de versão 1.1.10.3 não tem suporte e resultará em um erro de instalação. Para atualizar para a versão 1.2.10 ou posterior, você deve primeiro completamente desinstalar o software de serviço de agente e o proxy de controlador de domínio, e instalar a nova versão a partir do zero. Instalar e configurar o serviço proxy de proteção por senha do Azure Active Directory.  Não é necessário registrar novamente a floresta.

> [!NOTE]
> Atualizações in-loco do software agente DC exigirá uma reinicialização.

* Serviço de agente e o proxy do controlador de domínio agora oferecem suporte em execução em um servidor configurado para usar somente os algoritmos compatíveis com FIPS.
* Menor de desempenho e correções de robustez
* Log aprimorado

## <a name="11103"></a>1.1.10.3

Data do lançamento: 15/06/2018

Versão prévia pública inicial

## <a name="next-steps"></a>Próximos passos

[Implantar Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
