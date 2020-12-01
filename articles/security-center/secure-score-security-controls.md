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
ms.date: 11/10/2020
ms.author: memildin
ms.openlocfilehash: 0e853a4ce1e3891ddffd2f9fb1315da49a896933
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433152"
---
# <a name="secure-score-in-azure-security-center"></a>Pontuação segura na Central de Segurança do Azure

## <a name="introduction-to-secure-score"></a>Introdução à pontuação segura

A central de segurança do Azure tem dois objetivos principais: 

- para ajudá-lo a entender sua situação de segurança atual
- para ajudá-lo a melhorar sua segurança de forma eficiente e eficaz

O recurso central na central de segurança que permite alcançar essas metas é a **Pontuação segura**.

A Central de Segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado.

A pontuação segura é mostrada na portal do Azure páginas como um valor percentual, mas os valores subjacentes também são claramente apresentados:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Pontuação segura geral, conforme mostrado no portal":::

Para aumentar sua segurança, examine a página recomendações da central de segurança para as ações pendentes necessárias para aumentar sua pontuação. Cada recomendação inclui instruções para ajudá-lo a corrigir o problema específico.

As recomendações são agrupadas em **controles de segurança**. Cada controle é um grupo lógico de recomendações de segurança relacionadas e reflete as superfícies de ataque vulneráveis. Sua pontuação só melhora quando você corrige *todas* as recomendações para um único recurso dentro de um controle. Para ver como sua organização está protegendo cada superfície de ataque individual, examine as pontuações de cada controle de segurança.

