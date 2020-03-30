---
title: Fuso horário de instância gerenciada
description: Conheça as especificidades do fuso horário da instância gerenciada do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256088"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusos horários na instância gerenciada do banco de dados SQL do Azure

O Tempo Universal Coordenado (UTC) é o fuso horário recomendado para o nível de dados das soluções em nuvem. A Instância Gerenciada do Banco de Dados Do Azure SQL também oferece uma escolha de fusos horários para atender às necessidades dos aplicativos existentes que armazenam valores de data e hora e funções de data e hora de chamada com um contexto implícito de um fuso horário específico.

Funções T-SQL como [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou código CLR observam o fuso horário definido no nível de ocorrência. Os trabalhos do SQL Server Agent também seguem os horários de acordo com o fuso horário da ocorrência.

  >[!NOTE]
  > Instance Gerenciado é a única opção de implantação do Banco de Dados SQL do Azure que suporta a configuração do fuso horário. Outras opções de implantação sempre seguem UTC.
Use [em time zone](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) em bancos de dados SQL únicos e agrupados se você precisar interpretar informações de data e hora em um fuso horário não UTC.

## <a name="supported-time-zones"></a>Fusos horários suportados

Um conjunto de fusos horários suportados é herdado do sistema operacional subjacente da instância gerenciada. É atualizado regularmente para obter novas definições de fuso horário e refletir mudanças nas existentes.

[A política de mudanças de horário/fuso horário](https://aka.ms/time) de verão garante a precisão histórica de 2010 para frente.

Uma lista com nomes dos fusos horários suportados é exposta através da exibição do sistema [sys.time_zone_info.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

## <a name="set-a-time-zone"></a>Defina um fuso horário

Um fuso horário de uma instância gerenciada pode ser definido apenas durante a criação de instâncias. O fuso horário padrão é UTC.

  >[!NOTE]
  > O fuso horário de uma instância gerenciada existente não pode ser alterado.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Defina o fuso horário através do portal Azure

Quando você inserir parâmetros para uma nova instância, selecione um fuso horário na lista de fusos horários suportados.
  
![Definindo um fuso horário durante a criação da instância](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Especifique a propriedade timezoneId no [modelo do Gerenciador de recursos](https://aka.ms/sql-mi-create-arm-posh) para definir o fuso horário durante a criação da instância.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Uma lista de valores suportados para a propriedade timezoneId está no final deste artigo.

Se não for especificado, o fuso horário será definido como UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Verifique o fuso horário de uma instância

A função [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) retorna um nome de exibição do fuso horário da instância.

## <a name="cross-feature-considerations"></a>Considerações entre recursos

### <a name="restore-and-import"></a>Restauração e importação

Você pode restaurar um arquivo de backup ou importar dados para uma instância gerenciada a partir de uma instância ou de um servidor com diferentes configurações de fuso horário. Certifique-se de fazê-lo com cautela. Analise o comportamento do aplicativo e os resultados das consultas e relatórios, assim como quando você transfere dados entre duas instâncias do SQL Server com configurações de fuso horário diferentes.

### <a name="point-in-time-restore"></a>Restauração em um momento determinado

Quando você executa uma restauração point-in-time, o tempo para restaurar é interpretado como tempo UTC. Desta forma, quaisquer ambiguidades devido ao horário de verão e suas possíveis mudanças são evitadas.

### <a name="auto-failover-groups"></a>Grupos de failover automático

O uso do mesmo fuso horário em uma instância primária e secundária em um grupo de failover não é aplicado, mas recomendamos fortemente.

  >[!WARNING]
  > Recomendamos fortemente que você use o mesmo fuso horário para a instância primária e secundária em um grupo de failover. Devido a certos casos raros de uso, manter o mesmo fuso horário em instâncias primárias e secundárias não é aplicada. É importante entender que, no caso de failover manual ou automático, a instância secundária manterá seu fuso horário original.

## <a name="limitations"></a>Limitações

- O fuso horário da instância gerenciada existente não pode ser alterado.
- Os processos externos lançados a partir dos trabalhos do Agente de Servidor SQL não observam o fuso horário da instância.

## <a name="list-of-supported-time-zones"></a>Lista de fusos horários suportados

| **ID do fuso horário** | **Nome de exibição do fuso horário** |
| --- | --- |
| Hora padrão da linha de data | (UTC-12:00) Linha de Data Internacional Oeste |
| UTC-11 | (UTC-11:00) Tempo Universal Coordenado-11 |
| Horário Padrão aleuta | (UTC-10:00) Ilhas Aleutas |
| Horário padrão havaiano | (UTC-10:00) Havaí |
| Horário Padrão de Marquesas | (UTC-09:30) Ilhas Marquesas |
| Horário padrão do Alasca | (UTC-09:00) Alasca |
| UTC-09 | (UTC-09:00) Tempo Universal Coordenado-09 |
| Horário Padrão do Pacífico (México) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Tempo Universal Coordenado-08 |
| Horário padrão do Pacífico | (UTC-08:00) Hora do Pacífico (EUA & Canadá) |
| Horário padrão da montanha dos EUA | (UTC-07:00) Arizona |
| Horário padrão da montanha (México) | (UTC-07:00) Chihuahua |
| Horário padrão da montanha | (UTC-07:00) Mountain Time (EUA & Canadá) |
| Hora Padrão da América Central | (UTC-06:00) América Central |
| Horário padrão central | (UTC-06:00) Hora Central (EUA & Canadá) |
| Horário padrão da Ilha de Páscoa | (UTC-06:00) Ilha de Páscoa |
| Hora Padrão Central (México) | (UTC-06:00) Guadalajara |
| Horário Padrão Central do Canadá | (UTC-06:00) Saskatchewan |
| Horário padrão de SA Pacific | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Horário padrão oriental (México) | (UTC-05:00) Chetumal |
| Horário padrão oriental | (UTC-05:00) Horário do Leste (EUA & Canadá) |
| Horário padrão do Haiti | (UTC-05:00) Haiti |
| Horário Padrão de Cuba | (UTC-05:00) Havana |
| Horário padrão do leste dos EUA | (UTC-05:00) Indiana (Leste) |
| Hora Padrão de Turks e Caicos | (UTC-05:00) Turcos e Caicos |
| Horário Padrão do Paraguai | (UTC-04:00) Assunção |
| Horário padrão do Atlântico | (UTC-04:00) Atlantic Time (Canadá) |
| Horário Padrão da Venezuela | (UTC-04:00) Caracas |
| Horário Padrão Central brasileiro | (UTC-04:00) Cuiabá |
| Horário padrão de SA Ocidental | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Horário padrão da Pacific SA | (UTC-04:00) Santiago |
| Horário padrão de Newfoundland | (UTC-03:30) Newfoundland |
| Horário Padrão do Tocantins | (UTC-03:00) Araguaína |
| E. Horário Padrão da América do Sul | (UTC-03:00) Brasília |
| Horário padrão de SA Oriental | (UTC-03:00) Cayenne |
| Horário Padrão da Argentina | (UTC-03:00) City of Buenos Aires |
| Horário Padrão da Groenlândia | (UTC-03:00) Gronelândia |
| Horário padrão de Montevidéu | (UTC-03:00) Montevidéu |
| Hora Padrão de Magallanes | (UTC-03:00) Punta Arenas |
| Horário padrão de Saint Pierre | (UTC-03:00) Saint Pierre e Miquelon |
| Horário Padrão da Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Tempo Universal Coordenado-02 |
| Hora padrão do Meio-Atlântico | (UTC-02:00) Meio-Atlântico - Velho |
| Horário Padrão dos Açores | (UTC-01:00) Açores |
| Horário padrão de Cabo Verde | (UTC-01:00) Cabo Verde É. |
| UTC | (UTC) Tempo Universal Coordenado |
| Hora padrão GMT | (UTC+00:00) Dublin, Edimburgo, Lisboa, Londres |
| Horário padrão de Greenwich | (UTC+00:00) Monróvia |
| W. Horário Padrão da Europa | (UTC+01:00) Amsterdã, Berlim, Berna, Roma, Estocolmo, Viena |
| Horário Padrão da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Hora Padrão do Romance | (UTC+01:00) Bruxelas, Copenhague, Madrid, Paris |
| Horário padrão do Marrocos | (UTC+01:00) Casablanca |
| Horário padrão de São Tome | (UTC+01:00) São Tome |
| Horário Padrão da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| W. Horário Padrão da África Central | (UTC+01:00) África Ocidental |
| Hora Padrão da Jordânia | (UTC+02:00) Amã |
| Horário padrão do GTB | (UTC+02:00) Atenas |
| Horário padrão do Oriente Médio | (UTC+02:00) Beirute |
| Horário Padrão do Egito | (UTC+02:00) Cairo |
| E. Horário Padrão da Europa | (UTC+02:00) Chisinau |
| Horário Padrão da Síria | (UTC+02:00) Damasco |
| Horário padrão da Cisjordânia | (UTC+02:00) Gaza |
| Horário Padrão da África do Sul | (UTC+02:00) Harare |
| Horário padrão do FLE | (UTC+02:00) Helsinki, Kyiv, Riga, Sófia, Tallinn, Vilnius |
| Horário padrão de Israel | (UTC+02:00) Jerusalém |
| Horário padrão de Kaliningrado | (UTC+02:00) Kaliningrado |
| Horário padrão do Sudão | (UTC+02:00) Cartum |
| Horário padrão da Líbia | (UTC+02:00) Tripoli |
| Hora Padrão da Namíbia | (UTC+02:00) Windhoek |
| Horário padrão árabe | (UTC+03:00) Bagdá |
| Horário padrão da Turquia | (UTC+03:00) Istambul |
| Horário padrão árabe | (UTC+03:00) Kuwait |
| Horário padrão da Bielorrússia | (UTC+03:00) Minsk |
| Horário padrão russo | (UTC+03:00) Moscou |
| E. Horário Padrão da África | (UTC+03:00) Nairobi |
| Horário padrão do Irã | (UTC+03:30) Teerã |
| Horário padrão árabe | (UTC+04:00) Abu Dhabi |
| Horário Padrão de Astrakhan | (UTC+04:00) Astrakhan |
| Horário padrão do Azerbaijão | (UTC+04:00) Baku |
| Fuso Horário da Rússia 3 | (UTC+04:00) Izhevsk |
| Horário padrão das Maurícias | (UTC+04:00) Port Louis |
| Horário padrão de Saratov | (UTC+04:00) Saratov |
| Horário padrão da Geórgia | (UTC+04:00) Tbilisi |
| Horário padrão de Volgogrado | (UTC+04:00) Volgograd |
| Horário padrão do Cáucaso | (UTC+04:00) Yerevan |
| Horário padrão do Afeganistão | (UTC+04:30) Cabul |
| Horário Padrão da Ásia Ocidental | (UTC+05:00) Ashgabat |
| Horário Padrão de Ekaterinburg | (UTC+05:00) Ekaterinburg |
| Horário Padrão do Paquistão | (UTC+05:00) Islamabad |
| Horário Padrão da Índia | (UTC+05:30) Chennai, Calcutá, Mumbai, Nova Deli |
| Horário Padrão do Sri Lanka | (UTC+05:30) Sri Jayawardenepura |
| Horário padrão do Nepal | (UTC+05:45) Kathmandu |
| Horário Padrão da Ásia Central | (UTC+06:00) Astana |
| Horário padrão de Bangladesh | (UTC+06:00) Dhaka |
| Hora Padrão de Omsk | (UTC+06:00) Omsk |
| Horário padrão de Myanmar | (UTC+06:30) Yangon (Rangum) |
| Horário padrão da SE Asia | (UTC+07:00) Bangkok, Hanói, Jacarta |
| Horário Padrão de Altai | (UTC+07:00) Barnaul |
| W. Tempo Padrão da Mongólia | (UTC+07:00) Hovd |
| Horário Padrão do Norte da Ásia | (UTC+07:00) Krasnoyarsk |
| N. Horário Padrão da Ásia Central | (UTC+07:00) Novosibirsk |
| Horário Padrão de Tomsk | (UTC+07:00) Tomsk |
| Horário padrão da China | (UTC+08:00) Pequim, Chongqing, Hong Kong, Urumqi |
| Horário padrão do Norte da Ásia | (UTC+08:00) Irkutsk |
| Horário padrão de Cingapura | (UTC+08:00) Kuala Lumpur |
| W. Horário padrão da Austrália | (UTC+08:00) Perth |
| Horário padrão de Taipei | (UTC+08:00) Taipei |
| Horário Padrão de Ulaanbaatar | (UTC+08:00) Ulaanbaatar |
| Horário Padrão aus Central W. | (UTC+08:45) Eucla |
| Hora Padrão transbaikal | (UTC+09:00) Chita |
| Horário padrão de Tóquio | (UTC+09:00) Osaka, Sapporo, Tóquio |
| Horário Padrão da Coreia do Norte | (UTC+09:00) Pyongyang |
| Horário Padrão da Coreia | (UTC+09:00) Seul |
| Horário Padrão de Yakutsk | (UTC+09:00) Yakutsk |
| Cen. Horário padrão da Austrália | (UTC+09:30) Adelaide |
| Horário padrão central do AUS | (UTC+09:30) Darwin |
| E. Horário padrão da Austrália | (UTC+10:00) Brisbane |
| Horário padrão do Leste do AUS | (UTC+10:00) Canberra, Melbourne, Sydney |
| Horário padrão do Pacífico Oeste | (UTC+10:00) Guam |
| Hora Padrão da Tasmânia | (UTC+10:00) Hobart |
| Hora Padrão vladivostok | (UTC+10:00) Vladivostok |
| Hora Padrão de Lord Howe | (UTC+10:30) Ilha Lord Howe |
| Horário padrão de Bougainville | (UTC+11:00) Ilha Bougainville |
| Fuso horário da Rússia 10 | (UTC+11:00) Chokurdakh |
| Horário padrão de Magadan | (UTC+11:00) Magadan |
| Horário padrão de Norfolk | (UTC+11:00) Ilha Norfolk |
| Horário padrão de Sacalina | (UTC+11:00) Sakhalin |
| Horário padrão do Pacífico Central | (UTC+11:00) Solomon Is., Nova Caledonia |
| Fuso horário da Rússia 11 | (UTC+12:00) Anadyr |
| Horário padrão da Nova Zelândia | (UTC+12:00) Auckland |
| UTC+12 | (UTC+12:00) Tempo Universal Coordenado+12 |
| Horário padrão de Fiji | (UTC+12:00) Fiji |
| Kamchatka Horário Padrão | (UTC+12:00) Petropavlovsk-Kamchatsky - Velho |
| Hora Padrão das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC+13 | (UTC+13:00) Tempo Universal Coordenado+13 |
| Horário padrão de Tonga | (UTC+13:00) Nuku'alofa |
| Horário padrão de Samoa | (UTC+13:00) Samoa |
| Horário padrão das Ilhas de Linha | (UTC+14:00) Ilha Kiritimati |

## <a name="see-also"></a>Confira também 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
