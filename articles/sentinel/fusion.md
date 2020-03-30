---
title: Detecção avançada de ataque multiestágio no Azure Sentinel
description: Use a tecnologia Fusion no Azure Sentinel para reduzir a fadiga de alerta e criar incidentes acionáveis baseados na detecção avançada de ataques multiestágios.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587916"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Detecção avançada de ataque multiestágio no Azure Sentinel


> [!IMPORTANT]
> Alguns recursos do Fusion no Azure Sentinel estão atualmente em pré-visualização pública.
> Esses recursos são fornecidos sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Usando a tecnologia Fusion baseada no aprendizado de máquina, o Azure Sentinel pode detectar automaticamente ataques multiestágio, combinando comportamentos anômalos e atividades suspeitas que são observadas em vários estágios da cadeia de mortes. O Azure Sentinel então gera incidentes que de outra forma seriam muito difíceis de pegar. Esses incidentes envolvem dois ou mais alertas ou atividades. Pelo projeto, esses incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizada para o seu ambiente, essa detecção não só reduz as taxas falsas positivas, mas também pode detectar ataques com informações limitadas ou ausentes.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração de detecção avançada de ataque multiestágio

Essa detecção é ativada por padrão no Azure Sentinel. Para verificar o status ou desabilitá-lo talvez porque você está usando uma solução alternativa para criar incidentes com base em vários alertas, use as seguintes instruções:

