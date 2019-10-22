---
title: Introdução ao armazenamento de dados em aplicativos Xamarin. iOS
description: Este documento descreve vários meios de armazenamento de dados em um aplicativo Xamarin. iOS e fornece informações específicas sobre os benefícios do SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 4000e4cc5d260457c0e0da275e3a7beecafd1a98
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70767025"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Introdução ao armazenamento de dados em aplicativos Xamarin. iOS

## <a name="when-to-use-a-database"></a>Quando usar um banco de dados

Embora os recursos de armazenamento e processamento de dispositivos móveis estejam aumentando, os telefones e tablets ainda ficam atrasados por trás de suas contrapartes de desktop &amp; laptops. Por esse motivo, vale a pena dedicar algum tempo para planejar a arquitetura de armazenamento de dados para seu aplicativo, em vez de apenas pressupor que um banco de dado é a resposta certa o tempo todo. Há várias opções diferentes que atendam a diferentes requisitos, como:

- **Preferências** – o iOS oferece um mecanismo interno para armazenar pares chave-valor simples de dados. Se você estiver armazenando configurações de usuário simples ou pequenas partes de dados (como informações de personalização), use os recursos nativos da plataforma para armazenar esse tipo de informação. Para o iOS, você também pode aproveitar a sincronização do iCloud para esses dados, tanto para backup quanto para sincronização para usuários com vários dispositivos.
- **Arquivos de texto** – entrada do usuário ou caches de conteúdo baixado (por exemplo, HTML) pode ser armazenado diretamente no sistema de arquivos. Use uma Convenção de nomenclatura de arquivo apropriada para ajudá-lo a organizar os arquivos e localizar dados.
- **Arquivos de dados serializados** – os objetos podem ser persistidos como XML ou JSON no sistema de arquivos. O .NET Framework inclui bibliotecas que facilitam a serialização e a desserialização de objetos. Use os nomes apropriados para organizar os arquivos de dados.
- **Banco** de dados – o mecanismo de banco de dados SQLite está disponível para IOS e é útil para armazenar dados estruturados que você precisa consultar, classificar ou manipular de outra forma. O armazenamento de banco de dados é adequado para listas de dado com muitas propriedades.
- **Arquivos de imagem** – embora seja possível armazenar dados binários em um dispositivo móvel, é recomendável armazená-los diretamente no sistema de arquivos. Se necessário, você pode armazenar os nomes de fileem um banco de dados para associar a imagem a outro dado. Ao lidar com imagens grandes ou muitas imagens, é uma boa prática planejar uma estratégia de cache que exclua os arquivos que você não precisa mais para evitar o consumo de todo o espaço de armazenamento do usuário.

Se um banco de dados for o mecanismo de armazenamento certo para seu aplicativo, o restante deste documento discutirá como usar o SQLite na plataforma Xamarin.

## <a name="advantages-of-using-a-database"></a>Vantagens de usar um banco de dados

Há várias vantagens em usar um banco de dados SQL em seu aplicativo móvel:

- Os bancos de dados SQL permitem o armazenamento eficiente do dado estruturado.
- Dados específicos podem ser extraídos com consultas complexas.
- Os resultados da consulta podem ser classificados.
- Os resultados da consulta podem ser agregados.
- Os desenvolvedores com habilidades de banco de dados existentes podem utilizar o seu conhecimento para criar o banco de dado e o código de acesso a data.
- O modelo de dados do componente de servidor de um aplicativo conectado pode ser usado novamente (no todo ou em parte) no aplicativo móvel.

## <a name="sqlite-database-engine"></a>Mecanismo de Banco de Dados SQLite

O SQLite é um mecanismo de banco de dados de software livre que foi adotado pela Apple para sua plataforma móvel. O mecanismo de banco de dados SQLite é integrado ao iOS, portanto, não há nenhum trabalho adicional para os desenvolvedores tirar proveito dele. O SQLite é adequado para o desenvolvimento móvel de plataforma cruzada porque:

- O mecanismo de banco de dados é pequeno, rápido e fácil de ser portátil.
- Um banco de dados é armazenado em um único arquivo, que é fácil de gerenciar em dispositivos móveis.
- O formato de arquivo é fácil de usar entre plataformas: sejam os sistemas 32-ou 64 bits e Big-ou little-endian.
- Ele implementa a maior parte do padrão SQL92.

Como o SQLite foi projetado para ser pequeno e rápido, há algumas limitações em seu uso:

- Não há suporte para algumas sintaxes de junção externa.
- Somente a renomeação de tabela e a addcoluna têm suporte. Você não pode executar outras modificações em seu esquema.
- As exibições são somente leitura.

Você pode aprender mais sobre o SQLite no site- [SQLite.org](http://SQLite.org) -no entanto, todas as informações de que você precisa para usar o SQLite com o Xamarin estão contidas neste documento e em exemplos associados. O mecanismo de banco de dados SQLite é integrado a todas as versões do iOS.
Embora não seja abordado neste capítulo, o SQLite também está disponível para uso em aplicativos Windows Phone e Windows.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

O SQLite também pode ser usado em plataformas Windows, embora essas plataformas não sejam abordadas neste documento.
Leia mais nos estudos de caso do [profissional](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky) de [tarefas](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e da tarefa e examine o [blog de Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).

## <a name="related-links"></a>Links relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
