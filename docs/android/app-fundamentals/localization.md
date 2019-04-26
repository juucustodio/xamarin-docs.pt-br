---
title: Localização do Android
description: Este documento apresenta os recursos de localização do SDK do Android e como acessá-los com o Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 1f3e6dcc81c75d12a1dbba8fefc62a85178c8c24
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019181"
---
# <a name="android-localization"></a>Localização do Android

_Este documento apresenta os recursos de localização do SDK do Android e como acessá-los com o Xamarin._

## <a name="android-platform-features"></a>Recursos da plataforma Android

Esta seção descreve os recursos de localização principal do Android. Vá para o [próxima seção](#basics) para ver exemplos e código específicos.

### <a name="locale"></a>Localidade

Os usuários escolher seu idioma no **Configurações > idioma & entrada**. Essa seleção controla o idioma exibido e configurações regionais usadas (por exemplo. para formatação de número e data).

A localidade atual pode ser consultada por meio do contexto atual `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Esse valor será um identificador de localidade que contém um código de idioma e um código de localidade, separados por um caractere de sublinhado. Para referência, aqui está uma [lista de localidades de Java](https://www.oracle.com/technetwork/java/javase/locales-137662.html) e [localidades com suporte do Android por meio do StackOverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Exemplos comuns incluem:

* `en_US` para inglês (Estados Unidos)
* `es_ES` Espanhol (Espanha)
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

- Pastas de recursos para conter cadeias de caracteres localizadas, imagens e outros recursos.

- `GetText` método, que é usado para recuperar cadeias de caracteres localizadas no código

- `@string/id` em arquivos AXML, para colocar automaticamente as cadeias de caracteres localizadas em layouts.

### <a name="resource-folders"></a>Pastas de recursos

Aplicativos Android gerenciam a maior parte do conteúdo nas pastas de recurso, como:

* **layout** -contém os arquivos de layout AXML.
* **drawable** -contém imagens e outros recursos desenháveis.
* **valores** -contém cadeias de caracteres.
* **brutos** -contém arquivos de dados.

A maioria dos desenvolvedores já estiver familiarizado com o uso de **dpi** sufixos sobre o **desenháveis** directory para fornecer várias versões de uma imagem, permitindo que o Android escolha a versão correta para cada dispositivo. O mesmo mecanismo é usado para fornecer várias traduções de idioma acrescentando diretórios de recursos com os identificadores de idioma e cultura.

![Captura de tela das pastas de recursos/drawable e recursos/valores de vários identificadores culturais](localization-images/resources.png)

> [!NOTE]
> Ao especificar um idioma de nível superior, como `es` apenas dois caracteres forem necessárias; no entanto, ao especificar uma localidade completa, o formato de nome de diretório requer um traço e minúscula **r** para separar as duas partes, por exemplo **rBR pt** ou **zh-rCN**. Compare isso com o valor retornado no código, que tem um caractere de sublinhado (por exemplo. `pt_BR`). Ambos são diferentes para o valor .NET `CultureInfo` classe usos, que tem um traço apenas (por exemplo. `pt-BR`). Mantenha essas diferenças em mente ao trabalhar em plataformas do Xamarin.

#### <a name="stringsxml-file-format"></a>Formato de arquivo strings. XML

Um localizada **valores** (por exemplo. **valores-es** ou **valores-pt-rBR**) deve conter um arquivo chamado **strings. XML** que conterá o texto traduzido para essa localidade.

Cada cadeia de caracteres traduzível é um elemento XML com o recurso de ID especificado como o `name` atributo e a cadeia de caracteres traduzida como o valor:

```xml
<string name="app_name">TaskyL10n</string>
```

Você precisará escapar de acordo com as regras normais de XML e o `name` deve ser uma ID de recurso válido do Android (sem espaços ou traços). Aqui está um exemplo do arquivo (em inglês) de cadeias de caracteres padrão para o exemplo:

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

O diretório de espanhol **valores es** contém um arquivo com o mesmo nome (**strings. XML**) que contém as traduções:

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

![Captura de tela de vários valores pastas, cada um contendo um arquivo strings. XML](localization-images/values.png)

Com a configuração de arquivos de cadeias de caracteres, os valores traduzidos podem ser referenciados em layouts e código.

### <a name="axml-layout-files"></a>Arquivos de Layout AXML

Para fazer referência a cadeias de caracteres localizada em arquivos de layout, use o `@string/id` sintaxe. Este trecho XML do exemplo mostra `text` propriedades sendo definidas com localizadas (alguns outros atributos foram omitidos) de IDs de recurso:

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

Para recuperar cadeias de caracteres traduzidas em código, use o `GetText` método e passar a ID de recurso:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Quantidade de cadeias de caracteres

Recursos de cadeia de caracteres Android também permitem que você crie *cadeias de caracteres de quantidade* que permitem que os tradutores fornecer traduções diferentes para diferentes quantidades, tais como:

* "Há 1 tarefa à esquerda."
* "Há 2 tarefas ainda para fazer."

(em vez de um genérico "há n tarefa (s) para a esquerda").

No **strings. XML**

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

Para renderizar o uso de cadeia de caracteres completa o `GetQuantityString` método, passando a ID de recurso e o valor a ser exibido (que é passado duas vezes). O segundo parâmetro é usado pelo Android para determinar *qual* `quantity` cadeia de caracteres para usar, o terceiro parâmetro é o valor substituído, na verdade, na cadeia de caracteres (ambos são necessárias).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Válido `quantity` opções são:

* zero
* um
* Dois
* Alguns
* muitos
* outras

São descritos mais detalhadamente os [docs Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Se um determinado idioma não exigir tratamento 'especial', aqueles `quantity` cadeias de caracteres serão ignoradas (por exemplo, inglês usa apenas `one` e `other`; especificando uma `zero` cadeia de caracteres não terá efeito, ele não será usado).

### <a name="images"></a>Imagens

Imagens localizadas seguem as mesmas regras que os arquivos de cadeias de caracteres: referenciadas no aplicativo de todas as imagens devem ser colocadas em **desenháveis** diretórios, portanto, não há um fallback.

Imagens específicas de localidade devem, em seguida, ser colocadas em pastas desenháveis qualificadas, como **desenháveis es** ou **desenháveis ja** (especificadores de dpi também podem ser adicionados).

Nesta captura de tela, quatro imagens são salvas na **desenháveis** directory, mas apenas um deles, **flag.png**, tenha localizada cópias em outros diretórios.

![Captura de tela de várias pastas desenháveis, cada um contendo um ou mais arquivos. PNG localizada](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Outros tipos de recursos

Você também pode fornecer outros tipos de alternativa, os recursos específicos de linguagem incluindo layouts, animações e arquivos brutos. Isso significa que você pode fornecer um layout de tela específica para um ou mais dos seus idiomas de destino, por exemplo você pode criar um layout especificamente para o alemão que permite que os rótulos de texto muito longo.

Introduziu o suporte para Android 4.2 [direita para idiomas de à esquerda (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) se você definir a configuração de aplicativo `android:supportsRtl="true"`. O qualificador de recurso `"ldrtl"` podem ser incluídos em um nome de diretório para conter os layouts personalizados que são projetados para a exibição RTL.

Para obter mais informações sobre nomenclatura de diretório de recursos e fallback, consulte documentos do Android para [fornecer recursos alternativos](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nome do aplicativo

O nome do aplicativo é fácil de localizar, usando um `@string/id` em para o `MainLauncher` atividade:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Idiomas da direita para esquerda (RTL)

Android 4.2 e mais recente oferece suporte completo para layouts RTL, descritos detalhadamente na [blog de suporte nativo de RTL](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Ao usar o Android 4.2 (API nível 17) e o alinhamento mais recente, valores podem ser especificados com `start` e `end` em vez de `left` e `right` (por exemplo `android:paddingStart`). Também há novas APIs, como `LayoutDirection`, `TextDirection`, e `TextAlignment` para ajudar a criar telas que se adaptam para leitores RTL.

A captura de tela a seguir mostra a [localizada **Tasky** exemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em árabe:

[![Captura de tela do aplicativo Tasky em árabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

A captura de tela a seguir mostra a [localizada **Tasky** exemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em hebraico:

[![Captura de tela do aplicativo Tasky em hebraico](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Texto RTL é localizado usando **strings. XML** arquivos da mesma forma como o texto LTR.

## <a name="testing"></a>Testes

Certifique-se de testar completamente a localidade padrão. Seu aplicativo falhará se os recursos padrão não podem ser carregados por algum motivo (ou seja, elas não existirem).

### <a name="emulator-testing"></a>Emulador de teste

Consulte do Google [testes em um emulador do Android](https://developer.android.com/guide/topics/resources/localization.html#testing) seção para obter instruções sobre como configurar um emulador para uma localidade específica usando o shell do ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Teste de dispositivos

Para testar em um dispositivo, altere o idioma na **configurações** aplicativo.
**Dica:** Faça uma observação dos ícones e o local dos itens de menu para que você possa reverter a linguagem para a configuração original.


## <a name="summary"></a>Resumo

Este artigo aborda os conceitos básicos de localização de aplicativos Android usando a manipulação de recursos internos. Você pode aprender mais sobre i18n e L10n para iOS, Android e aplicativos de plataforma cruzada (incluindo xamarin. Forms) em [este guia de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Links relacionados

- [Tasky (localizado no código) (amostra)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localização de recursos do Android](https://developer.android.com/guide/topics/resources/localization.html)
- [Visão geral de localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
- [Localização do xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localização no iOS](~/ios/app-fundamentals/localization/index.md)
