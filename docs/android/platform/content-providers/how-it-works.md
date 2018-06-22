---
title: Provedores de conteúdo como funcionam
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b4c674176be5af09d6b780d79923737a364d1591
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767119"
---
# <a name="how-content-providers-work"></a>Provedores de conteúdo como funcionam

Há duas classes envolvidas em uma `ContentProvider` interação:

- **ContentProvider** &ndash; implementa uma API que expõe um conjunto de dados de uma maneira padrão. Os principais métodos são consultas, Insert, Update e Delete.

- **ContentResolver** &ndash; um proxy estático que se comunica com um `ContentProvider` para acessar seus dados, ou de dentro do mesmo aplicativo de outro aplicativo.

Um provedor de conteúdo normalmente é apoiado por um banco de dados SQLite, mas a API significa que código de consumo não precisa saber nada sobre o SQL subjacente. Consultas são feitas por meio de um Uri usando constantes para fazer referência a nomes de coluna (para reduzir as dependências na estrutura de dados subjacente) e um `ICursor` é retornado para o código iterar.


## <a name="consuming-a-contentprovider"></a>Consumindo um ContentProvider

`ContentProviders` expor sua funcionalidade por meio de um Uri que é registrado no **AndroidManifest.xml** do aplicativo que publica os dados. Há uma convenção de onde o Uri e as colunas de dados que são expostas devem estar disponíveis como constantes para tornar mais fácil associar aos dados. Interno do Android `ContentProviders` tudo isso fornece classes de conveniência com constantes que fazem referência a estrutura de dados no [ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/) namespace.



### <a name="built-in-providers"></a>Provedores internos

Android oferece acesso a uma ampla gama de sistema e dados de usuário usando `ContentProviders`:

- *Navegador* &ndash; indicadores e o histórico de navegação (requer a permissão `READ_HISTORY_BOOKMARKS` e/ou `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; recentes chamadas feitas ou recebidas com o dispositivo.

- *Contatos* &ndash; obter informações da lista de contatos do usuário, incluindo as pessoas, telefones, fotos e grupos.

- *MediaStore* &ndash; conteúdo de dispositivo do usuário: (álbuns, artistas, gêneros, listas de reprodução) de áudio, imagens (incluindo miniaturas) e vídeo.

- *Configurações de* &ndash; preferências e configurações de dispositivo de todo o sistema.

- *UserDictionary* &ndash; conteúdo do dicionário definido pelo usuário usado para entrada de texto previsto.

- *Mensagem de voz* &ndash; histórico de mensagens de voz.



## <a name="classes-overview"></a>Visão geral de classes

As classes principais usadas para trabalhar com um `ContentProvider` são mostradas aqui:

[![Diagrama de classe do aplicativo do provedor de conteúdo e consomem interações de aplicativo](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Neste diagrama, o `ContentProvider` implementa consultas e registra o URI usado por outros aplicativos para localizar os dados. O `ContentResolver` atua como um proxy para o `ContentProvider` (consultar, inserir, atualizar e excluir métodos). O `SQLiteOpenHelper` contém dados usados pelo `ContentProvider`, mas ele não seja exposto diretamente para aplicativos que consomem.
O `CursorAdapter` passa o cursor retornado pelo `ContentResolver` para exibir em um `ListView`. O `UriMatcher` é uma classe auxiliar que analisa os URIs durante o processamento de consultas.

A finalidade de cada classe é descrita abaixo:

- **ContentProvider** &ndash; implementar métodos dessa classe abstrata para expor dados. A API é disponibilizada para outros aplicativos por meio do atributo de Uri que é adicionado à definição de classe e classes.

- **SQLiteOpenHelper** &ndash; ajuda a implementa o armazenamento de dados SQLite que é exposto pelo `ContentProvider`.

- **UriMatcher** &ndash; Use `UriMatcher` no seu `ContentProvider` implementação para ajudar a gerenciar os Uris que são usadas para consultar o conteúdo.

- **ContentResolver** &ndash; código de consumo usa um `ContentResolver` para acessar um `ContentProvider` instância. As duas classes juntos cuidam dos problemas de comunicação entre processos, permitindo que os dados sejam facilmente compartilhadas entre aplicativos. Código de consumo nunca cria um `ContentProvider` classe explicitamente; em vez disso, os dados são acessados por meio da criação de um cursor com base em um Uri exposto pelo `ContentProvider` aplicativo.

- **CursorAdapter** &ndash; Use `CursorAdapter` ou `SimpleCursorAdapter` para exibir os dados acessados por meio de um `ContentProvider`.

O `ContentProvider` API permite que os consumidores executar uma variedade de operações nos dados, tais como:

-  Consultar dados para retornar listas ou registros individuais.
-  Modificar registros individuais.
-  Adicione novos registros.
-  Exclua registros.

Este documento contém um exemplo que usa um sistema fornecido `ContentProvider`, bem como um exemplo simples de somente leitura que implementa um personalizado `ContentProvider`.

