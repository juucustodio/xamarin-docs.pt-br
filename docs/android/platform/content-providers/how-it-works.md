---
title: Como os provedores de conteúdo funcionam
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020512"
---
# <a name="how-content-providers-work"></a>Como os provedores de conteúdo funcionam

Há duas classes envolvidas em uma `ContentProvider` interação:

- **ContentProvider** &ndash; Implementa uma API que expõe um conjunto de dados de forma padrão. Os principais métodos são Consulta, Inserção, Atualização e Exclusão.

- **ContentResolver** &ndash; Um proxy estático `ContentProvider` que se comunica com um para acessar seus dados, seja de dentro do mesmo aplicativo ou de outro aplicativo.

Um provedor de conteúdo normalmente é apoiado por um banco de dados SQLite, mas a API significa que o código de consumo não precisa saber nada sobre o SQL subjacente. As consultas são feitas através de um Uri usando constantes para referenciar nomes `ICursor` de colunas (para reduzir dependências da estrutura de dados subjacente), e uma é devolvida para que o código de consumo itera mais.

## <a name="consuming-a-contentprovider"></a>Consumindo um Provedor de Conteúdo

`ContentProviders`exponha sua funcionalidade através de um Uri que está registrado no **AndroidManifest.xml** do aplicativo que publica os dados. Existe uma convenção onde o Uri e as colunas de dados que são expostos devem estar disponíveis como constantes para facilitar a vinculação aos dados. Todos os androides `ContentProviders` fornecem aulas de conveniência com constantes [`Android.Providers`](xref:Android.Provider) que fazem referência à estrutura de dados no namespace.

### <a name="built-in-providers"></a>Provedores incorporados

O Android oferece acesso a uma ampla `ContentProviders`gama de dados do sistema e do usuário usando :

- *Marcadores* &ndash; do navegador e `READ_HISTORY_BOOKMARKS` histórico do `WRITE_HISTORY_BOOKMARKS`navegador (requer permissão e/ou ).

- *CallLog* &ndash; chamadas recentes feitas ou recebidas com o dispositivo.

- *Contatos* &ndash; informações detalhadas da lista de contatos do usuário, incluindo pessoas, telefones, fotos & grupos.

- *Conteúdo da MediaStore* &ndash; do dispositivo do usuário: áudio (álbuns, artistas, gêneros, listas de reprodução), imagens (incluindo miniaturas) & vídeo.

- *Configurações* &ndash; de configurações e preferências de dispositivos em todo o sistema.

- *Conteúdo do UserDictionary* &ndash; do dicionário definido pelo usuário usado para entrada de texto preditiva.

- *Histórico de mensagens* &ndash; de correio de voz.

## <a name="classes-overview"></a>Visão geral das classes

As classes primárias utilizadas ao trabalhar com um `ContentProvider` são mostradas aqui:

[![Diagrama de classe do aplicativo do Provedor de Conteúdo e interações de aplicativos de consumo](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Neste diagrama, `ContentProvider` o implementa consultas e registra uri's que outros aplicativos usam para localizar dados. O `ContentResolver` atua como um 'proxy' para os `ContentProvider` métodos (Consultar, Inserir, Atualizar e Excluir). O `SQLiteOpenHelper` contém dados `ContentProvider`usados pelo , mas não está diretamente exposto a aplicativos de consumo.
As `CursorAdapter` passagens do cursor `ContentResolver` retornaram `ListView`pelo display em um . O `UriMatcher` é uma classe auxiliar que analisa URIs ao processar consultas.

O propósito de cada classe é descrito abaixo:

- **ContentProvider** &ndash; Implemente os métodos desta classe abstrata para expor dados. A API é disponibilizada para outras classes e aplicativos através do atributo Uri que é adicionado à definição de classe.

- **O SQLiteOpenHelper** &ndash; ajuda a implementar o armazenamento de `ContentProvider`dados SQLite que é exposto pelo .

- **UriMatcher** &ndash; `UriMatcher` Use `ContentProvider` em sua implementação para ajudar a gerenciar Uris que são usados para consultar o conteúdo.

- **ContentResolver** &ndash; Código de `ContentResolver` consumo usa `ContentProvider` um para acessar uma instância. As duas classes juntas cuidam dos problemas de comunicação entre processos, permitindo que os dados sejam facilmente compartilhados entre aplicativos. O código de `ContentProvider` consumo nunca cria uma classe explicitamente; em vez disso, os dados são acessados criando um `ContentProvider` cursor baseado em um Uri exposto pelo aplicativo.

- **CursorAdapter** &ndash; `CursorAdapter` Use `SimpleCursorAdapter` ou para exibir `ContentProvider`dados acessados através de um .

A `ContentProvider` API permite que os consumidores realizem uma variedade de operações sobre os dados, tais como:

- Consultar dados para retornar listas ou registros individuais.
- Modifique registros individuais.
- Adicione novos registros.
- Apagar registros.

Este documento contém um exemplo que `ContentProvider`usa um exemplo fornecido pelo sistema, bem `ContentProvider`como um simples exemplo somente de leitura que implementa um personalizado .
