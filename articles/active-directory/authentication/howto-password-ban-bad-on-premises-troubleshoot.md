---
title: Solucionar problemas no local azure AD Password Protection
description: Saiba como solucionar problemas do Azure AD Password Protection para um ambiente de serviços de domínio de diretório ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263641"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Solução de problemas: Proteção por senha do Azure AD no local

Após a implantação da Proteção de Senha do Azure AD, a solução de problemas pode ser necessária. Este artigo apresenta detalhes para ajudá-lo a entender algumas etapas de solução de problemas comuns.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>O agente DC não pode localizar um proxy no diretório

O principal sintoma deste problema são os eventos de 30017 no registro de eventos do agente DC.

A causa usual deste problema é que um proxy ainda não foi registrado. Se um proxy tiver sido registrado, pode haver algum atraso devido à latência de replicação do AD até que um agente DC em particular seja capaz de ver esse proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>O agente DC não é capaz de se comunicar com um proxy

O principal sintoma deste problema são os eventos de 30018 no registro de eventos do agente DC. Este problema pode ter várias causas possíveis:

1. O agente DC está localizado em uma parte isolada da rede que não permite conectividade de rede aos proxy(s) registrados. Esse problema pode ser benigno, desde que outros agentes DC possam se comunicar com os proxy(s) a fim de baixar políticas de senha do Azure. Uma vez baixadas, essas políticas serão obtidas pela DC isolada através da replicação dos arquivos de diretiva no compartilhamento sysvol.

1. A máquina host proxy está bloqueando o acesso ao ponto final do ponto final do RPC (porta 135)

   O instalador proxy de proteção por senha do Azure AD cria automaticamente uma regra de entrada do Firewall do Windows que permite o acesso à porta 135. Se essa regra for posteriormente excluída ou desativada, os agentes DC não poderão se comunicar com o serviço Proxy. Se o Firewall do Windows incorporado tiver sido desativado em vez de outro produto de firewall, você deve configurar esse firewall para permitir o acesso à porta 135.

1. A máquina host proxy está bloqueando o acesso ao ponto final do RPC (dinâmico ou estático) escutado pelo serviço Proxy

   O instalador proxy de proteção por senha do Azure AD cria automaticamente uma regra de entrada do Firewall do Windows que permite o acesso a quaisquer portas de entrada ouvidas pelo serviço Proxy de Proteção por Senha do Azure AD. Se essa regra for posteriormente excluída ou desativada, os agentes DC não poderão se comunicar com o serviço Proxy. Se o Firewall do Windows incorporado tiver sido desativado em vez de outro produto de firewall, você deve configurar esse firewall para permitir o acesso a quaisquer portas de entrada ouvidas pelo serviço Proxy de Proteção por Senha AD do Azure. Essa configuração pode ser mais específica se o serviço Proxy tiver sido configurado `Set-AzureADPasswordProtectionProxyConfiguration` para ouvir em uma porta RPC estática específica (usando o cmdlet).

1. A máquina host proxy não está configurada para permitir aos controladores de domínio a capacidade de fazer logon na máquina. Esse comportamento é controlado através da atribuição de privilégio de usuário "Acesse este computador a partir da rede". Todos os controladores de domínio em todos os domínios da floresta devem ser concedidos a este privilégio. Essa configuração é frequentemente restrita como parte de um esforço maior de endurecimento da rede.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>O serviço proxy não consegue se comunicar com o Azure

1. Certifique-se de que a máquina proxy tenha conectividade com os pontos finais listados nos [requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md).

1. Certifique-se de que a floresta e todos os servidores proxy estejam registrados contra o mesmo inquilino do Azure.

   Você pode verificar este `Get-AzureADPasswordProtectionProxy` requisito `Get-AzureADPasswordProtectionDCAgent` executando os cmdlets `AzureTenant` powershell e e, em seguida, comparar a propriedade de cada item devolvido. Para a operação correta, o nome do inquilino relatado deve ser o mesmo em todos os agentes DC e servidores proxy.

   Se existir uma condição de incompatibilidade de registro de inquilino do `Register-AzureADPasswordProtectionProxy` Azure, esse problema pode ser corrigido executando os cmdlets e/ou `Register-AzureADPasswordProtectionForest` PowerShell conforme necessário, certificando-se de usar credenciais do mesmo inquilino do Azure para todos os registros.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>O agente DC não consegue criptografar ou descriptografar arquivos de política de senha

