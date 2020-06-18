---
title: Pontuação segura na Central de Segurança do Azure
description: Descrição da pontuação segura da Central de Segurança do Azure e seus controles de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 3b740fca47b233fe38915280a1f53458c139293d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778856"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Pontuação segura aprimorada (versão prévia) na Central de Segurança do Azure

## <a name="introduction-to-secure-score"></a>Introdução à pontuação segura

A Central de Segurança do Azure tem dois objetivos principais: ajudar você a entender sua situação de segurança atual e melhorar sua segurança de forma eficiente e eficaz. O aspecto central da Central de Segurança que permite alcançar essas metas é a pontuação segura.

A Central de Segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado. Use a pontuação para acompanhar os esforços de segurança e os projetos em sua organização. 

A página de pontuação segura da Central de Segurança inclui:

- **A pontuação** - a pontuação segura aparece como um valor de porcentagem, mas os valores subjacentes também são claros:

    [![A pontuação segura mostrada como um valor de porcentagem com os números subjacentes também é clara](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Controles de segurança** - cada controle é um grupo lógico de recomendações de segurança relacionadas e reflete as superfícies de ataque vulneráveis. Um controle é um conjunto de recomendações de segurança, com instruções que ajudam a implementar essas recomendações. Sua pontuação só melhora quando você corrige *todas* as recomendações para um único recurso dentro de um controle.

    Para ver imediatamente como sua organização está protegendo cada superfície de ataque individual, revise as pontuações de cada controle de segurança.

    Para obter mais informações, consulte [Como a pontuação segura é calculada](secure-score-security-controls.md#how-the-secure-score-is-calculated) abaixo. 


>[!TIP]
> As versões anteriores da Central de Segurança receberam pontos premiados no nível de recomendação: quando você corrigiu uma recomendação de um único recurso, sua pontuação segura melhorou. Hoje, sua pontuação só melhora se você corrigir *todas* as recomendações de um único recurso dentro de um controle. Portanto, sua pontuação só melhora quando você melhora a segurança de um recurso.
> Embora essa versão aprimorada ainda esteja em versão prévia, a experiência de pontuação segura anterior está disponível como uma opção no Portal do Azure. 


## <a name="locating-your-secure-score"></a>Como localizar sua pontuação segura

A Central de Segurança exibe sua pontuação de forma proeminente: é o primeiro item mostrado na página de Visão geral. Se você clicar na página de pontuação segura dedicada, verá a pontuação dividida por assinatura. Clique em uma assinatura única para ver a lista detalhada de recomendações priorizadas e o impacto potencial que a correção terá na pontuação da assinatura.

## <a name="how-the-secure-score-is-calculated"></a>Como a pontuação segura é calculada 

A contribuição de cada controle de segurança para a pontuação segura geral é mostrada claramente na página de recomendações.

[![A pontuação segura avançada apresenta os controles de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controle de segurança, todos os seus recursos devem estar em conformidade com todas as recomendações de segurança no controle de segurança. Por exemplo, a Central de Segurança tem várias recomendações sobre como proteger suas portas de gerenciamento. No passado, você poderia corrigir algumas dessas recomendações relacionadas e interdependentes, deixando outras não resolvidas, e sua pontuação segura melhoraria. Quando olhamos objetivamente, é fácil argumentar que sua segurança não havia melhorado até que você a resolvesse. Agora, você deve corrigir todas elas para fazer uma diferença na sua pontuação segura.

Por exemplo, o controle de segurança chamado "Aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que você pode ver na dica de ferramenta sobre o valor de aumento potencial do controle:

[![O controle de segurança "Aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A pontuação máxima para esse controle, Aplicar atualizações do sistema, é sempre 6. Neste exemplo, há 50 recursos. Portanto, dividimos a pontuação máxima por 50 e o resultado é que cada recurso contribui com 0,12 ponto. 

* **Potencial de aumento** (0,12 x 8 recursos não íntegros = 0,96) - os pontos restantes disponíveis para você no controle. Se você corrigir todas as recomendações neste controle, sua pontuação aumentará em 2% (nesse caso, 0,96 ponto arredondado para até um ponto). 
* **Pontuação atual** (0,12 x 42 recursos íntegros = 5,04) - a pontuação atual deste controle. Cada controle contribui para a pontuação total. Neste exemplo, o controle está contribuindo com 5,04 pontos para o total seguro atual.
* **Pontuação máxima** - o número máximo de pontos que você pode obter ao concluir todas as recomendações em um controle. A pontuação máxima de um controle indica o significado relativo desse controle. Use os valores máximos de pontuação para fazer a triagem dos problemas a serem resolvidos primeiro. 


### <a name="calculations---understanding-your-score"></a>Cálculos - noções básicas sobre sua pontuação

|Métrica|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controle de segurança**|<br>![Equação para calcular a pontuação atual de um controle de segurança](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Cada controle de segurança individual contribui para a pontuação de segurança. Cada recurso afetado por uma recomendação dentro do controle contribui para a pontuação atual do controle. A pontuação atual de cada controle é uma medida do status dos recursos *no* controle.<br>![Dicas de ferramenta mostrando os valores usados ao calcular a pontuação atual do controle de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos íntegros e não íntegros.<br>6 / 78 = 0,0769<br>Multiplicar isso pelo número de recursos íntegros (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Pontuação segura**<br>Assinatura única|<br>![Equação para calcular a pontuação segura atual](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Pontuação segura de assinatura única com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, há uma assinatura única com todos os controles de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os 32 pontos restantes são refletidos nas figuras de "Aumento da pontuação potencial" dos controles de segurança.<br>![Lista de controles e o aumento de pontuação potencial](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Pontuação segura**<br>Várias assinaturas|<br>As pontuações atuais de todos os recursos em todas as assinaturas são adicionadas e o cálculo é igual ao de uma assinatura única<br><br>Na visualização de várias assinaturas, a pontuação segura avalia todos os recursos em todas as políticas habilitadas e agrupa seu impacto combinado na pontuação máxima de cada controle de segurança.<br>![Pontuação segura para várias assinaturas com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do status de todos os recursos em todas as assinaturas.<br>Também neste caso, se você acessar a página de recomendações e adicionar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

## <a name="improving-your-secure-score"></a>Como melhorar sua pontuação segura

Para melhorar sua pontuação segura, corrija as recomendações de segurança da sua lista de recomendações. Você pode corrigir cada recomendação manualmente para cada recurso ou usando a opção **Correção Rápida!** (quando disponível) para aplicar rapidamente uma correção para uma recomendação a um grupo de recursos. Para obter mais informações, consulte [Recomendações de correção](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Somente as recomendações internas têm um impacto na pontuação segura.

## <a name="security-controls-and-their-recommendations"></a>Controles de segurança e suas recomendações

A tabela a seguir lista os controles de segurança na Central de Segurança do Azure. Para cada controle, é possível ver o número máximo de pontos que você poderá adicionar à sua pontuação segura se corrigir *todas* as recomendações listadas no controle, para *todos* os seus recursos. 

<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Controle de segurança, pontuação e descrição</b><br></th>
    <th class="tg-cly1"><b>Recomendações</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar MFA (máximo de pontuação 10)</p></strong>Se você usar apenas uma senha para autenticar um usuário, ela deixará um vetor de ataque aberto. Se a senha for fraca ou tiver sido exposta em outro lugar, será realmente o usuário entrando com o nome de usuário e a senha?<br>Com o <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> habilitado, suas contas estão mais seguras e os usuários ainda podem se autenticar em praticamente qualquer aplicativo com SSO (logon único).</td>
    <td class="tg-lboi"; width=55%>-O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura<br>-O MFA deve ser habilitado em contas com permissões de gravação na sua assinatura</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Proteger portas de gerenciamento (pontuação máxima de 8)</p></strong>Os ataques de força bruta direcionam as portas de gerenciamento para obter acesso a uma VM. Como as portas nem sempre precisam ser abertas, uma estratégia de mitigação é reduzir a exposição às portas usando controles de acesso à rede just-in-time, grupos de segurança de rede e gerenciamento de porta de máquina virtual.<br>Como muitas organizações de TI não bloqueiam a saída de comunicações SSH de sua rede, os invasores podem criar túneis criptografados que permitem que portas RDP em sistemas infectados se comuniquem com o comando do invasor para controlar servidores. Os invasores podem usar o subsistema de Gerenciamento Remoto do Windows para mover-se lateralmente em seu ambiente e usar credenciais roubadas para acessar outros recursos em uma rede.</td>
    <td class="tg-lboi"; width=55%>-O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais<br>-Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>-Portas de gerenciamento devem ser fechadas nas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar atualizações do sistema (pontuação máxima de 6)</p></strong>As atualizações do sistema fornecem às organizações a capacidade de manter a eficiência operacional, reduzir as vulnerabilidades de segurança e fornecer um ambiente mais estável para os usuários finais. A não aplicação de atualizações deixa as vulnerabilidades sem patches e resulta em ambientes suscetíveis a ataques. Essas vulnerabilidades podem ser exploradas e levar à perda de dados, ao vazamento de dados, ao ransomware e ao abuso de recursos. Você pode usar a <a href="https://docs.microsoft.com/azure/automation/automation-update-management">solução de Gerenciamento de Atualizações para gerenciar patches e atualizações</a> de suas máquinas virtuais. O gerenciamento de atualizações é o processo de controlar a implantação e a manutenção de versões de software.</td>
    <td class="tg-lboi"; width=55%>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>- O agente de monitoramento deve ser instalado nos conjuntos de dimensionamento de máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- A versão do sistema operacional deve ser atualizada para suas funções de serviço de nuvem<br>- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas<br>- As atualizações do sistema devem ser instaladas em suas máquinas<br>- Seus computadores devem ser reiniciados para que as atualizações do sistema sejam aplicadas<br>- Os Serviços do Kubernetes devem ser atualizados para uma versão não vulnerável do Kubernetes<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Corrigir vulnerabilidades (pontuação máxima de 6)</p></strong>Uma vulnerabilidade é um ponto fraco que um ator de ameaça pode aproveitar para comprometer a confidencialidade, a disponibilidade ou a integridade de um recurso. O <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">gerenciamento de vulnerabilidades</a> reduz a exposição organizacional, protege a área da superfície do ponto de extremidade, aumenta a resiliência organizacional e reduz a superfície de ataque de seus recursos. O Gerenciamento de Ameaças e Vulnerabilidades fornece visibilidade de configurações incorretas de software e segurança e fornece recomendações para mitigações.</td>
    <td class="tg-lboi"; width=55%>- A segurança de dados avançada deve ser habilitada nos servidores SQL<br>- As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas<br>- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas<br>- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades<br>- A avaliação de vulnerabilidades deve ser habilitada nas instâncias gerenciadas do SQL<br>- A avaliação de vulnerabilidades deve ser habilitada nos servidores SQL<br>- A solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar criptografia em repouso (pontuação máxima de 4)</p></strong><a href="https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest">A criptografia em repouso</a> fornece proteção de dados para dados armazenados. Os ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware no qual os dados são armazenados. Os Azures usam criptografia simétrica para criptografar e descriptografar grandes quantidades de dados em repouso. Uma chave de criptografia simétrica é usada para criptografar dados, pois eles são gravados no armazenamento. Essa chave de criptografia também é usada para descriptografar esses dados, pois eles estão prontos para uso na memória. As chaves devem ser armazenadas em uma localização segura com controle de acesso baseado em identidade e políticas de auditoria. Um local seguro é o Azure Key Vault. Se um invasor obtiver os dados criptografados, e não as chaves de criptografia, ele não poderá acessar os dados sem interromper a criptografia.</td>
    <td class="tg-lboi"; width=55%>- A criptografia de disco deve ser aplicada em máquinas virtuais<br>- A Transparent Data Encryption em bancos de dados SQL deve ser habilitada<br>- As variáveis da conta de automação devem ser criptografadas<br>- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign<br>- O protetor TDE do SQL Server deve ser criptografado com sua própria chave</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Criptografar dados em trânsito (pontuação máxima de 4)</p></strong>Os dados estão "em trânsito" quando são transmitidos entre componentes, locais ou programas. As organizações que não protegem os dados em trânsito são suscetíveis a ataques man-in-the-middle, espionagem e sequestro de sessão. Os protocolos SSL/TLS devem ser usados para trocar dados e uma VPN é recomendada. Quando você envia dados criptografados entre uma máquina virtual do Azure e um local, pela Internet, é possível usar um gateway de rede virtual, como <a href="https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways">Gateway de VPN do Azure</a>, para enviar tráfego criptografado.</td>
    <td class="tg-lboi"; width=55%>- O aplicativo de API só deve estar acessível via HTTPS<br>- O Aplicativo de Funções deve ser acessível apenas por HTTPS<br>- Apenas conexões seguras com o Cache Redis devem ser habilitadas<br>- A transferência segura para contas de armazenamento deve ser habilitada<br>- O Aplicativo Web deve ser acessível somente por HTTPS</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Gerenciar acesso e permissões (pontuação máxima de 4)</p></strong>Uma parte principal de um programa de segurança é garantir que os usuários tenham o acesso necessário para realizar seus trabalhos, porém, não mais do que isso: o <a href="https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">modelo de acesso de privilégios mínimos</a>.<br>Controle o acesso a seus recursos criando atribuições de função com <a href="https://docs.microsoft.com/azure/role-based-access-control/overview">RBAC (controle de acesso baseado em função)</a>. Uma atribuição de função consiste em três elementos:<br>- <strong>Entidade de segurança</strong>: o objeto ao qual o usuário está solicitando acesso<br>- <strong>Definição de função</strong>: suas permissões<br>- <strong>Escopo</strong>: o conjunto de recursos ao qual as permissões se aplicam</td>
    <td class="tg-lboi"; width=55%>- As contas preteridas devem ser removidas da sua assinatura (Versão Prévia)<br>- As contas preteridas com permissões de proprietário devem ser removidas da sua assinatura (Versão Prévia)<br>- As contas externas com permissões de proprietário devem ser removidas da sua assinatura (Versão Prévia)<br>- As contas externas com permissões de gravação devem ser removidas da sua assinatura (Versão Prévia)<br>- Deve haver mais de um proprietário atribuído à sua assinatura<br>- O RBAC (Controle de Acesso Baseado em Função) deve ser usado nos Serviços do Kubernetes (Versão Prévia)<br>- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Corrigir configurações de segurança (pontuação máxima de 4)</p></strong>Os ativos de TI configurados incorretamente têm um risco maior de ser atacado. As ações básicas de proteção geralmente são esquecidas quando os ativos estão sendo implantados e os prazos devem ser atendidos. As configurações incorretas de segurança podem estar em qualquer nível na infraestrutura: desde sistemas operacionais e dispositivos de rede a recursos de nuvem.<br>A Central de Segurança do Azure compara continuamente a configuração de seus recursos com os requisitos de padrões, regulamentos e parâmetros de comparação do setor. Quando você configurar os "pacotes de conformidade" relevantes (padrões e linhas de base) que são importantes para sua organização, quaisquer lacunas resultarão em recomendações de segurança que incluem o CCEID e uma explicação do possível impacto na segurança.<br>Os pacotes comumente usados são <a href="https://docs.microsoft.com/azure/security/benchmarks/introduction">Azure Security Benchmark</a> e <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations Benchmark versão 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- As Políticas de Segurança de Pods devem ser definidas nos Serviços do Kubernetes<br>- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas<br>- As vulnerabilidades na configuração de segurança nas máquinas devem ser corrigidas<br>- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- O agente de monitoramento deve ser instalado nos conjuntos de dimensionamento de máquinas virtuais<br>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Restringir o acesso não autorizado à rede (pontuação máxima de 4)</p></strong>Os pontos de extremidade em uma organização fornecem uma conexão direta de sua rede virtual com os serviços do Azure permitidos. As máquinas virtuais em uma sub-rede podem se comunicar com todos os recursos. Para limitar a comunicação de e para recursos em uma sub-rede, crie um grupo de segurança de rede e associe-o à sub-rede. As organizações podem limitar e proteger contra tráfego não autorizado criando regras de entrada e saída.</td>
    <td class="tg-lboi"; width=55%>- O encaminhamento de IP na máquina virtual deve ser desabilitado<br>- Os intervalos de IP autorizados devem ser definidos nos Serviços do Kubernetes (Versão Prévia)<br>- (PRETERIDO) O acesso aos Serviços de Aplicativos deve ser restrito (Versão Prévia)<br>- (PRETERIDO) As regras de aplicativos Web nos NSGs de IaaS devem ser fortalecidas<br>- Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>- O CORS não deve permitir que todos os recursos tenham acesso ao seu Aplicativo de API<br>- O CORS não deve permitir que todos os recursos acessem seu Aplicativo de Funções<br>- O CORS não deve permitir que todos os recursos acessem seu Aplicativo Web<br>- A depuração remota deve ser desativada para o Aplicativo de API<br>- A depuração remota deve ser desativada para o Aplicativo de Funções<br>- A depuração remota deve ser desativada para o Aplicativo Web<br>- O acesso deve ser restrito para os Grupos de Segurança de Rede permissivos com VMs para a Internet<br>- As Regras do Grupo de Segurança de Rede nas máquinas virtuais para a Internet devem ser protegidas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar controle de aplicativo adaptável (pontuação máxima de 3)</p></strong>O AAC (controle de aplicativo adaptável) é uma solução inteligente, automatizada e de ponta a ponta, que permite controlar quais aplicativos podem ser executados em seus computadores Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware.<br>A Central de Segurança usa o aprendizado de máquina para criar uma lista de permissões de aplicativos conhecidos para um grupo de computadores.<br>Essa abordagem inovadora da lista de permissões de aplicativos fornece os benefícios de segurança sem a complexidade do gerenciamento.<br>O AAC é particularmente relevante para servidores com finalidade específica que precisam executar um conjunto específico de aplicativos.</td>
    <td class="tg-lboi"; width=55%>- Os Controles de Aplicativos Adaptáveis devem ser habilitados em máquinas virtuais<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar classificação de dados (pontuação máxima de 2)</p></strong>Classificar os dados da sua organização por sensibilidade e impacto nos negócios permite que você determine e atribua valor aos dados e fornece a estratégia e a base para governança.<br>A <a href="https://docs.microsoft.com/azure/information-protection/what-is-information-protection">Proteção de Informações do Azure</a> pode ajudar na classificação de dados. Ela usa políticas de criptografia, identidade e autorização para proteger dados e restringir o acesso a dados. Algumas classificações usadas pela Microsoft são não comerciais, públicas, gerais, confidenciais e altamente confidenciais.</td>
    <td class="tg-lboi"; width=55%>- Dados confidenciais em seus bancos de dados SQL devem ser classificados (versão prévia)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Proteger aplicativos contra ataques de DDoS (pontuação máxima de 2)</p></strong>Ataques de DDoS (negação de serviço distribuído) sobrecarregam recursos e renderizam aplicativos inutilizáveis. Use o <a href="https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview">Padrão de Proteção contra DDoS do Azure</a> para defender sua organização dos três tipos principais de ataques de DDoS:<br>Os - <strong>ataques volumétricos</strong> inundam a rede com tráfego legítimo. O Padrão da Proteção contra DDoS atenua esses ataques, absorvendo-os ou depurando-os automaticamente.<br>- <strong>Ataques de protocolo</strong> renderizam um destino inacessível, explorando uma vulnerabilidade na pilha de protocolos das camadas 3 e 4. O Padrão da Proteção contra DDoS atenua esses ataques bloqueando o tráfego mal-intencionado.<br>Os - <strong>ataques de camada de recurso (aplicativo)</strong> apontam para os pacotes de aplicativos Web. Proteja-se contra esse tipo com um firewall do aplicativo Web e o Padrão da Proteção contra DDoS.</td>
    <td class="tg-lboi"; width=55%>- O Padrão da Proteção contra DDoS deve ser habilitada</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar a proteção do ponto de extremidade (pontuação máxima de 2)</p></strong>Para garantir que seus pontos de extremidade sejam protegidos contra malware, os sensores comportamentais coletam e processam dados dos sistemas operacionais de seus pontos de extremidade e enviam esses dados para a nuvem privada para análise. A análise de segurança utiliza big data, aprendizado de máquina e outras fontes para recomendar respostas a ameaças. Por exemplo, o <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> usa a inteligência contra ameaças para identificar métodos de ataque e gerar alertas de segurança.<br>A Central de Segurança dá suporte às seguintes soluções de proteção de ponto de extremidade: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, McAfee V10 para Windows, McAfee V10 para Linux e Sophos v9 para Linux. Se a Central de Segurança detectar qualquer uma dessas soluções, a recomendação para instalar a proteção de ponto de extremidade não será mais exibida.</td>
    <td class="tg-lboi"; width=55%>- As falhas de integridade da proteção de ponto de extremidade devem ser corrigidas nos conjuntos de dimensionamento de máquinas virtuais<br>- Os problemas de integridade da proteção de ponto de extremidade devem ser resolvidos nos seus computadores<br>- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais<br>- Instalar a solução de proteção de ponto de extremidade em máquinas virtuais<br>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>- O agente de monitoramento deve ser instalado nos conjuntos de dimensionamento de máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- Instale a solução de proteção de ponto de extremidade em suas máquinas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar auditoria e registro em log (pontuação máxima de 1)</p></strong>Os dados de registro em log fornecem informações sobre problemas anteriores, evitam possíveis, podem melhorar o desempenho do aplicativo e fornecem a capacidade de automatizar ações que, caso contrário, seriam manuais.<br>Os - <strong>logs de controle e gerenciamento</strong> fornecem informações sobre as operações do <a href="https://docs.microsoft.com/azure/azure-resource-manager/management/overview">Azure Resource Manager</a>.<br>Os - <strong>logs de plano de dados</strong> fornecem informações sobre eventos gerados como parte de uso de recursos do Azure.<br>Os - <strong>eventos processados</strong> fornecem informações sobre os eventos/alertas analisados que foram processados.</td>
    <td class="tg-lboi"; width=55%>- A auditoria no SQL Server deve ser habilitada<br>- Os logs de diagnóstico nos Serviços de Aplicativos devem ser habilitados<br>- Os logs de diagnóstico no Azure Data Lake Store devem ser habilitados<br>- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados<br>- Os logs de diagnóstico em contas do Lote devem ser habilitados<br>- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados<br>- Os logs de diagnóstico no Hub de Eventos devem ser habilitados<br>- Os logs de diagnóstico no Hub IoT devem ser habilitados<br>- Os logs de diagnóstico no Key Vault devem estar habilitados<br>- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados<br>- Os logs de diagnóstico no serviço Pesquisa devem ser habilitados<br>- Os logs de diagnóstico no Barramento de Serviço devem ser habilitados<br>- Os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados<br>- As regras de alerta de métrica devem ser configuradas em contas do Lote<br>- As configurações de Auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas<br>- Os servidores SQL devem ser configurados com um período de retenção de auditoria superior a 90 dias.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementar melhores práticas de segurança (pontuação máxima de 0)</p></strong>Práticas de segurança modernas "assumem a violação" do perímetro da rede. Por esse motivo, muitas das melhores práticas neste controle se concentram no gerenciamento de identidades.<br>Perder chaves e credenciais é um problema comum. O <a href="https://docs.microsoft.com/azure/key-vault/key-vault-overview">Azure Key Vault</a> protege chaves e segredos criptografando chaves, arquivos. pfx e senhas.<br>As VPNs (redes virtuais privadas) são uma maneira segura de acessar suas máquinas virtuais. Se as VPNs não estiverem disponíveis, use frases secretas complexas e autenticação de dois fatores, como <a href="https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks">Autenticação Multifator do Azure</a>. A autenticação de dois fatores evita os pontos fracos inerentes apenas a nomes de usuário e senhas.<br>O uso de plataformas de autorização e autenticação forte é outra melhor prática. O uso de identidades federadas permite que as organizações deleguem o gerenciamento de identidades autorizadas. Isso também é importante quando os funcionários são demitidos e o acesso precisa ser revogado.</td>
    <td class="tg-lboi"; width=55%>- Um máximo de três proprietários deve ser designado para sua assinatura<br>- As contas externas com permissões de leitura devem ser removidas de sua assinatura<br>- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura<br>- O acesso às contas de armazenamento com configurações de firewall e de rede virtual deve ser restrito<br>- Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do Hub de Eventos<br>- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL<br>- As regras de autorização na instância do Hub de Eventos devem ser definidas<br>- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager<br>- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager<br>- As configurações de segurança de dados avançada para o SQL Server devem conter um endereço de e-mail para receber alertas de segurança<br>- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas<br>- Todos os tipos de proteção avançada contra ameaças devem ser habilitados nas configurações de segurança de dados avançada da instância gerenciada do SQL<br>- As notificações por e-mail para os administradores e proprietários de assinaturas devem ser habilitadas nas configurações de segurança de dados avançada do SQL Server<br>- Os tipos de Proteção Avançada contra Ameaças devem estar definidos como "Todos" nas configurações de Segurança de Dados Avançada do SQL Server<br>- As sub-redes devem ser associadas a um Grupo de Segurança de Rede<br>- Todos os tipos de proteção avançada contra ameaças devem ser habilitados nas configurações de segurança de dados avançada do SQL Server<br>- [Versão prévia] O Windows Exploit Guard deve estar habilitado <br>- [Versão prévia] O agente de configuração do convidado deve ser instalado</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Perguntas frequentes sobre pontuação segura

### <a name="why-has-my-secure-score-gone-down"></a>Por que minha pontuação segura desapareceu?
A Central de Segurança mudou para uma pontuação segura aprimorada (atualmente em status de versão prévia), que inclui alterações na forma como a pontuação é calculada. Agora, você deve resolver todas as recomendações de que um recurso para receber pontos. As pontuações também foram alteradas para uma escala de 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações em um controle de segurança, minha pontuação de proteção será alterada?
Não. Ela não será alterada até que você corrija todas as recomendações para um único recurso. Para obter a pontuação máxima de um controle, corrija todas as recomendações de todos os recursos.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>A experiência anterior da pontuação segura ainda está disponível? 
Sim. Por um tempo, eles serão executados lado a lado para facilitar a transição. Espere que o modelo anterior seja dividido dentro do prazo. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim e eu desabilitá-la na política, meu controle de segurança será atendido e minha pontuação segura será atualizada?
Sim. É recomendável desabilitar as recomendações quando elas não são aplicáveis em seu ambiente. Para obter instruções sobre como desabilitar uma recomendação específica, consulte [Desabilitar políticas de segurança](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Caso um controle de segurança me ofereça zero pontos em relação à minha pontuação segura, devo ignorá-lo?
Em alguns casos, você verá uma pontuação máxima de controle maior que zero, mas o impacto é zero. Quando a pontuação incremental para corrigir recursos é insignificante, ela é arredondada para zero. Não ignore essas recomendações, pois elas ainda trazem melhorias de segurança. A única exceção é o controle de "melhor prática adicional". A correção dessas recomendações não aumentará sua pontuação, mas vai aprimorar a segurança geral.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a pontuação segura e os controles de segurança que ela apresenta. Para obter material relacionado, consulte os seguintes artigos:

- [Saiba mais sobre os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como corrigir recomendações](security-center-remediate-recommendations.md)