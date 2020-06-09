---
title: Localização no Android
description: Este documento apresenta os recursos de localização do SDK do Android e como acessá-los com o Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: b37f33e4f093c04e077529fbcb62567e4f702da4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84568512"
---
# <a name="android-localization"></a>Localização no Android

_Este documento apresenta os recursos de localização do SDK do Android e como acessá-los com o Xamarin._

## <a name="android-platform-features"></a>Recursos da plataforma Android

Esta seção descreve os principais recursos de localização do Android. Pule para a [próxima seção](#basics) para ver o código e os exemplos específicos.

### <a name="locale"></a>Localidade

Os usuários escolhem seu idioma nas **configurações > idioma & entrada**. Essa seleção controla o idioma exibido e as configurações regionais usadas (por exemplo, para formatação de data e número).

A localidade atual pode ser consultada por meio do contexto atual `Resources` :

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Esse valor será um identificador de localidade que contém um código de idioma e um código de localidade, separados por um sublinhado. Para referência, aqui está uma [lista de localidades Java](https://www.oracle.com/technetwork/java/javase/locales-137662.html) e [localidades com suporte do Android por meio de StackOverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Exemplos comuns incluem:

- `en_US`para inglês (Estados Unidos)
- `es_ES`para espanhol (Espanha)
- `ja_JP`para japonês (Japão)
- `zh_CN`para chinês (China)
- `zh_TW`para chinês (Taiwan)
- `pt_PT`para Português (Portugal)
- `pt_BR`para Português (Brasil)

### <a name="locale_changed"></a>LOCALE_CHANGED

O Android é gerado `android.intent.action.LOCALE_CHANGED` quando o usuário altera sua seleção de idioma.

As atividades podem optar por lidar com isso definindo o `android:configChanges` atributo na atividade, da seguinte maneira:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics"></a>

## <a name="internationalization-basics-in-android"></a>Noções básicas sobre internacionalização no Android

A estratégia de localização do Android tem as seguintes partes principais:

- Pastas de recursos para conter cadeias de caracteres localizadas, imagens e outros recursos.

- `GetText`método, que é usado para recuperar cadeias de caracteres localizadas no código

- `@string/id`em arquivos AXML, para posicionar automaticamente cadeias de caracteres localizadas em layouts.

### <a name="resource-folders"></a>Pastas de recursos

Os aplicativos Android gerenciam a maior parte do conteúdo em pastas de recursos, como:

- **layout** -contém arquivos de layout AXML.
- **desenhável** -contém imagens e outros recursos que são desenháveis.
- **valores** -contém cadeias de caracteres.
- **RAW** -contém arquivos de dados.

A maioria dos desenvolvedores já está familiarizada com o uso de sufixos de **dpi** no diretório **desenhado** para fornecer várias versões de uma imagem, permitindo que o Android escolha a versão correta para cada dispositivo. O mesmo mecanismo é usado para fornecer várias traduções de idioma por meio de sufixo de diretórios de recursos com identificadores de idioma e cultura.

![Captura de tela de pastas de recursos/empates e de recursos/valores para vários identificadores culturais](localization-images/resources.png)

> [!NOTE]
> Ao especificar uma linguagem de nível superior como `es` apenas dois caracteres são necessários; no entanto, ao especificar uma localidade completa, o formato de nome de diretório requer um traço e um **r** minúsculo para separar as duas partes, por exemplo, **pt-RBR** ou **zh-rCN**. Compare isso com o valor retornado no código, que tem um sublinhado (por exemplo, `pt_BR`). Ambos são diferentes do valor que a classe .NET `CultureInfo` usa, que tem apenas um traço (por exemplo, `pt-BR`). Tenha essas diferenças em mente ao trabalhar em plataformas do Xamarin.

#### <a name="stringsxml-file-format"></a>Formato de arquivo Strings. xml

Um diretório de **valores** localizados (por exemplo, **os valores-es** ou **Values-pt-rBR**) devem conter um arquivo chamado **Strings. xml** que conterá o texto traduzido para essa localidade.

Cada cadeia de caracteres traduzível é um elemento XML com a ID de recurso especificada como o `name` atributo e a cadeia de caracteres traduzida como o valor:

```xml
<string name="app_name">TaskyL10n</string>
```

Você precisa escapar de acordo com as regras XML normais e `name` deve ser uma ID de recurso do Android válida (sem espaços ou traços). Aqui está um exemplo do arquivo de cadeias de caracteres padrão (inglês) para o exemplo:

**Values/Strings. xml**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

Os valores do diretório espanhol **-es** contém um arquivo com o mesmo nome (**Strings. xml**) que contém as traduções:

**Values-es/Strings. xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![Captura de tela de pastas de vários valores, cada uma contendo um arquivo Strings. xml](localization-images/values.png)

Com os arquivos de cadeias de caracteres configurados, os valores traduzidos podem ser referenciados em layouts e código.

### <a name="axml-layout-files"></a>Arquivos de layout AXML

Para fazer referência a cadeias de caracteres localizadas em arquivos de layout, use a `@string/id` sintaxe. Este trecho de código XML do exemplo mostra `text` as propriedades que estão sendo definidas com IDs de recursos localizados (alguns outros atributos foram omitidos):

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>Método GetText

Para recuperar cadeias de caracteres traduzidas no código, use o `GetText` método e passe a ID do recurso:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Cadeias de caracteres de quantidade

Recursos de cadeia de caracteres do Android também permitem criar *cadeias* de caracteres de quantidade que permitem que os tradutores forneçam traduções diferentes para diferentes quantidades, como:

- "Há 1 tarefa restante".
- "Há duas tarefas ainda pendentes."

(em vez de um genérico "há n tarefa (s) restante").

No **Strings. xml**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

Para renderizar a cadeia de caracteres completa, use o `GetQuantityString` método, passando a ID do recurso e o valor a ser exibido (que é passado duas vezes). O segundo parâmetro é usado pelo Android para determinar *qual* `quantity` cadeia de caracteres usar, o terceiro parâmetro é o valor realmente substituído na cadeia de caracteres (ambos são obrigatórios).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

As `quantity` opções válidas são:

- zero
- one
- dois
- pouco
- many
- outros

Eles são descritos mais detalhadamente nos [documentos do Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Se um determinado idioma não exigir manipulação "especial", essas `quantity` cadeias de caracteres serão ignoradas (por exemplo, somente `one` o inglês usa e `other` ; a especificação de uma cadeia de caracteres não terá `zero` nenhum efeito, ela não será usada).

### <a name="images"></a>Imagens

Imagens localizadas seguem as mesmas regras que os arquivos de cadeias de caracteres: todas as imagens referenciadas no aplicativo devem ser colocadas em diretórios **desenháveis** para que haja um fallback.

As imagens específicas de localidade devem ser colocadas em pastas desenhadas qualificadas, como **Drawable-es** ou **Drawable-ja** (os especificadores de DPI também podem ser adicionados).

Nesta captura de tela, quatro imagens são salvas no diretório **desenhável** , mas apenas uma, **Flag. png**, tem cópias localizadas em outros diretórios.

![Captura de tela de várias pastas desenháveis, cada uma contendo um ou mais arquivos. png localizados](localization-images/drawable.png)

#### <a name="other-resource-types"></a>Outros tipos de recursos

Você também pode fornecer outros tipos de recursos alternativos específicos do idioma, incluindo layouts, animações e arquivos brutos. Isso significa que você pode fornecer um layout de tela específico para um ou mais dos seus idiomas de destino, por exemplo, você poderia criar um layout especificamente para alemão que permita rótulos de texto muito longos.

O Android 4,2 introduziu o suporte para [idiomas da direita para a esquerda (RTL)](https://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) se você definir a configuração do aplicativo `android:supportsRtl="true"` . O qualificador de recursos `"ldrtl"` pode ser incluído em um nome de diretório para conter layouts personalizados que são criados para exibição DPE.

Para obter mais informações sobre a nomenclatura e o fallback do diretório de recursos, consulte os documentos do Android para [fornecer recursos alternativos](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).

### <a name="app-name"></a>Nome do aplicativo

O nome do aplicativo é fácil de localizar usando um `@string/id` no para a `MainLauncher` atividade:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Idiomas da direita para a esquerda (RTL)

O Android 4,2 e mais recente fornece suporte completo para layouts RTL, descritos em detalhes no [blog de suporte nativo DPE](https://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Ao usar o Android 4,2 (API nível 17) e mais recente, os valores de alinhamento podem ser especificados com `start` e `end` em vez de `left` e `right` (por exemplo `android:paddingStart` ). Também há novas APIs como `LayoutDirection` , `TextDirection` e `TextAlignment` para ajudar a criar telas que se adaptem a leitores DPE.

A captura de tela a seguir mostra o [exemplo de **tarefa** localizada](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em árabe:

[![Captura de tela do aplicativo de tarefa em árabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

A próxima captura de tela mostra o [exemplo de **tarefa** localizada](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em Hebraico:

[![Captura de tela do aplicativo de tarefas em Hebraico](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

O texto RTL é localizado usando arquivos **String. xml** da mesma forma que o texto ltr.

## <a name="testing"></a>Testando

Certifique-se de testar exaustivamente a localidade padrão. Seu aplicativo falhará se os recursos padrão não puderem ser carregados por algum motivo (ou seja, estiverem ausentes).

### <a name="emulator-testing"></a>Testes de emulador

Consulte os testes do Google [em uma seção Android Emulator](https://developer.android.com/guide/topics/resources/localization.html#testing) para obter instruções sobre como definir um emulador para uma localidade específica usando o Shell do ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Teste de dispositivo

Para testar um dispositivo, altere o idioma no aplicativo **configurações** .

> [!TIP]
> Anote os ícones e o local dos itens de menu para que você possa reverter o idioma para a configuração original.

## <a name="summary"></a>Resumo

Este artigo aborda as noções básicas de localização de aplicativos Android usando a manipulação interna de recursos. Você pode aprender mais sobre o i18n e o l10n para aplicativos iOS, Android e multiplataforma (incluindo Xamarin. Forms) neste [Guia de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Links relacionados

- [Tarefa (localizada no código) (exemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localização do Android com recursos](https://developer.android.com/guide/topics/resources/localization.html)
- [Visão geral da localização entre plataformas](~/cross-platform/app-fundamentals/localization.md)
- [Localização do Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localização no iOS](~/ios/app-fundamentals/localization/index.md)
