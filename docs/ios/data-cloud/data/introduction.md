---
title: Introdução ao armazenamento de dados em aplicativos xamarin. IOS
description: Este documento descreve vários meios de armazenamento de dados em um aplicativo xamarin. IOS e fornece informações específicas sobre os benefícios do SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 5df001926512b182c19ccfcf896057e88f5a597a
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650240"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Introdução ao armazenamento de dados em aplicativos xamarin. IOS

## <a name="when-to-use-a-database"></a>Quando usar um banco de dados

Embora os recursos de armazenamento e processamento de dispositivos móveis estão aumentando, telefones e tablets ainda atrasar sua área de trabalho &amp; contrapartes do laptop. Por esse motivo é que vale a pena dedicar algum tempo para planejar a arquitetura de armazenamento de dados para seu aplicativo em vez de apenas supondo que um banco de dados é a resposta certa o tempo todo. Há várias opções diferentes que atendem a requisitos diferentes, tais como:

-  **Preferências** – iOS oferece um mecanismo interno para armazenar pares chave-valor simples de dados. Se você estiver armazenando as configurações do usuário simples ou pequenos pedaços de dados (como informações de personalização), em seguida, use os recursos da plataforma nativa para armazenar esse tipo de informação. Para iOS, você também pode tirar proveito da sincronização do iCloud para esses dados, tanto para backup e sincronização para usuários com vários dispositivos.
-  **Arquivos de texto** – entrada do usuário ou os caches de conteúdo (por exemplo, baixado HTML) pode ser armazenado diretamente no sistema de arquivos. Use uma convenção de nomenclatura de arquivo apropriada para ajudar você a organizar os arquivos e localizar os dados.
-  **Serializado arquivos de dados** – objetos podem ser persistidos como XML ou JSON no sistema de arquivos. O .NET framework inclui bibliotecas que tornam serializando e desserializando objetos fácil. Use os nomes apropriados para organizar arquivos de dados.
-  **Banco de dados** – mecanismo de banco de dados SQLite o IOS disponível e é útil para armazenar dados estruturados que você precisa consultar, classificar ou manipulados de outra forma. Armazenamento de banco de dados é adequado para listas de dados com muitas propriedades.
-  **Arquivos de imagem** – Embora seja possível armazenar dados binários no banco de dados em um dispositivo móvel, é recomendável armazená-los diretamente no sistema de arquivos. Se necessário, você pode armazenar os nomes de arquivo em um banco de dados para associar a imagem com outros dados. Ao lidar com muitas imagens ou imagens grandes, é uma boa prática para planejar uma estratégia de cache que exclui arquivos que desnecessários para evitar o consumo de espaço de armazenamento de todas as do usuário.


Se um banco de dados é o mecanismo de armazenamento adequada para seu aplicativo, o restante deste documento aborda como usar o SQLite na plataforma Xamarin.

## <a name="advantages-of-using-a-database"></a>Vantagens de usar um banco de dados

Há uma série de vantagens ao uso de um banco de dados SQL no aplicativo móvel:

-  Bancos de dados SQL permitem que o armazenamento eficiente de dados estruturados.
-  Dados específicos podem ser extraídos com consultas complexas.
-  Os resultados da consulta podem ser classificados.
-  Os resultados da consulta podem ser agregados.
-  Os desenvolvedores com habilidades existentes de banco de dados podem utilizar seus dados de conhecimento para o código de acesso do banco de dados e de design.
-  O modelo de dados do componente de servidor de um aplicativo conectado pode ser reutilizado (no todo ou em parte) no aplicativo móvel.


## <a name="sqlite-database-engine"></a>Mecanismo de banco de dados SQLite

O SQLite é um mecanismo de banco de dados do código-fonte aberto que tem sido adotado pela Apple para sua plataforma móvel. O mecanismo de banco de dados SQLite é interno para iOS, portanto, não há nenhum trabalho adicional para os desenvolvedores tirem proveito dele. SQLite é adequado para desenvolvimento móvel de plataforma cruzada, porque:

-  O mecanismo de banco de dados é pequeno, rápido e facilmente transportável.
-  Um banco de dados é armazenado em um único arquivo, que é fácil de gerenciar em dispositivos móveis.
-  O formato de arquivo é fácil de usar em plataformas: se 32 ou 64 bits e sistemas de big ou little-endian.
-  Ele implementa a maioria do SQL92 padrão.


Porque o SQLite é projetado para ser pequenos e rápidos, há algumas limitações sobre seu uso:

-  Não há suporte para algumas sintaxes de junção externa.
-  Somente a RENOMEAÇÃO de tabela e ADDCOLUMN têm suporte. Você não pode executar outras modificações no seu esquema.
-  Modos de exibição são somente leitura.


Você pode aprender mais sobre o SQLite no site - [SQLite.org](http://SQLite.org) – no entanto, todas as informações que você precisa usar o SQLite com o Xamarin estão contidas neste documento e associados exemplos. O mecanismo de banco de dados SQLite é interno a todas as versões do iOS.
Embora não seja abordado neste capítulo, SQLite também está disponível para uso no Windows Phone e aplicativos do Windows.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

SQLite também pode ser usado em plataformas Windows, embora essas plataformas não são abordadas neste documento.
Leia mais na [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e [Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky) estudos de caso e, em seguida, examine [blog de Tim](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).



## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
