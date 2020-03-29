---
title: Implantar proteção por senha AD do Azure no local
description: Saiba como planejar e implantar o Azure AD Password Protection em um ambiente de serviços de domínio de diretório ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671705"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planeje e implante no local o Azure Active Directory Password Protection

Os usuários geralmente criam senhas que usam palavras locais comuns, como uma escola, equipe esportiva ou pessoa famosa. Essas senhas são fáceis de adivinhar e fracas contra ataques baseados em dicionários. Para impor senhas fortes em sua organização, o Azure Active Directory (Azure AD) Password Protection fornece uma lista de senhas banidas global e personalizada. Uma solicitação de alteração de senha falha se houver uma correspondência nesta lista de senhas banidas.

Para proteger o ambiente AD DS (Active Directory Domain Services, serviços de domínio de diretório ativo) no local, você pode instalar e configurar o Azure AD Password Protection para trabalhar com o seu DC on-prem. Este artigo mostra como instalar e registrar o serviço de proxy Azure AD Password Protection e o agente DC de Proteção por Senha AD do Azure em seu ambiente local.

Para obter mais informações sobre como o Azure AD Password Protection funciona em um ambiente local, consulte [Como reforçar a proteção por senha do Azure AD para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Estratégia de implantação

O diagrama a seguir mostra como os componentes básicos do Azure AD Password Protection funcionam juntos em um ambiente de Diretório Ativo no local:

![Como os componentes de Proteção de Senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa idéia rever como o software funciona antes de implantá-lo. Para obter mais informações, consulte [visão geral conceitual do Azure AD Password Protection](concept-password-ban-bad-on-premises.md).

Recomendamos que você inicie implantações no modo *de auditoria.* O modo de auditoria é a configuração inicial padrão, onde as senhas podem continuar a ser definidas. Senhas que seriam bloqueadas são registradas no registro do evento. Depois de implantar os servidores proxy e os agentes DC no modo de auditoria, monitore o impacto que a política de senha terá nos usuários quando a diretiva for aplicada.

Durante a fase de auditoria, muitas organizações consideram que as seguintes situações se aplicam:

* Eles precisam melhorar os processos operacionais existentes para usar senhas mais seguras.
* Os usuários geralmente usam senhas inseguras.
* Eles precisam informar os usuários sobre a próxima mudança na aplicação da segurança, possível impacto sobre eles e como escolher senhas mais seguras.

Também é possível que uma validação de senha mais forte afete a automação de implantação do controlador de domínio do Active Directory existente. Recomendamos que pelo menos uma promoção de DC e um rebaixamento dc aconteçam durante a avaliação do período de auditoria para ajudar a descobrir tais problemas. Para obter mais informações, consulte os seguintes artigos:

* [O Ntdsutil.exe não consegue definir uma senha fraca do modo de reparo dos serviços de diretório](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A promoção da réplica do controlador de domínio falha devido a uma senha fraca do modo de reparo do modo de reparo do diretório](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [O rebaixamento do controlador de domínio falha devido a uma senha de administrador local fraca](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Depois que o recurso estiver sendo executado no modo de auditoria por um período razoável, você pode alternar a configuração de *Auditoria* para *Aplicar* para exigir senhas mais seguras. Monitoramento adicional durante este tempo é uma boa idéia.

### <a name="multiple-forest-considerations"></a>Múltiplas considerações florestais

Não há requisitos adicionais para implantar a Proteção de Senha do Azure AD em várias florestas.

Cada floresta é configurada independentemente, conforme descrito na seção a seguir para implantar o [Azure AD Password Protection on-prem.](#download-required-software) Cada proxy de proteção por senha do Azure AD só pode suportar controladores de domínio da floresta a que está unido.

O software azure AD Password Protection em qualquer floresta desconhece o software de proteção por senha que é implantado em outras florestas, independentemente das configurações de confiança do Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Considerações do controlador de domínio somente leitura

A alteração de senha ou eventos de conjunto não são processados e persistidos em controladores de domínio somente leitura (RODCs). Em vez disso, eles são encaminhados para controladores de domínio graváveis. Você não precisa instalar o software de agente DC de proteção de senha AD do Azure em RODCs.

Além disso, não é suportado para executar o serviço proxy azure AD Password Protection em um controlador de domínio somente leitura.

### <a name="high-availability-considerations"></a>Considerações de alta disponibilidade

A principal preocupação com a proteção por senha é a disponibilidade de servidores proxy azure AD Password Protection quando os DCs em uma floresta tentam baixar novas políticas ou outros dados do Azure. Cada agente DC de Proteção por Senha AZure AD usa um algoritmo simples no estilo round-robin ao decidir qual servidor proxy chamar. O agente ignora servidores proxy que não estão respondendo.

Para as implantações de Diretório Ativo totalmente conectadas que tenham uma replicação saudável do estado da pasta de diretório e sysvol, dois servidores proxy azure AD Password Protection são suficientes para garantir a disponibilidade. Essa configuração resulta em download oportuno de novas políticas e outros dados. Você pode implantar servidores proxy adicionais de proteção por senha do Azure AD, se desejar.

O design do software de agente AZure AD Password Protection DC mitiga os problemas usuais que estão associados à alta disponibilidade. O agente DC de Proteção por Senha AD do Azure mantém um cache local da política de senha sbaixada mais recentemente. Mesmo que todos os servidores proxy registrados se tornem indisponíveis, os agentes DC de Proteção por Senha Do Azure AD continuam a impor sua política de senha armazenada em cache.

Uma freqüência de atualização razoável para políticas de senha em uma grande implantação geralmente é dias, não horas ou menos. Assim, breves paralisações dos servidores proxy não afetam significativamente a Proteção por Senha AD do Azure.

## <a name="deployment-requirements"></a>Requisitos de implantação

Para obter informações sobre licenciamento, consulte [os requisitos de licenciamento de proteção por senha do Azure AD](concept-password-ban-bad.md#license-requirements).

Os seguintes requisitos principais se aplicam:

* Todas as máquinas, incluindo controladores de domínio, que possuem componentes de Proteção de Senha AD do Azure instalados devem ter o Universal C Runtime instalado.
    * Você pode obter o tempo de execução certificando-se de que você tem todas as atualizações do Windows Update. Ou você pode obtê-lo em um pacote de atualização específico do sistema operacional. Para obter mais informações, consulte [Atualizar para O Tempo de Execução C Universal no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Você precisa de uma conta que tenha privilégios de administrador de domínio do Active Directory no domínio raiz da floresta para registrar a floresta do Diretório Ativo do Servidor do Windows com o Azure AD.
* O Serviço de Distribuição de Chaves deve ser habilitado em todos os controladores de domínio no domínio que executam o Windows Server 2012. Por padrão, este serviço é habilitado via início manual do gatilho.
* A conectividade de rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que hospeda o serviço proxy para a Proteção por Senha AD do Azure. Essa conectividade deve permitir que o controlador de domínio acesse a porta mapeadora de ponto final RPC 135 e a porta do servidor RPC no serviço proxy.
    * Por padrão, a porta do servidor RPC é uma porta RPC dinâmica, mas pode ser configurada para [usar uma porta estática](#static).
* Todas as máquinas onde o serviço Proxy de Proteção por Senha AD do Azure será instalada devem ter acesso à rede aos seguintes pontos finais:

    |**Ponto de extremidade**|**Finalidade**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Solicitações de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de Proteção de Senha do Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agente DC de Proteção por Senha AD do Azure

Os seguintes requisitos se aplicam ao agente DC de Proteção por Senha AD do Azure:

* Todas as máquinas onde o software de agente DC de proteção por senha AD do Azure será instalada devem executar o Windows Server 2012 ou posterior.
    * O domínio active directory ou floresta não precisa estar no Nível funcional de domínio do Windows Server 2012 (DFL) ou no nível funcional da floresta (FFL). Como mencionado em [Princípios de Design,](concept-password-ban-bad-on-premises.md#design-principles)não há dfl mínimo ou FFL necessários para que o agente DC ou o software proxy sejam executados.
* Todas as máquinas que executam o agente DC azure AD Password Protection DC devem ter o .NET 4.5 instalado.
* Qualquer domínio do Active Directory que execute o serviço dc de proteção de senha do Azure AD deve usar o DFSR (Distributed File System Replication, replicação do sistema de arquivos distribuídos) para replicação sysvol.
   * Se o seu domínio ainda não estiver usando o DFSR, você deve migrar antes de instalar o Azure AD Password Protection. Para obter mais informações, consulte [o Guia de Migração de Replicação SYSVOL: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > O software de agente DC de proteção de senha AD Azure será instalado atualmente em controladores de domínio em domínios que ainda estão usando FRS (a tecnologia predecessora para DFSR) para replicação sysvol, mas o software NÃO funcionará corretamente neste ambiente.
    >
    > Efeitos colaterais negativos adicionais incluem arquivos individuais que não conseguem se replicar, e procedimentos de restauração sysvol parecem ter sucesso, mas silenciosamente falhando em replicar todos os arquivos.
    >
    > Migre seu domínio para usar o DFSR o mais rápido possível, tanto para os benefícios inerentes do DFSR quanto para desbloquear a implantação do Azure AD Password Protection. As versões futuras do software serão automaticamente desativadas ao serem executados em um domínio que ainda está usando FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Serviço de proxy de proteção por senha do Azure AD

Os seguintes requisitos se aplicam ao serviço proxy de proteção por senha do Azure AD:

* Todas as máquinas onde o serviço de proxy azure AD Password Protection será instalado devem executar o Windows Server 2012 R2 ou posterior.

    > [!NOTE]
    > A implantação do serviço proxy azure AD Password Protection é um requisito obrigatório para implantar o Azure AD Password Protection, embora o controlador de domínio possa ter conectividade direta com a internet.

* Todas as máquinas onde o serviço proxy azure AD Password Protection será instalado devem ter .NET 4.7 instalado.
    * .NET 4.7 já deve ser instalado em um Servidor Windows totalmente atualizado. Se necessário, baixe e execute o instalador encontrado no [instalador off-line .NET Framework 4.7 para Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todas as máquinas que hospedam o serviço proxy de proteção por senha Azure AD devem ser configuradas para conceder aos controladores de domínio a capacidade de fazer logon no serviço proxy. Essa habilidade é controlada através da atribuição de privilégio "Acesse este computador a partir da rede".
* Todas as máquinas que hospedam o serviço proxy de proteção por senha Azure AD devem ser configuradas para permitir tráfego TLS 1.2 HTTP de saída.
* Uma conta *de administrador global* para registrar o serviço de proxy de proteção por senha AZure AD e floresta com o Azure AD.
* O acesso à rede deve ser habilitado para o conjunto de portas e URLs especificados nos [procedimentos de configuração](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)do ambiente Proxy do aplicativo .

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Pré-requisitos do Microsoft Azure AD Connect Agent Updater

O serviço Microsoft Ad AD Connect Agent Updater é instalado lado a lado com o serviço Proxy de Proteção por Senha AD do Azure AD. É necessária uma configuração adicional para que o serviço Microsoft Ad AD Connect Agent Updater possa funcionar:

* Se o ambiente usar um servidor proxy HTTP, siga as diretrizes especificadas no [Trabalho com servidores proxy existentes no local](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).
* O serviço Microsoft Azure AD Connect Agent Updater também requer as etapas TLS 1.2 especificadas nos [requisitos TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

> [!WARNING]
> O proxy de proteção por senha do Azure AD e o Proxy do aplicativo Azure AD instalam diferentes versões do serviço Microsoft Azure AD Connect Agent Updater, razão pela qual as instruções se referem ao conteúdo do Proxy do aplicativo. Essas diferentes versões são incompatíveis quando instaladas lado a lado, por isso não é recomendável instalar o Proxy de Proteção por Senha AD do Azure e o Proxy de Aplicativo na mesma máquina.

## <a name="download-required-software"></a>Baixar software necessário

Existem dois instaladores necessários para uma implantação de Proteção por Senha AD no local:

* Azure AD Password Protection DC agent *(AzureADPasswordProtectionDCAgentConfigurup.msi*)
* Proxy de proteção por senha Azure AD *(AzureADPasswordProtectionProxyConfigurup.exe*)

Baixe ambos os instaladores do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instale e configure o serviço proxy

O serviço de proxy ad password protection do Azure ad está normalmente em um servidor membro em seu ambiente AD DS no local. Uma vez instalado, o serviço de proxy Azure AD Password Protection se comunica com o Azure AD para manter uma cópia das listas de senhas banidas global e do cliente para o seu inquilino Azure AD.

Na próxima seção, você instala os agentes DC de Proteção por Senha AD do Azure em controladores de domínio em seu ambiente AD DS no local. Esses agentes DC se comunicam com o serviço proxy para obter as listas de senhas banidas mais recentes para uso ao processar eventos de alteração de senha dentro do domínio.

Escolha um ou mais servidores para hospedar o serviço proxy de proteção por senha AD do Azure AD. As seguintes considerações se aplicam aos servidores:

* Cada serviço desse tipo só pode fornecer políticas de senha para uma única floresta. A máquina hospedeira deve ser juntada a um domínio naquela floresta. Os domínios raiz e filho são suportados. Você precisa de conectividade de rede entre pelo menos um DC em cada domínio da floresta e a máquina de proteção por senha.
* Você pode executar o serviço proxy azure AD Password Protection em um controlador de domínio para testes, mas esse controlador de domínio requer conectividade à internet. Essa conectividade pode ser uma preocupação de segurança. Recomendamos esta configuração apenas para testes.
* Recomendamos pelo menos dois servidores proxy azure AD Password Protection para redundância, conforme observado na seção anterior sobre [considerações de alta disponibilidade](#high-availability-considerations).
* Não é suportado para executar o serviço proxy azure AD Password Protection em um controlador de domínio somente leitura.

Para instalar o serviço proxy de proteção por senha do Azure AD, complete as seguintes etapas:

1. Para instalar o serviço proxy azure AD Password Protection, execute o instalador de `AzureADPasswordProtectionProxySetup.exe` software.

    A instalação do software não requer uma reinicialização e pode ser automatizada usando procedimentos Padrão MSI, como no exemplo a seguir:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > O serviço de firewall do Windows `AzureADPasswordProtectionProxySetup.exe` deve estar em execução antes de instalar o pacote para evitar um erro de instalação.
    >
    > Se o Firewall do Windows estiver configurado para não ser executado, a solução será ativar temporariamente e executar o serviço de Firewall durante a instalação. O software proxy não tem dependência específica do Firewall do Windows após a instalação.
    >
    > Se você estiver usando um firewall de terceiros, ele ainda deve ser configurado para satisfazer os requisitos de implantação. Isso inclui permitir o acesso de entrada à porta 135 e à porta do servidor Proxy RPC. Para obter mais informações, consulte a seção anterior sobre [os requisitos de implantação](#deployment-requirements).

1. O software proxy azure AD Password Protection inclui `AzureADPasswordProtection`um novo módulo PowerShell, . As etapas seguintes executam vários cmdlets deste módulo PowerShell.

    Para usar este módulo, abra uma janela PowerShell como administrador e importe o novo módulo da seguinte forma:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Para verificar se o proxy do Azure AD Password Protection está sendo executado, use o seguinte comando PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    O resultado deve mostrar um **Status** de *Execução*.

1. O serviço proxy está sendo executado na máquina, mas não tem credenciais para se comunicar com o Azure AD. Registre o servidor proxy azure AD Password Protection `Register-AzureADPasswordProtectionProxy` com o Azure AD usando o cmdlet.

    Este cmdlet requer credenciais de administrador global para o seu inquilino Azure. Você também precisa de privilégios de administrador de domínio do Active Directory no local no domínio raiz da floresta. Este cmdlet também deve ser executado usando uma conta com privilégios de administrador local:

    Depois que este comando for bem sucedido uma vez para um serviço proxy de proteção por senha Azure AD, invocações adicionais dele são bem sucedidas, mas são desnecessárias.

    O `Register-AzureADPasswordProtectionProxy` cmdlet suporta os três modos de autenticação a seguir. Os dois primeiros modos suportam a autenticação multifatorial do Azure, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso perceptível antes da conclusão da primeira vez que este cmdlet é executado para um inquilino Específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

     * Modo de autenticação interativo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funciona nos sistemas operacionais Server Core. Em vez disso, use um dos seguintes modos de autenticação. Além disso, esse modo pode falhar se a configuração de segurança aprimorada do Internet Explorer estiver ativada. A solução é desativar essa configuração, registrar o proxy e, em seguida, reativá-lo.

     * Modo de autenticação de código de dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, siga o link para abrir um navegador da Web e digite o código de autenticação.

     * Modo de autenticação silenciosa (baseada em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se a autenticação multifatorial do Azure for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou, em vez disso, use uma conta diferente que não exija MFA.
        >
        > Você também pode ver o MFA necessário se o Registro de Dispositivo Azure (que é usado as capas pelo Azure AD Password Protection) tiver sido configurado para exigir globalmente o MFA. Para contornar esse requisito, você pode usar uma conta diferente que suporta MFA com um dos dois modos de autenticação anteriores, ou você também pode relaxar temporariamente o requisito mfa de registro de dispositivo azure.
        >
        > Para fazer essa alteração, procure e selecione **o Diretório Ativo do Azure** no portal Azure e selecione Dispositivos > **Configurações do dispositivo**. Conjunto **Exigir Auth multi-fator para juntar dispositivos** a *No*. Certifique-se de reconfigurar essa configuração de volta para *Yes* uma vez que o registro esteja concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

    No momento, você não precisa especificar o parâmetro *ForestCredential,* que é reservado para funcionalidades futuras.

    O registro do serviço de proxy de proteção por senha Azure AD é necessário apenas uma vez na vida do serviço. Depois disso, o serviço proxy azure AD Password Protection executará automaticamente qualquer outra manutenção necessária.

1. Agora registre a floresta do Active Directory no local com as credenciais necessárias para se comunicar com o Azure usando o `Register-AzureADPasswordProtectionForest` cmdlet PowerShell.

    > [!NOTE]
    > Se vários servidores proxy do Azure AD Password Protection estiverem instalados no ambiente, não importa qual servidor proxy você usa para registrar a floresta.

    O cmdlet requer credenciais de administrador global para o seu inquilino Do Zure. Você também deve executar este cmdlet usando uma conta com privilégios de administrador local. Também requer privilégios de administrador de empresas do Diretório Ativo no local. Esta etapa é executada uma vez por floresta.

    O `Register-AzureADPasswordProtectionForest` cmdlet suporta os três modos de autenticação a seguir. Os dois primeiros modos suportam a autenticação multifatorial do Azure, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso perceptível antes da conclusão da primeira vez que este cmdlet é executado para um inquilino Específico do Azure. A menos que uma falha seja relatada, não se preocupe com esse atraso.

     * Modo de autenticação interativo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funcionará nos sistemas operacionais Server Core. Em vez disso, use um dos dois modos de autenticação a seguir. Além disso, esse modo pode falhar se a configuração de segurança aprimorada do Internet Explorer estiver ativada. A solução é desativar essa configuração, registrar a floresta e, em seguida, reativá-la.  

     * Modo de autenticação de código de dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, siga o link para abrir um navegador da Web e digite o código de autenticação.

     * Modo de autenticação silenciosa (baseada em senha):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se a autenticação multifatorial do Azure for necessária para sua conta. Nesse caso, use um dos dois modos de autenticação anteriores ou, em vez disso, use uma conta diferente que não exija MFA.
        >
        > Você também pode ver o MFA necessário se o Registro de Dispositivo Azure (que é usado as capas pelo Azure AD Password Protection) tiver sido configurado para exigir globalmente o MFA. Para contornar esse requisito, você pode usar uma conta diferente que suporta MFA com um dos dois modos de autenticação anteriores, ou você também pode relaxar temporariamente o requisito mfa de registro de dispositivo azure.
        >
        > Para fazer essa alteração, procure e selecione **o Diretório Ativo do Azure** no portal Azure e selecione Dispositivos > **Configurações do dispositivo**. Conjunto **Exigir Auth multi-fator para juntar dispositivos** a *No*. Certifique-se de reconfigurar essa configuração de volta para *Yes* uma vez que o registro esteja concluído.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

       Esses exemplos só serão bem sucedidos se o usuário atualmente conectado for também um administrador de domínio do Active Directory para o domínio raiz. Se este não for o caso, você pode fornecer credenciais de domínio alternativas através do parâmetro *ForestCredential.*

    O registro da floresta do Diretório Ativo é necessário apenas uma vez na vida da floresta. Depois disso, os agentes Azure AD Password Protection DC na floresta realizam automaticamente qualquer outra manutenção necessária. Depois `Register-AzureADPasswordProtectionForest` de correr com sucesso para uma floresta, invocações adicionais do cmdlet são bem sucedidas, mas são desnecessárias.
    
    Para `Register-AzureADPasswordProtectionForest` ter sucesso, pelo menos um DC executando o Windows Server 2012 ou posterior deve estar disponível no domínio do servidor proxy Ad Password Protection do Azure AD. O software de agente DC de proteção por senha AD do Azure não precisa ser instalado em nenhum controlador de domínio antes desta etapa.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Configure o serviço proxy para se comunicar através de um proxy HTTP

Se o ambiente exigir o uso de um proxy HTTP específico para se comunicar com o Azure, use as seguintes etapas para configurar o serviço de proteção por senha AD do Azure.

Crie um arquivo *AzureAADPasswordProtectionProxy.exe.config* na `%ProgramFiles%\Azure AD Password Protection Proxy\Service` pasta. Inclua o seguinte conteúdo:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Se o proxy HTTP exigir autenticação, adicione a tag *useDefaultCredentials:*

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Em ambos os `http://yourhttpproxy.com:8080` casos, substitua-o pelo endereço e pela porta do servidor proxy HTTP específico.

Se o seu proxy HTTP estiver configurado para usar uma política de autorização, você deve conceder acesso à conta do computador Active Directory da máquina que hospeda o serviço proxy para proteção por senha.

Recomendamos que você pare e reinicie o serviço proxy de proteção por senha azure AD depois de criar ou atualizar o arquivo *AzureADPasswordProtectionProxy.exe.config.*

O serviço proxy não suporta o uso de credenciais específicas para se conectar a um proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Configure o serviço proxy para ouvir em uma porta específica

O software de agente DC de proteção por senha AD do Azure usa RPC sobre TCP para se comunicar com o serviço proxy. Por padrão, o serviço proxy Azure AD Password Protection ouve qualquer ponto final dinâmico do RPC disponível. Você pode configurar o serviço para ouvir em uma porta TCP específica, se necessário devido aos requisitos de topologia de rede ou firewall em seu ambiente.

<a id="static" /></a>Para configurar o serviço para executar `Set-AzureADPasswordProtectionProxyConfiguration` uma porta estática, use o cmdlet da seguinte forma:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Você deve parar e reiniciar o serviço proxy azure AD Password Protection para que essas alterações entrem em vigor.

Para configurar o serviço para executar em uma porta dinâmica, use o mesmo procedimento, mas defina *StaticPort* de volta a zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Você deve parar e reiniciar o serviço proxy azure AD Password Protection para que essas alterações entrem em vigor.

O serviço proxy azure AD Password Protection requer uma reinicialização manual após qualquer alteração na configuração da porta. Você não precisa reiniciar o serviço de agente DC de proteção de senha ad do Azure em controladores de domínio depois de fazer essas alterações de configuração.

Para consultar a configuração atual do `Get-AzureADPasswordProtectionProxyConfiguration` serviço, use o cmdlet como mostrado no exemplo a seguir

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

O exemplo a seguir mostra que o serviço proxy Azure AD Password Protection está usando uma porta dinâmica:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instale o serviço de agente DC

Para instalar o serviço de agente DC de `AzureADPasswordProtectionDCAgentSetup.msi` proteção por senha AD do Azure, execute o pacote.

Você pode automatizar a instalação do software usando procedimentos MSI padrão, como mostrado no exemplo a seguir:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

O `/norestart` sinalizador pode ser omitido se você preferir que o instalador reinicie automaticamente a máquina.

A instalação do software, ou desinstalação, requer uma reinicialização. Esse requisito é porque os DLLs do filtro de senha só são carregados ou descarregados por uma reinicialização.

A instalação do On-prem Azure AD Password Protection é concluída depois que o software do agente DC é instalado em um controlador de domínio e esse computador é reiniciado. Nenhuma outra configuração é necessária ou possível. Eventos de alteração de senha contra os DCs on-prem usam as listas de senhas banidas configuradas do Azure AD.

Para habilitar a proteção por senha ad do Azure no azure ou configurar senhas proibidas personalizadas, consulte [Habilitar no local o Azure AD Password Protection](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Você pode instalar o agente DC de proteção por senha AD do Azure em uma máquina que ainda não é um controlador de domínio. Neste caso, o serviço inicia e executa, mas permanece inativo até que a máquina seja promovida para ser um controlador de domínio.

## <a name="upgrading-the-proxy-service"></a>Atualizando o serviço proxy

O serviço proxy ad Password Protection do Azure AD suporta atualização automática. O upgrade automático usa o serviço Microsoft Ad Connect Agent Updater, que é instalado lado a lado com o serviço proxy. O upgrade automático está ativado por padrão e `Set-AzureADPasswordProtectionProxyConfiguration` pode ser ativado ou desativado usando o cmdlet.

A configuração atual pode ser `Get-AzureADPasswordProtectionProxyConfiguration` consultada usando o cmdlet. Recomendamos que a configuração de atualização automática esteja sempre ativada.

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para consultar a versão de software de todos os servidores proxy ad Password Protection atualmente instalados em uma floresta.

### <a name="manual-upgrade-process"></a>Processo de atualização manual

Uma atualização manual é realizada executando `AzureADPasswordProtectionProxySetup.exe` a versão mais recente do instalador de software. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do serviço proxy Azure AD Password Protection - o instalador realiza uma atualização no local. Nenhuma reinicialização deve ser necessária ao atualizar o serviço proxy. A atualização de software pode ser automatizada usando `AzureADPasswordProtectionProxySetup.exe /quiet`procedimentos Padrão MSI, tais como .

## <a name="upgrading-the-dc-agent"></a>Atualizando o agente DC

Quando uma versão mais recente do software de agente AD Password Protection DC do Azure AD está disponível, a atualização é realizada executando a versão mais recente do `AzureADPasswordProtectionDCAgentSetup.msi` pacote de software. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software do agente DC - o instalador realiza uma atualização no local. Uma reinicialização é sempre necessária ao atualizar o software do agente DC - esse requisito é causado pelo comportamento principal do Windows.

A atualização de software pode ser automatizada usando `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`procedimentos Padrão MSI, tais como .

Você pode omitir o `/norestart` sinalizador se preferir que o instalador reinicie automaticamente a máquina.

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser usado para consultar a versão do software de todos os agentes AD Password Protection DC atualmente instalados em uma floresta.

## <a name="next-steps"></a>Próximas etapas

Agora que você instalou os serviços necessários para o Azure AD Password Protection em seus servidores locais, [habilite o Azure AD Password Protection no portal Azure](howto-password-ban-bad-on-premises-operations.md) para concluir sua implantação.
