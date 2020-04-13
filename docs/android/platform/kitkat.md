---
title: Características do KitKat
description: O Android 4.4 (KitKat) vem carregado com uma cornucópia de recursos para usuários e desenvolvedores. Este guia destaca vários desses recursos e fornece exemplos de código e detalhes de implementação para ajudá-lo a aproveitar ao máximo o KitKat.
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 43061272f3d3486926f38af792ee3b9df0c53670
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027236"
---
# <a name="kitkat-features"></a>Características do KitKat

_O Android 4.4 (KitKat) vem carregado com uma cornucópia de recursos para usuários e desenvolvedores. Este guia destaca vários desses recursos e fornece exemplos de código e detalhes de implementação para ajudá-lo a aproveitar ao máximo o KitKat._

## <a name="overview"></a>Visão geral

O Android 4.4 (API Nível 19), também conhecido como "KitKat", foi lançado no final de 2013. KitKat oferece uma variedade de novos recursos e melhorias, incluindo:

- [Experiência](#user_experience) &ndash; do usuário Animações fáceis com estrutura de transição, status translúcido e barras de navegação e modo imersivo em tela cheia ajudam a criar uma melhor experiência para o usuário.

- [Conteúdo](#user_content) &ndash; do usuário Gerenciamento de arquivos do usuário simplificado com a estrutura de acesso ao armazenamento; imprimir imagens, sites e outros conteúdos é mais fácil com APIs de impressão melhoradas.

- [Hardware](#hardware) &ndash; Transforme qualquer aplicativo em um cartão NFC com emulação de cartão baseada em host NFC; executar sensores de `SensorManager` baixa potência com o .

- [Ferramentas](#developer_tools) &ndash; de desenvolvedor Aplicativos screencast em ação com o cliente Android Debug Bridge, disponível como parte do Android SDK.

Este guia fornece orientações para migrar um aplicativo Xamarin.Android existente para KitKat, bem como uma visão geral de alto nível do KitKat para desenvolvedores Xamarin.Android.

## <a name="requirements"></a>Requisitos

Para desenvolver aplicativos Xamarin.Android usando KitKat, você precisa de *Xamarin.Android 4.11.0* ou superior e Android 4.4 (API Nível 19) instalado através do Android SDK Manager, conforme ilustrado pela seguinte captura de tela:

[![Selecionando o Android 4.4 no Android SDK Manager](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>Migrando seu aplicativo para KitKat

Esta seção fornece alguns itens de primeira resposta para ajudar na transição de aplicativos existentes para o Android 4.4.

### <a name="check-system-version"></a>Verificar a versão do sistema

Se um aplicativo precisar ser compatível com versões mais antigas do Android, certifique-se de envolver qualquer código específico do KitKat em uma verificação de versão do sistema, conforme ilustrado pela amostra de código abaixo:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>Loteamento de alarmes

O Android usa serviços de alarme para acordar um aplicativo em segundo plano em um horário especificado. KitKat leva isso um passo adiante, loteando alarmes para preservar a energia. Isso significa que, em vez de acordar cada aplicativo em um momento exato, o KitKat prefere agrupar vários aplicativos que são registrados para acordar durante o mesmo intervalo de tempo, e acordá-los ao mesmo tempo.
Para dizer ao Android para acordar um aplicativo `SetWindow` durante [`AlarmManager`](xref:Android.App.AlarmManager)um intervalo de tempo especificado, ligue para o , passando no tempo mínimo e máximo, em milissegundos, que pode transcorrer antes que o aplicativo seja acordado, e a operação para realizar no despertar.
O código a seguir fornece um exemplo de um aplicativo que precisa ser acordado entre meia hora e uma hora a partir do momento em que a janela é definida:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

Para continuar acordando um aplicativo `SetExact`em um momento exato, use, passando a hora exata que o aplicativo deve ser acordado, e a operação para realizar:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat não permite mais que você defina um alarme repetitivo exato. Aplicações que usam[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
e exigir alarmes exatos para funcionar agora precisará acionar cada alarme manualmente.

### <a name="external-storage"></a>Armazenamento externo

O armazenamento externo agora é dividido em dois tipos - armazenamento exclusivo do seu aplicativo e dados compartilhados por vários aplicativos. Ler e escrever para a localização específica do seu aplicativo sobre armazenamento externo não requer permissões especiais. Interagir com dados sobre armazenamento compartilhado `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` agora requer a ou permissão. Os dois tipos podem ser classificados como tal:

- Se você está recebendo um caminho de arquivo `Context` ou diretório chamando um método em - por exemplo,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  Ou[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
  - seu aplicativo não requer permissões extras.

- Se você está recebendo um caminho de arquivo ou diretório `Environment` acessando uma propriedade ou chamando um método, como[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  Ou[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
  , seu aplicativo `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` requer a ou permissão.

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE`implica `READ_EXTERNAL_STORAGE` a permissão, então você só precisa definir uma permissão.

### <a name="sms-consolidation"></a>Consolidação de SMS

KitKat simplifica a troca de mensagens para o usuário, agregando todo o conteúdo de SMS em um aplicativo padrão selecionado pelo usuário. O desenvolvedor é responsável por tornar o aplicativo selecionável como o aplicativo de mensagens padrão, e se comportar adequadamente em código e na vida, se o aplicativo não for selecionado. Para obter mais informações sobre a transição do seu aplicativo SMS para KitKat, consulte o guia [Getting Your SMS Ready for KitKat](https://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) do Google.

### <a name="webview-apps"></a>Aplicativos WebView

[O WebView](xref:Android.Webkit.WebView) tem uma transformação no KitKat. A maior mudança é a segurança `WebView`adicional para carregar conteúdo em um . Embora a maioria dos aplicativos direcionados a versões de API mais antigas deve funcionar como esperado, testar aplicativos que usam a `WebView` classe é altamente recomendado. Para obter mais informações sobre as APIs afetadas do WebView, consulte o Android Migrando para o WebView na documentação [do Android 4.4.](https://developer.android.com/guide/webapps/migrating.html)

<a name="user_experience" />

## <a name="user-experience"></a>Experiência do Usuário

KitKat vem com várias novas APIs para melhorar a experiência do usuário, incluindo a nova estrutura de transição para lidar com animações de propriedade e uma opção translúcida de IU para tema. Essas mudanças estão cobertas abaixo.

### <a name="transition-framework"></a>Marco de Transição

A estrutura de transição torna as animações mais fáceis de implementar. KitKat permite que você execute uma animação de propriedade simples com apenas uma linha de código ou personalize transições usando *Cenas*.

#### <a name="simple-property-animation"></a>Animação de propriedade simples

A nova biblioteca Android Transitions simplifica o código por trás das animações de propriedade. A estrutura permite que você execute animações simples com código mínimo. Por exemplo, a seguinte amostra de código usa[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
para animar a exibição e a ocultação de um: `TextView`

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

O exemplo acima usa a estrutura de transição para criar uma transição padrão automática entre os valores de propriedade que mudam. Como a animação é manuseada por uma única linha de código, você `BeginDelayedTransition` pode facilmente tornar isso compatível com versões mais antigas do Android, envolvendo a chamada em uma verificação de versão do sistema. Consulte a [seção Migrar seu aplicativo para KitKat](#Migrating_Your_App_to_KitKat) para obter mais informações.

A captura de tela abaixo mostra o aplicativo antes da animação:

[![Captura de tela do aplicativo antes da animação começar](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

A captura de tela abaixo mostra o aplicativo após a animação:

[![Captura de tela do aplicativo após a animação ser concluída](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

Você pode obter mais controle sobre a transição com Scenes, que são cobertos na próxima seção.

#### <a name="android-scenes"></a>Cenas do Android

[Cenas](xref:Android.Transitions.Scene) foram introduzidas como parte da estrutura de transição para dar ao desenvolvedor mais controle sobre animações. As cenas criam uma área dinâmica na ui: você especifica um contêiner e várias versões, ou "cenas", para o conteúdo XML dentro do contêiner, e o Android faz o resto do trabalho para animar as transições entre as cenas. O Android Scenes permite construir animações complexas com o mínimo de trabalho no lado do desenvolvimento.

O elemento de UI estática que abriga o conteúdo dinâmico é chamado de *recipiente* ou *base de cena*. O exemplo abaixo usa o `RelativeLayout` Android `container`Designer para criar um chamado :

[![Usando o Android Designer para criar um contêiner RelativeLayout](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

O layout da amostra também `sceneButton` define `container`um botão chamado abaixo do . Este botão acionará a transição.

O conteúdo dinâmico dentro do contêiner requer dois novos layouts android. Esses layouts especificam apenas o código *dentro* do contêiner.
O código de exemplo abaixo define um layout chamado *Scene1* que contém dois campos de texto lendo "Kit" e "Kat" respectivamente, e um segundo layout chamado *Scene2* que contém os mesmos campos de texto invertidos. O XML é o seguinte:

 **Cena1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

O exemplo `merge` acima usa para tornar o código de exibição mais curto e simplificar a hierarquia de exibição. Você pode ler `merge` mais sobre layouts [aqui](https://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html).

Uma cena é criada [`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*)chamando , passando no objeto do contêiner, o ID `Context`de recurso do arquivo de layout da Cena e a corrente , conforme ilustrado pelo exemplo de código abaixo:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

Clicando no botão vira entre as duas cenas, que o Android anima com os valores de transição padrão:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

A captura de tela abaixo ilustra a cena antes da animação:

[![Captura de tela do aplicativo antes da animação começar](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

A captura de tela abaixo ilustra a cena após a animação:

[![Captura de tela do aplicativo após a animação ser concluída](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> Há um [bug conhecido](https://code.google.com/p/android/issues/detail?id=62450) na biblioteca Android Transitions `GetSceneForLayout` que faz com que cenas criadas usando para quebrar quando um usuário navega através de uma Atividade pela segunda vez. Uma solução java é descrita [aqui.](http://www.doubleencore.com/2013/11/new-transitions-framework/)

##### <a name="custom-transitions-in-scenes"></a>Transições personalizadas em cenas

Uma transição personalizada pode ser definida em `transition` um `Resources`arquivo de recurso xml no diretório abaixo, conforme ilustrado pela captura de tela abaixo:

[![Localização do arquivo transition.xml em Resources/diretório de transição](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

A amostra de código a seguir define uma transição que anima por 5 segundos e usa o [interpolador de superação](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

A transição é criada na Atividade usando o [TransitionInflater](xref:Android.Transitions.TransitionInflater), conforme ilustrado pelo código abaixo:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

A nova transição é `Go` então adicionada à chamada que inicia a animação:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>UI translúcida

KitKat lhe dá mais controle sobre o tema do seu aplicativo com status translúcido opcional e barras de navegação. Você pode alterar a translucidez dos elementos de IA do sistema no mesmo arquivo XML que você usa para definir o seu tema android. KitKat introduz as seguintes propriedades:

- `windowTranslucentStatus`- Quando definido como verdadeiro, torna a barra de status superior translúcida.

- `windowTranslucentNavigation`- Quando definido como verdadeiro, torna a barra de navegação inferior translúcida.

- `fitsSystemWindows`- Definir a barra superior ou inferior para transcluent desloca o conteúdo sob os elementos de ida e volta transparentes por padrão. Definir esta `true` propriedade é uma maneira simples de evitar que o conteúdo se sobrepor com os elementos de interface do sistema translúcido.

O código a seguir define um tema com status translúcido e barras de navegação:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

A captura de tela abaixo mostra o tema acima com status translúcido e barras de navegação:

[![Exemplo de captura de tela de aplicativo com status translúcido e barras de navegação](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>Conteúdo do Usuário

### <a name="storage-access-framework"></a>Estrutura de acesso ao armazenamento

O Storage Access Framework (SAF) é uma nova maneira de os usuários interagirem com conteúdo armazenado, como imagens, vídeos e documentos. Em vez de apresentar aos usuários um diálogo para escolher um aplicativo para lidar com o conteúdo, o KitKat abre uma nova interface do usuário que permite que os usuários acessem seus dados em um local agregado. Uma vez escolhido o conteúdo, o usuário retornará ao aplicativo que solicitou o conteúdo, e a experiência do aplicativo continuará normalmente.

Essa mudança requer duas ações do lado do desenvolvedor: primeiro, os aplicativos que exigem conteúdo dos provedores precisam ser atualizados para uma nova maneira de solicitar conteúdo. Em segundo lugar, aplicativos `ContentProvider` que escrevem dados para uma necessidade de ser modificado para usar o novo framework. Ambos os cenários dependem do novo[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API.

#### <a name="documentsprovider"></a>Provedor de documentos

No KitKat, as `ContentProviders` interações com `DocumentsProvider` são abstraídas com a classe. Isso significa que a SAF não se importa onde os dados `DocumentsProvider` estão fisicamente, desde que sejam acessíveis através da API. Provedores locais, serviços em nuvem e dispositivos de armazenamento externo usam a mesma interface, e são tratados da mesma forma, fornecendo ao usuário e ao desenvolvedor um lugar para interagir com o conteúdo do usuário.

Esta seção abrange como carregar e salvar conteúdo com o Storage Access Framework.

#### <a name="request-content-from-a-provider"></a>Solicitar conteúdo de um provedor

Podemos dizer ao KitKat que queremos escolher conteúdo `ActionOpenDocument` usando a UI SAF com a Intenção, o que significa que queremos nos conectar a todos os provedores de conteúdo disponíveis para o dispositivo. Você pode adicionar alguma filtragem a `CategoryOpenable`esta Intenção especificando , o que significa que apenas o conteúdo que pode ser aberto (ou seja, conteúdo acessível e utilizável) será devolvido. KitKat também permite filtragem `MimeType`de conteúdo com o . Por exemplo, o código abaixo filtra os resultados `MimeType`da imagem especificando a imagem:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

A `StartActivityForResult` chamada lança a UI SAF, que o usuário pode navegar para escolher uma imagem:

[![Exemplo de captura de tela de um aplicativo usando o Storage Access Framework para navegar em uma imagem](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

Depois que o usuário `OnActivityResult` tiver `Android.Net.Uri` escolhido uma imagem, retorna o arquivo escolhido. A amostra de código abaixo exibe a seleção de imagens do usuário:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>Escreva conteúdo para um provedor

Além de carregar conteúdo da SAF UI, o KitKat `ContentProvider` também permite `DocumentProvider` que você salve conteúdo para qualquer um que implemente a API. Salvar conteúdo `Intent` usa `ActionCreateDocument`um com:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

A amostra de código acima carrega a UI SAF, permitindo que o usuário altere o nome do arquivo e selecione um diretório para abrigar o novo arquivo:

[![Captura de tela do usuário mudando o nome do arquivo para NewDoc no diretório Downloads](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

Quando o usuário **pressiona Salvar**, `OnActivityResult` é passado o `Android.Net.Uri` arquivo recém-criado, que pode ser acessado com `data.Data`. O uri pode ser usado para transmitir dados para o novo arquivo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

Note que[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
retorna `System.IO.Stream`a , para que todo o processo de streaming possa ser escrito em .NET.

Para obter mais informações sobre o carregamento, criação e edição de conteúdo com o Storage Access Framework, consulte a [documentação](https://developer.android.com/guide/topics/providers/document-provider.html)do Android para o Storage Access Framework .

### <a name="printing"></a>Imprimindo

O conteúdo de impressão é simplificado no KitKat com a introdução dos [Serviços de Impressão](xref:Android.PrintServices) e `PrintManager`. KitKat também é a primeira versão da API a aproveitar totalmente as [APIs do serviço de Cloud Print do Google](https://developers.google.com/cloud-print/) usando o aplicativo Google Cloud [Print](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint).
A maioria dos dispositivos que são enviados com KitKat baixam automaticamente o aplicativo Google Cloud Print e o [HP Print Service Plugin](https://play.google.com/store/apps/details?id=com.hp.android.printservice)quando se conectam pela primeira vez ao WiFi. Um usuário pode verificar as configurações de impressão do seu dispositivo navegando para **Configurações > Sistema > Impressão**:

[![Exemplo de captura de tela da tela de configurações de impressão](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> Embora as APIs de impressão estejam configuradas para funcionar com o Google Cloud Print por padrão, o Android ainda permite que os desenvolvedores preparem conteúdo impresso usando as novas APIs e enviem para outros aplicativos para lidar com a impressão.

#### <a name="printing-html-content"></a>Impressão de conteúdo HTML

KitKat cria automaticamente [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) um para `WebView.CreatePrintDocumentAdapter`uma visualização web com . Imprimir conteúdo da Web é [`WebViewClient`](xref:Android.Webkit.WebViewClient) um esforço coordenado entre um que espera que o conteúdo HTML seja carregado e permite que a Atividade saiba para disponibilizar `Print`a `PrintManager`opção de impressão no menu de opções, e a Atividade, que aguarda o usuário selecionar a opção Imprimir e chamadas no . Esta seção abrange a configuração básica necessária para imprimir conteúdo HTML na tela.

Observe que o carregamento e a impressão de conteúdo da Web exigem a permissão da Internet:

[![Definindo a permissão da Internet nas opções do aplicativo](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>Item do menu de impressão

A opção de impressão aparecerá normalmente no menu de [opções](https://developer.android.com/guide/topics/ui/menus.html#options-menu)da Atividade .
O menu de opções permite que os usuários realizem ações em uma atividade. Está no canto superior direito da tela, e se parece com isso:

[![Exemplo de captura de tela do item do menu Print exibido no canto superior direito da tela](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

Itens adicionais do menu podem ser definidos no diretório do *menu*em *Resources*. O código abaixo define um item de menu de exemplo chamado [Imprimir](xref:Android.Print.PrintManager):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

A interação com o menu de `OnCreateOptionsMenu` `OnOptionsItemSelected` opções na Atividade acontece através dos métodos.
`OnCreateOptionsMenu`é o lugar para adicionar novos itens de menu, como a opção Imprimir, do diretório de recursos do *menu.*
`OnOptionsItemSelected`ouve o usuário selecionando a opção Imprimir no menu e começa a imprimir:

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

O código acima também define `dataLoaded` uma variável chamada para acompanhar o status do conteúdo HTML. O `WebViewClient` definirá essa variável como verdadeira quando todo o conteúdo tiver sido carregado, para que a Atividade saiba adicionar o item do menu Imprimir ao menu de opções.

##### <a name="webviewclient"></a>WebViewClient

O trabalho `WebViewClient` do é garantir `WebView` que os dados no está totalmente carregado antes que `OnPageFinished` a opção de impressão apareça no menu, o que ele faz com o método. `OnPageFinished`ouve o conteúdo da Web para terminar o carregamento e `InvalidateOptionsMenu`diz à Atividade para recriar seu menu de opções com :

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished`também define `dataLoaded` o `true`valor `OnCreateOptionsMenu` para , para que possa recriar o menu com a opção Imprimir no lugar.

##### <a name="printmanager"></a>PrintManager

O exemplo do código a `WebView`seguir imprime o conteúdo de a:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`toma como argumentos: um nome para o trabalho de impressão ("MyWebPage" neste exemplo), um[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
que gera o documento de impressão a partir do conteúdo, e[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(no`null` exemplo acima). Você pode `PrintAttributes` especificar para ajudar a definir o conteúdo na página impressa, embora os atributos padrão devem lidar com a maioria dos cenários.

Chamar `Print` carrega a iu de impressão, que lista opções para o trabalho de impressão. A ui dá aos usuários a opção de imprimir ou salvar o conteúdo HTML em um PDF, conforme ilustrado pelas capturas de tela abaixo:

[![Captura de tela de PrintHtmlActivity exibindo o menu Imprimir](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![Captura de tela do PrintHtmlActivity exibindo o menu Salvar como PDF](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>Hardware

KitKat adiciona várias APIs para acomodar novos recursos do dispositivo. As mais notáveis são a Emulação de `SensorManager`Cartão Baseada em Host e a nova .

### <a name="host-based-card-emulation-in-nfc"></a>Emulação de cartão baseada em host em NFC

O Host-Based Card Emulation (HCE) permite que os aplicativos se comportem como cartões NFC ou leitores de cartões NFC sem depender do Elemento Seguro proprietário da operadora. Antes de configurar o HCE, certifique-se `PackageManager.HasSystemFeature`de que o HCE está disponível no dispositivo com:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

O HCE exige que tanto `Nfc` o recurso HCE quanto `AndroidManifest.xml`a permissão sejam registrados no aplicativo:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![Definindo a permissão NFC nas opções de aplicativos](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

Para funcionar, o HCE tem que ser capaz de ser executado em segundo plano, e ele tem que começar quando o usuário faz uma transação NFC, mesmo que o aplicativo usando HCE não esteja sendo executado. Podemos fazer isso escrevendo o código `Service`HCE como um . Um serviço HCE `HostApduService` implementa a interface, que implementa os seguintes métodos:

- *ProcessCommandApdu* - Uma Unidade de Dados de Protocolo de Aplicativo (APDU) é o que é enviado entre o Leitor NFC e o Serviço HCE. Este método consome uma ADPU do leitor e retorna uma unidade de dados em resposta.

- *OnDesativado* `HostAdpuService` - O é desativado quando o Serviço HCE não está mais se comunicando com o Leitor NFC.

Um Serviço HCE também precisa ser registrado no manifesto do aplicativo e decorado com as permissões adequadas, filtro de intenção e metadados. O código a seguir `HostApduService` é um exemplo de `Service` um registrado com o Manifesto do Android usando o atributo (para obter mais informações sobre atributos, consulte o guia Xamarin [Working with Android Manifest):](~/android/platform/android-manifest.md)

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

O Serviço acima fornece uma maneira para o leitor DE NFC interagir com o aplicativo, mas o leitor NFC ainda não tem como saber se este Serviço está emulando o cartão NFC que precisa digitalizar. Para ajudar o leitor nfc a identificar o Serviço, podemos atribuir ao Serviço um *ID de aplicativo (AID)* exclusivo . Especificamos um AID, juntamente com outros metadados sobre o Serviço HCE, em um arquivo de recurso xml registrado com o atributo `MetaData` (veja exemplo de código acima). Este arquivo de recursos especifica um ou mais filtros AID - seqüências de identificadores exclusivas no formato hexadecimal que correspondem aos AIDs de um ou mais dispositivos leitores NFC:

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

Além dos filtros AID, o arquivo de recursos xml também fornece uma descrição voltada para o usuário do Serviço HCE, especifica um grupo AID (aplicativo de pagamento versus "outro") e, no caso de um aplicativo de pagamento, um banner de 260x96 dp para exibir ao usuário.

A configuração descrita acima fornece os blocos básicos de construção para um aplicativo que emula um cartão NFC. O próprio NFC requer várias etapas e testes adicionais para configurar. Para obter mais informações sobre a Emulação de Cartão baseada em Host, consulte o [portal de documentação](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)do Android .
Para obter mais informações sobre o uso de NFC com Xamarin, confira as [amostras de Xamarin NFC](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample).

### <a name="sensors"></a>Sensores

KitKat fornece acesso aos sensores [`SensorManager`](xref:Android.Hardware.SensorManager)do dispositivo através de um .
O `SensorManager` permite que o SISTEMA OPERACIONAL agende a entrega de informações do sensor para um aplicativo em lotes, preservando a vida útil da bateria.

KitKat também é fornecido com dois novos tipos de sensores para rastrear os passos do usuário. Estes são baseados em acelerômetro e incluem:

- *StepDetector* - O aplicativo é notificado/acordado quando o usuário dá um passo, e o detector fornece um valor de tempo para quando a etapa ocorreu.

- *StepCounter* - Acompanhe o número de passos que o usuário tomou desde que o sensor foi registrado *até a reinicialização do próximo dispositivo*.

A captura de tela abaixo mostra o contador de passos em ação:

[![Captura de tela do aplicativo SensorsActivity exibindo um contador de passos](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

Você pode `SensorManager` criar `GetSystemService(SensorService)` um chamando e `SensorManager`lançando o resultado como um . Para usar o contador `GetDefaultSensor` de `SensorManager`passos, ligue para o . Você pode registrar o sensor e ouvir as mudanças na contagem de passos com a ajuda do[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
interface, conforme ilustrado pela amostra de código abaixo:

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged`é chamado se a contagem de passos atualizar enquanto o aplicativo está em primeiro plano. Se o aplicativo entrar em segundo plano, `OnSensorChanged` ou o dispositivo estiver dormindo, não será chamado; no entanto, os passos `UnregisterListener` continuarão a ser contados até que seja chamado.

Tenha em mente que *o valor da contagem de passos é cumulativo em todas as aplicações que registram o sensor*. Isso significa que, mesmo que você desinstale `count` e reinstale seu aplicativo, e inicialize a variável em 0 na inicialização do aplicativo, o valor relatado pelo sensor permanecerá o número total de etapas tomadas enquanto o sensor foi registrado, seja por seu aplicativo ou outro. Você pode impedir que sua aplicação adicione ao contador de passos, chamando `UnregisterListener` o `SensorManager`, como ilustrado pelo código abaixo:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

A reinicialização do dispositivo reinicia a contagem de passos para 0. Seu aplicativo exigirá código extra para garantir que está relatando uma contagem precisa para o aplicativo, independentemente de outras aplicações usando o sensor ou o estado do dispositivo.

> [!NOTE]
> Enquanto a API para a detecção de etapas e a contagem de navios com KitKat, nem todos os telefones são equipados com o sensor. Você pode verificar se o `PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`sensor está disponível executando ou `GetDefaultSensor` verificar `null`se o valor devolvido não é .

<a name="developer_tools" />

## <a name="developer-tools"></a>Ferramentas de Desenvolvedor

### <a name="screen-recording"></a>Gravação de tela

KitKat inclui novos recursos de gravação de tela para que os desenvolvedores possam gravar aplicativos em ação. A gravação de tela está disponível através do cliente [Android Debug Bridge (ADB),](https://developer.android.com/tools/help/adb.html) que pode ser baixado como parte do Android SDK.

Para gravar sua tela, conecte seu dispositivo; em seguida, localize sua instalação do Android SDK, navegue até o diretório **de ferramentas de plataforma** e execute o cliente **adb:**

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

O comando acima gravará um vídeo padrão de 3 minutos na resolução padrão de 4Mbps. Para editar o comprimento, adicione o *sinalizador --limite de tempo.*
Para alterar a resolução, adicione o sinalizador *--bit-rate.* O comando a seguir gravará um vídeo de um minuto de duração a 8Mbps:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

Você pode encontrar seu vídeo em seu dispositivo - ele aparecerá em sua Galeria quando a gravação estiver concluída.

## <a name="other-kitkat-additions"></a>Outras adições kitkat

Além das alterações descritas acima, o KitKat permite:

- *Use a Tela Cheia* - KitKat introduz um novo [modo Imersivo](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)) para navegar conteúdo, jogar jogos e executar outros aplicativos que poderiam se beneficiar de uma experiência em tela cheia.

- *Personalizar notificações* - Obter detalhes adicionais sobre notificações do sistema com o[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . Isso permite que você apresente as informações de uma forma diferente dentro do seu aplicativo.

- *Recursos de saque ável-espelhado* - Recursos desempates têm um novo[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  atributo que diz ao sistema criar uma versão espelhada para imagens que requerem a inversão para layouts da esquerda para a direita.

- *Pausa rindade animações* - Pausa rastae e retome animações criadas com o[`Animator`](xref:Android.Animation.Animator)
  .

- *Leia texto de alteração dinâmica* - Denote partes da UI que atualizam dinamicamente com o novo texto como "regiões vivas" com o novo[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  atributo para que o novo texto seja lido automaticamente no modo de acessibilidade.

- *Melhorar a experiência de áudio* - Faça faixas mais altas com o[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  , encontre o Pico e o RMS de um fluxo de áudio com o[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  classe, e obter informações de um carimbo de [data e hora](xref:Android.Media.AudioTimestamp) de áudio para ajudar com a sincronização áudio-vídeo.

- *Sincronizar conteúdoResolver em intervalo personalizado* - KitKat adiciona alguma variabilidade ao tempo em que uma solicitação de sincronização é realizada. Sincronize um `ContentResolver` na `ContentResolver.RequestSync` hora ou `SyncRequest`intervalo personalizado sacaneando e passando em um .

- *Distinguish Between Controllers* - No KitKat, os controladores recebem identificadores inteiros exclusivos `ControllerNumber` que podem ser acessados através da propriedade do dispositivo. Isso torna mais fácil diferenciar os jogadores em um jogo.

- *Controle remoto* - Com algumas mudanças tanto no hardware quanto no lado do software, o KitKat `ConsumerIrService`permite que você transforme um dispositivo equipado com um transmissor IR em um controle remoto usando o , e interaja com dispositivos periféricos com o novo[`RemoteController`](xref:Android.Media.RemoteController)
  Apis.

Para obter mais informações sobre as alterações acima da API, consulte a visão geral das APIs do Google [Android 4.4.](https://developer.android.com/about/versions/android-4.4.html)

## <a name="summary"></a>Resumo

Este artigo introduziu algumas das novas APIs disponíveis no Android 4.4 (API Nível 19), e cobriu as práticas recomendadas ao fazer a transição de um aplicativo para KitKat. Ele esboçou alterações nas APIs que afetam a experiência do usuário, incluindo o *quadro de transição* e novas opções de *temática.* Em seguida, introduziu o Framework `DocumentsProvider` e a classe *de acesso ao armazenamento,* bem como as novas *APIs de impressão*. Ele explorou a *emulação de cartão baseada em NFC* e como trabalhar com *sensores de baixa potência,* incluindo dois novos sensores para rastrear os passos do usuário. Finalmente, ele demonstrou capturar demonstrações em tempo real de aplicativos com gravação de *tela,* e forneceu uma lista detalhada de alterações e adições da API kitkat.

## <a name="related-links"></a>Links relacionados

- [KitKat Amostra](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [Android 4.4 APIs](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
