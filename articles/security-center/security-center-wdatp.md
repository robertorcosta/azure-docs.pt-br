---
title: Microsoft Defender Advanced Threat Protection - Azure Security Center
description: Este documento introduz a integração entre o Azure Security Center e o Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 5136a00421aebaa72998b1dfcdf75feb935851c6
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435448"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Proteção avançada contra ameaças do Microsoft Defender com o Azure Security Center

O Azure Security Center está ampliando sua oferta de plataformas de proteção de carga de trabalho na nuvem, integrando-se ao [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Essa alteração traz recursos abrangentes de EDR (detecção e resposta de ponto de extremidade). Com a integração do Microsoft Defender ATP, você pode detectar anormalidades. Você também pode detectar e responder a ataques avançados em pontos de extremidade de servidor monitorados pela Central de Segurança do Azure.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Recursos do Microsoft Defender ATP no Security Center

Quando você usa o Microsoft Defender ATP, você recebe:

- **Sensores avançados de detecção pós-violação**: os sensores Microsoft Defender ATP para servidores Windows coletam uma vasta gama de sinais comportamentais.

- **Detecção de violação de post**baseada em analytics e alimentada por nuvem : o Microsoft Defender ATP se adapta rapidamente às ameaças em mudança. Ele usa análise avançada e Big Data. O Microsoft Defender ATP é amplificado pelo poder do Gráfico de Segurança Inteligente com sinais no Windows, Azure e Office para detectar ameaças desconhecidas. Ele fornece alertas acionáveis e permite que você responda rapidamente.

- **Inteligência de ameaças**: O Microsoft Defender ATP gera alertas quando identifica ferramentas, técnicas e procedimentos do atacante. Ele usa dados gerados pelos caçadores de ameaças da Microsoft e pelas equipes de segurança, incrementados por inteligência oferecida por parceiros.

Os seguintes recursos estão agora disponíveis no Azure Security Center:

- **Onboarding automatizado**: O sensor MICROSOFT Defender ATP é ativado automaticamente para servidores Windows que estão a bordo do Azure Security Center.

- **Painel único de vidro**: O console Azure Security Center exibe alertas microsoft Defender ATP.

- **Investigação detalhada da máquina**: os clientes do Azure Security Center podem usar o console ATP do Microsoft Defender para realizar uma investigação detalhada para descobrir o escopo de uma violação.

![Central de Segurança do Azure, exibindo uma lista de alertas e informações gerais sobre cada alerta](media/security-center-wdatp/image1.png)

Para investigar mais, use o Microsoft Defender ATP. O Microsoft Defender ATP fornece informações adicionais, como a árvore do processo de alerta e o gráfico de incidentes. Você também pode ver uma linha do tempo detalhada do computador, mostrando cada comportamento por um período de histórico de até seis meses.

![Página do Microsoft Defender ATP com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte a plataforma

O Microsoft Defender ATP no Security Center suporta a detecção no Windows Server 2016, 2012 R2 e 2008 R2 SP1, para VMs Azure você precisa de uma assinatura de nível Padrão e para VMs não-Azure você precisa de nível Padrão apenas no nível de espaço de trabalho.

> [!NOTE]
> Quando você usa o Azure Security Center para monitorar servidores, um inquilino do Microsoft Defender ATP é criado automaticamente e os dados do Microsoft Defender ATP são armazenados na Europa por padrão. Se você precisar mover seus dados para outro local, você precisa entrar em contato com o Suporte da Microsoft para redefinir o inquilino. O monitoramento do ponto final do servidor utilizando essa integração foi desativado para clientes do Office 365 GCC.

## <a name="onboarding-servers-to-security-center"></a>Integração de servidores à Central de Segurança 

Para acessar servidores a bordo do Security Center, clique em **Ir ao Azure Security Center para acessar servidores** a bordo do servidor ATP do Microsoft Defender.

1. Na área **onboarding,** selecione ou crie um espaço de trabalho para armazenar os dados. <br>
2. Se você não conseguir ver todos os seus workspaces, pode ser devido à falta de permissões, verifique se que seu workspace está definido na camada Standard de Segurança do Azure. Para obter mais informações, consulte [Upgrade para o nível Padrão do Security Center para obter maior segurança](security-center-pricing.md).
    
3. Selecione **Adicionar servidores** para exibir instruções sobre como instalar o agente Log Analytics. 

4. Após a integração, monitore as máquinas **Computação e aplicativos**.

   ![Integrar computadores](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Habilite a integração do Microsoft Defender ATP

Para ver se a integração do Microsoft Defender ATP está ativada, selecione As**configurações de & de preços do** security **center** > > clique em sua assinatura.
Aqui você pode ver as integrações habilitadas no momento.

  ![Página de configurações de detecção de ameaças do Azure Security Center com integração do Microsoft Defender ATP ativada](media/security-center-wdatp/enable-integrations.png)

- Se você já integrou os servidores à camada Standard da Central de Segurança do Azure, nenhuma ação adicional é necessária. O Azure Security Center abordo automaticamente os servidores para o Microsoft Defender ATP. O embarque pode levar até 24 horas.

- Se você nunca integrou os servidores à camada Standard da Central de Segurança do Azure, integre-os à Central de Segurança do Azure como de costume.

- Se você tiver embarcado nos servidores através do Microsoft Defender ATP:
  - Veja a documentação para obter diretrizes sobre [como remover computadores de servidor](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Integre esses servidores à Central de Segurança do Azure.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Acesso ao portal Microsoft Defender ATP

Siga as instruções em [Atribuir acesso de usuário ao portal](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Definir a configuração do firewall

Se você tiver um proxy ou firewall que esteja bloqueando o tráfego anônimo, pois um sensor MICROSOFT Defender ATP está se conectando a partir do contexto do sistema, certifique-se de que o tráfego anônimo seja permitido. Siga as instruções em [Habilitar o acesso aos URLs](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)de serviço ATP do Microsoft Defender no servidor proxy .

## <a name="test-the-feature"></a>Testar o recurso

Para gerar um alerta de teste ATP do Microsoft Defender benigno:

1. Criar uma pasta 'C:\test-MDATP-test'.

1. Use a Área de Trabalho Remota para acessar uma VM do Windows Server 2012 R2 ou uma VM do Windows Server 2016. Abra uma janela de linha de comando.

1. No prompt, copie e execute o comando a seguir. A janela do prompt de comando será fechada automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Uma janela do prompt de comando com o comando acima](media/security-center-wdatp/image4.jpeg)

3. Se o comando for bem sucedido, você verá um novo alerta no painel do Azure Security Center e no portal microsoft defender ATP. Esse alerta pode levar alguns minutos para aparecer.

4. Para revisar o alerta no Security Center, vá para **alertas** > de segurança**suspeitos PowerShell CommandLine**.

5. Na janela de investigação, selecione o link para ir ao portal do Microsoft Defender ATP.

## <a name="next-steps"></a>Próximas etapas

- [Plataformas e recursos compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md)
- [Como configurar políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