O Azure AD Password Protection tem uma dependência crítica da funcionalidade de criptografia e descriptografia fornecida pelo Microsoft Key Distribution Service. Falhas de criptografia ou descriptografia podem se manifestar com uma variedade de sintomas e ter várias causas potenciais.

1. Certifique-se de que o serviço KDS esteja ativado e funcional em todos os controladores de domínio do Windows Server 2012 e posteriores em um domínio.

   Por padrão, o modo de início de serviço do serviço KDS é configurado como Manual (Trigger Start). Essa configuração significa que a primeira vez que um cliente tenta usar o serviço, ele é iniciado sob demanda. Este modo de início de serviço padrão é aceitável para que o Azure AD Password Protection funcione.

   Se o modo de início do serviço KDS tiver sido configurado como Desativado, essa configuração deve ser corrigida antes que o Azure AD Password Protection funcione corretamente.

   Um teste simples para este problema é iniciar manualmente o serviço KDS, seja através do console MMC de gerenciamento de serviços, ou usando outras ferramentas de gerenciamento (por exemplo, executar "net start kdssvc" a partir de um console prompt de comando). Espera-se que o serviço KDS comece com sucesso e continue funcionando.

   A causa raiz mais comum para o serviço KDS não ser capaz de iniciar é que o objeto controlador de domínio active directory está localizado fora do OU controladores de domínio padrão. Essa configuração não é suportada pelo serviço KDS e não é uma limitação imposta pelo Azure AD Password Protection. A correção para esta condição é mover o objeto do controlador de domínio para um local o OU controladores de domínio padrão.

1. Mudança de formato de buffer criptografado do KDS incompatível do Windows Server 2012 R2 para o Windows Server 2016

   Uma correção de segurança KDS foi introduzida no Windows Server 2016 que modifica o formato de buffers criptografados KDS; esses buffers às vezes falham em descriptografar no Windows Server 2012 e no Windows Server 2012 R2. A direção inversa é boa - buffers que são criptografados por KDS no Windows Server 2012 e no Windows Server 2012 R2 sempre descriptografarão com sucesso no Windows Server 2016 e posteriormente. Se os controladores de domínio em seus domínios do Active Directory estiverem executando uma mistura desses sistemas operacionais, eventuais falhas de descriptografia de proteção por senha do Azure AD podem ser relatadas. Não é possível prever com precisão o tempo ou os sintomas dessas falhas dada a natureza da correção de segurança, e dado que não é determinista qual Agente DC de Proteção de Senha AZure ad em qual controlador de domínio criptografará dados em um dado momento.

   A Microsoft está investigando uma correção para este problema, mas ainda não há ETA disponível. Enquanto isso, não há solução para este problema além de não executar uma mistura desses sistemas operacionais incompatíveis em seus domínios do Active Directory. Em outras palavras, você deve executar apenas os controladores de domínio Windows Server 2012 e Windows Server 2012 R2, OU você deve executar apenas o Windows Server 2016 e acima dos controladores de domínio.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Senhas fracas estão sendo aceitas, mas não devem ser

Este problema pode ter várias causas.

