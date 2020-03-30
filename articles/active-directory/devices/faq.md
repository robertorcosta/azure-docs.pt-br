---
title: Perguntas frequentes sobre o gerenciamento de dispositivo do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497526"
---
# <a name="azure-active-directory-device-management-faq"></a>Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory

## <a name="general-faq"></a>Faq geral

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P: Registrei o dispositivo recentemente. Por que não posso ver o dispositivo sob as informações do meu usuário no portal Azure? Ou, por que o proprietário do dispositivo está marcado como N/D para dispositivos ingressados no Azure Active Directory (Azure AD) híbrido?

**A:** Os dispositivos windows 10 que são híbridos aderidos ao Azure AD não aparecem em **dispositivos DE USUÁRIO**.
Use o modo de exibição **Todos os dispositivos** no portal do Azure. Você também pode usar um cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) do PowerShell.

Apenas os dispositivos a seguir estão listados sob os **dispositivos do usuário**:

- Todos os dispositivos pessoais que não são ingressados no Azure AD híbrido. 
- Todos os dispositivos não Windows 10 ou Windows Server 2016.
- Todos os dispositivos não Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>P: Como saber qual é o estado de registro do dispositivo do cliente?

**A:** No portal Azure, vá para **Todos os dispositivos**. Procure o dispositivo usando a identificação do dispositivo. Verifique o valor na coluna de tipo de associação. Às vezes, o dispositivo pode ter sido redefinido ou recriado. Portanto, é essencial verificar também o estado do registro do dispositivo no dispositivo:

