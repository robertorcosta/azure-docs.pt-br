---
title: Faturas do Registro Enterprise do Azure
description: Este artigo explica como gerenciar e usar sua fatura do Azure Enterprise.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/04/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 2a75972d00733d23df7f4a59b2d5010404df8dc3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023388"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faturas do Registro Enterprise do Azure

Este artigo explica como gerenciar e usar sua fatura do Azure EA (Contrato Enterprise do Azure). A fatura é uma representação de sua conta. Examine sua precisão. Conheça outras tarefas que podem ser necessárias para gerenciar sua fatura.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Alterar o número da OC de uma fatura de excedente

O Azure Enterprise Portal gera automaticamente uma OC (ordem de compra) padrão, a menos que o administrador corporativo configure uma antes da data da fatura. Um administrador corporativo pode atualizar o número da OC até sete dias após receber um email de notificação automático com a fatura.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Para atualizar o número da ordem de compra dos serviços do Azure:

1. No Azure Enterprise Portal, selecione **Relatório** > **Resumo do Uso**.
1. Selecione **Editar números da OC** no canto superior direito.
1. Selecione o botão de opção **Serviços do Azure**.
1. Selecione um **Período da fatura** no menu suspenso com intervalos de datas.

   É possível editar um número de OC durante um período de sete dias depois de obter uma notificação de fatura, mas antes de pagar a fatura.
1. Insira o novo número da OC no campo **Número da OC** .
1. Selecione **Salvar** para enviar sua alteração.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Para atualizar o número da ordem de compra do Azure Marketplace:

1. No Azure Enterprise Portal, selecione **Relatório** > **Resumo do Uso**.
1. Selecione **Editar números da OC** no canto superior direito.
1. Selecione o botão de opção do **Marketplace**.
1. Selecione um **Período da fatura** no menu suspenso com intervalos de datas.

   É possível editar um número de OC durante um período de sete dias depois de obter uma notificação de fatura, mas antes de pagar a fatura.
1. Insira o novo número da OC no campo **Número da OC** .
1. Selecione **Salvar** para enviar sua alteração.

## <a name="cadence-of-azure-enterprise-billing"></a>Cadência da cobrança do Azure Enterprise

A Microsoft cobra uma vez ao ano na data de início de vigência do registro para qualquer compra de compromisso de serviços do Microsoft Azure. Para qualquer uso que exceda os valores do compromisso, a Microsoft cobra pelos débitos anteriores.

- Valores de compromisso são cotados com base em uma taxa mensal e cobradas anualmente com antecedência.
- Valores excedentes são calculados mensalmente e cobrados posteriormente, no final do período de cobrança.

### <a name="billing-intervals"></a>Intervalos de cobrança

O intervalo de cobrança depende de como você opta por fazer suas compras de compromisso. Seu compromisso anual é coextensivo com:

- A data de aniversário do registro
- A data de início de vigência da sua assinatura de aditamento de um ano.

A data em que você recebe a fatura excedente depende da data de início e da configuração do registro:

- **Registros diretos com uma data de início anterior a 1º de maio de 2018**:
  - Se você estiver em um EA (Contrato Enterprise) direto, o ciclo de cobrança será anual para os serviços do Azure, exceto serviços do Azure Marketplace. Seu ciclo de cobrança é baseado na data de aniversário: a data em que seu contrato se tornou efetivo.
  - Se ultrapassar 150% de seu limite de MC (compromisso monetário) do EA, você será automaticamente convertido em um ciclo de cobrança trimestral com base em sua data de aniversário. Você também receberá uma fatura de excedente do serviço do Azure.
  - Se você não ultrapassar 150% do seu limite de MC, seu registro permanecerá em um ciclo de cobrança anual. A fatura de excedente será recebida no final do ano de compromisso.

- **Registros diretos com uma data de início posterior a 1º de maio de 2018**:
  - As faturas de consumo e cobranças do Azure cobradas separadamente estão em um ciclo de cobrança mensal.
  - As cobranças não cobertas pelo seu compromisso monetário são devidas a um pagamento de excedente.  

- **Registros indiretos com um registro iniciado antes de 1º de maio de 2018**:
  
  Se você for um cliente indireto de EA (Contrato Enterprise) com uma data de início antes de 1º de maio de 2018, estará configurado em um ciclo de cobrança trimestral. O CP (parceiro de canal) fatura você diretamente.  

- **Registros indiretos com uma data de início posterior a 1º de maio de 2018**:
  
  Você está em um ciclo de cobrança mensal.  

### <a name="increase-your-monetary-commitment"></a>Aumentar seu compromisso monetário

É possível aumentar seu compromisso a qualquer momento. A cobrança será realizada pelo número de meses restantes no período de compromisso deste ano. Por exemplo, se você se inscrever para uma assinatura de aditamento de um ano e, depois, aumentar seu compromisso durante o mês seis, será faturado pelos seis meses restantes desse período. Suas quantidades de compromisso serão atualizadas para os últimos seis meses de seu período de compromisso. Essas novas quantidades serão usadas para determinar cobranças excedentes.

### <a name="overage"></a>Excedente

No caso de excedente, você será cobrado pelo uso ou pelas reservas que excederam seu compromisso durante o período de cobrança. Para exibir um detalhamento de como as quantidades excedentes de itens individuais foram calculadas, confira o relatório de resumo do uso ou fale com o parceiro de canal.

Para cada item na fatura, você verá:

- **Valor ampliado**: o total de cobranças
- **Uso do compromisso**: a quantidade de seu compromisso usada para cobrir os encargos
- **Valor líquido**: as cobranças que excedem seu compromisso

Os tributos incidentes são computados somente no valor líquido que excede o compromisso.

O faturamento excedente é automatizado. O cronograma das notificações e das faturas depende da data de término do seu período de cobrança.

- A notificação de excedente geralmente é enviada sete dias após a data de término da cobrança.
- As faturas de excedentes são enviadas de sete a nove dias após a notificação.
- Você pode examinar as cobranças e atualizar os números de OC gerados pelo sistema durante os sete dias entre a notificação de excedente e o faturamento.

### <a name="azure-marketplace"></a>Azure Marketplace

A partir do ciclo de cobrança de abril de 2019, os clientes começaram a receber uma fatura do Azure que combina todas as cobranças do Azure e do Azure Marketplace em uma só fatura, ao invés de duas faturas separadas. Essa alteração não afeta os clientes na Austrália, no Japão ou em Singapura.

