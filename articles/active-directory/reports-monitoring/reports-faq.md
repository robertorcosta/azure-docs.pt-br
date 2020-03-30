---
title: Perguntas frequentes sobre os relatórios do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes em torno dos relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266501"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Perguntas frequentes sobre os relatórios do Azure Active Directory

Este artigo inclui respostas a perguntas frequentes sobre os relatórios do Azure AD (Azure Active Directory). Para saber mais, consulte [Relatórios do Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introdução 

**P: Atualmente, `https://graph.windows.net/<tenant-name>/reports/` uso as APIs de ponto final para puxar a auditoria Azure AD e relatórios integrados de uso de aplicativos em nossos sistemas de relatórios de forma programática. Para o que devo mudar?**

**R:** Consulte a [Referência de API](https://developer.microsoft.com/graph/) para saber como é possível [usar as APIs para acessar os relatórios de atividades](concept-reporting-api.md). Esse ponto de extremidade tem dois relatórios (**Auditoria** e **Entradas**) que fornecem todos os dados que você obteve no ponto de extremidade de API antigo. Esse novo ponto de extremidade também tem um relatório de entradas com a licença do Azure AD Premium que você pode usar para obter informações de uso de aplicativo uso de dispositivo e conexão do usuário.

---

**P: Atualmente, `https://graph.windows.net/<tenant-name>/reports/` uso as APIs de ponto final para puxar relatórios de segurança Do Azure AD (tipos específicos de detecções, como credenciais vazadas ou logins de endereços IP anônimos) em nossos sistemas de relatórios de forma programática. Para o que devo mudar?**

**A:** Você pode usar a  [API de detecção de risco de proteção de identidade](../identity-protection/graph-get-started.md)para acessar detecções de segurança através do Microsoft Graph. Esse novo formato oferece maior flexibilidade na forma como você pode consultar dados, com filtragem avançada, seleção de campo e muito mais, e padroniza detecções de risco em um tipo para facilitar a integração em SIEMs e outras ferramentas de coleta de dados. Uma vez que os dados estão em um formato diferente, você não pode substituir uma nova consulta para suas consultas antigas. No entanto, [a nova API usa o Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que é o padrão da Microsoft para essas APIs, como O365 ou Azure AD. Assim, o trabalho necessário pode estender seus investimentos atuais do Microsoft Graph ou ajudá-lo a iniciar sua transição para esta nova plataforma padrão.

---

**P: Como obtenho uma licença premium?**

**R:** Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory.

---

**P: Em quanto tempo deverei ver dados de atividades depois de obter uma licença premium?**

**R:** Se você já tiver dados de atividades como uma licença gratuita, poderá vê-los imediatamente. Se você não tiver dados, levará um ou dois dias para os dados serem exibidos nos relatórios.

---

**P: Eu poderei visualizar os dados do último mês, após obter uma licença premium do Azure AD?**

**A:** Se você mudou recentemente para uma versão Premium (incluindo uma versão de teste), você pode ver dados até 7 dias inicialmente. Quando os dados forem acumulados, será possível visualizar os dados dos últimos 30 dias.

---

**P: Eu preciso ser um administrador global para ver as entradas da atividade no portal do Azure ou obter dados por meio da API?**

**R:** Não, você também poderá acessar os dados do relatório por meio do portal ou por meio da API se for um **Leitor de Segurança** ou **Administrador de Segurança** do locatário. É claro que os ** Administradores Globais** também terão acesso a esses dados.

---


## <a name="activity-logs"></a>Logs de atividade


**P: O que é a retenção de dados para logs de atividade (de entradas e de auditoria) no Portal do Azure?** 

**R:** A tabela a seguir lista o período de retenção de dados para logs de atividades. Para obter mais informações, consulte as [políticas de retenção de dados para relatórios do Azure AD](reference-reports-data-retention.md).

| Relatório                 | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Logs de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Entradas               | N/D           | 30 dias             | 30 dias             |
| Uso do MFA do Azure        | 30 dias       | 30 dias             | 30 dias             |

---

**P: Quanto tempo leva até que eu possa ver os dados de atividade depois de ter concluído minha tarefa?**

**R:** Os logs de auditoria têm uma latência que varia de 15 minutos a uma hora. Logs de atividade de conexão podem levar de 15 minutos a até 2 horas para alguns registros.

---

**P: Posso obter informações do log de atividades do Office 365 por meio do Portal do Azure?**

**A:** Mesmo que as atividades do Office 365 e os logs de atividades do Azure AD compartilhem muitos dos recursos do diretório, se você quiser uma visão completa dos registros de atividades do Office 365, você deve ir ao [centro de admin microsoft 365](https://admin.microsoft.com) para obter informações do registro de atividades do Office 365.

---

**P: Quais APIs devo usar para obter informações sobre logs de atividades do Office 365?**

**A:** Use as [APIs de gerenciamento do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) para acessar os logs de atividade do Office 365 através de uma API.

---

**P: Quantos registros posso baixar do Portal do Azure?**

**R:** É possível baixar até 5000 registros do portal do Azure. Os registros são classificados por *mais recentes* e, por padrão, você obterá 5000 registros mais recentes.

---

## <a name="risky-sign-ins"></a>Entradas de risco

**P: Há uma detecção de risco na Proteção de Identidade, mas não estou vendo o login correspondente no relatório de login. Isso é esperado?**

**R:** Sim, o Identity Protection avalia o risco de todos os fluxos de autenticação, sejam eles interativos ou não. No entanto, o relatório de todas as entradas mostra apenas as entradas interativas.

---

**P: Como posso saber o motivo pelo qual uma entrada ou um usuário foi sinalizado como arriscado no Portal do Azure?**

**A:** Se você tiver uma assinatura **Azure AD Premium,** você pode aprender mais sobre as detecções de risco subjacentes selecionando o usuário em **Usuários sinalizados para risco** ou selecionando um registro no relatório de **logins de Risco.** Se você tiver uma assinatura **gratuita** ou **básica,** você poderá visualizar os usuários em relatórios de login sustais de risco e risco, mas não pode ver as informações de detecção de risco subjacentes.

---

**P: Como os endereços IP são calculados no relatório de entradas e entradas arriscadas?**

**R:** Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é ainda mais complicado por fatores como provedores móveis e VPNs que emitem endereços IP de pools centrais, geralmente muito longe de onde o dispositivo cliente é realmente usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações. 

---

**P: O que significa a detecção de risco "Sign-in with additional risk detected"?**

**R:** Para fornecer insights sobre todos as entradas arriscadas no ambiente, "Entrar com risco adicional detectado" funciona como espaço reservado para entradas de detecções exclusivas dos assinantes do Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Acesso Condicional

**P: quais são as novidades com esse recurso?**

**A:** Os clientes agora podem solucionar problemas nas políticas de acesso condicional através de todos os relatórios de login. Os clientes podem rever o status de Acesso Condicional e mergulhar nos detalhes das políticas aplicadas ao login e no resultado de cada política.

**P: Como faço para começar?**

**R:** para começar a usar:

* Navegue até o relatório de logins no [Portal do Azure](https://portal.azure.com).
* Clique no login que você deseja solucionar.
* Navegue até a guia **Acesso Condicional.** Aqui, você pode ver todas as políticas que impactaram o login e o resultado de cada política. 
    
**P: Quais são todos os valores possíveis para o status de Acesso Condicional?**

**A:** O status de Acesso Condicional pode ter os seguintes valores:

* **Não aplicado**: isso significa que não havia nenhuma política de autoridade de certificação com o usuário e o aplicativo no escopo. 
* **Sucesso**: isso significa que havia uma política de autoridade de certificação com o usuário e o aplicativo no escopo e as políticas da autoridade de certificação foram atendidas com êxito. 
* **Falha**: isso significa que havia uma política de autoridade de certificação com o usuário e o aplicativo no escopo e as políticas da autoridade de certificação não foram atendidas. 
    
**P: Quais são todos os valores possíveis para o resultado da política de acesso condicional?**

**A:** Uma política de acesso condicional pode ter os seguintes resultados:

* **Sucesso**: A política foi atendida com êxito.
* **Falha**: A política não foi atendida.
* **Não aplicado**: isso pode ocorrer porque não atenderam às condições da política.
* **Não habilitado**: isso é devido à política no estado desabilitado. 
    
**P: O nome da política no relatório de entrada de todos não corresponde ao nome da política na CA. Porque?**

**R:** O nome da política no relatório todas as entrada baseia-se no nome da política de autoridade de certificação no momento da entrada. Isso pode ser inconsistente com o nome da política na autoridade de certificação, se você tiver atualizado o nome da política mais tarde, ou seja, após o logon.

**P: Meu login foi bloqueado devido a uma política de Acesso Condicional, mas o relatório de atividade de login mostra que o login foi bem sucedido. Porque?**

**A:** Atualmente, o relatório de login pode não mostrar resultados precisos para os cenários do Exchange ActiveSync quando o Acesso Condicional é aplicado. Pode haver casos em que o resultado de login no relatório mostra um login bem-sucedido, mas o login realmente falhou devido a uma política de Acesso Condicional. 