Para obter mais informações, consulte [como sua pontuação segura é calculada](secure-score-security-controls.md#how-your-secure-score-is-calculated) abaixo. 


## <a name="access-your-secure-score"></a>Acesse sua pontuação segura

Você pode encontrar a pontuação segura geral, bem como sua pontuação por assinatura, por meio da portal do Azure ou de forma programática, conforme descrito nas seções a seguir:

- [Obtenha sua pontuação segura no portal](#get-your-secure-score-from-the-portal)
- [Obtenha sua pontuação segura da API REST](#get-your-secure-score-from-the-rest-api)
- [Obtenha sua pontuação segura do grafo de recursos do Azure (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Obtenha sua pontuação segura no portal

A central de segurança exibe sua pontuação de forma proeminente no Portal: é o primeiro bloco principal da página Visão geral da central de segurança. Ao selecionar esse bloco, você será levado para a página de Pontuação segura dedicada, na qual verá a pontuação dividida por assinatura. Selecione uma única assinatura para ver a lista detalhada de recomendações priorizadas e o impacto potencial que a correção terá na pontuação da assinatura.

Para recapitular, sua pontuação segura é mostrada nos locais a seguir nas páginas do portal da central de segurança.

- Em um bloco na **visão geral** da central de segurança (painel principal):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A pontuação segura no painel da central de segurança":::

- Na página **Pontuação segura** dedicada:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="A pontuação de segurança na página de Pontuação segura da central de segurança":::

- Na parte superior da página de **recomendações** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A pontuação de segurança na página de recomendações da central de segurança":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Obtenha sua pontuação segura da API REST

Você pode acessar sua pontuação por meio da API de Pontuação segura (atualmente em visualização). Os métodos de API oferecem a flexibilidade para consultar os dados e criar seu mecanismo de relatório das suas classificações de segurança ao longo do tempo. Por exemplo, você pode usar a [API de pontuações seguras](/rest/api/securitycenter/securescores) para obter a pontuação de uma assinatura específica. Além disso, você pode usar a [API de controles de Pontuação segura](/rest/api/securitycenter/securescorecontrols) para listar os controles de segurança e a pontuação atual de suas assinaturas.

![Recuperando uma única Pontuação segura por meio da API](media/secure-score-security-controls/single-secure-score-via-api.png)

Para obter exemplos de ferramentas criadas com base na API de Pontuação segura, consulte [a área de Pontuação segura da nossa comunidade do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Obtenha sua pontuação segura do grafo de recursos do Azure (ARG)

O grafo de recursos do Azure fornece acesso instantâneo a informações de recursos em seus ambientes de nuvem com recursos robustos de filtragem, agrupamento e classificação. É uma maneira rápida e eficiente de consultar informações em assinaturas do Azure programaticamente ou de dentro do portal do Azure. [Saiba mais sobre o grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/).

Para acessar a pontuação segura para várias assinaturas com ARG:

1. No portal do Azure, abra o **Gerenciador de grafo de recursos do Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Iniciando a página de recomendação do Gerenciador de gráficos de recursos do Azure * *" :::

1. Insira sua consulta do Kusto (usando os exemplos abaixo para obter diretrizes).

    - Essa consulta retorna a ID da assinatura, a pontuação atual em pontos e como porcentagem e a pontuação máxima para a assinatura. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Essa consulta retorna o status de todos os controles de segurança. Para cada controle, você obterá o número de recursos não íntegros, a pontuação atual e a pontuação máxima. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecione **Executar consulta**.




## <a name="tracking-your-secure-score-over-time"></a>Acompanhando sua pontuação segura ao longo do tempo

Se você for um usuário Power BI com uma conta pro, poderá usar o painel de **Pontuação segura ao longo do tempo** Power bi para controlar sua pontuação segura ao longo do tempo e investigar as alterações.

> [!TIP]
> Você pode encontrar esse painel, bem como outras ferramentas para trabalhar de forma programática com Pontuação segura, na área dedicada da Comunidade da central de segurança do Azure no GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

O painel contém os dois relatórios a seguir para ajudá-lo a analisar seu status de segurança:

- **Resumo de recursos** -fornece dados resumidos sobre a integridade dos seus recursos.
- **Resumo da Pontuação de segurança** – fornece dados resumidos sobre o andamento da pontuação. Use o gráfico "Pontuação segura ao longo do tempo por assinatura" para exibir as alterações na pontuação. Se você perceber uma alteração drástica na sua pontuação, marque a tabela "alterações detectadas que podem afetar sua pontuação segura" para possíveis alterações que possam ter causado a alteração. Esta tabela apresenta recursos excluídos, recursos implantados recentemente ou recursos que seu status de segurança alterou para uma das recomendações.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="O painel do PowerBI de Pontuação segura opcional ao longo do tempo para acompanhar sua pontuação segura ao longo do tempo e investigar as alterações":::





## <a name="how-your-secure-score-is-calculated"></a>Como sua pontuação segura é calculada 

A contribuição de cada controle de segurança para a pontuação segura geral é mostrada claramente na página de recomendações.

[![A pontuação segura avançada apresenta os controles de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controle de segurança, todos os seus recursos devem estar em conformidade com todas as recomendações de segurança no controle de segurança. Por exemplo, a Central de Segurança tem várias recomendações sobre como proteger suas portas de gerenciamento. Você precisará corrigi-los para fazer uma diferença na sua pontuação segura.

Por exemplo, o controle de segurança chamado "Aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que você pode ver na dica de ferramenta sobre o valor de aumento potencial do controle:

[![O controle de segurança "Aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A pontuação máxima para esse controle, Aplicar atualizações do sistema, é sempre 6. Neste exemplo, há 50 recursos. Portanto, dividimos a pontuação máxima por 50 e o resultado é que cada recurso contribui com 0,12 ponto. 

* **Potencial de aumento** (0,12 x 8 recursos não íntegros = 0,96) - os pontos restantes disponíveis para você no controle. Se você corrigir todas as recomendações neste controle, sua pontuação aumentará em 2% (nesse caso, 0,96 ponto arredondado para até um ponto). 
* **Pontuação atual** (0,12 x 42 recursos íntegros = 5,04) - a pontuação atual deste controle. Cada controle contribui para a pontuação total. Neste exemplo, o controle está contribuindo com 5,04 pontos para o total seguro atual.
* **Pontuação máxima** - o número máximo de pontos que você pode obter ao concluir todas as recomendações em um controle. A pontuação máxima de um controle indica o significado relativo desse controle. Use os valores máximos de pontuação para fazer a triagem dos problemas a serem resolvidos primeiro. 


### <a name="calculations---understanding-your-score"></a>Cálculos - noções básicas sobre sua pontuação

|Métrica|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controle de segurança**|<br>![Equação para calcular a pontuação de um controle de segurança](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Cada controle de segurança individual contribui para a pontuação de segurança. Cada recurso afetado por uma recomendação dentro do controle contribui para a pontuação atual do controle. A pontuação atual de cada controle é uma medida do status dos recursos *no* controle.<br>![Dicas de ferramenta mostrando os valores usados ao calcular a pontuação atual do controle de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos íntegros e não íntegros.<br>6 / 78 = 0,0769<br>Multiplicar isso pelo número de recursos íntegros (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Pontuação segura**<br>Assinatura única|<br>![Equação para calcular a pontuação segura de uma assinatura](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Pontuação segura de assinatura única com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, há uma assinatura única com todos os controles de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os 32 pontos restantes são refletidos nas figuras de "Aumento da pontuação potencial" dos controles de segurança.<br>![Lista de controles e o aumento de pontuação potencial](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Pontuação segura**<br>Várias assinaturas|<br>![Equação para calcular a pontuação segura para várias assinaturas](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Ao calcular a pontuação combinada para várias assinaturas, a central de segurança inclui um *peso* para cada assinatura. Os pesos relativos para suas assinaturas são determinados pela central de segurança com base em fatores como o número de recursos.<br>A pontuação atual de cada assinatura é calculada da mesma maneira que para uma única assinatura, mas, em seguida, o peso é aplicado conforme mostrado na equação.<br>Na visualização de várias assinaturas, a pontuação segura avalia todos os recursos em todas as políticas habilitadas e agrupa seu impacto combinado na pontuação máxima de cada controle de segurança.<br>![Pontuação segura para várias assinaturas com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do status de todos os recursos em todas as assinaturas.<br>Também neste caso, se você acessar a página de recomendações e adicionar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Quais recomendações estão incluídas nos cálculos de Pontuação segura?

Somente as recomendações internas têm um impacto na pontuação segura.

As recomendações sinalizadas como **Visualização** não estão incluídas nos cálculos da sua pontuação segura. Elas ainda deverão ser corrigidas sempre que possível, para que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Um exemplo de recomendação de versão prévia:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com o sinalizador de versão prévia":::

## <a name="improve-your-secure-score"></a>Melhorar sua pontuação segura

Para melhorar sua pontuação segura, corrija as recomendações de segurança da sua lista de recomendações. Você pode corrigir cada recomendação manualmente para cada recurso ou usando a opção **Correção Rápida!** (quando disponível) para aplicar rapidamente uma correção para uma recomendação a um grupo de recursos. Para obter mais informações, consulte [Recomendações de correção](security-center-remediate-recommendations.md).

Outra maneira de melhorar sua pontuação e garantir que os usuários não criem recursos que afetam negativamente sua pontuação é configurar as opções de aplicação e negação nas recomendações relevantes. Saiba mais em [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Controles de segurança e suas recomendações

A tabela a seguir lista os controles de segurança na Central de Segurança do Azure. Para cada controle, é possível ver o número máximo de pontos que você poderá adicionar à sua pontuação segura se corrigir *todas* as recomendações listadas no controle, para *todos* os seus recursos. 

O conjunto de recomendações de segurança fornecido com a central de segurança é adaptado aos recursos disponíveis no ambiente de cada organização. As recomendações podem ser personalizadas ainda mais com a [desabilitação de políticas](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) e [a isenção de recursos específicos de uma recomendação](exempt-resource.md). 
 
Recomendamos que cada organização revise cuidadosamente suas iniciativas de Azure Policy atribuídas. 

> [!TIP]
> Para obter detalhes sobre como revisar e editar suas iniciativas, consulte [trabalhando com políticas de segurança](tutorial-security-policy.md). 

Embora a iniciativa de segurança padrão da central de segurança seja baseada em padrões e práticas recomendadas do setor, há cenários em que as recomendações internas listadas abaixo podem não se ajustar completamente à sua organização. Consequentemente, às vezes, será necessário ajustar a iniciativa padrão-sem comprometer a segurança-para garantir que ela esteja alinhada com as políticas próprias da sua organização. padrões do setor, padrões regulatórios e benchmarks que você está obrigada a atender.<br><br>
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
    <td class="tg-lboi"; width=55%>- As portas de gerenciamento de máquinas virtuais devem ser protegidas com o controle de acesso à rede just-in-time<br>-Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>-Portas de gerenciamento devem ser fechadas nas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar atualizações do sistema (pontuação máxima de 6)</p></strong>As atualizações do sistema fornecem às organizações a capacidade de manter a eficiência operacional, reduzir as vulnerabilidades de segurança e fornecer um ambiente mais estável para os usuários finais. A não aplicação de atualizações deixa as vulnerabilidades sem patches e resulta em ambientes suscetíveis a ataques. Essas vulnerabilidades podem ser exploradas e levar à perda de dados, ao vazamento de dados, ao ransomware e ao abuso de recursos. Você pode usar a <a href="/azure/automation/update-management/overview">solução de Gerenciamento de Atualizações para gerenciar patches e atualizações</a> de suas máquinas virtuais. O gerenciamento de atualizações é o processo de controlar a implantação e a manutenção de versões de software.</td>
    <td class="tg-lboi"; width=55%>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>- O agente de monitoramento deve ser instalado nos conjuntos de dimensionamento de máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- A versão do sistema operacional deve ser atualizada para suas funções de serviço de nuvem<br>- As atualizações do sistema nos conjuntos de dimensionamento de máquinas virtuais devem ser instaladas<br>- As atualizações do sistema devem ser instaladas em suas máquinas<br>- Seus computadores devem ser reiniciados para que as atualizações do sistema sejam aplicadas<br>- Os Serviços do Kubernetes devem ser atualizados para uma versão não vulnerável do Kubernetes<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de Log Analytics deve ser instalado em suas máquinas de arco do Azure baseadas no Windows (versão prévia)<br>- O agente de Log Analytics deve ser instalado em seus computadores de Arc do Azure baseados em Linux (versão prévia)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Corrigir vulnerabilidades (pontuação máxima de 6)</p></strong>Uma vulnerabilidade é um ponto fraco que um ator de ameaça pode aproveitar para comprometer a confidencialidade, a disponibilidade ou a integridade de um recurso. O <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">gerenciamento de vulnerabilidades</a> reduz a exposição organizacional, protege a área da superfície do ponto de extremidade, aumenta a resiliência organizacional e reduz a superfície de ataque de seus recursos. O Gerenciamento de Ameaças e Vulnerabilidades fornece visibilidade de configurações incorretas de software e segurança e fornece recomendações para mitigações.</td>
    <td class="tg-lboi"; width=55%>- A segurança de dados avançada deve ser habilitada no banco de dados SQL<br>- As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas<br>- As vulnerabilidades nos bancos de dados SQL devem ser corrigidas<br>- As vulnerabilidades devem ser corrigidas por uma solução de Avaliação de Vulnerabilidades<br>- A avaliação de vulnerabilidade deve ser habilitada no SQL Instância Gerenciada<br>- A avaliação de vulnerabilidades deve ser habilitada nos servidores SQL<br>- A solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais<br>- As imagens de contêiner devem ser implantadas somente de registros confiáveis (versão prévia)<br>- Azure Policy complemento para kubernetes deve ser instalado e habilitado em seus clusters (visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar criptografia em repouso (pontuação máxima de 4)</p></strong><a href="/azure/security/fundamentals/encryption-atrest">A criptografia em repouso</a> fornece proteção de dados para dados armazenados. Os ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware no qual os dados são armazenados. Os Azures usam criptografia simétrica para criptografar e descriptografar grandes quantidades de dados em repouso. Uma chave de criptografia simétrica é usada para criptografar dados, pois eles são gravados no armazenamento. Essa chave de criptografia também é usada para descriptografar esses dados, pois eles estão prontos para uso na memória. As chaves devem ser armazenadas em uma localização segura com controle de acesso baseado em identidade e políticas de auditoria. Um local seguro é o Azure Key Vault. Se um invasor obtiver os dados criptografados, e não as chaves de criptografia, ele não poderá acessar os dados sem interromper a criptografia.</td>
    <td class="tg-lboi"; width=55%>- A criptografia de disco deve ser aplicada em máquinas virtuais<br>- Transparent Data Encryption no banco de dados SQL deve ser habilitado<br>- As variáveis da conta de automação devem ser criptografadas<br>- A propriedade ClusterProtectionLevel dos clusters do Service Fabric deve ser definida como EncryptAndSign<br>- O protetor TDE do SQL Server deve ser criptografado com sua própria chave</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Criptografar dados em trânsito (pontuação máxima de 4)</p></strong>Os dados estão "em trânsito" quando são transmitidos entre componentes, locais ou programas. As organizações que não protegem os dados em trânsito são suscetíveis a ataques man-in-the-middle, espionagem e sequestro de sessão. Os protocolos SSL/TLS devem ser usados para trocar dados e uma VPN é recomendada. Quando você envia dados criptografados entre uma máquina virtual do Azure e um local, pela Internet, é possível usar um gateway de rede virtual, como <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Gateway de VPN do Azure</a>, para enviar tráfego criptografado.</td>
    <td class="tg-lboi"; width=55%>- O aplicativo de API só deve estar acessível via HTTPS<br>- O Aplicativo de Funções deve ser acessível apenas por HTTPS<br>- Apenas conexões seguras com o Cache Redis devem ser habilitadas<br>- A transferência segura para contas de armazenamento deve ser habilitada<br>- O Aplicativo Web deve ser acessível somente por HTTPS<br>- O ponto de extremidade privado deve ser habilitado para servidores PostgreSQL<br>- Impor a conexão SSL deve ser habilitada para servidores de banco de dados PostgreSQL<br>- Impor a conexão SSL deve ser habilitada para servidores de banco de dados MySQL<br>- O TLS deve ser atualizado para a versão mais recente para seu aplicativo de API<br>- O TLS deve ser atualizado para a versão mais recente para seu aplicativo de funções<br>- O TLS deve ser atualizado para a versão mais recente para seu aplicativo Web<br>- FTPS deve ser necessário em seu aplicativo de API<br>- FTPS deve ser necessário em seu aplicativo de funções<br>- FTPS deve ser necessário em seu aplicativo Web</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Gerenciar acesso e permissões (pontuação máxima de 4)</p></strong>Uma parte principal de um programa de segurança é garantir que os usuários tenham o acesso necessário para realizar seus trabalhos, porém, não mais do que isso: o <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">modelo de acesso de privilégios mínimos</a>.<br>Controle o acesso a seus recursos criando atribuições de função com o Azure <a href="/azure/role-based-access-control/overview">RBAC (controle de acesso baseado em função)</a>. Uma atribuição de função consiste em três elementos:<br>- <strong>Entidade de segurança</strong>: o objeto ao qual o usuário está solicitando acesso<br>- <strong>Definição de função</strong>: suas permissões<br>- <strong>Escopo</strong>: o conjunto de recursos ao qual as permissões se aplicam</td>
    <td class="tg-lboi"; width=55%>- As contas preteridas devem ser removidas da sua assinatura (Versão Prévia)<br>- As contas preteridas com permissões de proprietário devem ser removidas da sua assinatura (Versão Prévia)<br>- As contas externas com permissões de proprietário devem ser removidas da sua assinatura (Versão Prévia)<br>- As contas externas com permissões de gravação devem ser removidas da sua assinatura (Versão Prévia)<br>- Deve haver mais de um proprietário atribuído à sua assinatura<br>- O Azure RBAC (controle de acesso baseado em função) deve ser usado em serviços Kubernetess (versão prévia)<br>- Os clusters do Service Fabric só devem usar o Azure Active Directory para autenticação de cliente<br>- As entidades de serviço devem ser usadas para proteger suas assinaturas em vez de certificados de gerenciamento<br>- Os recursos do Linux com privilégios mínimos devem ser impostos para contêineres (versão prévia)<br>- O sistema de arquivos raiz imutável (somente leitura) deve ser imposto para contêineres (versão prévia)<br>- O contêiner com elevação de privilégio deve ser evitado (versão prévia)<br>- Os contêineres em execução como usuário raiz devem ser evitados (visualização)<br>- Os contêineres que compartilham namespaces de host confidenciais devem ser evitados (visualização)<br>- O uso de montagens de volume HostPath Pod deve ser restrito a uma lista conhecida (versão prévia)<br>- Contêineres com privilégios devem ser evitados (versão prévia)<br>- Azure Policy complemento para kubernetes deve ser instalado e habilitado em seus clusters (visualização)<br>- Os aplicativos Web devem solicitar um certificado SSL para todas as solicitações de entrada<br>- A identidade gerenciada deve ser usada em seu aplicativo de API<br>- A identidade gerenciada deve ser usada em seu aplicativo de funções<br>- A identidade gerenciada deve ser usada em seu aplicativo Web</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Corrigir configurações de segurança (pontuação máxima de 4)</p></strong>Os ativos de TI configurados incorretamente têm um risco maior de ser atacado. As ações básicas de proteção geralmente são esquecidas quando os ativos estão sendo implantados e os prazos devem ser atendidos. As configurações incorretas de segurança podem estar em qualquer nível na infraestrutura: desde sistemas operacionais e dispositivos de rede a recursos de nuvem.<br>A Central de Segurança do Azure compara continuamente a configuração de seus recursos com os requisitos de padrões, regulamentos e parâmetros de comparação do setor. Quando você configurar os "pacotes de conformidade" relevantes (padrões e linhas de base) que são importantes para sua organização, quaisquer lacunas resultarão em recomendações de segurança que incluem o CCEID e uma explicação do possível impacto na segurança.<br>Os pacotes comumente usados são <a href="/azure/security/benchmarks/introduction">Azure Security Benchmark</a> e <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations Benchmark versão 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- As vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas<br>- As vulnerabilidades na configuração de segurança nas máquinas devem ser corrigidas<br>- As vulnerabilidades da configuração de segurança nos conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- O agente de Log Analytics deve ser instalado em suas máquinas de arco do Azure baseadas no Windows (versão prévia)<br>- O agente de Log Analytics deve ser instalado em seus computadores de Arc do Azure baseados em Linux (versão prévia)<br>- O agente de monitoramento deve ser instalado nos conjuntos de dimensionamento de máquinas virtuais<br>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>- Substituir ou desabilitar o perfil AppArmor de contêineres deve ser restrito (versão prévia)<br>- Azure Policy complemento para kubernetes deve ser instalado e habilitado em seus clusters (visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Restringir o acesso não autorizado à rede (pontuação máxima de 4)</p></strong>Os pontos de extremidade em uma organização fornecem uma conexão direta de sua rede virtual com os serviços do Azure permitidos. As máquinas virtuais em uma sub-rede podem se comunicar com todos os recursos. Para limitar a comunicação de e para recursos em uma sub-rede, crie um grupo de segurança de rede e associe-o à sub-rede. As organizações podem limitar e proteger contra tráfego não autorizado criando regras de entrada e saída.</td>
    <td class="tg-lboi"; width=55%>- O encaminhamento de IP na máquina virtual deve ser desabilitado<br>- Os intervalos de IP autorizados devem ser definidos nos Serviços do Kubernetes (Versão Prévia)<br>- (PRETERIDO) O acesso aos Serviços de Aplicativos deve ser restrito (Versão Prévia)<br>- (PRETERIDO) As regras de aplicativos Web nos NSGs de IaaS devem ser fortalecidas<br>- Máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>- O CORS não deve permitir que todos os recursos tenham acesso ao seu Aplicativo de API<br>- O CORS não deve permitir que todos os recursos acessem seu Aplicativo de Funções<br>- O CORS não deve permitir que todos os recursos acessem seu Aplicativo Web<br>- A depuração remota deve ser desativada para o Aplicativo de API<br>- A depuração remota deve ser desativada para o Aplicativo de Funções<br>- A depuração remota deve ser desativada para o Aplicativo Web<br>- O acesso deve ser restrito para os Grupos de Segurança de Rede permissivos com VMs para a Internet<br>- As Regras do Grupo de Segurança de Rede nas máquinas virtuais para a Internet devem ser protegidas<br>- Azure Policy complemento para kubernetes deve ser instalado e habilitado em seus clusters (visualização)<br>- Os contêineres devem escutar somente em portas permitidas (versão prévia)<br>- Os serviços devem escutar somente em portas permitidas (versão prévia)<br>- O uso de rede e portas do host deve ser restrito (versão prévia)<br>- As redes virtuais devem ser protegidas pelo firewall do Azure (versão prévia)<br>- O ponto de extremidade privado deve ser habilitado para servidores MariaDB<br>- O ponto de extremidade privado deve ser habilitado para servidores MySQL<br>- O ponto de extremidade privado deve ser habilitado para servidores PostgreSQL</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar controle de aplicativo adaptável (pontuação máxima de 3)</p></strong>O AAC (controle de aplicativo adaptável) é uma solução inteligente, automatizada e de ponta a ponta, que permite controlar quais aplicativos podem ser executados em seus computadores Azure e não Azure. Ele também ajuda a proteger seus computadores contra malware.<br>A central de segurança usa o aprendizado de máquina para criar uma lista de aplicativos conhecidos confiáveis para um grupo de computadores.<br>Essa abordagem inovadora para a listagem de aplicativos aprovados fornece os benefícios de segurança sem a complexidade do gerenciamento.<br>O AAC é particularmente relevante para servidores com finalidade específica que precisam executar um conjunto específico de aplicativos.</td>
    <td class="tg-lboi"; width=55%>- Os Controles de Aplicativos Adaptáveis devem ser habilitados em máquinas virtuais<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- O agente de Log Analytics deve ser instalado em suas máquinas de arco do Azure baseadas no Windows (versão prévia)<br>- O agente de Log Analytics deve ser instalado em seus computadores de Arc do Azure baseados em Linux (versão prévia)<br>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar classificação de dados (pontuação máxima de 2)</p></strong>Classificar os dados da sua organização por sensibilidade e impacto nos negócios permite que você determine e atribua valor aos dados e fornece a estratégia e a base para governança.<br>A <a href="/azure/information-protection/what-is-information-protection">Proteção de Informações do Azure</a> pode ajudar na classificação de dados. Ela usa políticas de criptografia, identidade e autorização para proteger dados e restringir o acesso a dados. Algumas classificações usadas pela Microsoft são não comerciais, públicas, gerais, confidenciais e altamente confidenciais.</td>
    <td class="tg-lboi"; width=55%>- Dados confidenciais em seus bancos de dados SQL devem ser classificados (versão prévia)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Proteger aplicativos contra ataques de DDoS (pontuação máxima de 2)</p></strong>Ataques de DDoS (negação de serviço distribuído) sobrecarregam recursos e renderizam aplicativos inutilizáveis. Use o <a href="/azure/virtual-network/ddos-protection-overview">Padrão de Proteção contra DDoS do Azure</a> para defender sua organização dos três tipos principais de ataques de DDoS:<br>Os - <strong>ataques volumétricos</strong> inundam a rede com tráfego legítimo. O Padrão da Proteção contra DDoS atenua esses ataques, absorvendo-os ou depurando-os automaticamente.<br>- <strong>Ataques de protocolo</strong> renderizam um destino inacessível, explorando uma vulnerabilidade na pilha de protocolos das camadas 3 e 4. O Padrão da Proteção contra DDoS atenua esses ataques bloqueando o tráfego mal-intencionado.<br>Os - <strong>ataques de camada de recurso (aplicativo)</strong> apontam para os pacotes de aplicativos Web. Proteja-se contra esse tipo com um firewall do aplicativo Web e o Padrão da Proteção contra DDoS.</td>
    <td class="tg-lboi"; width=55%>- O Padrão da Proteção contra DDoS deve ser habilitada<br>- Limites de memória e CPU do contêiner devem ser aplicados (visualização)<br>- Azure Policy complemento para kubernetes deve ser instalado e habilitado em seus clusters (visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar a proteção do ponto de extremidade (pontuação máxima de 2)</p></strong>Para garantir que seus pontos de extremidade sejam protegidos contra malware, os sensores comportamentais coletam e processam dados dos sistemas operacionais de seus pontos de extremidade e enviam esses dados para a nuvem privada para análise. A análise de segurança utiliza big data, aprendizado de máquina e outras fontes para recomendar respostas a ameaças. Por exemplo, o <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> usa a inteligência contra ameaças para identificar métodos de ataque e gerar alertas de segurança.<br>A Central de Segurança dá suporte às seguintes soluções de proteção de ponto de extremidade: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, McAfee V10 para Windows, McAfee V10 para Linux e Sophos v9 para Linux. Se a Central de Segurança detectar qualquer uma dessas soluções, a recomendação para instalar a proteção de ponto de extremidade não será mais exibida.</td>
    <td class="tg-lboi"; width=55%>- As falhas de integridade da proteção de ponto de extremidade devem ser corrigidas nos conjuntos de dimensionamento de máquinas virtuais<br>- Os problemas de integridade da proteção de ponto de extremidade devem ser resolvidos nos seus computadores<br>- A solução de proteção de ponto de extremidade deve ser instalada nos conjuntos de dimensionamento de máquinas virtuais<br>- Instalar a solução de proteção de ponto de extremidade em máquinas virtuais<br>- Os problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>- O agente de monitoramento deve ser instalado nos conjuntos de dimensionamento de máquinas virtuais<br>- O agente de monitoramento deve ser instalado em seus computadores<br>- O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>- O agente de Log Analytics deve ser instalado em suas máquinas de arco do Azure baseadas no Windows (versão prévia)<br>- O agente de Log Analytics deve ser instalado em seus computadores de Arc do Azure baseados em Linux (versão prévia)<br>- Instale a solução de proteção de ponto de extremidade em suas máquinas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar auditoria e registro em log (pontuação máxima de 1)</p></strong>Os dados de registro em log fornecem informações sobre problemas anteriores, evitam possíveis, podem melhorar o desempenho do aplicativo e fornecem a capacidade de automatizar ações que, caso contrário, seriam manuais.<br>Os - <strong>logs de controle e gerenciamento</strong> fornecem informações sobre as operações do <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a>.<br>Os - <strong>logs de plano de dados</strong> fornecem informações sobre eventos gerados como parte de uso de recursos do Azure.<br>Os - <strong>eventos processados</strong> fornecem informações sobre os eventos/alertas analisados que foram processados.</td>
    <td class="tg-lboi"; width=55%>- A auditoria no SQL Server deve ser habilitada<br>- Os logs de diagnóstico nos Serviços de Aplicativos devem ser habilitados<br>- Os logs de diagnóstico no Azure Data Lake Store devem ser habilitados<br>- Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados<br>- Os logs de diagnóstico em contas do Lote devem ser habilitados<br>- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados<br>- Os logs de diagnóstico no Hub de Eventos devem ser habilitados<br>- Os logs de diagnóstico no Hub IoT devem ser habilitados<br>- Os logs de diagnóstico no Key Vault devem estar habilitados<br>- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados<br>- Os logs de diagnóstico no serviço Pesquisa devem ser habilitados<br>- Os logs de diagnóstico no Barramento de Serviço devem ser habilitados<br>- Os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados<br>- As regras de alerta de métrica devem ser configuradas em contas do Lote<br>- As configurações de Auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas<br>- Os servidores SQL devem ser configurados com um período de retenção de auditoria superior a 90 dias.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Habilitar proteção avançada contra ameaças (máximo de Pontuação 0)</p></strong>Os planos opcionais de proteção contra ameaças do Azure defender da central de segurança do Azure fornecem defesas abrangentes para seu ambiente. Quando a Central de Segurança detecta uma ameaça em qualquer área do ambiente, ela gera um alerta. Esses alertas descrevem os detalhes dos recursos afetados, as etapas de correção sugeridas e, em alguns casos, uma opção para disparar um aplicativo lógico em resposta.<br>Cada plano do Azure defender é uma oferta separada e opcional que você pode habilitar usando a recomendação relevante nesse controle de segurança.<br><a href="/azure/security-center/threat-protection">Saiba mais sobre a proteção contra ameaças na central de segurança</a>.</td>
    <td class="tg-lboi"; width=55%>- A segurança de dados avançada deve ser habilitada nos servidores do banco de dados SQL do Azure<br>- A segurança de dados avançada deve ser habilitada em servidores SQL em computadores<br>- A proteção avançada contra ameaças deve ser habilitada em máquinas virtuais<br>- A proteção avançada contra ameaças deve ser habilitada em planos de serviço Azure App<br>- A proteção avançada contra ameaças deve ser habilitada nas contas de armazenamento do Azure<br>- A proteção avançada contra ameaças deve ser habilitada nos clusters do serviço kubernetes do Azure<br>- A proteção avançada contra ameaças deve ser habilitada nos registros do registro de contêiner do Azure<br>- A proteção avançada contra ameaças deve ser habilitada em cofres Azure Key Vault</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementar melhores práticas de segurança (pontuação máxima de 0)</p></strong>Práticas de segurança modernas "assumem a violação" do perímetro da rede. Por esse motivo, muitas das melhores práticas neste controle se concentram no gerenciamento de identidades.<br>Perder chaves e credenciais é um problema comum. O <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> protege chaves e segredos criptografando chaves, arquivos. pfx e senhas.<br>As VPNs (redes virtuais privadas) são uma maneira segura de acessar suas máquinas virtuais. Se as VPNs não estiverem disponíveis, use frases secretas complexas e autenticação de dois fatores, como a <a href="/azure/active-directory/authentication/concept-mfa-howitworks">autenticação multifator do Azure ad</a>. A autenticação de dois fatores evita os pontos fracos inerentes apenas a nomes de usuário e senhas.<br>O uso de plataformas de autorização e autenticação forte é outra melhor prática. O uso de identidades federadas permite que as organizações deleguem o gerenciamento de identidades autorizadas. Isso também é importante quando os funcionários são demitidos e o acesso precisa ser revogado.</td>
    <td class="tg-lboi"; width=55%>- Um máximo de três proprietários deve ser designado para sua assinatura<br>- As contas externas com permissões de leitura devem ser removidas de sua assinatura<br>- O MFA deve ser habilitado em contas com permissões de leitura em sua assinatura<br>- O acesso às contas de armazenamento com configurações de firewall e de rede virtual deve ser restrito<br>- Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do Hub de Eventos<br>- Um administrador do Azure Active Directory deve ser provisionado para servidores SQL<br>- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas<br>- As regras de autorização na instância do Hub de Eventos devem ser definidas<br>- As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager<br>- As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager<br>- As sub-redes devem ser associadas a um Grupo de Segurança de Rede<br>- [Versão prévia] O Windows Exploit Guard deve estar habilitado <br>- [Versão prévia] O agente de configuração do convidado deve ser instalado<br>- Máquinas virtuais não voltadas para a Internet devem ser protegidas com grupos de segurança de rede<br>- O backup do Azure deve ser habilitado para máquinas virtuais<br>- O backup com redundância geográfica deve ser habilitado para o banco de dados do Azure para MariaDB<br>- O backup com redundância geográfica deve ser habilitado para o banco de dados do Azure para MySQL<br>- O backup com redundância geográfica deve ser habilitado para o banco de dados do Azure para PostgreSQL<br>- O PHP deve ser atualizado para a versão mais recente para seu aplicativo de API<br>- O PHP deve ser atualizado para a versão mais recente para seu aplicativo Web<br>- O Java deve ser atualizado para a versão mais recente para seu aplicativo de API<br>- O Java deve ser atualizado para a versão mais recente para seu aplicativo de funções<br>- O Java deve ser atualizado para a versão mais recente para seu aplicativo Web<br>- O Python deve ser atualizado para a versão mais recente para seu aplicativo de API<br>- O Python deve ser atualizado para a versão mais recente para seu aplicativo de funções<br>- O Python deve ser atualizado para a versão mais recente para seu aplicativo Web<br>- A retenção de auditoria para servidores SQL deve ser definida para pelo menos 90 dias</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Perguntas frequentes sobre pontuação segura

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações em um controle de segurança, minha pontuação de proteção será alterada?
Não. Ela não será alterada até que você corrija todas as recomendações para um único recurso. Para obter a pontuação máxima de um controle, corrija todas as recomendações de todos os recursos.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim e eu desabilitá-la na política, meu controle de segurança será atendido e minha pontuação segura será atualizada?
Sim. É recomendável desabilitar as recomendações quando elas não são aplicáveis em seu ambiente. Para obter instruções sobre como desabilitar uma recomendação específica, consulte [Desabilitar políticas de segurança](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Caso um controle de segurança me ofereça zero pontos em relação à minha pontuação segura, devo ignorá-lo?
Em alguns casos, você verá uma pontuação máxima de controle maior que zero, mas o impacto é zero. Quando a pontuação incremental para corrigir recursos é insignificante, ela é arredondada para zero. Não ignore essas recomendações, pois elas ainda trazem melhorias de segurança. A única exceção é o controle de "melhor prática adicional". A correção dessas recomendações não aumentará sua pontuação, mas vai aprimorar a segurança geral.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu a pontuação segura e os controles de segurança que ela apresenta. Para obter material relacionado, consulte os seguintes artigos:

- [Saiba mais sobre os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como corrigir recomendações](security-center-remediate-recommendations.md)
- [Exibir as ferramentas baseadas no GitHub para trabalhar de forma programática com Pontuação segura](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)