Durante a transição para uma fatura combinada, você receberá uma fatura parcial do Azure Marketplace. Essa última fatura separada mostrará as cobranças do Azure Marketplace antes da data da sua atualização de cobrança. As cobranças do Azure Marketplace incorridas após essa data serão exibidas em sua fatura do Azure. Após o período de transição, você verá todas as cobranças do Azure e do Azure Marketplace na fatura combinada.  

## <a name="adjust-billing-frequency"></a>Alterar a frequência da cobrança

A frequência da cobrança do cliente pode ser anual, trimestral ou mensal. O período de cobrança é determinado quando o cliente assina o contrato. A cobrança mensal é o menor intervalo de cobrança.

- É necessária a **aprovação** do administrador corporativo para alterar o ciclo de cobrança de anual para trimestral em registros diretos. É necessária a aprovação do administrador de parceiro para registros indiretos. A alteração entra em vigor no fim do trimestre de cobrança atual.
- É necessário acrescentar **um aditamento** ao contrato para alterar um ciclo de cobrança anual ou trimestral para mensal.  Qualquer alteração no ciclo de cobrança do registro existente exige aprovação de um administrador corporativo ou do "Contato para Cobrança".
- **Envie** sua aprovação para o [suporte do Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Selecione a categoria do problema: **Cobrança e faturamento**.

A alteração entra em vigor no fim do trimestre de cobrança atual.

Se um Aditamento M503 for assinado, você poderá alterar qualquer contrato de qualquer frequência para a cobrança mensal. 

## <a name="credits-and-adjustments"></a>Créditos e ajustes

É possível exibir todos os créditos ou ajustes aplicados ao seu registro na seção **Relatórios** do [Azure Enterprise Portal](https://ea.azure.com).

Para exibir créditos:

1. No [Azure Enterprise Portal](https://ea.azure.com), selecione a seção **Relatórios**.
1. Selecione **Resumo do Uso**.
1. No canto superior direito, altere a exibição **M** para **C**.
1. Aumente o campo de ajuste na tabela de compromisso do serviço do Azure.
1. Você verá os créditos aplicados ao seu registro e uma breve explicação. Por exemplo:  Crédito do Contrato de Nível de Serviço.

## <a name="request-an-invoice-copy"></a>Solicitar uma cópia da fatura

Para solicitar uma cópia da fatura, fale com seu parceiro.

## <a name="overage-offset"></a>Compensação de excedente

Para aplicar seu compromisso monetário a excedentes, você precisa atender aos seguintes critérios:

- Ter incorrido cobranças de excedente que não foram pagas e estão no prazo de um ano da data de término do serviço cobrado.
- O valor do compromisso monetário disponível cobre o valor total das cobranças incorridas, incluindo todas as faturas anteriores do Azure não pagas.
- O termo de cobrança que você deseja concluir precisa estar totalmente encerrado. A cobrança se encerra totalmente após o quinto dia de cada mês.
- O período de cobrança que você deseja compensar precisa estar totalmente encerrado.
- O ACD (Desconto de Compromisso com o Azure) se baseia no novo compromisso real menos os fundos planejados para consumo anterior. Este requisito se aplica somente às cobranças de excedente incorridas. Isso só é válido para serviços que consomem compromisso monetário, então não é aplicável a cobranças do Azure Marketplace. As cobranças do Azure Marketplace são feitas separadamente.

Para concluir uma compensação de excedente, você ou a equipe de conta pode abrir uma solicitação de suporte. É necessária uma aprovação por email do administrador corporativo ou do Contato para Cobrança.

## <a name="view-price-sheet-information"></a>Exibir informações sobre a tabela de preços

Administradores corporativos podem ver a tabela de preços associada ao registro nos serviços do Azure.

Para exibir a tabela de preços atual:

1. No Azure Enterprise Portal, selecione **Relatórios** e, em seguida, selecione **Tabela de Preços**.
2. Veja a tabela de preços ou selecione **Baixar**.

![Exemplo mostrando as informações da tabela de preços](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Para baixar o histórico da tabela de preços:

1. No Azure Enterprise Portal, selecione **Relatórios** e, em seguida, selecione **Baixar Uso**.
2. Baixe a tabela de preços.

![Exemplo mostrando onde baixar uma tabela de preços mais antiga](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Alguns motivos para as diferenças de preço:

- O preço pode ter sido alterado entre o registro anterior e o novo. Alterações de preço podem ocorrer porque o preço é definido por contrato para um registro específico da data de início à data de término de um contrato.
- Quando você transfere para o novo registro, o preço muda para o novo contrato. O preço é definido por sua tabela de preços, que pode ser maior no novo registro.
- Se um registro tiver o prazo aumentado, o preço também será alterado. Os preços mudam de acordo com as taxas pagas conforme o uso.

## <a name="request-detailed-usage-information"></a>Solicitar informações detalhadas sobre o uso

Os administradores corporativos podem exibir um resumo dos dados de uso, do compromisso monetário consumido e das cobranças associadas ao uso adicional no Azure Enterprise Portal. As cobranças são apresentadas no nível do resumo em todas as contas e assinaturas.

Para exibir o uso detalhado de contas específicas, baixe o relatório de detalhes de uso navegando até **Relatórios** > **Baixar Uso**.

> [!NOTE]
> O relatório de detalhe de uso não inclui as tributos incidentes. Talvez haja uma latência de até oito horas do momento em que o uso foi incorrido até ser refletido no relatório.

Para registros indiretos, seu parceiro precisa habilitar a função de marcação antes de você poder ver quaisquer informações relacionadas ao custo.

## <a name="reports"></a>Relatórios

Os administradores corporativos podem exibir um resumo dos dados de uso, do compromisso monetário consumido e das cobranças associadas ao uso adicional no Azure Enterprise Portal. As cobranças são apresentadas no nível do resumo em todas as contas e assinaturas.

### <a name="azure-enterprise-reports"></a>Relatórios do Azure Enterprise

- Resumo e gráficos de uso
- Relatório de uso do serviço
- Relatório de saldo e cobrança
- Relatório de detalhes de uso
- Relatório de cobranças do Azure Marketplace
- Tabela de preços
- Download do relatório avançado
- Formatação do relatório em CSV

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Para exibir os relatórios e os grafos de resumo de uso:

1. Vá para o Azure Enterprise Portal.
1. Selecione **Relatórios** no painel esquerdo.
1. Selecione a guia **Resumo do Uso**.
1. Selecione o terno do compromisso no menu de intervalos de datas na parte superior esquerda.
1. Selecione o período ou mês no grafo para exibir detalhes adicionais.
1. Nessa guia, é possível:
   - Exibir o grafo de uso de cada mês com um detalhamento do uso, das cobranças adicionais de serviço, do que foi cobrado separadamente e das cobranças do Azure Marketplace.
   - Filtrar por departamentos, contas e assinaturas abaixo do grafo.
   - Alternar entre o detalhamento **Cobrar por serviços** e o detalhamento **Cobrar por hierarquia**.
   - Exibir os detalhes dos serviços do Azure, do que foi cobrado separadamente e das cobranças do Azure Marketplace.

## <a name="service-usage-report"></a>Relatório de uso do serviço

A página de relatório de utilização do serviço permite que os administradores corporativos exibam um resumo dos dados de utilização do serviço. O uso é apresentada no nível do resumo em todas as contas e assinaturas. Para exibir o uso detalhado, filtre o relatório por contas ou assinaturas.

> [!NOTE]
> Pode haver uma latência de até cinco dias entre a data de uso incorrida e quando esse uso é refletido neste relatório.

### <a name="to-view-the-report"></a>Para exibir o relatório:

1. Entre no Azure Enterprise Portal.
1. Selecione **Relatórios** na área de navegação à esquerda.
1. Selecione a guia **Resumo do Uso**.
1. Selecione o intervalo de data.
1. Escolha quais contas ou assinaturas exibir.
1. Como opção, é possível:
   - Alterar a exibição entre **Cobrar por serviços** e **Cobrar por hierarquia** para exibir detalhamentos diferentes.
   - Exibir detalhes do nome do serviço, da unidade de medida, das unidades consumidas, da taxa efetiva e do custo estendido.

## <a name="download-csv-reports"></a>Baixar relatórios CSV

A página de download de relatório mensal permite que os administradores corporativos baixem vários relatórios como arquivos CSV. Os relatórios baixáveis incluem:

- Relatório de saldo e cobrança
- Relatório de detalhes de uso
- Relatório de cobranças do Azure Marketplace
- Tabela de preços

### <a name="to-download-reports"></a>Para baixar relatórios:

1. No Azure Enterprise Portal, selecione **Relatório**.
1. Selecione **Baixar uso** na faixa de opções superior.
1. Selecione **Baixar** ao lado do relatório do mês apropriado.

### <a name="csv-report-formatting-issues"></a>Problemas de formatação do relatório em CSV

Os clientes que visualizam os relatórios em CSV do Azure Enterprise Portal em euros podem encontrar problemas de formatação com relação a vírgulas e pontos.

Por exemplo, você talvez veja:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Você deverá ver:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Horas | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Esse problema de formatação ocorre devido às configurações padrão na funcionalidade de importação do Excel. O Excel importa todos os campos como texto 'Geral' e supõe que um número seja separado no padrão matemático. Por exemplo:  "1,000.00".

Se uma moeda europeia usar um ponto (.) para o separador de milhar e uma vírgula para o separador de casas decimais (,), a exibição será incorreta. Por exemplo:  "-1.000,00". Os resultados da importação podem variar dependendo da configuração de idioma regional.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Para importar o arquivo CSV sem problemas de formatação:

1. No Microsoft Excel, vá para **Arquivo** > **Abrir**.
   O assistente de importação de texto será exibido.
1. Em **Tipo de dados originais**, escolha **delimitado**.  O padrão é **Largura Fixa**.
1. Selecione **Avançar**.
1. Em Delimitadores, marque a caixa de seleção para **Vírgula**. Desmarque **Guia** se ela estiver selecionada.
1. Selecione **Avançar**.
1. Role até as colunas **ResourceRate** e **ExtendedCost**.
1. Selecione a coluna **ResourceRate**. Ele aparece realçada em preto.
1. Na seção **Formato de dados da coluna**, selecione **Texto** em vez de **Geral**. O cabeçalho da coluna será alterado de **Geral** para **Texto**.
1. Repita as etapas 8 e 9 para a coluna **Custo Estendido** e selecione **Concluir**.

> [!TIP]
> Se você tiver arquivos CSV definidos para abrir automaticamente no Excel, use a função **Abrir** no Excel. No Excel, vá para **Arquivo** > **Abrir**.

## <a name="balance-and-charge-report"></a>Relatório de saldo e cobrança

O relatório de saldo e cobrança oferece um resumo mensal de informações sobre saldos, novas compras, cobranças de serviço do Azure Marketplace, ajustes e cobranças excedentes.

Todas as linhas na tabela de resumo de Compromisso do Serviço do Azure permanecem mensais estáticas. Você encontrará detalhes de todas as compras e ajustes nas seções **Novos Detalhes da Compra** e **Detalhes de Ajuste**.

### <a name="download-the-balance-and-charge-report"></a>Baixar o relatório de saldo e cobrança

1. Entre no Azure Enterprise Portal como um administrador corporativo.
1. Selecione **Relatórios** no painel esquerdo.
1. Selecione a guia **Baixar Relatório**.
1. Selecione o mês apropriado na coluna **Saldo e cobrança** e selecione para baixar o relatório.

## <a name="usage-detail-report"></a>Relatório de detalhes de uso

O relatório de detalhes de uso oferece um resumo mensal de um consumo de registros de serviços e quantidades. Ele inclui informações sobre nomes de medidores, tipos de medidores e quantidades consumidas.

### <a name="download-the-usage-detail-report"></a>Baixar o relatório de detalhes de uso

1. Entre no Azure Enterprise Portal como um administrador corporativo.
1. Selecione **Relatórios** na área de navegação à esquerda.
1. Selecione a guia **Baixar Uso**.
1. Selecione o mês apropriado na coluna **Detalhes de uso** e selecione para baixar o relatório.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Cobranças do Azure Marketplace nos relatórios do Azure Enterprise Portal

Há dois tipos de cobranças do Azure Marketplace:

- **Baseado em uso:** produtos medidos em unidades transacionais.  Por exemplo, as máquinas virtuais são cobradas por hora, as pesquisas de API do Bing são cobradas pelo número de pesquisas.
- **Valor mensal**: cobrado mensalmente com base no uso ou acesso.

Para obter mais informações sobre as cobranças do Azure Marketplace, confira as [perguntas frequentes do Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

Para exibir as diferentes cobranças no Azure Enterprise Portal:

- **Relatório resumo de uso**: mostra **ambas** as cobranças (baseada em uso e o valor mensal do Azure Marketplace).
- **Relatório de cobranças do Marketplace**: mostra **apenas** as cobranças do Azure Marketplace com base no uso.  Valores únicos não são exibidos.

> [!NOTE]
> O Azure Marketplace não está disponível para registros de MPSA.

## <a name="advanced-report-download"></a>Download do relatório avançado

Para relatórios sobre contas ou intervalos de datas específicos, é possível usar o download do relatório avançado. Desde 30 de agosto de 2016, o formato do arquivo de saída é CSV para acomodar conjuntos de registros maiores.

1. No Azure Enterprise Portal, selecione **Download de Relatório Avançado**.
1. Escolha **Intervalo de datas apropriado**.
1. Escolha **Contas apropriadas**.
1. Escolha **Solicitar dados de uso**.
1. Escolha o botão **Atualizar** até que o status do relatório seja atualizado para **Baixar**.
1. Baixe o relatório.

## <a name="reporting-for-non-enterprise-administrators"></a>Relatórios para administradores não corporativos

Os administradores corporativos podem conceder a DA (administradores de departamento) e AO (proprietários de conta) permissões para exibir cobranças em um registro. Os proprietários da conta com acesso podem baixar relatórios em CSV específicos para suas contas e assinaturas. Eles também podem exibir as informações visualmente na seção de relatório do Azure Enterprise Portal.

### <a name="to-enable-access"></a>Para habilitar o acesso:

 1. Entre no Azure Enterprise Portal como um administrador corporativo.
 1. Selecione **Gerenciar** na área de navegação à esquerda.
 1. Selecione a guia **Registro**.
 1. Na seção **Detalhes do registro**, selecione o ícone de lápis ao lado de **Exibir cobranças de DA** ou **Exibir cobranças de AO**.
 1. Selecione **Habilitado**.
 1. Clique em **Salvar**.

### <a name="to-view-reports"></a>Para exibir relatórios:

1. Entre no Azure Enterprise Portal como um administrador de departamento ou um proprietário de conta.
1. Selecione **Relatórios** na área de navegação à esquerda.
1. Selecione a guia **Resumo do Uso** para exibir informações sobre a conta e a assinatura visualmente.
1. Selecione **Baixar Uso** para exibir os relatórios em CSV.

Os administradores de departamento e os proprietários de conta não podem exibir cobranças usando a função **Download do relatório avançado**. Os custos são exibidos como US$ 0,00.

As permissões do proprietário da conta para exibir cobranças aplicam-se aos proprietários de conta e a todos os usuários que têm permissões em assinaturas associadas. Se você for um cliente indireto, os recursos de custos deverão ser habilitados pelo seu parceiro de canal.

## <a name="move-usage-data-to-another-enrollment"></a>Mover dados de uso para outro registro

Os dados de uso só serão movidos se a transferência for retroativa. Há duas opções para mover dados de uso de um registro para outro:

- Transferências de conta de um registro para outro
- Transferências de registro de um registro para outro

Para ambas as opções, é preciso enviar uma [solicitação de suporte](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à Equipe de Suporte do EA para obter assistência. 

## <a name="azure-ea-pricing-overview"></a>Visão geral de preços do EA do Azure

Esta seção fornece detalhes sobre como o uso é calculado. Ela responde muitas perguntas frequentes sobre cobranças para vários serviços do Azure em um Contrato Enterprise em que os cálculos são mais complexos.

### <a name="price-protection"></a>Proteção de preço

Como um cliente ou parceiro de canal, você tem a garantia de receber preços iguais ou abaixo dos preços mostrados na sua CPS (Tabela de Preços ao Cliente) ou o preço em vigor na data de início de vigência de sua compra do Azure. Esse preço é chamado de preço de linha de base. Para serviços introduzidos após a compra do Azure, você é cobrado pelo preço em vigor no desconto de nível aplicável quando o serviço foi introduzido pela primeira vez. Essa proteção de preço aplica-se durante o termo do compromisso, um ou três anos, dependendo do seu Contrato Enterprise.

### <a name="price-changes"></a>Alterações de preço

A Microsoft pode baixar o preço atual do Contrato Enterprise para serviços individuais do Azure durante o termo de um registro. Estenderemos essas taxas reduzidas aos clientes existentes enquanto o preço mais baixo estiver em vigor. Se essas taxas aumentarem posteriormente, o preço do registro de um serviço não aumentará além do teto de proteção de preço, conforme definido acima. No entanto, a taxa pode aumentar em relação ao preço anterior menor. Em ambos os casos, informaremos os clientes e os parceiros de canal indiretos sobre alterações de preço futuras por meio de um email aos administradores corporativos e parceiros associados ao registro.

### <a name="current-effective-pricing"></a>Preço efetivo atual

Parceiros de cliente e de canal podem exibir seus preços atuais para um registro fazendo logon no [Azure Enterprise Portal](https://ea.azure.com/) e exibindo a página da tabela de preços para esse registro. Se você comprar o Azure indiretamente por meio de um de nossos parceiros de canal, precisará obter as atualizações de preços do seu parceiro de canal, a menos que eles tenham habilitado a marcação de preços para seu registro.

### <a name="introduction-of-new-azure-services"></a>Introdução de novos serviços do Azure

Estamos aprimorando continuamente o Azure e adicionamos periodicamente novos serviços que são cobrados separadamente dos serviços existentes. Alguns serviços em versão prévia estão disponíveis automaticamente, enquanto outros exigem a ação do cliente por meio do [portal da Conta do Azure](https://account.windowsazure.com/PreviewFeatures).

Alguns serviços começam com preços promocionais em vigor quando introduzidos pela primeira vez, o que pode aumentar em uma data futura.

Quando os serviços mudam da versão prévia para a GA (disponibilidade geral), os preços podem aumentar à medida que os SLAs de desempenho e confiabilidade são aplicados em sua totalidade. Esse aumento não é limitado pela proteção de preço de linha de base normal. O uso desses serviços é cobrado com a taxa aumentada. Para evitar cobranças relacionadas a esses novos serviços, você precisará recusar o uso deles.

### <a name="introduction-of-regional-pricing"></a>Introdução de preços regionais

Além da introdução de novos serviços, os serviços também mudam periodicamente de uma base global única para um modelo mais regional à medida que o suporte regional para esses serviços aumenta.

Quando a regionalização de um serviço é introduzida pela primeira vez, a proteção de preços de linha de base aplica-se a essas novas regiões. No entanto, se as regiões adicionais para esse mesmo serviço forem introduzidas posteriormente, elas serão consideradas novos serviços e serão oferecidas com taxas individuais próprias, independentemente da proteção de preço de linha de base.

### <a name="enterprise-devtest"></a>Desenvolvimento/Teste Enterprise

Os administradores corporativos podem habilitar os proprietários de conta para criar assinaturas com base na oferta de Desenvolvimento/Teste Enterprise. O proprietário da conta precisa configurar as assinaturas de Desenvolvimento/Teste Enterprise que são necessárias para os assinantes subjacentes. Essa configuração permite que os assinantes ativos do Visual Studio executem cargas de trabalho de desenvolvimento e teste no Azure com taxas especiais de Desenvolvimento/Teste Enterprise. Para obter mais informações, consulte [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Cálculos de uso do Contrato Enterprise

Confira [serviços do Azure](https://azure.microsoft.com/services/) e [preços do Azure](https://azure.microsoft.com/pricing/) para saber mais sobre preços, unidades de medida, perguntas frequentes e diretrizes de relatório de uso para cada serviço individualmente. Veja mais informações sobre cálculos de EA nas seções a seguir.

### <a name="enterprise-agreement-units-of-measure"></a>Unidades de medida do Contrato Enterprise

As unidades de medida para Contratos Enterprise costumam ser diferentes daquelas utilizadas em nossos outros programas, como o programa MOSA (Contrato do Microsoft Online Services). Essa diferença significa que, para vários serviços, a unidade de medida é agregada para fornecer os preços normalizados. A unidade de medida mostrada na exibição Resumo do Uso do Azure Enterprise Portal é sempre a medida do Enterprise. Apresentamos uma lista completa das unidades atuais de medida e conversões de cada serviço no arquivo Excel [Nomes de serviços amigáveis](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Regras de arredondamento

O Azure Enterprise Portal segue a lógica padrão de arredondamento bancário do IEEE ou a lógica de arredondamento gaussiana. Essa lógica arredonda números para o dígito par mais próximo para valores de meio dígito. A lógica mais típica de arredondar meio para cima sempre arredonda meio dígito para o próximo dígito mais alto. Esse método do Azure Enterprise Portal, na verdade, fornece uma soma total mais precisa sobre o grupo quando comparado à lógica padrão do Excel.

Para ilustrar: quando o primeiro dígito solto é um 5 e não há dígitos a seguir ou os dígitos a seguir são zeros, arredonde para o dígito par mais próximo. Por exemplo: 2,315 e 2,325 arredondados para o 100º mais próximo tornam-se 2,32.

Para referência, a seguinte tabela mostra as fórmulas do Excel que você pode usar para modelar as regras do Azure Enterprise Portal para arredondamento e conversão:

| Cenário | Fórmula da lógica de arredondamento bancária |
| --- | --- |
| Uso de arredondamento | =MARRED({_origem_}, 0,0002) |
| Arredondamento de preços (2 decimais) | =MARRED({_origem_}, 0,02) |
| Arredondamento de preços (0 decimais) | =MARRED({_origem_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversão entre o relatório de detalhes de uso e a página de resumo de uso

No relatório de download de dados de uso, é possível ver o uso bruto de recursos de até seis casas decimais. No entanto, os dados de uso mostrados no Azure Enterprise Portal são arredondados para quatro casas decimais para unidades de compromisso e truncados para zero decimais para unidades excedentes. Os dados de uso brutos são arredondados primeiro para quatro dígitos antes da conversão para as unidades usadas no Azure Enterprise Portal. Em seguida, as unidades do Enterprise convertidas são arredondadas novamente para quatro dígitos. Só é possível ver as horas reais consumidas antes da conversão no relatório de download de uso e não no Azure Enterprise Portal.

Por exemplo:  Se 694,533404 horas SQL Server reais forem relatadas no relatório de detalhes de uso. Essas unidades são convertidas em 6,94533404 de 100 horas de computação que, em seguida, são arredondadas para 6,9453 e exibidas no Azure Enterprise Portal.

- Para determinar o valor da cobrança estendida, as unidades exibidas são multiplicadas pelo preço unitário do compromisso e o resultado é truncado para dois decimais. Para o iene japonês (JPY) e o won coreano (KRW), o valor estendido é arredondado para zero decimais.
- No caso de excedente, as unidades faturáveis são truncadas para seis e, em seguida, multiplicadas pelo preço unitário excedente para determinar o valor da cobrança estendida.
- Para a cobrança do MSP (provedor de serviços gerenciados), todo o uso associado a um departamento marcado como MSP é truncado para zero decimais após a conversão para a unidade de medida do EA. Como resultado, a soma desse uso pode ser menor do que a soma total de todos os usos relatados no Azure Enterprise Portal. Isso dependerá se o MSP estiver dentro do saldo do compromisso monetário ou estiver excedente.

### <a name="graduated-pricing"></a>Preços graduados

Atualmente, o preço do Contrato Enterprise não inclui preços graduados, em que as cobranças por unidade diminuem à medida que o uso aumenta. Quando você passa de um programa MOSA com preços graduados para um Contrato Enterprise, seus preços são definidos proporcionalmente à camada base do serviço menos os ajustes aplicáveis para descontos de Contrato Enterprise.

### <a name="partial-hour-billing"></a>Cobrança de hora parcial

Todo uso cobrado tem base em minutos convertidos em horas parciais, e não em incrementos de hora inteira. As taxas por hora listadas do Enterprise são aplicadas ao total de horas, além de horas parciais.

### <a name="average-daily-consumption"></a>Consumo diário médio

Alguns serviços são cobrados mensalmente, mas o uso é relatado diariamente. Nesses casos, o uso é avaliado uma vez por dia, dividido por 31 e somado ao longo do número de dias nesse mês de cobrança. Portanto, as taxas nunca são maiores do que o esperado para qualquer mês e são ligeiramente menores para meses com menos de 31 dias.

### <a name="compute-hours-conversion"></a>Conversão de horas de computação

Antes de 28 de janeiro de 2016, o uso das máquinas virtuais Standard e Basic A0, A2, A3 e A4 e Serviços de Nuvem era emitido como minutos dos medidores da Máquina Virtual A1. As VMs A0 contavam como frações de minutos da VM A1 enquanto A2s, A3s e A4s foram convertidas em múltiplos. Como essa política causou alguma confusão para nossos clientes, implementamos uma alteração para atribuir o uso por minuto para os medidores A0, A2, A3 e A4 dedicados.

A nova medição entrou em vigor entre 27 de janeiro e 28 de janeiro de 2016. No download de CSV que mostra o uso durante esse período de transição, você pode ver:

- O uso emitido no medidor A1
- O uso emitido no novo medidor dedicado correspondente ao tamanho da sua implantação.

| **Tamanho da implantação** | **Uso emitido como múltiplo de A1 até 26 de janeiro de 2016** | **Uso emitido no medidor dedicado a partir de 27 de janeiro de 2016** |
| --- | --- | --- |
| A0 | 0,25 de hora em A1 | 1 hora em A0 |
| A2 | 2 horas em A1 | 1 hora em A2 |
| A3 | 4 horas em A1 | 1 hora em A3 |
| A4 | 8 horas em A1 | 1 hora em A4 |

### <a name="regions"></a>Regiões

Para os serviços em que a zona e a região afetam os preços, confira a seguinte tabela para ver o mapa de regiões geográficas:

| Localização geográfica | Regiões de transferência de dados | Regiões CDN |
| --- | --- | --- |
| Zona 1 | Norte da Europa <br> Europa Ocidental <br> Oeste dos EUA <br> Leste dos EUA <br> Centro-Norte dos EUA <br> Centro-Sul dos EUA <br> Leste dos EUA 2 <br> EUA Central | América do Norte <br> Europa |
| Zona 2 | Leste do Pacífico Asiático <br> Sudeste do Pacífico Asiático <br> Leste do Japão <br> Oeste do Japão <br> Leste da Austrália <br> Sudeste da Austrália | Pacífico Asiático <br> Japão <br> América Latina <br> Oriente Médio e África <br> Leste da Austrália <br> Sudeste da Austrália |
| Zona 3 | Sul do Brasil |   |

Não há cobranças para saída de dados entre serviços hospedados no mesmo data center. Por exemplo, o Office 365 e o Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Compromisso monetário e uso não faturado

O compromisso monetário do Azure é um valor pago adiantadamente pelos serviços do Azure. O compromisso monetário é consumido conforme os serviços são usados. Serviços internos do Azure são cobrados em relação ao compromisso monetário. No entanto, algumas cobranças são realizadas separadamente e os serviços do Azure Marketplace não consomem o compromisso monetário.

### <a name="charges-billed-separately"></a>Cobranças arrecadadas separadamente

Alguns produtos e serviços fornecidos por fontes de terceiros não consomem o compromisso monetário do Azure. Ao invés disso, esses itens são cobrados separadamente como parte da fatura excedente do ciclo de cobrança padrão.

Combinamos todas as cobranças do Azure e do Azure Marketplace em uma fatura que se alinha com o ciclo de cobrança do registro. A fatura combinada não se aplica aos clientes na Austrália, no Japão ou em Singapura.

A fatura combinada mostra o uso do Azure primeiro, seguido por qualquer cobrança do Azure Marketplace. Os clientes da Austrália, do Japão ou de Singapura veem as cobranças do Azure Marketplace em uma fatura separada.

Se não houver uso excedente no final do ciclo de cobrança padrão, as cobranças arrecadadas separadamente serão faturadas separadamente. Esse processo é aplicável aos clientes na Austrália, no Japão e em Singapura.

Os seguintes serviços são cobrados separadamente:

- Canônico
- Citrix XenApp Essentials
- Usuário registrado do Citrix XenDesktop
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace para clientes EA

Para clientes diretos, as cobranças do Azure Marketplace ficam visíveis no Azure Enterprise Portal. As compras e consumo no Azure Marketplace são faturadas fora do compromisso monetário e são cobradas trimestral ou mensalmente e em atraso.

Clientes indiretos podem encontrar suas assinaturas do Azure Marketplace na página **Gerenciar Assinaturas** do Azure Enterprise Portal, mas os preços estarão ocultos. Os clientes devem entrar em contato com o LSP (Provedor de Soluções de Licenciamento) para saber mais sobre cobranças do Azure Marketplace.

Novas compras mensais ou anuais recorrentes do Azure Marketplace são cobradas por completo durante o período em que os itens do Azure Marketplace são comprados. Esses itens serão renovados automaticamente após o período seguinte no mesmo dia da compra original.

Os encargos mensais recorrentes existentes continuarão a ser renovados no primeiro dia de cada mês do calendário. Os encargos anuais serão renovados no aniversário da data de compra.

Alguns serviços de revendedores de terceiros disponíveis no Azure Marketplace agora consumirão o saldo do compromisso monetário do seu EA (Contrato Enterprise). Anteriormente, esses serviços eram cobrados fora do compromisso monetário do EA, sendo faturados separadamente. O compromisso monetário do EA para esses serviços no Azure Marketplace ajudam a simplificar o gerenciamento de compras e pagamentos do cliente. Para obter uma lista completa dos serviços que agora consomem o compromisso monetário, confira a [atualização de 6 de março de 2018 no site do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Parceiros

Os LSPs podem baixar uma lista de preços do Azure Marketplace na página da tabela de preços no Azure Enterprise Portal. Selecione o link **lista de preços do Marketplace** no canto superior direito. A lista de preços do Azure Marketplace mostra todos os serviços disponíveis e seus preços.

Para baixar a lista de preços:

1. No Azure Enterprise Portal, acesse **Relatórios** > **Tabela de Preços**.
1. No canto superior direito, localize o link para a lista de preços do Azure Marketplace sob seu nome de usuário.
1. Clique com o botão direito do mouse no link e selecione **Salvar Destino Como**.
1. Na janela **Salvar**, altere o título do documento para `AzureMarketplacePricelist.zip`, o que alterará o arquivo de .xlsx para um arquivo .zip.
1. Quando o download for concluído, você terá um arquivo zip com listas de preços específicas de cada país.
1. Os LSPs devem consultar o arquivo de seu país para obter os preços específicos. Os LSPs podem usar a guia **Notificações** para estarem atentos às SKUs que são novas ou foram desativadas.
1. As alterações de preço ocorrem com pouca frequência. Os LSPs recebem notificações por email de aumentos de preço e alterações de FX (câmbio internacional) com 30 dias de antecedência.
1. Os LSPs recebem uma fatura por registro, por ISV, por trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Habilitar compras no Azure Marketplace

Os administradores corporativos podem desabilitar ou habilitar compras no Azure Marketplace para todas as assinaturas do Azure sob o registro deles. Se o administrador corporativo desabilitar as compras e houver assinaturas do Azure que já têm assinaturas do Azure Marketplace, essas assinaturas do Azure Marketplace não serão canceladas nem afetadas.

Embora os clientes possam converter suas assinaturas diretas do Azure para o EA do Azure associando-as ao registro no Azure Enterprise Portal, essa ação não converte automaticamente as assinaturas filhas.

Para habilitar compras no Azure Marketplace:

1. Entre no Azure Enterprise Portal como um administrador corporativo.
1. Acesse **Gerenciar**.
1. Em **Detalhe do registro**, selecione o ícone de lápis ao lado do item de linha **Azure Marketplace**.
1. Selecione **Habilitado/Desabilitado** ou **Somente SKUs Gratuitas e BYOL**, conforme apropriado.
1. Clique em **Salvar**.

> [!NOTE]
> A opção Somente SKUs Gratuitas e BYOL (traga sua própria licença) limita a compra e a aquisição de SKUs do Azure Marketplace a apenas SKUs Gratuitas e BYOL.

Obtenha mais informações sobre [cobranças do Azure Marketplace nos relatórios do Azure Enterprise Portal](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Serviços cobrados por hora para o EA do Azure

Os seguintes serviços são cobrados por hora em um Contrato Enterprise em vez da taxa mensal em MOSP:

- Rede de Distribuição de Aplicativos
- Firewall do Aplicativo Web

### <a name="azure-remoteapp"></a>RemoteApp do Azure

Se você tiver um Contrato Enterprise, pagará pelo Azure RemoteApp com base em sua categoria de preços de Contrato Enterprise. Não há cobranças adicionais. O preço padrão inclui um inicial de 40 horas. O preço ilimitado cobre um inicial de 80 horas. O RemoteApp para de emitir o uso além das 80 horas.

## <a name="azure-marketplace-faq"></a>Perguntas frequentes sobre o Azure Marketplace

Esta seção explica como seu compromisso monetário do Azure pode se aplicar a alguns serviços de revendedores de terceiros no Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>O que mudou com os serviços do Azure Marketplace e o compromisso monetário de EA?

A partir de 1º de março de 2018, alguns serviços de revendedores de terceiros consomem o MC (compromisso monetário) de EA. Exceto para as RI (instâncias de VM Reservadas) do Azure, os serviços foram cobrados anteriormente fora do compromisso monetário de EA e foram faturados separadamente.

Expandimos o uso do MC para incluir alguns dos serviços do Azure Marketplace publicados por terceiros que são adquiridos com mais frequência. O compromisso monetário do EA para esses serviços no Azure Marketplace ajuda a simplificar o gerenciamento de compras e pagamentos.

### <a name="why-did-we-make-this-change"></a>Por que fizemos essa alteração?

Os clientes estão continuamente procurando maneiras adicionais de aproveitar o pagamento adiantado do MC. Essa alteração foi solicitada com frequência pelos clientes e afetou uma grande parte dos clientes do Azure Marketplace.

### <a name="how-do-you-benefit"></a>Como você se beneficia?

Você tem uma experiência de cobrança mais simples e é mais capaz de gastar seu compromisso monetário de EA. Como esses serviços estão incluídos no MC pré-pago, seu compromisso monetário de EA torna-se mais valioso.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Quais serviços do Azure Marketplace usam o compromisso monetário de EA e como posso saber?

Quando você compra um serviço que usa o MC, o Azure Marketplace apresenta um aviso de isenção de responsabilidade. Há suporte para alguns serviços publicados por Red Hat, SUSE, Autodesk e Oracle. Atualmente, com nomes semelhantes, os serviços publicados por outras partes não deduzem do MC. Uma lista completa está disponível no final das perguntas frequentes.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>E se o meu compromisso monetário de EA acabar?

Se você consumir todo os seu MC e ficar em excedente, as cobranças relacionadas a esses serviços aparecerão na próxima fatura excedente, com outros serviços de consumo. Antes da alteração de 1º de março de 2018, essas cobranças eram faturadas com outros serviços do Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Por que nem todos os Azure Marketplaces consomem o compromisso monetário de EA?

Trabalhamos com frequência para fornecer a melhor experiência do cliente relacionada ao compromisso monetário do EA. Essa alteração afetou uma grande quantidade de clientes e uma parte significativa do gasto total no Azure Marketplace. Outros serviços podem ser adicionados no futuro.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Como isso afeta o registro indireto e os parceiros?

Nossos clientes ou parceiros de registro indireto não serão afetados. Esses serviços estão sujeitos aos mesmos recursos de marcação de parceiros que outros serviços de consumo. A única alteração é que as cobranças aparecem em uma fatura diferente e o pagamento delas sai do compromisso monetário do EA do cliente.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Há uma lista de serviços do Azure Marketplace que consomem o compromisso monetário de EA?

As ofertas específicas do Azure Marketplace podem usar fundos do compromisso monetário. Confira [serviços de terceiros que usam o compromisso monetário](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) para obter uma lista completa de produtos participantes desse programa.

## <a name="power-bi-reporting"></a>Relatórios do Power BI

O Power BI Reporting está disponível para clientes diretos, indiretos e parceiros do EA que tenham acesso para exibir informações de cobrança.

### <a name="power-bi-pro"></a>Power BI Pro

O Power BI Pro está disponível para clientes EA. Com o Power BI Pro, você pode gerar e compartilhar relatórios para gerenciar efetivamente seus dados de custo. Ele também tem recursos adicionais de colaboração e atualização de dados. O Power BI Pro oferece maior capacidade de dados e limites de fluxo de dados.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer. 
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Para acessar o Microsoft Azure Consumption Insights:

1. Vá para [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Selecione **Obtenha agora**.
1. Forneça um número de registro e o número de meses e, em seguida, selecione **Avançar**.
1. Forneça a chave de acesso da API para se conectar. A chave do seu registro está disponível no [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Selecione **Entrar** para iniciar automaticamente o processo de importação.
1. Depois de concluído, um novo painel, relatório e modelo serão exibidos no painel de navegação. Selecione o painel para exibir os dados importados por você.

> [!TIP]
> 
> - Para saber como gerar a chave de API para seu registro, confira o arquivo de ajuda sobre Relatórios de API no [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Para obter mais informações sobre como conectar o Power BI ao seu consumo do Azure, confira [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Para acessar o pacote de conteúdo herdado do Power BI para EA:

1. Acesse o [site do Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Entre com uma conta corporativa ou de estudante válida.

   A conta corporativa ou de estudante pode ser a mesma ou uma diferente da usada para acessar o registro pelo Azure Enterprise Portal.
1. No painel de serviços, selecione **Microsoft Azure Enterprise** e selecione **Conectar**.
1. Na tela **Conectar-se ao Azure Enterprise**, preencha os campos:
    - URL do ambiente do Azure: [https://ea.azure.com](https://ea.azure.com/)
    - Número de meses: de 1 a 36
    - Número de registro: o número de seu registro
1. Selecione **Avançar**.
1. Na caixa **Chave de Autenticação**, insira a chave de API.

    Acesse a chave de API no Azure Enterprise Portal na guia **Baixar Uso**. Selecione **Chave de acesso à API** e cole a chave na caixa **Chave de Conta**.
1. Os dados demoram entre 5 e 30 minutos para carregar no Power BI, dependendo do tamanho dos conjuntos de dados.

## <a name="reports-faq"></a>Perguntas frequentes sobre relatórios

Esta seção aborda perguntas comuns sobre relatórios.

### <a name="why-is-my-cost-showing-as-0"></a>Por que meu custo é mostrado como US$ 0,00?

Para os clientes de **registro direto**, os administradores corporativos podem fornecer aos proprietários da conta e aos administradores do departamento acesso a informações de custo/preço nos relatórios de uso. Siga estas etapas:

1. No Azure Enterprise Portal, selecione **Gerenciar** na área de navegação à esquerda.
1. Selecione o símbolo de lápis azul ao lado de Exibir cobranças de DA (administrador de departamento).
1. Selecione **Habilitado** e salve.
1. Selecione o lápis azul ao lado de Exibir cobranças de AO (proprietário de conta).
1. Selecione **Habilitado** e salve.

> [!NOTE]
> Se você for proprietário de conta ou administrador de departamento, entre em contato com seu administrador corporativo para habilitar o recurso de preços.

Para clientes de **registro indireto**, entre em contato com seu parceiro para verificar se ele habilitou o recurso de preços para você. Isso só pode ser feito pelo parceiro. Depois que você estiver habilitado, poderá exibir o custo e os preços em seu registro como um administrador corporativo.

Parceiros, se você quiser habilitar o recurso de exibir encargos para um proprietário da conta ou um administrador do departamento, siga as etapas em **registro direto**.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Por que não há informações de SKU no meu relatório de detalhes de uso?

O relatório de detalhes de uso não contém informações de SKU. No entanto, o relatório contém informações de uso para que você possa baixar o relatório da tabela de preços para obter as informações de SKU.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Por que o valor total no Azure Marketplace não corresponde aos relatórios de resumo e detalhes de uso?

O relatório de cobranças do Azure Marketplace exibe apenas as cobranças com base no uso. Valores únicos não são exibidos. Confira a página de resumo de uso para obter as cobranças com base em uso e nos valores únicos mais atualizadas.

### <a name="why-is-there-no-information-on-my-api-report"></a>Por que não há informações em meu relatório de API?

As chaves de API expiram a cada seis meses. Se você estiver tendo um problema, um administrador corporativo deverá gerar uma nova chave de API. Lembre-se de seguir as etapas nas perguntas frequentes sobre o relatório de API.

### <a name="why-isnt-my-power-bi-report-working"></a>Por que meu relatório do Power BI não está funcionando?

Para problemas com o Power BI, registre um tíquete com a [equipe de suporte do Power BI](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Por que as minhas marcas de recurso não aparecem em meus relatórios?

As marcas de recurso são gerenciadas no portal do Azure. Entre em contato com a equipe de assinatura do Azure no [portal do Azure](https://portal.azure.com). Siga as etapas no artigo [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Por que minha taxa de recursos muda todos os dias?

A taxa de recursos mostrada no relatório de uso detalhado é um valor calculado. Ela representa a taxa mensal média cobrada pelo serviço. A taxa de recurso é calculada usando a média do compromisso mensal e as cobranças mensais excedentes para uma unidade de serviço. A porção de uso cobrada do compromisso e das tarifas excedentes mudará para o dia em que o mês for fechado. Assim, a taxa de recursos listada também é alterada durante o mês. A taxa de recursos é bloqueada no quinto dia após o fim do mês.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossário de processos para calcular a taxa de recursos

- **Total de unidades BRUTAS**: quantidade consumida no relatório de uso detalhado.
- **Recurso de MOCP por unidade**: o sistema de uso upstream emite os usos para cada serviço em unidades diferentes. (Predefinição)
- **Consumo por unidade:** unidade de medida do Azure Enterprise. (Predefinição)
- **Preço:** preço unitário no Azure Enterprise Portal.
- **Custo total**: custo estendido do relatório de detalhes de uso ou uso do compromisso mais excedente do Azure Enterprise Portal.

### <a name="charges-calculations"></a>Cálculos de cobranças

- **Converter em recursos de MOCP consumidos** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Converter em unidades consumidas** = `Consumed MOCP Resources / Consumption per Unit`
- **Calcular o custo total** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Lógica no cálculo de uso

**Taxa de recursos** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

A taxa de recursos é derivada com base em seus encargos. Ela pode não corresponder ao preço unitário real na tabela de preços.

No relatório de download de dados de uso, é possível ver o uso bruto de recursos de até seis casas decimais. Esses dados são usados para cálculos de cobrança de excedentes. No entanto, os dados de uso mostrados no Azure Enterprise Portal são arredondados para quatro casas decimais para unidades de compromisso e truncados para zero decimais para unidades excedentes. No Azure Enterprise Portal, todo o uso excedente é cobrado somente por unidades completas. Você pode ver uma grande diferença entre o preço unitário e a taxa de recursos para uso cobrado como excedente ou em meses mistos.

## <a name="next-steps"></a>Próximas etapas

- Os seguintes arquivos do Excel fornecem detalhes sobre os serviços do Azure e são atualizados no sexto e no vigésimo dia de cada mês:

   | Title | Descrição | Nome do arquivo |
   | --- | --- | --- |
   | [Nomes de serviços amigáveis](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Lista todos os serviços ativos e inclui: <br>  <ul><li>categoria de serviço</li>   <li>nome de serviço amigável</li>   <li>nome do compromisso e número da peça</li> <li>nome do consumo e número da peça</li>   <li>unidades de medida</li>   <li>fatores de conversão entre o uso relatado e o uso exibido no Enterprise Portal</li></ul> | Nomes\_Serviços\_Amigáveis.xlsx |
   | [Campos de download do serviço](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Esta planilha fornece uma lista de todas as combinações possíveis dos campos relacionados ao serviço no relatório Baixar uso. | Campos\_Download\_Serviço.xlsx |

- Para saber mais sobre como funcionam as faturas e as cobranças, confira [Entender a fatura do Contrato Enterprise do Azure](../understand/review-enterprise-agreement-bill.md).
- Para começar a usar o Azure Enterprise Portal, confira [Introdução ao Portal do EA do Azure](ea-portal-get-started.md).
