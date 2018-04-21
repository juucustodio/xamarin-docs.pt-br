---
title: Introdução ao armazenamento de dados no Android
ms.prod: xamarin
ms.assetid: FDAC0771-4749-4758-865A-F1BD190CA54B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/28/2017
ms.openlocfilehash: 42af02d2ed2a679d89425257d92f03a3764675ce
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2018
---
# <a name="introduction"></a>Introdução

## <a name="when-to-use-a-database"></a>Quando usar um banco de dados

Enquanto os recursos de armazenamento e processamento de dispositivos móveis estão aumentando, telefones e tablets ainda atrasem suas contrapartes de desktop e laptop. Por esse motivo vale a pena dedicar algum tempo para planejar a arquitetura de armazenamento de dados para seu aplicativo em vez de apenas supondo que um banco de dados é a resposta correta, o tempo todo. Há várias opções diferentes que atendem a requisitos diferentes, como:

-  **Preferências** – Android oferece um mecanismo interno para armazenar pares de chave-valor simples de dados. Se você estiver armazenando as configurações do usuário simples ou pequenos pedaços de dados (como informações de personalização), em seguida, use os recursos da plataforma nativo para armazenar este tipo de informações.
-  **Arquivos de texto** – entrada do usuário ou caches do baixado conteúdo (por exemplo. HTML) pode ser armazenado diretamente no sistema de arquivos. Use uma convenção de nomenclatura de arquivo apropriada para ajudá-lo a organizar os arquivos e localizar dados.
-  **Serializado arquivos de dados** – objetos podem ser mantidos como XML ou JSON no sistema de arquivos. O .NET framework inclui bibliotecas que tornam serializar e desserializar objetos. Use os nomes apropriados para organizar os arquivos de dados.
-  **Banco de dados** – SQLite o mecanismo de banco de dados está disponível na plataforma Android, e é útil para o armazenamento estruturado que você precisa consultar, classificar ou manipular dados. Armazenamento de banco de dados é adequado para listas de dados com várias propriedades.
-  **Arquivos de imagem** – Embora seja possível armazenar dados binários no banco de dados em um dispositivo móvel, é recomendável que você armazená-los diretamente no sistema de arquivos. Se necessário, você pode armazenar os nomes dos arquivos em um banco de dados para associar a imagem com outros dados. Ao lidar com imagens grandes ou muitas imagens, é recomendável para planejar uma estratégia de cache que exclui arquivos que desnecessários para evitar o consumo de espaço de armazenamento de todas as do usuário.

Se um banco de dados é o mecanismo de armazenamento adequada para seu aplicativo, o restante deste documento descreve como usar SQLite na plataforma do Xamarin.

## <a name="advantages-of-using-a-database"></a>Vantagens de usar um banco de dados

Há inúmeras vantagens de usar um banco de dados SQL em seu aplicativo móvel:

-  Bancos de dados SQL permitem armazenamento eficiente de dados estruturados.
-  Dados específicos podem ser extraídos com consultas complexas.
-  Resultados da consulta podem ser classificados.
-  Resultados da consulta podem ser agregados.
-  Os desenvolvedores com habilidades existentes do banco de dados podem utilizar seu conhecimento para desenvolver o código de acesso e banco de dados.
-  O modelo de dados do componente de servidor de um aplicativo conectado pode ser reutilizado (no todo ou em parte) no aplicativo móvel.


## <a name="sqlite-database-engine"></a>Mecanismo de banco de dados SQLite

SQLite é um mecanismo de banco de dados do código-fonte aberto que foi adotado por Google para sua plataforma móvel. O mecanismo de banco de dados SQLite é integrado para ambos os sistemas operacionais, portanto não há nenhum trabalho adicional para os desenvolvedores a aproveitá-lo. SQLite é adequado para desenvolvimento móvel em plataforma cruzada porque:

-  O mecanismo de banco de dados é pequena, rápida e facilmente portáteis.
-  Um banco de dados é armazenado em um único arquivo, que é fácil de gerenciar dispositivos móveis.
-  O formato de arquivo é fácil de usar em plataformas: se 32 ou 64 bits e os sistemas de big ou little-endian.
-  Ele implementa a maioria do SQL92 padrão.


Como SQLite é projetado para ser menor e mais rápida, há algumas restrições sobre seu uso:

-  Não há suporte para uma sintaxe de junção externa.
-  Somente tabela RENOMEAR e ADDCOLUMN têm suporte. Não é possível executar outras modificações em seu esquema.
-  Modos de exibição são somente leitura.


Você pode aprender mais sobre SQLite no site - [SQLite.org](http://SQLite.org) - no entanto, todas as informações que você precisa usar SQLite com Xamarin contidas neste documento e exemplos de associados. O mecanismo de banco de dados SQLite com suporte no Android desde 2 Android.
Embora não abordados neste capítulo, SQLite também está disponível para uso em aplicativos do Windows e Windows Phone.

## <a name="windows-and-windows-phone"></a>Windows e Windows Phone

SQLite também pode ser usado em plataformas Windows, embora essas plataformas não são abordadas neste documento.
Leia mais no [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) e [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) estudos de caso e examine [blog de Tim](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Dados do Android receitas](https://developer.xamarin.com/recipes/android/data/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
