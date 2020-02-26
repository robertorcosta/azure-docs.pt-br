---
title: Detecção avançada de ataques de multiestágio no Azure Sentinel
description: Use a tecnologia Fusion no Azure Sentinel para reduzir o fadiga de alertas e criar incidentes acionáveis que se baseiam na detecção avançada de ataques de multiestágio.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587916"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Detecção avançada de ataques de multiestágio no Azure Sentinel


> [!IMPORTANT]
> Alguns recursos do Fusion no Azure Sentinel estão atualmente em visualização pública.
> Esses recursos são fornecidos sem um contrato de nível de serviço e não são recomendados para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Usando a tecnologia de fusão baseada no aprendizado de máquina, o Azure Sentinel pode detectar automaticamente ataques de várias etapas combinando comportamentos anormais e atividades suspeitas que são observadas em vários estágios da cadeia de eliminação. Em seguida, o Azure Sentinel gera incidentes que, de outra forma, seriam muito difíceis de detectar. Esses incidentes encerrarãom dois ou mais alertas ou atividades. Por design, esses incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizado para seu ambiente, essa detecção não apenas reduz as taxas de falsos positivos, mas também pode detectar ataques com informações limitadas ou ausentes.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração para detecção avançada de ataque de multiestágio

Essa detecção é habilitada por padrão no Azure Sentinel. Para verificar o status ou para desabilitá-lo, talvez porque você esteja usando uma solução alternativa para criar incidentes com base em vários alertas, use as seguintes instruções:

1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com).

2. Navegue até o **Azure Sentinel** > **configuração** > **Analytics**

3. Selecione **regras ativas** e localize **detecção avançada de ataque** de várias etapas na coluna **nome** . Verifique a coluna **status** para confirmar se essa detecção está habilitada ou desabilitada.

4. Para alterar o status, selecione essa entrada e, na folha **detecção avançada de ataques de multiestágio** , selecione **Editar**.

5. Na folha **Assistente para criação de regras** , a alteração de status é selecionada automaticamente para você, portanto, selecione **Avançar: revisar**e **salvar**. 

Os modelos de regra não são aplicáveis para a detecção avançada de ataques de multiestágio.

> [!NOTE]
> Atualmente, o Azure Sentinel usa 30 dias de dados históricos para treinar os sistemas de aprendizado de máquina. Esses dados são sempre criptografados usando as chaves da Microsoft à medida que passam pelo pipeline do Machine Learning. No entanto, os dados de treinamento não serão criptografados usando [CMK (chaves gerenciadas pelo cliente)](customer-managed-keys.md) se você tiver habilitado o CMK em seu espaço de trabalho do Azure Sentinel. Para recusar a fusão, navegue até **Azure Sentinel** \> **Configuration** \> **Analytics \> regras ativas \> detecção avançada de ataques** de várias etapas e na coluna **status** , selecione **desabilitar.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion usando o Palo Alto Networks e o Microsoft defender ATP