- Para dispositivos Windows 10 e Windows Server 2016 ou posterior, execute `dsregcmd.exe /status`.
- Para versões de sistema operacional de nível inferior, execute `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**A:** Para obter informações sobre solução de problemas, consulte estes artigos:
- [Dispositivos de solução de problemas usando o comando dsregcmd](troubleshoot-device-dsregcmd.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas o Azure Active Directory híbrido juntou-se a dispositivos de nível baixo](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P: Vejo o registro do dispositivo as informações do USUÁRIO no portal Azure. E posso ver o estado como registrado no dispositivo. Estou configurado corretamente para usar o Acesso Condicional?

**A:** O dispositivo join state, mostrado pelo **deviceID,** deve corresponder ao estado no Azure AD e atender a quaisquer critérios de avaliação para acesso condicional. Para obter mais informações, consulte [Exigir dispositivos gerenciados para acesso a aplicativos em nuvem com Acesso Condicional](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>P: Por que meus usuários veem uma mensagem de erro dizendo "Sua organização excluiu o dispositivo" ou "Sua organização desativou o dispositivo" em seus dispositivos Windows 10?

**A:** Nos dispositivos Windows 10 unidos ou registrados com o Azure AD, os usuários são emitidos um [token de atualização primária (PRT)](concept-primary-refresh-token.md) que permite o sinal único ligado. A validade do PRT baseia-se na validade do próprio dispositivo. Os usuários veem esta mensagem se o dispositivo estiver excluído ou desativado no Azure AD sem insuma a ação do próprio dispositivo. Um dispositivo pode ser excluído ou desativado no Azure AD um dos seguintes cenários: 

- O usuário desativa o dispositivo do portal Meus Aplicativos. 
- Um administrador (ou usuário) exclui ou desativa o dispositivo no portal Dozure ou usando o PowerShell
- O Ad do Azure híbrido aderiu apenas: um administrador remove os dispositivos OU fora do escopo de sincronização, resultando na exclusão dos dispositivos do Azure AD
- Atualizando o Azure AD conecte-se à versão 1.4.xx.x. [Entendendo o Azure AD Connect 1.4.xx.x e o desaparecimento do dispositivo](/azure/active-directory/hybrid/reference-connect-device-disappearance).


Veja abaixo como essas ações podem ser corrigidas.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>P: Desabilitei ou excluí meu dispositivo no portal Azure ou usando o Windows PowerShell. Mas o estado local no dispositivo diz que ele ainda está registrado. O que devo fazer?

**A:** Esta operação é por projeto. Neste caso, o dispositivo não tem acesso a recursos na nuvem. Os administradores podem executar essa ação para dispositivos obsoletos, perdidos ou roubados para impedir o acesso não autorizado. Se esta ação foi realizada sem querer, você precisará reativar ou recadastrar o dispositivo conforme descrito abaixo

- Se o dispositivo foi desativado no Azure AD, um administrador com privilégios suficientes pode habilitá-lo a partir do portal Azure AD  
  > [!NOTE]
  > Se você estiver sincronizando dispositivos usando o Azure AD Connect, os dispositivos adeptos híbridos do Azure AD serão automaticamente reativados durante o próximo ciclo de sincronização. Então, se você precisa desativar um dispositivo adepto a Ad híbrido do Azure, você precisa desabilitá-lo do seu AD no local

 - Se o dispositivo for excluído no Azure AD, você precisará recadastrar o dispositivo. Para recadastrar, você deve tomar uma ação manual no dispositivo. Veja abaixo as instruções para o recadastramento com base no estado do dispositivo. 

      Para recadastrar o Azure AD híbrido juntou-se aos dispositivos Windows 10 e Windows Server 2016/2019, tome as seguintes etapas:

      1. Abra o prompt de comando como administrador.
      1. Digite `dsregcmd.exe /debug /leave`.
      1. Saia e entre para disparar a tarefa agendada que registra o dispositivo com o Azure AD novamente. 

      Para versões do Sistema Operacional Windows de nível baixo que são aderidas ao Azure AD híbrido, tome as seguintes etapas:

      1. Abra o prompt de comando como administrador.
      1. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Para que o Azure AD se junte aos dispositivos Com Windows 10, tome as seguintes etapas:

      1. Abra o prompt de comando como administrador
      1. Digite `dsregcmd /forcerecovery` (Nota: Você precisa ser um administrador para executar esta ação).
      1. Clique em "Entrar" na caixa de diálogo que abre e continue com o login em processo.
      1. Faça login e faça login no dispositivo para completar a recuperação.

      Para dispositivos Com OZure AD registrados com Windows 10, tome as seguintes etapas:

      1. Vá para **Configurações** > **Contas** > **Acessatrabalho ou Escola**. 
      1. Selecione a conta e selecione **Desconectar**.
      1. Clique em "+ Conectar" e registre o dispositivo novamente através do processo de login.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>P: Por que vejo entradas duplicadas de dispositivos no portal Azure?

**Um:**

- Para o Windows 10 e o Windows Server 2016, tentativas repetidas de desunir e reingressar no mesmo dispositivo podem resultar em entradas duplicadas. 
- Cada usuário do Windows que usar **Adicionar Conta Corporativa ou de Estudante** cria um novo registro do dispositivo com o mesmo nome do dispositivo.
- Para versões do sistema operacional do Windows de nível inferior que são ingressadas no domínio do Azure Directory local, o registro automático cria um novo registro de dispositivo com o mesmo nome do dispositivo para cada usuário de domínio que entra no dispositivo. 
- Uma máquina conectada do Azure AD que foi limpa, reinstalada e reunida com o mesmo nome é exibida como outro registro com o mesmo nome de dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>P: O registro do dispositivo Windows 10 no Azure AD suporta TPMs no modo FIPS?

**A:** O registro do dispositivo do Windows 10 só é suportado para OTPM 2.0 compatível com FIPS e não suportado para OTPM 1.2. Se seus dispositivos tiverem o TPM 1.2 compatível com FIPS, você deve desabilitá-los antes de prosseguir com a adesão do Azure AD ou a adesão ao Hybrid Azure AD. A Microsoft não fornece nenhuma ferramenta para desativar o modo FIPS para TPMs, pois depende do fabricante de TPM. Entre em contato com seu OEM de hardware para obter suporte. 

---

**P: Por que um usuário ainda pode acessar recursos de um dispositivo que desabilitei no portal Azure?**

**A:** Leva até uma hora para que uma revogação seja aplicada a partir do momento em que o dispositivo Azure AD é marcado como desativado.

>[!NOTE] 
>Para dispositivos registrados, é recomendável apagar o dispositivo para garantir que os usuários não possam acessar os recursos. Para obter mais informações, confira [O que é o registro de dispositivo?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>P: Por que existem dispositivos marcados como Pendentes a coluna REGISTRADA no portal Azure?

**A:** Pendente indica que o dispositivo não está registrado. Este estado indica que um dispositivo foi sincronizado usando o azure AD conectado a partir de um AD no local e está pronto para registro do dispositivo. Esses dispositivos têm o TIPO DE JOIN definido como "Hybrid Azure AD joined". Saiba mais sobre [como planejar sua implementação híbrida do Azure Active Directory](hybrid-azuread-join-plan.md).

>[!NOTE]
>Um dispositivo também pode mudar de ter um estado registrado para "Pendente"
>* Se um dispositivo for excluído do Azure AD primeiro e resincronizado a partir de um AD no local.
>* Se um dispositivo for removido de um escopo de sincronização no Azure AD Connect e for adicionado de volta.
>
>Em ambos os casos, você deve recadastrar o dispositivo manualmente em cada um desses dispositivos. Para analisar se o dispositivo foi previamente registrado, você pode [solucionar dispositivos usando o comando dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>P: Como faço para aderir a um Azure AD aderido localmente ao dispositivo?

**A:** Para dispositivos aderidos ao Azure AD puros, certifique-se de ter uma conta de administrador local offline ou criar uma. Você não pode entrar com as credenciais de usuário do Azure AD. Em seguida, vá para **Configurações** > **Contas** > **Acessam Trabalho ou Escola**. Selecione sua conta e, em seguida, **Desconectar**. Siga os prompts e forneça as credenciais de administrador local, quando solicitado. Reinicie o dispositivo para concluir o processo de cancelar a associação.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>P: O login dos meus usuários no Azure AD pode se juntar a dispositivos que são excluídos ou desativados no Azure AD?

**A:** Sim. O Windows tem uma funcionalidade de armazenamento em cache de nome de usuário e senha que permite aos usuários que se conectaram anteriormente acessarem a área de trabalho rapidamente, mesmo sem conectividade de rede. 

Quando um dispositivo é excluído ou desabilitado no Azure AD, ele não é reconhecido pelo dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente continuam a acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. Mas como o dispositivo é excluído ou desativado, os usuários não podem acessar nenhum recurso protegido pelo Acesso Condicional baseado em dispositivos. 

Os usuários que não se conectaram anteriormente não podem acessar o dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>P: Um usuário desativado ou excluído pode entrar em um AD aderido ao Azure

**A:** Sim, mas só por um tempo limitado. Quando um usuário é excluído ou desabilitado no Azure AD, ele não é imediatamente conhecido para o dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente podem acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. 

Normalmente, o dispositivo está ciente do estado do usuário em menos de quatro horas. Em seguida, o Windows bloqueia o acesso desses usuários à área de trabalho. Como o usuário é excluído ou desabilitado no Azure AD, todos os seus tokens são revogados. Portanto, eles não podem acessar nenhum recurso. 

Os usuários excluídos ou desabilitados que não se conectaram anteriormente não podem acessar um dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>P: Por que meus usuários têm problemas com dispositivos aderidos ao Azure AD depois de alterar sua UPN?

**A:** Atualmente, as alterações upn não são totalmente suportadas em dispositivos aderidos ao Azure AD. Portanto, a autenticação com o Azure AD falha após o UPN mudar. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, os usuários precisam entrar no Windows por meio do bloco "Outro usuário" usando o novo UPN para resolver esse problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P: Meus usuários não podem pesquisar impressoras do Azure AD unidos dispositivos. Como habilitar a impressão usando esses dispositivos?

**A:** Para implantar impressoras para dispositivos azure AD unidos, consulte [Implantar o Windows Server Hybrid Cloud Print com pré-autenticação](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Você precisa de um Windows Server local para implantar a impressão de nuvem híbrida. Atualmente, o serviço de impressão baseado em nuvem não está disponível. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>P: Como me conecto a um dispositivo aderido a um Azure AD remoto?

**A:** Consulte [Conecte-se ao PC com o Azure Active Directory](/windows/client-management/connect-to-remote-aadj-pc)remoto .

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P: Por que meus usuários veem *que você não pode chegar lá daqui?*

**A:** Você configurou certas regras de acesso condicional para exigir um estado específico do dispositivo? Se o dispositivo não atender aos critérios, os usuários são bloqueados e veem essa mensagem. Avalie as regras da política de acesso condicional. Verifique se o dispositivo atende aos critérios para evitar a mensagem.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>P: Por que alguns dos meus usuários não recebem solicitações de autenticação multifatorial do Azure nos dispositivos aderidos ao Azure AD?

**A:** Um usuário pode participar ou registrar um dispositivo com o Azure AD usando a Autenticação Multifatorial. Em seguida, o próprio dispositivo torna-se um segundo fator confiável para esse usuário. Sempre que o mesmo usuário faz logon no dispositivo e acessa um aplicativo, o Azure AD considera o dispositivo como um segundo fator. Ele permite que o usuário acesse diretamente os aplicativos sem prompts de autenticação multifator adicionais. 

Esse comportamento:

- É aplicável aos dispositivos associados do Azure AD e aos dispositivos registrados do Azure AD, mas não aos dispositivos associados híbridos do Azure AD.
- Não é aplicável a qualquer outro usuário que se conecte a esse dispositivo. Portanto, todos os outros usuários que acessam esse dispositivo serão apresentados a um desafio de autenticação multifator. Só então eles poderão acessar os aplicativos que exigem a autenticação multifator.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>P: Por que eu recebo um *nome de usuário ou senha é* mensagem incorreta para um dispositivo que acabei de aderir ao Azure AD?

**A:** As razões comuns para este cenário são as seguintes:

- Suas credenciais de usuário não são mais válidas.
- O computador não consegue se comunicar com o Azure Active Directory. Verifique se há problemas de conectividade de rede.
- Credenciais federadas requerem que o servidor de federação dê suporte a pontos de extremidade do WS-Trust que estejam habilitados e acessíveis. 
- Você habilitou a autenticação de passagem. Portanto, sua senha temporária precisa ser alterada quando você entrar.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q: Por que eu vejo *os Oops... ocorreu um erro!* dialogar quando eu tento Azure AD se juntar ao meu PC?

**A:** Esse erro acontece quando você configura a inscrição do Azure Active Directory com o Intune. Certifique-se de que o usuário que tenta fazer o ingresso do Azure AD tenha a licença correta do Intune atribuída. Para obter mais informações, confira [Configurar registro para dispositivos Windows](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>P: Por que minha tentativa de azure AD se juntar a um PC falhou, embora eu não tenha obter nenhuma informação de erro?

**A:** Uma causa provável é que você fez o adesão ao dispositivo usando a conta de administrador incorporada local. Crie uma conta local diferente antes de usar o ingresso do Azure Active Directory para concluir a configuração. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>P: Quais são os certificados de MS-Organization-P2P-Access presentes em nossos dispositivos Windows 10?

**A:** Os certificados MS-Organization-P2P-Access são emitidos pelo Azure AD para ambos, a adesão do Azure AD e dispositivos híbridos Azure AD. Esses certificados são usados para habilitar a confiança entre os dispositivos no mesmo locatário para cenários de área de trabalho remotos. Um certificado é emitido para o dispositivo e o outro é emitido para o usuário. O certificado do dispositivo está presente no `Local Computer\Personal\Certificates` e é válido por um dia. Esse certificado é renovado (emitindo um novo certificado) se o dispositivo ainda estiver ativo no Azure AD. O certificado de usuário está presente no `Current User\Personal\Certificates` e esse certificado também é válido por um dia, mas é emitido sob demanda quando um usuário tenta uma sessão de área de trabalho remota para outro dispositivo ingressado do Azure AD. Ele não é renovado após a expiração. Esses dois certificados são emitidos usando o certificado MS-Organization-P2P-Access presente no `Local Computer\AAD Token Issuer\Certificates`. Esse certificado é emitido pelo Azure AD durante o registro do dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>P: Por que vejo vários certificados expirados emitidos pelo MS-Organization-P2P-Access em nossos dispositivos Windows 10? Como exclui-los?

**A:** Houve um problema identificado na versão 1709 do Windows 10 e menor onde os certificados MS-Organization-P2P-Access expirados continuaram a existir na loja de computadores por causa de problemas criptográficos. Seus usuários podem enfrentar problemas com a conectividade de rede, se você estiver usando quaisquer clientes VPN (por exemplo, Cisco AnyConnect) que não possam lidar com o grande número de certificados vencidos. Esse problema foi corrigido no Windows 10 versão 1803 para excluir automaticamente esses certificados MS-Organization-P2P-Access expirados. Você pode resolver esse problema atualizando seus dispositivos para o Windows 10 versão 1803. Se você não conseguir atualizar, você pode excluir esses certificados sem que seja causado nenhum impacto negativo.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD Híbrido

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>P: Como faço para aderir a um AD Híbrido Azure aderido localmente no dispositivo?

**A:** Para dispositivos híbridos Azure AD, certifique-se de desativar o registro automático. A tarefa agendada não registrará o dispositivo novamente. Agora, abra um prompt de comando como administrador e digite `dsregcmd.exe /debug /leave`. Ou execute este comando como um script em vários dispositivos para fazer um cancelamento de associação em massa.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>P: Onde posso encontrar informações de solução de problemas para diagnosticar falhas híbridas do Azure AD?

**A:** Para obter informações sobre solução de problemas, consulte estes artigos:

- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas o Azure Active Directory híbrido juntou-se a dispositivos de nível baixo](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>P: Por que vejo um registro de AD azure azure duplicado para o meu dispositivo azure AD híbrido do Windows 10 na lista de dispositivos AD do Azure?

**A:** Quando seus usuários adicionam suas contas a aplicativos em um dispositivo com domínio, eles podem ser solicitados com **adicionar conta ao Windows?** Se o usuário escolher **Sim** no prompt, o dispositivo registra com o Azure AD. O tipo de relação de confiança é marcado como registrado no Azure AD. Depois de habilitar o ingresso do Azure AD híbrido na organização, o dispositivo também será incluído no Azure AD híbrido. Em seguida, dois estados de dispositivo aparecem para o mesmo dispositivo. 

O ingresso do Azure AD híbrido tem precedência sobre o estado de registrado pelo Azure AD. Assim, seu dispositivo é considerado híbrido Azure AD unido para qualquer autenticação e avaliação de Acesso Condicional. Você pode excluir com segurança o registro do dispositivo registrado pelo Azure AD no portal do Azure AD. Saiba como [evitar ou limpar esse estado duplo no computador com Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>P: Por que meus usuários têm problemas no Azure AD híbrido do Windows 10 após mudar em seu UPN?

**A:** Atualmente, as alterações da UPN não são totalmente suportadas com dispositivos aderidos híbridos do Azure AD. Embora os usuários possam entrar dispositivo e acessar seus aplicativos locais, a autenticação com o Azure AD falhará após a alteração de um UPN. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, você precisa desjuntar o dispositivo do Azure AD (executar "dsregcmd /leave" com privilégios elevados) e voltar (acontece automaticamente) para resolver o problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>P: O Windows 10 híbrido Azure AD juntou dispositivos que exigem linha de visão para o controlador de domínio para obter acesso aos recursos da nuvem?

**A:** Não, exceto quando a senha do usuário é alterada. Depois que o Azure AD híbrido do Windows 10 estiver completo e o usuário tiver feito o registro pelo menos uma vez, o dispositivo não requer linha de visão para o controlador de domínio para acessar recursos na nuvem. O Windows 10 pode obter o login único em aplicativos AD do Azure de qualquer lugar com uma conexão à internet, exceto quando uma senha é alterada. Os usuários que fazem login com o Windows Hello for Business continuam a obter o login único nos aplicativos Azure AD mesmo após uma alteração de senha, mesmo que não tenham linha de visão para seu controlador de domínio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>P: O que acontece se um usuário alterar sua senha e tentar fazer login no seu dispositivo azure AD híbrido do Windows 10 fora da rede corporativa?

**A:** Se uma senha for alterada fora da rede corporativa (por exemplo, usando o Azure AD SSPR), o usuário entrará com a nova senha falhará. Para dispositivos híbridos azure AD, o Active Directory no local é a autoridade principal. Quando um dispositivo não tem linha de visão para o controlador de domínio, ele não consegue validar a nova senha. Assim, o usuário precisa estabelecer conexão com o controlador de domínio (via VPN ou estando na rede corporativa) antes de poder entrar no dispositivo com sua nova senha. Caso contrário, eles só podem fazer login com sua senha antiga por causa do recurso de login em cache no Windows. No entanto, a senha antiga é invalidada pelo Azure AD durante as solicitações de token e, portanto, impede o logon único e falha em quaisquer políticas de acesso condicional baseadas em dispositivos. Este problema não ocorre se você usar o Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Perguntas frequentes sobre o registro do Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>P: Como removo um estado registrado no Azure AD para um dispositivo localmente?

**Um:** 
- Para dispositivos registrados no Windows 10 Azure AD, vá para **Configurações** > **Contas** > **Acesse trabalho ou escola**. Selecione sua conta e, em seguida, **Desconectar**. O registro do dispositivo é por perfil de usuário no Windows 10.
- Para iOS e Android, você pode usar o aplicativo Autenticador Microsoft Configurações**Registro do dispositivo** e selecionar dispositivo **Descadastramento** **Settings** > .
- Para macOS, você pode usar o aplicativo Microsoft Intune Company Portal para descadastrar o dispositivo do gerenciamento e remover qualquer registro. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>P: Como posso impedir os usuários de adicionar contas de trabalho adicionais (Azure AD registrada) em meus dispositivos corporativos com Windows 10?

**A:** Habilite o registro a seguir para bloquear que seus usuários adicionem contas de trabalho adicionais ao seu domínio corporativo aderido, o Azure AD se juntou ou o Azure AD híbrido juntou-se aos dispositivos Windows 10. Essa política também pode ser usada para bloquear as máquinas de domínio que se juntam inadvertidamente para obter o AD do Azure registrado na mesma conta de usuário. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P: É possível registrar dispositivos Android ou iOS BYOD?

**A:** Sim, mas apenas com o serviço de registro de dispositivos Azure e para clientes híbridos. Ele não é compatível com o serviço de registro de dispositivo local nos Serviços de Federação do Active Directory (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>P: Como posso registrar um dispositivo macOS?

**A:** Tome as seguintes etapas:

1.    [Criar uma política de conformidade](/intune/compliance-policy-create-mac-os)
1.    [Defina uma política de acesso condicional para dispositivos macOS](../active-directory-conditional-access-azure-portal.md) 

**Comentários:**

- Os usuários incluídos em sua política de acesso condicional precisam de uma [versão suportada do Office para macOS](../conditional-access/concept-conditional-access-conditions.md) para acessar recursos. 
- Durante a primeira tentativa de acesso, os usuários são solicitados a registrar o dispositivo usando o portal da empresa.

---
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [dispositivos registrados no Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD híbrido](concept-azure-ad-join-hybrid.md)