1. Seus agentes DC estão executando uma versão pública do software de visualização que expirou. Consulte [O software de agente DC de visualização pública expirou](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Seus agentes DC não podem baixar uma diretiva ou não são capazes de descriptografar políticas existentes. Verifique possíveis causas nos tópicos acima.

1. O modo de Imposição de política de senha ainda está definido para Auditoria. Se essa configuração estiver em vigor, reconfigure-a para aplicar usando o portal de proteção por senha AD do Azure. Para obter mais informações, consulte [Modos de operação](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. A política de senha foi desabilitada. Se essa configuração estiver em vigor, reconfigure-a para habilitar usando o portal de proteção por senha AD do Azure. Para obter mais informações, consulte [Modos de operação](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Você não instalou o software do agente DC em todos os controladores de domínio no domínio. Nesta situação, é difícil garantir que clientes remotos do Windows direcionem um controlador de domínio específico durante uma operação de alteração de senha. Se você acha que tem como alvo com sucesso um DC específico onde o software do agente DC está instalado, você pode verificar verificando duas vezes o registro de eventos do agente DC: independentemente do resultado, haverá pelo menos um evento para documentar o resultado da senha Validação. Se não houver nenhum evento presente para o usuário cuja senha é alterada, então a alteração de senha provavelmente foi processada por um controlador de domínio diferente.

   Como um teste alternativo, tente definir\alterar senhas enquanto estiver conectado diretamente a um DC onde o software do agente DC está instalado. Esta técnica não é recomendada para os domínios do Active Directory de produção.

   Embora a implantação incremental do software do agente DC seja suportada sujeita a essas limitações, a Microsoft recomenda fortemente que o software do agente DC seja instalado em todos os controladores de domínio em um domínio o mais rápido possível.

1. O algoritmo de validação de senha pode estar funcionando como esperado. Veja [Como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe falha ao definir uma senha DSRM fraca

O Active Directory sempre validará uma nova senha do modo de reparo de serviços de diretório para garantir que ele atenda aos requisitos de complexidade de senha do domínio; essa validação também chama para dlls de filtro de senha como Azure AD Password Protection. Se a nova senha do DSRM for rejeitada, a seguinte mensagem de erro resultará:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Quando o Azure AD Password Protection registra o registro de eventos de validação de senha para uma senha dSRM do Active Directory, espera-se que as mensagens de registro de eventos não incluam um nome de usuário. Esse comportamento ocorre porque a conta DSRM é uma conta local que não faz parte do domínio do Active Directory real.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>A promoção da réplica do controlador de domínio falha por causa de uma senha DSRM fraca

Durante o processo de promoção dc, a nova senha do Modo de Reparo de Serviços de Diretório será submetida a um DC existente no domínio para validação. Se a nova senha do DSRM for rejeitada, a seguinte mensagem de erro resultará:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Assim como no problema acima, qualquer evento de validação de senha do Azure AD Password Protection terá nomes de usuário vazios para este cenário.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>O rebaixamento do controlador de domínio falha devido a uma senha de administrador local fraca

Há suporte para rebaixar um controlador de domínio que ainda esteja executando o software do agente DC. No entanto, os administradores devem estar cientes de que o software do agente do DC continua aplicando a política de senha atual durante o procedimento de rebaixamento. A nova senha da conta de Administrador local (especificada como parte da operação de rebaixamento) é validada como qualquer outra senha. A Microsoft recomenda que senhas seguras sejam escolhidas para contas de administrador local como parte de um procedimento de disque-lo dc.

Depois que o rebaixamento tiver ocorrido com êxito e o controlador de domínio for reiniciado e estiver em execução novamente como um servidor membro normal, o software do agente DC será revertido para execução em modo passivo. Então, poderá ser desinstalado a qualquer momento.

## <a name="booting-into-directory-services-repair-mode"></a>Inicialização no modo de reparo de serviços de diretório

Se o controlador de domínio for inicializado no modo de reparo de serviços de diretório, o filtro de senha do agente DC dll detectará essa condição e fará com que todas as atividades de validação ou execução de senha sejam desativadas, independentemente da política ativa atualmente Configuração. O dll do filtro de senha do agente DC registrará um evento de aviso 10023 no registro de eventos do Admin, por exemplo:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>O software de agente DC de pré-visualização pública expirou

Durante o período de visualização pública do Azure AD Password Protection, o software do agente DC foi codificado para parar de processar solicitações de validação de senha nas seguintes datas:

* A versão 1.2.65.0 deixará de processar solicitações de validação de senha em 1º de setembro de 2019.
* Versão 1.2.25.0 e anteriormente parou de processar solicitações de validação de senha em 1º de julho de 2019.

À medida que o prazo se aproxima, todas as versões limitadas de agente DC emitirão um evento 10021 no registro de eventos do agente DC Na hora da inicialização que se parece com isso:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Uma vez passado o prazo, todas as versões de agente DC com tempo limitado emitirão um evento 10022 no registro de eventos do agente DC Na hora da inicialização que se parece com isso:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Como o prazo só é verificado na inicialização, você pode não ver esses eventos até muito tempo depois do prazo do calendário ter passado. Uma vez que o prazo tenha sido reconhecido, nenhum efeito negativo sobre o controlador de domínio ou o ambiente maior ocorrerá além de todas as senhas serão automaticamente aprovadas.

> [!IMPORTANT]
> A Microsoft recomenda que agentes DC de visualização pública expirados sejam imediatamente atualizados para a versão mais recente.

Uma maneira fácil de descobrir agentes DC em seu ambiente `Get-AzureADPasswordProtectionDCAgent` que precisam ser atualizados é executando o cmdlet, por exemplo:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Para este tópico, o campo SoftwareVersion é obviamente a propriedade chave a ser olhada. Você também pode usar a filtragem PowerShell para filtrar agentes DC que já estão na versão de linha de base necessária, por exemplo:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

O software Proxy de Proteção por Senha AD do Azure Não é limitado por tempo em nenhuma versão. A Microsoft ainda recomenda que os agentes DC e proxy sejam atualizados para as versões mais recentes à medida que são lançados. O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para encontrar agentes proxy que requerem upgrades, semelhante ao exemplo acima para agentes DC.

Consulte atualizar [o agente DC](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) e atualizar o serviço [proxy](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) para obter mais detalhes sobre procedimentos específicos de atualização.

## <a name="emergency-remediation"></a>Correção de emergência

Se ocorrer uma situação em que o serviço do agente DC esteja causando problemas, o serviço do agente DC poderá ser encerrado imediatamente. O dll do filtro de senha do agente DC ainda tentará chamar o serviço que não está em execução e registrará eventos de aviso (10012, 10013), mas todas as senhas de entrada serão aceitas durante esse período. O serviço do agente DC poderá, então, também ser configurado através do Gerenciador de Controle de Serviço do Windows com um tipo de inicialização “Desabilitado”, conforme necessário.

Outra medida de correção seria definir para o modo Habilitar para Não no portal de Proteção de Senha do Azure AD. Depois que a política atualizada tiver sido baixada, cada serviço do agente do DC entrará no modo inativo em que todas as senhas são aceitas no estado em que se encontram. Para obter mais informações, consulte [Modos de operação](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Remoção

Se for decidido desinstalar o software de proteção por senha AZure AD e limpar todos os estados relacionados dos domínios e da floresta, essa tarefa pode ser realizada usando as seguintes etapas:

> [!IMPORTANT]
> É importante executar essas etapas na ordem. Se qualquer instância do serviço Proxy for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint. Se qualquer instância do serviço do agente DC for deixada em execução, ele recriará periodicamente seu objeto serviceConnectionPoint e o estado sysvol.

1. Desinstale o software do Proxy de todos os computadores. Esta etapa **não** exige um reinício.
2. Desinstale o software do Agente DC de todos os controladores de domínio. Essa etapa **exige** um reinício.
3. Remova manualmente todos os pontos de conexão do serviço Proxy em cada contexto de nomenclatura do domínio. O local desses objetos pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente.

4. Remova manualmente todos os pontos de conexão do agente DC em cada contexto de nomeação de domínios. Pode haver um desses objetos por controlador de domínio na floresta, dependendo de quão amplamente o software foi implantado. O local desse objeto pode ser descoberto com o seguinte comando do PowerShell do Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   O (s) objeto (s) resultante (s) encontrado (s) através do comando `Get-ADObject` pode então ser canalizado para `Remove-ADObject`, ou deletado manualmente.

   Não omita o asterisco (“*”) no final do valor da variável $keywords.

5. Remova manualmente o estado de configuração no nível da floresta. O estado de configuração da floresta é mantido em um contêiner no contexto de nomeação de configuração do Active Directory. É possível descobrir e excluir da seguinte forma:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Remova manualmente todo o estado relacionado ao sysvol excluindo manualmente a seguinte pasta e todo o conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessário, esse caminho também poderá ser acessado localmente em um determinado controlador de domínio e o local padrão seria semelhante ao caminho a seguir:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Esse caminho será diferente se o compartilhamento sysvol foi configurado em um local não padrão.

## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes sobre a Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-faq.md)

Para obter mais informações sobre as listas de senhas proibidas globais e personalizadas, consulte o artigo [Proibir senhas incorretas](concept-password-ban-bad.md)
