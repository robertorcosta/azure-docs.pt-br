---
title: Guia de segurança do Edge Azure Stack mini R | Microsoft Docs
description: Descreve as convenções de segurança, as diretrizes, as considerações e explica como instalar e operar com segurança seu dispositivo de Azure Stack Edge mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465998"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Instruções de segurança do Edge Azure Stack mini R

![Ícone de aviso 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ler ícone ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **de aviso de segurança ler informações de segurança e integridade**

Leia todas as informações de segurança neste artigo antes de usar o Azure Stack dispositivo mini R de borda, uma composição de um pacote de bateria, uma fonte de alimentação conectada por AC/DC, um adaptador de energia de módulo e um módulo de servidor. A falha em seguir as instruções pode resultar em incêndios, choques elétricos, ferimentos ou danos às suas propriedades. Leia todas as informações de segurança abaixo antes de usar Azure Stack o mini R de borda.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança

As seguintes palavras de sinal para sinais de alerta de risco são:

| ícone | Descrição |
|:--- |:--- |
| ![Símbolo de perigo](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Perigo:** Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos sério. <br> **AVISO:** Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos grave. <br> **Cuidado:** Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos secundário ou moderado.|
|

Os seguintes ícones de risco devem ser observados durante a configuração e execução do dispositivo de borda do Azure Stack Edge:

| ícone | Descrição |
|:--- |:--- |
| ![Leia antes todas as instruções](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Leia antes todas as instruções |
| ![Símbolo de perigo](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Símbolo de perigo |
| ![Ícone de choque elétrico](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Risco de choque elétrico |
| ![Somente uso interno](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Somente uso interno |
| ![Ícone de Sem peças cuja manutenção possa ser feita pelo usuário](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Nenhuma parte do usuário que seja atendida. Não acesse a menos que seja devidamente treinado. |
|

## <a name="handling-precautions-and-site-selection"></a>Tratamento de precauções e seleção de site

O dispositivo de borda Azure Stack mini R tem as seguintes precauções de tratamento e critérios de seleção de site:

![Ícone de aviso 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ sem usuários ícone de partes com serviço de peças ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **cuidado:**

* Inspecione o dispositivo *da forma como foi recebido* para ver se há danos. Se o compartimento do dispositivo estiver danificado, [entre em contato com suporte da Microsoft](azure-stack-edge-placeholder.md) para obter uma substituição. Não tente operar o dispositivo.
* Se você suspeitar de que o dispositivo está com problemas de funcionamento, [entre em contato com suporte da Microsoft](azure-stack-edge-placeholder.md) para obter uma substituição. Não tente reparar o dispositivo.
* O dispositivo não contém peças operadas pelo usuário. Níveis de tensão, corrente e energia de risco no aparelho. Não abra. Devolva o dispositivo para a Microsoft para manutenção.

![Ícone de aviso 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

É recomendável operar o sistema:

* Longe de fontes de calor, incluindo luz de sol direto e radiadores.
* Em locais não expostos a umidade ou chuva.
* Localizada em um espaço que minimiza a vibração e choque físico.  O sistema foi projetado para choques e vibração de acordo com MIL-STD-810G.
* Isolado de campos eletromagnéticos fortes produzidos por dispositivos elétricos.
* Não permita que nenhum Liquid ou qualquer objeto estrangeiro entre no sistema. Não coloque bebidas ou outros contêineres líquidos no sistema ou perto dele.

![Ícone de aviso 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de partes sem serviço de usuário ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **cuidado:**

* Este equipamento contém uma bateria de lítio. Não tente atender ao pacote de bateria. As baterias neste equipamento não são atendidas pelo usuário. Risco de explosão se a bateria for substituída por um tipo incorreto.

![Ícone de aviso 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

Somente cobrar o pacote de bateria quando ele fizer parte do dispositivo do Azure Stack Edge mini R, não cobrar como um dispositivo separado.

![Ícone de aviso 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

* A opção ligar/desligar no pacote de bateria é descarregando a bateria apenas para o módulo de servidor. Se o adaptador de energia estiver conectado ao pacote de bateria, a energia será passada para o módulo de servidor, mesmo se a opção estiver na posição desligado.

![Ícone de aviso 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

* Não grave ou faça um circuito curto do pacote de bateria. Ele deve ser reciclado ou descartado corretamente.

![Ícone de aviso 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

* Em vez de usar a fonte de alimentação AC/DC fornecida, esse sistema também tem a opção de usar um campo fornecido com o tipo 2590 de bateria. Nesse caso, o usuário final deve verificar se ele atende a todas as normas de segurança, transporte, ambiente e qualquer outra legislação nacional/local aplicável.
* Ao operar o sistema com a bateria do tipo 2590, opere a bateria dentro das condições de uso especificadas pelo fabricante da bateria.

![Ícone de aviso 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **cuidado:**

Este dispositivo tem duas portas SFP + que podem ser usadas com transceptores ópticos. Para evitar a radiação de laser perigosa, use somente com transceptores de classe 1.

## <a name="electrical-precautions"></a>Cuidados elétricos

O dispositivo mini R do Azure Stack Edge tem as seguintes precauções elétricas:

![Ícone de aviso 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVISO:**

Quando usado com o adaptador de fonte de energia:

* Forneça uma conexão de aterramento elétrico segura para os cabos de alimentação. O cabo corrente alternado (AC) tem um plugue de aterramento de três fios (um plug que tem um pino de aterramento). Esse plugue se encaixa apenas em uma tomada CA com aterramento. Não ignore o objetivo do pino de aterramento.
* Considerando que o plugue no cabo de alimentação é o dispositivo de desconexão principal, verifique se as saídas de soquete estão localizadas perto do dispositivo e são de fácil acesso.
* Desconecte os cabos de alimentação (puxando o plug-in, não o cabo) e desconecte todos os cabos se alguma das seguintes condições existir:

  * O cabo de alimentação ou o plugue ficou desgastado ou está danificado.
  * O dispositivo é exposto a chuva, excesso de umidade ou outros liquids.
  * O dispositivo foi descartado e a embalagem do dispositivo foi danificada.
  * Você acha que o dispositivo precisa de reparos.
* Desconecte permanentemente a unidade antes de movê-la ou se achar que ela foi danificada de alguma forma.

* Forneça uma fonte de alimentação adequada com proteção contra sobrecarga elétrica para atender às seguintes especificações de energia:

* Tensão: 100-240 volts AC
* Atual: 1,7 amperes
* Frequência: 50 a 60 Hz

![Ícone de aviso 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Não tente modificar ou usar cabos de energia CA diferentes daqueles fornecidos com o equipamento.

![Ícone de aviso 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico, ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ Use somente o aviso de uso interno ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **:**

* A fonte de energia rotulada com este símbolo é classificada apenas para uso interno.

## <a name="regulatory-information"></a>Informações normativas

O seguinte contém informações regulatórias para Azure Stack dispositivo mini R de borda, número do modelo normativo: TMA01.

Azure Stack o dispositivo Microsoft Edge mini R foi projetado para uso com NRTL listado (UL, CSA, ETL, etc.) e IEC/EN 60950-1 ou IEC/EN 62368-1 em conformidade com o equipamento de tecnologia da informação (CE marcado).

Em outros países além dos EUA e do Canadá, os cabos de rede (não fornecidos com o equipamento) não deverão ser instalados com este equipamento se seu comprimento for maior que 3 metros.

O equipamento foi projetado para operar nos seguintes ambientes:

| Ambiente | Especificações |
|:---  |:--- |
| Especificações de temperatura do sistema | <ul><li>Temperatura de armazenamento: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Operação contínua: 0 &deg; c – 40 &deg; C (32 &deg; F – 104 &deg; F)</li></ul> |
| Especificações relativas à umidade (RH) | <ul><li>Armazenamento: 5% a 95% de umidade relativa</li><li>Operação: 10% a 90% de umidade relativa</li></ul>|
| Especificações máximas de altitude | <ul><li>Operação: 15.000 pés (4.572 metros)</li><li>Não operacional: 40.000 pés (12.192 metros)</li></ul>|

> ![Aviso de aviso ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** &nbsp; alterações ou modificações feitas no equipamento não expressamente aprovado pela Microsoft podem anular a autoridade do usuário para operar o equipamento.

CANADÁ e EUA:

Aviso: este equipamento foi testado e foi encontrado para estar em conformidade com os limites de um dispositivo digital de classe a, de acordo com a parte 15 das regras de FCC. Esses limites são projetados para fornecer uma proteção razoável contra interferência prejudicial quando o equipamento é operado em um ambiente comercial. O equipamento gera, usa e pode irradiar energia de radiofrequência. Se não for instalado e usado de acordo com o manual de instruções, poderá causar interferências prejudiciais às comunicações de rádio. A operação deste equipamento em uma área residencial provavelmente causará interferência prejudicial, caso em que o usuário será solicitado a corrigir a interferência por sua própria despesa.

O dispositivo está em conformidade com a parte 15 das Regras da FCC e normas RSS isentas de licença do Canadá. A operação está sujeita a estas duas condições: (1) o dispositivo não pode causar interferência prejudicial e (2) o dispositivo deve aceitar todas as interferências recebidas, inclusive interferências que possam causar uma operação indesejada do dispositivo.

![Aviso de informações regulatórias 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

PODE ICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, EUA.
Estados Unidos: (800) 426-9400 Canadá: (800) 933-4750

União Europeia: solicite uma cópia da declaração de conformidade da UE.

> ![Ícone de aviso 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) este é um produto de classe a. Em um ambiente doméstico, o produto pode causar interferências de rádio. Nesse caso, o usuário talvez tenha que tomar as devidas medidas.

Descarte de baterias e equipamentos eletroeletrônicos:

![Ícone de aviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Este símbolo no produto, nas baterias ou embalagem significa que o produto e as baterias contidas não devem ser descartadas no lixo comum. É sua responsabilidade levá-los a um ponto de coleta para reciclagem de baterias e equipamentos eletroeletrônicos. Essa coleta e reciclagem separadas ajudarão a conservar os recursos naturais e impedir possíveis consequências negativas para a saúde humana e o meio ambiente devido à possível existência de substâncias perigosas nas baterias e equipamentos eletroeletrônicos, que podem ser causadas pelo descarte inadequado. Para obter mais informações sobre onde entregar suas baterias e equipamentos eletroeletrônicos, entre em contato com a Prefeitura, com o serviço de coleta seletiva ou a loja que vendeu o produto. Entre em contato com erecycle@microsoft.com para obter mais informações sobre a WEEE.

Este produto contém baterias em forma de moeda.
Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL número de telefone: + 353 1 295 3826 número de fax: + 353 1 706 4110

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-prep.md)
