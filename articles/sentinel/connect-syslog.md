---
title: Conecte os dados do Syslog ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Syslog ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588069"
---
# <a name="connect-your-external-solution-using-syslog"></a>Conecte sua solução externa usando o Syslog

Você pode conectar qualquer aparelho no local que suporte o Syslog ao Azure Sentinel. Isso é feito usando um agente baseado em uma máquina Linux entre o aparelho e o Azure Sentinel. Se a sua máquina Linux estiver no Azure, você pode transmitir os logs do seu aparelho ou aplicativo para um espaço de trabalho dedicado que você cria no Azure e conectá-lo. Se a sua máquina Linux não estiver no Azure, você pode transmitir os logs do seu aparelho para um VM ou máquina dedicado no local em que você instala o Agent for Linux. 

> [!NOTE]
> Se o aparelho suportar o Syslog CEF, a conexão está mais completa e você deve escolher esta opção e seguir as instruções em [Conectar dados da CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Como ele funciona

O Syslog é um protocolo de registro de eventos em log que é comum para o Linux. Os aplicativos enviarão mensagens que podem ser armazenadas no computador local ou entregues a um coletor de Syslog. Quando o agente do Log Analytics para Linux é instalado, ele configura o daemon do Syslog local para encaminhar mensagens para o agente. O agente envia a mensagem ao Azure Monitor, onde um registro correspondente é criado.

Para obter mais informações, consulte [as fontes de dados do Syslog no Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - O agente pode coletar registros de várias fontes, mas deve ser instalado em uma máquina proxy dedicada.
> - Se você quiser fazer suporte a conectores para CEF e Syslog na mesma VM, execute as seguintes etapas para evitar a duplicação de dados:
>    1. Siga as instruções para [conectar seu CEF](connect-common-event-format.md).
>    2. Para conectar os dados do Syslog, vá para **Configurações configurações** > **de espaço de trabalho Configurações avançadas** > **Advanced settings** > **Data** > **Syslog** e defina as Instalações e suas prioridades para que elas não sejam as mesmas instalações e propriedades que você usou na configuração do CEF. <br></br>Se você selecionar **Aplicar abaixo da configuração às minhas máquinas,** ele aplicará esses conjuntos a todas as VMs conectadas a este espaço de trabalho.


## <a name="connect-your-syslog-appliance"></a>Conecte seu aparelho Syslog

1. No Azure Sentinel, selecione **conectores de dados** e selecione o conector **Syslog.**

2. Na lâmina **Syslog,** selecione **Abrir a página do conector**.

3. Instale o agente Linux:
    
    - Se a sua máquina virtual Linux estiver no Azure, selecione **Baixar e instalar o agente na máquina virtual Do Azure Linux**. Na **lâmina das máquinas virtuais,** selecione as máquinas virtuais para instalar o agente e clique em **Conectar**.
    - Se a sua máquina Linux não estiver no Azure, selecione **Baixar e instalar o agente na máquina Linux não-Azure**. Na lâmina **do agente Direto,** copie o comando para **DOWNLOAD E AGENTE DE BORDO PARA LINUX** e execute-o no seu computador. 
    
   > [!NOTE]
   > Certifique-se de configurar as configurações de segurança desses computadores de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar as configurações de rede para se alinhar com a política de segurança de rede da sua organização e alterar as portas e protocolos no daemon para se alinhar aos requisitos de segurança.

4. Selecione **Abrir a configuração de configurações avançadas do espaço de trabalho**.

5. Na **lâmina de configurações Avançadas,** selecione **Data** > **Syslog**. Em seguida, adicione as instalações para o conector coletar.
    
    Adicione as facilidades que o seu aparelho syslog inclui em seus cabeçalhos de registro. Você pode ver essa configuração no seu aparelho Syslog `/etc/rsyslog.d/security-config-omsagent.conf` no **Syslog-d** na `/etc/syslog-ng/security-config-omsagent.conf`pasta e no **r-Syslog** de .
    
    Se você quiser usar detecção anômala de login SSH com os dados coletados, adicione **auth** e **authpriv**. Consulte a [seção](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) a seguir para obter mais detalhes.

6. Quando você tiver adicionado todas as facilidades que deseja monitorar e ajustado todas as opções de gravidade para cada uma, selecione a caixa de seleção **Aplicar abaixo da configuração para minhas máquinas**.

7. Selecione **Salvar**. 

8. No seu aparelho syslog, certifique-se de que você está enviando as instalações que você especificou.

9. Para usar o esquema relevante no Azure Monitor para os logs de syslog, procure por **Syslog**.

10. Você pode usar a função Kusto descrita em [Usar funções no log do Azure Monitor para](../azure-monitor/log-query/functions.md) analisar suas mensagens syslog. Em seguida, você pode salvá-los como uma nova função Log Analytics para usar como um novo tipo de dados.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configure o conector Syslog para detecção anômala de login SSH

> [!IMPORTANT]
> A detecção de login SSH anômala está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel pode aplicar o aprendizado de máquina (ML) aos dados do syslog para identificar atividades anômalas de login Secure Shell (SSH). Os cenários incluem:

- Viagem impossível – quando dois eventos de login bem-sucedidos ocorrem a partir de dois locais que são impossíveis de alcançar dentro do prazo dos dois eventos de login.
- Localização inesperada – o local de onde ocorreu um evento de login bem-sucedido é suspeito. Por exemplo, a localização não foi vista recentemente.
 
Essa detecção requer uma configuração específica do conector de dados Syslog: 

1. Para a etapa 5 do procedimento anterior, certifique-se de que tanto **auth** quanto **authpriv** sejam selecionados como instalações para monitorar. Mantenha as configurações padrão para as opções de gravidade, para que todas sejam selecionadas. Por exemplo: 
    
    > [!div class="mx-imgBorder"]
    > ![Instalações necessárias para detecção anômala de login SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Permita tempo suficiente para que as informações do syslog sejam coletadas. Em seguida, navegue **até o Azure Sentinel - Logs**e copie e cole a seguinte consulta:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Altere o **intervalo de tempo,** se necessário, e **selecione Executar**.
    
    Se a contagem resultante for zero, confirme a configuração do conector e se os computadores monitorados tiverem uma atividade de login bem sucedida durante o período de tempo especificado para a consulta.
    
    Se a contagem resultante for maior que zero, seus dados de syslog são adequados para detecção anômala de login SSH. Você habilita essa detecção a partir de **modelos** >  de**regra de** > análise **(Visualização) detecção de login ssh anômalo**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar os aparelhos syslog no local ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

