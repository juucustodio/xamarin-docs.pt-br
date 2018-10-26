---
title: Caixas de diálogo no xamarin. Mac
description: Este artigo aborda o trabalho com caixas de diálogo e janelas modais em um aplicativo xamarin. Mac. Ele descreve como criar janelas modais no Interface builder e Xcode, trabalhando com caixas de diálogo padrão e interagir com esses controles no código c#.
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8c176944d1bec4850f084043fdab81b5c2d51ceb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105201"
---
# <a name="dialogs-in-xamarinmac"></a>Caixas de diálogo no xamarin. Mac

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso a caixas de diálogo e o Windows Modal mesmo que um desenvolvedor que trabalha em *Objective-C* e *Xcode* faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter seu Windows Modal (ou, opcionalmente, criá-los diretamente em código c#).

Uma caixa de diálogo é exibida em resposta a uma ação do usuário e normalmente fornece formas como os usuários podem concluir a ação. Uma caixa de diálogo requer uma resposta do usuário antes de ser fechado.

Windows podem ser usado em um estado sem janela restrita (por exemplo, um editor de texto que pode ter vários documentos abertos ao mesmo tempo) ou Modal (por exemplo, uma caixa de diálogo de exportação deve ser descartada antes que o aplicativo possa continuar).

[![](dialog-images/dialog03.png "Uma caixa de diálogo Abrir")](dialog-images/dialog03.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com caixas de diálogo e modais do Windows em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` comandos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Introdução às caixas de diálogo

Uma caixa de diálogo é exibida em resposta a uma ação do usuário (como salvar um arquivo) e fornece uma maneira para os usuários a concluir a ação. Uma caixa de diálogo requer uma resposta do usuário antes de ser fechado.

De acordo com a Apple, há três maneiras de apresentar uma caixa de diálogo:

- **Documentar Modal** -caixa de diálogo Modal de um documento impede que o usuário fazendo tudo dentro de um determinado documento até que ele seja liberado.
- **Aplicativo – Modal** – um aplicativo de caixa de diálogo Modal impede que o usuário interagir com o aplicativo até que ele seja liberado.
- **Sem janela restrita** um diálogo de sem janela restrita permite que os usuários alterem as configurações na caixa de diálogo enquanto ainda interage com a janela do documento.

### <a name="modal-window"></a>Janela Modal

Qualquer padrão `NSWindow` pode ser usado como uma caixa de diálogo personalizada exibindo modalmente:

[![](dialog-images/modal01.png "Um exemplo de janela modal")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Folhas de caixa de diálogo Modal do documento

Um _folha_ é uma caixa de diálogo modal está anexada a uma janela de documento fornecido, impedindo que os usuários interajam com a janela até que eles descartar a caixa de diálogo. Uma folha é anexada à janela do qual ela surge e apenas uma planilha pode ser aberta para uma janela a qualquer momento.

[![](dialog-images/sheet08.png "Uma folha modal de exemplo")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Preferências Windows

Uma janela de preferências é uma caixa de diálogo sem janela restrita que contém as configurações do aplicativo que o usuário muda com pouca frequência. As preferências do Windows geralmente incluem uma barra de ferramentas que permite ao usuário alternar entre diferentes grupos de configurações:

[![](dialog-images/dialog02.png "Uma janela de preferência de exemplo")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Caixa de diálogo Abrir

A caixa de diálogo Abrir dá aos usuários uma maneira consistente para localizar e abrir um item em um aplicativo:

[![](dialog-images/dialog03.png "Uma caixa de diálogo de abertura")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>Impressão e caixas de diálogo Configurar página

macOS fornece impressão padrão e página Instalação caixas de diálogo que seu aplicativo pode exibir para que os usuários podem ter uma impressão consistente experiência em todos os aplicativos que usam.

A caixa de diálogo de impressão pode ser exibida como ambos os uma caixa de diálogo flutuante livre:

[![](dialog-images/print01.png "Uma caixa de diálogo de impressão")](dialog-images/print01.png#lightbox)

Ou ele pode ser exibido como uma planilha:

[![](dialog-images/print02.png "Uma folha de impressa")](dialog-images/print02.png#lightbox)

A caixa de diálogo de configuração de página podem ser exibida como ambos os uma caixa de diálogo flutuante livre:

[![](dialog-images/print03.png "Uma caixa de diálogo de configuração de página")](dialog-images/print03.png#lightbox)

Ou ele pode ser exibido como uma planilha:

[![](dialog-images/print04.png "Uma folha de configuração de página")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Salvar as caixas de diálogo

A caixa de diálogo Salvar oferece aos usuários uma maneira consistente para salvar um item em um aplicativo. A caixa de diálogo Salvar tem dois estados: **mínimo** (também conhecido como recolhido):

[![](dialog-images/save01.png "A caixa de diálogo Salvar")](dialog-images/save01.png#lightbox)

E o **expandido** estado:

[![](dialog-images/save02.png "Caixa de diálogo Salvar uma expandida")](dialog-images/save02.png#lightbox)

O **mínimo** também pode ser exibida a caixa de diálogo Salvar como uma planilha:

[![](dialog-images/save03.png "Mínimo de salvamento folha")](dialog-images/save03.png#lightbox)

Como o **expandido** caixa de diálogo Salvar:

[![](dialog-images/save04.png "Folha de salvar uma expandida")](dialog-images/save04.png#lightbox)

Para obter mais informações, consulte o [Dialogs](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Adicionando uma janela Modal a um projeto

Além da janela do documento principal, talvez seja necessário exibir outros tipos de janelas para o usuário, como preferências ou painéis do Inspetor de um aplicativo xamarin. Mac.

Para adicionar uma nova janela, faça o seguinte:

1. No **Gerenciador de soluções**, abra o `Main.storyboard` arquivo para edição no Interface Builder do Xcode.
2. Arraste um novo **controlador de exibição** na superfície de Design:

    [![](dialog-images/new01.png "Selecionar um controlador de exibição da biblioteca")](dialog-images/new01.png#lightbox)
3. No **Inspetor de identidade**, insira `CustomDialogController` para o **nome da classe**: 

    [![](dialog-images/new02.png "Configurando o nome de classe")](dialog-images/new02.png#lightbox)
4. Alterne para Visual Studio para Mac, permitir a sincronização com o Xcode e criar o `CustomDialogController.h` arquivo.
5. Retorne para o Xcode e sua interface de design: 

    [![](dialog-images/new03.png "Projetando a interface do usuário no Xcode")](dialog-images/new03.png#lightbox)
6. Criar uma **Segue Modal** da janela principal do seu aplicativo para o novo controlador de exibição, arrastando o controle do elemento da interface do usuário que será aberta a caixa de diálogo janela da caixa de diálogo. Atribuir a **identificador** `ModalSegue`: 

    [![](dialog-images/new06.png "Um segue modal")](dialog-images/new06.png#lightbox)
6. Wire-up qualquer **ações** e **saídas**: 

    [![](dialog-images/new04.png "Configurar uma ação")](dialog-images/new04.png#lightbox)
6. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

Verifique o `CustomDialogController.cs` arquivo são semelhantes ao seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Este código expõe algumas propriedades para definir o título e a descrição da caixa de diálogo e alguns eventos para reagir à caixa de diálogo que está sendo cancelada ou aceito.

Em seguida, edite o `ViewController.cs` do arquivo, substituir o `PrepareForSegue` método e torná-lo semelhante ao seguinte:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

Esse código inicializa o segue que definimos no Interface Builder do Xcode para nossa caixa de diálogo e define o título e descrição. Ele também lida com a opção que faz com que o usuário na caixa de diálogo.

Podemos executar nosso aplicativo e exibir a caixa de diálogo personalizada:

[![](dialog-images/new05.png "Uma caixa de diálogo de exemplo")](dialog-images/new05.png#lightbox)

Para obter mais informações sobre como usar o windows em um aplicativo xamarin. Mac, consulte nosso [trabalhando com o Windows](~/mac/user-interface/window.md) documentação.

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Criando uma folha de personalizado

Um _folha_ é uma caixa de diálogo modal está anexada a uma janela de documento fornecido, impedindo que os usuários interajam com a janela até que eles descartar a caixa de diálogo. Uma folha é anexada à janela do qual ela surge e apenas uma planilha pode ser aberta para uma janela a qualquer momento. 

Para criar uma planilha personalizado no xamarin. Mac, vamos fazer o seguinte:

1. No **Gerenciador de soluções**, abra o `Main.storyboard` arquivo para edição no Interface Builder do Xcode.
2. Arraste um novo **controlador de exibição** na superfície de Design:

    [![](dialog-images/new01.png "Selecionar um controlador de exibição da biblioteca")](dialog-images/new01.png#lightbox)
2. Projetar sua interface do usuário:

    [![](dialog-images/sheet01.png "O design de interface do usuário")](dialog-images/sheet01.png#lightbox)
3. Criar uma **folha Segue** na janela do principal para o novo controlador de exibição: 

    [![](dialog-images/sheet02.png "Selecionar o tipo de segue folha")](dialog-images/sheet02.png#lightbox)
4. No **Inspetor de identidade**, nomeie o controlador de exibição **classe** `SheetViewController`: 

    [![](dialog-images/sheet03.png "Configurando o nome de classe")](dialog-images/sheet03.png#lightbox)
5. Defina qualquer necessários **tomadas** e **ações**: 

    [![](dialog-images/sheet04.png "Definindo as saídas e ações necessárias")](dialog-images/sheet04.png#lightbox)
6. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar.

Em seguida, edite o `SheetViewController.cs` de arquivo e torná-lo semelhante ao seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Em seguida, edite o `ViewController.cs` do arquivo, edite o `PrepareForSegue` método e torná-lo semelhante ao seguinte:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

Se podemos executar nosso aplicativo e abra a folha, ele será anexado à janela:

[![](dialog-images/sheet08.png "Uma folha de exemplo")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Criando uma caixa de diálogo de preferências

Antes de nós dispor o modo de preferência no Interface Builder, precisaremos adicionar um tipo de segue personalizado para lidar com as preferências de alternância. Adicione uma nova classe ao seu projeto e chamá-lo `ReplaceViewSeque `. Edite a classe e torná-lo a seguinte aparência:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

Com o segue personalizado criado, podemos adicionar uma nova janela no Interface Builder do Xcode, para lidar com as preferências de nossa.

Para adicionar uma nova janela, faça o seguinte:

1. No **Gerenciador de soluções**, abra o `Main.storyboard` arquivo para edição no Interface Builder do Xcode.
2. Arraste um novo **controlador da janela** na superfície de Design:

    [![](dialog-images/pref01.png "Selecione um controlador de janela da biblioteca")](dialog-images/pref01.png#lightbox)
3. Organizar a janela próximo a **barra de menus** designer:

    [![](dialog-images/pref02.png "Adicionar a nova janela")](dialog-images/pref02.png#lightbox)
4. Crie cópias do controlador de exibição anexada, pois haverá guias no modo de exibição de preferência:

    [![](dialog-images/pref03.png "Adicionando controladores de exibição necessária")](dialog-images/pref03.png#lightbox)
5. Arraste um novo **controlador de barra de ferramentas** da **biblioteca**:

    [![](dialog-images/pref04.png "Selecione um controlador de barra de ferramentas da biblioteca")](dialog-images/pref04.png#lightbox)
6. E solte-a na janela na superfície de Design:

    [![](dialog-images/pref05.png "Adicionando um novo controlador de barra de ferramentas")](dialog-images/pref05.png#lightbox)
7. O design da sua barra de ferramentas de layout:

    [![](dialog-images/pref06.png "A barra de ferramentas de layout")](dialog-images/pref06.png#lightbox)
8. CTRL + clique e arraste a cada **botão de barra de ferramentas** aos modos de exibição que você criou acima. Selecione uma **personalizado** segue o tipo:

    [![](dialog-images/pref07.png "Definindo o tipo de segue")](dialog-images/pref07.png#lightbox)
9. Selecione o novo Segue e defina as **classe** para `ReplaceViewSegue`:

    [![](dialog-images/pref08.png "Definindo a classe segue")](dialog-images/pref08.png#lightbox)
10. No **Designer da barra de menus** na superfície de Design, no Menu do aplicativo, selecione **preferências...** , CTRL + clique e arraste para a janela de preferências para criar um **Mostrar** segue:

    [![](dialog-images/pref09.png "Definindo o tipo de segue")](dialog-images/pref09.png#lightbox)
11. Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar.

Se vamos executar o código e selecionar o **preferências...**  do **Menu aplicativo**, a janela será exibida:

[![](dialog-images/pref10.png "Uma janela de preferências de exemplo")](dialog-images/pref10.png#lightbox)

Para obter mais informações sobre como trabalhar com o Windows e barras de ferramentas, consulte nosso [Windows](~/mac/user-interface/window.md) e [barras de ferramentas](~/mac/user-interface/toolbar.md) documentação.

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Salvando e Carregando preferências

Em um aplicativo de macOS típico, quando o usuário faz alterações em qualquer uma das preferências do usuário do aplicativo, essas alterações são salvas automaticamente. A maneira mais fácil de lidar com isso em um aplicativo xamarin. Mac, é criar uma única classe para gerenciar todas as preferências do usuário e compartilhá-lo em todo o sistema.

Primeiro, adicione um novo `AppPreferences` classe ao projeto e herdam `NSObject`. As preferências serão criadas para usar [associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) que tornará o processo de criação e manutenção a preferência de forma muito mais simples. Como as preferências consistem em uma pequena quantidade de tipos de dados simples, use o interno no `NSUserDefaults` para armazenar e recuperar valores.

Editar o `AppPreferences.cs` de arquivo e torná-lo semelhante ao seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLangauge")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLangauge", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLangauge");
                SaveInt ("DefaultLangauge", value, true);
                DidChangeValue ("DefaultLangauge");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

Essa classe contém algumas rotinas auxiliares, como `SaveInt`, `LoadInt`, `SaveColor`, `LoadColor`, etc. para funcionar com `NSUserDefaults` mais fácil. Além disso, como `NSUserDefaults` não tem um modo interno para manipular `NSColors`, o `NSColorToHexString` e `NSColorFromHexString` métodos são usados para converter cores em cadeias de caracteres hexadecimais baseado na web (`#RRGGBBAA` onde `AA` é a transparência alfa) que pode ser facilmente armazenados e recuperados.

No `AppDelegate.cs` de arquivo, crie uma instância das **AppPreferences** objeto que será usado por todo o aplicativo:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>Preferências de fiação a preferência de modos de exibição

Em seguida, conecte-se a classe de preferência para elementos de interface do usuário na janela de preferência e modos de exibição criados acima. No construtor de Interface, selecionar um controlador de exibição de preferência e alternar para o **Inspetor de identidade**, criar uma classe personalizada para o controlador: 

[![](dialog-images/prefs12.png "O Inspetor de identidade")](dialog-images/prefs12.png#lightbox)

Alterne para o Visual Studio para Mac para sincronizar suas alterações e abra a classe recém-criado para edição. Tornar a classe a seguinte aparência:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Observe que essa classe tem feito duas coisas aqui: primeiro, há um auxiliar `App` propriedade para ter acesso a **AppDelegate** mais fácil. Segundo, o `Preferences` propriedade expõe global **AppPreferences** para vinculação de dados com todos os controles da interface do usuário é colocado nesta exibição de classe.

Em seguida, clique duas vezes no arquivo de Storyboard para abri-la novamente no construtor de Interface (e ver as alterações feitas recentemente acima). Arraste os controles da interface do usuário necessários para criar a interface de preferências no modo de exibição. Para cada controle, alterne para o **Inspetor de associação** e associar a propriedades individual da **AppPreference** classe:

[![](dialog-images/prefs13.png "O Inspetor de associação")](dialog-images/prefs13.png#lightbox)

Repita as etapas acima para todos os painéis (controladores de exibição) e propriedades de preferência necessárias.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>Aplicação de preferência muda para todos os Windows

Como mencionado acima, em um macOS típico do aplicativo, quando o usuário faz alterações em qualquer uma das preferências do usuário do aplicativo, essas alterações são salvas automaticamente e aplicadas a todas as janelas o usuário tenha aberto no aplicativo.

Um planejamento cuidadoso e design de preferências de seu aplicativo e o windows permitirá que esse processo ocorra sem problemas e transparente para o usuário final, com uma quantidade mínima de trabalho de codificação.

Para qualquer janela que estará consumindo as preferências do aplicativo, adicione a seguinte propriedade de auxiliar para seu controlador de exibição de conteúdo para tornar a acessar nossos **AppDelegate** mais fácil:

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

Em seguida, adicione uma classe para configurar o conteúdo ou o comportamento com base nas preferências do usuário:

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

Você precisa chamar o método de configuração quando a janela é aberta pela primeira vez para garantir que ele está em conformidade com as preferências do usuário:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Em seguida, edite o `AppDelegate.cs` arquivo e adicione o seguinte método para aplicar qualquer preferência muda para todas as janelas abertas:

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

Em seguida, adicione um `PreferenceWindowDelegate` classe ao projeto e que ela fique semelhante ao seguinte:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWindowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

Isso fará com que as alterações de preferência a ser enviada para todos os Windows abertos quando a preferência de janela é fechada.

Por fim, edite o controlador da janela de preferência e adicionar o delegado criado acima:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWindowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Com todas essas alterações em vigor, se o usuário edita as preferências do aplicativo e fecha a janela de preferência, as alterações serão aplicadas a todos os Windows abertas:

[![](dialog-images/prefs14.png "Uma janela de preferências de exemplo")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Caixa de diálogo Abrir

A caixa de diálogo Abrir oferece aos usuários uma maneira consistente para localizar e abrir um item em um aplicativo. Para exibir uma caixa de diálogo aberta em um aplicativo xamarin. Mac, use o seguinte código:

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

No código acima, estamos abrindo uma nova janela de documento para exibir o conteúdo do arquivo. Você precisará substituir esse código com a funcionalidade é exigido pelo seu aplicativo.

As seguintes propriedades estão disponíveis ao trabalhar com um `NSOpenPanel`:

- **CanChooseFiles** - se `true` o usuário pode selecionar arquivos.
- **CanChooseDirectories** - se `true` o usuário pode selecionar diretórios.
- **AllowsMultipleSelection** - se `true` o usuário pode selecionar mais de um arquivo por vez.
- **ResolveAliases** - se `true` selecionando e alias, resolve para o caminho do arquivo original.
- **AllowedFileTypes** -é uma matriz de cadeia de caracteres de tipos de arquivo que o usuário pode selecionar qualquer uma extensão ou _UTI_. O valor padrão é `null`, que permite que qualquer arquivo a ser aberto.

O `RunModal ()` método exibe a caixa de diálogo Abrir e permitir que o usuário selecione arquivos ou diretórios (conforme especificado pelas propriedades) e retorna `1` se o usuário clica o **abrir** botão.

Caixa de diálogo Abrir retorna os arquivos selecionados ou pastas do usuário como uma matriz de URLs no `URL` propriedade.

Se executar o programa e selecione o **abra...**  de item do **arquivo** menu, o seguinte é exibido: 

[![](dialog-images/dialog03.png "Uma caixa de diálogo Abrir")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>A impressão e caixas de diálogo Configurar página

macOS fornece impressão padrão e página Instalação caixas de diálogo que seu aplicativo pode exibir para que os usuários podem ter uma impressão consistente experiência em todos os aplicativos que usam.

O código a seguir mostra a caixa de diálogo de impressão padrão:

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

Se definirmos o `ShowPrintAsSheet` propriedade para `false`, execute o aplicativo e exibir a caixa de diálogo de impressa, será exibido o seguinte:

[![](dialog-images/print01.png "Uma caixa de diálogo de impressão")](dialog-images/print01.png#lightbox)

Se definir a `ShowPrintAsSheet` propriedade para `true`, execute o aplicativo e exibir a caixa de diálogo de impressa, será exibido o seguinte:

[![](dialog-images/print02.png "Uma folha de impressa")](dialog-images/print02.png#lightbox)

O código a seguir exibirá a caixa de diálogo do Layout de página:

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

Se definirmos o `ShowPrintAsSheet` propriedade para `false`, execute o aplicativo e exibir a caixa de diálogo do layout de impressão, será exibido o seguinte:

[![](dialog-images/print03.png "Uma caixa de diálogo de configuração de página")](dialog-images/print03.png#lightbox)

Se definir a `ShowPrintAsSheet` propriedade para `true`, execute o aplicativo e exibir a caixa de diálogo do layout de impressão, será exibido o seguinte:

[![](dialog-images/print04.png "Uma folha de configuração de página")](dialog-images/print04.png#lightbox)

Para obter mais informações sobre como trabalhar com a impressão e caixas de diálogo de configuração de página, consulte da Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092), [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) e [Introdução à impressão](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1) documentação.

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>Salvar caixa de diálogo

A caixa de diálogo Salvar oferece aos usuários uma maneira consistente para salvar um item em um aplicativo.

O código a seguir mostra a caixa de diálogo padrão salvar:

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

O `AllowedFileTypes` propriedade é uma matriz de cadeia de caracteres de tipos de arquivo que o usuário pode selecionar para salvar o arquivo como. O tipo de arquivo pode ser especificado como uma extensão ou _UTI_. O valor padrão é `null`, que permite que qualquer tipo de arquivo a ser usado.

Se definirmos o `ShowSaveAsSheet` propriedade para `false`, execute o aplicativo e selecione **Salvar como...**  do **arquivo** menu, será exibido o seguinte:

[![](dialog-images/save01.png "A caixa de diálogo de salvamento")](dialog-images/save01.png#lightbox)

O usuário pode expandir a caixa de diálogo:

[![](dialog-images/save02.png "Caixa de diálogo Salvar uma expandida")](dialog-images/save02.png#lightbox)

Se definirmos o `ShowSaveAsSheet` propriedade para `true`, execute o aplicativo e selecione **Salvar como...**  do **arquivo** menu, será exibido o seguinte:

[![](dialog-images/save03.png "Salvar folha")](dialog-images/save03.png#lightbox)

O usuário pode expandir a caixa de diálogo:

[![](dialog-images/save04.png "Folha de salvar uma expandida")](dialog-images/save04.png#lightbox)

Para obter mais informações sobre como trabalhar com a caixa de diálogo Salvar, consulte da Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) documentação.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com Windows Modal, planilhas e caixas de diálogo padrão do sistema em um aplicativo xamarin. Mac. Vimos os diferentes tipos e usos do Windows Modal, planilhas e caixas de diálogo, como criar e manter o Windows Modal e folhas no Xcode do Interface Builder e como trabalhar com o Windows Modal, folhas e caixas de diálogo em código c#.

## <a name="related-links"></a>Links relacionados

- [MacWindows (amostra)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menus](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Barras de ferramentas](~/mac/user-interface/toolbar.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Introdução às planilhas](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Introdução à impressão](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
