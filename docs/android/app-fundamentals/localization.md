---
title: Localização do Android
description: Este documento apresenta os recursos de localização do SDK do Android e como acessá-los com o Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 076cadd16c3953ee4e06193190b59035ad57f2c1
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798599"
---
# <a name="android-localization"></a>Localização do Android

_Este documento apresenta os recursos de localização do SDK do Android e como acessá-los com o Xamarin._

## <a name="android-platform-features"></a>Recursos da plataforma Android

Esta seção descreve os recursos de localização principal do Android. Vá para o [próxima seção](#basics) para ver exemplos e código específico.

### <a name="locale"></a>Localidade

Os usuários escolher seu idioma no **Configurações > idioma & entrada**. Essa seleção controla o idioma exibido tanto configurações regionais usadas (por exemplo. para formatação de número e data).

A localidade atual pode ser consultada por meio do contexto atual `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Esse valor será um identificador de localidade que contém um código de idioma e um código de localidade, separados por um sublinhado. Para referência, aqui está uma [lista de locais de Java](http://www.oracle.com/technetwork/java/javase/locales-137662.html) e [localidades com suporte do Android por meio de StackOverflow](http://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Exemplos comuns incluem:

* `en_US` para inglês (EUA Statees)
* `es_ES` para Espanhol (Espanha)
* `ja_JP` para japonês (Japão)
* `zh_CN` para chinês (China)
* `zh_TW` para chinês (Taiwan)
* `pt_PT` para português (Portugal)
* `pt_BR` para português (Brasil)

### <a name="localechanged"></a>LOCALE_CHANGED

Android gera `android.intent.action.LOCALE_CHANGED` quando o usuário altera a seleção de idioma.

As atividades podem aceitar para lidar com isso, definindo o `android:configChanges` atributo na atividade, como este:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Noções básicas de internacionalização no Android

Estratégia de localização do Android tem as seguintes partes principais:

- Pastas de recurso para conter cadeias de caracteres localizadas, imagens e outros recursos.

- `GetText` método, que é usado para recuperar as cadeias de caracteres localizadas no código

- `@string/id` em arquivos AXML, para colocar automaticamente as cadeias de caracteres localizadas em layouts.

### <a name="resource-folders"></a>Pastas de recursos

Aplicativos Android gerenciam conteúdo a maioria das pastas de recursos, como:

* **layout** -contém os arquivos de layout AXML.
* **drawable** -contém imagens e outros recursos drawable.
* **valores** -contém cadeias de caracteres.
* **bruto** -contém arquivos de dados.

A maioria dos desenvolvedores já estão familiarizados com o uso de **dpi** sufixos no **drawable** directory para fornecer várias versões de uma imagem, permitindo que o Android escolha a versão correta para cada dispositivo. O mesmo mecanismo é usado para fornecer várias traduções de idioma, como sufixo diretórios de recursos com identificadores de idioma e cultura.

![Captura de tela de recursos/drawable e valores de recursos/pastas para vários identificadores culturais](localization-images/resources.png)

> [!NOTE]
> Ao especificar um idioma de nível superior como `es` apenas dois caracteres forem necessárias; no entanto, ao especificar uma localidade completa, o formato de nome de diretório requer um traço e minúsculas **r** para separar as duas partes, por exemplo **pt rBR** ou **zh-rCN**. Compare isso com o valor retornado no código, que tenha um sublinhado (por exemplo. `pt_BR`). Ambos são diferentes para o valor .NET `CultureInfo` classe usa, que tem um traço apenas (por exemplo. `pt-BR`). Mantenha essas diferenças em mente ao trabalhar com as plataformas de Xamarin.

#### <a name="stringsxml-file-format"></a>Formato de arquivo strings.XML

Um **valores** (por exemplo. **valores-es** ou **valores-pt-rBR**) deve conter um arquivo chamado **Strings.xml** que conterá o texto traduzido para essa localidade.

Cada cadeia de caracteres da traduzível é um elemento XML com a ID do recurso especificada como o `name` atributo e a cadeia de caracteres convertida como o valor:

```xml
<string name="app_name">TaskyL10n</string>
```

Você precisa de escape acordo com as regras normais de XML e o `name` deve ser uma ID de recurso Android válido (sem espaços ou traços). Aqui está um exemplo do arquivo (em inglês) de cadeias de caracteres padrão para o exemplo:

**values/Strings.xml**

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

O diretório espanhol **valores es** contém um arquivo com o mesmo nome (**Strings.xml**) que contém as traduções:

**values-es/Strings.xml**

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

![Captura de tela de vários valores pastas, cada uma contendo um arquivo Strings.xml](localization-images/values.png)

Com a configuração de arquivos de cadeias de caracteres, os valores traduzidos podem ser referenciados em layouts e código.

### <a name="axml-layout-files"></a>Arquivos de Layout AXML

Para fazer referência a cadeias de caracteres localizada em arquivos de layout, use o `@string/id` sintaxe. Este trecho XML de exemplo mostra `text` propriedades sendo definidas com localizadas (alguns outros atributos foram omitidos) de IDs de recurso:

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

Para recuperar as cadeias de caracteres traduzidas no código, use o `GetText` método e passe a ID de recurso:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Quantidade de cadeias de caracteres

Recursos de cadeia de caracteres Android também permitem que você crie *cadeias de caracteres de quantidade* que permitem que os tradutores fornecer diferentes traduções para quantidades diferentes, como:

* "Há 1 tarefa esquerda".
* "Há 2 tarefas ainda para fazer."

(em vez de um genérico "há esquerda tarefa n").

No **Strings.xml**

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

Para renderizar o uso de cadeia de caracteres completa o `GetQuantityString` método, passando a ID de recurso e o valor a ser exibida (que é passado duas vezes). O segundo parâmetro é usado pelo Android para determinar *que* `quantity` de cadeia de caracteres para usar, o terceiro parâmetro é o valor substituído realmente na cadeia de caracteres (ambos são necessárias).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Válido `quantity` opções são:

* zero
* um
* dois
* Alguns
* muitos
* outras

Eles são descritos mais detalhadamente o [documentos Android](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Se um determinado idioma não exigem tratamento 'special', aqueles `quantity` cadeias de caracteres serão ignoradas (por exemplo, inglês usa apenas `one` e `other`; especificando uma `zero` cadeia de caracteres não terá efeito, ela não será usada).

### <a name="images"></a>Imagens

Imagens localizadas seguem as mesmas regras que os arquivos de cadeias de caracteres: referenciadas no aplicativo de todas as imagens devem ser colocadas em **drawable** diretórios para que haja um fallback.

Imagens específicas da localidade devem ser inseridas em pastas drawable qualificadas como **drawable-es** ou **drawable ja** (especificadores de dpi também podem ser adicionados).

Nessa tela, quatro imagens são salvas no **drawable** diretório, mas apenas um deles, **flag.png**, foi localizado cópias em outros diretórios.

![Captura de tela de várias pastas drawable, cada uma contendo um ou mais arquivos. PNG localizada](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Outros tipos de recursos

Você também pode fornecer outros tipos de alternativa, os recursos específicos de linguagem incluindo layouts, animações e arquivos brutos. Isso significa que você pode fornecer um layout de tela específico para um ou mais dos seus idiomas de destino, por exemplo você pode criar um layout especificamente para alemão que permite que os rótulos de texto muito longo.

Android 4.2 introduziu suporte para [direita para idiomas de esquerda (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) se você definir a configuração de aplicativo `android:supportsRtl="true"`. O qualificador de recurso `"ldrtl"` pode ser incluído em um nome de diretório de códigos para conter layouts personalizados são projetados para exibir RTL.

Para obter mais informações sobre nomenclatura de diretório de recursos e fallback, consulte os documentos do Android para [fornecer recursos alternativos](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nome do aplicativo

O nome do aplicativo é fácil de localizar usando um `@string/id` para o `MainLauncher` atividade:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Idiomas da direita para esquerda (RTL)

Android 4.2 e mais recente fornece suporte completo para layouts RTL, descritos detalhadamente no [suporte nativo a RTL blog](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Ao usar o Android 4.2 (API nível 17) e mais recente, aligment valores podem ser especificados com `start` e `end` em vez de `left` e `right` (por exemplo `android:paddingStart`). Também há novas APIs como `LayoutDirection`, `TextDirection`, e `TextAlignment` para ajudar a criar telas adaptam para leitores RTL.

A captura de tela a seguir mostra o [localizada **Tasky** exemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em árabe:

[![Captura de tela do aplicativo Tasky em árabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

A próxima captura de tela mostra a [localizada **Tasky** exemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em hebraico:

[![Captura de tela do aplicativo Tasky em hebraico](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Texto RTL está localizado usando **Strings.xml** arquivos da mesma forma como texto LTR.

## <a name="testing"></a>Testes

Certifique-se de testar completamente a localidade padrão. O aplicativo falhará se os recursos padrão não podem ser carregados por algum motivo (ou seja, eles estão ausentes).

### <a name="emulator-testing"></a>Emulador de teste

Consulte do Google [testes em um emulador Android](https://developer.android.com/guide/topics/resources/localization.html#testing) seção para obter instruções sobre como configurar um emulador para uma localidade específica usando o shell ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Testes de dispositivos

Para testar em um dispositivo, altere o idioma no **configurações** aplicativo.
**Dica:** faça uma nota dos ícones e o local dos itens de menu para que você possa reverter a linguagem para a configuração original.


## <a name="summary"></a>Resumo

Este artigo aborda os conceitos básicos de localização de aplicativos Android usando a manipulação de recursos internos. Você pode aprender mais sobre i18n e L10n para iOS, Android e aplicativos de plataforma cruzada (incluindo xamarin. Forms) em [este guia de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Links relacionados

- [Tasky (localizado no código) (amostra)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localizando com recursos de Android](http://developer.android.com/guide/topics/resources/localization.html)
- [Visão geral de localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
- [Localização do xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localização do iOS](~/ios/app-fundamentals/localization/index.md)
