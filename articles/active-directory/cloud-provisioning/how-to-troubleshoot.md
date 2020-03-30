---
title: Azure AD Connect cloud provisioning solução de problemas
description: Este artigo descreve como solucionar problemas que podem surgir com o agente de provisionamento de nuvem.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549478"
---
# <a name="cloud-provisioning-troubleshooting"></a>Solução de problemas de provisionamento de nuvens

O provisionamento de nuvens toca muitas coisas diferentes e tem muitas dependências diferentes. Este amplo escopo pode dar origem a vários problemas. Este artigo ajuda você a solucionar esses problemas. Ele introduz as áreas típicas para você se concentrar, como coletar informações adicionais e as várias técnicas que você pode usar para rastrear problemas.


## <a name="common-troubleshooting-areas"></a>Áreas comuns de solução de problemas

|Nome|Descrição|
|-----|-----|
|[Problemas com agentes](#agent-problems)|Verifique se o agente foi instalado corretamente e se ele se comunica com o Azure Active Directory (Azure AD).|
|[Problemas de sincronização de objetos](#object-synchronization-problems)|Use registros de provisionamento para solucionar problemas de sincronização de objetos.|
|[Provisionamento de problemas em quarentena](#provisioning-quarantined-problems)|Entenda os problemas de quarentena de provisionamento e como corrigi-los.|


## <a name="agent-problems"></a>Problemas com agentes
Algumas das primeiras coisas que você quer verificar com o agente são:

-  Está instalado?
-  O agente está correndo localmente?
-  O agente está no portal?
-  O agente está marcado como saudável?

Esses itens podem ser verificados no portal Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure

Para verificar se o agente é visto pelo Azure e está saudável, siga estes passos.

1. Entre no portal do Azure.
1. À esquerda, selecione **Azure Active Directory** > **Azure AD Connect**. No centro, selecione **Gerenciar provisionamento (visualização)**.
1. Na tela **azure AD Provisioning (preview),** selecione **Revisar todos os agentes**.

   ![Revise todos os agentes](media/how-to-install/install7.png)</br>
 
1. Na tela dos **agentes de provisionamento no local,** você vê os agentes que você instalou. Verifique se o agente em questão está lá e está marcado *como Saudável*.

   ![Tela de agentes de provisionamento no local](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verifique a porta

Para verificar se o Azure está ouvindo na porta 443 e se seu agente pode se comunicar com ele, use a seguinte ferramenta:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Este teste verifica se seus agentes podem se comunicar com o Azure pela porta 443. Abra um navegador e vá para a URL anterior do servidor onde o agente está instalado.

![Verificação da acessibilidade da porta](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local

Para verificar se o agente está em execução, siga estas etapas.

1. No servidor com o agente instalado, abra **serviços** navegando para ele ou indo para Iniciar > **Serviços de** > **Execução.msc**. **Start**
1. Em **Serviços,** certifique-se de que **o Microsoft Azure AD Connect Agent Updater** e o Microsoft **Azure AD Connect Provisioning Agent** estejam lá e seu status esteja em *execução*.

   ![Tela de serviços](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Problemas comuns de instalação do agente

As seções a seguir descrevem alguns problemas comuns de instalação do agente e resoluções típicas.

#### <a name="agent-failed-to-start"></a>O agente não conseguiu começar.

Você pode receber uma mensagem de erro que afirma:

**O serviço 'Microsoft Azure AD Connect Provisioning Agent' não foi iniciada. Verifique se você tem privilégios suficientes para iniciar os serviços do sistema.** 

Esse problema é normalmente causado por uma política de grupo que impediu que as permissões fossem aplicadas à conta de logon do SERVIÇO NT local criada pelo instalador (NT SERVICE\AADConnectProvisionprovisionAgent). Essas permissões são necessárias para iniciar o serviço.

Para resolver esse problema, siga estes passos.

1. Faça login no servidor com uma conta de administrador.
1. Abrir **serviços** navegando para ele ou indo para **Iniciar Serviços** > **de Execução.msc** > **Services.msc**.
1. Em **Serviços,** clique duas vezes no **Microsoft Azure AD Connect Provisioning Agent**.
1. Na guia **Log On,** **altere esta conta** para um admin de domínio. Em seguida, reinicie o serviço. 

   ![Guia de logon](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Os tempos de agente ou certificado são inválidos

Você pode receber a seguinte mensagem de erro quando tentar registrar o agente.

![Mensagem de erro de tempo](media/how-to-troubleshoot/troubleshoot4.png)

Esse problema geralmente é causado pelo agente não poder se conectar ao Serviço de Identidade Híbrida e exigir que você configure um proxy HTTP. Para resolver esse problema, configure um proxy de saída. 

O agente de provisionamento suporta o uso de um proxy de saída. Você pode configurá-lo editando o arquivo *c:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Adicione as seguintes linhas nele, no final do `</configuration>` arquivo pouco antes da tag de fechamento.
Substitua as `[proxy-server]` `[proxy-port]` variáveis e com os valores de nome e porta do servidor proxy.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Registro do agente falha com erro de segurança

Você pode receber uma mensagem de erro quando instalar o agente de provisionamento na nuvem.

Esse problema é normalmente causado pela não podeser ção do agente para executar os scripts de registro do PowerShell devido às políticas locais de execução do PowerShell.

Para resolver esse problema, altere as políticas de execução do PowerShell no servidor. Você precisa ter as políticas de máquina e usuário definidas como *Não definidas* ou *Remotas Assinadas*. Se eles forem definidos como *Irrestrito,* você verá este erro. Para obter mais informações, consulte [as políticas de execução do PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Arquivos de log

Por padrão, o agente emite mensagens de erro mínimas e empilha informações de rastreamento. Você pode encontrar esses registros de rastreamento na pasta *C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace*.

Para reunir detalhes adicionais para solucionar problemas relacionados a agentes, siga estas etapas.

1. Interrompa o serviço **Microsoft Azure AD Connect Provisioning Agent**.
1. Criar uma cópia do arquivo de configuração original: *C:\Arquivos do programa\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisionprovisionAgent.exe.config*.
1. Substitua a `<system.diagnostics>` seção existente pelo seguinte, e todas as mensagens de rastreamento irão para o arquivo *ProvAgentTrace.log*.

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. Inicie o serviço **Microsoft Azure AD Connect Provisioning Agent**.
1. Use o seguinte comando para seguir os problemas de arquivo e depuração. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problemas de sincronização de objetos

A seção a seguir contém informações sobre a sincronização do objeto de solução de problemas.

### <a name="provisioning-logs"></a>Provisionando logs

No portal Azure, os registros de provisionamento podem ser usados para ajudar a rastrear e solucionar problemas de sincronização de objetos. Para exibir os logs, **selecione Logs**.

![Botão Logs](media/how-to-troubleshoot/log1.png)

Os registros de provisionamento fornecem uma riqueza de informações sobre o estado dos objetos que estão sendo sincronizados entre o ambiente de Diretório Ativo no local e o Azure.

![Tela de registros de provisionamento](media/how-to-troubleshoot/log2.png)

Você pode usar as caixas de baixa na parte superior da página para filtrar a exibição para zero em problemas específicos, como datas. Clique duas vezes em um evento individual para ver informações adicionais.

![Informações da caixa de entrada de registros de provisionamento](media/how-to-troubleshoot/log3.png)

Essas informações fornecem etapas detalhadas e onde o problema de sincronização está ocorrendo. Desta forma, você pode identificar o ponto exato do problema.


## <a name="provisioning-quarantined-problems"></a>Provisionamento de problemas em quarentena

O provisionamento em nuvem monitora a saúde de sua configuração e coloca objetos insalubres em estado de quarentena. Se a maioria ou todas as chamadas feitas contra o sistema de destino falharem consistentemente por causa de um erro, por exemplo, credenciais de administrador inválidas, o trabalho de provisionamento é marcado como em quarentena.

![Status de quarentena](media/how-to-troubleshoot/quarantine1.png)

Ao selecionar o status, você pode ver informações adicionais sobre a quarentena. Você também pode obter o código de erro e a mensagem.

![Informações sobre status de quarentena](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Resolva uma quarentena

- Use o portal Azure para reiniciar o trabalho de provisionamento. Na página de configuração do agente, selecione **Reinicialização**.

  ![Reinicie o provisionamento](media/how-to-troubleshoot/quarantine3.png)

- Use o Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Você terá controle total sobre o que reiniciará. Você pode optar por limpar:
  - Escrows, para reiniciar o contador de depósito que se acumula para o status de quarentena.
  - Quarentena, para remover a aplicação da quarentena.
  - Marcas d' água. 
  
  Envie a seguinte solicitação:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)



