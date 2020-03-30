---
title: Solucionar problemas no servidor de processo de recuperação do site do Azure
description: Este artigo descreve como solucionar problemas com o servidor de processo de recuperação do site do Azure
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256855"
---
# <a name="troubleshoot-the-process-server"></a>Solucionar problemas do servidor de processo

O servidor de processo [de recuperação do site](site-recovery-overview.md) é usado quando você configura a recuperação de desastres no Azure para VMMs e servidores físicos VMware no local. Este artigo descreve como solucionar problemas com o servidor de processo, incluindo problemas de replicação e conectividade.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o servidor de processos.

## <a name="before-you-start"></a>Antes de começar

Antes de começar a solucionar problemas:

1. Certifique-se de entender como [monitorar servidores de processo](vmware-physical-azure-monitor-process-server.md).
2. Reveja as melhores práticas abaixo.
3. Certifique-se de seguir [considerações de capacidade](site-recovery-plan-capacity-vmware.md#capacity-considerations)e use orientação de dimensionamento para o [servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou [servidores de processo autônomos](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Práticas recomendadas para implantação de servidores de processo

Para o melhor desempenho dos servidores de processo, resumimos uma série de práticas recomendadas gerais.

**Melhor prática** | **Detalhes**
--- |---
**Uso** | Certifique-se de que o servidor de configuração/servidor de processo autônomo só seja usado para o propósito pretendido. Não coloque mais nada na máquina.
**Endereço IP** | Certifique-se de que o servidor de processo tenha um endereço IPv4 estático e não tenha o NAT configurado.
**Controle o uso da memória/CPU** |Mantenha o uso de CPU e memória abaixo de 70%.
**Garantir espaço livre** | O espaço livre refere-se ao espaço em disco de cache no servidor de processo. Os dados de replicação são armazenados no cache antes de serem carregados no Azure.<br/><br/> Mantenha espaço livre acima de 25%. Se for abaixo de 20%, a replicação é estrangulada para máquinas replicadas que estão associadas ao servidor de processo.

## <a name="check-process-server-health"></a>Verifique a saúde do servidor do processo

O primeiro passo para solucionar problemas é verificar a saúde e o status do servidor de processo. Para fazer isso, revise todos os alertas, verifique se os serviços necessários estão sendo executados e verifique se há um batimento cardíaco do servidor de processo. Essas etapas são resumidas no gráfico a seguir, seguidos de procedimentos para ajudá-lo a executar as etapas.

![Solucionar problemas na saúde do servidor do processo](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Passo 1: Solucionar problemas alertas de saúde do servidor de processos

O servidor de processos gera uma série de alertas de saúde. Esses alertas e ações recomendadas são resumidos na tabela a seguir.

**Tipo de alerta** | **Erro** | **Solução de problemas**
--- | --- | --- 
![Healthy][green] | Nenhum  | O servidor de processos está conectado e saudável.
![Aviso][yellow] | Os serviços especificados não estão sendo executados. | 1. Verifique se os serviços estão em execução.<br/> 2. Se os serviços estiverem funcionando como esperado, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Aviso][yellow]  | A utilização da CPU > 80% nos últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado a [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  O uso da CPU > 95% nos últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado a [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planejador de implantação](https://aka.ms/asr-v2a-deployment-planner) para replicação do vMware/servidor físico.
![Aviso][yellow] | O uso da memória > 80% nos últimos 15 minutos. |  1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs que usam o servidor de processo está alinhado a [limites definidos](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga todas as instruções associadas ao aviso.<br/> 4. Se o problema persistir, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] | O uso da memória > 95% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considere configurar um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/> 2. Siga todas as instruções associadas ao aviso.<br/> 3. 4. Se o problema continuar, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute o [Planejador de implantação](https://aka.ms/asr-v2a-deployment-planner) para problemas de replicação do VMware/servidor físico.
![Aviso][yellow] | O espaço livre da pasta de cache < 30% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considere configurar um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>2. Verifique se o número de VMs usando o servidor de processo está alinhado às [diretrizes](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Espaço livre < 25% nos últimos 15 minutos | 1. Siga as instruções associadas ao aviso para esta questão.<br/> 2. 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação.](#check-connectivity-and-replication)<br/> 3. Se o problema persistir, execute o [Planejador de implantação](https://aka.ms/asr-v2a-deployment-planner) para replicação do vMware/servidor físico.
![Crítico][red] | Sem batimentos cardíacos do servidor de processo por 15 minutos ou mais. O serviço tmansvs não está se comunicando com o servidor de configuração. | 1) Verifique se o servidor de processo está em funcionamento.<br/> 2. Verifique se o tmassvc está sendo executado no servidor de processo.<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).


![Chave de tabela](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Passo 2: Verifique os serviços do servidor de processos

Os serviços que devem estar em execução no servidor de processo são resumidos na tabela a seguir. Há pequenas diferenças nos serviços, dependendo de como o servidor de processo é implantado. 

Para todos os serviços, exceto o Microsoft Azure Recovery Services Agent (obengine), verifique se o StartType está configurado como **Automático** ou **Automático (Início atrasado)**.
 
**Implantação** | **Serviços em execução**
--- | ---
**Servidor de processo no servidor de configuração** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de upload de log (Logupload); Serviço de aplicação do InMage Scout; Agente de serviços de recuperação do Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; Serviço Mundial de Publicação Web (W3SVC); Mysql; Serviço de recuperação do site do Microsoft Azure (dra)
**Servidor de processo em execução como um servidor autônomo** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de upload de log (Logupload); Serviço de aplicação do InMage Scout; Agente de serviços de recuperação do Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Servidor de processo implantado no Azure para failback** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de upload de log (Logupload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Passo 3: Verifique o batimento cardíaco do servidor de processo

Se não houver batimentocardíaco do servidor de processo (código de erro 806), faça o seguinte:

1. Verifique se a VM do servidor de processo está em funcionamento.
2. Verifique se há erros nesses registros.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Verifique conectividade e replicação

 Falhas de replicação iniciais e contínuas são frequentemente causadas por problemas de conectividade entre as máquinas de origem e o servidor de processo, ou entre o servidor de processo e o Azure. Essas etapas são resumidas no gráfico a seguir, seguidos de procedimentos para ajudá-lo a executar as etapas.

![Solucionar problemas de conectividade e replicação](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Passo 4: Verifique a sincronização de tempo na máquina de origem

Certifique-se de que a data/hora do sistema para a máquina replicada está em sincronia. [Saiba mais](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Passo 5: Verifique o software antivírus na máquina de origem

Verifique se nenhum software antivírus na máquina replicada está bloqueando a recuperação do site. Se você precisar excluir o Site Recovery de programas antivírus, revise [este artigo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Passo 6: Verifique a conectividade da máquina de origem


1. Instale o [cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) na máquina de origem, se necessário. Não use ping.
2. A partir da máquina de origem, pingo do servidor de processo na porta HTTPS com Telnet. Por padrão, 9443 é a porta HTTPS para tráfego de replicação.

    `telnet <process server IP address> <port>`

3. Verifique se a conexão é bem sucedida.


**Conectividade** | **Detalhes** | **Ação**
--- | --- | ---
**Bem sucedido** | A telnet mostra uma tela em branco, e o servidor de processo é acessível. | Nenhuma ação adicional é necessária.
**Malsucedido** | Você não pode se conectar | Certifique-se de que a porta de entrada 9443 é permitida no servidor de processo. Por exemplo, se você tiver uma rede de perímetro ou uma sub-rede rastreada. Verifique a conectividade novamente.
**Parcialmente bem sucedido** | Você pode se conectar, mas a máquina de origem informa que o servidor de processo não pode ser alcançado. | Continue com o próximo procedimento de solução de problemas.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Passo 7: Solucionar problemas de um servidor de processo inalcançável

Se o servidor de processo não for acessível a partir da máquina de origem, o erro 78186 será exibido. Se não for resolvido, esse problema levará a que os pontos de recuperação consistentes com aplicativos e consistentes com falhas não sejam gerados como esperado.

Solucionar problemas verificando se a máquina de origem pode alcançar o endereço IP do servidor de processo e executar a ferramenta cxpsclient na máquina de origem, para verificar a conexão de ponta a ponta.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verifique a conexão IP no servidor de processo

Se a telnet for bem sucedida, mas a máquina de origem informar que o servidor de processo não pode ser alcançado, verifique se você pode acessar o endereço IP do servidor de processo.

1. Em um navegador da Web, tente acessar o endereço IP https://<PS_IP>:<PS_Data_Port>/.
2. Se esta verificação mostrar um erro de certificado HTTPS, isso é normal. Se você ignorar o erro, você deve ver um 400 - Solicitação Ruim. Isso significa que o servidor não pode atender à solicitação do navegador, e que a conexão HTTPS padrão está bem.
3. Se essa verificação não funcionar, observe a mensagem de erro do navegador. Por exemplo, um erro do 407 indicará um problema com autenticação por proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Verifique a conexão com cxpsclient

Além disso, você pode executar a ferramenta cxpsclient para verificar a conexão de ponta a ponta.

1. Execute a ferramenta da seguinte maneira:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. No servidor de processo, verifique os logs gerados nessas pastas:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verifique os registros de VM de origem para falhas de upload (erro 78028)

O problema com uploads de dados bloqueados das máquinas de origem para o serviço de processo pode resultar em pontos de recuperação consistentes com falhas e consistentes com aplicativos não sendo gerados. 

1. Para solucionar problemas de upload de rede, você pode procurar erros neste registro:

    C:\Arquivos de programa (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. O uso do resto dos procedimentos neste artigo pode ajudá-lo a resolver problemas de upload de dados.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Passo 8: Verifique se o servidor de processo está empurrando dados

Verifique se o servidor de processo está enviando dados por push ativamente para o Azure.

  1. No servidor de processo, abra o Gerenciador de Tarefas (pressione Ctrl+Shift+Esc).
  2. Selecione a guia **Desempenho** > **Monitor de recursos abertos**.
  3. Na página **Monitor de recursos,** selecione a guia **Rede.** Em **Processos com Atividade de Rede,** verifique se o cbengine.exe está enviando ativamente um grande volume de dados.

       ![Volumes em processos com atividade de rede](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe não estiver enviando um grande volume de dados, conclua as etapas nas seções a seguir.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Passo 9: Verifique a conexão do servidor de processo ao armazenamento blob do Azure

1. No Resource Monitor, selecione **cbengine.exe**.
2. Em **Conexões TCP,** verifique se há conectividade do servidor de processo para o armazenamento Do Zure.

  ![Conectividade entre cbengine.exe e a URL de armazenamento Azure Blob](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Verificar serviços

Se não houver conectividade do servidor de processo com a URL de armazenamento blob do Azure, verifique se os serviços estão sendo executados.

1. No Painel de Controle, selecione **Serviços**.
2. Verifique se os seguintes serviços estão sendo executados:

    - cxprocessserver
    - Agente do InMage Scout VX – Sentinel/Outpost
    - Agente de Serviços dos Serviços de Recuperação do Microsoft Azure
    - Serviço do Microsoft Azure Site Recovery
    - tmansvc

3. Iniciar ou reiniciar qualquer serviço que não está em execução.
4. Verifique se o servidor de processo está conectado e acessível. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Passo 10: verifique a conexão do servidor de processo com o endereço IP público do Azure

1. No servidor de processos, em **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, abra o arquivo CBEngineCurr.errlog mais recente.
2. No arquivo, procure por **443**, ou para a tentativa de conexão de seqüência **falhou**.

  ![Logins de erro na pasta Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se você ver problemas, localize seu endereço IP público do Azure no arquivo CBEngineCurr.currLog usando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na linha de comando do servidor de processo, use a Telnet para pingar seu endereço IP público do Azure.
6. Se você não puder se conectar, siga o próximo procedimento.

## <a name="step-11-check-process-server-firewall-settings"></a>Passo 11: Verifique as configurações do firewall do servidor de processo. 

Verifique se o firewall baseado em endereço IP no servidor de processo está bloqueando o acesso.

1. Para regras de firewall baseadas em endereçoIP:

    a) Baixe a lista completa das faixas IP do [data center do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Adicionar os intervalos de endereçoIP à configuração de firewall, para garantir que o firewall permita a comunicação ao Azure (e à porta HTTPS padrão, 443).

    c) Permitir faixas de endereçoIP para a região Azure de sua assinatura e para a região do Azure West US (usada para controle de acesso e gerenciamento de identidade).

2. Para firewalls baseados em URL, adicione as URLs listadas na tabela a seguir à configuração de firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Etapa 12: Verifique as configurações de proxy do servidor de processo 

1. Se você estiver usando um servidor proxy, verifique se o nome do servidor proxy é resolvido pelo servidor DNS. Verifique o valor que você forneceu ao configurar o servidor de configuração na tecla de registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Certifique-se de que as mesmas configurações sejam usadas pelo agente de recuperação do site do Azure para enviar dados.

    a) Procure o **Backup do Microsoft Azure**.

    b) Abra o **backup do Microsoft Azure**e selecione propriedades**de alteração de** **ação** > .

    c) Na guia **Configuração proxy,** o endereço proxy deve ser o mesmo que o endereço proxy mostrado nas configurações do registro. Caso contrário, altere-a para o mesmo endereço.

## <a name="step-13-check-bandwidth"></a>Passo 13: Verifique a largura de banda

Aumente a largura de banda entre o servidor de processo e o Azure e, em seguida, verifique se o problema ainda ocorre.


## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
