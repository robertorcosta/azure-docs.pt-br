---
title: 'Início Rápido: Integração no Azure Sentinel'
description: Neste guia de início rápido, saiba como integrar o Azure Sentinel habilitando primeiro o Sentinel e conectando fontes de dados.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 3efd0303b447242c0229bc71720e55c8aa3a2f2d
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566243"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Início Rápido: Integração do Azure Sentinel

Neste início rápido, saiba como integrar o Azure Sentinel. 

Para a integração do Azure Sentinel, primeiro você precisa habilitar o Azure Sentinel e depois conectar suas fontes de dados. O Azure Sentinel é fornecido com uma série de conectores para soluções da Microsoft, disponíveis prontas para uso e fornecendo integração em tempo real, incluindo soluções de Proteção contra Ameaças da Microsoft, fontes do Microsoft 365 (incluindo o Office 365), o Azure AD, o ATP do Azure, o Microsoft Cloud App Security, entre outros. Além disso, existem conectores internos no ecossistema de segurança mais amplo para soluções que não são da Microsoft. Use também o CEF (Formato Comum de Evento), o Syslog ou a API REST para conectar suas fontes de dados ao Azure Sentinel.  

Depois de conectar suas fontes de dados, escolha em uma galeria de pastas de trabalho criadas por especialistas que mostram insights com base em seus dados. Essas pastas de trabalho podem ser facilmente personalizadas de acordo com suas necessidades.

>[!IMPORTANT] 
> Para obter informações sobre os encargos incorridos ao usar o Azure Sentinel, confira [preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Pré-requisitos globais

- Assinatura ativa do Azure, se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Workspace do Log Analytics. Saiba como [criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Para saber mais sobre workspaces do Log Analytics, confira [Criar sua implantação de logs do Azure Monitor](../azure-monitor/platform/design-logs-deployment.md).

- Para habilitar o Azure Sentinel, você precisa de permissões de colaborador na assinatura na qual reside o workspace do Azure Sentinel. 
- Para usar o Azure Sentinel, você precisa de permissões de colaborador ou leitor no grupo de recursos ao qual o workspace pertence.
- Talvez você precise de permissões adicionais para conectar fontes de dados específicas.
- O Azure Sentinel é um serviço pago. Para saber mais sobre preços, confira [Sobre o Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Habilitar o Azure Sentinel <a name="enable"></a>

1. Entre no portal do Azure. Verifique se a assinatura na qual o Azure Sentinel foi criado está selecionada.

1. Pesquise e selecione o **Azure Sentinel**.

   ![pequisa](./media/quickstart-onboard/search-product.png)

1. Selecione **Adicionar**.

1. Selecione o workspace que você quer usar ou crie um novo. Você pode executar o Azure Sentinel em mais de um workspace, mas os dados são isolados em um único workspace.

   ![pequisa](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Os workspaces padrão criados pela Central de Segurança do Azure não serão exibidos na lista. Você não pode instalar o Azure Sentinel neles.
   > - O Azure Sentinel pode ser executado em workspaces em qualquer [região de GA do Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), exceto nas regiões China e Alemanha (soberanas). Os dados gerados pelo Azure Sentinel (como incidentes, indicadores e regras de alerta, que podem conter alguns dados do cliente provenientes desses workspaces) são salvos na Europa (para workspaces localizados na Europa), na Austrália (para workspaces localizados na Austrália) ou no Leste dos EUA (para os workspaces localizados em qualquer outra região).

   >[!IMPORTANT]
   >
   > - Após a implantação em um workspace, atualmente o Azure Sentinel **não dá suporte** para mover o workspace para outros grupos de recursos ou assinaturas. 
   >
   >   Se você já tiver movido o workspace, desabilite todas as regras ativas em **Análise** e habilite-as novamente após cinco minutos. Isso deve ser eficaz na maioria dos casos, porém, para reiterar, não há suporte para esse procedimento e ele traz riscos.

1. Selecione **Adicionar o Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Conectar fontes de dados

O Azure Sentinel cria a conexão com serviços e aplicativos, conectando-se ao serviço e encaminhando os eventos e logs ao Azure Sentinel. Para computadores e máquinas virtuais, você pode instalar o agente do Azure Sentinel que coleta os logs e os encaminha para o Azure Sentinel. Para firewalls e proxies, o Azure Sentinel utiliza um servidor Syslog Linux. O agente é instalado nele e coleta os arquivos de log e os encaminha para o Azure Sentinel. 
 
1. Clique em **Conectores de dados**.
1. Há um bloco para cada fonte de dados a qual você pode se conectar.<br>
Por exemplo, clique em **Azure Active Directory**. Se você conectar essa fonte de dados, transmitirá todos os logs do Azure AD para o Azure Sentinel. É possível selecionar quais tipos de log você quer obter: logs de entrada e/ou logs de auditoria. <br>
Na parte inferior, o Azure Sentinel fornece recomendações das pastas de trabalho que você deve instalar para cada conector, assim você obtém imediatamente informações interessantes sobre seus dados. <br> Siga as instruções de instalação ou [consulte o guia de conexão relevante](connect-data-sources.md) para saber mais. Para saber mais sobre conectores de dados, confira [Conectar serviços da Microsoft](connect-data-sources.md).

Após a conexão das fontes de dados, seus dados começarão a ser transmitidos para o Azure Sentinel e estarão prontos para você começar a trabalhar com eles. Você pode exibir os logs nos [dashboards internos](quickstart-get-visibility.md) e começar a criar consultas no Log Analytics para [investigar os dados](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar fontes de dados ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- Transmita dados de [dispositivos com Formato Comum de Evento](connect-common-event-format.md) para o Azure Sentinel.