1. Se você ainda não fez isso, faça login no [portal Azure](https://portal.azure.com).

2. Navegue até **o Azure Sentinel** > **Configuration** > **Analytics**

3. Selecione **Regras ativas** e localize **detecção avançada de ataque multiestágio** na coluna **NAME.** Verifique a coluna **STATUS** para confirmar se essa detecção está ativada ou desativada.

4. Para alterar o status, selecione esta entrada e na lâmina **Detecção de ataque avançada multiestágio,** selecione **Editar**.

5. Na lâmina **assistente de criação de regras,** a mudança de status é automaticamente selecionada para você, então selecione **Next: Review**e, em seguida, **Salve**. 

Os modelos de regras não são aplicáveis para a detecção avançada de ataque em vários estágios.

> [!NOTE]
> Atualmente, o Azure Sentinel usa 30 dias de dados históricos para treinar os sistemas de aprendizagem de máquina. Esses dados são sempre criptografados usando as chaves da Microsoft enquanto passam pelo pipeline de aprendizado de máquina. No entanto, os dados de treinamento não são criptografados usando [cmk (Customer Managed Keys, chaves gerenciadas pelo cliente)](customer-managed-keys.md) se você habilitou o CMK no seu espaço de trabalho do Azure Sentinel. Para desativar o Fusion, navegue até **o Azure Sentinel** \> **Configuration** \> **Analytics \> Active rules \> Advanced Multistage Attack Detection** e na coluna **Status,selecione** **Desativar.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion usando Palo Alto Networks e Microsoft Defender ATP

Esses cenários combinam dois dos registros fundamentais usados pelos analistas de segurança: logs de firewall da Palo Alto Networks e registros de detecção de ponto final do Microsoft Defender ATP. Em todos os cenários listados abaixo, uma atividade suspeita é detectada no ponto final que envolve um endereço IP externo, então, isso é seguido por tráfego anômalo do endereço IP externo de volta para o firewall. Nos registros de Palo Alto, o Azure Sentinel se concentra em [registros de ameaças,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando ameaças são permitidas (dados suspeitos, arquivos, inundações, pacotes, varreduras, spyware, URLs, vírus, vulnerabilidades, vírus de incêndio, incêndios florestais).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Solicitação de rede ao serviço de anonimização tor seguido de tráfego anômalo sinalizado pelo firewall Palo Alto Networks.

Neste cenário, o Azure Sentinel detecta primeiro um alerta de que o Microsoft Defender Advanced Threat Protection detectou uma solicitação de rede a um serviço de anonimização tor que leva a atividades anômalas. Isso foi iniciado a conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a conexão foi {IndividualIp}.
Em seguida, a atividade incomum foi detectada pelo Palo Alto Networks Firewall em {TimeGenerated}. Isso indica tráfego malicioso inserido em sua rede O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário está atualmente em pré-visualização pública.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A PowerShell fez uma conexão de rede suspeita seguida de tráfego anômalo sinalizado pelo firewall da Palo Alto Networks.

Neste cenário, o Azure Sentinel detecta pela primeira vez um alerta de que o Microsoft Defender Advanced Threat Protection detectou que o PowerShell fez uma conexão de rede suspeita levando a atividades anômalas que foram detectadas por um Firewall de Rede palo alto. Isso foi iniciado pela conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a conexão foi {IndividualIp}. Em seguida, a atividade incomum foi detectada pelo Palo Alto Networks Firewall em {TimeGenerated}. Isso indica que o tráfego malicioso entrou em sua rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário está atualmente em pré-visualização pública.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Conexão de saída ao IP com um histórico de tentativas de acesso não autorizadas seguidas de tráfego anômalo sinalizado pelo firewall palo alto networks

Neste cenário, o Azure Sentinel detecta um alerta de que o Microsoft Defender Advanced Threat Protection detectou uma conexão de saída a um endereço IP com um histórico de tentativas de acesso não autorizadas que levam à detecção de atividades anômalas pelo Palo Alto Firewall de redes. Isso foi iniciado pela conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a conexão foi {IndividualIp}. Depois disso, a atividade incomum foi detectada pelo Palo Alto Networks Firewall em {TimeGenerated}. Isso indica que o tráfego malicioso entrou em sua rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário está atualmente em pré-visualização pública.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusion usando proteção de identidade e segurança de aplicativos na nuvem da Microsoft

Usando detecção avançada de ataque em vários estágios, o Azure Sentinel suporta os seguintes cenários que combinam eventos de anomalias do Azure Active Directory Identity Protection e microsoft Cloud App Security:

- [Viagem impossível para local atípico seguido de atividade anômala Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Atividade de login para localização desconhecida seguida de atividade anômala do Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Atividade de login do dispositivo infectado seguido pela atividade anômala do Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Atividade de login a partir de endereço IP anônimo seguido de atividade anômala do Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Atividade de login de usuário com credenciais vazadas seguida sustais atividade do Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Você deve ter o [conector de dados Azure AD Identity Protection](connect-azure-ad-identity-protection.md) e os conectores [de segurança do aplicativo em nuvem](connect-cloud-app-security.md) configurados.

Nas descrições a seguir, o Azure Sentinel exibirá o valor real de seus dados representados nesta página como variáveis entre parênteses. Por exemplo, o nome de exibição real de uma conta em vez do \< *nome* da conta>, e o número real em vez de \< *número*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Viagem impossível para local atípico seguido de atividade anômala Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam viagens impossíveis a alertas de localização atípicos do Azure AD Identity Protection e alertas anômalos do Office 365 gerados pelo Microsoft Cloud App Security:

- **Viagem impossível para locais atípicos que levam à exfiltração da caixa de correio office 365**
    
    Este alerta é uma indicação de \<um evento de login \<por nome de *conta*> de uma viagem impossível para *a localização*>, um local atípico, seguido por uma regra suspeita de encaminhamento de caixa de entrada foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O \< *nome* da conta de usuário> criou ou atualizou uma regra de \<encaminhamento de caixa de entrada que encaminha todos os e-mails de entrada para o endereço de endereço externo *>.*

- **Viagens impossíveis para locais atípicos levando a atividadeadministrativa suspeita de aplicativos em nuvem**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de uma viagem impossível para \< *a localização*>, um local atípico.
    
    Em seguida, \<o nome \<da *conta*> realizado sobre *o número*> atividades administrativas em uma única sessão.

- **Viagem impossível para locais atípicos que levam à exclusão de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> para a *localização*>, um local atípico. 
    
    Em seguida, \<o nome \<da *conta*> número excluído *de* arquivos> únicos em uma única sessão.

- **Viagem impossível para locais atípicos que levam ao download de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de uma viagem impossível para \< *a localização*>, um local atípico. 
    
    Em seguida, \<o nome da \< *conta*> baixado sobre o *número de* arquivos> únicos em uma única sessão.

- **Viagem impossível para locais atípicos que levam à representação do Office 365**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de uma viagem impossível para \< *a localização*>, um local atípico. 
    
    Em seguida, o \< *nome* da conta\<> realizou uma quantidade incomum (*número de atividades*>) de atividades de personificação em uma única sessão.

- **Viagem impossível para locais atípicos que levam ao compartilhamento de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de uma viagem impossível para \< *a localização*>, um local atípico. 
    
    Em seguida, \<o nome da \< *conta*> compartilhado sobre o *número de* arquivos> únicos em uma única sessão.

- **Viagem impossível para locais atípicos levando a ransomware em aplicativo na nuvem**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de uma viagem impossível para \< *a localização*>, um local atípico. 
    
    Em seguida, \<o nome \<da *conta*> o número carregado \< *de* arquivos> e excluiu um total de *>* arquivos. 
    
    Esse padrão de atividade é indicativo de um potencial ataque de ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Atividade de login para localização desconhecida seguida de atividade anômala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login para alertas de localização desconhecidos do Azure AD Identity Protection e alertas anômalos do Office 365 gerados pelo Microsoft Cloud App Security.

- **Evento de login de um local desconhecido que leva à exfiltração da caixa de correio Exchange Online**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> do *local*>, um local desconhecido, seguido por uma regra suspeita de encaminhamento de caixa de entrada foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O \< *nome* da conta de usuário> criou ou atualizou uma regra de \<encaminhamento de caixa de entrada que encaminha todos os e-mails de entrada para o endereço de endereço externo *>.* 

- **Evento de login de um local desconhecido levando a atividade administrativa suspeita de aplicativo em nuvem**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> do *local*>, um local desconhecido. 
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de* atividades administrativas> em uma única sessão.

- **Evento de login de um local desconhecido que leva à exclusão de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> do *local*>, um local desconhecido. 
    
    Em seguida, \<o nome \<da *conta*> número excluído *de* arquivos> únicos em uma única sessão.

- **Evento de login de um local desconhecido que leva ao download de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> do *local*>, um local desconhecido. 
    
    Em seguida, \<o nome da \< *conta*> baixado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login de um local desconhecido que leva à personificação do Office 365**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> do *local*>, um local desconhecido.
    
    Em seguida, \<o nome da \< *conta*> se passou por número *de*> diferentes contas em uma única sessão.

- **Evento de login de um local desconhecido que leva ao compartilhamento de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> do *local*>, um local desconhecido. 
    
    Em seguida, \<o nome da \< *conta*> compartilhado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login de um local desconhecido que leva ao ransomware em aplicativo na nuvem**
    
    Este alerta é uma indicação de \<um evento \<de login por nome de *conta*> do *local*>, um local desconhecido. 
    
    Em seguida, \<o nome \<da *conta*> o número carregado \< *de* arquivos> e excluiu um total de *>* arquivos. 
    
    Esse padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Atividade de login do dispositivo infectado seguido pela atividade anômala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam a atividade de login a partir de alertas de dispositivos infectados do Azure AD Identity Protection e alertas anômalos do Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de login de um dispositivo infectado que leva à exfiltração da caixa de correio office 365**
    
    Este alerta é uma indicação de \<um evento de login pelo nome da *conta*> de um dispositivo potencialmente infectado com malware, seguido por uma regra suspeita de encaminhamento de caixa de entrada foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O \< *nome* da conta de usuário> criou ou atualizou uma regra de \<encaminhamento de caixa de entrada que encaminha todos os e-mails de entrada para o endereço de endereço externo *>.* 

- **Evento de login de um dispositivo infectado levando a atividade administrativa suspeita de aplicativo em nuvem**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um dispositivo potencialmente infectado com malware.
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de* atividades administrativas> em uma única sessão.

- **Evento de login de um dispositivo infectado que leva à exclusão de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, \<o nome \<da *conta*> número excluído *de* arquivos> únicos em uma única sessão.

- **Evento de login de um dispositivo infectado que leva ao download de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, \<o nome da \< *conta*> baixado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login de um dispositivo infectado que leva à personificação do Office 365**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, \<o nome da \< *conta*> se passou por número *de*> diferentes contas em uma única sessão.

- **Evento de login de um dispositivo infectado que leva ao compartilhamento de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, \<o nome da \< *conta*> compartilhado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login de um dispositivo infectado que leva ao ransomware em aplicativo na nuvem**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, \<o nome \<da *conta*> o número carregado \< *de* arquivos> e excluiu um total de *>* arquivos. 
    
    Esse padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Atividade de login a partir de endereço IP anônimo seguido de atividade anômala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login a partir de alertas de endereçoIP anônimos do Azure AD Identity Protection e alertas anômalos do Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de login a partir de um endereço IP anônimo que leva à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um \< *endereço IP* de endereço IP proxy anônimo>, seguido por uma regra de encaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O \< *nome* da conta de usuário> criou ou atualizou uma regra de \<encaminhamento de caixa de entrada que encaminha todos os e-mails de entrada para o endereço de endereço externo *>.* 

- **Evento de login a partir de um endereço IP anônimo levando a atividade administrativa suspeita de aplicativo em nuvem**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um \< *endereço IP* de endereço IP proxy anônimo>. 
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de* atividades administrativas> em uma única sessão.

- **Evento de login a partir de um endereço IP anônimo que leva à exclusão de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um \< *endereço IP* de endereço IP proxy anônimo>. 
    
    Em seguida, \<o nome \<da *conta*> número excluído *de* arquivos> únicos em uma única sessão.

- **Evento de login a partir de um endereço IP anônimo que leva ao download de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um \< *endereço IP* de endereço IP proxy anônimo>. 
    
    Em seguida, \<o nome da \< *conta*> baixado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login a partir de um endereço IP anônimo que leva à personificação do Office 365**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um \< *endereço IP* de endereço IP proxy anônimo>. 
    
    Em seguida, \<o nome da \< *conta*> se passou por número *de*> diferentes contas em uma única sessão.

- **Evento de login a partir de um endereço IP anônimo que leva ao compartilhamento de arquivos em massa**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um \< *endereço IP* de endereço IP proxy anônimo>. 
    
    Em seguida, \<o nome da \< *conta*> compartilhado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login de um endereço IP anônimo para ransomware em aplicativo na nuvem**
    
    Este alerta é uma indicação de \<um evento de login por nome de *conta*> de um \< *endereço IP* de endereço IP proxy anônimo>. 
    
    Em seguida, \<o nome \<da *conta*> o número carregado \< *de* arquivos> e excluiu um total de *>* arquivos. 
    
    Esse padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Atividade de login de usuário com credenciais vazadas seguida sustais atividade do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam a atividade de login do usuário com alertas de credenciais vazadas do Azure AD Identity Protection e alertas anômalos do Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de login de usuário com credenciais vazadas levando à exfiltração da caixa de correio office 365**
    
    Este alerta é uma indicação de \<que o evento de login pelo nome da *conta*> usou credenciais vazadas, seguido por uma regra suspeita de encaminhamento de caixa de entrada foi definida na caixa de entrada de um usuário. 
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O \< *nome* da conta de usuário> criou ou atualizou uma regra de \<encaminhamento de caixa de entrada que encaminha todos os e-mails de entrada para o endereço de endereço externo *>.* 

- **Evento de login de usuário com credenciais vazadas levando a atividade administrativa suspeita de aplicativo em nuvem**
    
    Este alerta é uma indicação de \<que o evento de login por *nome da conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de* atividades administrativas> em uma única sessão.

- **Evento de login do Usuário com credenciais vazadas que levam à exclusão de arquivos em massa**
    
    Este alerta é uma indicação de \<que o evento de login por *nome da conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome \<da *conta*> número excluído *de* arquivos> únicos em uma única sessão.

- **Evento de login do Usuário com credenciais vazadas que levam ao download de arquivos em massa**
    
    Este alerta é uma indicação de \<que o evento de login por *nome da conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome da \< *conta*> baixado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login de usuário com credenciais vazadas que levam à personificação do Office 365**
    
    Este alerta é uma indicação de \<que o evento de login por *nome da conta*> usou credenciais vazadas. 
    
    Em seguida, \<o nome da \< *conta*> se passou por número *de*> diferentes contas em uma única sessão.

- **Evento de login do Usuário com credenciais vazadas que levam ao compartilhamento de arquivos em massa**
    
    Este alerta é uma indicação de \<que o evento de login por *nome da conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome da \< *conta*> compartilhado sobre o *número de* arquivos> únicos em uma única sessão.

- **Evento de login do usuário com credenciais vazadas para ransomware em aplicativo na nuvem**
    
    Este alerta é uma indicação de \<que o evento de login por *nome da conta*> usou credenciais vazadas. 
    
    Em seguida, \<o nome \<da *conta*> o número carregado \< *de* arquivos> e excluiu um total de *>* arquivos. 
    
    Esse padrão de atividade é indicativo de um potencial ataque de ransomware.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu mais sobre detecção avançada de ataques multiestágios, você pode estar interessado no seguinte início rápido para aprender como obter visibilidade em seus dados e ameaças potenciais: [Comece com o Azure Sentinel](quickstart-get-visibility.md).

Se você estiver pronto para investigar os incidentes criados para você, consulte o tutorial a seguir: [Investigue incidentes com o Azure Sentinel](tutorial-investigate-cases.md).