Esses cenários combinam dois dos logs fundamentais usados por analistas de segurança: logs de firewall de redes Palo Alto e logs de detecção de ponto de extremidade do Microsoft defender ATP. Em todos os cenários listados abaixo, uma atividade suspeita é detectada no ponto de extremidade que envolve um endereço IP externo e, em seguida, isso é seguido pelo tráfego anormal do endereço IP externo de volta para o firewall. Nos logs do Palo Alto, o Azure Sentinel se concentra nos [logs de ameaças](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando ameaças são permitidas (dados suspeitos, arquivos, inundações, pacotes, exames, spyware, URLs, vírus, vulnerabilidades, incêndio-vírus, Wildfires).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Solicitação de rede para TOR o serviço de anonimato seguido pelo tráfego anormal sinalizado pelo firewall do Palo Alto Networks.

Nesse cenário, o Azure Sentinel primeiro detecta um alerta que a proteção avançada contra ameaças do Microsoft defender detectou uma solicitação de rede para um serviço de anonimato TOR que leva à atividade anômala. Isso foi iniciado na conta {nome da conta} com a ID SID {Sid} às {time}. O endereço IP de saída para a conexão era {IndividualIp}.
Em seguida, uma atividade incomum foi detectada pelo firewall do Palo Alto Networks em {TimeGenerated}. Isso indica que o tráfego mal-intencionado entrou na sua rede o endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário está atualmente em visualização pública.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>O PowerShell fez uma conexão de rede suspeita seguida pelo tráfego anormal sinalizado pelo firewall do Palo Alto Networks.

Nesse cenário, o Azure Sentinel primeiro detecta um alerta de que a proteção avançada contra ameaças do Microsoft defender detectou que o PowerShell fez uma conexão de rede suspeita que leva à atividade anômala que foi detectada por um firewall de rede Palo Alto. Isso foi iniciado pela conta {nome da conta} com a ID SID {Sid} às {time}. O endereço IP de saída para a conexão era {IndividualIp}. Em seguida, uma atividade incomum foi detectada pelo firewall do Palo Alto Networks em {TimeGenerated}. Isso indica que o tráfego mal-intencionado entrou na rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário está atualmente em visualização pública.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Conexão de saída com o IP com um histórico de tentativas de acesso não autorizado seguido pelo tráfego anormal sinalizado pelo firewall do Palo Alto Networks

Nesse cenário, o Azure Sentinel detecta um alerta de que a proteção avançada contra ameaças do Microsoft defender detectou uma conexão de saída para um endereço IP com um histórico de tentativas de acesso não autorizadas que levam à atividade anômala sendo detectada pelo Palo Alto Firewall de redes. Isso foi iniciado pela conta {nome da conta} com a ID SID {Sid} às {time}. O endereço IP de saída para a conexão era {IndividualIp}. Depois disso, uma atividade incomum foi detectada pelo firewall do Palo Alto Networks em {TimeGenerated}. Isso indica que o tráfego mal-intencionado entrou na rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário está atualmente em visualização pública.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusion usando a proteção de identidade e Microsoft Cloud App Security

Usando a detecção avançada de ataques de multiestágio, o Azure Sentinel dá suporte aos seguintes cenários que combinam eventos de anomalias de Azure Active Directory Identity Protection e Microsoft Cloud App Security:

- [Viagem impossível para o local atípicos seguido pela atividade anômala do Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Atividade de entrada para um local não familiar seguido pela atividade anômala do Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Atividade de entrada do dispositivo infectado seguido pela atividade anormal do Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Atividade de entrada do endereço IP anônimo seguido pela atividade anormal do Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Atividade de entrada do usuário com credenciais vazadas, seguidas pela atividade anormal do Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Você deve ter o [conector de dados Azure ad Identity Protection](connect-azure-ad-identity-protection.md) e os conectores de [Cloud app Security](connect-cloud-app-security.md) configurados.

Nas descrições a seguir, o Azure Sentinel exibirá o valor real de seus dados representados nesta página como variáveis entre colchetes. Por exemplo, o nome de exibição real de uma conta em vez de \<*nome da conta*> e o número real em vez de \<*número*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Viagem impossível para o local atípicos seguido pela atividade anômala do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a viagem impossível a alertas de local atípicos de alertas Azure AD Identity Protection e anormais do Office 365 gerados pelo Microsoft Cloud App Security:

- **Viagem impossível a locais atípicoss que levam ao Office 365 caixa de correio vazamento**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de uma viagem impossível para \<> *local*, um local atípicos, seguido por uma regra de encaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome da *conta*de \<de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todo o email de entrada para o endereço externo \<> de *endereço de email*.

- **Viagem impossível a locais atípicoss que levam à atividade administrativa suspeita do aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de uma viagem impossível para \<> *local*, um local atípicos.
    
    Em seguida, a conta \<*nome da conta*> executada sobre \<*número*> atividades administrativas em uma única sessão.

- **Viagem impossível a locais atípicoss que levam à exclusão em massa de arquivos**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> para \<> *local*, um local atípicos. 
    
    Em seguida, a conta \<*nome da conta*> excluído \<*número*> arquivos exclusivos em uma única sessão.

- **Viagem impossível a locais atípicoss que levam ao download de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de uma viagem impossível para \<> *local*, um local atípicos. 
    
    Em seguida, a conta \<*nome da conta*> baixado \<*número de*> arquivos exclusivos em uma única sessão.

- **Viagem impossível a locais atípicoss que levam à representação do Office 365**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de uma viagem impossível para \<> *local*, um local atípicos. 
    
    Em seguida, a conta \<*nome da conta*> realizou uma quantidade incomum (\<*número de atividades*>) de atividades de representação em uma única sessão.

- **Viagem impossível a locais atípicoss que levam ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de uma viagem impossível para \<> *local*, um local atípicos. 
    
    Em seguida, a conta \<*nome da conta*> compartilhado \<*número de*> arquivos exclusivos em uma única sessão.

- **Viagem impossível a locais atípicoss que levam ao ransomware no aplicativo em nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de uma viagem impossível para \<> *local*, um local atípicos. 
    
    Em seguida, a conta \<*nome da conta*> carregado \<*número de*arquivos > e excluiu um total de \<*número de*arquivos >. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Atividade de entrada para um local não familiar seguido pela atividade anômala do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a atividade de entrada para alertas de local desconhecidos de Azure AD Identity Protection e alertas anormais do Office 365 gerados pelo Microsoft Cloud App Security.

- **Evento de entrada de um local desconhecido que leva à caixa de correio do Exchange Online vazamento**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de \<*local*>, um local desconhecido, seguido por uma regra de encaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome da *conta*de \<de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todo o email de entrada para o endereço externo \<> de *endereço de email*. 

- **Evento de entrada de um local desconhecido que leva à atividade administrativa do aplicativo de nuvem suspeito**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de \<*local*>, um local desconhecido. 
    
    Em seguida, a conta \<*nome da conta*> executada sobre \<*número de*> atividades administrativas em uma única sessão.

- **Evento de entrada de um local não familiar que leva à exclusão de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de \<*local*>, um local desconhecido. 
    
    Em seguida, a conta \<*nome da conta*> excluído \<*número*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um local desconhecido que leva ao download de arquivo em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de \<*local*>, um local desconhecido. 
    
    Em seguida, a conta \<*nome da conta*> baixado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um local desconhecido que leva à representação do Office 365**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de \<*local*>, um local desconhecido.
    
    Em seguida, a conta \<*nome da conta*> representada sobre \<*número de*> contas diferentes em uma única sessão.

- **Evento de entrada de um local desconhecido que leva ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de \<*local*>, um local desconhecido. 
    
    Em seguida, a conta \<*nome da conta*> compartilhado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um local desconhecido que leva ao ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de \<*local*>, um local desconhecido. 
    
    Em seguida, a conta \<*nome da conta*> carregado \<*número de*arquivos > e excluiu um total de \<*número de*arquivos >. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Atividade de entrada do dispositivo infectado seguido pela atividade anormal do Office 365

Há sete possíveis incidentes do Azure sentinela que combinam a atividade de entrada de alertas de dispositivo infectados dos alertas Azure AD Identity Protection e anormal Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de entrada de um dispositivo infectado que leva à caixa de correio do Office 365 vazamento**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um dispositivo potencialmente infectado com malware, seguido por uma regra de encaminhamento de caixa de entrada suspeita definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome da *conta*de \<de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todo o email de entrada para o endereço externo \<> de *endereço de email*. 

- **Evento de entrada de um dispositivo infectado que leva à atividade administrativa suspeita do aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um dispositivo potencialmente infectado com malware.
    
    Em seguida, a conta \<*nome da conta*> executada sobre \<*número de*> atividades administrativas em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva à exclusão em massa de arquivos**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, a conta \<*nome da conta*> excluído \<*número*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva ao download de arquivo em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, a conta \<*nome da conta*> baixado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva à representação do Office 365**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, a conta \<*nome da conta*> representada sobre \<*número de*> contas diferentes em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, a conta \<*nome da conta*> compartilhado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva ao ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, a conta \<*nome da conta*> carregado \<*número de*arquivos > e excluiu um total de \<*número de*arquivos >. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Atividade de entrada do endereço IP anônimo seguido pela atividade anormal do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a atividade de entrada de alertas de endereço IP anônimo de Azure AD Identity Protection e de alertas anormais do Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de entrada de um endereço IP anônimo que leva à caixa de correio do Office 365 vazamento**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um endereço IP de proxy anônimo \<*endereço IP*>, seguido por uma regra de encaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome da *conta*de \<de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todo o email de entrada para o endereço externo \<> de *endereço de email*. 

- **Evento de entrada de um endereço IP anônimo que leva à atividade administrativa suspeita do aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um endereço IP de proxy anônimo \<*endereço IP*>. 
    
    Em seguida, a conta \<*nome da conta*> executada sobre \<*número de*> atividades administrativas em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva à exclusão de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um endereço IP de proxy anônimo \<*endereço IP*>. 
    
    Em seguida, a conta \<*nome da conta*> excluído \<*número*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva ao download de arquivo em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um endereço IP de proxy anônimo \<*endereço IP*>. 
    
    Em seguida, a conta \<*nome da conta*> baixado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva à representação do Office 365**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um endereço IP de proxy anônimo \<*endereço IP*>. 
    
    Em seguida, a conta \<*nome da conta*> representada sobre \<*número de*> contas diferentes em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um endereço IP de proxy anônimo \<*endereço IP*>. 
    
    Em seguida, a conta \<*nome da conta*> compartilhado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um endereço IP anônimo para ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \<*nome da conta*> de um endereço IP de proxy anônimo \<*endereço IP*>. 
    
    Em seguida, a conta \<*nome da conta*> carregado \<*número de*arquivos > e excluiu um total de \<*número de*arquivos >. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Atividade de entrada do usuário com credenciais vazadas, seguidas pela atividade anormal do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a atividade de entrada do usuário com alertas de credenciais vazadas de alertas Azure AD Identity Protection e anormais do Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de entrada do usuário com credenciais vazadas que levam à caixa de correio do Office 365 vazamento**
    
    Esse alerta é uma indicação de que o evento de entrada por \<*nome da conta*> usou credenciais vazadas, seguido por uma regra de encaminhamento de caixa de entrada suspeita definida na caixa de entrada de um usuário. 
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome da *conta*de \<de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todo o email de entrada para o endereço externo \<> de *endereço de email*. 

- **Evento de entrada do usuário com credenciais vazadas que levam à atividade administrativa do aplicativo de nuvem suspeito**
    
    Esse alerta é uma indicação de que o evento de entrada por \<*nome da conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<*nome da conta*> executada sobre \<*número de*> atividades administrativas em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas levando à exclusão de arquivos em massa**
    
    Esse alerta é uma indicação de que o evento de entrada por \<*nome da conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<*nome da conta*> excluído \<*número*> arquivos exclusivos em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas levando ao download de arquivo em massa**
    
    Esse alerta é uma indicação de que o evento de entrada por \<*nome da conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<*nome da conta*> baixado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas que levam à representação do Office 365**
    
    Esse alerta é uma indicação de que o evento de entrada por \<*nome da conta*> usou credenciais vazadas. 
    
    Em seguida, a conta \<*nome da conta*> representada sobre \<*número de*> contas diferentes em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas que levam ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de que o evento de entrada por \<*nome da conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<*nome da conta*> compartilhado \<*número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas para ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de que o evento de entrada por \<*nome da conta*> usou credenciais vazadas. 
    
    Em seguida, a conta \<*nome da conta*> carregado \<*número de*arquivos > e excluiu um total de \<*número de*arquivos >. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu mais sobre a detecção avançada de ataques de multiestágio, talvez esteja interessado no início rápido a seguir para saber como obter visibilidade de seus dados e ameaças potenciais: comece a usar o [Azure Sentinel](quickstart-get-visibility.md).

Se você estiver pronto para investigar os incidentes que são criados para você, consulte o seguinte tutorial: [investigar incidentes com o Azure Sentinel](tutorial-investigate-cases.md).

