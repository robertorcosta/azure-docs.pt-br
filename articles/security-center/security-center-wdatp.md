---
title: Proteção avançada contra ameaças do Microsoft defender – central de segurança do Azure
description: Este documento apresenta a integração entre a central de segurança do Azure e a proteção avançada contra ameaças do Microsoft defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: 92feb159fe4c893a55d37fa90c34acf4c4c93631
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826178"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Proteção avançada contra ameaças do Microsoft defender com a central de segurança do Azure

A central de segurança do Azure integra-se à [ATP (proteção avançada contra ameaças) do Microsoft defender](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) para fornecer recursos abrangentes de EDR (detecção de ponto de extremidade e resposta).


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Preço:|Exige o [Azure Defender](security-center-pricing.md)|
|Computadores com suporte:|![Sim](./media/icons/yes-icon.png) Máquinas do Azure executando o Windows<br>![Sim](./media/icons/yes-icon.png) Computadores do Arc do Azure executando o Windows|
|Funções e permissões necessárias:|Para habilitar/desabilitar a integração: **administrador de segurança** ou **proprietário**<br>Para exibir alertas do MDATP na central de segurança: **leitor de segurança**, **leitor**, colaborador do **grupo de recursos**, proprietário do **grupo de recursos**, administrador de **segurança**, **proprietário da assinatura**ou colaborador da **assinatura**|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais.<br>![Não](./media/icons/no-icon.png) Clientes GCC executando cargas de trabalho em nuvens públicas do Azure<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) Governo da China e outros governos|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>Recursos do Microsoft defender ATP na central de segurança

O Microsoft defender ATP fornece:

- **Sensores avançados de detecção de pós-violação**: os sensores do Microsoft defender ATP para Windows Servers coletam uma vasta gama de sinais comportamentais.

- **Detecção de pós-violação com base em análise, baseada em nuvem**: o Microsoft defender ATP se adapta rapidamente às ameaças em constante mudança. Ele usa análise avançada e Big Data. O Microsoft defender ATP é amplificado pelo poder do Gráfico de Segurança Inteligente com sinais entre o Windows, o Azure e o Office para detectar ameaças desconhecidas. Ele fornece alertas acionáveis e permite que você responda rapidamente.

- **Inteligência contra ameaças**: o Microsoft defender ATP gera alertas ao identificar ferramentas, técnicas e procedimentos do invasor. Ele usa dados gerados pelos caçadores de ameaças da Microsoft e pelas equipes de segurança, incrementados por inteligência oferecida por parceiros.


Ao integrar o defender ATP à central de segurança do Azure, você também pode se beneficiar dos seguintes recursos adicionais:

- Integração **automatizada**: ela habilita automaticamente o sensor Microsoft defender ATP para servidores Windows que monitoram pela central de segurança do Azure (a menos que estejam executando o Windows Server 2019).

- **Painel único de vidro**: o console da central de segurança do Azure exibe alertas do Microsoft defender ATP. Para investigar ainda mais, use o Microsoft defender ATP. O Microsoft defender ATP fornece informações adicionais, como a árvore de processo de alerta e o grafo de incidentes. Você também pode ver uma linha do tempo detalhada do computador, mostrando cada comportamento por um período de histórico de até seis meses.

    ![Página do Microsoft defender ATP com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte a plataforma

O Microsoft defender ATP na central de segurança dá suporte à detecção no Windows Server 2016, 2012 R2 e 2008 R2 SP1. Para VMs do Azure, você precisa de um para habilitar o Azure defender em sua assinatura e para VMs não Azure, você precisa que o Azure defender seja habilitado somente no nível do espaço de trabalho.

O monitoramento do ponto de extremidade do servidor usando essa integração foi desabilitado para clientes do Office 365 GCC.

## <a name="data-storage-location"></a>Local de armazenamento de dados

Quando você usa a central de segurança do Azure para monitorar servidores, um locatário do Microsoft defender ATP é criado automaticamente. Os dados coletados pelo Microsoft defender ATP são armazenados na localização geográfica do locatário, conforme identificado durante o provisionamento. Os dados do cliente no formulário pseudônimos também podem ser armazenados no armazenamento central e nos sistemas de processamento no Estados Unidos. 

Após configurar, você não pode alterar o local onde os dados são armazenados. Se você precisar mover seus dados para outro local, entre em contato com Suporte da Microsoft para redefinir o locatário.


## <a name="onboard-servers-to-security-center"></a>Integrar servidores à central de segurança 

Para integrar servidores à central de segurança, clique em **ir para a central de segurança do Azure para integrar servidores** da integração do servidor do Microsoft defender ATP.

1. Na área de **integração** , selecione ou crie um espaço de trabalho no qual armazenar os dados.

2. Se você não conseguir ver todos os seus espaços de trabalho, pode ser devido à falta de permissões, verifique se o espaço de trabalho está protegido pelo Azure defender.
    
3. Selecione **adicionar servidores** para exibir instruções sobre como instalar o agente de log Analytics. 

4. Após a integração, você pode monitorar os computadores no [inventário de ativos](asset-inventory.md).

   ![Integrar computadores](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Habilitar a integração do Microsoft defender ATP

Para exibir se a integração do Microsoft defender ATP está habilitada, selecione preços da **central de segurança**  >  **& configurações** > selecione sua assinatura.

Aqui você pode ver as integrações habilitadas no momento.

  ![Página de configurações de detecção de ameaças da central de segurança do Azure com integração do Microsoft defender ATP habilitada](media/security-center-wdatp/enable-integrations.png)

- Se você já tiver habilitado o Azure defender, nenhuma ação adicional será necessária. A central de segurança do Azure integrará automaticamente os servidores ao Microsoft defender ATP. A integração pode levar até 24 horas.

- Se você nunca tiver integrado os servidores à central de segurança do Azure, integre-os à central de segurança do Azure e habilite o Azure defender como de costume.

- Se você tiver integrado os servidores por meio do Microsoft defender ATP:
  - Veja a documentação para obter diretrizes sobre [como remover computadores de servidor](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Integre esses servidores à Central de Segurança do Azure.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Acesso ao portal do Microsoft defender ATP

1. Siga as instruções em [Atribuir acesso de usuário ao portal](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Verifique se você tem um proxy ou firewall que está bloqueando o tráfego anônimo. O sensor do defender ATP se conecta do contexto do sistema; portanto, o tráfego anônimo deve ser permitido. Para garantir o acesso sem impedimento ao portal do Microsoft defender ATP, siga as instruções em [habilitar o acesso às URLs de serviço do Microsoft defender ATP no servidor proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testar o recurso

Para gerar um alerta de teste benigno do Microsoft defender ATP:

1. Crie uma pasta ' C:\test-MDATP-test '.

1. Use a Área de Trabalho Remota para acessar uma VM do Windows Server 2012 R2 ou uma VM do Windows Server 2016. Abra uma janela de linha de comando.

1. No prompt, copie e execute o comando a seguir. A janela do prompt de comando será fechada automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Uma janela do prompt de comando com o comando acima](media/security-center-wdatp/image4.jpeg)

1. Se o comando for bem-sucedido, você verá um novo alerta no painel da central de segurança do Azure e no portal do Microsoft defender ATP. Esse alerta pode levar alguns minutos para aparecer.

1. Para examinar o alerta na central de segurança, acesse **alertas de segurança aviso**de  >  **linha de comando do PowerShell suspeito**.

1. Na janela investigação, selecione o link para acessar o portal do Microsoft defender ATP.

## <a name="next-steps"></a>Próximas etapas

- [Plataformas e recursos compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md)
- [Como configurar políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.